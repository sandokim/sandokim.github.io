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

## 여기서부터 parallel computation이 등장합니다.

- parallel computation 과정은 다음과 같습니다.
  - cpu creates a kernel, which creates a grid on gpu
  - a grid contains multiple blocks, each of them containing multiple threads
  - a thread does a parallel computation, in our case it's doing interpolation of a point from its 8 vertices' features

    ![image](https://github.com/user-attachments/assets/297f328d-6774-48cb-a0f6-8e2924c3318e)

### computation을 하기에 앞서 blocks과 threads의 size를 알아야 합니다.

- 사실 blocks 수는 formula에 의해 결정됩니다. blocks 수를 계산하는 formula는 나중에 살펴보겠습니다.
- 따라서 block 당 threads 수만 결정해주면 됩니다.
- 우리는 현재 얼마나 많은 threads가 필요가요?
- 우리의 알고리즘을 볼 때, 얼마나 많은 dimensions이 parallel computation에 사용되는지 알아야 합니다. (input, output 계산확인)
- Tutorial 2에서 parallelize될 수 있는 경우는 2가지였습니다.
1. N point can be parallelized
2. the interpolation of F features can be parallelized
- 따라서 현재 2개의 dimensions이 parallelized 될 수 있습니다.
- 이걸 알면, the number of threads를 정의할 수 있습니다.
- threads는 최대 3개의 dimensions을 가질 수 있습니다.
- Each dimension correspond to one of the dimension that your algorithm is doing parallel computation
- ***In other words, it also means that your algorithm can only have at most three dimensions for parallel computation.***
- If you algorithm has more than three dimensions for parallel computation, then you need to redesign it.

### To set the number of threads, there is a rule of thumb that is to set the total number of threads as 256.

- So if now we have two dimensions for parallel computation, we can "evenly" divide the threads into these dimensions.
- Of course you can experiment different numbers according to the balance of N and F and the computation complexity on different dimensions.
- But here I'll show example of "evenely distributed" threads.
- Since I want to divide 256 into two dimensions, both of them will have $\sqrt{256}=16$
- In our case, there are 16 threads in each dimension in total 256 threads.
  
  ![image](https://github.com/user-attachments/assets/35e1e655-276e-4aa1-a957-a683c37e7580)

- If you only have one dimension to do parallel computation, there is a simpler syntax, you can just write `const int threads = 256` or `const dim3 threads(256)`
  
  ```css
  const int threads = 256;
  ```

  ```css
  const dim3 threads(256);
  ```

- The dimension that we didn't specify is defaulted to 1. You can omit it.

  ```css
  const dim3 threads(16, 16);
  ```

  ```css
  const dim3 threads(16, 16, 1);
  ```
  

















감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 3 - English CC -](https://www.youtube.com/watch?v=-vV08i-Eifs&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=3)
