---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 4"
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
  - forward
  - backward
  - c++/cuda
  - cuda extension
  - cuda tutorial 4
excerpt: "Pytorch c++/cuda extension tutorial 4"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 4

### After calculating threads and blocks, we call a function `AT_DISPATCH_FLOATING_TYPES` which is reponsible for instantiating a kernel.

![image](https://github.com/user-attachments/assets/66688969-08c3-4f5a-b734-061fa0ce7fd5)

- kernel은 `AT_DISPTACH_FLOATING_TYPES` 내부에 쓰여있으니, copy & paste 하고 arguments만 바꾸면 됩니다.

#### First we place the kernel that we want to call. We will code this function later. `triliner_fw_kernel` 
#### Next, the `<scalar_t>` is a "template" for the data type. This allows the kernel to do computation for different data types.
  
  ![image](https://github.com/user-attachments/assets/7b587889-dab6-4674-9be2-fc99cdb696e0)

  - 예를 들어, 우리는 `AT_DISPATCH_FLOATING_TYPES`로 `FLOATING_TYPES`로 float32, float64에 대한 연산으로 정의했습니다.
  - 만약 우리가 input인 `feats`의 type을 모르는 상황이라면, 우리는 `scalar_t`를 사용할 수 있습니다.
    - `feats`가 float32 --> `scalar_t`도 float32
    - `feats`가 float64 --> `scalar_t`도 float64
    - `scalar_t` efficiently covers different data types.

#### `blocks`, `threads`는 앞서 정의한 blocks와 threads의 개수입니다.

#### 그 다음은 우리의 inputs과 outputs 입니다.
- ***강조드릴 점은 kernel function은 return하는 것이 없습니다.*** return type이 항상 void 입니다.
- 우리의 모든 inputs과 outputs을 kernel function에 넣어줍니다. (inputs인 feats, points, output인 feat_interp)







감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 4 - English CC -](https://www.youtube.com/watch?v=jXQxF5FnR5Q&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=4)
