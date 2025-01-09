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


### Image Reconstruction Loss in 2D (Warping the counter-part RGB image with the disparity map)

**구한 predicated disparity로 right image를 left image로 warping하여 RGB image loss를 구현할 수 있습니다.**

_With the predicted disparity maps and the original stereo image pair, **left and right images could then be reconstructed by warping the counter-part RGB image with the disparity map** mimicking optical flow [3,14]._

위 문장은 **예측된 disparity map(시차 맵)과 원래의 스테레오 이미지 쌍(좌우 이미지)을 사용하여, 각 좌우 이미지를 재구성할 수 있다**는 내용입니다.

이 과정은 disparity map을 optical flow처럼 사용하여 반대쪽 RGB 이미지를 왜곡(warping)시켜 이루어진다고 설명하고 있습니다. 

하나씩 풀어서 설명하겠습니다:

1. Disparity Map (시차 맵)
- 스테레오 이미지(좌우 카메라 이미지)를 통해 얻은 깊이 정보를 담고 있는 맵입니다.
- Disparity map은 픽셀이 좌우 이미지 간 얼마나 이동했는지 나타내며, 이를 통해 3D 공간에서의 깊이 정보를 계산할 수 있습니다.

2. 스테레오 이미지 쌍 (Stereo Image Pair)
- 두 대의 카메라(왼쪽 카메라와 오른쪽 카메라)로 촬영된 이미지 쌍을 뜻합니다.
- 이 두 이미지는 약간의 시차(parallax)를 가지며, 이를 분석하면 깊이 정보를 추출할 수 있습니다.
3. Warping(왜곡)
- 하나의 이미지를 기준으로 다른 이미지를 disparity map을 사용해 "왜곡"시킵니다.
- 예를 들어, 왼쪽 이미지를 기준으로 오른쪽 이미지를 재구성하려면, 오른쪽 이미지를 disparity map에 따라 좌표를 이동시켜야 합니다. 이 과정을 warping이라고 합니다.
- Optical flow와 비슷한 접근 방식으로, 픽셀 단위로 이미지가 어떻게 움직이는지를 표현합니다.

_With the predicted disparity maps and the original stereo image pair, **left and right images could then be reconstructed by warping the counter-part RGB image with the disparity map** mimicking optical flow [3,14]._
위 문장에서 말하는 작업
- 목표: 왼쪽 이미지를 오른쪽 이미지로, 또는 오른쪽 이미지를 왼쪽 이미지로 재구성(reconstruction)합니다.
- 방법:
  - 예측된 disparity map을 사용하여 각 픽셀의 이동 방향과 거리를 계산.
  - 이동된 픽셀 정보를 사용해 반대편 이미지를 "왜곡(warping)"시킴.
  - 결과적으로 반대편 이미지를 기준으로 원래의 이미지를 재구성.
  - 쉽게 말하면
    - Disparity map은 깊이 정보를 나타내는 데이터입니다. 이를 활용해 스테레오 이미지 쌍 중 하나의 이미지를 기준으로 다른 이미지를 예측하는 과정을 설명한 것입니다. 이 과정에서 disparity map은 optical flow처럼 작동해 각 픽셀의 움직임을 묘사합니다.

### Learning 3D Geometric Consistency

**The disparity maps of the left and right images were first converted to depth maps and then backprojected to 3D coordinates to obtain left and right surgical scene point clouds $(P^l,P^r \in \mathcal{R}^{hw \times 3})$ by multiplying the depth maps with the intrinsic parameter matrix $(K)$.**

The 3D consistency loss employed Iterative Closest Point (ICP) [17,21], a classic rigid registration method that derives a transformation matrix between two point clouds by iteratively minimizing point-to-point distances between correspondences.

**From an initial alignment, ICP alternately computed corresponding points between two input point clouds using a closest point heuristic and then recomputed a more accurate transformation based on the given correspondences.**

The final residual registration error after ICP minimization was output as one of the returned values. 

More specifically, to explicitly explore global 3D loss, the ICP loss at the original input image scale was calculated with only 1000 randomly selected points to reduce the computational workload.
