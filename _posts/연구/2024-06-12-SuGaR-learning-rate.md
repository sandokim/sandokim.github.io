---
title: "[3D CV 연구] 3DGS SuGaR position_lr_init & spatial_lr_scale"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - learning rate
  - position_lr_init
  - spatial_lr_scale
excerpt: "3DGS SuGaR position_lr_init & spatial_lr_scale"
use_math: true
classes: wide
---

### Scene Scaling and Learning Rate in Gaussian Splatting

***Gaussian Splatting 방법에서는 장면의 스케일에 맞춰 학습률을 조정하는 것이 중요합니다.***

이 방법의 3dgs original paper 저자들이 강조한 바와 같이, 카메라 위치의 반지름(즉, 장면의 스케일)에 비례하여 position learning rate를 조정하면, 방법이 장면의 일반적인 스케일에 대해 불변하게 됩니다. 

#### 1. Position Learning Rate의 역할
정의: position learning rate는 학습 과정에서 Gaussian들이 장면 내에서 얼마나 움직일 수 있는지를 결정합니다.
- large position learning rate: Gaussian들이 큰 움직임을 하기 어렵게 만듭니다.
- small position learning rate: Gaussian들이 작은 움직임을 하기 어렵게 만듭니다.
  
#### 2. 장면 스케일링 적용 예시
두 개의 동일한 데이터셋: 동일한 SfM 포인트 클라우드와 동일한 카메라 위치를 가진 두 개의 데이터셋이 있다고 가정합니다.

스케일 조정: 두 장면 중 하나의 스케일을 2배로 증가시킵니다. 즉, 카메라 위치와 SfM 포인트 클라우드의 좌표에 2를 곱합니다.

#### 3. 학습률 조정의 필요성
**스케일 증가에 따른 학습률 조정:**
- 스케일이 커진 장면의 학습률을 2배로 증가시키면 두 장면은 동일하게 최적화됩니다.
- 학습률을 조정하지 않으면, 스케일이 커진 장면에서 학습률이 너무 작아져 두 장면이 다르게 동작하게 됩니다.

**방법의 robustness**:
- 학습률을 장면의 스케일에 맞춰 조정하면, 방법이 SfM 스케일에 상관없이 견고해집니다.
- 예를 들어, SfM 방법이 -1에서 1 사이의 좌표를 출력하든 -10에서 10 사이의 좌표를 출력하든 동일한 결과를 얻을 수 있습니다.

#### 5. position_lr_init 값의 중요성

position_lr_init default 값: 기본 position_lr_init 값은 일반적인 크기의 장면에서 좋은 재구성을 위해 최적화되어 있습니다. general scene(COLMAP으로 얻은 scene)에서 객체 크기가 전체 장면의 1/100에서 1/10 사이인 경우, default 값이 적절합니다.

하지만 큰 장면에서의 문제가 있습니다. 

도시 구역 같은 ***큰 장면에서는 객체 크기가 전체 장면의 1/1000인 경우가 많습니다.*** 

이때는 기본 position_lr_init 값으로는 정확한 scene reconstruction이 불가능합니다.

***이 경우 position_lr_init 값을 낮추어야 재구성 품질이 크게 향상됩니다.***

------

### 작은 학습률의 문제
#### 학습률 (Learning Rate)
- 정의: 학습률은 Gaussian들이 학습 과정에서 장면 내에서 얼마나 움직일 수 있는지를 결정하는 값입니다.
- 작은 학습률: Gaussian들이 작은 움직임만 하게 하여 더 세밀한 조정을 가능하게 하지만, 더 많은 Gaussian이 필요하게 합니다.
- 
#### 작은 학습률의 문제
**작은 움직임 강제**: ***작은 학습률을 사용하면 Gaussian들이 작은 움직임을 하게 되어, 더 많은 Gaussian이 필요합니다.*** 이는 객체의 디테일을 살릴 수 있지만, 일반적인 장면에서는 불필요하게 많은 Gaussian이 필요하게 되어 메모리 효율이 떨어집니다.
**큰 장면에서의 장점**: ***큰 장면에서는 작은 학습률이 장면의 크기에 비해 작은 객체의 디테일을 살리는 데 도움이 됩니다.**

#### 요약:
- 작은 학습률은 작은 객체의 디테일을 살리는 데 유리하지만, 일반적인 장면에서는 메모리 효율이 떨어지는 문제가 있습니다.
- 큰 장면에서는 작은 학습률이 유리하여 더 정확한 디테일을 재구성할 수 있습니다.

***이와 같이, 장면의 스케일에 따라 학습률을 조정하는 것은 Gaussian Splatting 방법의 성능과 효율성을 높이는 중요한 전략입니다.***

https://github.com/Anttwo/SuGaR/issues/30

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/bffb1855-a959-4bca-be65-e88c29a1f533)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9a1165aa-9182-4103-8615-4a940b27ab6b)


