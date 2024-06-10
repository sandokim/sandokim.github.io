---
title: "[3D CV 연구] 3DGS big gaussians culling in view-space code bug"
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
  - 3dgs bug
excerpt: "3DGS original code has a bug for big gaussians culling"
use_math: true
classes: wide
---

3dgs original paper에서 view-space size culling이랑 world-space size culling으로 big gaussians을 제거하는 방식을 취한다고 설명하는데,

저자의 3dgs original code에 버그가 있어서 실제로는 view-space culling은 적용이 안되고 있고, world-space culling만 적용되고 있다고 합니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/123

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6d01e1d6-2251-4327-afb9-3761b43d8175)

https://github.com/graphdeco-inria/gaussian-splatting/issues/643

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/04d459a5-9264-4b84-aed6-2b514cb7e780)
