---
title: "[3D CV 연구] 3DGS SuGaR sdf estimation"
last_modified_at: 2024-06-12
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - sdf estimation
excerpt: "3DGS SuGaR sdf estimation"
use_math: true
classes: wide
comments: true
---

### `sdf_values는 3D points의 calculated sdf이고, `sdf_estimation`은 projection depth와 depth-map depth의 difference를 나타내는데, 왜 `sdf_estimation`이 `abs()`를 필요로 하나요?

실제로, 우리는 Gaussian들이 ***"ideal"*** SDF function의 zero-level set에 align되도록 하고 싶은 것입니다.

하지만 practice에서 ideal SDF의 sign(-, +, 0)를 계산하는 것은 불가능합니다. 왜냐면, optimization 동안 어떤 point가 object의 안에 있는지 밖에 있는지를 아는 것은 굉장히 어렵기 때문입니다.

하지만 우리는 sign(-, +, 0)을 고려하지 않습니다.

왜냐면 우리는 그저 (a) the SDF estimator의 zero-level sets과 (b) the ideal SDF의 zero-level sets 모두에 대해 align하고 싶기 때문입니다.

다른말로, 우리는 `sdf_values`와 `sdf_estimation`이 같은 zero points를 가지도록 하고 싶습니다.

그러므로, 우리는 그냥 absolute value에 대해 optimize합니다. 이는 computation을 쉽게 만들어주고, zero-level sets의 convergence를 다르게 바꿔놓지 않습니다.

https://github.com/Anttwo/SuGaR/issues/68

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/cc291775-e800-4710-aac7-484cb0bef7c8)









