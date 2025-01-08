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


### Cost Volume의 의미와 역할

On the other hand, **we could also consider enhancing the fine feature extraction ability of the model** in a complex surgical environment, and a possible solution is to concatenate special layers focusing on **extracting local fine features when constructing the cost volume,** although it will slow down the prediction speed of the model.

여기서 **cost volume은 3D 복원 또는 매칭 작업에서 각 픽셀이나 포인트의 특정 가설(disparity hypothesis 또는 depth hypothesis) 간의 비용을 표현한 3차원 데이터 구조를 의미**합니다. 

이 문맥에서는 수술 환경에서 복잡한 물체(예: 얇은 수술용 실 등)를 정확히 재구성하기 위해 cost volume의 역할이 강조됩니다.

- **Cost volume은 픽셀 또는 포인트 간의 상관관계(similarity)를 계산하여 저장**합니다.
- 각 위치에서 **다양한 깊이 가설(disparity hypothesis)에 대한 비용**을 나타내며, **이 비용이 낮을수록 매칭 가능성이 높습니다**.
- 수술용 실(surgical suture)과 같은 작고 섬세한 구조물을 배경에서 분리하기 위해, cost volume이 중요한 역할을 합니다.
- 하지만 기존의 모델은 이러한 세밀한(local fine) 특징을 충분히 추출하지 못하기 때문에, 복잡한 수술 환경에서 일부 물체를 정확히 재구성하지 못하는 한계가 드러났습니다.
  - 해결책으로서 cost volume 강화:
    - 이 문맥에서 제안된 해결책은 local fine feature를 추출할 수 있는 layers를 추가하여 cost volume을 강화하는 것입니다.
    - 즉, 복잡한 환경에서도 작은 구조물을 구별할 수 있도록 cost volume에 정교한 특징 추출 능력을 부여하자는 접근입니다.


