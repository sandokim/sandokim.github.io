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

`pcd`에 대해서 points, colors, normals 정보를 넣어주고, `o3d.geometry.TriangleMesh.create_from_point_cloud_poisson`로 `pcd`를 주면 density를 구할 수 있습니다.

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





