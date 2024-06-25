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

# Local Taylor Expansion을 이용한 깊이 값 계산

Local Taylor expansion은 복잡한 함수의 값을 특정 지점 근처에서 근사하기 위해 사용하는 방법입니다. 이 예제에서는 Gaussian surfel의 깊이 값을 보다 정확하게 계산하기 위해 사용됩니다. 여기서는 Gaussian kernel의 중심 위치에서 깊이 값을 단순히 혼합하는 것이 부정확하다는 문제를 해결하기 위해, 각 픽셀 \( u \)에서의 깊이 \( d_i(u) \)를 근사적으로 계산하기 위해 local Taylor expansion을 사용합니다.

## 문제점
기본적으로 중심 위치 \( u_i \)에서의 깊이 값 \( d_i(u_i) \)를 사용하여 깊이 값을 계산하는 것은 경사진 평면과 일치하지 않는 부정확한 결과를 초래할 수 있습니다. 이는 깊이 렌더링이 표면 법선과 일치하지 않게 되는 원인이 됩니다.

## 해결책
이 문제를 해결하기 위해, 각 픽셀 \( u \)에서의 깊이를 Gaussian ellipse와의 교차점을 계산하여 보다 정확하게 계산합니다. 이를 간단하게 하기 위해, local Taylor expansion을 사용하여 다음과 같이 근사합니다:

$$
d_i (u) = d_i (u_i) + (W_k R_i) [2,:] J^{-1}_{pr} (u - u_i)
$$

여기서:
- \( d_i (u) \)는 픽셀 \( u \)에서의 깊이 값입니다.
- \( d_i (u_i) \)는 Gaussian kernel의 중심 위치 \( u_i \)에서의 깊이 값입니다.
- \( (W_k R_i) [2,:] \)는 \( W_k R_i \) 행렬의 두 번째 행 전체를 선택하여 벡터로 만든 것입니다.
- \( J^{-1}_{pr} \)는 이미지 공간의 픽셀을 Gaussian surfel의 접평면(tangent plane)으로 역 매핑하는 야코비안(Jacobian)입니다.
- \( (u - u_i) \)는 픽셀 \( u \)와 중심 위치 \( u_i \) 간의 차이 벡터입니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b59e2b5f-c267-43c0-ae54-ce1884cca9eb)
















