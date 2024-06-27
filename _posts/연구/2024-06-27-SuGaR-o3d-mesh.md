---
title: "[3D CV 연구] 3DGS SuGaR surface_mesh_to_bind = o3d_mesh"
last_modified_at: 2024-06-26
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - implementation details
  - surface_mesh_to_bind
  - o3d mesh
  - open3d mesh
  - n_points
excerpt: "surface_mesh_to_bind = o3d_mesh / n_points = len(triangles * n_gaussians_per_triangle"
use_math: true
classes: wide
---

# `self._surface_mesh_faces` 설명

- `self._surface_mesh_faces` = `surface_mesh_to_bind.triangles`
- 즉, mesh의 faces인 triangles에 대한 인덱스를 가집니다.
- 이 triangle에 대한 index는 triangle의 vertices, colors 정보를 인덱싱하는데 사용합니다.

### 모든 삼각형의 꼭짓점을 모으는 부분
```python
faces_verts = self._points[self._surface_mesh_faces]  # n_faces, 3, n_coords
```
- `self._points`: 메쉬의 모든 꼭짓점의 좌표를 포함하는 배열입니다. 일반적으로 `(n_vertices, n_coords)` 형태를 가지며, `n_vertices`는 꼭짓점의 수, `n_coords`는 좌표의 차원(일반적으로 3D에서 x, y, z)을 의미합니다.
- `self._surface_mesh_faces`: 메쉬의 각 면(삼각형)을 구성하는 꼭짓점의 인덱스를 포함하는 배열입니다. 일반적으로 `(n_faces, 3)` 형태를 가지며, `n_faces`는 면의 수, 각 면은 세 개의 꼭짓점 인덱스로 표현됩니다.
- 인덱싱: `self._points`를 `self._surface_mesh_faces`로 인덱싱함으로써 각 면을 구성하는 꼭짓점의 좌표를 모읍니다. 결과적으로 `faces_verts`는 `(n_faces, 3, n_coords)` 형태를 가지게 됩니다. 즉, 각 면에 대해 세 개의 꼭짓점 좌표를 가지게 됩니다.

### 각 면의 꼭짓점 색상을 얻는 부분
```python
faces_colors = self._vertex_colors[self._surface_mesh_faces]  # n_faces, 3, n_coords
```
- `self._vertex_colors`: 메쉬의 각 꼭짓점에 대한 색상 정보를 포함하는 배열입니다. 일반적으로 `(n_vertices, n_coords)` 형태를 가지며, `n_coords`는 색상 구성 요소의 수(일반적으로 RGB의 경우 3)를 의미합니다.
- `self._surface_mesh_faces`: 메쉬의 각 면을 구성하는 꼭짓점의 인덱스를 포함합니다.
- 인덱싱: `self._vertex_colors`를 `self._surface_mesh_faces`로 인덱싱하여 각 면을 구성하는 꼭짓점의 색상을 얻습니다. 결과적으로 `faces_colors`는 `(n_faces, 3, n_coords)` 형태를 가지게 됩니다. 즉, 각 면에 대해 세 개의 꼭짓점 색상을 가지게 됩니다.

### `self._surface_mesh_faces`의 역할
- 꼭짓점 인덱스 매핑: `self._surface_mesh_faces`는 각 면을 구성하는 꼭짓점의 인덱스를 매핑합니다. 각 항목은 삼각형 면을 구성하는 세 꼭짓점 인덱스를 제공합니다.
- **데이터 추출**: `self._surface_mesh_faces`를 사용하여 `self._points` 및 `self._vertex_colors`를 인덱싱함으로써 ***각 면을 구성하는 꼭짓점의 좌표 및 색상을 효율적으로 모을 수 있습니다.***

### 예시
간단한 예를 통해 설명드리겠습니다:

