---
title: "[Registration] Depth hypothesis, Disparity hypothesis, Cost Volume"
last_modified_at: 2025-01-05
categories:
  - Registration
tags:
  - Registration
  - 정합
  - depth map inference
  - cost volume
  - depth hypotheses
  - depth hypothesis
  - disparity channels
excerpt: "Cost volume의 channel 수는 disparity hypothesis의 수와 관련이 있습니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [FRSR: Framework for real-time scene reconstruction in robot-assistedminimally invasive surgery](https://www.sciencedirect.com/science/article/pii/S0010482523005863)

_We did not downsample the disparity channels after forming the cost volume to keep the larger disparity hypothesis range._


### Cost Volume:

Stereo matching이나 optical flow와 같은 3D 비전 작업에서 두 이미지 간의 비용(cost)을 계산하여 각 픽셀의 매칭 가능성을 표현하는 3차원 데이터 구조입니다.

일반적으로, **cost volume의 channel 수는 각 disparity 후보에 대한 비용을 저장합니다.** 

따라서 cost volume의 channel은 disparity range와 관련이 있습니다.

### Disparity Hypothesis:

**Disparity hypothesis는 각 픽셀에서 가질 수 있는 disparity의 후보 값을 의미합니다.**

예를 들어, disparity range가 [0, 64]라면 각 픽셀은 65개의 disparity hypothesis를 가질 수 있습니다.

#### Cost Volume에서의 Channel 수:

- 일반적으로 disparity hypothesis의 수와 동일합니다.
- 하지만 channel은 hypothesis 자체를 의미하지 않고, 각 hypothesis에 대한 cost 값을 저장합니다.
- Disparity Hypothesis는 단순히 disparity 값의 후보(range)를 가리키는 개념이며, cost volume의 channel 수와 혼동해서는 안 됩니다.
