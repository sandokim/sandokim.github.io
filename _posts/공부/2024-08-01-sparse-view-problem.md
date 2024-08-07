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
  - few-input setting
  - SfM point clouds
excerpt: "Sparse View setting에서 카메라 포즈는 전체 이미지를 COLMAP으로 돌려서 얻습니다."
use_math: true
classes: wide
comments: true
---

# Sparse View Problem Setting

### View의 수는 다음과 같이 합니다.

- **train views**: 3 views, 4 views, 5 views, 6 views 
- **test views**: test set에서 사용하는 views

### 페이퍼에서 사용하기 좋은 표현
- `... , using only a handful of input images.`
- `We show that our approach improves over recent and concurrent work that uses a sprase depth from SfM or multi-view stereo (MVS) in NeRF [10, 26].`
  - `[10] Kangle Deng, Andrew Liu, Jun-Yan Zhu, and Deva Ramanan. Depth-supervised nerf: Fewer views and faster training for free. ArXiv, abs/2107.02791, 2021.`
  - `[26] YiWei, Shaohui Liu, Yongming Rao,Wang Zhao, Jiwen Lu, and Jie Zhou. Nerfingmvs: Guided optimization of neural radiance fields for indoor multi-view stereo. In ICCV, 2021.`
- `In this work, we directly address the few-input setting, proposing a strategy that ...`
- `... in the few-input setting without ...`
- `To handle inaccuracy in the sparse reconstruction, the 3D points are weighted according to their reprojection error.`
- `0 values of the sparse depth indicate invalid pixels, and ...`
- `, the density of SfM point clouds varies significantly across space, depending on the number of image features. E.g., SfM reconstructions from 18-20 images per ScanNet scene lead to sparse depth maps with 0.04% valid pixels on average.`
- `To avoid both the effort of running SfM on a large dataset and the possibility of SfM failures in the training data, the sparse depth input is sampled from the range sensor depth.`
- `Specifically, a SIFT feature extractor, e.g., from COLMAP [23], is used to determine locations where sparse depth points would exist in a SfM reconstruction.`
- `, and n is the number of valid pixels in the dense sensor depth map.`

### 페이퍼에서 사용하기 좋은 few input views의 문제점

- `"Floaters" are a common problem when applying NeRF approaches in a setting with few input views.`

### 페이퍼에서 사용하기 좋은 Experimental Setup

#### COLMAP SfM, camera parameters, point cloud, sparse depth maps
- `We run COLMAP SfM [23] to obtain camera parameters and sparse depth. Specifically, we run SfM on all images to obtain camera parameters. To ensure a clean split between train and test data, we withhold the test images when computing the point cloud used for rendering the sparse depth maps`
  - sparse depth maps을 만들기 위한, point cloud를 computing할 때는 test images를 withhold하여 배제하였습니다.
 
#### Dataset train / test split
- **ScanNet**: 18~20 train images / 8 test images
- **Matterport3D**: 24~26 train images / 8 test images


### Evaluation Metrics
- `For quantitative comparison, we compute the peak signal-to-noise ratio (PSNR), the Structural Similarity Index Measure (SSIM) [25] and the Learned Perceptual Image Patch Similarity (LPIPS) [28] on the RGB of novel views as well as the root-mean-square error (RMSE) on the expected ray termination depth of NeRF against the sensor depth in meters.`

### References for my paper
- COLMAP 
  - `Schonberger, Johannes L., and Jan-Michael Frahm. "Structure-from-motion revisited." Proceedings of the IEEE conference on computer vision and pattern recognition. 2016.`
- peak signal-to-noise ratio (PSNR)
  - No need reference
- Structure Similarity Index Measure (SSIM)
  - `Zhou Wang, Alan Conrad Bovik, Hamid R. Sheikh, and Eero P. Simoncelli. Image quality assessment: from error visibility to structural similarity. IEEE Transactions on Image Processing, 13:600–612, 2004. 5`
- Learned Perceptual Image Patch Similarity (LPIPS)
  - `Richard Zhang, Phillip Isola, Alexei A. Efros, Eli Shechtman, and Oliver Wang. The unreasonable effectiveness of deep features as a perceptual metric. 2018 IEEE/CVF Conference on Computer Vision and Pattern Recognition, pages 586–595, 2018. 5`
- root-mean-square error (RMSE) for depth
  - No need reference

-----

### Sparse Views에 대한 카메라 포즈

[CVPRW 2024: Depth-Regularized Optimization for 3D Gaussian Splatting in Few-Shot Images](https://openaccess.thecvf.com/content/CVPR2024W/3DMV/papers/Chung_Depth-Regularized_Optimization_for_3D_Gaussian_Splatting_in_Few-Shot_Images_CVPRW_2024_paper.pdf)

- Fair comparison을 위해서, **scene을 촬영한 모든 이미지를 사용하여 COLMAP을 돌려서 각 이미지에 대한 카메라 포즈를 얻어서 사용합니다.**
   
  ![image](https://github.com/user-attachments/assets/dfe565f3-1e30-4bd0-a2e5-af3472f3d484)

-----

### data-efficient novel view synthesis (18-36 views)

[CVPR 2022: Dense Depth Priors for Neural Radiance Fields from Sparse Input Views](https://openaccess.thecvf.com/content/CVPR2022/papers/Roessle_Dense_Depth_Priors_for_Neural_Radiance_Fields_From_Sparse_Input_CVPR_2022_paper.pdf)

- "18개의 images만으로 entire scene에 대한 novel view synthesis를 했습니다."
- 18~36 images로 Novel View Synthesis (NVS)를 했으면, sparse view, few view라기 보다는 data-efficient approach라고 표현합니다.

  ![image](https://github.com/user-attachments/assets/29b6d82a-3d50-4efa-af78-dc49dfd71ced)
  ![image](https://github.com/user-attachments/assets/bb8cc48c-56d4-46d6-bf0d-69be3c4af408)


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

### dense depth priors, without the need for additional depth inpu (e.g., from an RGB-D sensor) of the scene

- Sparse view problem에서는 NeRF를 guide하기 위해 depth input을 주기 위해, RGB-D sensor를 사용할 수 있습니다.
- 추가적인 RGB-D sensor 사용하지 않고도 dense depth priors를 주는 것이 novelty가 될 수 있습니다.

  ![image](https://github.com/user-attachments/assets/54b6644e-264d-42e7-9888-d592bc248a05)


-----


