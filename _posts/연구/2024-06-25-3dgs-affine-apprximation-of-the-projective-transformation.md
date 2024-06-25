---
title: "[3D CV 연구] 3DGS affine approximation of the projective transformation"
last_modified_at: 2024-06-25
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - affine transform
excerpt: "3DGG affine apprximiation of the projective transformation"
use_math: true
classes: wide
---

### 3dgs의 original paper 혹은 3dgs의 관련 페이퍼를 찾아보면 affine apprxomiation 이라는 개념이 나옵니다.

- [3D Gaussian Splatting for Real-Time Radiance Field Rendering](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1077270-fb9a-4ef4-bfaa-121256bb2ed2)

- [High-quality Surface Reconstruction using Gaussian Surfels](https://arxiv.org/abs/2404.17774)
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fb723212-839d-4163-a85c-5a335f88742f)

***"affine approximation of the projective transformation": projective transformation 3x3 행렬에서 z축에 따른 perspective distortion이 그냥 없다고 가정하여 affine transformation 3x3 행렬에서 3행을 [0, 0, 1]로 대체하는 것을 의미합니다.***

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3d75cddc-b269-491e-a668-35bcecb036f5)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ae617767-ebd4-4a07-9016-9a9d044b0211)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/17db1224-f9bb-4fa6-8994-665f94d486ce)



