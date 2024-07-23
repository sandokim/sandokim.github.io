---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 1"
last_modified_at: 2024-07-23
categories:
  - CUDA
tags:
  - cuda programming
  - cuda
  - forward
  - backward
  - c++/cuda
  - cuda extension
  - cuda tutorial 1
excerpt: "Pytorch c++/cuda extension tutorial 1"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial

CUDA is a programming language for GPU, so it enables faster parallel computation, thus making pytorch faster.

![image](https://github.com/user-attachments/assets/1dc62636-0a44-4772-ae17-7ed120a1db29)

CUDA does parallel computation and return the output to pytorch.

So the actual important part is on cuda, not c++.

c++ is only a bridge that connect pytorch and cuda.

## 본 tutorial 1에서는 NeRF의 volume rendering을 쉬운 버전으로 만들어볼 것입니다.

- trilinear interpolation으로 8 vertices로부터 feature f를 얻는 것을 구현할 것입니다.

![image](https://github.com/user-attachments/assets/7537f3cc-c49b-4806-b70a-85df1bfd4099)

![image](https://github.com/user-attachments/assets/7d417c2f-5267-4fda-a299-9387da733f42)

- tutorial 1에서는 cuda없이 c++ bridge만 써볼 것입니다.
- vscode에서 conda로 python 가상환경을 만듭니다.
- interpolation.cpp 파일을 만들고, c++ bridge를 여기에 작성해서 pytorch와 cuda를 연결할 것입니다.
  
  ![image](https://github.com/user-attachments/assets/dc1da146-5f0c-4a20-9bf7-a0b8e82310a2)

- code가 pytorch로부터 tensors를 받으므로, c++가 tensor가 뭔지 알게 하도록 하기 위해, `#include <torch/extension.h>`를 작성해줍니다.
  - 이때 빨간줄이 나오는데, `ctrl+shift+p`를 누르고 C/C++: Edit Configurations (UI)를 선택해주어, 빨간줄을 없앱니다.

    ![image](https://github.com/user-attachments/assets/a51f3578-c241-40b8-bce1-b75aefaf9ed9)
    
  - `C/C++: Edit Configuration (JSON)`을 선택하고 `includePath`에 python과 pytorch path를 conda environment에 경로로 추가해줍니다.
    
    ![image](https://github.com/user-attachments/assets/dade4568-2014-4e7a-9c9b-d9b9766d9749)

- 파일을 저장하면 `#include <torch/extension.h>`의 빨간 밑줄이 사라졌습니다.

![image](https://github.com/user-attachments/assets/b9b54b95-35b7-4ba7-80c4-9113d8330c43)

### tutorial 1에서는 `placeholder`만 작성할 것입니다. 왜냐하면 실제 computation은 cpp 파일이 아니라 cuda 파일에서 실행되기 때문입니다.

- ***따라서 cpp 파일에서는 function name, input and output만 작성하면 됩니다.***

![image](https://github.com/user-attachments/assets/7faac12c-ca66-418b-a1af-8b24cc04b02f)

- trilinear interpolation으로 function name과 input인 feats, point와 output인 return feats를 Tensor로 아래와 같이 작성합니다.

![image](https://github.com/user-attachments/assets/797b3312-d898-454d-b087-5003afa2e405)

![image](https://github.com/user-attachments/assets/c6d96b65-9c37-47d6-ac57-0c3988f77981)

- c++은 cuda execution을 call하기 위한 함수를 정의합니다.
- c++은 python을 call하기 위한 interface를 제공합니다. (이것이 cpp에서 정의한 함수를 python에서 call할 수 있는 이유입니다.)
  
### 만약 python에서 c++를 call해봤다면, pybind라는 library를 아실텐데, 이 library는 python으로부터 c++ code를 불러오는 방법을 제공합니다. 

이 pybind라는 library는 pytorch와 관련될 필요는 없습니다. 예를 들어, opencv code가 사용될 수도 있습니다.

***우리가 할일은 이런 pybind interface를 사용해 python에서 c++ 함수를 call할 수 있도록 방법을 제공해주는 것입니다.***

- 첫번째 인자는 python으로부터 불려질 function name: `"trilinear_interpolation"`
- 두번째 인자는 불려진 c++ function인 `trilinear_interpolation`입니다.
- 아래처럼 PYBIND11_MODULE로 첫번째 인자, 두번째 인자를 넣어주면 c++ bridge를 성공적으로 만든 것입니다.
  
![image](https://github.com/user-attachments/assets/1a1797e9-c9ba-45ba-830f-1abcf6fc627d)

  




### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 1 - English CC -](https://youtu.be/l_Rpk6CRJYI?si=VUe9psNzk60F7iO6&t=478)
