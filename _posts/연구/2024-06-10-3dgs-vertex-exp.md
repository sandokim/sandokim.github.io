---
title: "[3D CV 연구] 3DGS input & output .ply vertex properties & Meshlab Vert & Spherical Harmonics (SH) & Mesh"
last_modified_at: 2024-07-02
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - mesh
  - point cloud
  - vertex
  - Meshlab Vert
  - 정점
  - spherical harmonics
excerpt: "3DGS input & output .ply properties & Meshlab Vert & Spherical Harmonics (SH) & Mesh"
use_math: true
classes: wide
---

**Meshlab**에서 표현되는 Vert의 의미는 일반적으로 point cloud 또는 3D mesh에 있어서 각각의 점(정점, vertex)를 의미합니다. 이 점들은 공간 내 위치와 해당 위치의 속성(i.e. color, normal vector)을 나타냅니다.

3dgs에서 각 Vert(정점) 데이터는 다음과 같습니다.

### 1) Random initialization으로 생성한 input.ply는 다음을 포함합니다.

- x, y, z (position)
- nx, ny, nz (normal)
- red, green, blue (color)

첫 5개의 Vert(정점) 데이터를 출력하면 다음과 같습니다

#### input.ply

```python
import numpy as np
from plyfile import PlyData

def fetchPly(path):
    plydata = PlyData.read(path)
    vertices = plydata['vertex']
    positions = np.vstack([vertices['x'], vertices['y'], vertices['z']]).T
    colors = np.vstack([vertices['red'], vertices['green'], vertices['blue']]).T / 255.0
    normals = np.vstack([vertices['nx'], vertices['ny'], vertices['nz']]).T

    # 첫 5개의 요소 출력
    print("First 5 elements:")
    print("x:", vertices['x'][:5])
    print("y:", vertices['y'][:5])
    print("z:", vertices['z'][:5])
    print("nx:", vertices['nx'][:5])
    print("ny:", vertices['ny'][:5])
    print("nz:", vertices['nz'][:5])
    print("red:", vertices['red'][:5])
    print("green:", vertices['green'][:5])
    print("blue:", vertices['blue'][:5])

# PLY 파일 경로를 지정합니다.
ply_file_path = "C:/Users/MNL/KHS/gaussian-splatting/output/realsense_d435/180deg@15/input.ply"

# PLY 파일을 읽어오고 첫 5개 요소를 출력합니다.
fetchPly(ply_file_path)
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9e952ea5-62df-47aa-a41c-6464021c5757)

### 2) Depth camera로 촬영하여 얻은 input.ply 데이터는 다음을 포함합니다.

- x, y, z (position)
- nx, ny, nz (normal)
- red, green, blue (color)

첫 5개의 Vert(정점) 데이터를 출력하면 다음과 같습니다.

#### input.ply

```python
import numpy as np
from plyfile import PlyData

def fetchPly(path):
    plydata = PlyData.read(path)
    vertices = plydata['vertex']
    positions = np.vstack([vertices['x'], vertices['y'], vertices['z']]).T
    colors = np.vstack([vertices['red'], vertices['green'], vertices['blue']]).T / 255.0
    normals = np.vstack([vertices['nx'], vertices['ny'], vertices['nz']]).T

    # 첫 5개의 요소 출력
    print("First 5 elements:")
    print("x:", vertices['x'][:5])
    print("y:", vertices['y'][:5])
    print("z:", vertices['z'][:5])
    print("nx:", vertices['nx'][:5])
    print("ny:", vertices['ny'][:5])
    print("nz:", vertices['nz'][:5])
    print("red:", vertices['red'][:5])
    print("green:", vertices['green'][:5])
    print("blue:", vertices['blue'][:5])

# PLY 파일 경로를 지정합니다.
ply_file_path = "C:/Users/MNL/KHS/gaussian-splatting/output/realsense_d435/180deg@15_cam_poses_bbox_pcd/input.ply"

# PLY 파일을 읽어오고 첫 5개 요소를 출력합니다.
fetchPly(ply_file_path)
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/166a6b04-8c8e-400e-a435-c795f8c3c3e9)

***즉, random initialization으로 생성하는 input.ply와 depth camera로 depth 정보까지 얻어 생성한 input.ply가 가지는 정보의 종류는 같습니다.***

### 3) 3dgs output인 point_cloud.ply를 구성하는 요소와 default shape은 다음과 같습니다.

