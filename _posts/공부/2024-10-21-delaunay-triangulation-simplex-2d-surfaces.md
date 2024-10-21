---
title: "[3D CV] Extract 2D surfaces (표면 메시) in 3D with Delaunay triangulation"
last_modified_at: 2024-10-18
categories:
  - 공부
tags:
  - Delaunay triangulation
  - 델로네 삼각분할
  - 외접원
  - polygon mesh
  - reconstruction
  - point cloud
  - 표면 메시
  - mesh
  - 3D Delaunay triangulation
  - tetrahedra
  - 2D surfaces
excerpt: "Delaunay triangulation로 표면 메시 추출하는 방법 정리"
use_math: true
classes: wide
comments: true
---

> [Simplex](https://en.wikipedia.org/wiki/Simplex)

3D 포인트 클라우드에서 입체를 모델링할 때는 3D Delaunay triangulation을 사용해야 합니다.

3D Delaunay triangulation은 사면체(tetrahedra)를 생성하지만, 우리는 표면 메시만 필요하므로 사면체의 외부 면(2D surfaces)만 추출하여 사용할 것입니다.

이를 위해 `scipy.spatial import Delaunay`로 point cloud에 대한 Delaunay 객체를 tri 변수로 생성하고, simplices를 사용합니다.

### simplices

In geometry, a simplex (plural: simplexes or simplices) is a generalization of the notion of a triangle or tetrahedron to arbitrary dimensions. The simplex is so-named because it represents the simplest possible polytope in any given dimension. For example,

- a 0-dimensional simplex is a point,
- a 1-dimensional simplex is a line segment,
- a 2-dimensional simplex is a triangle,
- a 3-dimensional simplex is a tetrahedron, and
- a 4-dimensional simplex is a 5-cell.

![image](https://github.com/user-attachments/assets/adffe796-66ce-4529-b406-cf3bedd86042)

The four simplexes that can be fully represented in 3D space.

### Code 구현

```python
import open3d as o3d
import numpy as np
from scipy.spatial import Delaunay

def extract_surface_mesh(points, tetra):
    # 각 사면체의 면을 추출
    faces = tetra[:, [0, 1, 2, 0, 2, 3, 0, 1, 3, 1, 2, 3]].reshape(-1, 3)
    
    # 중복 면 제거 (표면 면만 남김)
    faces = np.sort(faces, axis=1)
    faces, counts = np.unique(faces, return_counts=True, axis=0)
    surface_faces = faces[counts == 1]
    
    return surface_faces

def barycentric_deformation(GT, BP, GT_colors, BP_colors):
    print("\n Processing Barycentric Deformation.. \n")
    
    # Convert numpy arrays to Open3D point clouds
    gt_pcd = o3d.geometry.PointCloud()
    gt_pcd.points = o3d.utility.Vector3dVector(GT)
    gt_pcd.colors = o3d.utility.Vector3dVector(GT_colors / 255.0)
    
    bp_pcd = o3d.geometry.PointCloud()
    bp_pcd.points = o3d.utility.Vector3dVector(BP)
    bp_pcd.colors = o3d.utility.Vector3dVector(BP_colors / 255.0)
    
    # Create a mesh from BP using 3D Delaunay triangulation
    bp_points = np.asarray(bp_pcd.points)
    tri = Delaunay(bp_points)
    surface_faces = extract_surface_mesh(bp_points, tri.simplices)
    
    bp_mesh = o3d.geometry.TriangleMesh()
    bp_mesh.vertices = o3d.utility.Vector3dVector(bp_points)
    bp_mesh.triangles = o3d.utility.Vector3iVector(surface_faces)
    bp_mesh.compute_vertex_normals()
```



