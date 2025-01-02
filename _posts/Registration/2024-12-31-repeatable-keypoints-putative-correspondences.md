---
title: "[Registration] Repeatable keypoints, keypoint-free method, putative correspondences"
last_modified_at: 2024-12-31
categories:
  - Registration
tags:
  - Registration
  - 정합
  - repeatable keypoints
  - putative correspondences
  - keypoint-free method
excerpt: " Repeatable keypoints, putative correspondences"
use_math: true
classes: wide
comments: true
---

> Reference

> [Geometric Transformer for Fast and Robust Point Cloud Registration](https://openaccess.thecvf.com/content/CVPR2022/papers/Qin_Geometric_Transformer_for_Fast_and_Robust_Point_Cloud_Registration_CVPR_2022_paper.pdf)

### Repeatable Keypoints
Repeatable keypoints are distinctive points in an image or point cloud that can be consistently detected across different views or instances of the same object or scene. 

These points have specific characteristics:
- Uniqueness: They stand out from surrounding pixels due to distinct visual attributes.
- Invariance: They remain detectable despite image transformations like rotation, scaling, or lighting changes.
- Repeatability: They can be reliably identified in different versions of the same scene.
Repeatable keypoints are crucial for various computer vision tasks, including object recognition, tracking, and 3D reconstruction.

### Keypoint-free method
keypoint-free method는 3D 포인트 클라우드 처리와 객체 포즈 추정에서 사용되는 접근 방식입니다. 
- 키포인트 검출 불필요: 전통적인 방식과 달리 반복 가능한 키포인트를 검출할 필요가 없습니다3.
- 직접 매칭: 이미지나 포인트 클라우드에서 직접 2D-3D 대응점을 찾습니다

### Superpoints & dense point correspondences

Inspired by the recent advances in image matching [22, 25, 39], keypoint-free methods [36] **downsample the input point clouds into superpoints and then match them through examining whether their local neighborhood (patch) overlaps.** **Such superpoint (patch) matching is then propagated to individual points, yielding dense point correspondences.** Consequently, **the accuracy of dense point correspondences highly depends on that of superpoint matches.**

![image](https://github.com/user-attachments/assets/ffe29722-cc63-4731-9757-2f3795829fe4)

위 그림 왼쪽 superpoint (patch) level / 오른쪽 dense point level

### Putative Correspondences
Putative correspondences are initial matches between keypoints in two different images or point clouds4. The process typically involves:
- Detecting keypoints in both images or point clouds.
- Describing the local area around each keypoint using feature descriptors.
- Matching these descriptors to find potential correspondences.

These initial matches are called "putative" because they are not guaranteed to be correct. Many of these correspondences may be incorrect due to various factors, including:
- Perceptual aliasing (similar-looking but different parts of a scene)
- Repetitive patterns in the scene
- Changes in viewpoint or lighting

The challenge lies in filtering these putative correspondences to find the true matches, which is often done using geometric constraints or robust estimation techniques like RANSAC.

### Challenges in Correspondence-Based Methods
The difficulty in detecting repeatable keypoints across two point clouds, especially with small overlapping areas, often leads to a low inlier ratio in the putative correspondences. This means that a large proportion of the initial matches are incorrect, making it harder to establish reliable correspondences for tasks like 3D reconstruction or object recognition.