- x, y, z (position) # (n_points, 3)
- f_dc_0, f_dc_1, f_dc_2 (Spherical Harmonics 0번째 band의 rgb에서 채널마다 다른 색상 값) # (n_points, 1, 3)
- f_rest_0 ~ f_rest_44 (사용 가능한 다양한 추가 속성, i.e. Spherical Harmonics로 사용가능함) # (n_points, 15, 3)
  - **f_rest shape에서 앞의 15는 max_sh_degree = 3일 때 15개, 뒤에 3은 rgb 채널을 의미**
- opacity (불투명도) # (n_points, 1)
- scale_n (스케일 정보) # (n_points, 3)
- rot_n (회전 정보) # (n_points, 4) # quaternion이라서 4

3dgs의 output인 point_cloud.ply의 첫 번째 Vert(정점) 데이터를 출력하면 다음과 같습니다. 
- 불러오는 코드는 gaussian_model.py에서 불러오는 코드를 그대로 사용하여 print문만 출력하였습니다.
- `plydata.elements[0]`에서 0번째만 인덱싱하는 이유는 하나의 point cloud data만 저장하고 있는 ply 파일이기 때문입니다.
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b122dd59-39e4-4710-a7f1-955ccbe25ebf)
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/261cbc4f-7765-4768-ad85-b69ee555ca4b)


