---
title: "[Mesh] How Gaussians turn into Primitives in SuGaR model"
last_modified_at: 2024-06-20
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - gaussians
  - primitives
  - triangle
  - diamond
  - square (quad)
excerpt: "SuGaR에서 Gaussians이 어떻게 Primitives (diamond 또는 square(qaud))로 변형되는지 알아봅시다."
use_math: true
classes: wide
---

# SuGaR에서 Gaussians이 어떻게 Primitives (diamond 또는 square(quad))로 변형되는지 알아봅시다.

SuGaR 모델에서는 Gaussian splats를 더 정밀한 다각형(primitive polygon) 형태로 대체하여 3D 표면을 보다 정확하게 표현합니다. 

Gaussian splats가 다이아몬드(diamond) 또는 사각형(square) 형태의 다각형으로 변형되는 과정을 알아봅시다.

## 다이아몬드와 사각형의 차이점

다이아몬드와 사각형은 정점의 개수는 같지만, 정점의 배열 방식과 그에 따른 구조가 다릅니다. 다이아몬드는 중심을 기준으로 위아래, 좌우로 배열된 입체적 형태를 가지며, 사각형은 평면 상에서 네 방향으로 배열된 평면적 형태를 가집니다.

여기서 사용된 square는 일반적으로 3D 그래픽스에서 "quad"라고 불리는 사각형을 의미합니다. quad는 네 개의 정점을 가진 다각형을 의미하며, 이 코드에서는 Gaussian splats를 대체하기 위해 사용됩니다.

### `sugar_model.py`에서 diamond와 sqaure primitive 정의는 다음과 같이 합니다.

```python
# Primitive polygon that will be used to replace the gaussians
self.primitive_types = primitive_types
self._diamond_verts = torch.Tensor(
        [[0., -1., 0.], [0., 0, 1.], 
        [0., 1., 0.], [0., 0., -1.]]
        ).to(nerfmodel.device)
self._square_verts = torch.Tensor(
        [[0., -1., 1.], [0., 1., 1.], 
        [0., 1., -1.], [0., -1., -1.]]
        ).to(nerfmodel.device)

if primitive_types == 'diamond':
    self.primitive_verts = self._diamond_verts  # Shape (n_vertices_per_gaussian, 3)
elif primitive_types == 'square':
    self.primitive_verts = self._square_verts  # Shape (n_vertices_per_gaussian, 3)

self.primitive_triangles = torch.Tensor(
    [[0, 2, 1], [0, 3, 2]]
    ).to(nerfmodel.device).long()  # Shape (n_triangles_per_gaussian, 3)
self.primitive_border_edges = torch.Tensor(
    [[0, 1], [1, 2], [2, 3], [3, 0]]
    ).to(nerfmodel.device).long()  # Shape (n_edges_per_gaussian, 2)
self.n_vertices_per_gaussian = len(self.primitive_verts)
self.n_triangles_per_gaussian = len(self.primitive_triangles)
self.n_border_edges_per_gaussian = len(self.primitive_border_edges)
self.triangle_scale = triangle_scale
```

### 이를 visualize하는 코드를 작성하여 봅시다.

```python
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d.art3d import Poly3DCollection

# Convert tensors to numpy arrays
_diamond_verts = np.array([[0., -1., 0.], [0., 0, 1.], [0., 1., 0.], [0., 0., -1.]])
_square_verts = np.array([[0., -1., 1.], [0., 1., 1.], [0., 1., -1.], [0., -1., -1.]])

primitive_triangles = np.array([[0, 2, 1], [0, 3, 2]])
primitive_border_edges = np.array([[0, 1], [1, 2], [2, 3], [3, 0]])

# Function to plot vertices
def plot_vertices(vertices, ax, color='b'):
    ax.scatter(vertices[:, 0], vertices[:, 1], vertices[:, 2], color=color, s=100)

# Function to plot edges
def plot_edges(vertices, edges, ax, color='r'):
    for edge in edges:
        edge_verts = vertices[edge]
        ax.plot(edge_verts[:, 0], edge_verts[:, 1], edge_verts[:, 2], color=color)

# Function to plot triangles
def plot_triangles(vertices, triangles, ax, color='g'):
    for tri in triangles:
        tri_verts = vertices[tri]
        poly3d = [[tri_verts[0], tri_verts[1], tri_verts[2]]]
        ax.add_collection3d(Poly3DCollection(poly3d, facecolors=color, linewidths=1, edgecolors='k', alpha=.25))

# Plotting
fig = plt.figure(figsize=(18, 6))

# Plot diamond vertices and edges
ax1 = fig.add_subplot(131, projection='3d')
plot_vertices(_diamond_verts, ax1, color='b')
plot_edges(_diamond_verts, primitive_border_edges, ax1, color='r')
plot_triangles(_diamond_verts, primitive_triangles, ax1, color='g')
ax1.set_title('Diamond')
ax1.set_xlabel('X')
ax1.set_ylabel('Y')
ax1.set_zlabel('Z')

# Plot square vertices and edges
ax2 = fig.add_subplot(132, projection='3d')
plot_vertices(_square_verts, ax2, color='b')
plot_edges(_square_verts, primitive_border_edges, ax2, color='r')
plot_triangles(_square_verts, primitive_triangles, ax2, color='g')
ax2.set_title('Square')
ax2.set_xlabel('X')
ax2.set_ylabel('Y')
ax2.set_zlabel('Z')

# Separate plot for _diamond_verts and _square_verts visualization
ax3 = fig.add_subplot(133, projection='3d')
plot_vertices(_diamond_verts, ax3, color='b')
plot_vertices(_square_verts, ax3, color='r')
ax3.set_title('Diamond (blue) and Square (red) Vertices')
ax3.set_xlabel('X')
ax3.set_ylabel('Y')
ax3.set_zlabel('Z')

plt.show()
```
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e923a360-582c-465b-a8fe-9c3211eeb81d)

### 요약
- 다이아몬드와 사각형의 정점 배열 순서:
  - 다이아몬드는 중앙을 기준으로 위아래, 좌우로 배열됩니다.
  - 사각형은 평면 상에서 네 방향으로 배열됩니다.

- 형성된 구조:
  - 다이아몬드는 입체적인 구조를 형성합니다.
  - 사각형(quad)은 평면적인 구조를 형성합니다.
  - 이 두 가지 형태는 정점의 배열 순서와 그에 따른 삼각형의 배치가 달라서 서로 다른 구조를 형성합니다. 이를 통해 Gaussian splats를 다양한 형태로 변환하여 더 정밀하고 복잡한 3D 모델을 표현할 수 있습니다.
    
    
 

