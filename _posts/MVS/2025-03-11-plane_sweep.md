---
title: "[MVS] Plane sweep algorithm in multi-view stereo"
last_modified_at: 2025-03-11
categories:
  - MVS
tags:
  - MVS
  - Multi view stereo
  - plane sweep
  - plane sweep algorithm
excerpt: "Plane sweep algorithm in multi-view stereo"
use_math: true
classes: wide
comments: true
---

> Reference

> [Plane sweep algorithm in multi-view stereo](https://medium.com/@alokguy2004/plane-sweep-algorithm-in-multi-view-stereo-fd070bb0d92f)

Plane sweep 알고리즘은 다중 시점 스테레오(Multi-view Stereo)에서 2D 이미지 집합으로부터 3D 장면을 재구성하기 위해 널리 사용되는 기법입니다. 

이 알고리즘의 핵심 아이디어는 3D 공간을 따라 하나의 평면을 쓸어가면서, 각 위치에서 장면의 2D 투영을 해당 평면 상에 계산하는 것입니다. 

이를 통해 평면의 각 픽셀에 대해 장면의 깊이 정보를 얻을 수 있으며, 이 깊이 정보를 활용하여 전체 3D 장면을 재구성할 수 있습니다.

