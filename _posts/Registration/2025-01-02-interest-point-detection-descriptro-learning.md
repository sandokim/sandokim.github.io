---
title: "[Registration] Interest point detection, descriptor learning"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - superpoints
  - interest point detection
  - descriptor learning
excerpt: "Interest point detection은 이미지에서 특징이 되는 부분, 즉 keypoint를 찾는 과정입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://hydragon-cv.info/entry/SuperPoint-Self-Supervised-Interest-Point-Detection-and-Description)

> [CVPR 2020 - Deep Visual SLAM Frontends - SuperPoint, SuperGlue and SuperMaps (Tomasz Malisiewicz 발표)](https://www.cv-learn.com/20201227-cvpr2020-slam-malisiewicz/)

### Interest Point Detection
**Interest point detection은 이미지에서 특징이 되는 부분, 즉 keypoint를 찾는 과정입니다.**

- 목적: 이미지에서 매칭에 유용한 특징점을 자동으로 찾아내는 것
- 방법:
  - Synthetic 데이터셋을 이용한 MagicPoint 모델 pre-training
  - Homographic Adaptation을 통한 pseudo ground truth 생성
  - 실제 이미지에 대한 fine-tuning
- 특징:
  - CNN 기반으로 2D keypoint의 위치를 추출
  - 기하학적 변형에 강인한 interest point 검출

### Descriptor Learning
**Descriptor learning은 검출된 keypoint의 특성을 수치화된 벡터로 표현하는 과정입니다.**

- 목적: 서로 다른 이미지에서 같은 keypoint를 식별할 수 있게 하는 것
- 방법:
  - Contrastive loss를 이용한 metric learning
  - Siamese training 기법 활용
- 특징:
  - Keypoint location과 descriptor 계산이 ~90%의 가중치를 공유
  - 같은 keypoint는 다른 각도에서도 유사한 descriptor 생성
  - 다른 keypoint와는 구별되는 descriptor 생성

SuperPoint의 핵심은 이 두 과정(Interest point detection & descriptor learning)을 하나의 네트워크에서 동시에 수행한다는 점입니다. 이를 통해 GPU에서 실시간으로 처리가 가능하며, 기존의 hand-crafted 방식보다 우수한 성능을 보입니다.
