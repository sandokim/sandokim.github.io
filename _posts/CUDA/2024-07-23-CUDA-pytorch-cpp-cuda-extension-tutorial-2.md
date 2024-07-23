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

### THIS IS HOW CUDA WORKS
- **The secret behind parallelism is that when we call a function, it instantiate a grid that contains multiple blocks which contain again many threads and each thread can do its operation in parallel.**
  - For example, a matrix addition is addition of two elements at the same position, the program generates a grid that contains as many threads as the elements in the matrix, and each thread performs one addition of two elements at its position.
    - You can think of "threads" as "workers", each worker performs a task that requires roughly the same amount of time.
    - Each worker performs only one addition of two numbers, no one needs more resource or more computational time than others.
  - Since we divide the taks to multiple sub-tasks that can be done in parallel with each task requiring the same resource and amount of time, we can accelerate the entire process.

### 다시 CUDA가 동작하는 걸 써보면..

- Each time a function is called, it will generate a grid, grid contains blocks, block contains threads, it's a three layer hierarchy like this.

  ![image](https://github.com/user-attachments/assets/c16b3c9c-5eb4-4c49-8f2b-f7627120ad4f)

- You might think of a question: why do we need to pass by blocks? Can't we just have many threads per grid?
  - It's a hardware limitation, the max number of threads is limited to 1024.
  - 각 block 내에서 생성할 수 있는 스레드의 최대 수는 GPU 아키텍처에 따라 다릅니다.
  - 예를 들어, NVIDIA의 CUDA 기반 GPU에서는 보통 한 block당 최대 1024개의 스레드를 생성할 수 있습니다.
  - block have larger limits than therads
  - And the max number of "workers" is the product of all these numbers.
  - if we don't have blocks, we can only have at most 1024 threads.
  - **By having blocks, we can increase the max number of threads we can have.**
    
    ![image](https://github.com/user-attachments/assets/5335a50b-6892-41d4-8cb4-37057a46aa7f)

### Parallelism is achieved by having each thread do a minimal work, such as the operation that we just mentioned: adding two numbers together.

- this operation is fast, and we have many threads doing this work on different positions, whcih makes the throuughput high

# 이제 Tutorial 1에서 언급했던 trilinear interpolation에 대해 parallelism을 적용해봅시다.

- trilinear interpolation을 간단히 이해하기 위해 bilinear interpolation 형태에서 식을 쓰고 먼저 이해해봅시다.
- vertices가 4개일 때, 각 vertex마다 feature가 존재합니다.
  - (v_1, f_1), (v_2, f_2), (v_3, f_3), (v_4, f_4)
  - 사각형안의 임의의 점에 대한 feature f는 4개의 vertices로부터 거리의 비율을 interpolation으로 구하고, interpolation의 weight만큼을 각 vertex feature에 곱하고 이들을 모두 더하여 얻습니다.

![image](https://github.com/user-attachments/assets/fac85e28-21e1-4482-b7d6-79fee5e32627)












감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 2 - English CC -](https://www.youtube.com/watch?v=_QqG_I8nfH0&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=2)
