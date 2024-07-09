---
title: "[3D CV 연구] 3DGS gaussian opacity activation is sigmoid"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - opacity activation
excerpt: "3DGS opacity activation은 sigmoid를 사용합니다."
use_math: true
classes: wide
comments: true
---

training에서 각 gaussian의 opacity인 self._opacity가 negative value로 나올 수 있지만, 이는 get_opacity 함수를 통해서 sigmoid로 0~1 사이의 값으로 만들어서 사용합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/17d067c5-1078-4ac5-9580-423a164e4af0)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/92f25ff7-7459-4c7b-a73c-3f62ad57e468)

https://github.com/graphdeco-inria/gaussian-splatting/issues/555

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/be85c5c0-3cc3-4ef3-ad91-928131682a3d)


