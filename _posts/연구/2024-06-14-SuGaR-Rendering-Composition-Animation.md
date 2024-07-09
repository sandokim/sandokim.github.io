---
title: "[3D CV 연구] 3DGS SuGaR Rendering, composition and animation"
last_modified_at: 2024-06-14
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - rendering, composition and animation
excerpt: "3DGS SuGaR Rendering, composition and animation"
use_math: true
classes: wide
comments: true
---

## Rendering, Composition, and Animation in SuGaR

### 1. Rendering a Scene
- `view_sugar_results.ipynb` notebook과 `metrics.py` 스크립트는 하이브리드 표현과 Gaussian Splatting rasterizer를 사용하여 장면을 렌더링하는 방법의 예시를 제공합니다.
자세한 내용은 곧 추가될 예정입니다.

### 2. Composition and Animation Data from Blender
- Exporting from Blender: `blender` directory는 Blender에서 수정하거나 애니메이션한 SuGaR meshes의 composition 및 animation 데이터를 내보내는 여러 Python 스크립트가 포함되어 있습니다.
- `sugar_scene/sugar_compositor.py` 스크립트에는 이러한 애니메이션 또는 composition 데이터를 PyTorch로 가져와 SuGaR 하이브리드 표현에 적용할 수 있는 Python 클래스가 포함되어 있습니다.
  
### 3. High-Quality Rendering with Hybrid Representation
- Hybrid representation: 하이브리드 표현을 통해 Gaussian Splatting rasterizer를 사용하여 고품질의 장면 렌더링이 가능합니다.
- 아래 예시를 참고하세요.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d000f24d-51df-436f-9fbf-8edb6cdc956e)

### 4. Future Tutorial
- 이러한 스크립트와 클래스를 사용하는 방법이 약간 까다로울 수 있습니다. 사용법에 대한 자세한 튜토리얼이 곧 추가될 예정입니다.





