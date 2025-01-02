---
title: "[Registration] Homographic Adaptation"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - superpoints
  - keypoint
  - descriptors
  - homographic adatation
excerpt: "Homographic adaptation은 다양한 호모그래피 변환을 적용한 이미지들에서 관심점을 검출하고 이를 원본 이미지로 역변환하여 pseudo ground truth를 생성하는 자기 지도 학습 기법입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://hydragon-cv.info/entry/SuperPoint-Self-Supervised-Interest-Point-Detection-and-Description)

> [TILDE: A Temporally Invariant Learned DEtector](https://openaccess.thecvf.com/content_cvpr_2015/papers/Verdie_TILDE_A_Temporally_2015_CVPR_paper.pdf)

The TILDE interest point detection system used a principle similar to **Homographic Adaptation**; however, their approach does not benefit from the power of large fully-convolutional neural networks.

### Homographic Adaptation 이란?

Homographic Adaptation은 SuperPoint에서 제안된 기법으로, 관심점 검출의 반복성을 높이고 도메인 간 적응(예: 합성에서 실제 이미지로)을 수행하기 위한 다중 스케일, 다중 호모그래피 접근 방식입니다. 

주요 특징은 다음과 같습니다:
- 다중 변환: 입력 이미지에 여러 무작위 호모그래피를 적용하여 다양한 시점과 스케일에서 장면을 볼 수 있게 합니다.
- 자기 지도 학습: 레이블이 없는 대상 도메인의 이미지에 자동으로 레이블을 생성합니다.
- 성능 향상: 초기 MagicPoint 검출기의 성능을 향상시켜 더 풍부한 관심점 집합을 반복적으로 검출할 수 있게 합니다.
- Pseudo ground truth 생성: 이 과정을 통해 실제 이미지에 대한 pseudo ground truth 관심점을 생성합니다.
- 도메인 적응: 합성 데이터에서 실제 이미지로의 도메인 적응을 가능하게 합니다.
 
**Homographic Adaptation은 SuperPoint 모델의 핵심 구성 요소**로, 다양한 환경에서의 성능을 향상시키고 실제 세계 응용에 더 적합한 관심점 검출기를 만드는 데 중요한 역할을 합니다.
