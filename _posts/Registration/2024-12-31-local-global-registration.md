---
title: "[Registration] Global Registration & Local Registration"
last_modified_at: 2024-12-31
categories:
  - Registration
tags:
  - Registration
  - 정합
  - global registration
  - local registration
  - Iterative Closest Point (ICP)
excerpt: "Global & Local Registartion"
use_math: true
classes: wide
comments: true
---

#### Reference

> [Zero NeRF: Registration with Zero Overlap](https://arxiv.org/abs/2211.12544)

### Registration은 크게 Global Registration과 Local Registration으로 분류할 수 있습니다.

- Local registration은 approximate solution을 초기값으로써 필요로 합니다.
- 반면 Global Registration은 초기값을 필요로 하지 않고 일반적으로 explicit correspondences를 찾는 과정을 포함합니다.

#### Iterative Closes Point (ICP)

- Iterative Closest Point (ICP)와 그 변형 기법들은 Local Registration의 대표적인 형태입니다.
- ICP는 local minima에 빠지기 쉬우며, 장면이 완벽하게 정합되더라도 부분적으로만 겹치는 장면에서는 문제가 발생할 수 있습니다.
- ICP는 명시적인 대응(explicit correspondences)을 필요로 하지 않지만, 겹침(overlap)에 의존합니다.
