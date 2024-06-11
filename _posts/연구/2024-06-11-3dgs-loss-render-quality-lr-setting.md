---
title: "[3D CV 연구] 3DGS Loss not going down and renders look bad, scene에 따른 learning rate 조절"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - loss
  - render quality
  - learning rate
  - spatial_lr_scale
  - real extent
  - move Gaussians
excerpt: "3DGS Loss not going down and renders look bad, scene에 따른 learning rate 조절"
use_math: true
classes: wide
---

loss가 잘 떨어지지 않고, rendering quality가 나쁘다면,

scaling, initial, and final position learning rate를 줄여보세요. (i.e. 각 하이퍼 파라미터를 3으로 나눠서 시작해보기)

https://github.com/graphdeco-inria/gaussian-splatting/issues/17

------

### 카메라 분포와 학습률 조정

카메라의 분포를 고려하여 학습률을 조정하는 접근법(spatial_lr_scale)에 대해 알아봅니다.

***기본적으로 한 단위가 얼마나 되는지에 대한 절대적 스케일링 정보가 없기 때문에, 카메라 간의 거리에 따라 Gaussians의 이동 속도가 달라집니다.***

**카메라 간 거리와 Gaussians의 이동 속도**:
- 카메라가 1,000 units 떨어져 있는 경우, Gaussians는 1 unit 떨어져 있을 때보다 더 빨리 움직입니다.
- 카메라가 kilometers 단위로 분포되거나 긴 직선 경로에 위치해 있다면, Gaussians가 너무 빨리 움직여 문제가 발생할 수 있습니다.

**데이터 세트의 extent를 사용하는 방법**:
- 데이터 세트의 extent를 알고 있다면, 이 정보를 사용해 학습률을 설정하는 것은 간단합니다.
- **공식을 사용하여 학습률을 조정할 수 있습니다: constant * spatial_lr_scale / real_extent.**

**고정된 크기의 좌표를 사용하는 방법**:
- **포인트 데이터 세트의 좌표가 항상 알려진 크기 (1 unit = 1m)로 되어 있다면, spatial_lr_scale을 constant로 대체**하는 것도 해결책이 될 수 있습니다.

**결론**
- 카메라의 분포와 데이터 세트의 extent를 고려하여 학습률을 적절히 조정하는 것이 중요합니다. 이를 통해 Gaussians의 이동 속도를 적절히 제어하고, 학습의 효율성을 높일 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c5a57c44-9062-4e4c-873a-ae820f47faf7)

