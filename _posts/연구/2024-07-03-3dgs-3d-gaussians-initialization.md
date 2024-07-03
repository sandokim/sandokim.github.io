---
title: "[3D CV 연구] 3DGS 3D gaussians initialization"
last_modified_at: 2024-07-03
categories:
  - 연구
tags:
  - 연구
  - 3D CV20200
  - 3dgs
  - 3d gaussian splatting
  - implementation details
  - 3dgs initialization
  - knn
  - simple knn
  - 최근접 알고리즘
  - distCUDA2
  - scale
excerpt: "3D Gaussian들의 properties를 initilize하는 법을 정리합니다."
use_math: true
classes: wide
---

## 3D gaussian의 x,y,z의 scale을 초기화 할때, K-Nearest Neighbor (knn) 알고리즘 사용
- 3개의 nearest neighbor points를 사용하여 평균거리를 계산하고, 그 값으로 3D gaussian의 scale을 isotropic으로 초기화 합니다.
- **knn**은 `simple_knn` 라이브러리의 `distCUDA2`로 구현되어 있는 것을 사용합니다.

```python
# 3dgs/scene/gaussian_model.py

from simple_knn._C import distCUDA2

class GaussianModel:

...

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





