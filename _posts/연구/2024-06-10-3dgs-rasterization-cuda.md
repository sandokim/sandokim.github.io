---
title: "[3D CV 연구] 3DGS rasterization cuda"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - cuda
  - rasterization
excerpt: "3DGS rasterization은 cuda를 사용합니다."
use_math: true
classes: wide
---

파이썬 자체는 항상 GPU를 사용하여 계산을 수행하기 위해 다른 인터페이스가 필요합니다. 가장 쉬운 방법은 CUDA를 사용하는 것입니다.

3dgs는 CUDA로 rasterization을 코딩하여 GPU를 활용합니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/221

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/aedaa8a0-64fa-4ee3-af42-4260993dca5d)