**가정:**
- `self._points`:
```python
np.array([
    [0.0, 0.0, 0.0],  # 꼭짓점 0
    [1.0, 0.0, 0.0],  # 꼭짓점 1
    [0.0, 1.0, 0.0],  # 꼭짓점 2
    [1.0, 1.0, 0.0]   # 꼭짓점 3
])
```
각 행은 꼭짓점의 좌표를 나타냅니다.

- `self._vertex_colors`:
```python
np.array([
    [1.0, 0.0, 0.0],  # 빨강 (꼭짓점 0)
    [0.0, 1.0, 0.0],  # 초록 (꼭짓점 1)
    [0.0, 0.0, 1.0],  # 파랑 (꼭짓점 2)
    [1.0, 1.0, 0.0]   # 노랑 (꼭짓점 3)
])
```
각 행은 꼭짓점의 색상을 나타냅니다.

- `self._surface_mesh_faces`:
```python
np.array([
    [0, 1, 2],  # 면 1 (꼭짓점 0, 1, 2)
    [1, 2, 3]   # 면 2 (꼭짓점 1, 2, 3)
])
```

**연산:**

- **모든 삼각형의 꼭짓점을 모으기:**
```python
faces_verts = self._points[self._surface_mesh_faces]
```
- 결과:
```python
np.array([
    [
        [0.0, 0.0, 0.0],  # 꼭짓점 0
        [1.0, 0.0, 0.0],  # 꼭짓점 1
        [0.0, 1.0, 0.0]   # 꼭짓점 2
    ],
    [
        [1.0, 0.0, 0.0],  # 꼭짓점 1
        [0.0, 1.0, 0.0],  # 꼭짓점 2
        [1.0, 1.0, 0.0]   # 꼭짓점 3
    ]
])
```

- **각 면의 꼭짓점 색상을 얻기:**
```python
faces_colors = self._vertex_colors[self._surface_mesh_faces]
```
- 결과:
```python
np.array([
    [
        [1.0, 0.0, 0.0],  # 빨강 (꼭짓점 0)
        [0.0, 1.0, 0.0],  # 초록 (꼭짓점 1)
        [0.0, 0.0, 1.0]   # 파랑 (꼭짓점 2)
    ],
    [
        [0.0, 1.0, 0.0],  # 초록 (꼭짓점 1)
        [0.0, 0.0, 1.0],  # 파랑 (꼭짓점 2)
        [1.0, 1.0, 0.0]   # 노랑 (꼭짓점 3)
    ]
])
```

### 이름 그림으로 그려보면 다음과 같습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6ef690f5-fa6b-459f-8930-4a63cf47833e)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3ef371ba-9f73-4c0a-ab15-5f91a12f7036)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/309c48f9-7bf6-44f9-aabf-ad6ffc14e7e3)

- `vertex의 3차원 coords`와 `color의 rgb 3개 color` 모두 `dim=3`이므로, 같은 이름으로 `n_coords = 3` 으로 주석을 단 것으로 보입니다.
- `n_coords`는 **실제로 코드에서 변수로 할당되어서 쓰고 있지는 않으며, 단지 주석에서만 사용하고 있습니다.**
- 즉, 위 코드에서는 rgb color도 n_coords로 주석을 작성하였는데, 정확히 하려면 rgb로 쓰는게 맞습니다.
  ```python
  faces_verts = self._points[self._surface_mesh_faces] # n_faces, 3, n_coords
  faces_colors = self._vertex_colors[self._surface_mesh_faces] # n_faces, 3, rgb <-- n_coords말고 rgb로 주석달기
  ```

### 위의 원리를 이해하면, triangle의 index 정보인 o3d_mesh.triangles을 통해 우리는 다양한 연산을 수행할 수 있게 됩니다.

- **아래 코드에서 `face_verts[:, [1, 2, 0]`과 같이 face의 vertices 3개를 재배열한것과 기존의 face의 vertices와의 차이로 각 triangle의 변에 대한 벡터를 구할 수 있습니다.**
- 그리고 그 벡터의 크기를 구하고, 가장 짧은 변의 길이를 계산할 수 있습니다.