#### output.ply
```python
import numpy as np
import torch
from torch import nn
from plyfile import PlyData

class GaussianModel:
    def __init__(self, max_sh_degree):
        self.max_sh_degree = max_sh_degree

    def load_ply(self, path):
        plydata = PlyData.read(path)
        # print("plydata.elements:", plydata.elements)
        # print("plydata.elements[0]:", plydata.elements[0])
        
        xyz = np.stack((np.asarray(plydata.elements[0]["x"]),
                        np.asarray(plydata.elements[0]["y"]),
                        np.asarray(plydata.elements[0]["z"])),  axis=1)
        opacities = np.asarray(plydata.elements[0]["opacity"])[..., np.newaxis]

        features_dc = np.zeros((xyz.shape[0], 3, 1))
        features_dc[:, 0, 0] = np.asarray(plydata.elements[0]["f_dc_0"])
        features_dc[:, 1, 0] = np.asarray(plydata.elements[0]["f_dc_1"])
        features_dc[:, 2, 0] = np.asarray(plydata.elements[0]["f_dc_2"])

        extra_f_names = [p.name for p in plydata.elements[0].properties if p.name.startswith("f_rest_")]
        extra_f_names = sorted(extra_f_names, key=lambda x: int(x.split('_')[-1]))
        assert len(extra_f_names) == 3 * (self.max_sh_degree + 1) ** 2 - 3
        features_extra = np.zeros((xyz.shape[0], len(extra_f_names)))
        for idx, attr_name in enumerate(extra_f_names):
            features_extra[:, idx] = np.asarray(plydata.elements[0][attr_name])
        features_extra = features_extra.reshape((features_extra.shape[0], 3, (self.max_sh_degree + 1) ** 2 - 1))

        scale_names = [p.name for p in plydata.elements[0].properties if p.name.startswith("scale_")]
        scale_names = sorted(scale_names, key=lambda x: int(x.split('_')[-1]))
        scales = np.zeros((xyz.shape[0], len(scale_names)))
        for idx, attr_name in enumerate(scale_names):
            scales[:, idx] = np.asarray(plydata.elements[0][attr_name])

        rot_names = [p.name for p in plydata.elements[0].properties if p.name.startswith("rot")]
        # rot_names = sorted(rot_names, key=lambda x: int(x.split('_')[-1]))
        rots = np.zeros((xyz.shape[0], len(rot_names)))
        for idx, attr_name in enumerate(rot_names):
            rots[:, idx] = np.asarray(plydata.elements[0][attr_name])

        self._xyz = nn.Parameter(torch.tensor(xyz, dtype=torch.float, device="cuda").requires_grad_(True))
        self._features_dc = nn.Parameter(torch.tensor(features_dc, dtype=torch.float, device="cuda").transpose(1, 2).contiguous().requires_grad_(True))
        self._features_rest = nn.Parameter(torch.tensor(features_extra, dtype=torch.float, device="cuda").transpose(1, 2).contiguous().requires_grad_(True))
        self._opacity = nn.Parameter(torch.tensor(opacities, dtype=torch.float, device="cuda").requires_grad_(True))
        self._scaling = nn.Parameter(torch.tensor(scales, dtype=torch.float, device="cuda").requires_grad_(True))
        self._rotation = nn.Parameter(torch.tensor(rots, dtype=torch.float, device="cuda").requires_grad_(True))

        self.active_sh_degree = self.max_sh_degree

    def print_parameters(self):
        parameters = {
            "XYZ": self._xyz.cpu().detach().numpy(),
            "Features DC": self._features_dc.cpu().detach().numpy(),
            "Features Rest": self._features_rest.cpu().detach().numpy(),
            "Opacity": self._opacity.cpu().detach().numpy(),
            "Scaling": self._scaling.cpu().detach().numpy(),
            "Rotation": self._rotation.cpu().detach().numpy(),
        }
        
        for key, value in parameters.items():
            print(f"{key} shape: {value.shape}")
            print(f"{key} first 5 elements:\n{value[:5]}\n")

# 클래스 외부에서 객체 생성 및 PLY 파일 로드 및 출력

# max_sh_degree 값을 지정합니다. 예를 들어 3으로 설정합니다.
model = GaussianModel(max_sh_degree=3)

# PLY 파일 경로를 지정합니다.
ply_file_path = "C:/Users/MNL/KHS/gaussian-splatting/output/realsense_d435/180deg@15_cam_poses_bbox_pcd/point_cloud/iteration_30000/point_cloud.ply"

# PLY 파일을 읽어옵니다.
model.load_ply(ply_file_path)

# 각 파라미터 값을 출력합니다.
model.print_parameters()
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/83cad61a-a3c7-4406-b43f-db8245b0f695)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/657dbd42-0a63-416c-b9b1-6fe9d348e355)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/29bde592-e182-440d-b92c-870fb261df69)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/454ed660-bad3-4e8c-8e37-0ed55920c7f2)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c109a1f6-28bb-4c2d-901f-79f9ed3ab535)

Meshlab에서 .ply를 visualize해보면 

input은 color에 대해 Vert를 가지고 있으나, output.ply는 color에 대해 Vert를 가지고 있지 않습니다.

1) Random initialization input.ply
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/bda5d8a8-1ec5-403d-8e4c-18a71378f1b2)

2) Depth camera로 얻은 input.ply
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/97d1ce00-d53d-4bd0-8d6a-0d00edb7e279)

3) 3dgs output인 point_cloud.ply
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4b63bd79-95da-48b6-82d8-5c4b682907d8)

3dgs code에서 output.ply는 point_cloud.ply 이름으로 저장됩니다.

### Color에 대한 Vert
- **input.ply**: 각 정점(Vertex)에 색상 정보 red, green, blue (color)가 포함되어 있습니다. 정점마다 고유한 색상 정보가 있어, 메쉬 또는 포인트 클라우드가 컬러로 시각화됩니다.
- **output.ply**: 각 정점(Vertex)에 색상 정보 red, green, blue (color)가 포함되어 있지 않습니다. 색상 정보가 없으므로, Meshlab에서 시각화할 때 회색조로 표시될 가능성이 큽니다.
  
### Shading에 대한 Vert
**input.ply와 output.ply 모두**: 각 정점(Vertex)에 법선 벡터(normal) 정보가 포함되어 있으나 0으로 초기화 됩니다.
- 법선 벡터는 표면의 방향을 나타내며, 조명과 음영(Shading)을 계산하는 데 사용됩니다.
- 법선 벡터는 조명과 음영 효과를 통해 3D 모델을 보다 현실적으로 시각화하는 데 도움을 줍니다.

----------

## Spherical Harmonics는 orignal 3dgs paper & code에서 사용하고 있지 않습니다. RGB로만 색상 값을 최적화 했습니다.

Q) Spherical Harmonics를 사용하지 않을 경우 색상을 표현하는 방법은 무엇일까요?

A) 모든 방향에 대해 일정한 색상을 가지는 0번째 band를 사용하는 것입니다. 이를 통해 단순히 RGB 색상 값을 최적화하였습니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/73

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/800fb8e8-2077-4c08-905a-67cf36da93ea)

----------

### 3DGS는 point cloud를 생성하지, mesh를 생성하지는 않습니다.

3dgs의 output은 point_cloud를 .ply 형태로 내보냅니다. 저자는 mesh 자체를 3dgs로부터 directly produce하는 것은 계획하고 있지 않다고 밝혔습니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/125

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/acbc3eac-64ab-4a5e-8905-74bc07f44627)

하지만 3dgs로 얻은 .ply 파일인 point cloud에 poisson reconstruction을 적용하면 mesh를 생성해낼 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5d526f7f-e625-4891-b5b4-11835e4940e2)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3d640c8a-3a02-4918-a852-3e556d29056c)
