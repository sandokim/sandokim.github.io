---
title: "[Registration] How to recover depth maps from predicted dispairty & RGB image warping loss"
last_modified_at: 2025-01-09
categories:
  - Registration
tags:
  - Registration
  - 정합
  - surgical scene reconstruction
  - inverse depth map
  - predicted disparity
  - depth map
  - Monodepth1
  - M3Depth
  - image warping
  - Iterative Closest Point (ICP) for 3D Consistency loss
excerpt: "Left image만 input으로 하여, left, right disparity를 예측하고, 예측된 두 disparity로부터 camera focal length와 두 카메라 간의 baseline distance로 계산하여 depth map을 복원할 수 있습니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [M3Depth: Self-supervised Depth Estimation in Laparoscopic Image Using 3D Geometric Consistency](https://arxiv.org/pdf/2208.08407)

#### Depth estimation model(Monodepth1)는 Left image만 input으로 하여, left, right disparity를 예측하고, 

#### 예측된 두 disparity로부터 camera focal length와 두 카메라 간의 baseline distance로 계산하여 depth map을 복원할 수 있습니다.

Similar to Monodepth1, in M3Depth, the left image $I^l$ of a stereo image pair $(I^l, I^r \in \mathcal{R}\_+^{h \times w \times 3})$ was always the input and the framework generated two distinct left and right disparity maps $d^l, d^r \in \mathcal{R}\_+^{h \times w}$ simultaneously, _i.e_ $\mathcal{Z}: I^l \rightarrow (d^l, d^r)$.

Given the camera focal length $f$ and the baseline distance $b$ between the cameras, left and right depth maps $D^l, D^r \in \mathcal{R}_+^{h \times w}$ could then be trivially recovered from the predicted disparity, $(D^l, D^r) = bf(d^l, d^r)$.

$h$ and $w$ denot image height and width.


### Image Reconstruction Loss in 2D

With the predicted disparity maps and the original stereo image pair, **left and right images could then be reconstructed by warping the counter-part RGB image with the disparity map** mimicking optical flow [3,14].


### Learning 3D Geometric Consistency

**The disparity maps of the left and right images were first converted to depth maps and then backprojected to 3D coordinates to obtain left and right surgical scene point clouds $(P^l,P^r \in \mathcal{R}^{hw \times 3})$ by multiplying the depth maps with the intrinsic parameter matrix $(K)$.**

The 3D consistency loss employed Iterative Closest Point (ICP) [17,21], a classic rigid registration method that derives a transformation matrix between two point clouds by iteratively minimizing point-to-point distances between correspondences.

**From an initial alignment, ICP alternately computed corresponding points between two input point clouds using a closest point heuristic and then recomputed a more accurate transformation based on the given correspondences.**

The final residual registration error after ICP minimization was output as one of the returned values. 

More specifically, to explicitly explore global 3D loss, the ICP loss at the original input image scale was calculated with only 1000 randomly selected points to reduce the computational workload.