- triangle에서 가장 짧은 변의 길이를 구하는 자세한 순서는 다음과 같습니다.
  - 꼭짓점 좌표 재배치: `faces_verts[:, [1, 2, 0]]`을 통해 각 삼각형의 꼭짓점 순서를 재배치합니다.
  - 벡터 계산: `faces_verts - faces_verts[:, [1, 2, 0]]`을 통해 각 삼각형의 변 벡터를 계산합니다.
  - 벡터 크기 계산: `.norm(dim=-1)`을 사용하여 각 변 벡터의 크기를 계산합니다.
  - 최소 변 길이 추출: `.min(dim=-1)[0]`을 통해 각 삼각형에서 가장 짧은 변의 길이를 구합니다.

```python
# SuGaR/sugar_scene/sugar_model.py
...
            # First gather vertices of all triangles
            faces_verts = self._points[self._surface_mesh_faces]  # n_faces, 3, n_coords
            
            # Then, compute initial scales
            scales = (faces_verts - faces_verts[:, [1, 2, 0]]).norm(dim=-1).min(dim=-1)[0] * self.surface_triangle_circle_radius
            scales = scales.clamp_min(0.0000001).reshape(len(faces_verts), -1, 1).expand(-1, self.n_gaussians_per_surface_triangle, 2).clone().reshape(-1, 2)
            self._scales = nn.Parameter(
                scale_inverse_activation(scales),
                requires_grad=self.learn_surface_mesh_scales).to(nerfmodel.device)
```

- triangle에서 가장 짧은 변의 길이를 구하고자 vertices로 벡터를 구하는 방법을 그림으로 나타내면 다음과 같습니다.
  - `faces_verts - faces_verts[:, [1, 2, 0]]`을 통해 각 삼각형의 변 벡터를 계산합니다.
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/21494ec8-65c9-40e2-9c64-70978e7efba4)


- `scales`은 reshape, expand, reshape을 거쳐 최종적으로는 gaussian당 2개의 scale factor를 가지도록 학습파라미터가 초기화됩니다.
```python
scales = scales.clamp_min(0.0000001).reshape(len(faces_verts), -1, 1).expand(-1, self.n_gaussians_per_surface_triangle, 2).clone().reshape(-1, 2)
```
- 위 코드를 단계별로 쪼개보면 아래와 같습니다.
```python
scales = scales.clamp_min(0.0000001).reshape(len(faces_verts), -1, 1)  # (n_faces, 1, 1)
scales = scales.expand(-1, self.n_gaussians_per_surface_triangle, 2)  # (n_faces, n_gaussians_per_surface_triangle, 2)
scales = scales.clone().reshape(-1, 2)  # (n_faces * n_gaussians_per_surface_triangle, 2)
```
- `# (n_faces *  n_gaussians_per_surface_triangle) * 2`를 해석하면 다음과 같습니다.
- `faces 수 * face 당 gaussian 수 = 모든 triangles에 초기화 되는 총 gaussian 수`마다 scale 차원이 2개로 초기화됩니다.
- face와 triangle은 같은 의미로 사용되므로 헷갈리지 맙시다.

### 요약
- `self._surface_mesh_faces`는 메쉬의 각 면을 구성하는 꼭짓점 인덱스를 매핑합니다.
- 이를 사용하여 `self._points`와 `self._vertex_colors`를 인덱싱하면 각 면을 구성하는 꼭짓점의 좌표와 색상을 모을 수 있습니다. 결과적으로, 이 배열들은 각각 `(n_faces, 3, n_coords)` 형태를 가지게 됩니다.

### open3d mesh를 불러와서 initialize하는 법을 알아봅시다.

- surface_mesh_to_bind는 o3d mesh이고
- n_points = surface_mesh_to_bind의 triangle 수 * triangle당 gaussian 수 입니다.
- ***즉, n_points는 triangle들 위에 정의한 gaussian들의 총 개수를 의미합니다.***

