---
title: "[3D CV] NeRF, 3dgs Sparse View Problem"
last_modified_at: 2024-07-04
categories:
  - 공부
tags:
  - Multiple View Geometry
  - NeRF
  - coordinate system
  - NeRF custom dataset
  - COLMAP
  - 3dgs
  - 3d gaussian splatting
  - sparse view
  - sparse views
  - camera pose
excerpt: "Sparse View setting에서 카메라 포즈는 전체 이미지를 COLMAP으로 돌려서 얻습니다."
use_math: true
classes: wide
comments: true
---

# Sparse View Problem Setting

### View의 수는 다음과 같이 합니다.

- **train views**: 3 views, 4 views, 5 views, 6 views 
- **test views**: test set에서 사용하는 views

### Sparse Views 애 대한 카메라 포즈

- Fair comparison을 위해서, **scene을 촬영한 모든 이미지를 사용하여 COLMAP을 돌려서 각 이미지에 대한 카메라 포즈를 얻어서 사용합니다.**
   
  ![image](https://github.com/user-attachments/assets/dfe565f3-1e30-4bd0-a2e5-af3472f3d484)


### Reference
- [Depth-Regularized Optimization for 3D Gaussian Splatting in Few-Shot Images](https://openaccess.thecvf.com/content/CVPR2024W/3DMV/papers/Chung_Depth-Regularized_Optimization_for_3D_Gaussian_Splatting_in_Few-Shot_Images_CVPRW_2024_paper.pdf)


