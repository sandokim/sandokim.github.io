---
title: "[Registration] Point cloud registration is for large-scale 3D scene reconstruction"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - HLoc
  - SuperPoint
  - SuperGlue
  - feature extractor
  - feature matcher
  - GaussReg
  - large-scale 3D scene reconstruction
excerpt: "Point cloud registration is a fundamental problem for large-scale 3D scene scanning and reconstruction"
use_math: true
classes: wide
comments: true
---

> Reference

> [GaussReg: Fast 3D Registration with Gaussian Splatting](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/02380.pdf)

> [From coarse to fine: Robust hierarchical localization at large scale](https://openaccess.thecvf.com/content_CVPR_2019/papers/Sarlin_From_Coarse_to_Fine_Robust_Hierarchical_Localization_at_Large_Scale_CVPR_2019_paper.pdf)

> [DReg-NeRF: Deep Registration for Neural Radiance Fields](https://openaccess.thecvf.com/content/ICCV2023/papers/Chen_DReg-NeRF_Deep_Registration_for_Neural_Radiance_Fields_ICCV_2023_paper.pdf)

### Registration 순서: feature extraction, feature matching, transformation estimation

**Registration의 기본 순서는 일반적으로 다음과 같이 이루어집니다:**

- Feature Extraction (특징 추출):
  - 두 데이터(예: 두 이미지, 포인트 클라우드 등)에서 의미 있는 특징점(keypoints)과 디스크립터(descriptors)를 추출합니다.
  - 예: SIFT, ORB, SuperPoint 등
- Feature Matching (특징 매칭):
  - 추출된 특징점을 기반으로 두 데이터 간의 대응점을 찾습니다.
  - 방법: 최근접 이웃 검색(NN Search), KNN, RANSAC 등을 사용하여 신뢰할 수 있는 매칭을 필터링
- Transformation Estimation (변환 추정):
  - 매칭된 대응점을 이용해 두 Point cloud 간의 변환 관계(예: 회전, 이동, 스케일 등)를 계산합니다.
  - 예: homography, rigid transformation, Affine transformation, ICP(Iterative Closest Point) 등
 
### Point cloud registration

> [DReg-NeRF: Deep Registration for Neural Radiance Fields](https://openaccess.thecvf.com/content/ICCV2023/papers/Chen_DReg-NeRF_Deep_Registration_for_Neural_Radiance_Fields_ICCV_2023_paper.pdf)

**Point cloud registration is** a classic problem in 3D computer vision, which aims at **computing the relative transformation from the source point cloud to the target point cloud.**

**Point cloud registration은 source point cloud와 target point cloud의 3D 좌표계에서 correspondences를 기반으로 상대적인 rigid transformation을 계산하는 문제입니다.**

이 과정에서는 correspondences(두 점군 사이의 대응점)에 대해 confidence scores를 계산합니다. 이 점수는 각 대응점의 신뢰도를 나타내며, 

- 높은 confidence scores를 가진 correspondences는 더 중요한 것으로 간주되어 가중치가 더 크게 부여됩니다.
- 반면, 낮은 confidence scores를 가진 correspondences는 신뢰도가 낮다고 판단되어 계산에서 제외(mask out)됩니다.

여기서 confidence scores란, 예측된 점 $\tilde{X}\_{source}$와 $\tilde{X}\_{target}$이 실제로 서로 대응되는 점일 가능성을 나타냅니다. 동시에, **이 점들이 각각 source NeRF와 target NeRF에서 얼마나 잘 보이는지(가시성, visibility)**를 반영합니다. 

간단히 말하면, 점수가 높을수록 두 점이 진짜 대응점일 확률이 높고, 해당 점이 NeRF의 렌더링된 3D 공간에서 카메라 시점(viewpoint)에서 가려지지 않고 명확히 관찰될 수 있는, 가시성이 높은 위치에 있다는 의미입니다.

After encoding features by transformer, we further adopt a single-head attention layer to predict the corresponding point locations $\tilde{X}\_{source}$ and confidence scores $\tilde{S}\_{source}$ of the source voxel points $\hat{X}_{source}$ in the target NeRF’s coordinate frame. 

Similarly, we also predict the corresponding point locations $\tilde{X}\_{target}$ and confidence scores $S_{target}$ of the target voxel points $\hat{X}_{target}$ in the source NeRF’s coordinate frame. 

Finally, we **utilize the predicted correspondences to compute the relative rigid transformation.**

**The confidence scores are used as weights that mask out the irrelevant correspondences and can be interpreted as how likely the predicted points from $\tilde{X}\_source$ and $\tilde{X}\_target$ are correspondences and are visible in the source NeRF and the target NeRF.** 

### 3D point cloud registration (correspondence searching & transformation estimation)

3D point cloud registration는 feature extraction과 feature matching을 합쳐 부르는 correspondence searching과 transformation estimation으로 구성된다고 말할 수도 있습니다.

3D point cloud registration has been developed for decades. Given two overlapping point clouds with different coordinate systems, the target of this task is to find the transformation between them. 

Traditional methods [4, 17, 18, 21, 23, 40, 42] divide this process into two parts: **correspondence searching and transformation estimation.**

Correspondence searching involves finding sparse matched feature points between the source and target point clouds. 

Transformation estimation is to calculate the transformation matrix using these correspondences.

These two stages will be conducted iteratively to find the optimal transformation.

### Point cloud registration을 하는 이유는 large-scale 3D scene reconstruction을 하기 위해서입니다.

**Point cloud registration is a fundamental problem for larges-cale 3D scene scanning and reconstruction.**

In traditional 3D scene scanning and reconstruction, a large-scale scene is usually divided into different blocks, resulting in many independent sub-scenes that may not in the same coordinate system. 

Therefore, the registration between them plays a crucial role.

The mainstream methods typically involve **extracting features from point clouds** and **locating matching points to calculate the transformation between the two input scenes.**

### NeRF로 large-scale scene reconstruction을 하는 이유

1. large-scale reconstruction을 위해서는 data collection process가 길어지게 됩니다. 즉, 시간이 걸려 추가적으로 얻어지는 데이터가 생기게 됩니다. 이 데이터로 학습되어 얻어지는 scene을 registration해서 더하는 방식으로 scene을 확장합니다.
2. NeRF을 많은 이미지로 학습 시키는 것은 시간이 오래걸림. 따라서 large-scale scene을 여러 개의 small scene들로 나눠서 병렬적으로 학습한 다음, registration으로 small scene들을 합칠 수 있습니다.

When considering large-scale scene reconstruction based on NeRF, there are two main challenges: 

1. Due to the complex occlusions present in real-world scenes, lots of images or videos are often required to capture for large-scale reconstruction, leading to a time-consuming data collection process. 
2. Optimizing NeRF with numerous images is computationally intensive. Therefore, a direct approach is to divide a large-scale scene into some smaller scenes, reconstruct them separately, and then use registration to combine all these small scenes together.

### HLoc vs GaussReg

Our GaussReg is 44× faster than HLoc (SuperPoint as the feature extractor and SuperGlue as the matcher) with comparable accuracy.
