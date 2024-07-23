---
title: "[CUDA] Pytorch c++/cuda extenstion tutorial 1"
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
  - cuda tutorial 1
excerpt: "Pytorch c++/cuda extension tutorial 1"
use_math: true
classes: wide
comments: true
---

# CUDA Programming Tutorial 1

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

### c++에서 작성한 이 함수를 python에서 어떻게 불러오는지 해봅시다.

- ***c++에서 작성한 함수를 불러오기 위해서는 작성한 c++ 코드를 먼저 `build` 해야합니다.***
- c++는 다른 곳에서 call 되기 위해서는 compiling과 building을 요구합니다.
- `setup.py`로 c++ 코드를 compiling과 building을 할 수 있습니다.

![image](https://github.com/user-attachments/assets/030aab50-357d-4b1a-8a16-03b909127947)

![image](https://github.com/user-attachments/assets/767d4ff1-c27c-424b-b596-e27db8028c2c)

### `setup.py`에서 c++ 코드가 어떻게 bulit 될 것인지 정의합니다.

- 아래 내용을 copy and paste해서 수정하면 됩니다.
- `name`은 package의 이름을 설정하는 것이고, 나머지는 부가적입니다.
- 가장 중요한 부분은 `ext_modules`입니다.
  - 먼저 build하고 싶은 c++ 코드를 sources의 리스트 안에 써줍니다. (여러개 있으면, 콤마로 이어서 다른 cpp 파일도 써줍니다.)
    
    ![image](https://github.com/user-attachments/assets/63ce2152-8e77-49d1-b781-2bfb74eb0511)

  - 마지막으로 `cmdclass`는 우리가 code를 building한 다는 것을 알려주는 부분입니다.

### 마지막으로 pip version을 `python -m pip install pip -U`로 업그레이드하고, `setup.py`로 c++ 코드를 build 해봅시다.

- path는 `setup.py`가 위치한 곳입니다. 우리는 현재 current folder에 `setup.py`가 위치하므로 그냥 현재위치를 나타내는 `.`을 path로 넣어주면 됩니다.
- `pip install .`을 입력하면 시간이 좀 걸리면서 build가 완료됩니다.

![image](https://github.com/user-attachments/assets/866e768a-1c28-40cd-871e-7280360f0053)

### `setup.py`로 c++ 파일이 build가 완료되면 python test.py 파일에서 불러와서 함수를 사용해봅시다.

- cppcuda_tutorial에서 torch를 import했으므로 torch를 import 안해도 될 거라 생각할 수 있지만, torch부터 import를 해줘야 합니다.
- 만약 그렇지 않으면 우리가 c++ 코드로 짠 custom package가 import되지 않습니다.
- 직접 짠 `cppcuda_tutorial` c++ 코드에서 `trilinear_interpolation` 함수를 불러와서 python `test.py`에서 사용하여 결과까지 성공적으로 도출하였습니다.

![image](https://github.com/user-attachments/assets/f3652d69-9c72-46ba-a038-22307f24eb6d)

감사합니다.

# 실전 에제

- [`diff-gaussian-rasterization/ext.cpp`](https://github.com/graphdeco-inria/diff-gaussian-rasterization/blob/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d/ext.cpp)에서 c++ bridge는 다음과 같이 작성하였습니다.
- 기본적인 `#include <torch/extension.h>`로 torch가 무엇인지 cpp 파일에게 알려줍니다.
- [`rasterize_points.h`](https://github.com/graphdeco-inria/diff-gaussian-rasterization/blob/59f5f77e3ddbac3ed9db93ec2cfe99ed6c5d121d/rasterize_points.h)에 c++ 코드로 function name, input, output에 대한 정의를 합니다.
  
  ```cpp
    /*
   * Copyright (C) 2023, Inria
   * GRAPHDECO research group, https://team.inria.fr/graphdeco
   * All rights reserved.
   *
   * This software is free for non-commercial, research and evaluation use 
   * under the terms of the LICENSE.md file.
   *
   * For inquiries contact  george.drettakis@inria.fr
   */
  
  #pragma once
  #include <torch/extension.h>
  #include <cstdio>
  #include <tuple>
  #include <string>
  	
  std::tuple<int, torch::Tensor, torch::Tensor, torch::Tensor, torch::Tensor, torch::Tensor>
  RasterizeGaussiansCUDA(
  	const torch::Tensor& background,
  	const torch::Tensor& means3D,
      const torch::Tensor& colors,
      const torch::Tensor& opacity,
  	const torch::Tensor& scales,
  	const torch::Tensor& rotations,
  	const float scale_modifier,
  	const torch::Tensor& cov3D_precomp,
  	const torch::Tensor& viewmatrix,
  	const torch::Tensor& projmatrix,
  	const float tan_fovx, 
  	const float tan_fovy,
      const int image_height,
      const int image_width,
  	const torch::Tensor& sh,
  	const int degree,
  	const torch::Tensor& campos,
  	const bool prefiltered,
  	const bool debug);
  
  std::tuple<torch::Tensor, torch::Tensor, torch::Tensor, torch::Tensor, torch::Tensor, torch::Tensor, torch::Tensor, torch::Tensor>
   RasterizeGaussiansBackwardCUDA(
   	const torch::Tensor& background,
  	const torch::Tensor& means3D,
  	const torch::Tensor& radii,
      const torch::Tensor& colors,
  	const torch::Tensor& scales,
  	const torch::Tensor& rotations,
  	const float scale_modifier,
  	const torch::Tensor& cov3D_precomp,
  	const torch::Tensor& viewmatrix,
      const torch::Tensor& projmatrix,
  	const float tan_fovx, 
  	const float tan_fovy,
      const torch::Tensor& dL_dout_color,
  	const torch::Tensor& sh,
  	const int degree,
  	const torch::Tensor& campos,
  	const torch::Tensor& geomBuffer,
  	const int R,
  	const torch::Tensor& binningBuffer,
  	const torch::Tensor& imageBuffer,
  	const bool debug);
  		
  torch::Tensor markVisible(
  		torch::Tensor& means3D,
  		torch::Tensor& viewmatrix,
  		torch::Tensor& projmatrix);
  ```
  
- `ext.cpp`파일을 `setup.py`로 `pip install .`로 build하면 이제 python 파일에서 CUDA로 작성한 함수를 import하여 사용가능합니다.
  - CUDA로 작성한 `RasterizeGaussianCUDA`는 python 파일에서 `rasterize_gaussians`로 함수로 불러 사용합니다.
  - CUDA로 작성한 `RasterizeGaussiansBackwardCUDA`는 python 파일에서 `rasterize_gaussians_backward`로 함수로 불러 사용합니다.
  - CUDA로 작성한 `markVisible`는 python 파일에서 `mark_visible`로 함수로 불러 사용합니다.

  ```cpp
  /*
   * Copyright (C) 2023, Inria
   * GRAPHDECO research group, https://team.inria.fr/graphdeco
   * All rights reserved.
   *
   * This software is free for non-commercial, research and evaluation use 
   * under the terms of the LICENSE.md file.
   *
   * For inquiries contact  george.drettakis@inria.fr
   */
  
  #include <torch/extension.h>
  #include "rasterize_points.h"
  
  PYBIND11_MODULE(TORCH_EXTENSION_NAME, m) {
    m.def("rasterize_gaussians", &RasterizeGaussiansCUDA);
    m.def("rasterize_gaussians_backward", &RasterizeGaussiansBackwardCUDA);
    m.def("mark_visible", &markVisible);
  }
  ```

### Reference
[Pytorch+cpp/cuda extension 教學 tutorial 1 - English CC -](https://youtu.be/l_Rpk6CRJYI?si=VUe9psNzk60F7iO6&t=478)
