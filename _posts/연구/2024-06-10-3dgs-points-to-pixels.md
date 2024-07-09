---
title: "[3D CV 연구] 3DGS How can I get the points' indices which contribute to the pixels?"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - points contribute to pixels
excerpt: "3DGS How can I get the points' indices which contribute to the pixels?"
use_math: true
classes: wide
comments: true
---

CUDA rasterization을 수정하여 특정 pixels에 기여하는 points를 알아내기 위해서는 last_contributor 변수에 기록된 값을 추적하면 됩니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/159

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d2f61bd6-c0a2-4d04-b941-f5b65a0037a0)

https://github.com/graphdeco-inria/diff-gaussian-rasterization/blob/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d/cuda_rasterizer/forward.cu#L361
