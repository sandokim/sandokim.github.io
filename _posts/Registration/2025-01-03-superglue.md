---
title: "[Registration] SuperGlue, Predator, REGTR"
last_modified_at: 2024-12-31
categories:
  - Registration
tags:
  - Registration
  - 정합
  - SuperGlue
  - Predator
  - REGTR
  - self-attention
  - cross-attention
  - matching 2D image correspondences
excerpt: "Matching 2D image correspondences: SuperGlue, Predator, REGTR"
use_math: true
classes: wide
comments: true
---

> Reference

> [DReg-NeRF: Deep Registration for Neural Radiance Fields
](https://openaccess.thecvf.com/content/ICCV2023/papers/Chen_DReg-NeRF_Deep_Registration_for_Neural_Radiance_Fields_ICCV_2023_paper.pdf)

> [Superglue: Learning feature matching with graph neural networks]()

> [Predator: Registration of 3d point clouds with low overlap]()

> [REGTR: end-to-end point cloud correspondences with transformers]()

### Matching 2D image correspondences: SuperGlue, Predator, REGTR

Inspired by **SuperGlue** [32], which is a deep learning method for **matching 2D image correspondences**, Predator [16] and REGTR [42] adopted the self-attention and cross-attention mechanisms from SuperGlue to learn the correlation for pairwise low overlapping point clouds. 

The ground-truth overlapping scores are computed from dense point clouds and used to mask out the correspondences outside the overlapping regions.

### SuperGlue, Predator, REGTR 모두 self-attention, cross-attention mechanism 사용

- In the self-attention layers, $Q = K = V$ represents the same feature tensor in each block. 
- In the cross-attention layers, **the keys, and the values are the feature tensors from the other block.**

- The self-attention mechanism enables the network to learn the relationship inside the same feature points,
- while the cross-attention mechanism enables the communication of the different feature points.
