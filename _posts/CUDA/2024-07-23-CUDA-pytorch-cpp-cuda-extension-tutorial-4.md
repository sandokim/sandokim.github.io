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
  - forward pass
  - backward pass
  - c++/cuda
  - cuda extension
  - cuda tutorial 4
  - __global__
  - __device__
  - __host__
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
- `.packed_accessor`에 이어 나오는 것으로는...
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

  - Next is the function definition, `__global__` means a keyword for cuda function. `__global__` means the function is called by the host(=cpu) and it is executed on the gpu. 
     
    ![image](https://github.com/user-attachments/assets/bf582a92-0067-459c-b7a6-30c2b148f92c)

    - ***So basically when you call the kernel using `AT_DISPATCH`, you always need this `__global__` keyword, since you call it from cpu and the execution is on gpu.***
    - **`__global__`: the function is called on cpu and executed on gpu.**
    - `__global__`만 알아도 충분합니다.
    - `__host__`: the function is called and executed both on cpu.
    - `__device__`: the function is called and executed both on the gpu.
    - Again, basically you only need this `__global__`, since everything called by `AT_DISPATCH` is called from cpu and executed on gpu. So we always have this `__global__` keyword.
    
  - **the return type is `void`**, the kernel function doesn't return anything, it fills the correct values into the output tensor. No matter what your kernel is, the return type is always void. You need to pass the input and output tensors as arguments, and fill in the output tensors inside the function.

    ![image](https://github.com/user-attachments/assets/9c0bbd94-44b9-43ef-b37d-be1a2ad6a63e)

  - `trilinear_fw_kernel`은 kernel의 name.

    ![image](https://github.com/user-attachments/assets/2a37b862-1ac7-4082-975c-057209392330)

  - inputs are packed accessors that we just converted, it is a data type under torch namespace(`torch::`) which we need to write the full name (`PackedTensorAccesor`)

    ![image](https://github.com/user-attachments/assets/3c0a444d-a7be-4629-a6c5-422eb732f5a4)

    - `PackedTensorAccesor`의 뒷 부분은 kernel function인 `trilinear_fw_kernel`을 `AT_DISPATCH_FLOATING_TYPES`에서 수행할 때 사용한 것과 동일한 것을 copy & paste 하면 됩니다.
      
      ![image](https://github.com/user-attachments/assets/e025a933-16b4-4dd2-98db-f0d95797de21)
      
    - 맨 뒤에는 variable name을 써줍니다. (feats, points, feat_interp)
      
      ![image](https://github.com/user-attachments/assets/6f36b144-8a27-4c70-a758-fd71ea18516c)

      - output인 feat_interp에는 const를 넣지 않았는데, 이유는 const인 input과 다르게 output은 correct output으로 one by one으로 fill 할 것이기 때문입니다.

## 지금까지 kernel function인 `trilinear_fw_kernel`의 input을 살펴보았고, 이제 이 함수의 기능을 펼쳐서 봅시다.

![image](https://github.com/user-attachments/assets/609cbf3f-9c7c-44bb-8d08-fefbd9cc968c)

## Recap: the process of parallel programming is, each element of the output tensor is calculated by the thread that covers that element.**

![image](https://github.com/user-attachments/assets/13d7e21c-ae4e-4b42-a047-5a8c06c2a26b)

![image](https://github.com/user-attachments/assets/6280a6a7-cb34-4e4b-9da3-1d39de41d04a)

![image](https://github.com/user-attachments/assets/f600af4d-998a-4ab4-9b0a-8835d64171b5)

## So, we need to know each element is computed by which thread in which block.

### But How?
  
  - ### step 1: compute the id for each thread.
    
    ![image](https://github.com/user-attachments/assets/7e0d1a67-ab1a-4f24-a1b4-0d7bc7d76186)

    - 2-dimensional로 parallel computation을 하는 경우, 아래 코드를 copy & paste 하여 사용하면 됩니다.
      
      ```css
      const int n = blockIdx.x * blockDim.x + threadIdx.x;
      const int f = blockIdx.y * blockDim.y + threadIdx.y;
      ```

      ![image](https://github.com/user-attachments/assets/c673968d-6eb0-4cac-a0e8-f3613cde1c5f)

  - ### step 2: we need to exclude redundant threads from the computation.

    ![image](https://github.com/user-attachments/assets/cd692f73-6562-4e62-a2e5-6e2f12f526bb)

    ![image](https://github.com/user-attachments/assets/9647294d-6bb1-467f-a385-e030cba7f170)

    ![image](https://github.com/user-attachments/assets/676a8509-1a8c-4624-9b02-d545256be122)

    ![image](https://github.com/user-attachments/assets/0bd7ba00-c15e-4bc3-889b-064d90c474f7)

    - Redundant한 영역에 존재하는 threads의 computation을 중단하는 방법은 2개가 있습니다.

      ![image](https://github.com/user-attachments/assets/c9035983-61a0-4b5b-91d8-5c119d181d82)

    - input의 shape으로 valid range를 설정할 수도 있
    
      ![image](https://github.com/user-attachments/assets/2a34d010-25c5-48ce-b7fd-6cc46d294608)

    - valid range를 넘어가는 것에 대해서는 return하여 computation을 중단시킬 수도 있습니다.

      ![image](https://github.com/user-attachments/assets/6224ee75-4632-42fa-a1ce-62b7ec8b6d37)

### 위 조건을 만족하면 드디어 valid한 threads에 대해서만 parallel computation하는 코드가 이어집니다.

![image](https://github.com/user-attachments/assets/df50e6ed-8af5-4cea-994b-ebcf29845cf6)

![image](https://github.com/user-attachments/assets/5755460e-692f-4324-b1b2-0e660b33a5fe)

![image](https://github.com/user-attachments/assets/a64b52e9-ac70-4735-b89e-c13c1eb254d0)

#### xyz에 대한 trilinear interpolation weight로 u,v,w를 사용합니다.

![image](https://github.com/user-attachments/assets/08b05fd4-165f-4230-a377-e89a8aef23ea)

#### input (local) coordinate가 -1 ~ 1에 존재하므로, normalize를 하기 위해, 1을 더하고 2로 나눠줍니다.

![image](https://github.com/user-attachments/assets/276f05c9-53cc-4af6-b083-897118fa87d7)

#### n,f로 각 thread의 위치를 정의해주었으므로, n,f로 points와 features에 대해 indexing을 할 수 있습니다.

![image](https://github.com/user-attachments/assets/73b1d21c-b192-44a2-a117-c27789f5fe21)

![image](https://github.com/user-attachments/assets/b51905ea-0acb-4d7e-a6b3-5b78974d3430)

#### interpolation의 weights를 구해줍니다.

![image](https://github.com/user-attachments/assets/ceb8d6d1-d636-4fa7-a965-8f2f7d074429)

#### output tensor인 feat_interp에 trilinear interpolation으로 구한 value를 채워줍니다.

![image](https://github.com/user-attachments/assets/49b0e492-b0e4-442d-8dea-6ae16fe1af15)


### `trilinear_fw_kernel`에서 `return feat_interp`를 해줘야 합니다. (본 튜토리얼 진행에서 까먹고 안넣었음)

![image](https://github.com/user-attachments/assets/3968ef61-a242-448b-b8f0-822f1d22ad49)

- return 되는 `feat_interp`의 type은 `torch::Tensor` type이므로, `trilinear_fw_cu` 앞에 return output의 type인 `torch::Tensor`를 명시해줍니다.
- `torch::Tensor trilinear_fw_cu`
  
  ![image](https://github.com/user-attachments/assets/5a77babb-e331-4b31-ac11-a65a916fe767)

#### 만약 return 되는 output이 2개 이상이면, {}로 묶어서 return하고, return type을 `std::vector<torch::Tensor>로 써줍니다.

![image](https://github.com/user-attachments/assets/0795ee8b-0253-4ac1-b802-e518ed35d12d)

- `std::vector<torch::Tensor>`로 `trilinear_fw_cu` 함수의 return type을 바꿨으면 `.h`, `.cpp`에서도 똑같이 return type을 `std::vector<torch::Tensor>`로 바꿔줘야합니다.

  - i.e. `utils.h`
    
    ![image](https://github.com/user-attachments/assets/30052f90-67a7-47bf-a389-3ede96d26bc0)

    - 위 내용을 아래와 같이 수정합니다.
    
      ```css
      std::vector<torch::Tensor trilinear_fw_cu(
          torch::Tensor feats,
          torch::Tensor points
      );
      ```

#### 코드를 변경했으면, `pip install <path/to/your/setup.py>`로 re-compile하는 것도 까먹지 맙시다. 

```cmd
pip install .
```

감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 4 - English CC -](https://www.youtube.com/watch?v=jXQxF5FnR5Q&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=4)
