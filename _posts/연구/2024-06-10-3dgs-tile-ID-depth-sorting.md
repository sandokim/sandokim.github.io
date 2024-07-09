---
title: "[3D CV 연구] 3DGS Sorting gaussians with tile ID and depth keys"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - culling
  - sorting
  - tile ID
  - depth keys
excerpt: "3DGS tile ID와 depth keys 기반 sorting을 .cu 파일에서 합니다."
use_math: true
classes: wide
comments: true
---

tile ID와 depth keys로 각 gaussian을 묶어서, key에 따라 depth가 가까운 순으로 Gaussian들을 sorting 합니다.

tiles에서 gaussians은 depth에 가까운 순서로 정렬하고, color가 accumulation이 됩니다.

이때 불투명도인 opacity가 1을 넘어가면 tile에 대한 color accumulation 연산을 하는 thread를 중단합니다.

tile 별로 thread를 할당하여 병렬연산하므로 속도가 빠릅니다.

camera로부터 가까운 순서로 gaussian들을 overlap하다가 tile의 모든 pixel에 대해 opacity가 1에 도달하면, 그 tile에 대해서 뒤쪽에 존재하는 gaussian에 대해서는 더하는 계산을 하지 않습니다.

이 알고리즘은 cuda 파일에서 실행합니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/42

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/61004042-09af-461c-ac19-13a9a464e9b0)
