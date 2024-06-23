---
title: "[Mesh] SuGaR o3d TriangleMesh"
last_modified_at: 2024-06-23
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - Mesh extraction
  - SuGaR
  - coarse mesh
  - refined mesh
  - TriangleMesh
  - read_triangle_mesh
  - open3d
  - o3d
excerpt: "open3d로 TriangleMesh를 만들고, read_triangle_mesh로 불러오는 법 정리"
use_math: true
classes: wide
---

# Open3D에서 TriangleMesh 직접 생성하기

`TriangleMesh` 객체를 직접 생성하여 3D 데이터를 정의할 수 있습니다. Open3D에서 `TriangleMesh`를 직접 만드는 방법을 단계별로 설명하겠습니다. 이 과정에서는 정점(vertices)과 삼각형(triangles)을 명시적으로 정의하여 메쉬를 생성합니다.

## Open3D 라이브러리 설치
먼저 Open3D 라이브러리가 설치되어 있어야 합니다. 설치하지 않았다면 다음 명령어를 사용하여 설치할 수 있습니다:
```sh
pip install open3d
```

# TriangleMesh 객체 생성
다음은 정점과 삼각형을 직접 정의하여 TriangleMesh 객체를 생성하고 시각화하는 예시입니다.

```python
import open3d as o3d
import numpy as np

# 정점 (vertices) 정의
vertices = np.array([
    [0, 0, 0],  # Vertex 0
    [1, 0, 0],  # Vertex 1
    [0, 1, 0],  # Vertex 2
    [0, 0, 1]   # Vertex 3
])

# 삼각형 (triangles) 정의
triangles = np.array([
    [0, 1, 2],  # Triangle 0
    [0, 1, 3],  # Triangle 1
    [0, 2, 3],  # Triangle 2
    [1, 2, 3]   # Triangle 3
])

# TriangleMesh 객체 생성
mesh = o3d.geometry.TriangleMesh()
mesh.vertices = o3d.utility.Vector3dVector(vertices)
mesh.triangles = o3d.utility.Vector3iVector(triangles)

# 메쉬에 색상 추가 (선택 사항)
mesh.vertex_colors = o3d.utility.Vector3dVector(np.array([
    [1, 0, 0],  # Color for Vertex 0
    [0, 1, 0],  # Color for Vertex 1
    [0, 0, 1],  # Color for Vertex 2
    [1, 1, 0]   # Color for Vertex 3
]))

# 메쉬 시각화
o3d.visualization.draw_geometries([mesh])
```

- 정점 정의:정점은 3차원 공간의 좌표로 정의됩니다. 여기서는 네 개의 정점을 정의했습니다.
- 삼각형 정의:삼각형은 정점의 인덱스로 정의됩니다. 예를 들어, [0, 1, 2]는 첫 번째, 두 번째, 세 번째 정점을 연결하는 삼각형을 의미합니다.
- TriangleMesh 객체 생성:TriangleMesh 객체를 생성한 후, 정점과 삼각형 데이터를 Vector3dVector와 Vector3iVector를 사용하여 설정합니다.
- 색상 추가 (선택 사항):각 정점에 색상을 추가할 수 있습니다. 이는 시각화할 때 유용합니다.
- 시각화:draw_geometries 함수를 사용하여 생성한 메쉬를 시각화합니다.

### 아래의 open3d official document 예제의 그림을 보면 vertices와 triangle이 어떻게 정의되어 TriangleMesh 객체를 구성하게 되는지 알 수 있습니다.

https://www.open3d.org/docs/release/python_api/open3d.utility.Vector3iVector.html

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2854550f-481b-4013-bd4e-01beb00926d8)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/50f106e0-3a42-49f1-aba0-679f7130d9da)

### 이제 SuGaR에서 TriangleMesh를 어떻게 불러오는지와 어떻게 새롭게 정의해서 넘기는지 봅시다.

- 저장된 TriangleMesh 객체를 불러오는 법은 `o3d.io.read_triangle_mesh`로 합니다.
  
```python
# sugar_extractors/refined_mesh.py

# --- Loading coarse mesh ---
o3d_mesh = o3d.io.read_triangle_mesh(sugar_mesh_path)
    
# --- Loading refined SuGaR model ---
checkpoint = torch.load(refined_model_path, map_location=nerfmodel.device)

refined_sugar = SuGaR(
        nerfmodel=nerfmodel,
        points=checkpoint['state_dict']['_points'],        colors=SH2RGB(checkpoint['state_dict']['_sh_coordinates_dc'][:, 0, :]),
        initialize=False,        sh_levels=nerfmodel.gaussians.active_sh_degree+1,
keep_track_of_knn=False,
knn_to_track=0,
beta_mode='average',
surface_mesh_to_bind=o3d_mesh,    n_gaussians_per_surface_triangle=n_gaussians_per_surface_triangle,
)
    refined_sugar.load_state_dict(checkpoint['state_dict'])
refined_sugar.eval()
```

