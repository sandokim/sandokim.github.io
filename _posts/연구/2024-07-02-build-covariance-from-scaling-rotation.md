---
title: "[3D CV 연구] 3DGS build_covariance_from_scaling_rotation"
last_modified_at: 2024-06-28
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - implementation details
  - build_covariance
  - scaling
  - scaling_modifier
  - rotation
excerpt: "3DGS SH2RGB"
use_math: true
classes: wide
---

## 3D Gaussian을 표현하는 covariance matrix를 scaling & rotation으로 만들어줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/857b5a13-c828-4860-829d-888942474543)

$RS = L$, $S^TR^T=(RS)^T=L^T$ 이므로 $RSS^TR^T=LL^T$이고 코드에서 actual_covariance는 $RSS^TR^T$에 해당합니다.

```python
            L = build_scaling_rotation(scaling_modifier * scaling, rotation)
            actual_covariance = L @ L.transpose(1, 2)
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a1b61388-b2da-4655-9ea0-1b5ba2518db2)

```python
# 3dgs/scene/gaussian_model.py

from utils.general_utils import strip_symmetric, build_scaling_rotation

class GaussianModel:

    def setup_functions(self):
        def build_covariance_from_scaling_rotation(scaling, scaling_modifier, rotation):
            L = build_scaling_rotation(scaling_modifier * scaling, rotation)
            actual_covariance = L @ L.transpose(1, 2)
            symm = strip_symmetric(actual_covariance)
            return symm
        
        self.scaling_activation = torch.exp
        self.scaling_inverse_activation = torch.log

        self.covariance_activation = build_covariance_from_scaling_rotation

        self.opacity_activation = torch.sigmoid
        self.inverse_opacity_activation = inverse_sigmoid

        self.rotation_activation = torch.nn.functional.normalize
```

```python
# utils/general_utils

def build_scaling_rotation(s, r):
    L = torch.zeros((s.shape[0], 3, 3), dtype=torch.float, device="cuda")
    R = build_rotation(r)

    L[:,0,0] = s[:,0]
    L[:,1,1] = s[:,1]
    L[:,2,2] = s[:,2]

    L = R @ L
    return L
```

- `build_rotation(r)`에서 `r`에 해당하는 것은 `self._rotation # shape (n_points, 4)`인 quaternion입니다.
```python
# utils/general_utils
def build_rotation(r):
    norm = torch.sqrt(r[:,0]*r[:,0] + r[:,1]*r[:,1] + r[:,2]*r[:,2] + r[:,3]*r[:,3])

    q = r / norm[:, None]

    R = torch.zeros((q.size(0), 3, 3), device='cuda')

    r = q[:, 0]
    x = q[:, 1]
    y = q[:, 2]
    z = q[:, 3]

    R[:, 0, 0] = 1 - 2 * (y*y + z*z)
    R[:, 0, 1] = 2 * (x*y - r*z)
    R[:, 0, 2] = 2 * (x*z + r*y)
    R[:, 1, 0] = 2 * (x*y + r*z)
    R[:, 1, 1] = 1 - 2 * (x*x + z*z)
    R[:, 1, 2] = 2 * (y*z - r*x)
    R[:, 2, 0] = 2 * (x*z - r*y)
    R[:, 2, 1] = 2 * (y*z + r*x)
    R[:, 2, 2] = 1 - 2 * (x*x + y*y)
    return R
```

- 이때 quaternion에 해당하는 `r`은 모든 points에 대해 회전성분이 없는 [0,0,0,1]로 초기화 됩니다.
  - 아래에서 `rots`는 n_points 수인 `fused_point_cloud.shape[0]`와 quaternion을 구성하는 4개의 성분으로 정의하고 모든 points에 대해 [0,0,0,1]로 초기화합니다.
  - `rots # shape (n_points, 4)`
  ```python
      def create_from_pcd(self, pcd : BasicPointCloud, spatial_lr_scale : float):
        self.spatial_lr_scale = spatial_lr_scale
        fused_point_cloud = torch.tensor(np.asarray(pcd.points)).float().cuda()
        fused_color = RGB2SH(torch.tensor(np.asarray(pcd.colors)).float().cuda())
        features = torch.zeros((fused_color.shape[0], 3, (self.max_sh_degree + 1) ** 2)).float().cuda()
        features[:, :3, 0 ] = fused_color
        features[:, 3:, 1:] = 0.0

        print("Number of points at initialisation : ", fused_point_cloud.shape[0])

        dist2 = torch.clamp_min(distCUDA2(torch.from_numpy(np.asarray(pcd.points)).float().cuda()), 0.0000001)
        scales = torch.log(torch.sqrt(dist2))[...,None].repeat(1, 3)
        rots = torch.zeros((fused_point_cloud.shape[0], 4), device="cuda")
        rots[:, 0] = 1

        opacities = inverse_sigmoid(0.1 * torch.ones((fused_point_cloud.shape[0], 1), dtype=torch.float, device="cuda"))

        self._xyz = nn.Parameter(fused_point_cloud.requires_grad_(True))
        self._features_dc = nn.Parameter(features[:,:,0:1].transpose(1, 2).contiguous().requires_grad_(True))
        self._features_rest = nn.Parameter(features[:,:,1:].transpose(1, 2).contiguous().requires_grad_(True))
        self._scaling = nn.Parameter(scales.requires_grad_(True))
        self._rotation = nn.Parameter(rots.requires_grad_(True))
        self._opacity = nn.Parameter(opacities.requires_grad_(True))
        self.max_radii2D = torch.zeros((self.get_xyz.shape[0]), device="cuda")
  ```
- 혹은 학습후 `output.ply`에서 quaternion인 `rots`를 불러오면 다음과 같은 형태입니다.
  - `rots # shape (n_points, 4)`
  - `rot_names`:

    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2af1a453-a6b4-42e8-91fe-6a15b5bb09d2)
    
  - `rot_0`, `rot_1`, `rot_2`, `rot_3`:

    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ec422b5a-836a-4558-a3e6-8153dd787d01)


```python
# utils/general_utils.py

def strip_lowerdiag(L):
    uncertainty = torch.zeros((L.shape[0], 6), dtype=torch.float, device="cuda")

    uncertainty[:, 0] = L[:, 0, 0]
    uncertainty[:, 1] = L[:, 0, 1]
    uncertainty[:, 2] = L[:, 0, 2]
    uncertainty[:, 3] = L[:, 1, 1]
    uncertainty[:, 4] = L[:, 1, 2]
    uncertainty[:, 5] = L[:, 2, 2]
    return uncertainty

def strip_symmetric(sym):
    return strip_lowerdiag(sym)
```




