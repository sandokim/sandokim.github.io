---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 3"
last_modified_at: 2024-07-23
categories:
  - CUDA
tags:
  - cuda programming
  - cuda
  - cpp
  - c++
  - cpp bridge
  - c++ bridge
  - forward
  - backward
  - c++/cuda
  - cuda extension
  - cuda tutorial 3
excerpt: "Pytorch c++/cuda extension tutorial 3"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 3

## trilinear interpolation

### inputs 
- feats # (N, 8, F)
  - N is the number of cubes
  - 8 is the number of vertices
  - F is the number of features on each vertex

- points # (N, 3)
  - point coordinates
  - N is the number of points and each of them corresponds to one cube in the "feats" tensor

### outputs
- feat_interp # (N, F)
  - N points
  - Each point's feature is the interpolation of the 8 vertices of the cube that contains that point, and each point has F features, so after interpolation, each point has F features too.

### input & output shape을 기억하고, 이제 `interpolation_kernel.cu`에서 `trilinear_fw_cu` cuda 코드를 작성해봅시다.




감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 3 - English CC -](https://www.youtube.com/watch?v=-vV08i-Eifs&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=3)
