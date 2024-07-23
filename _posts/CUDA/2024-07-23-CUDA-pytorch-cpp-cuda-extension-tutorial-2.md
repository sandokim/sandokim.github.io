---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 2"
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
  - cuda tutorial 2
excerpt: "Pytorch c++/cuda extension tutorial 2"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 2

Tutorial 1에서 ***c++는 오직 pytorch과 cuda만을 연결하는 bridge 역할만 한다***고 배웠습니다.

Tutorial 2에서부터는 cuda programming을 배워보겠습니다.

### 먼저 cuda에서 parallelism이 어떻게 되는지 알아봅시다.

![image](https://github.com/user-attachments/assets/63efeadb-2754-44bd-ac37-fafbc6261f7c)

- host(=cpu)에서 c++ program이 cuda function을 call 하면, functiona call마다 하나의 kernel을 instantiate 합니다.
- cuda가 하는 것은 host(=cpu)로부터 device(=gpu)로 data를 transfer합니다. 이때, 동시에 grid가 생성됩니다. (grid는 나중에 parallel computation에 책임을 가집니다.)
- grid는 multiple "units"을 가지며 이들은 parallel computation이 가능합니다.
- grid는 multiple "blocks"를 가지고, blocks의 수는 program에 의해 결정됩니다.
- 각 block은 더 divide될 수 있습니다.
  
  ![image](https://github.com/user-attachments/assets/4204a994-88ac-4485-8df0-40c99ad7ddc9)

- Each block은 multiple "threads"를 가지고 있고, threads는 finest computation units이라고 보면 됩니다.

### The secret behind parallelism is that when we call a function, it instantiate a grid that contains multiple blocks which contain again many threads.
















감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 2 - English CC -](https://www.youtube.com/watch?v=_QqG_I8nfH0&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=2)
