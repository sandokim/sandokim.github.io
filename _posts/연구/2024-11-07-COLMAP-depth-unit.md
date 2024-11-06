---
title: "[3D CV 연구] 3DGS COLMAP depth unit"
last_modified_at: 2024-11-07
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - COLMAP unit
  - COLMAP depth unit
  - real-world scales
excerpt: "COLMAP의 real-world scales를 알 수는 없습니다."
use_math: true
classes: wide
comments: true
---

> [Depth unit #1043](https://github.com/graphdeco-inria/gaussian-splatting/issues/1043)

![image](https://github.com/user-attachments/assets/e66bc1e2-1032-46d0-8407-9c4976d768f5)

COLMAP은 real-world scales를 가지고 있지 않습니다. 

따라서 3dgs로 rendering된 depths가 pixel values (0~255)를 가질 때, 이 pixel values를 real-world units (예를 들어 meters)로 변환하는 것은 불가능합니다.

3DGS 저자왈: COLMAP에서 camera poses가 pixel들로부터 추정되었기 때문에, Gaussian들의 real-world scales를 알 수는 없습니다. (2D 이미지들은 z-axis를 잃어버리기 때문입니다.)

필요하다면 카메라 포즈를 COLMAP으로 추정하지 않고 LiDAR sensor같은걸 사용해서 real-world units으로 촬영된 데이터를 얻어 볼 수는 있습니다.

이 경우 real-world units을 알기 때문에 Gaussian들의 real-world scales를 알 수 있을 것입니다.

