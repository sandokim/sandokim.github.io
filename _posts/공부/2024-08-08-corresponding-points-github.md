---
title: "[3D CV] Correspondence Matching Algorithm"
last_modified_at: 2024-08-08
categories:
  - 공부
tags:
  - Multiple View Geometry
  - COLMAP
  - Structure from Motion
  - SfM
  - Structure-from-Motion Revisited
  - CVPR
  - Dense Matching
  - PDC-Net
  - correspondence
excerpt: "Dense Matching으로 2개의 views 사이에서 매칭되는 pixels p,q를 찾을 수 있습니다."
use_math: true
classes: wide
comments: true
---

# Correspondence Matching Algorithm

[Dense Matching github](https://github.com/PruneTruong/DenseMatching?tab=readme-ov-file)에서 2개의 views 사이에서 매칭되는 pixels p,q를 찾을 수 있습니다.

가장 대표적인 correspondence matching network는 PDC-Net이며 CVPR2021에 발표되었으며, CVPR2023 SPARF: Neural Radiance Fields from Sparse and Noisy Poses (hightlight paper)에 사용되었습니다.