SuGaR에서 사용하는 o3d_mesh의 properties는 다음과 같습니다.

- o3d_mesh.triangles --> self._surface_mesh_faces 변수로 할당 (triangles와 faces는 같은 의미로 사용되기 때문입니다.)
- o3d_mesh.vertices --> points 변수로 할당
- o3d_mesh.vertex_normals --> verts_normals 변수로 할
- o3d_mesh.vertex_colors --> self._vertex_colors 변수로 할당

```python
# SuGaR/sugar_scene/sugar_model.py
...
            self._surface_mesh_faces = torch.nn.Parameter(
                torch.tensor(np.array(surface_mesh_to_bind.triangles)).to(nerfmodel.device), 
                requires_grad=False).to(nerfmodel.device)
...
            points = torch.tensor(np.array(surface_mesh_to_bind.vertices)).float().to(nerfmodel.device)
            # verts_normals = torch.tensor(np.array(surface_mesh_to_bind.vertex_normals)).float().to(nerfmodel.device)
            self._vertex_colors = torch.tensor(np.array(surface_mesh_to_bind.vertex_colors)).float().to(nerfmodel.device)
...
```

- o3d_mesh.vertex_colors[o3d_mesh.triangles] --> you obtain an array of the vertex colors organized by faces, which can be useful for operations that need face-wise color information, such as rendering or computing face colors from vertex colors.
- 아래 코드에서는 `faces_colors = self._vertex_colors[self._surface_mesh_faces]`로 face(triangle)의 각 3개의 vertex에 대한 colors를 얻습니다.
- 그리고 face(triangle)의 각 3개의 vertex에 대한 colors는 barycentric coordinates를 계산하는데 각 3개의 vertex의 value로써 사용됩니다.
- 각 triangle당 3개의 vertex colors는 sum된 다음 barycentric coordinates의 weight를 곱해 triangle 내에서 초기화되는 gaussian center들의 colors를 initialize 해줍니다.
- 또한 triangle 내부의 gaussian들이 겹치지 않도록 gaussian의 radius를 정해줍니다. 이는 삼각형 내부의 작은원의 반지름과 같다고 볼 수 있습니다.

```python
# SuGaR/sugar_scene/sugar_model.py
...
            print("Binding radiance cloud to surface mesh...")
            if n_gaussians_per_surface_triangle == 1:
                self.surface_triangle_circle_radius = 1. / 2. / np.sqrt(3.)
                self.surface_triangle_bary_coords = torch.tensor(
                    [[1/3, 1/3, 1/3]],
                    dtype=torch.float32,
                    device=nerfmodel.device,
                )[..., None]
            
            if n_gaussians_per_surface_triangle == 3:
                self.surface_triangle_circle_radius = 1. / 2. / (np.sqrt(3.) + 1.)
                self.surface_triangle_bary_coords = torch.tensor(
                    [[1/2, 1/4, 1/4],
                    [1/4, 1/2, 1/4],
                    [1/4, 1/4, 1/2]],
                    dtype=torch.float32,
                    device=nerfmodel.device,
                )[..., None]
            
            if n_gaussians_per_surface_triangle == 4:
                self.surface_triangle_circle_radius = 1 / (4. * np.sqrt(3.))
                self.surface_triangle_bary_coords = torch.tensor(
                    [[1/3, 1/3, 1/3],
                    [2/3, 1/6, 1/6],
                    [1/6, 2/3, 1/6],
                    [1/6, 1/6, 2/3]],
                    dtype=torch.float32,
                    device=nerfmodel.device,
                )[..., None]  # n_gaussians_per_face, 3, 1
                
            if n_gaussians_per_surface_triangle == 6:
                self.surface_triangle_circle_radius = 1 / (4. + 2.*np.sqrt(3.))
                self.surface_triangle_bary_coords = torch.tensor(
                    [[2/3, 1/6, 1/6],
                    [1/6, 2/3, 1/6],
                    [1/6, 1/6, 2/3],
                    [1/6, 5/12, 5/12],
                    [5/12, 1/6, 5/12],
                    [5/12, 5/12, 1/6]],
                    dtype=torch.float32,
                    device=nerfmodel.device,
                )[..., None]
...

            self._surface_mesh_faces = torch.nn.Parameter(
                torch.tensor(np.array(surface_mesh_to_bind.triangles)).to(nerfmodel.device), 
                requires_grad=False).to(nerfmodel.device)
...
            self._vertex_colors = torch.tensor(np.array(surface_mesh_to_bind.vertex_colors)).float().to(nerfmodel.device)
            faces_colors = self._vertex_colors[self._surface_mesh_faces]  # n_faces, 3, n_coords
            colors = faces_colors[:, None] * self.surface_triangle_bary_coords[None]  # n_faces, n_gaussians_per_face, 3, n_colors
            colors = colors.sum(dim=-2)  # n_faces, n_gaussians_per_face, n_colors
            colors = colors.reshape(-1, 3)  # n_faces * n_gaussians_per_face, n_colors
...
```

