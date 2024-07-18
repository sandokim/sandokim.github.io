---
title: "[Git] pip install -r requirements.txt"
last_modified_at: 2024-07-09
categories:
  - Git
tags:
  - git
  - github
  - requirements.txt
  - pip install -r requirements.txt
  - torch
  - torch version
  - pytorch
  - pytorch lightning
excerpt: "requirements.txt를 설치할 때, 미리 설치한 torch가 uninstall되버리고 다른 버전의 torch로 설치되는 문제 해결법"
use_math: true
classes: wide
comments: true
---

# pip install -r requirements.txt로 설치할 때, library의 version들에 따라서 torch가 저절로 uninstall 되고 다른 torch version으로 재설치가 되는 경우가 있습니다.

![image](https://github.com/user-attachments/assets/5f92c7a4-7211-4e85-9e6f-c75e0b01670f)

# 위의 에러는 CUDA 11.8에 맞게 pytorch를 먼저 설치하고, requirments.txt에서 torch 관련 주석을 모두 지우고 깔았을 때 발생한 현상입니다.

```python
conda create -n omnidata -y python=3.8
conda activate -n omnidata
conda install pytorch torchvision torchaudio pytorch-cuda=11.8 -c pytorch -c nvidia # torch==2.3.1
pip install pytorch_lightning==2.3.1 
```

- 맨 처음 `requirements.txt`를 보시면 `torch==1.9.0`, `torchvision==0.10.0`, `pytorch_lightning==1.1.8`로 되어 있는데

```python
numpy==1.19.5
pillow==8.3.2
pyyaml==5.3.1
torch==1.9.0
torchvision==0.10.0
pytorch_lightning==1.1.8
einops==0.3.2
matplotlib==3.3.4
joblib==1.1.0
pandas==1.1.5
h5py==3.1.0
scipy==1.5.4
seaborn==0.11.2
kornia==0.5.11
timm==0.4.12
```

- 앞서 pytorch를 현재 서버에 깔려있는 CUDA version 11.8에 맞게 먼저 설치해주었으므로, torch 관련된 항목을 주석처리하고 설치해보았습니다.
 
```python
numpy==1.19.5
pillow==8.3.2
pyyaml==5.3.1
# torch==1.9.0
# torchvision==0.10.0
# pytorch_lightning==1.1.8
einops==0.3.2
matplotlib==3.3.4
joblib==1.1.0
pandas==1.1.5
h5py==3.1.0
scipy==1.5.4
seaborn==0.11.2
kornia==0.5.11
timm==0.4.12
```

```python
pip install -r requirements.txt
```

- 그러나 이때, 나머지 라이브러리 중에 CUDA 11.8에 맞게 깔린 torch 2.3.1 버전과 충돌하는 것이 존재한다면, torch를 uninstall하고 충돌하는 라이브러리에 compatible하게 torch version을 바꿔서 재설치해버리게 됩니다.
  
![image](https://github.com/user-attachments/assets/5f92c7a4-7211-4e85-9e6f-c75e0b01670f)

### 이런 문제를 해결하기 위해서는, requirements.txt에서 torch 버전을 CUDA 11.8에 맞는 버전으로 설정하고, 나머지 라이브러리들의 버전에 대한 정보를 주석처리합니다.

- 이를 통해 CUDA 11.8에 맞는 torch 2.3.1 버전과 compatible한 라이브러리들을 자동으로 설치하게 됩니다.

```python
numpy # default 1.19.5
pillow # default 8.3.2
pyyaml # default 5.3.1
torch==2.3.1 # default 1.9.0
# torchvision==0.10.0
# pytorch_lightning==1.1.8
einops # default 0.3.2
matplotlib # default 3.3.4
joblib # default 1.1.0
pandas # default 1.1.5
h5py # default 3.1.0
scipy # default 1.5.4
seaborn # default 0.11.2
kornia # default 0.5.11
timm # default 0.4.12
```

![image](https://github.com/user-attachments/assets/561e8b3e-bbcc-4c30-8abf-723b76443c01)

### normal을 추정하는 omnidata에 대한 가상환경을 위처럼 설치하여서, 제대로 동작함을 확인하였습니다.

![image](https://github.com/user-attachments/assets/be7f6e6f-df9c-4a70-a501-327f85200d8b)



### Reference
- https://github.com/EPFL-VILAB/omnidata
- https://github.com/EPFL-VILAB/omnidata/tree/main/omnidata_tools/torch#pretrained-models
