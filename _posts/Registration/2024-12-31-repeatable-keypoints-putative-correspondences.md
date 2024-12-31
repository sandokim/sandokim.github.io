---
title: "[Registration] Repeatable keypoints, putative correspondences"
last_modified_at: 2024-12-31
categories:
  - Registration
tags:
  - Registration
  - 정합
  - repeatable keypoints
  - putative correspondences
excerpt: " Repeatable keypoints, putative correspondences"
use_math: true
classes: wide
comments: true
---

### Repeatable Keypoints
Repeatable keypoints are distinctive points in an image or point cloud that can be consistently detected across different views or instances of the same object or scene13. These points have specific characteristics:
- Uniqueness: They stand out from surrounding pixels due to distinct visual attributes.
- Invariance: They remain detectable despite image transformations like rotation, scaling, or lighting changes.
- Repeatability: They can be reliably identified in different versions of the same scene.
Repeatable keypoints are crucial for various computer vision tasks, including object recognition, tracking, and 3D reconstruction.

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
