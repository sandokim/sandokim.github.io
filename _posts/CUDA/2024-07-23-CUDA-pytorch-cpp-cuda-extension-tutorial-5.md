---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 5"
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
  - cuda tutorial 5
excerpt: "Pytorch c++/cuda extension tutorial 5"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 5

### tutorial 4에서 작성한 trilinear interpolation을 python에서 call 하여 사용하여 학습해봅시다. (쉬움)

#### pytorch code와 cuda code로 작성한 것의 속도를 비교해 볼 것입니다.

만약 pytorch code와 cuda code의 outputs이 같다면, cuda code가 올바르게 작성되었다고 판단할 수 있습니다.

# Call the function in python

![image](https://github.com/user-attachments/assets/8cdbd527-e011-406b-9892-f9e168a6aad4)

- feats: random tensor of shape (N,8,F) representing the features to interpolate. 
- points: (N,3) representing the local coordinates of the points. To make sure the range is in [-1, 1], do *2-1.

### To call the cuda function, we first import the package (whose name is defined in `setup.py`)

- `setup.py`에서 CUDAExtension 아래 `name`으로 정의된 `cppcuda_tutorial`을 import해서 사용합니다.

![image](https://github.com/user-attachments/assets/12452377-8ff5-4a6e-bb89-5a1eb5a7d07b)

![image](https://github.com/user-attachments/assets/9769d32e-f75f-4709-b473-821021933649)

![image](https://github.com/user-attachments/assets/354a3c58-5d1e-4539-9603-b20b42ee2ed7)

# Check the correctness with the same function in pytorch code

- pytorch로 작성한 `trilinear_interpolation_py`과, cuda로 작성한 `trilinear_interpolation`의 output이 같음을 확인했습니다.
- `torch.allclose()`는 두 tensor가 일정 에러 미만의 차이면 True를 return 합니다.

![image](https://github.com/user-attachments/assets/b18d36e2-7001-489e-afbb-d4b321079e43)


## CUDA에서 Deep Learning에서의 forward pass와 backward pass 

- ***If you write in cuda, it doesn't provide "autograd" automatically. It cannot compute gradient automatically.***
- No matter your inputs are trainable or not, the output is always not trainable.
- If we compute loss based on "out_cuda" and backpropagate, it cannot update "feats" correctly.

  ![image](https://github.com/user-attachments/assets/a702c667-79ed-45e8-8673-31f64894d59c)

- ***So we need to implement the backward pass as well in CUDA.***
- **We need to compute the gradients manually and implement.**


감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 5 - English CC -](https://www.youtube.com/watch?v=XpHwMriwe-I&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=5)
