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
- 다만, 저자는 background color를 color rendering 중에 더하는 것과 다르게, blending weight $T_i \alpha_i$로 normalize하는 것이 depth와 normal maps을 rendering하는데 더욱 적합하다는 것을 발견했습니다.

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

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b59e2b5f-c267-43c0-ae54-ce1884cca9eb)

## Depth-normal consistency loss

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

