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

## trilinear interpolation을 하기 위해 feats와 points의 shape을 정의합니다.

- feats # (N, 8, F)
  - N is the number of cubes
  - 8 is the number of vertices
  - F is the number of features on each vertex

- points # (N, 3)
  - point coordinates
  - N is the number of points and each of them corresponds to one cube in the "feats" tensor

## Shape을 알았으니 이제 어떻게 parallelize computation을 수행할지 생각해볼 수 있습니다.

- Parallelize하는 2가지 방법이 있습니다. (N parallelize, F parallelize)
1. Since the interpolation of each point doesn't depend on any other points, we can parallelize N.
2. We can parallelize F, if we have multiple features, the interpolation of each feature doesn't depend on any other feature either, so we can parallelize.
 
### 이제 Parallize를 코딩해봅시다.

- Tutorial 1에서 만들었던 `interpolation.cpp`
  
  ![image](https://github.com/user-attachments/assets/c6e31b5e-b6fa-40ce-a89e-8ce9508ddb5d)

- cuda code를 작성하기 위해 `interpolation_kernel.cu`를 만들고, 여기 parallelism을 코딩한 다음, 이를 c++ 코드인 `interpolation.cpp`로부터 call 할 것입니다.
- 항상 그랬듯이 `#include <torch/extension.h>`를 먼저 작성하여, cuda가 tensor가 무엇인지 알려줍니다.
- `interpolation.cpp`에서 `trilinear_interpolation`으로 정의한 함수를, `interpolation_kernel.cu`에서는 `triliner_fw_cu`로 이름만 바꿔줘서 foward 문을 만듭시다.
- `fw`는 `forward`, `bw`는 `backward`를 의미합니다.
- `_cu`는 `cuda code`를 의미합니다.
- 우리가 each vertex의 `"feats"`의 weights를 train하려면, 나중에 trilinear_interpolation의 backward pass도 implement 해야합니다.

  ![image](https://github.com/user-attachments/assets/733e6291-b1b7-46a8-aa9e-b46f8f39693f)

- `interpolation_kernel.cu`에서 작성한 `trilinear_fw_cu` 함수를 c++ 파일인 `interpolation.cpp` 불러오려면 c++ project에서 하는 것처럼 `.cpp`파일 말고도
- function들을 정의하는 `.h` 헤더파일을 가지고 있습니다.
- cuda programming에서도 함수를 정의하는 `.h` 파일이 필요합니다.
- 먼저 함수를 declare 하여, c++가 함수가 존재하는 것을 알게 해줘야 합니다.
- 당연히 같은 `.cpp` 파일 안에 함수를 정의할 수도 있지만, 더 좋은 방법은 `include` 폴더를 새로 만들고, 그 안에 `.h` 파일을 만드는 것입니다.

  ![image](https://github.com/user-attachments/assets/f0202bba-083c-4783-9e78-7282b477fab8)

  - 여기에 역시 `#include <torch/extension.h>`를 쓰고,
  - 다음으로 function delcaration (function name, inputs and output types)를 써줍니다.

    ![image](https://github.com/user-attachments/assets/453dbf2e-b131-4532-9c17-15b9b964de1a)

  - 이것을 `.cpp` 파일에 `.h`로 include를 해주면 됩니다. 예시에서는 `#include "utils.h"`로 include 했습니다.
  - c++는 이제 header 파일을 보고 `trilinear_fw_cu`가 어떤 함수인지 알 수 있습니다.

    ![image](https://github.com/user-attachments/assets/7ca25f28-cb17-4675-98c4-6a0d71293c7a)

### 지금까지 한건 function content를 cuda에 넣은 것 뿐이고, 지금부터 놀라운 일을 할 것입니다.

- `.cpp`에서 `utils.h`에 정의한 함수를 불러오기 전에 inputs를 먼저 "check" 해줍시다.
- `utils.h`에 `CHECK_CUDA, CHECK_CONTIGUOUS, CHECK_INPUT`에 관련된 라인을 copy & paste 합시다.

  ![image](https://github.com/user-attachments/assets/18b62644-7f17-4717-8843-8abdd3abbe36)

  - `CHECK_CUDA`는 tensor가 cuda 위에 있는지 확인합니다. cuda에서 computation을 하기 위해서는 tensor를 cuda로 옮겨야만 합니다.
  - `CHECK_CONTIGUOUS`는 if a tensor's storage is the same after flattening the tensor를 체크합니다. Because in parallel computation, the workers are one after another, their positions in the memory must be continuous so that each worker can access the data correctly. So the tensor must be contiguous
  - `CHECK_INPUT`은 위의 2개의 conditions을 check합니다.
 
```cpp
#define CHECK_CUDA(x) TORCH_CHECK(x.is_cuda(), #x " must be a CUDA tensor")
#define CHECK_CONTIGUOUS(x) TORCH_CHECK(x.is_contiguous(), #x " must be contiguous")
#define CHECK_INPUT(x) CHECK_CUDA(x); CHECK_CONTIGUOUS(x)
```

- 위 3개의 magic line들을 `utils.h`에 추가하고, `.cpp`에 `#include utils.h`함으로써
- 각 c++ function에 대해 우리가 cuda 위에서 실행하고 싶으면, `CHECK_INPUT`을 call하여 각 input이 tensor라면 이 tensor가 cuda위에 있는지 확인할 수 있습니다.

  ![image](https://github.com/user-attachments/assets/2c0affed-e2f0-4cc5-922e-ee12f0df8732)

- tensor말고 다른 inputs을 넣는 경우, i.e. `const float x`와 같은 inputs에 대해서는 `CHECK_INPUT`이 필요없습니다.

  ![image](https://github.com/user-attachments/assets/f48ae0f4-d76e-482b-8c26-c74f6d8eda7c)


### `interpolation_kernel.cu`에서 `trilinear_fw_cu`에 대한 코드는 Tutorial 3에서 짜는 것으로 하고 마지막으로, build를 알아봅시다.

- Tutorial 1에서는 CppExtension으로 cpp code를 build 하고 python으로부터 call 하였습니다.

  ![image](https://github.com/user-attachments/assets/4d10d908-961c-41f5-b5c6-4cdf369e8f64)

- 지금은 cpp code가 cuda를 사용하고 있으므로, `setup.py`에서도 이에 맞게 바꿔줘야합니다.
- CppExtenssion을 CUDAExtension으로 바꿔줍니다.
- name도 cuda를 넣은 것으로 바꿔줍니다.
- build 할 sources가 `.cpp`, `.cu`로 여러개가 되었는데, 이를 콤마로 연결하여 하나하나 치면 실수를 할 수도 있으니, `glob.glob`으로 `.cpp`, `.cu` 관련 파일을 찾아 리스트 다 넣어줄 수 있습니다.
- `extra_compile_args`는 code optimization으로 complied files의 file size를 낮춥니다. program speed와는 아무런 상관이 없고 오직 file size만 줄이는 옵션입니다.

  ![image](https://github.com/user-attachments/assets/8ff0664c-81d8-428c-a091-bef676fe4a93)



감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 2 - English CC -](https://www.youtube.com/watch?v=_QqG_I8nfH0&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=2)
