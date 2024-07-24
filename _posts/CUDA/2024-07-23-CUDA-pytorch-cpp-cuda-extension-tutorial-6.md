---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 6"
last_modified_at: 2024-07-23
categories:
  - CUDA
tags:
  - cuda programming
  - cuda
  - cpp
  - c++
  - cpp bridge
  - c++ bridge
  - forward pass
  - backward pass
  - c++/cuda
  - cuda extension
  - cuda tutorial 6
excerpt: "Pytorch c++/cuda extension tutorial 6"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 6

- Tutorial 5에서 trilinear interpolation을 forward pass로 구현했지만, 아직까진 backward pass는 못합니다.
- So if we want to calculate some loss based on this variable, it is not able to compute the correct gradient to update our features,

## backward pass를 implement하여 gradient가 back으로 passed 되도록 cuda code를 작성해보겠습니다.

### cuda backward implementation에서 가장 중요한 2가지 스텝은 다음과 같습니다.

- First, we need to compute all outputs' partial derivatives w.r.t. all trainable inputs.

![image](https://github.com/user-attachments/assets/decf0757-6b08-406f-938b-d58d0a2b4abd)



















감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 6 - English CC -](https://www.youtube.com/watch?v=oG0WUq3bRz0&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=6)
