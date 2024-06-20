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

-----

### 이제 SuGaR에서 3D mesh를 load하고 Phong Shading을 사용하여 Rendering하는 전체 프로세스를 이해해봅시다. 

전체 코드는 건너뛰고, Traditional Color Texture를 사용한 Rendering 부분에 대해서 이해해봅시다.

### 전통적인 컬러 텍스처를 사용한 렌더링

전통적인 컬러 텍스처를 사용한 렌더링은 3D 메시의 표면에 색상을 적용하여 시각적으로 더 현실감 있는 이미지를 생성하는 과정입니다. 이 과정은 다음 단계로 나눌 수 있습니다:

1. **메시 로드**: 3D 모델을 파일에서 불러옵니다. 예를 들어, `load_objs_as_meshes` 함수를 사용하여 `.obj` 파일 형식의 메시를 로드합니다.
2. **텍스처 매핑**: 로드된 메시에 색상 정보를 매핑합니다. 텍스처 매핑은 메시의 각 폴리곤에 색상을 할당하여 메시의 표면에 그림이나 패턴을 입히는 과정입니다.
3. **렌더 설정**: 렌더링 설정을 정의합니다. 예를 들어, 카메라 위치, 조명 설정, 레스터 설정 등을 포함합니다.
4. **렌더링**: 설정된 파라미터를 바탕으로 메시를 렌더링합니다. 이 과정에서 메시의 표면에 적용된 텍스처가 고려되어 최종 이미지를 생성합니다.
5. **시각화**: 렌더링된 이미지를 화면에 표시합니다. `matplotlib` 라이브러리를 사용하여 이미지를 시각화할 수 있습니다.

- `faces_per_pixel`은 픽셀당 렌더링할 폴리곤의 최대 개수를 의미합니다. 이 파라미터는 렌더링 시각화의 정밀도와 직접적으로 관련이 있습니다. 예를 들어, 높은 값으로 설정하면 픽셀당 더 많은 폴리곤을 고려하여 렌더링하게 되며, 이는 더 세밀한 렌더링 결과를 제공합니다. 반대로, 낮은 값으로 설정하면 픽셀당 고려되는 폴리곤의 수가 줄어들어 렌더링 속도는 빨라지지만 정밀도가 떨어질 수 있습니다.
- `max_faces_per_bin`은 레스터화 과정에서 각 "bin"에 포함될 수 있는 최대 폴리곤 수를 설정합니다. "Bin"은 렌더링 공간을 작은 영역으로 나눈 것입니다. 이 파라미터는 렌더링 메모리 사용량과 성능에 영향을 미칩니다. 높은 값으로 설정하면 더 많은 폴리곤이 한 번에 처리되어 렌더링 정확도가 높아질 수 있지만, 메모리 사용량이 증가할 수 있습니다.

```python
refined_mesh_path = None

if refined_mesh_path is None:
    post_processed = False
    if post_processed:
        post_processed_str = '_postprocessed'
    else:
        post_processed_str = ''

    scene_name = refined_sugar_path.split('/')[-3]
    refined_mesh_dir = './output/refined_mesh'
    refined_mesh_path = os.path.join(
        refined_mesh_dir, scene_name,
        refined_sugar_path.split('/')[-2].split('.')[0] + '.obj'
    )

print(f"Loading refined mesh from {refined_mesh_path}, this could take a minute...")
textured_mesh = load_objs_as_meshes([refined_mesh_path]).to(nerfmodel.device)
print(f"Loaded textured mesh with {len(textured_mesh.verts_list()[0])} vertices and {len(textured_mesh.faces_list()[0])} faces.")
```

### Mesh Rendering
```python
cam_idx = np.random.randint(0, len(cameras_to_use))

faces_per_pixel = 1
max_faces_per_bin = 50_000

mesh_raster_settings = RasterizationSettings(
    image_size=(refined_sugar.image_height, refined_sugar.image_width),
    blur_radius=0.0, 
    faces_per_pixel=faces_per_pixel,
)
lights = AmbientLights(device=nerfmodel.device)
rasterizer = MeshRasterizer(
        cameras=cameras_to_use.p3d_cameras[cam_idx], 
        raster_settings=mesh_raster_settings,
    )
renderer = MeshRenderer(
    rasterizer=rasterizer,
    shader=SoftPhongShader(
        device=refined_sugar.device, 
        cameras=cameras_to_use.p3d_cameras[cam_idx],
        lights=lights,
        blend_params=BlendParams(background_color=(1.0, 1.0, 1.0)),
    )
)

with torch.no_grad():
    print("Rendering image", cam_idx)
    print("Image ID:", cameras_to_use.gs_cameras[cam_idx].image_name)

    p3d_cameras = cameras_to_use.p3d_cameras[cam_idx]
    rgb_img = renderer(textured_mesh, cameras=p3d_cameras)[0, ..., :3]

plot_ratio = 2.
plt.figure(figsize=(10 * plot_ratio, 10 * plot_ratio))
plt.axis("off")
plt.title("Refined SuGaR mesh with a traditional color UV texture")
plt.imshow(rgb_img.cpu().numpy())
plt.show()
```














