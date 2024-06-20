---
title: "[Rendering] 3D mesh load, Phong Shading, Rendering"
last_modified_at: 2024-06-20
categories:
  - Rendering
tags:
  - Rendering
  - 렌더링
  - Rasterization
  - 레스터화
  - 3D mesh
  - Phong Shading
  - pytorch3d
  - AmbientLights
  - RasterizationSettings
  - faces_per_pixel
  - MeshRenderer
  - MeshRasterizer
  - SoftPhongShader
  - BlendParams
  - SuGaR
  - 3dgs
excerpt: "SuGaR에서 3D mesh를 load하고 Phong Shading을 사용하여 Rendering하는 전체 프로세스"
use_math: true
classes: wide
---

https://github.com/Anttwo/SuGaR/blob/main/view_sugar_results.ipynb

### SuGaR에서 mesh를 load하고 Phong Shading을 사용하여 Rendering하는 전체 프로세스를 이해해봅시다.

본 ipynb 코드에서는 pytorch3d를 사용합니다.

```python
import os
import torch
import numpy as np
import matplotlib.pyplot as plt
import open3d as o3d
from pytorch3d.io import load_objs_as_meshes
from pytorch3d.renderer import (
    AmbientLights,
    RasterizationSettings, 
    MeshRenderer, 
    MeshRasterizer,  
    SoftPhongShader,
    )
from pytorch3d.renderer.blending import BlendParams
from sugar_scene.gs_model import GaussianSplattingWrapper
from sugar_scene.sugar_model import SuGaR, load_refined_model
from sugar_utils.spherical_harmonics import SH2RGB
```

먼저 각 pytorch3d의 구성요소의 의미를 이해해봅시다.

## Pytorch3D 구성 요소 해석

### AmbientLights
3D 씬에 대한 주변 조명을 정의합니다. 기본적으로 씬 전체에 균일한 조명을 적용하여 모든 객체가 일정한 밝기로 렌더링되도록 합니다.

```python
from pytorch3d.renderer import AmbientLights

# 3D 씬에 대한 주변 조명을 정의합니다.
# 기본적으로 씬 전체에 균일한 조명을 적용하여 모든 객체가 일정한 밝기로 렌더링되도록 합니다.
lights = AmbientLights()
```

### RasterizationSettings
메쉬를 래스터화하는 데 사용되는 설정을 정의합니다. 여기에는 이미지 크기, 픽셀 크기, 안티앨리어싱 등을 포함한 다양한 설정이 포함됩니다.
```python
from pytorch3d.renderer import RasterizationSettings

# 메쉬를 래스터화하는 데 사용되는 설정을 정의합니다.
# 여기에는 이미지 크기, 픽셀 크기, 안티앨리어싱 등을 포함한 다양한 설정이 포함됩니다.
raster_settings = RasterizationSettings(
    image_size=512, 
    blur_radius=0.0, 
    faces_per_pixel=1
)
```

### MeshRenderer, MeshRasterizer, SoftPhongShader
메쉬를 렌더링하기 위한 구성 요소입니다.

- MeshRenderer: 메쉬를 래스터화하고 셰이딩을 적용하는 데 사용되는 전체 렌더링 파이프라인입니다.
- MeshRasterizer: 메쉬를 래스터화하여 2D 이미지에 매핑합니다.
- SoftPhongShader: Phong 셰이딩 모델을 적용하여 매끄러운 표면을 렌더링합니다.

```python
from pytorch3d.renderer import MeshRenderer, MeshRasterizer, SoftPhongShader

# 메쉬를 렌더링하기 위한 구성 요소입니다.
# MeshRenderer: 메쉬를 래스터화하고 셰이딩을 적용하는 데 사용되는 전체 렌더링 파이프라인입니다.
# MeshRasterizer: 메쉬를 래스터화하여 2D 이미지에 매핑합니다.
# SoftPhongShader: Phong 셰이딩 모델을 적용하여 매끄러운 표면을 렌더링합니다.
rasterizer = MeshRasterizer(raster_settings=raster_settings)
shader = SoftPhongShader(device=device, lights=lights)

renderer = MeshRenderer(rasterizer=rasterizer, shader=shader)
```

### BlendParams
3D 객체를 렌더링할 때의 혼합 매개변수를 정의합니다. 주로 알파 블렌딩과 관련된 설정을 포함합니다.
```python
from pytorch3d.renderer.blending import BlendParams

# 3D 객체를 렌더링할 때의 혼합 매개변수를 정의합니다.
# 주로 알파 블렌딩과 관련된 설정을 포함합니다.
blend_params = BlendParams(sigma=1e-4, gamma=1e-4)
```

### 기타 모듈
- GaussianSplattingWrapper: 이는 sugar_scene.gs_model 모듈에서 가져온 클래스입니다. Gaussian splatting 기법을 사용하여 3D 데이터를 처리하는 래퍼로 추정됩니다.
- SuGaR 및 load_refined_model: sugar_scene.sugar_model 모듈에서 가져온 SuGaR 클래스와 load_refined_model 함수는 SuGaR 모델을 초기화하고 학습된 모델을 로드하는 데 사용됩니다.
- SH2RGB: sugar_utils.spherical_harmonics 모듈에서 가져온 함수로, 구면 조화 함수(Spherical Harmonics)를 RGB 값으로 변환하는 기능을 제공합니다.




