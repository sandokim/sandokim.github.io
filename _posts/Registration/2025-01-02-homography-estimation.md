---
title: "[Registration] Homograpy Estimation & Homographic Adaptation"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - superpoints
  - keypoint
  - descriptors
  - interest point
  - homographic adatation
  - homography estimation
excerpt: "Homography Estimation과 Homographic Adaptation은 관련은 있지만 다른 개념입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://hydragon-cv.info/entry/SuperPoint-Self-Supervised-Interest-Point-Detection-and-Description)

> [Deep Homography Estimation에 관한 모든 것](https://ffighting.net/deep-learning-paper-review/deep-homography-estimation/all-about-deep-homography-estimation/)

> [Deep Image Homography Estimation](https://arxiv.org/pdf/1606.03798)

> [[OpenCV][C++] Homography 호모그래피 findHomography getPerspectiveTransform Projective Perspective 차이 warp](https://blog.naver.com/dorergiverny/223362309242)

### Homography 란?

호모그래피 (homography)는 서로 다른 카메라 뷰의 영상에 있는 두 평면 사이의 변환 또는 3차원 공간의 평면과 영상에 투영된 평면 사이의 변환행렬입니다. 

Homography는 다시 말하면 **두 개의 2D 평면 사이의 관계를 나타내는 행렬**입니다. 

### Homographic Adaptation 이란?

Homography Estimation과 Homographic Adaptation은 관련은 있지만 다른 개념입니다:

### Homography Estimation:
- 두 이미지 간의 평면 변환 관계를 나타내는 행렬(homography matrix)을 찾는 과정입니다.
- 동일한 평면상의 장면을 다른 시점에서 촬영한 두 이미지 간의 매핑 함수를 구합니다.
- 주로 이미지 정합, 파노라마 생성, 카메라 보정 등에 사용됩니다.

### Homographic Adaptation:
- SuperPoint에서 제안된 기법으로, 관심점 검출의 반복성을 향상시키는 방법입니다.
- 입력 이미지에 여러 무작위 호모그래피를 적용하여 다양한 시점과 스케일에서 관심점을 검출합니다.
- 검출된 관심점들을 원본 이미지로 역변환하여 의사 ground truth를 생성합니다.
- 이를 통해 모델의 도메인 적응력을 높이고, 실제 환경에서의 성능을 개선합니다.

**Homographic Adaptation은 Homography Estimation의 개념을 활용하지만, 주로 관심점 검출기의 성능 향상과 자기 지도 학습에 초점을 맞춥니다.**
