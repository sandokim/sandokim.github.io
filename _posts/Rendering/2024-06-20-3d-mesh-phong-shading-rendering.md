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


