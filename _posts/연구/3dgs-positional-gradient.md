---
title: "[3D CV 연구] 3DGS positional gradient in NDC/view space not in world space"
last_modified_at: 2024-06-04
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - near far
  - z_far z_near
  - scene extent
  - pruning
  - prune
  - big gaussians culling
  - view-space size culling
  - world-space size culling
  - NDC/view space
excerpt: "3DGS positional gradient는 NDC/view space에서 계산됩니다."
use_math: true
classes: wide
---

3DGS positional gradient는 NDC/view space에서 계산됩니다. 

이는 gradient들을 normalize함으로써 모든 gaussian들이 camera/world space에 어디에 위치하든 image에 미치는 영향력을 동등하게 만드는 역할을 합니다.

Intuitively you can **think NDC space as a way to normalize the gradients and treat all gaussians equally with respect to the impact they have on the
image no matter where they are in camera/world space.**

https://github.com/graphdeco-inria/gaussian-splatting/issues/584

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0aad0eb8-4992-4e8d-a9c6-838ede51bc0f)


