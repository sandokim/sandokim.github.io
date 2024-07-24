---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 6"
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
  - cuda tutorial 6
excerpt: "Pytorch c++/cuda extension tutorial 6"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 6

- Tutorial 5에서 trilinear interpolation을 forward pass로 구현했지만, 아직까진 backward pass는 못합니다.
- So if we want to calculate some loss based on this variable, it is not able to compute the correct gradient to update our features,

## backward pass를 implement하여 gradient가 back으로 passed 되도록 cuda code를 작성해보겠습니다.

### cuda backward implementation에서 가장 중요한 2가지 스텝은 다음과 같습니다.

- First, we need to compute all outputs' partial derivatives w.r.t. all trainable inputs.

![image](https://github.com/user-attachments/assets/decf0757-6b08-406f-938b-d58d0a2b4abd)

### 사용할 Loss를 $L$이라고 합시다.

trainable inputs인 $f_1$ ~ $f_4$의 미소 변화량과 Loss의 관계에서 미분인 $\frac{dL}{df_1}$, $\frac{dL}{df_2}$, $\frac{dL}{df_3}$, $\frac{dL}{df_4}$을 구합니다.

Since we already computed all partial derivaties of "all outputs" w.r.t. "all trainable inputs", we certainly can represent all these $dL/d_{something}$.

![image](https://github.com/user-attachments/assets/9e54fbf4-f3b3-4a61-bcc2-ab65b985384d)

This is why we did the $df/d_{inputs}$ in the first place.

![image](https://github.com/user-attachments/assets/febd6796-5c0f-4665-be3f-c10266217d98)

### Loss에 대해서 모든 inputs에 대한 $dL$, 즉, $\frac{dL}{df_1}$, $\frac{dL}{df_2}$, $\frac{dL}{df_3}$, $\frac{dL}{df_4}$을 정의하였으니까, backward pass를 코딩할 수 있습니다.

**For $\frac{dL}{d_{feats}}$, the original feats has shape (N,8,F), $\frac{dL}{d_{feats}}$ has the same shape (a tensor always has the same shape as its gradient).**
 
**tensor와 tensor의 gradient는 항상 같은 shape을 가짐을 기억합시다.**

### backward pass 미분계산 요약

- To calculate partial derivates, we imagine a virtual Loss $L$
- then there will be the partial derivates(s) of this $L$ w.r.t the output(s), in our case $\frac{dL}{f}$

  ![image](https://github.com/user-attachments/assets/da4aadd0-28d6-4ceb-a293-7b932d1ca5fb)

- We need to establish, from $\frac{dL}}{df}, the formulae of the partial derivates of $L$ w.r.t the inputs, so $\frac{dL}{df_1}$ ~ $\frac{dL}{df_4}$.
  - This can be achieved using chain rule and the partial derivates of the output(s) w.r.t the input(s).

    ![image](https://github.com/user-attachments/assets/ac491b9c-7ee5-4a73-97fe-eb43c50f4650)

- Once we have the forumula, last thing we need to do is to put the value one by one into our tensor.

  ![image](https://github.com/user-attachments/assets/8b81208f-ac08-451d-ba85-bc238e94a1fb)

### Backward pass code

- forward code를 copy & paste하고, fill in the partial derviates, change the input output tensors and their shape.

  ![image](https://github.com/user-attachments/assets/cdc001ee-f30a-4ffa-8adf-e32ebd984374)

- 그리고 fowrad function에서 해준 것처럼 `.cpp`, `.h` 파일들에 backward function을 선언해줍니다.

  ![image](https://github.com/user-attachments/assets/96f9e41f-cd34-4f36-b51f-cc6443de0ce0)

  ![image](https://github.com/user-attachments/assets/c702cc5e-0683-4e67-a420-52d6daa295e5)
  
- 마지막으로 `setup.py`의 current working dir에서 `pip install .`로 library를 build 해주는 것을 잊지 맙시다.

## Pytorch에서 `torch.autograd.Function`을 call하여 `fw`, `bw` functions을 wrap 해주어야 합니다.

![image](https://github.com/user-attachments/assets/cf3b8892-53c5-44f1-b2fe-66d4c9b2eb9f)

- `ctx`: the abbreviation of context, it is used to store some variables that we need for the backward pass.
- `ctx` is an argument that is always required and should be the first argument.

### forward pass
- intuitively we only need to return the output
- but since in the backward partial derivatives, we need both the points and feats variables (c.f. for formulae).
- in the formulae there are u,v which can only be calculated from points, so we need to save them.
  
  ![image](https://github.com/user-attachments/assets/c616ce64-ecbc-449b-843e-87a877dbfdb0)

  다음 코드로 `fw`에서 partial derivatives에 관련된 inputs (`feats`, `points`)를 저장합니다.
  
  ```css
  ctx.save_for_backward(feats, points)
  ```

### backward pass

![image](https://github.com/user-attachments/assets/e20f4d89-2449-4f28-9148-741ed0a97674)

- the following argument(s) is what we imagined earlier, $\frac{dL}{d_{output(s)}}$.
- 즉, backprop에 사용되는 Loss term $L$에 output `feat_interp`이 주는 미소 변화량 $\frac{dL}{dfeat \ interp}$를 backward pass에 넣습니다.

  ![image](https://github.com/user-attachments/assets/2250cc52-59ee-44ba-85ab-d9efcac3caff)

- If you have multiple outputs, your backward function will have as many arguments as your outputs, all with the form $\frac{dL}{d_{output}}.
- Loss term $L$과 관련된 outputs이 여러 개면, backward pass에 $L$에 대한 outputs의 미소 변화량들을 모두 넣어줍니다.

backward pass에서는 먼저 forward pass에서 저장된 tensors들을 가져옵니다.

![image](https://github.com/user-attachments/assets/fb03a50a-6323-4f8f-8744-da7747d7a3f3)

- backward pass에 inputs으로 작성한 3개의 inputs을 넣어주어 $\frac{dL}{d_{feats}}$인 the partial derivatives of the loss w.r.t the inputs을 얻습니다.

- backward pass에서 return하는 value는 forward pass의 inputs의 수와 같습니다.
- `feats`, `points`가 input인데, backward pass에서 return 되는 value는 partial derivative of the loss w.r.t that input 입니다.
- Because the first input ins `feats`, the first return value is $\frac{dL}{d_{feats}}$.
- Why is the second None? That's because we said that in the problem definition, we assume that the points are fixed, so never changed. So the None here means we won't have any gradient for the second input, `points`.

  ![image](https://github.com/user-attachments/assets/17d136b6-60a0-4411-a9e3-40cbcb212790)


### 이제 backward pass 까지 `required_grad = True`가 되는지 확인해봅시다.

![image](https://github.com/user-attachments/assets/0d067646-3f6b-44e9-a940-c03f2caaf655)

- `Trilinear_interpolation_cuda.apply(feats2, points)`는 forward pass와 backward pass를 `torch.autograd.Function`로 상속받은 class로 구현한 함수 입니다.
- `trilinear_interpolation_py(feats, points)`는 pytorch로 구현한 함수입니다.
  
- 둘의 값 차이는 없음을 `torch.allclose(out_py, out_cuda)`로 확인했습니다.
  - `out_py`는 pytorch로 구현한 함수의 output
  - `out_cuda`는 cuda로 forward pass, backward pass를 구현한 함수의 output

- 속도에서 forward에는 pytoch나 cuda나 거의 속도 차이가 없지만, backward pass에서 cuda가 pytorch보다 10배 빠름을 확인했습니다.

![image](https://github.com/user-attachments/assets/dc4f7af2-8d1a-4205-bebb-a0cd69c5ef90)


감사합니다.

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 6 - English CC -](https://www.youtube.com/watch?v=oG0WUq3bRz0&list=PLDV2CyUo4q-LKuiNltBqCKdO9GH4SS_ec&index=6)
