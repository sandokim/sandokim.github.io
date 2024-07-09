---
title: "[3D CV 연구] 3DGS submodules installation"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - submodules
  - diff-gaussian-rasterization
  - simple-knn
excerpt: "3DGS submodules 설치방법"
use_math: true
classes: wide
comments: true
---

submodules에 대한 directory가 있다고 해서 그것이 설치가 되었다는 걸 의미하지는 않습니다. 

submodules 폴더 내로 경로를 이동하여 python setup.py install을 해서 설치합니다.

만약 submodules 폴더가 비어있다면, --recursive flag를 사용하지 않아서 일 것입니다. submodules의 내용까지 제대로 clone하기 위해서는 다음을 terminal 창에서 실행해줍니다.

```python
git submodule update --init --recursive
```

가상환경을 만들고 submodules까지 전체를 설치하는 방법은 다음과 같습니다.

```python
conda activate gaussian_splatting
cd <dir_to_repo>/gaussian-splatting
git submodule update --init --recursive
pip install submodules\diff-gaussian-rasterization
pip install submodules\simple-knn
```

https://github.com/graphdeco-inria/gaussian-splatting/issues/137

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/660d5c50-466e-4aea-99eb-6be104e78460)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a17bf400-dfad-4eee-b8ee-4acefb129cfe)


