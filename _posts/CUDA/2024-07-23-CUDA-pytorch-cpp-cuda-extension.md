---
title: "[CUDA] Pytorch c++/cuda extenstion"
last_modified_at: 2024-07-23
categories:
  - CUDA
tags:
  - cuda programming
  - cuda
  - forward
  - backward
  - c++/cuda
  - cuda extension
excerpt: "Pytorch c++/cuda extension"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial

CUDA is a programming language for GPU, so it enables faster parallel computation, thus making pytorch faster.

![image](https://github.com/user-attachments/assets/1dc62636-0a44-4772-ae17-7ed120a1db29)

CUDA does parallel computation and return the output to pytorch.

So the actual important part is on cuda, not c++.

c++ is only a bridge that connect pytorch and cuda.

## 본 tutorial 1에서는 NeRF의 volume rendering을 쉬운 버전으로 만들어볼 것입니다.

- trilinear interpolation으로 8 vertices로부터 feature f를 얻습니다.

![image](https://github.com/user-attachments/assets/7537f3cc-c49b-4806-b70a-85df1bfd4099)

![image](https://github.com/user-attachments/assets/7d417c2f-5267-4fda-a299-9387da733f42)

- tutorial 1에서는 cuda없이 c++ bridge만 써볼 것입니다.





### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 1 - English CC -](https://youtu.be/l_Rpk6CRJYI?si=VUe9psNzk60F7iO6&t=478)
