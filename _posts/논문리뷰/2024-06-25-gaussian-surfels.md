---
title: "[논문리뷰] High-quality Surface Reconstruction using Gaussian Surfels"
last_modified_at: 2024-06-25
categories:
  - 논문리뷰
tags:
  - Computer Vision
  - 3dgs
  - 3d gaussian splatting
  - gaussian surfels
  - monocular normal priors
  - foreground masks
  - screened Poisson reconstruction
  - SuGaR
  - shape-radiance ambiguity
  - 3D ellipsoid into a 2D ellipse
  - self-supervised normal-depth consistency loss
excerpt: "[논문리뷰] High-quality Surface Reconstruction using Gaussian Surfels"
use_math: true
classes: wide
---

### High-quality Surface Reconstruction using Gaussian Surfels
> ACM SIGGRAPH 2024. [[Paper](https://arxiv.org/pdf/2404.17774)] [[Page](https://turandai.github.io/projects/gaussian_surfels/)] [[Github](https://github.com/turandai/gaussian_surfels)]
> Pinxuan Dai1*, Jiamin Xu2*, Wenxiang Xie1, Xinguo Liu1, Huamin Wang3, Weiwei Xu1†
> 1State Key Lab of CAD&CG, Zhejiang University; 2Hangzhou Dianzi University; 3Style3D Research
> 27 Apr 2024

## Gaussian Surfels

- 3d Gaussian인 3x3 covaraince matrix은 scaling matrix $S_i$와 rotation matrix $R(r_i)$로 구성됩니다.
- 각 Gaussian은 truncate하여 2D ellipse로 나타낼 수 있고
- 각 Gaussian kernel의 normal은 rotation matrix $R(r_i)$에서 numpy slicing하여 다음처럼 $n_i = R(r_i)[:,2]$로 직접적으로 얻을 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/444819b1-b933-4806-a4f4-46a01290e75c)

- 나중에 설명하기로, 이처럼 covariance matrix의 gradient 계산시 세번째 column에 해당하는 $R_i$ (normal)에 해당하는 부분은 gradient가 0이 됩니다.
- 이는 결과적으로 photometric loss를 계산하여 chain rule을 적용할 때, 각 Gaussian surfel의 normal에 해당하는 loss가 gradient를 가지지 않는 결과를 냅니다.
- 하지만, $R_i$는 SO(3)에 속하는 rotation matrix이므로, normal은 rotation matrix $R_i$의 첫 2개의 axes (x-axis, y-axis)에 따라 여전히 조정되는 상황입니다. 
- 이와 같은 indirect modification은 error를 불러일으키므로, 저자는 depth-normal consistency loss를 주어, 각 Gaussian surfel의 normal을 조절합니다. 
- 이는 rendered depth로부터 얻은 gradient를 사용하여 depth-normal consistency loss를 줍니다.
  
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0ff8459d-5f7d-4dc8-90b5-99a8d4111981)

- Implementation에서는 photometric loss가 $R(r_i)[:,2]$에 대해 gradient를 가지지 않으므로, $R(r_i)$의 each axis (x-axis, y-axis)을 따른 gradients와 balance를 맞춰주기 위해 normal에 대한 gradient $\partial \tilde{N} / \partial R[:,2]$는 10만큼 scaling 해줍니다.
- 이를 통해 x-axis, y-axis에 대해서도, normal에 대해서도 Gaussian ellipse가 올바른 방향으로 rotate하도록 guide 해주었습니다.

## With 3dgs & alpha-blending, we can calculate the rendered depth $\tilde{D}$ & the rendered normal $\tilde{N}$ for each pixel 

- 놀랍게도 3dgs와 alpha-blending으로 각 pixel 마다의 depth와 normal을 계산할 수 있습니다. 😮
- 다만, 저자는 background color를 color rendering 중에 더하는 것과 다르게, blending weight $T_i \alpha_i$를 normalize하는 것이 depth와 normal maps을 rendering하는데 더욱 적합하다는 것을 발견했습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e40b6d48-1153-42b7-847c-2aac7c1150f4)

## Local Taylor Expansion을 이용한 깊이 값 계산