- `n_points` 변수에 o3d_mesh.triangles 개수 * triangle당 gaussian의 개수로, triangles에 bind되어 학습되는 총 triangles의 수를 구합니다.

```python
# SuGaR/sugar_scene/sugar_model.py
...
            n_points = len(np.array(surface_mesh_to_bind.triangles)) * n_gaussians_per_surface_triangle
            self._n_points = n_points
...
```


### SuGaR에서 o3d_mesh로 사용하는 coarse_mesh를 불러와서 triangles, vertices, vertex_normals, vertex_colors를 사용하는 전체 코드를 봅시다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1d240062-091f-4373-b5b6-ecc60e3d12ce)

```python
import open3d as o3d
import pandas as pd
import numpy as np
import os

# Load the mesh
mesh_path = "C:/Users/maila/Desktop/coarse_mesh/bicycle/sugarmesh_3Dgs7000_sdfestim02_sdfnorm02_level03_decim1000000.ply"
mesh = o3d.io.read_triangle_mesh(mesh_path)

# Extract properties
triangles = np.asarray(mesh.triangles)
vertices = np.asarray(mesh.vertices)
vertex_normals = np.asarray(mesh.vertex_normals)
vertex_colors = np.asarray(mesh.vertex_colors)

# Analyze shapes before slicing
shapes = {
    'Triangles': triangles.shape,
    'Vertices': vertices.shape,
    'Vertex Normals': vertex_normals.shape,
    'Vertex Colors': vertex_colors.shape
}

# Slice first 10 entries
triangles = triangles[:10]
vertices = vertices[:10]
vertex_normals = vertex_normals[:10]
vertex_colors = vertex_colors[:10]

# Check if vertex colors are always positive
all_colors_positive = (vertex_colors >= 0).all()

# Convert to DataFrames
triangles_df = pd.DataFrame(triangles, columns=['v0', 'v1', 'v2'])
vertices_df = pd.DataFrame(vertices, columns=['x', 'y', 'z'])
vertex_normals_df = pd.DataFrame(vertex_normals, columns=['nx', 'ny', 'nz'])
vertex_colors_df = pd.DataFrame(vertex_colors, columns=['r', 'g', 'b'])

# Create a DataFrame to save the shapes
df_shapes = pd.DataFrame({
    'Property': shapes.keys(),
    'Shape': [str(shape) for shape in shapes.values()]
})

# Save shapes DataFrame to CSV
output_shapes_path = os.path.join(os.path.dirname(mesh_path), "mesh_properties_shapes.csv")
df_shapes.to_csv(output_shapes_path, index=False)

# Save the DataFrames to separate CSV files
output_values_paths = {
    'Triangles': os.path.join(os.path.dirname(mesh_path), "mesh_triangles_first_10.csv"),
    'Vertices': os.path.join(os.path.dirname(mesh_path), "mesh_vertices_first_10.csv"),
    'Vertex Normals': os.path.join(os.path.dirname(mesh_path), "mesh_vertex_normals_first_10.csv"),
    'Vertex Colors': os.path.join(os.path.dirname(mesh_path), "mesh_vertex_colors_first_10.csv")
}

triangles_df.to_csv(output_values_paths['Triangles'], index=False)
vertices_df.to_csv(output_values_paths['Vertices'], index=False)
vertex_normals_df.to_csv(output_values_paths['Vertex Normals'], index=False)
vertex_colors_df.to_csv(output_values_paths['Vertex Colors'], index=False)

# Display the shapes DataFrame
print("Mesh Properties Shapes:")
print(df_shapes)

# Display the paths to the saved files
output_paths = {
    'Shapes CSV': output_shapes_path,
    **output_values_paths
}
print("\nSaved Files Paths:")
for key, path in output_paths.items():
    print(f"{key}: {path}")

# Display the result of the vertex colors positivity check
if all_colors_positive:
    print("All vertex colors are positive.")
else:
    print("There are negative values in vertex colors.")
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3fdb5620-5cd0-4ab2-acbe-0848ef80f4a1)

- Shape: o3d_mesh.triangles, o3d_mesh.vertices, o3d_mesh.vertex_normals, o3d_mesh.vertex_colors
- vertices, vertex_normals, vertex_colors의 shape은 같아야 합니다.
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8bca6098-a1d9-4c84-a5a6-3cfd846af30a)

- 첫 10개의 triangles (v0, v1, v2는 x,y,z를 갖는 vertices의 인덱스)
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d3706c88-7f13-4927-b30b-74342714d3ee)

- 첫 10개의 vertices
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/48c37ec0-47f1-4247-9627-bd72b87c3668)

- 첫 10개의 vertex_normals
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f21648e3-5676-44b2-abc2-033dec9d55fc)

- 첫 10개의 vertex_colors (항상 양수임)
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ac0121aa-f14f-496e-b29d-0a0d90b3e357)



```python
# SuGaR/sugar_scene/sugar_model.py

