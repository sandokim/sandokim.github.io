---
title: "[3D CV 연구] NeRF Spherical Harmonics Coarse-to-Fine training"
last_modified_at: 2024-07-08
categories:
  - 연구
tags:
  - 연구
  - HDR plenoxel
  - spherical harmonics
  - hash grid
  - multi resolution
excerpt: "HDR Plenoxel에서는 coarse-to-fine으로 spherical harmonics를 학습시킵니다."
use_math: true
classes: wide
comments: true
---

# HDR Plenoxel에서는 coarse-to-fine으로 학습을 진행합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/05b9c904-8539-4eb9-a955-52085a9caf4a)

# Spherical Harmonics Regularization

저품질 입력 조건에서, 주변 뷰로부터 얻은 LDR 이미지들이 크게 동적인 노출 차이를 가지는 경우, 위의 초기화는 초기 최적화 단계를 안정화시킵니다. 만약 화이트 밸런스의 최적화 속도가 SH 계수의 최적화 속도와 맞지 않으면, 최적화의 후반 단계에서 모호성이 다시 발생할 수 있습니다. 이를 정규화하기 위해, 우리는 SH coefficient masking을 도입합니다. 이는 먼저 확산 반사 속성(diffuse reflectance property, view direction invariant radiance)을 학습하고, 그 후 뷰 방향에 민감한 특성(view direction sensitive ones)을 학습하도록 일정을 조정할 수 있게 합니다. 즉, 저주파수 차수 SH에서 고주파수 차수 SH로 학습을 진행합니다.

## 방법

- 우리는 2차 및 3차의 SH 계수에 SH masking을 적용합니다.
- 초기 5 에포크 동안 SH masking 비율을 1/5씩 감소시켜 점진적으로 학습합니다.
  - Epoch 1: SH masking 비율 1
  - Epoch 2: SH masking 비율 0.8
  - Epoch 3: SH masking 비율 0.6
  - Epoch 4: SH masking 비율 0.4
  - Epoch 5: SH masking 비율 0.2
- 초기 5 에포크 이후에는 모든 차수의 SH를 완전한 비율로 업데이트합니다, 즉, SH masking을 적용하지 않습니다.

## 효과

이러한 일정 조정은 어려운 조건의 입력 케이스에 대한 최적화를 눈에 띄게 안정화합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ad7c48a1-e33c-4be9-9b72-05e37d36f650)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0d8f5c24-f54e-4680-9238-3da5d1ae5a2a)




### Reference
[HDR-Plenoxels: Self-Calibrating High Dynamic Range Radiance Fields](https://hdr-plenoxels.github.io/)


