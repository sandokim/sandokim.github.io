---
title: "[3D CV] Monocular Depth Estimation"
last_modified_at: 2024-03-27
categories:
  - 공부
tags:
  - Multiple View Geometry
  - Epipolar Geometry
  - 3D CV
  - depth
  - depth estimation
  - monocular depth estimation
  - scene representation
  - scene understanding
excerpt: "Monocular Depth Estimation"
use_math: true
classes: wide
comments: true
---

사람이 한쪽 눈을 잃어서 다른 한쪽 눈으로만 물체를 보면, 그 물체에 대한 깊이를 추정하는데 굉장히 어렵다고 합니다.

사람처럼 scene에 대한 이해가 있으면, 한쪽 눈만으로도 깊이를 어느정도 추정할 수는 있긴 하지만 어렵다고 합니다.

즉, 하나의 이미지만 사용하여 depth를 추정하는 monocular depth estimation을 수행할 때, scene에 대한 이해가 있는 모델이라도 view가 하나만 존재할 때는 depth를 추정하는 것이 어렵다는 의미입니다.
