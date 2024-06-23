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

### 아래의 open3d official document 예제를 보면 확실히 vertex와 triangle이 어떻게 정의되는지 이해됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2854550f-481b-4013-bd4e-01beb00926d8)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/50f106e0-3a42-49f1-aba0-679f7130d9da)

