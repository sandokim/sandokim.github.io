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
  - forward pass
  - backward pass
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

## computation을 하기에 앞서 blocks과 threads의 size를 알아야 합니다.

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
  

## 이제 아까 넘어갔던 the number of blocks는 어떻게 계산하는지 알아봅시다.

- 이 컨셉은 매우 중요하므로 100% 이해해야합니다.
- N=20, F=10 일때, 20 points를 interpolate 해야하고, each point는 10개의 features를 가집니다.
- output shape은 (20, 10)이 됩니다.
- **우리는 threads가 16x16이라고 했습니다.**
- **thread가 cover하는 region은 다음 그림과 같습니다.**

  ![image](https://github.com/user-attachments/assets/203c840f-7afa-4bd9-b06f-2a6722126e7f)

- 지금 그림에서는 하나의 차원은 threads로 fully covered 되고, 나머지 하나는 아닙니다.

  ![image](https://github.com/user-attachments/assets/f43e67d2-7a89-40c2-a4f3-025319d890ef)

### the principle to compute the number of blocks is that we want to tile "threads" so that all of them cover all output elements

- In order to cover all elements, there is a formula to do this based on the shape of inputs and threads
- In our example, since x axis is already covered by 1 "threads", so we need 1 block along this dimension.
- As for y-axis, we need another (16x16) "threads" to cover all the elements, therefore we need 2 blocks along this dimension
- 즉 block size (2, 1)이면 모든 elements를 cover할 수 있습니다.

  ![image](https://github.com/user-attachments/assets/64bee9c1-03d5-4a33-8254-40ea719426b4)

- the number of blocks을 계산하는 건 되게 이처럼 생각보다 되게 쉽고, 그냥 식을 copy & paste 하면 해결됩니다.

  ![image](https://github.com/user-attachments/assets/7fb48f68-3124-4ee9-b26d-5b6a2d177002)

### Naturally, you can imagine how parallel computation works: each output element is computed by the thread that covers that place.

- i.e. 좌측상단 맨 첫번째 element는 first block의 first thread에 의해 계산됩니다.

  ![image](https://github.com/user-attachments/assets/f93ad9d8-badd-469c-83c0-b754c5a08b75)

- **the number of threads와 blocks를 가지게 되면, 우리는 그림처럼 kernel을 launch할 수 있습니다.**

  ![image](https://github.com/user-attachments/assets/9ca226ad-bad1-4aff-8c7a-e443d98cbfaa)


### Launch a kernel

- `FLOATING_TYPES`는 kenerl 이 floating type operations을 할 것이라는 의미입니다.
- `FLOATING`은 float32, float64를 포함합니다. default pytorch floating은 float32이지만, higher precision computation을 원하면 float64도 가능합니다.
- float16으로 speed up하고 reduce memory usage를 줄이기도 하는데, `AS_DISPATCH_FLOATING_TYPES_HALF`로 수행할 수 있습니다.

![image](https://github.com/user-attachments/assets/cdb72bce-817b-4b69-b965-68f4484c35a0)

- `AT_DISPATCH_FLOATING_TYPES`에서 첫번째 argument는 operate하고 싶은 data type입니다.
  - `feats.type()`
  - input인 feats의 data type으로 feats.type()을 넣어줍니다.
  - "feats"는 float32이므로 이 kernel은 float32 operation을 수행합니다.

- `AT_DISPATCH_FLOATING_TYPES`에서 두번째 argument는 string인데, 기본적으로 cuda function의 이름을 여기에 써줍니다.
  - `"trilinear_fw_cu"`
  - 이 string은 indicator로서 사용되며, kernel launch fails일 때, 이 이름과 함계 error를 띄웁니다.
  - 이로써 어떤 function이 fails했는지 알 수 있습니다.
  - 따라서 cuda function 이름과 같게 써주면, 어떤 function이 fails 했는지 빠르게 알 수 있습니다.

- `AT_DISPATCH_FLOATING_TYPES`에서 나머지 이어지는 부분은 kernel을 실제로 launch하는 코드입니다.

  ![image](https://github.com/user-attachments/assets/9ee701de-dfae-430a-a9bb-61178782f34e)

  - To launch a kernel, first we define a kernel name. `trilinear_fw_kernel`
  - `trilinear_fw_kernel`의 argments는 3개의 inputs을 받습니다
    - 2개는 cuda function inputs (feats, points)이고
    - 1개는 우리가 채우고 싶은 output (feat_interp)입니다.
    - 위 3개 다 kernel inputs으로 넣으면, `trilinear_fw_kernel`이 trilinear interpolation을 수행합니다.
    - kenrel takes the inputs, does the interpolation, and fill the outputs to the output tensor.

감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 3 - English CC -](https://www.youtube.com/watch?v=-vV08i-Eifs&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=3)
