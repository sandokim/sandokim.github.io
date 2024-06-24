---
title: "[Mesh] o3d poisson reconstruction"
last_modified_at: 2024-06-24
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - o3d
  - open3d
  - poisson reconstruction
  - pcd
  - fg_pcd
  - bg_pcd
excerpt: "o3d poisson reconstruction"
use_math: true
classes: wide
---

## Poisson Surface Reconstruction의 원리

Poisson surface reconstruction은 주어진 포인트 클라우드 데이터로부터 삼각형 메쉬를 생성하는데, 이 방법은 점의 위치뿐만 아니라 각 점의 노멀 벡터 정보를 사용합니다.

### 원리

1. **포인트 클라우드 데이터**:
   - 점들의 좌표 (points)
   - 각 점에서의 색상 정보 (colors)
   - 각 점에서의 노멀 벡터 (normals)

2. **Octree 생성**:
   - 주어진 깊이(depth)에 따라 포인트 클라우드 데이터를 옥트리 구조로 분할합니다. 깊이가 깊을수록 더 세밀한 구조가 생성됩니다.

3. **Poisson 방정식 풀이**:
   - 포인트 클라우드 데이터의 노멀 벡터를 이용해 Poisson 방정식을 풉니다. 이를 통해 점들이 나타내는 표면의 연속적인 구조를 추정합니다.

4. **삼각형 메쉬 생성**:
   - Poisson 방정식의 해를 바탕으로 포인트 클라우드 데이터를 삼각형 메쉬로 변환합니다. 이는 점들이 매끄러운 표면을 나타내도록 합니다.

### Input Requirements

- **포인트 데이터** (3D 좌표)
- **노멀 데이터** (각 점에서의 표면 노멀 벡터)
- 추가적으로 **컬러 데이터**는 시각화를 위해 사용될 수 있습니다.

포인트 클라우드 데이터가 충분히 밀집되고, 노멀 벡터가 정확하게 계산된 경우, Poisson surface reconstruction을 통해 매끄럽고 정확한 표면을 생성할 수 있습니다.

Poisson surface reconstruction의 성공 여부는 입력된 포인트 클라우드의 품질에 크게 의존합니다. 특히, 노멀 벡터가 올바르게 설정되지 않으면 표면의 정확도가 떨어질 수 있습니다.

## 요약

Poisson surface reconstruction은 포인트 클라우드 데이터와 해당 데이터의 노멀 벡터를 기반으로 삼각형 메쉬를 생성하는 방법입니다. 이 과정에서 옥트리 깊이(depth) 설정은 생성될 메쉬의 세밀함을 조절합니다.

### poisson reconstruction을 o3d로 구현시, points와 normals을 모두 가진 pcd를 poisson reconstruction을 수행하는 함수에 넣어줍니다.

- `pcd`에 대해서 points, colors, normals 정보를 넣어주고, `o3d.geometry.TriangleMesh.create_from_point_cloud_poisson`로 `pcd`를 주면 densities를 구할 수 있습니다.
- 아래 코드에서 fg_pcd에 fg_points, fg_colors, fg_normals을 Vector로 넣어주고, fg_pcd를 poisson 함수에 넣고, poisson_depth(=octree depth)를 설정하여, fg_densities를 얻습니다.
- `o3d_fg_densities = o3d.geometry.TriangleMesh.create_from_point_cloud_poisson(fg_pcd, depth=poisson_depth)`
- bg_pcd에 대해서도 동일하게 진행합니다.

```python
# sugar_extractors/coarse_mesh.py
def extract_mesh_from_coarse_sugar(args):

...

               # ---Compute foreground mesh---
                CONSOLE.print("\n-----Foreground mesh-----")
                if fg_points.shape[0] > 0:
                    CONSOLE.print("Computing points, colors and normals...")
                    fg_pcd = o3d.geometry.PointCloud()
                    fg_pcd.points = o3d.utility.Vector3dVector(fg_points.double().cpu().numpy())
                    fg_pcd.colors = o3d.utility.Vector3dVector(fg_colors.double().cpu().numpy())
                    fg_pcd.normals = o3d.utility.Vector3dVector(fg_normals.double().cpu().numpy())

                    # outliers removal
                    cl, ind = fg_pcd.remove_statistical_outlier(nb_neighbors=20, std_ratio=20.)
                    CONSOLE.print("Cleaning Point Cloud...")
                    fg_pcd = fg_pcd.select_by_index(ind)

                    CONSOLE.print("Finished computing points, colors and normals.")

                    CONSOLE.print("Now computing mesh...")
                    o3d_fg_mesh, o3d_fg_densities = o3d.geometry.TriangleMesh.create_from_point_cloud_poisson(
                        fg_pcd, depth=poisson_depth) #, width=0, scale=1.1, linear_fit=False)  # depth=10 should be the default value? 11 is good to (but it starts to make a big number of triangles)

                    if vertices_density_quantile > 0.:
                        CONSOLE.print("Removing vertices with low densities...")
                        vertices_to_remove = o3d_fg_densities < np.quantile(o3d_fg_densities, vertices_density_quantile)
                        o3d_fg_mesh.remove_vertices_by_mask(vertices_to_remove)
                else:
                    CONSOLE.print("\n[WARNING] Foreground is empty.")
                    o3d_fg_mesh = None
                
                # ---Compute background mesh---
                CONSOLE.print("\n-----Background mesh-----")
                if bg_points.shape[0] > 0:
                    CONSOLE.print("Computing points, colors and normals...")
                    bg_pcd = o3d.geometry.PointCloud()
                    bg_pcd.points = o3d.utility.Vector3dVector(bg_points.double().cpu().numpy())
                    bg_pcd.colors = o3d.utility.Vector3dVector(bg_colors.double().cpu().numpy())
                    bg_pcd.normals = o3d.utility.Vector3dVector(bg_normals.double().cpu().numpy())

                    # outliers removal
                    cl, ind = bg_pcd.remove_statistical_outlier(nb_neighbors=20, std_ratio=20.)
                    CONSOLE.print("Cleaning Point Cloud...")
                    bg_pcd = bg_pcd.select_by_index(ind)

                    CONSOLE.print("Finished computing points, colors and normals.")

                    CONSOLE.print("Now computing mesh...")
                    o3d_bg_mesh, o3d_bg_densities = o3d.geometry.TriangleMesh.create_from_point_cloud_poisson(
                        bg_pcd, depth=poisson_depth) #, width=0, scale=1.1, linear_fit=False)  # depth=10 should be the default value? 11 is good to (but it starts to make a big number of triangles)

                    if vertices_density_quantile > 0.:
                        CONSOLE.print("Removing vertices with low densities...")
                        vertices_to_remove = o3d_bg_densities < np.quantile(o3d_bg_densities, vertices_density_quantile)
                        o3d_bg_mesh.remove_vertices_by_mask(vertices_to_remove)
                else:
                    CONSOLE.print("\n[WARNING] Background is empty.")
                    o3d_bg_mesh = None
                
                CONSOLE.print("Finished computing meshes.")
                CONSOLE.print("Foreground mesh:", o3d_fg_mesh)
                CONSOLE.print("Background mesh:", o3d_bg_mesh)

```





