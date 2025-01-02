---
title: "[Registration] keypoint, descriptors"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - superpoints
  - keypoint
  - descriptors
excerpt: "Descriptors는 특징점(keypoint)의 고유한 특성을 수치화한 벡터입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://hydragon-cv.info/entry/SuperPoint-Self-Supervised-Interest-Point-Detection-and-Description)

> [CVPR 2020 - Deep Visual SLAM Frontends - SuperPoint, SuperGlue and SuperMaps (Tomasz Malisiewicz 발표)](https://www.cv-learn.com/20201227-cvpr2020-slam-malisiewicz/)

Descriptors는 특징점(keypoint)의 고유한 특성을 수치화한 벡터입니다. SuperPoint와 geometric correspondences 맥락에서 descriptors의 역할과 특징은 다음과 같습니다:

1. 특징점 식별: Descriptors는 각 keypoint의 주변 영역 정보를 압축하여 표현합니다. 이를 통해 서로 다른 이미지에서 동일한 특징점을 식별할 수 있게 됩니다.
2. 매칭 가능성: 같은 keypoint를 다른 각도에서 보더라도 유사한 descriptor가 생성되어야 하며, 다른 keypoint와는 구별되는 descriptor가 생성되어야 합니다.
3. CNN 기반 추출: SuperPoint는 CNN 네트워크를 사용하여 이미지에서 2D keypoint의 위치와 해당 feature의 descriptor 정보를 동시에 추출합니다.
4. 효율성: SuperPoint에서는 keypoint location과 descriptor 계산이 약 90%의 가중치 정보를 공유하여 GPU에서 실시간으로 처리할 수 있도록 설계되었습니다.
5. Geometric awareness: Descriptor는 특징점의 기하학적 관계를 포착하는 데 중요한 역할을 합니다. 그러나 최근 연구에 따르면 기존의 foundation model 특징들이 "geometry-aware" 의미론적 대응에서 성능이 떨어지는 것으로 나타났습니다.

Descriptors는 컴퓨터 비전 작업에서 이미지 매칭, 객체 인식, SLAM 등 다양한 응용 분야에서 중요한 역할을 합니다.
