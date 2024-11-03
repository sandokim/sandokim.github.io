---
title: "[3D CV] DBSCAN clustering"
last_modified_at: 2024-11-03
categories:
  - 공부
tags:
  - DSBSCAN clustering
  - Density based clustering
  - 밀도 기반 클러스터링
excerpt: "DBSCAN(밀도 기반 클러스터링)에 대해 알아봅시다."
use_math: true
classes: wide
comments: true
---

> [DBSCAN clustering open3d code](https://www.open3d.org/docs/latest/tutorial/Basic/pointcloud.html)

### DBSCAN(Density-based spatial clustering of applications with noise)

Given a point cloud from e.g. a depth sensor we want to group local point cloud clusters together. 
For this purpose, we can use clustering algorithms. 
Open3D implements DBSCAN [Ester1996] that is a density based clustering algorithm. 

The algorithm is implemented in cluster_dbscan and requires two parameters: 

- `eps` defines the distance to neighbors in a cluster 
- `min_points` defines the minimum number of points required to form a cluster.
- The function returns labels, where the `label` -1 indicates noise.

```python
pcd = o3d.io.read_point_cloud("../../test_data/fragment.ply")

with o3d.utility.VerbosityContextManager(
        o3d.utility.VerbosityLevel.Debug) as cm:
    labels = np.array(
        pcd.cluster_dbscan(eps=0.02, min_points=10, print_progress=True))

max_label = labels.max()
print(f"point cloud has {max_label + 1} clusters")
colors = plt.get_cmap("tab20")(labels / (max_label if max_label > 0 else 1))
colors[labels < 0] = 0
pcd.colors = o3d.utility.Vector3dVector(colors[:, :3])
o3d.visualization.draw_geometries([pcd],
                                  zoom=0.455,
                                  front=[-0.4999, -0.1659, -0.8499],
                                  lookat=[2.1813, 2.0619, 2.0999],
                                  up=[0.1204, -0.9852, 0.1215])
```

```terminal
[Open3D DEBUG] Precompute Neighbours
[Open3D DEBUG] Done Precompute Neighbours
[Open3D DEBUG] Compute Clusters
[Open3D DEBUG] Done Compute Clusters: 10
point cloud has 10 clusters
```

![image](https://github.com/user-attachments/assets/ad50d119-fd76-46d8-9631-7610305fbefa)

![image](https://github.com/user-attachments/assets/15e3b279-9c6e-4607-a0f0-159cac56b3a5)


