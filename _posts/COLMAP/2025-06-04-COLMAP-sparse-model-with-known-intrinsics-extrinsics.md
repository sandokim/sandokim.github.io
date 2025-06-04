---
title: "[COLMAP] COLMAP Sparse model with known intrinsics & known extrinsics"
last_modified_at: 2025-06-03
categories:
  - 공부
tags:
  - COLMAP
  - Poses
  - Camera Extrinsics
  - Camera Intrinsics
  - Sparse Model
  - COLMAP Database
excerpt: "COLMAP의 DB를 조작하여 known intrinsics와 known extrinsics으로 sparse model 만들기"
use_math: true
classes: wide
comments: true
---

> [How to use pre-constructed DB for reconstruction from known poses #433](https://github.com/colmap/colmap/issues/433)

_COLMAP GUI에서 known intrinsics과 known extrinsics를 manual하게 넣어주어, sparse 3D model을 만들 수 있습니다._

본 포스트에서는 다중 카메라 세팅에서 known intrinsics와 known extrinsics를 구하는 방법을 알아봅시다.

### ✅ Known Intrinsics 기반 다중 카메라 보정 및 Undistortion 후 처리
카메라의 intrinsics는 제조사 명세와 실제 렌즈 왜곡, 센서 정렬 등의 오차로 인해 상이할 수 있으므로, 정확한 intrinsics를 얻기 위해서는 각 카메라에 대해 checkerboard 패턴을 활용한 별도의 보정(calibration)이 필요합니다. 이를 위해 각 카메라로 다양한 6 DoF 자세(회전 및 병진)에서 최소 20장 이상의 checkerboard 이미지를 촬영합니다.

보정된 intrinsics를 기반으로 각 이미지에 대해 해당 카메라의 내부 파라미터를 적용하여 undistortion을 수행하면, 왜곡 제거로 인해 이미지 가장자리에 유효하지 않은 검정 영역(black border)이 발생할 수 있습니다. 

예를 들어, COLMAP의 경우 이 검정 영역이 포함되지 않도록 자동으로 이미지를 crop하며, 이로 인해 결과 이미지의 해상도가 원본과 달라질 수 있습니다.

<p align="center">
  <img width="494" alt="image" src="https://github.com/user-attachments/assets/d8663ad9-7d4a-4330-a526-208df257c54c" />
  <br/>
  <em>그림 1. COLMAP의 undistortion 이후 이미지 해상도 변화.</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/23e7069f-bc28-45cd-b08a-1a2daea2e944" />
  <br/>
  <em>그림 2. 원본 이미지 예시.</em>
</p>

<p align="center">
  <img src="https://github.com/user-attachments/assets/95644cb8-2dd0-4225-be88-f6b837bb7432" />
  <br/>
  <em>그림 3. COLMAP undistortion 후 이미지 예시.</em>
</p>

3D 복원 문제에서 다중 뷰 데이터셋을 구성할 때, 일부 저자들은 모든 카메라에 대해 공통된 intrinsics를 적용한 후, 동일한 기준으로 undistortion과 crop을 수행합니다.

대표적으로 LLFF, Mip-NeRF360 데이터셋에서 각 scene을 촬영한 카메라들에 대해 shared intrinsics를 사용합니다.

이는 `sparse/0/cameras.txt`에서 CAMERA_ID의 수가 1개이며, `sparse/0/images.txt`에서 각 이미지에 해당하는 CAMERA_ID가 동일한 ID를 공유하는 것을 봄으로써 확인 가능합니다.

<p align="center">
  <img src="https://github.com/user-attachments/assets/9a191c34-4860-4065-b98b-f763a997e518" />
  <br/>
  <em>그림 4. LLFF의 fern 장면의 sparse/0/cameras.txt에서 CAMERA_ID는 1만 존재</em>
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/cf04d499-3fe3-48dd-b136-e6cac5bc571b" />
  <br/>
  <em>LLFF의 fern 장면의 sparse/0/images.txt에서 IMG_4045를 촬영한 CAMERA_ID는 1</em>
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/525e579a-fed2-428b-9bda-1b8469ce1786" />
  <br/>
  <em>LLFF의 fern 장면의 sparse/0/images.txt에서 IMG_4044를 촬영한 CAMERA_ID는 1</em>
</p>
<p align="center">
  <img src="https://github.com/user-attachments/assets/5b869731-44be-453e-a865-398d7877f61d" />
  <br/>
  <em>LLFF의 fern 장면의 sparse/0/images.txt에서 IMG_4043를 촬영한 CAMERA_ID는 1</em>
</p>

LLFF, Mip-NeRF360, 그리고 CoR-GS의 Sparse View 설정에 맞게 전처리된 DTU 데이터셋은 모두 장면(scene)별로 shared intrinsics를 사용하며, 각 장면에 대해 **하나의 CAMERA_ID**만 존재합니다.
카메라 모델은 LLFF는 SIMPLE_RADIAL, Mip-NeRF360과 DTU는 PINHOLE을 사용합니다. 이 경우 이미지 간 유효 영역이 일관되므로, 추가적인 정합 보정이 필요하지 않습니다.

반면, 실제 환경에서는 shared intrinsics를 사용하지 않고, 각 카메라마다 개별적으로 intrinsics를 보정한 후 이를 바탕으로 undistortion을 적용하는 방식이 일반적입니다. 이 경우 카메라별 왜곡 특성의 차이로 인해 유효 영역의 위치와 크기가 서로 달라질 수 있습니다. 이는 각 이미지가 자신에게 맞는 intrinsics를 기준으로 정의된 픽셀 좌표계와 광선 투사 방향을 정확히 유지함으로써, 여러 시점에서의 3D 복원이 기하적으로 일관되게 이루어지도록 하기 위함입니다.

이 과정에서 focal length인 $f_x$, $f_y$는 변하지 않으며, crop 위치에 따라 이미지 중심점 $(c_x, c_y)$만 보정됩니다. 이는 crop이 센서나 렌즈의 물리적 특성을 변경하는 것이 아니라, 단지 이미지의 좌표계를 재정렬하는 과정이기 때문입니다. 중심점의 보정은 다음과 같은 방식으로 수행됩니다:

$$
\begin{bmatrix}
f_x & 0 & c_x - \delta{x} \\
0 & f_y & c_y - \delta{y} \\
0 & 0 & 1 
\end{bmatrix}
$$

여기서 $(\delta_x, \delta_y)$는 crop 영역의 좌상단이 원본 이미지 기준에서 얼마나 떨어져 있는지를 나타내는 offset입니다. 이를 반영해 intrinsics를 보정함으로써, 이후의 구조 복원(SfM), 삼각측량(triangulation) 등의 단계에서 정확한 기하 정합을 유지할 수 있습니다.