class SuGaR(nn.Module):
    """Main class for SuGaR models.
    Because SuGaR optimization starts with first optimizing a vanilla Gaussian Splatting model for 7k iterations,
    we built this class as a wrapper of a vanilla Gaussian Splatting model.
    Consequently, a corresponding Gaussian Splatting model trained for 7k iterations must be provided.
    However, this wrapper implementation may not be the most optimal one for memory usage, so we might change it in the future.
    """
    def __init__(
        self, 
        ...
        surface_mesh_to_bind=None,  # Open3D mesh
        ...
        *args, **kwargs) -> None:
        """
        Args:
            ...
            primitive_types (str, optional): Type of primitive to use to replace the Gaussians. Defaults to 'diamond'.
            surface_mesh_to_bind (None, optional): Surface mesh to bind the Gaussians to. Defaults to None.
            surface_mesh_thickness (None, optional): Thickness of the bound Gaussians. Defaults to None.
            learn_surface_mesh_positions (bool, optional): Whether to learn the positions of the bound Gaussians. Defaults to True.
            learn_surface_mesh_opacity (bool, optional): Whether to learn the opacity of the bound Gaussians. Defaults to True.
            learn_surface_mesh_scales (bool, optional): Whether to learn the scales of the bound Gaussians. Defaults to True.
            n_gaussians_per_surface_triangle (int, optional): Number of bound Gaussians per surface triangle. Defaults to 6.
        """

