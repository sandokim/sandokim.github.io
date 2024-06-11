---
title: "[3D CV 연구] 3DGS spatial_lr_scale, scaling_lr, position_lr"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - spatial_lr_scale
  - scaling_lr
  - position_lr
  - scene extent
excerpt: "3DGS spatial_lr_scale"
use_math: true
classes: wide
---

**spatial_lr_scale**: position 및 scaling learning rate에 대한 곱셈자로 작용합니다. 

학습 과정에서 spatial_lr_scale를 통해 position과 scaling의 학습률을 동적으로 조절할 수 있습니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/164

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ff227c82-76de-43c4-aa45-89c3ccf0dcc0)

--------

### sptatial_lr_scale, scaling_lr, position_lr_... (position_lr_init, position_lr_final, position_lr_delay_mult, position_lr_max_steps)

self.sptial_lr_scale = 5 로 hard coded되어 있습니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/38

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/01b90490-67ab-4b52-943e-c4ab91c97910)

저자는 COLMAP이 항상 비슷한 scene extent를 사용하기 때문에 대부분의 모델에 대한 lr_scale은 5에 매우 가깝게 했습니다.

그렇다면 COLMAP으로 얻은 데이터가 아닐 경우 hard coded된 value는 어떻게 결정하나요? COLMAP에서 한것처럼 '물리적'으로 어떠한 scene에 대해서든 scale을 해야하나요?

저자는 scaling_lr을 0.005로 value를 fix하였고, position_lr_... (position_lr_init, position_lr_final, position_lr_delay_mult, position_lr_max_steps)를 scene의 extent에 맞게 scaled하였습니다.

이렇게 했다는게 왜 scene scale에 상관없이 adaptive behavior를 보장할까요?

이는 position의 case에서 직관적입니다. Gaussian들은 scene이 larger하다면 더 멀리 이동해야만 합니다. (사실 이 이유는 Adam을 사용하고 있기 때문이긴 합니다.)

Scales에 대해서 우리는 log로 인코딩했습니다. 그 말은 우리가 그걸 사용할 때, exponential로 activate해야한다는 뜻입니다. 

In a larger scene에서는 Gaussian들이 start에서 larger해야하고, scale에 consistent해야합니다. (이는 nearest neighbors까지의 distance에 기반합니다.)

이것은 이미 constant learning rate을 사용하여 크기 변화율이 실제 scene size에 비례하도록 보장합니다.

이는 수식적으로 아래와 같이 증명할 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c04afaa9-3db4-479a-87da-c309610bd1e5)

Q) 지금까지 scales과 positions은 scale하기 쉽다는 것을 이해했습니다. 하지만 **문제는 rotations**에서 발생하지 않나요?

A) 현재 우리의 이해에 따르면, 두 가지 수준의 **spatial discrepancies**가 발생합니다:

Gradients Size Variation due to Projection: 동일한 장면(same scene)을 확대하면 world space에서 gradients가 더 작아집니다.

Adaptation of Learning Rates: Enlarged scenes(확대된 동일한 장면)에 맞게 learning rates를 조정해야 합니다. 더 큰 장면에서는 실제로 더 많이 이동해야 합니다.

이 문제를 해결하기 위해 다음과 같은 방안을 고려할 수 있습니다:

Adam Optimizer 사용: Adam optimizer는 learning rates를 동적으로 조정하여 일정한 scaling factor에 대해 불변성을 유지합니다.

업데이트의 적응: 더 큰 장면에 맞게 업데이트를 조정해야 합니다. 예를 들어, 10배 큰 동일한 장면에서는 동일한 업데이트가 이제 Gaussians를 10배 더 멀리 이동시켜야 합니다. 

그러나 **rotations은 증가하지 않아야 하며, 현재로서는 Adam이 이 부분에서 중요한 역할을 할 것**이라고 생각합니다.