Local Taylor expansion은 복잡한 함수의 값을 특정 지점 근처에서 근사하기 위해 사용하는 방법입니다. 이 예제에서는 Gaussian surfel의 깊이 값을 보다 정확하게 계산하기 위해 사용됩니다. 여기서는 Gaussian kernel의 중심 위치에서 깊이 값을 단순히 혼합하는 것이 부정확하다는 문제를 해결하기 위해, 각 픽셀 $u$에서의 깊이 $d_i(u)$를 근사적으로 계산하기 위해 local Taylor expansion을 사용합니다.

### 문제점
기본적으로 중심 위치 $u_i$에서의 깊이 값 $d_i(u_i)$를 사용하여 깊이 값을 계산하는 것은 경사진 평면과 일치하지 않는 부정확한 결과를 초래할 수 있습니다. 이는 깊이 렌더링이 표면 법선과 일치하지 않게 되는 원인이 됩니다.

### 해결책
이 문제를 해결하기 위해, 각 픽셀 $u$에서의 깊이를 Gaussian ellipse와의 교차점을 계산하여 보다 정확하게 계산합니다. 이를 간단하게 하기 위해, local Taylor expansion을 사용하여 다음과 같이 근사합니다:

$$
d_i (u) = d_i (u_i) + (W_k R_i) [2,:] J^{-1}_{pr} (u - u_i)
$$

여기서:
- $d_i (u)$는 픽셀 $u$에서의 깊이 값입니다.
- $d_i (u_i) $는 Gaussian kernel의 중심 위치 $u_i$에서의 깊이 값입니다.
- $(W_k R_i) [2,:]$는 $W_k R_i$ 행렬의 두 번째 행 전체를 선택하여 벡터로 만든 것입니다.
- $J^{-1}_{pr}$는 이미지 공간의 픽셀을 Gaussian surfel의 접평면(tangent plane)으로 역 매핑하는 Jacobian 입니다.
- $(u - u_i)$는 픽셀 $u$와 중심 위치 $u_i$ 간의 차이 벡터입니다.

