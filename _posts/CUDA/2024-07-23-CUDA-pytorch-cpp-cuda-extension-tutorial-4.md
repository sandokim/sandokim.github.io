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
- computation을 하면, correct outputs이 output tensor에 채워지게 됩니다. (this is how kernel works)
- We need to pass the output tensor as argument, and fill it in gradually.

#### tensor vairables에 `.packed_accessor`는 cuda에서 tensor를 manipulate 가능하도록 type을 바꿉니다.

- 일반적으로 우리는 variable을 function에 pass 합니다.
- cuda는 "tensor" type을 recognize하지 못하므로, cuda가 recognizes할 수 있는 type으로 바꿔줘야 합니다.
- "tensor"를 `.packed_accessor`로 변환하여, kernel에서 사용될 수 있는 type으로 바꿉니다.
- `.packed_accessor`에 이어 나오는 것
  - `scalar_t` 위에서와 동일하게, 들어오는 데이터 타입과 같은 데이터 타입으로 만들어줍니다. (`scalar_t` 대신 명시적으로 float32 타입인 `float`로 정의할 수도 있긴 합니다, 하지만 kenerl function에 들어오는 inputs이 float32가 아니면 에러가 발생합니다. Flexibility를 위해 `scalar_t`로 사용하는게 일반적입니다.)
    
    ![image](https://github.com/user-attachments/assets/f6761312-ac8a-4ad4-b0cd-b8fc84a53cea)

  - `3`은 tensor의 dimensions, 이때 "feats" # (N, 8, F)의 shape을 가지는 `three-dimensional tensor`이므로 `3`.
  - 나머지 2개 인자인 `torch::RestrictPtrTraits, size_t`는 대부분의 경우 바뀌지 않습니다.
    - `torch::RestrictPtrTraits`는 "feats"가 다른 어떤 tensors와도 overlay되지 않도록 합니다.
    - `size_t` means how many "steps" to take between each elements. We can think of this packed accessor as a 3D array. To access elements, we do a bracket indexing and `size_t` means what data type we use for these indices, basically just leave as `size_t`, we don't change this.

#### tensor가 아니라면 kernel function에 `.packed_accessor`같은 convertion 없이 그냥 넣으면 됩니다.

![image](https://github.com/user-attachments/assets/59051439-961b-487c-83b4-75ca4bdafea0)

- 위 예시에서는 tensor가 아닌 `bool a`를 정의하고, kernel function `trilinear_fw_kernel`에 `a`를 넣었습니다.


## Launch a kernel까지 끝냈으니, 사용될 kenerl function 코드를 실제로 짜봅시다.

### 위에서 kernel function으로 사용된 `trilinear_fw_kernel`을 짜봅시다.

![image](https://github.com/user-attachments/assets/3bb10d60-c997-46bd-a159-178bbc492d16)

- kernel function을 수행시켰던 `interpolation_kernel.cu`에 `trilinear_fw_kernel`을 정의해봅시다.
- `triliner_fw_kernel`
  - 먼저 자동으로 input의 데이터 타입들에 맞게 사용하기 위해서 `scalar_t`를 사용합니다.
  - In order to apply this setting to the kernel, we have this line above the function (`template <typename scalar_t>`) to tell that the data type is actually variable.

    ![image](https://github.com/user-attachments/assets/2017af81-de5e-425c-8d61-1165af0fd586)

  - Next is the function definition, let's ignore this `__global__` now.
  - **the return type is `void`**, the kernel function doesn't return anything, it fills the correct values into the output tensor. No matter what your kernel is, the return type is always void. You need to pass the input and output tensors as arguments, and fill in the output tensors inside the function.

    ![image](https://github.com/user-attachments/assets/9c0bbd94-44b9-43ef-b37d-be1a2ad6a63e)

  - `trilinear_fw_kernel`은 kernel의 name.

    ![image](https://github.com/user-attachments/assets/2a37b862-1ac7-4082-975c-057209392330)




감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 4 - English CC -](https://www.youtube.com/watch?v=jXQxF5FnR5Q&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=4)