- 새로운 TriangleMesh 객체를 만들때는,`o3d.geometry.TriangleMesh()`로 정의하고, 이에 `vertices`, `triangles`, `vertex_normals`, `vertex_colors`를 넘겨줍니다.

```python
# sugar_extractors/refined_mesh.py

new_o3d_mesh = o3d.geometry.TriangleMesh()
new_o3d_mesh.vertices = o3d.utility.Vector3dVector(new_verts.cpu().numpy())
new_o3d_mesh.triangles = o3d.utility.Vector3iVector(new_faces.cpu().numpy())
new_o3d_mesh.vertex_normals = o3d.utility.Vector3dVector(new_normals.cpu().numpy())
new_o3d_mesh.vertex_colors = o3d.utility.Vector3dVector(torch.ones_like(new_verts).cpu().numpy())
            
refined_sugar = SuGaR(
nerfmodel=nerfmodel,
points=None,
colors=None,
initialize=False,
sh_levels=nerfmodel.gaussians.active_sh_degree+1,
keep_track_of_knn=False,
knn_to_track=0,
beta_mode='average',
surface_mesh_to_bind=new_o3d_mesh,
n_gaussians_per_surface_triangle=refined_sugar.n_gaussians_per_surface_triangle,
)
```
  - 이때, `vertices`, `triangles`, `vertex_normals`, `vertex_colors`는 앞에서 미리 mesh에서 `verts_list()`, `face_list()`, `faces_normals_list()`로 불러와서 만든 정보입니다.
  ```python
       if postprocess_mesh:
        CONSOLE.print("Postprocessing mesh by removing border triangles with low-opacity gaussians...")
        with torch.no_grad():
            new_verts = refined_sugar.surface_mesh.verts_list()[0].detach().clone()
            new_faces = refined_sugar.surface_mesh.faces_list()[0].detach().clone()
            new_normals = refined_sugar.surface_mesh.faces_normals_list()[0].detach().clone()
            
            # For each face, get the 3 edges
            edges0 = new_faces[..., None, (0,1)].sort(dim=-1)[0]
            edges1 = new_faces[..., None, (1,2)].sort(dim=-1)[0]
            edges2 = new_faces[..., None, (2,0)].sort(dim=-1)[0]
            all_edges = torch.cat([edges0, edges1, edges2], dim=-2)
            
            # We start by identifying the inside faces and border faces
            face_mask = refined_sugar.strengths[..., 0] > -1.
            for i in range(postprocess_iterations):
                CONSOLE.print("\nStarting postprocessing iteration", i)
                # We look for edges that appear in the list at least twice (their NN is themselves)
                edges_neighbors = knn_points(all_edges[face_mask].view(1, -1, 2).float(), all_edges[face_mask].view(1, -1, 2).float(), K=2)
                # If all edges of a face appear in the list at least twice, then the face is inside the mesh
                is_inside = (edges_neighbors.dists[0][..., 1].view(-1, 3) < 0.01).all(-1)
                # We update the mask by removing border faces
                face_mask[face_mask.clone()] = is_inside

            # We then add back border faces with high-density
            face_centers = new_verts[new_faces].mean(-2)
            face_densities = refined_sugar.compute_density(face_centers[~face_mask])
            face_mask[~face_mask.clone()] = face_densities > postprocess_density_threshold

            # And we create the new mesh and SuGaR model
            new_faces = new_faces[face_mask]
            new_normals = new_normals[face_mask]

            new_scales = refined_sugar._scales.reshape(len(face_mask), -1, 2)[face_mask].view(-1, 2)
            new_quaternions = refined_sugar._quaternions.reshape(len(face_mask), -1, 2)[face_mask].view(-1, 2)
            new_densities = refined_sugar.all_densities.reshape(len(face_mask), -1, 1)[face_mask].view(-1, 1)
            new_sh_coordinates_dc = refined_sugar._sh_coordinates_dc.reshape(len(face_mask), -1, 1, 3)[face_mask].view(-1, 1, 3)
            new_sh_coordinates_rest = refined_sugar._sh_coordinates_rest.reshape(len(face_mask), -1, 15, 3)[face_mask].view(-1, 15, 3)
    ```
