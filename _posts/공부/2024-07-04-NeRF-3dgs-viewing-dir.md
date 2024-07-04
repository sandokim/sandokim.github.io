---
title: "[3D CV] NeRF, 3dgs에서 viewing direction d의 의미"
last_modified_at: 2024-07-04
categories:
  - 공부
tags:
  - Multiple View Geometry
  - NeRF
  - coordinate system
  - camera coordinate system
  - world coordinate system
  - ccs
  - wcs
  - viewing direction d
  - viewing dir
excerpt: "NeRF, 3dgs에서 viewing direction d의 의미"
use_math: true
classes: wide
---

## NeRF, 3dgs에서 viewing direction d는 현재 카메라 좌표계를 기준으로 합니다.

- `viewing direction d`는 다음과 같이 설명 가능합니다.
  - NeRF에서는 현재 카메라 죄표계를 기준으로 ray위의 sample point까지의 상대적인 각도
  - 3dgs에서는 현재 카메라 좌표계를 기준으로 3d gaussian까지의 상대적인 각도

### `viewing direction d`을 3D Gaussian에 대해 정의하는 예시를 봅시다.

- 3DGS-Avatar 페이퍼에서는 3D Gaussian에 대한 viewing direction d를 `현재 카메라 좌표계(=현재 frame)을 기준좌표계`로 삼을 때, `3D gaussian의 상대적인 위치`로부터 구했다고 서술합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c9135a01-140c-4dc3-a1dc-5c4d9743573e)

### Reference
- [3DGS-Avatar: Animatable Avatars via Deformable 3D Gaussian Splatting](https://arxiv.org/abs/2312.09228)
