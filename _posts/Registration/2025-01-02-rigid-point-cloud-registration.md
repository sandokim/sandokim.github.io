---
title: "[Registration] Rigid point cloud registration"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - rigid point cloud registration
excerpt: "Rigid point cloud registration refers to the problem of finding the optimal rotation and translation parameters that align two point clouds."
use_math: true
classes: wide
comments: true
---

> Reference

> [REGTR: End-to-end Point Cloud Correspondences with Transformers](https://openaccess.thecvf.com/content/CVPR2022/papers/Yew_REGTR_End-to-End_Point_Cloud_Correspondences_With_Transformers_CVPR_2022_paper.pdf)

### Rigid point cloud registration

**Rigid point cloud registration refers to the problem of finding the optimal rotation and translation parameters that align two point clouds.**

A common solution to point cloud registration follows the following pipeline: 

1. detect salient keypoints
2. compute feature descriptors for these keypoints
3. obtain putative correspondences via nearest neighbor matching
4. estimate the rigid transformation, typically in a robust fashion using RANSAC.

In recent years, researchers have applied learning to point cloud registration.

Many of these works focus on **learning the feature descriptors** [14,15,54] and sometimes also the **keypoint detection** [2, 20, 49].

**The final two steps generally remain unchanged and these approaches still require nearest neighbor matching and RANSAC to obtain the final transformation.**

These algorithms do not take the post-processing into account during training, and their performance can be sensitive to the post-processing choices to pick out the correct correspondences, e.g. number of sampled interest points or
distance threshold in RANSAC.
