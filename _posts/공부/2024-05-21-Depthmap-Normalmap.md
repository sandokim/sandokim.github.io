---
title: "[3D CV] Depth map & Normal map"
last_modified_at: 2024-05-21
categories:
  - 공부
tags:
  - 3D CV
  - depth map
  - normal map
  - normal vector
  - surface
excerpt: "Depth map & Normal map 정리"
use_math: true
classes: wide
---

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/07d0121e-6528-419e-8217-bf54614fc68b" alt="Image">
</p>

- **Depth Map**:
    - **목적**: **깊이 정보를 제공하여 거리와 3D 구조를 파악.**
    - **표현 방식**: **각 픽셀이 깊이 값을 가지며, 흑백 이미지로 표현.**
    - **주요 사용 사례**: 거리 측정, 3D 스캐닝, 가림 현상 처리.

- **Normal Map**:
    - **목적**: **표면의 법선 벡터 정보를 제공하여 디테일과 조명 효과를 개선**.
    - **표현 방식**: **각 픽셀이 RGB 값을 가지며, 이는 법선 벡터의 X, Y, Z 성분을 나타냄.**
    - **주요 사용 사례**: 표면 디테일 추가, 조명 계산, 게임 그래픽.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/907f1a07-9221-4edc-b93f-409e36d0f642" alt="Image">
</p>

[이미지 출처](https://raypop.tistory.com/71)


## Normal map default 색이 약간 보라색인 이유

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f834ca09-8b62-46f8-b193-9b8954a5ad72" alt="Image">
</p>

***Value Range*** 

- **normal vector xyz (-1.0~1.0)**
- **normal map RGB values (0.0~1.0)**

**Default normal vector:** (0.0,0.0,**1.0**) / xy**z** vector value range -1.0~1.0 

→ z는 surface에서 튀어나오는 방향이므로 default가 1.0이고 x,y는 표면상에서 좌우상하이므로 flat한 surface에서는 x,y값이 0.0입니다.

→ default normal map은 표면에서 z 방향 벡터만 1.0의 값으로 존재하고 x,y 벡터 방향 성분은 없습니다. → (0.0,0.0,1.0)

**Default normal map** (0.5, 0.5, 1.0)

Default normal vector (0.0, 0.0, 1.0) → Default normal map (0.5, 0.5, 1.0) ←보라색

## Normal map 색깔 보는법

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/dbb9beaa-39e1-4b9b-a9c5-add26d11dbd0" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/2dff7eb1-58f6-4416-9d78-e78fa5d91fc5" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/65f40c75-7215-4fe8-a7c0-c48a9f971e6d" alt="Image">
</p>

Normal map에서 normal vector를 그려보면 위처럼 표현해볼 수 있습니다.