### Taylor expansion이 무엇일까요?
- Taylor expansion은 복잡한 함수를 다항식으로 풀어서 전개할 때 매우 유용한 수식입니다.
- 이유는 복잡한 함수를 다항식으로 표현하면 미분과 계산 측면에서 빠르고 간단하기 때문입니다.
- Taylor expansion은 함수 $f(x)$가 주어졌을 때, 함수 $f(x)$의 특정 한 점 $a$에서의 그 $f(x)$의 대한 근사치 $P(x)$를 다항식의 미분으로 나타낼 수 있습니다.
- [테일러 시리즈 | 제11장 미적분학의 본질](https://www.youtube.com/watch?v=3d6DsjIBzJ4&t=196s)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/bc456058-8107-4777-a30f-52166efa0699)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5a1337d7-e522-4755-afc9-40c14dd48a8a)

$$
P(x) = f(a) + \frac{df}{dx}(a)\frac{(x-a)^1}{1!}+\frac{d^2f}{dx^2}(a)\frac{(x-a)^2}{2!}+...
$$

### local taylor expansion의 의미
- taylor expansion을 한다는 것은 어떤 관심있는 특정 한 점에서의 그 함수의 다항식 함수로의 근사를 의미합니다.
- local taylor expansion에서 local도 특정 한 점에 대해서 함수를 다항식 함수로 나타내는 것이라고 해석하면 됩니다.

다시 각 픽셀 $u$에서의 깊이를 근사하는 식을 살펴봅시다.

$$
d_i (u) = d_i (u_i) + (W_k R_i) [2,:] J^{-1}_{pr} (u - u_i)
$$

- 여기서 $d_i (u_i) + (W_k R_i) [2,:] J^{-1}_{pr} (u - u_i)$는 $f(a) + \frac{df}{dx}(a)\frac{(x-a)^1}{1!}$과 식이 형태만 다를 뿐 의미는 동일합니다.
- 즉, 다항식으로 근사하고자 하는 함수는 $d_i$이고, $f$에 해당합니다.
- 그리고, 어떤 점 $u_i$가 $a$를 의미합니다.
- 따라서 $d_i(u_i)$는 $f(a)$와 의미상 동일합니다.
- 즉, 두 항을 다음과 같이 정리할 수 있습니다.
- 0차항: $d_i(u_i) = f(a)$
- 1차항: $(W_k R_i) [2,:] J^{-1}_{pr} (u - u_i) = \frac{df}{dx}(a)\frac{(x-a)^1}{1!}$
  - 1차항에서 $(u - u_i) = \frac{(x-a)^1}{1!}$으로 보면 됩니다.
  - 즉, 1차항의 앞의 부분에 해당하는 $(W_k R_i) [2,:] J^{-1}_{pr}$가 1차미분에 해당하는 $\frac{df}{dx}(a)$인 것입니다.
  - $(W_k R_i) [2,:] J^{-1}_{pr}$에서 Jacobian인 $J$는 1차항에 대한 계수를 가지는 행렬입니다.
  - 그리고 $u$는 pixel의 좌표에 해당하는 3차원 벡터입니다.

- Jacobian으로 1차항 다항식 함수를 표현하는 방법을 잠시 살펴봅시다.
  - Jacobian에서 3x3일 경우, 3개의 행벡터는 각각 x,y,z의 1차 계수를 가지게 됩니다.
  - 그 의미는 다음과 같이 행벡터가 존재할 경우,
  - 1st row vector: $2x + 3y + 4z$
  - 2nd row vector: $x + y + z$
  - 3rd row vector: $3x + 2y + 5z$$
  - Jacobian은 다음과 같습니다.

$$
J = \begin{bmatrix} 
2 & 3 & 4 \\ 
1 & 1 & 1 \\ 
3 & 2 & 5 
\end{bmatrix}
$$
    
  - Jacobian으로 1차항 x, 1차항 y, 1차항 z를 가지는 벡터의 계수를 행렬로 표현할 수 있는 것입니다.
  - 예를 들어, $u$ 벡터는 1차항의 x,y,z의 선형조합으로 표현이 되고, 아래와 같이 Jacobian으로 표현할 수 있습니다.

$$
J\boldsymbol{u} = \begin{bmatrix} 
2 & 3 & 4 \\ 
1 & 1 & 1 \\ 
3 & 2 & 5 
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z
\end{bmatrix}
$$

- 1차항 중에서도 $(W_k R_i) [2,:] J^{-1}_{pr}$에서 1차 다항식 근사의 계수를 가지는 Jacobian $J^{-1} _{pr}$까지 계산하였습니다.
- $(W_k R_i) [2,:]$로는 구한 1차 다항식 근사 함수 $J^{-1}_{pr} (u - u_i)$에 0,1,2중 마지막 행에 해당하는 **z-axis에 대한 rotation 변환 $R_i$와 world to camera 변환 $W_k$를 적용해줍니다.**
- z-axis에 대한 것만 고려하는 것은 $(W_k R_i)$에 대해 numpy slicing으로 [2,:]로 마지막 행만 취하여 수행합니다.
- 이를 통해 최종적으로 z-axis에 대한 1차 다항식 근사 함수 $d_i (u)$를 얻을 수 있습니다.
- $d_i (u)$는 gaussian surfel (2D gaussian)까지의 z-axis 거리이고, $d_i (u_i)$는 3d gaussian까지의 z-axis 거리였습니다.
- 우리는 2d gaussian surfel까지의 z-axis 거리를 정확히 구하기 위해서 3d gaussian까지의 z-axis 거리인 $d_i (u_i)$를 이용해 taylor expansion으로 1차 다항식 함수로 근사한 것입니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/bc5ebe64-5f9c-45c1-a0a9-926ad928b8fa)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1dc96ba7-5082-4e9d-bb04-c7c45f92de70)


## depth-normal consistency loss

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/77fefe8b-718d-470a-aa45-60aa592006aa)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/811e6629-8f1d-4eff-848c-8afce09ddf8c)

- the rendered depth $\tilde{D}$
- the rendered normal $\tilde{N}$

the rendered depth $\tilde{D}$에 대해 V(⋅)로 각 픽셀과 그 깊이를 3D 점으로 변환하고, N(⋅)로 이웃한 점들로부터 cross product를 사용하여 normal을 계산합니다.

이렇게 rendered depth로부터 이웃한 점들의 cross product로 얻은 normal $𝑁 (𝑉 (\tilde{D})$과 rendered normal $\tilde{N}$이 일치하도록 loss를 줍니다.

- 주의할 점은 rendered depth로부터 얻은 normal $𝑁 (𝑉 (\tilde{D}))$과 rendered normal $\tilde{N}$이 일치하여 Depth-normal consistency는 유지될 수 있으나, 실제 surfel의 surface에 잘 align되지 않는 Figure 4 (d) 경우가 발생할 수 있습니다.
- 그러나 이 경우다른 loss와 combine하면 문제가 해결되어, 보통 Figure 4 (a)처럼 잘나오게 됩니다.

## Gaussian Point Cutting and Meshing

### Volumetric cutting

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6d7a11bd-b2e2-4d8e-8fce-f1b004833c65)

