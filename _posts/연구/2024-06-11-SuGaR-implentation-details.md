---
title: "[3D CV 연구] 3DGS SuGaR nearsest gaussians implementation details"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - nearest gaussians
  - implementation details
excerpt: "3DGS SuGaR equations"
use_math: true
classes: wide
---

For the regularization from 9000-15000 steps, eq(8)에 따라 gaussian distribution에서 different sample $p$를 샘플링합니다.

저자에 따르면 point $p$의 neighbors를 사용하지 않고, Gaussian $g$의 neighbors에서 point $p$를 샘플링합니다.

다른말로, every 500 iters마다 모든 Gaussians의 neighbor Gaussians를 업데이트 합니다.

그래서, 저자는 Gaussian $g$를 사용하여 샘플링한 point $p$의 density가 대부분 Gaussian $g$에 가까운 Gaussian들에 의존합니다. (물론 Gaussian $g$ 자체도 포함합니다.)

https://github.com/Anttwo/SuGaR/issues/5

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0f212637-b622-4fc2-845a-0ecfa3061bd2)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/71a84b6a-a0f6-4f8b-9bd4-b27b59d4234a)