...

        if surface_mesh_to_bind is not None:
            self.learn_surface_mesh_positions = learn_surface_mesh_positions
            self.binded_to_surface_mesh = True
            self.learn_surface_mesh_opacity = learn_surface_mesh_opacity
            self.learn_surface_mesh_scales = learn_surface_mesh_scales
            self.n_gaussians_per_surface_triangle = n_gaussians_per_surface_triangle
            self.editable = editable
            
            self.learn_positions = self.learn_surface_mesh_positions
            self.learn_scales = self.learn_surface_mesh_scales
            self.learn_quaternions = self.learn_surface_mesh_scales
            self.learn_opacities = self.learn_surface_mesh_opacity
            
            self._surface_mesh_faces = torch.nn.Parameter(
                torch.tensor(np.array(surface_mesh_to_bind.triangles)).to(nerfmodel.device), 
                requires_grad=False).to(nerfmodel.device)

...

            points = torch.tensor(np.array(surface_mesh_to_bind.vertices)).float().to(nerfmodel.device)
            # verts_normals = torch.tensor(np.array(surface_mesh_to_bind.vertex_normals)).float().to(nerfmodel.device)
            self._vertex_colors = torch.tensor(np.array(surface_mesh_to_bind.vertex_colors)).float().to(nerfmodel.device)
            faces_colors = self._vertex_colors[self._surface_mesh_faces]  # n_faces, 3, n_coords
            colors = faces_colors[:, None] * self.surface_triangle_bary_coords[None]  # n_faces, n_gaussians_per_face, 3, n_colors
            colors = colors.sum(dim=-2)  # n_faces, n_gaussians_per_face, n_colors
            colors = colors.reshape(-1, 3)  # n_faces * n_gaussians_per_face, n_colors
                
            self._points = nn.Parameter(points, requires_grad=self.learn_positions).to(nerfmodel.device)
            n_points = len(np.array(surface_mesh_to_bind.triangles)) * n_gaussians_per_surface_triangle
            self._n_points = n_points

...

```

- `surface_mesh_to_bind=o3d_mesh`, open3d mesh를 사용합니다.

```python
# SuGaR/sugar_scene/sugar_model.py

def load_refined_model(refined_sugar_path, nerfmodel:GaussianSplattingWrapper):
    checkpoint = torch.load(refined_sugar_path, map_location=nerfmodel.device)
    n_faces = checkpoint['state_dict']['_surface_mesh_faces'].shape[0]
    n_gaussians = checkpoint['state_dict']['_scales'].shape[0]
    n_gaussians_per_surface_triangle = n_gaussians // n_faces

    print("Loading refined model...")
    print(f'{n_faces} faces detected.')
    print(f'{n_gaussians} gaussians detected.')
    print(f'{n_gaussians_per_surface_triangle} gaussians per surface triangle detected.')

    with torch.no_grad():
        o3d_mesh = o3d.geometry.TriangleMesh()
        o3d_mesh.vertices = o3d.utility.Vector3dVector(checkpoint['state_dict']['_points'].cpu().numpy())
        o3d_mesh.triangles = o3d.utility.Vector3iVector(checkpoint['state_dict']['_surface_mesh_faces'].cpu().numpy())
        # o3d_mesh.vertex_normals = o3d.utility.Vector3dVector(normals.cpu().numpy())
        o3d_mesh.vertex_colors = o3d.utility.Vector3dVector(torch.ones_like(checkpoint['state_dict']['_points']).cpu().numpy())
        
    refined_sugar = SuGaR(
        nerfmodel=nerfmodel,
        points=checkpoint['state_dict']['_points'],
        colors=SH2RGB(checkpoint['state_dict']['_sh_coordinates_dc'][:, 0, :]),
        initialize=False,
        sh_levels=nerfmodel.gaussians.active_sh_degree+1,
        keep_track_of_knn=False,
        knn_to_track=0,
        beta_mode='average',
        surface_mesh_to_bind=o3d_mesh,
        n_gaussians_per_surface_triangle=n_gaussians_per_surface_triangle,
        )
    refined_sugar.load_state_dict(checkpoint['state_dict'])
    
    return refined_sugar

```