- 각 Gaussian surfel로부터 aggregated된 alpha values에 따라 volumetric cutting을 수행합니다.
- Volumetric cutting. The idea is to mark those voxels far from Gaussian surfels as un-occupied, i.e., cutting them from the grid.
- Volumetric cutting은 구체적으로 다음과 같이 수행합니다.
  - 먼저 $512^3$ voxel grid를 bounding box에 construct합니다.
  - 그 다움 모든 Gaussian ellipses를 traverse하며, Gaussian ellipses와 surrounding voxels과의 intersection을 게산하고, 해당 voxels들에 대해 weighted opacity를 계산합니다.
  - computational cost를 줄이기 위해, voxel center의 weighted opacity를 사용하여 Gaussian weights와 opacities를 intersection area 내에서 적분을 근사합니다.
  - 만약 어떤 voexl이 low accumulated weighted opacity를 가진다는건, foreground surface와 background surface 사이의 거리가 크다는 것입니다.
  - 이런 voxels들과 함께 그에 상응하는 3D points를 depth 계산에서 prune 해줍니다.
  - 이와 같은 outlier removal은 단순히 각 pixel에 대한 median depth를 사용하여 outlier를 제거한 것보다 퀄리티가 좋고 computationally efficient합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/de4a15b6-7ca1-4646-816c-136cfdadb898)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/df4dca03-d6a2-4b4a-b3c4-755a767d8725)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7a428892-fe35-455c-8ccc-bc28fd6529de)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/efeb73e5-ed1c-45d1-831c-cf8abbb01df0)


### Gaussian Surfels은, signed distance functions처럼 closed surfaces로 가정해버리지 않기에, open surfaces에 대한 reconstruction도 가능합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/35dbac1d-faf0-4364-b0ba-a01924ec6e8f)


## Comparison

- SuGaR는 3D Gaussians을 flat하게 encourage하긴 하지만, **extracted 𝜆−level set에 잘 align 하지 않아서** ellipsoid-like artifacts와 holes이 surface에 생긴다 주장합니다.
- extracted 𝜆−level set가 정확히 무엇인지 알아야합니다.. TBD.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/161c2844-1b30-4548-997f-3f8e51ad3cd3)

### Chamfer distance (mm) 

- Chamfer distance를 측정시, point cloud에서 sample할 point의 개수를 설정하여 target point cloud와 result point cloud가 얼마나 차이가 적은지 측정합니다.
- Table 1에서 Methods인 24, 37, ..., 122는 DTU dataset의 물체 스캔 ID를 의미합니다.
- Table 2에서 Methods인 Basketball, Bear, ..., Stone은 BlendedMVS dataset의 scene 이름입니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9262c96a-e90f-43f1-b782-637d1383dc51)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ac6a5d2c-f738-4625-a89c-26e841ff1ed6)

## Ablation
  
- Ablation study를 할 때, 현재 페이퍼에는 DTU dataset와 BlendedMVS dataset 2개를 소개하였는데, Ablation study는 모두 DTU dataset에 대해 진행하였습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8d7432e5-4be7-45f3-96c0-37f4487775a9)
- 그리고 loss에 대한 ablation study를 정성적으로 보여줄 때, DTU dataset 중에서도 아래처럼 scene 별로 가장 그 효과가 잘 드러나는 부분을 보여주었습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9558d7b1-fe03-4993-bfe3-e9f8eae2cf91)

- BlendedMVS dataset의 경우 **challenging한 18개의 scenes들에 대해서만 결과를 report**하였습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/472ebded-caee-45fb-bcab-50d411a80d32)

## Initialization
- Initialization으로 Structure from Motion (SfM)로 계산된 sparse points를 사용하여, first few steps에서 convergence rate를 높일 수 있습니다.
- 하지만 본 페이퍼에서는 크게 달라지지 않아서, target object의 bounding box에서 random positions과 rotations으로 Gaussian surfels를 initialize하였습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c1a56685-88f7-4cb7-bcfc-6501ab535c24)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/71fd9dc6-17f0-4109-a165-522b8dac22b5)

