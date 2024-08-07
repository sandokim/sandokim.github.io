---
title: "[CUDA] 3DGS cuda_rasterizer forward.cu & backward.cu"
last_modified_at: 2024-07-22
categories:
  - CUDA
tags:
  - 3dgs
  - 3d gaussian splatting
  - gaussian rasterizer
  - cuda programming
  - cuda rasterization
  - diff-gaussian-rasterization
  - forward.cu
  - backward.cu
excerpt: "diff-gaussian-rasterization/cuda_rasterizer/forward.cu"
use_math: true
classes: wide
comments: true
---

# Original 3D Gaussian Splatting의 submodules/diff-gaussian-rasterization의 forward.cu

### `forward.cu`는 gaussian splat이 Tile-based Rendering을 하는 코드입니다.
- block = tile
- thread = pixel
- `cuda_rasterizer/forward.cu`에서 2D gaussian의 center로부터 현재 pixel까지의 거리 `d`를 구하고,
- 그 2D gaussian 함수이 `d`만큼 떨어진 위치의 pixel에 contribution하는 정도를 weight로 나타냅니다.
- gaussian 함수의 수식은 아래와 같습니다.
  
$$
\exp^{-(x-\mu)^2/(2\sigma^2)}
$$

- `forward.cu`에서는 위 수식을 `x,y`를 갖는 2차원 가우시안 분포로 표현하여, `power` 변수로 정의하였음을 확인 가능합니다.

![image](https://github.com/user-attachments/assets/73f22f0b-1ffb-4d36-93ef-7d429b614725)


```cuda
# diff-gaussian-rasterization/cuda_rasterizer/foward.cu
...
		// Iterate over current batch
		for (int j = 0; !done && j < min(BLOCK_SIZE, toDo); j++)
		{
			// Keep track of current position in range
			contributor++;

			// Resample using conic matrix (cf. "Surface 
			// Splatting" by Zwicker et al., 2001)
			float2 xy = collected_xy[j];
			float2 d = { xy.x - pixf.x, xy.y - pixf.y };
			float4 con_o = collected_conic_opacity[j];
			float power = -0.5f * (con_o.x * d.x * d.x + con_o.z * d.y * d.y) - con_o.y * d.x * d.y;
			if (power > 0.0f)
				continue;

			// Eq. (2) from 3D Gaussian splatting paper.
			// Obtain alpha by multiplying with Gaussian opacity
			// and its exponential falloff from mean.
			// Avoid numerical instabilities (see paper appendix). 
			float alpha = min(0.99f, con_o.w * exp(power));
			if (alpha < 1.0f / 255.0f)
				continue;
			float test_T = T * (1 - alpha);
			if (test_T < 0.0001f)
			{
				done = true;
				continue;
			}
...
```


### `backward.cu`는 output인 pixel의 rgb가 gaussian properties에 대한 backprop을 계산합니다.

#### output color c ; p개의 pixel
- p_rgb # (p, 3)

#### input ; G개의 gaussian
- g_point_3D # (G, 3)
- g_rgb # (G, 3)
- g_scale # (G, 3)
- g_rotation # (G, 4)
- g_opacity # (G, 1)

## output가 input을 chain rule로 연결하여 각 gaussian properties를 tile 별로 계산합니다.

먼저 output color c는

$$
c = c_0\alpha_0 + c_1\alpha_1(1-\alpha_0) + ...
$$

### output color c가 gaussian g_rgb에 chain rule로 연결

여기서 $L$ 은 Loss이고, 미분에서는 변화량으로 $dL$ 로 씁니다.

미소 변화량 $dg_{rgb}$ 가 Loss의 미소 변화량 $dL$ 에 미치는 미분 관계는 다음과 같이 풀어쓸 수 있습니다.

$$
\frac{dL}{dg_{rgb}} = \frac{dL}{dc} \times \frac{dc}{dg_{rgb}}
$$

$\frac{dc}{dc_0} = \alpha_0, \\ \frac{dc}{dc_1} = \alpha_1(1-\alpha_0)$, ... 이고

$c_0, c_1, ... , c_n$는 현재 camera에서 가까운 순서대로 0번째, 1번째, ... n번째의 각 gaussian의 color를 의미하므로

Each gaussian 별로 color에 contribution하는 정도를 output color c와 chain rule로 연결하여 표현할 수 있습니다.

### output color c가 gaussian g_opacity에 chain rule로 연결
  
$$
\frac{dL}{dg_{opacity}} = \frac{dL}{dc} \times \frac{dc}{dg_{opacity}}
$$

### output color c가 나머지 gaussian properties들인 g_point_3D, g_scale, g_rotation에 chain rule로 연결하는 과정도 동일합니다.

#### Tip: Write what you have on the left and write what you have on the right

- gaussian의 position과 color는 아무 상관이 없어보이지만, $\alpha$는 gaussian의 position $dg_{position}$과 관련이 있습니다.

$$
\frac{dL}{dg_{position}} = \frac{dL}{dc} \times \frac{dc}{dg_{position}} = \frac{dL}{dc} \times \frac{dc}{d\alpha} \times \frac{d\alpha}{dg_{position}}
$$

- $\alpha$에 대한 식을 먼저 찾고 다시 chain rule을 전개해봅시다.

$$
\alpha = g_{opacitiy} \times \exp^{-\frac{1}{2}(x-\mu)^T\Sigma(x-\mu)} = \exp^{-\frac{1}{2}(c_0d^2x + 2c_1dxdy + c_2d^2y)}
$$

- `forward.cu`, `backward.cu`에서 사용하는 gaussian의 지수는 아래처럼 변수 `power`로 정의하여 사용합니다.

$$
power = -\frac{1}{2}(c_0d^2x + 2c_1dxdy + c_2d^2y)
$$

- 이제 다시 처음 식을 전개하면

$$
\frac{dL}{dg_{position}} = \frac{dL}{dc} \times \frac{dc}{dg_{position}} = \frac{dL}{dc} \times \frac{dc}{d\alpha} \times \frac{d\alpha}{dg_{position}} = \frac{dL}{dc} \times \frac{dc}{d\alpha} \times g_{opacity} \times \frac{d\exp^{power}}{dg_{position}} = \frac{dL}{dc} \times \frac{dc}{d\alpha} \times g_{opacity} \times \exp^{power} \times \frac{dpower}{dg_{position}}
$$
