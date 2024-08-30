---
title: "[CUDA] CUDA_HOME environment variable is not set. Please set it to your CUDA install root"
last_modified_at: 2024-07-22
categories:
  - CUDA
tags:
  - git
  - github
  - CUDA
  - subprocess-exited-with-error
  - diff-gaussian-rasterization-depth
  - end of output
  - This error originates from a subprocess, and is likely not a problem with pip
  - pip install -e .
  - pip install --no-build-isolation -e .
  - .cu 설치
  - c++
excerpt: "CUDA_HOME error"
use_math: true
classes: wide
comments: true
---

## OSError: CUDA_HOME environment variable is not set. Please set it to your CUDA install root.

![image](https://github.com/user-attachments/assets/eb79be71-8b5f-4312-a9aa-eda841991f3c)

에러의 원인은 pytorch가 gpu가 아닌 cpu로 (재)설치 되었을 수 있습니다.

`torch.cuda.is_available()`이 `False`이므로 pytorch가 cpu 버전으로 깔렸음을 알 수 있습니다.

```cmd
python
Python 3.8.6 | packaged by conda-forge | (default, Jan 25 2021, 23:21:18) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import torch
>>> torch.cuda.is_available()
False
>>> print(torch.__version__)
2.4.0
```

`conda list`로 확인한 결과 torch 관련 모듈이 전부 cpu인 것을 확인할 수 있습니다.

```cmd
conda list

# Name                    Version                   Build    Channel
...
pytorch                   2.4.0               py3.8_cpu_0    pytorch
...
torchaudio                2.4.0                  py38_cpu    pytorch
torchvision               0.19.0                 py38_cpu    pytorch
```

`conda uninstall pytorch`이후 `nvcc -V`로 확인한 cuda 버전과 맞는 torch를 설치해줍니다.

```cmd
nvcc -V
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2022 NVIDIA Corporation
Built on Wed_Sep_21_10:33:58_PDT_2022
Cuda compilation tools, release 11.8, V11.8.89
Build cuda_11.8.r11.8/compiler.31833905_0
```

```cmd
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia
```

다시 `pip install -e .`를 하여 제대로 `.cu` 관련 submodule을 설치 완료하였습니다.

```cmd
pip install -e .
Obtaining file:///mai_nas/KHS/SoSC/submodules/diff-gaussian-rasterization-depth
  Preparing metadata (setup.py) ... done
Installing collected packages: diff_gaussian_rasterization_depth
  Running setup.py develop for diff_gaussian_rasterization_depth
Successfully installed diff_gaussian_rasterization_depth-0.0.0
```

