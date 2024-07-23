---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 3"
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
  - cuda tutorial 3
excerpt: "Pytorch c++/cuda extension tutorial 3"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 3

## trilinear interpolation

### inputs 
- feats # (N, 8, F)
  - N is the number of cubes
  - 8 is the number of vertices
  - F is the number of features on each vertex

- points # (N, 3)
  - point coordinates
  - N is the number of points and each of them corresponds to one cube in the "feats" tensor

### outputs
- feat_interp # (N, F)
  - N points
  - Each point's feature is the interpolation of the 8 vertices of the cube that contains that point, and each point has F features, so after interpolation, each point has F features too.

### input & output shape을 기억하고, 이제 `interpolation_kernel.cu`에서 `trilinear_fw_cu` cuda 코드를 작성해봅시다.

- 꼭 `const`로 정의해야만 하는 건 아니지만, `const`로 정의하면 variable이 변하지 않는다는 것을 알려주므로 `const`를 넣어 변수를 정의하는 것이 좋습니다.

  ![image](https://github.com/user-attachments/assets/9fa57d2e-500e-4512-98b8-d3295066ef68)

- output tensor는 처음에 아무거나 정의하면 되고, 나중에 correct values로 채워주면 됩니다.
  - 텐서를 초기화할 때 그 안의 모든 값이 (대부분의 경우) 커널에 올바르게 채워지면 torch::empty를 사용하는 것이 것보다 낫습니다.
  - torch ::zeros 가 훨씬 더 좋아졌습니다(조금 더 빨라졌습니다). torch::zeros는 먼저 torch::empty를 수행한 다음 모든 값을 0으로 재설정하기 때문에 이는 한 단계 더 수행하는 것과 같습니다.

## cuda programming도 python과 동일하게 device와 dtype에 대한 설정을 해줘야 합니다.

### Rule 1. the output tensor ALWAYS put on the same device as the input tensor(s).
- For example, if "feats" is put on the first gpu, then "feat_interp" needs to be on that gpu too.
- It is for multi-gpu training, we need to put the tensors on the same device.
- This rule applies for any function, no matter the content.

### Rule 2. the data type is not necessarily the same as the input.
- In out example, the output data type is the same as input ("feats").
- no matter how many bits (float16, float32, float64) it has, the output has the same data type.

### `.options()` assigns both the same data type and the device.

![image](https://github.com/user-attachments/assets/2673c483-6c56-4a6b-b436-7d1ea1574d51)

- `.option()`으로 input과 same data type, same device에 존재하는 output tensor를 정의할 수도 있고
- input과 다른 data type으로 output tensor를 가지려면, 원하는 데이터 타입을 dtype으로 정의하고, `.device(feats.device)`로 input과 same device에 올려야 합니다.

```css
torch::Tensor feat_interp = torch::zeros({N, F}, feats.options());
torch::Tensor feat_interp = torch::zeros({N, F}, torch::dtype(torch::kInt32).device(feats.device));
```

- 이로써 output에 대한 placeholder까지 생성한 상황이고, correct values로 채워넣어야 합니다.








감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 3 - English CC -](https://www.youtube.com/watch?v=-vV08i-Eifs&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=3)
