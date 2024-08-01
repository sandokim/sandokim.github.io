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
  - camera pose
  - fewshot
  - few views
excerpt: "Sparse View setting에서 카메라 포즈는 전체 이미지를 COLMAP으로 돌려서 얻습니다."
use_math: true
classes: wide
comments: true
---

# Sparse View Problem Setting

### View의 수는 다음과 같이 합니다.

- **train views**: 3 views, 4 views, 5 views, 6 views 
- **test views**: test set에서 사용하는 views

### Sparse Views에 대한 카메라 포즈

[CVPRW 2024: Depth-Regularized Optimization for 3D Gaussian Splatting in Few-Shot Images](https://openaccess.thecvf.com/content/CVPR2024W/3DMV/papers/Chung_Depth-Regularized_Optimization_for_3D_Gaussian_Splatting_in_Few-Shot_Images_CVPRW_2024_paper.pdf)

- Fair comparison을 위해서, **scene을 촬영한 모든 이미지를 사용하여 COLMAP을 돌려서 각 이미지에 대한 카메라 포즈를 얻어서 사용합니다.**
   
  ![image](https://github.com/user-attachments/assets/dfe565f3-1e30-4bd0-a2e5-af3472f3d484)

### data-efficient novel view synthesis (18 views)

[CVPR 2022: Dense Depth Priors for Neural Radiance Fields from Sparse Input Views](https://openaccess.thecvf.com/content/CVPR2022/papers/Roessle_Dense_Depth_Priors_for_Neural_Radiance_Fields_From_Sparse_Input_CVPR_2022_paper.pdf)

- "18개의 images만으로 entire scene에 대한 novel view synthesis를 했습니다."

  ![image](https://github.com/user-attachments/assets/29b6d82a-3d50-4efa-af78-dc49dfd71ced)

-  "10개의 views만 주어졌을 때, 몇 가지 이유로 인해, desirable한 results를 produce하지 못합니다."
  -  NeRF는 RGB values에만 의존하여, input images 간의 correspondences를 determine 합니다. 결론적으로, 충분한 수의 이미지들이 주어져야, NeRF는 correspondence problem의 inherent ambiguity를 극복할 수 있습니다.
  
      ![image](https://github.com/user-attachments/assets/d7a3da3e-1a69-4363-a46b-bd2e8ce5ee06)
      ![image](https://github.com/user-attachments/assets/4388306e-9576-4c78-b409-572bc30816be)

### SfM에서 correspondence를 찾기 어려운 대표적인 몇가지 이유
1. images with siginificantly less overlap with each other.
2. large areas with minimal texture.
3. inconsistent color values across views, e.g., due to white balance or lens shading artifacts.

위의 이유들로 인해 correspondences를 제대로 찾지 못하여, very sparse SfM reconstructions이 되며, 대부분 severe outliers를 가지게 됩니다.
   
![image](https://github.com/user-attachments/assets/91159b42-b7b6-42f2-8b3a-11ac9b655389)

-----


