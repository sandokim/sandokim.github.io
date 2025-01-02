---
title: "[Registration] SuperPoint & MagicPoint"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - superpoints
  - keypoint
  - descriptors
  - interest points
  - homographic adatation
  - homography estimation
  - MagicPoint
excerpt: "MagicPoint는 interest points 검출에 초점을 맞춘 모델입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://hydragon-cv.info/entry/SuperPoint-Self-Supervised-Interest-Point-Detection-and-Description)

### MagicPoint

The MagicPoint architecture is the SuperPoint architecture without the descriptor head.

MagicPoint는 SuperPoint 아키텍처에서 descriptor head를 제외한 버전으로, 주로 관심점(interest point) 검출에 초점을 맞춘 모델입니다. 

MagicPoint의 주요 특징은 다음과 같습니다:
- 합성 데이터 학습: 단순한 기하학적 도형들로 구성된 Synthetic Shapes 데이터셋을 사용하여 학습됩니다.
- VGG 스타일 인코더: 이미지의 차원을 축소하기 위해 VGG 스타일의 인코더를 사용합니다.
- 전통적 방식 대비 우수한 성능: FAST, Harris, Shi-Tomasi와 같은 전통적인 keypoint 검출기보다 우수한 성능을 보입니다.
- 노이즈 강인성: 특히 노이즈가 있는 상황에서 좋은 성능을 보입니다.
- SuperPoint의 기초: MagicPoint는 실제 이미지에 대한 fine-tuning과 augmentation supervision을 거쳐 SuperPoint로 발전합니다.

MagicPoint는 SuperPoint 개발의 중요한 첫 단계로, 합성 데이터를 통해 학습된 강력한 관심점 검출기의 역할을 합니다.

![image](https://github.com/user-attachments/assets/1a5434b1-0600-4a7b-a669-0a6a597a6a3a)

### MagicPoint vs SuperPoint

SuperPoint는 조명 변화에 가장 반복적으로 대응하며, 시점 변화에서도 경쟁력을 보이고, 모든 시나리오에서 MagicPoint를 능가합니다.

![image](https://github.com/user-attachments/assets/06530fab-79e6-4577-9f28-ccb595142f98)



