---
title: "[Registration] 3D reconstruction process"
last_modified_at: 2025-01-05
categories:
  - Registration
tags:
  - Registration
  - 정합
  - 3D reconstruction
  - COLMAP
  - SIFT
  - bundle adjustment
  - large-scale scene reconstruction
excerpt: "3D reconstruction pipeline: Extract 2D features, matche features across different images, jointly optimize a set of 3D points and camera poses to be consistent with these matches."
classes: wide
comments: true
---

> Reference

>[Block-NeRF: Scalable Large Scene Neural View Synthesis](https://openaccess.thecvf.com/content/CVPR2022/papers/Tancik_Block-NeRF_Scalable_Large_Scene_Neural_View_Synthesis_CVPR_2022_paper.pdf)

### 3D Reconstruction

3D reconstruction techniques have been developed and refined over decades, with modern methods relying on mature software like COLMAP.

Most reconstruction methods follow a common pipeline:

- Extract 2D image features (e.g., SIFT).
- Match features across different images.
- **jointly optimize a set of 3D points and camera poses to be consistent with these matches** (the wellexplored problem of bundle adjustment).

