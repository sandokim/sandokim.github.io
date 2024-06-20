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

### textured_mesh 정보 확인
print("Vertices list length:", len(textured_mesh.verts_list()))
print("Faces list length:", len(textured_mesh.faces_list()))

# 첫 번째 메시의 정점과 면 확인
print("First mesh vertices shape:", textured_mesh.verts_list()[0].shape)
print("First mesh faces shape:", textured_mesh.faces_list()[0].shape)

# 정점 좌표 출력 (처음 몇 개만)
print("First few vertices:", textured_mesh.verts_list()[0][:5])

# 면 인덱스 출력 (처음 몇 개만)
print("First few faces:", textured_mesh.faces_list()[0][:5])

# 텍스처 정보 확인
if textured_mesh.textures is not None:
    print("Texture maps available.")
    print("Texture maps shape:", textured_mesh.textures.maps_padded().shape)
else:
    print("No texture maps available.")
```
#### 출력결과
```
Loading refined mesh from ./output/refined_mesh/bicycle/sugarfine_3Dgs7000_sdfestim02_sdfnorm02_level03_decim1000000_normalconsistency01_gaussperface1.obj, this could take a minute...
Loaded textured mesh with 1051626 vertices and 1996616 faces.
Vertices list length: 1
Faces list length: 1
First mesh vertices shape: torch.Size([1051626, 3])
First mesh faces shape: torch.Size([1996616, 3])
First few vertices: tensor([[-0.7875,  2.8858, -4.9428],
        [-1.1828,  2.8120, -4.9368],
        [-1.3978,  2.6690, -4.9410],
        [ 2.2472,  2.8223, -4.9400],
        [ 0.6677,  2.8504, -4.9427]], device='cuda:0')
First few faces: tensor([[126,  37, 144],
        [126,  48,  37],
        [ 37,  55, 144],
        [175, 310, 158],
        [212,  68, 213]], device='cuda:0')
Texture maps available.
Texture maps shape: torch.Size([1, 10000, 10000, 3])
```
#### 해석
- Vertices list length: 1 --> 메시 객체는 하나의 3D 메시로 구성되어 있습니다.
- Faces list length: 1 --> 마찬가지로, 면 리스트도 하나의 3D 메시에 대한 정보를 포함하고 있습니다.
- First mesh vertices shape: torch.Size([1051626, 3]) --> 첫 번째 메시의 정점은 총 1,051,626개이며, 각 정점은 3D 공간의 좌표 (x, y, z)를 나타내는 3개의 값을 가집니다.
- First mesh faces shape: torch.Size([1996616, 3]) --> 첫 번째 메시의 면은 총 1,996,616개이며, 각 면은 3개의 정점 인덱스로 구성된 삼각형입니다.
- First few vertices:
  - [-0.7875, 2.8858, -4.9428]
  - [-1.1828, 2.8120, -4.9368]
  - [-1.3978, 2.6690, -4.9410]
  - [ 2.2472, 2.8223, -4.9400]
  - [ 0.6677, 2.8504, -4.9427]
  - 위 정점 좌표는 3D 공간에서의 위치를 나타내며, 각각의 좌표는 x, y, z 값을 가집니다. 

- First few faces:
  - [126, 37, 144]
  - [126, 48, 37]
  - [ 37, 55, 144]
  - [175, 310, 158]
  - [212, 68, 213]
  - 각 면 인덱스는 정점 리스트에서 정점의 위치를 가리키며, 이를 통해 삼각형 면을 형성합니다. 예를 들어, [126, 37, 144]는 126번, 37번, 144번 정점을 연결하여 하나의 삼각형 면을 형성합니다.

- 텍스처 맵 정보 확인
  - Texture maps available. --> 메시에는 텍스처 맵이 포함되어 있습니다.
  - Texture maps shape: torch.Size([1, 10000, 10000, 3])
  - 텍스처 맵의 크기는 다음과 같습니다:
  - 배치 크기: 1 (단일 텍스처 맵)
  - 텍스처 해상도: 10,000 x 10,000 픽셀
  - 채널 수: 3 (RGB 색상 값)

#### 종합 해석
이 메시 객체는 매우 세밀한 3D 모델로, 1,051,626개의 정점과 1,996,616개의 삼각형 면으로 구성되어 있습니다. 정점은 3D 공간에서의 위치를 나타내고, 면은 이 정점을 연결하여 메시의 표면을 형성합니다. 또한, 이 메시에는 10,000 x 10,000 해상도의 고해상도 텍스처 맵이 포함되어 있어, 시각적으로 매우 정교한 렌더링이 가능합니다. 전체 메시와 텍스처 데이터는 GPU 디바이스에서 처리됩니다.

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

#### 해석
1. 카메라 인덱스 설정
- `cameras_to_use` 목록에서 무작위로 하나의 카메라 인덱스를 선택합니다. 이는 렌더링 시 사용할 카메라를 결정합니다.

2. 레스터화 설정
- `RasterizationSettings` 객체를 생성하여 레스터화 설정을 정의합니다.
 - `faces_per_pixel`: 픽셀당 최대 렌더링할 폴리곤의 수를 설정합니다. 여기서는 1로 설정되어 있습니다.
 - `image_size`: 렌더링할 이미지의 크기를 설정합니다.
 - `blur_radius`: 블러링 반경을 설정합니다. 여기서는 0으로 설정되어 있습니다.

3. 조명 설정
- **`AmbientLights` 객체를 생성하여 기본 환경 조명을 설정합니다. 이는 메시의 모든 면을 균일하게 비추는 조명입니다.**

4. Rasterizer 및 Render 설정
- `MeshRasterizer`: 선택된 카메라 설정과 레스터화 설정을 사용하여 메시를 레스터화합니다.
- `MeshRenderer`: 레스터화된 메시를 `SoftPhongShader`를 사용하여 렌더링합니다.
- `SoftPhongShader`: Phong shading 모델을 사용하여 렌더링하며, `blend_params`를 통해 배경 색상을 흰색(background_color=(1.0, 1.0, 1.0))으로 설정합니다.

5. Mesh Rendering
- `torch.no_grad()`: 그래디언트 계산을 비활성화하여 메모리 사용량을 줄입니다.
- 선택된 카메라 인덱스를 출력하고, 해당 카메라로 메시를 렌더링하여 RGB 이미지를 생성합니다.

#### 종합 해석
이 과정에서 사용된 설정과 매개변수들은 렌더링된 이미지의 품질과 성능에 직접적인 영향을 미칩니다. 예를 들어, faces_per_pixel 값을 높이면 더 많은 폴리곤을 처리하여 세밀한 렌더링을 할 수 있지만, 성능이 저하될 수 있습니다. 배경 색상을 흰색으로 설정함으로써 결과 이미지가 깔끔하고 명확하게 보일 수 있습니다.

