---
title: "[3D CV] Disparity map, Inverse Depth map, Depth map"
last_modified_at: 2024-07-04
categories:
  - 공부
tags:
  - Multiple View Geometry
  - disparity map
  - inverse depth map
  - depth map
  - depth budget
  - disparity range
  - positive disparity
  - negative disparity
  - stereo matching
  - single-image depth estimation
  - metric depth estimation (MDE)
  - relative depth estimation (RED)
excerpt: "Depth map visualize는 inverse depth로 표현합니다."
use_math: true
classes: wide
comments: true
---

# Metric Depth Estimation (MDE) & Relative Depth Estimation (RDE)

**There are two branches of work: metric depth estimation (MDE) and relative depth estimation (RDE). The dominant branch is MDE, where the goal is to estimate depth in absolute physical units, i.e. meters.**

### Metric Depth Estimation (MDE)는 물리적인 단위 (i.e. meters)를 표현합니다.
- 실제 물리적인 거리인 metric depth를 측정하므로 robotics에서 mapping, planning, navigation, object recognition, 3D reconstruction, image editing등에 사용됩니다.
- 그러나, single metric depth estimation model을 다양한 datasets에 대해 학습시키면 성능이 떨어집니다.
- 특히, image collection에서 depth scale에서 큰차이를 보이는 indoor and outdoor images를 포함한 경우 MDE 성능이 떨어져 depth를 잘 추정하지 못합니다.
- 결과적으로 MDE models은 보통 특정한 datasets에 overfit하고 다른 datasets으로 generalize를 잘하지 못합니다.

### Relative Depth Estimation (RDE)는 image frame 내에서 상대적인 depth만 표현합니다.
- RDE는 scale factor를 고려하지 않음으로써, 다양한 환경에서 depth scale variation이 크게 다른 것들을 고려할 수 있습니다.
- 따라서 RDE는 disparity만 supervision으로 주어져도 충분하며, scale과 camera parameters가 요구되지 않습니다.
- In RDE, depth predictions per pixel are only consistent relative to each other across image frames and the scale factor is unknown.

# ZoeDepth

- ZoeDepth는 MiDaS로 먼저 large datasets으로 Relative Depth Estimation을 학습하여, 일반화 성능을 얻고, 특정 데이터셋들에 대해 fine-tuning하여 Metric Depth Estimation을 학습하였습니다.
- 이로써, ZoeDepth를 사용하면 실제 물리적인 의미를 갖는 Metric Depth를 Estimation한 Depth map을 얻을 수 있습니다.


# Depth map은 Inverse Depth로 시각화합니다.

- depth map은 가까우면 0, 멀어지면 그 값이 inf까지 커질수 있습니다.
- inverse depth map은 가까우면 값이 크게, 멀어지면 값이 0이 되도록 표현합니다.

![image](https://github.com/user-attachments/assets/55ccbb24-c2fc-4741-92f1-7f22340ec691)



### Reference
- [ZoeDepth: Zero-shot Transfer by Combining Relative and Metric Depth](https://arxiv.org/abs/2302.12288)
- [Towards Robust Monocular Depth Estimation: Mixing Datasets for Zero-shot Cross-dataset Transfer, MiDaS model](https://arxiv.org/abs/1907.01341)
