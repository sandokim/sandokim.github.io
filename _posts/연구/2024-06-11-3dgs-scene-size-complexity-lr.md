---
title: "[3D CV 연구] 3DGS scene size & scene complexity and spatial learning rate"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - scene size
  - scene complexity
  - spatial learning rate
  - lr
excerpt: "3DGS scene size & scene complexity and spatial learning rate"
use_math: true
classes: wide
comments: true
---

### 3dgs 알고리즘과 장면 크기 및 복잡성
3dgs 알고리즘은 장면의 크기(scene size)와는 독립적이지만, 장면의 복잡성(scene complexity)과는 독립적이지 않습니다. 

동일한 장면(scene)과 카메라(cameras)를 공간적으로 확장해도 알고리즘은 동일하게 작동해야 합니다. 

그러나 몇 개의 객체(objects) 대신 전체 도시 구역(city district)을 처리하려고 하면, 공간 학습률(spatial learning rate)이 너무 높을 가능성이 있으며, spatial learning rate를 조정할 필요가 있을 것입니다.

### Spatial Learning Rate(공간 학습률)이란?
Spatial learning rate(공간 학습률)은 알고리즘이 공간적인 정보, 즉 장면의 물리적 배치나 구조를 학습하는 속도를 의미합니다. 

이는 알고리즘이 공간적 특징을 얼마나 빠르게 이해하고 최적화하는지를 나타내는 중요한 파라미터입니다.

### 왜 Spatial Learning Rate(공간 학습률)이 중요한가?
- Small Scenes(작은 장면): 작은 장면에서는 spatial learning rate이 높아도 문제가 되지 않을 수 있습니다. 알고리즘이 비교적 단순한 구조를 빠르게 학습하고 최적화할 수 있기 때문입니다.
- Large Scenes(큰 장면): 큰 장면, 특히 전체 도시 구역 같은 **복잡한 장면에서는 spatial learning rate이 너무 높으면 알고리즘이 학습을 제대로 하지 못할 수 있습니다.**

***따라서 장면의 크기(Scene Size)보다는 복잡성(Scene Complexity)이 알고리즘의 성능에 더 큰 영향을 미칩니다.***

### 복잡한 장면을 처리할 때는 spatial learning rate을 적절히 조정하여 알고리즘이 최적의 성능을 발휘할 수 있도록 해야 합니다.

- ***복잡한 scene에 대해서는 기본적으로 낮은 초기 spatial learning rate로 시작합니다.*** 예를 들어, 0.001 또는 그 이하의 값을 사용할 수 있습니다.
- **학습률 감소(Decay) 적용**: 학습이 진행됨에 따라 spatial learning rate을 점차 감소시키는 방법을 사용합니다. 예를 들어, 매 에폭(epoch)마다 learning rate을 0.9배로 줄이는 방식입니다.
- **Adaptive Learning Rate 사용**: AdaGrad, RMSprop, Adam 같은 adaptive learning rate 알고리즘을 사용하여 학습 과정 중에 자동으로 spatial learning rate을 조정합니다.
- **학습률 스케줄링**: 학습률을 단계별로 낮추는 스케줄링 기법을 사용할 수 있습니다. 예를 들어, 학습 오류가 감소하지 않으면 spatial learning rate을 줄이는 방식입니다.
- **Validation Set 활용**: 검증 데이터셋을 활용하여 최적의 spatial learning rate을 찾아냅니다. 여러 학습률을 테스트하고, 가장 좋은 성능을 보이는 값을 선택합니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/67

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/916bd265-e1fa-4a71-972c-f0c901e195dc)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6fa1238a-d643-4663-b5ff-0292c3925c7e)
