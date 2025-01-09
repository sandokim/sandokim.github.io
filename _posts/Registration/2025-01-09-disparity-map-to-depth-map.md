---
title: "[Registration] How to recover depth maps from predicted dispairty"
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
excerpt: "Left image만 input으로 하여, left, right disparity를 예측하고, 예측된 두 disparity로부터 camera focal length와 두 카메라 간의 abseline distanc로 계산하여 depth map을 복원할 수 있습니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [M3Depth: Self-supervised Depth Estimation in Laparoscopic Image Using 3D Geometric Consistency](https://arxiv.org/pdf/2208.08407)

#### Left image만 input으로 하여, left, right disparity를 예측하고, 예측된 두 disparity로부터 camera focal length와 두 카메라 간의 abseline distanc로 계산하여 depth map을 복원할 수 있습니다.

Similar to Monodepth1, in M3Depth, the left image $I^l$ of a stereo image pair $(I^l, I^r \in \mathcal{R}\_+^{h \times w \times 3})$ was always the input and the framework generated two distinct left and right disparity maps $d^l, d^r \in \mathcal{R}\_+^{h \times w}$ simulataneously, _i.e_ $\mathcal{Z}: I^l \rightarrow (d^l, d^r)$.

Given the camera focal length $f$ and the baseline distance $b$ between the cameras, left and right depth maps $D^l, D^r \in \mathcal{R}_+^{h \times w}$ could then be trivially recovered from the predicted disparity, $(D^l, D^r) = bf(d^l, d^r)$.

$h$ and $w$ denot image height and widht.

