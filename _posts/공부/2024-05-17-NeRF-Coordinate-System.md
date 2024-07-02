---
title: "[3D CV] NeRF Coordinate System"
last_modified_at: 2024-07-02
categories:
  - 공부
tags:
  - Multiple View Geometry
  - NeRF
  - coordinate system
  - COLMAP
  - viewing frustum
  - viewing direction
excerpt: "NeRF Coordinate system 이해 정리"
use_math: true
classes: wide
---

### NeRF의 Coordinate System을 이해해보자

### NeRF 데이터셋은 기본적으로 각 카메라 포즈에 대한 정보는 World에서 각 Camera좌표까지의 변환을 모은 것이라고 생각하면 됩니다.

- 코드에 따라 다를 수 있지만, 기본적으로 W2C로 정의하여 불러 옵니다. (사람에 따라 C2W라고 정의해버리면 헷갈릴 수 있습니다. 주의해야합니다.)

- MipNeRF360(NeRF++) 데이터를 불러오는 부분을 봅시다. `cam`마다 `cam_info`에서 `R`과 `T`를 불러와서 `W2C (world to camera)` 변환을 정의해줍니다.
- `C2W (camera to world)`는 `W2C (world to camera)`를 역변환하여 정의합니다.
- 만약 카메라의 center을 알고 싶다면 World Coordinate System을 기준으로 정의해야합니다.
- 그러므로 카메라가 World Coordinate System을 기준으로 할 때의 translation이 카메라의 center가 됩니다.
- `C2W (camera to world)`는 Camera Coordinate System에서 World Coordinate System으로 변환해줍니다.
- 즉, `C2W (camera to world)`는 World Coordinate System이 기준일 때, 카메라의 Rotation과 Translation을 포함하는 행렬입니다.
- 따라서, `C2W (camera to world)`에서 마지막 열을 인덱싱하면 World Coordinate System에서 카메라의 center 위치를 얻는 것입니다.

### C2W를 수식으로 쓰면 다음과 같습니다.

- `C2W` 변환 행렬은 카메라 좌표계에서 월드 좌표계로 변환하는 행렬입니다. 이 행렬은 보통 회전 행렬 $R$과 변환 벡터 $T$로 구성됩니다.
$$
C2W = \begin{bmatrix}
R & T \\
0 & 1
\end{bmatrix}
$$

- 여기서 $R$은 3x3 회전 행렬이고, $T$는 3x1 변환 벡터입니다. 보다 구체적으로 표현하면:

$$
C2W = \begin{bmatrix}
R_{11} & R_{12} & R_{13} & T_x \\
R_{21} & R_{22} & R_{23} & T_y \\
R_{31} & R_{32} & R_{33} & T_z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

- 여기서 `C2W # shape 4x4`에서, `C2W[:3, 3:4]`와 같이 취하면 World Coordinate System 기준 camera center 위치를 구할 수 있습니다.

$$
camera \ center = \begin{bmatrix}
T_x \\
T_y \\
T_z \\
\end{bmatrix}
$$

```python
# 3dgs/scene/dataset_reader.py

def getNerfppNorm(cam_info):
    def get_center_and_diag(cam_centers):
        cam_centers = np.hstack(cam_centers)
        avg_cam_center = np.mean(cam_centers, axis=1, keepdims=True)
        center = avg_cam_center
        dist = np.linalg.norm(cam_centers - center, axis=0, keepdims=True)
        diagonal = np.max(dist)
        return center.flatten(), diagonal

    cam_centers = []

    for cam in cam_info:
        W2C = getWorld2View2(cam.R, cam.T)
        C2W = np.linalg.inv(W2C)
        cam_centers.append(C2W[:3, 3:4])

    center, diagonal = get_center_and_diag(cam_centers)
    radius = diagonal * 1.1

    translate = -center

    return {"translate": translate, "radius": radius}
```



<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f7a817d0-9ee1-4cdf-9a0d-71d2deb1df18" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/64f409ab-9094-4ca3-9301-cd306d14a91a" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/e9a689f2-0c24-4094-9565-b619e0bb156c" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/b255e68d-bfd9-48e0-a1e1-61e0fbec1820" alt="Image">
</p>


<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f87d491e-1d43-4c81-a019-590f5e5b5124" alt="Image">
</p>
