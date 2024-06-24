---
title: "[Mesh] Surface Reconstruction, multi-view depth maps and normal maps, screened Poisson Reconstruction"
last_modified_at: 2024-06-24
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - surface reconstruction
  - multi-view
  - depth maps
  - normal maps
  - screened Poisson Reconstruction
  - Gaussian Surfels
excerpt: "Surface Reconstruction, multi-view depth maps and normal maps, screened Poisson Reconstruction"
use_math: true
classes: wide
---

# Multi-view Depth Maps and Normal Maps Rendering and Fusion

[High-quality Surface Reconstruction using Gaussian Surfels](https://arxiv.org/abs/2404.17774)에서 multi-view depth maps과 normal maps을 render하고, 그들을 fuse하여 screened Poisson Reconstruction에 넣는다는 표현이 나옵니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5f57748b-b7e7-497c-8415-c3558585e7c9)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/17c89c49-adec-4b48-8849-5e1c956f3021)

위 내용을 해석하면 다음과 같습니다.

### 1. Multi-view Depth Maps과 Normal Maps을 Render한다
- **Multi-view Depth Maps**: 여러 각도에서 본 깊이 맵(depth map)을 생성합니다. 깊이 맵은 각 픽셀의 깊이 값을 나타내며, 객체와 카메라 사이의 거리를 나타냅니다.
- **Normal Maps**: 표면의 각 점에서의 법선 벡터(normal vector)를 나타내는 맵을 생성합니다. 이는 객체의 표면 방향을 나타내며, 조명 계산 등에 사용됩니다.

### 2. Render한 Depth Maps과 Normal Maps을 Fuse한다
- 여기서는 여러 각도에서 얻은 깊이 맵과 법선 맵을 결합(fuse)하여 하나의 통합된 표현을 만듭니다. 이는 각각의 깊이 맵과 법선 맵에서 얻은 정보를 종합하여 더 정확한 3D 구조를 얻기 위함입니다.

### 3. Screened Poisson Reconstruction에 넣는다
- Kazhdan과 Hoppe(2013)의 방법을 사용하여, 결합된 깊이 맵과 법선 맵을 바탕으로 고품질의 글로벌 메쉬(global mesh)를 추출합니다.
- Screened Poisson Reconstruction은 주어진 입력 데이터(깊이 맵과 법선 맵)를 기반으로 연속적이고 부드러운 3D 메쉬를 생성하는 알고리즘입니다.

## 정리

전체 과정은 최적화 후 여러 각도에서 깊이 맵과 법선 맵을 렌더링하고, 이를 결합(fuse)한 다음, 결합된 데이터를 Screened Poisson Reconstruction 알고리즘에 입력하여 고품질의 글로벌 메쉬를 추출하는 것입니다.

# Screened Poisson Reconstruction에 대해서 좀 더 알아봅시다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1499385a-7b9a-4829-a142-8861cce8adae)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/738a6fb5-ca37-4ccb-b402-baab65badf77)


### Reference
[Screened Poisson Surface Reconstruction](https://www.cs.jhu.edu/~misha/MyPapers/ToG13.pdf)
