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



