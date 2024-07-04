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
  - fused color
  - RGB2SH
  - features
  - features_dc
  - features_rest
excerpt: "3D Gaussian들의 properties를 initilize하는 법을 정리합니다."
use_math: true
classes: wide
---

## 3D gaussian의 x,y,z의 scale을 초기화 할때, K-Nearest Neighbor (knn) 알고리즘 사용
- 3개의 nearest neighbor points를 사용하여 평균거리를 계산하고, 그 값으로 3D gaussian의 scale을 isotropic으로 초기화 합니다.
- **knn**은 `simple_knn` 라이브러리에서`spatial.cu`에서 `SimpleKNN::knn`를 사용하여 `distCUDA2`로 구현되어 있는 것을 사용합니다.
```cuda
#include "spatial.h"
#include "simple_knn.h"

torch::Tensor
distCUDA2(const torch::Tensor& points)
{
  const int P = points.size(0);

  auto float_opts = points.options().dtype(torch::kFloat32);
  torch::Tensor means = torch::full({P}, 0.0, float_opts);
  
  SimpleKNN::knn(P, (float3*)points.contiguous().data<float>(), means.contiguous().data<float>());

  return means;
}
```
- `SimpleKNN::knn`은 `simple_knn.cu`에 정의되어 있으며, 최근접 `K`개의 이웃을 구할땐 `updateKBest<K>` 호출을 통해 이루어집니다.
- 이때 `updateKBest<3>`을 호출하도록 하드코딩되어 있고 이 부분에서 **3개의 최근접 이웃을 사용**하고 있음을 확인 가능합니다.
- 결론적으로 아래 코드에서 `dist2`는 `distCUDA2`로 point마다 3개의 nearest neighbor point의 평균 거를 의미합니다.
- `dist2`로 3d gaussian의 scale을 isotropic하게 initialize하고 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/031fa214-d612-487f-956c-bf2923c6695b)

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

## point cloud로부터 n_points 개수만큼 RGB channel 각각에 대한 sh의 계수를 정의하여 초기화 합니다.

- fused_color는 점 구름의 색상 정보를 Spherical Harmonics(SH)로 변환한 것입니다. 이 변환은 일반적으로 점 구름의 색상 정보를 더 잘 표현하고, 다양한 조명 조건에서의 반응을 모델링하는 데 사용됩니다.

```python
fused_color = RGB2SH(torch.tensor(np.asarray(pcd.colors)).float().cuda())
```

- 여기서 RGB2SH는 RGB 색상 값을 Spherical Harmonics(SH) 계수로 변환하는 함수입니다. 변환 후, fused_color는 다음과 같은 구조를 가집니다:
  - **fused_color.shape**: (`num_points`, `3`, `* (max_sh_degree + 1) ** 2`) # (n_points, RGB, SH 계수의 수)
    - **num_points**: 점의 개수
    - **3**: RGB 색상 채널 (Red, Green, Blue)
    - (max_sh_degree + 1) ** 2: SH 계수의 수

- SH 변환은 주로 저차수에서 고차수로 진행되며, 각 색상 채널에 대해 여러 계수를 갖게 됩니다.


### `features_dc`, `features_rest`
- `features_dc`는 각 점의 색상 정보를 Spherical Harmonics 계수로 변환하여 저장한 텐서입니다.
- `features_dc`는 초기화 시 RGB 색상의 DC(Direct Component) 계수만을 포함하며, 나머지 SH 계수들은 features_rest에 저장됩니다.
- `features`는 `(n_points, RGB 3 channel, sh 계수의 수)`의 shape으로 0으로 초기화됩니다.
- `features[:, :3, 0 ] = fused_color`는 sh 0번째 계수에 대한 값을 `RGB2SH`로 넣어줍니다.
- `features[:, 3:, 1:] = 0.0`는 사실상 RGB 3 channel에 대한 인덱싱을 넘어갔으므로 아무효과가 없습니다. 단순히 sh 1번째~44번째 계수에 대한 초기값이 0.0이라는 것을 명시하는 것으로 보입니다.

```python
# 3dgs/scene/gaussian_model.py

...

    def create_from_pcd(self, pcd : BasicPointCloud, spatial_lr_scale : float):
        self.spatial_lr_scale = spatial_lr_scale
        fused_point_cloud = torch.tensor(np.asarray(pcd.points)).float().cuda()
        fused_color = RGB2SH(torch.tensor(np.asarray(pcd.colors)).float().cuda())
        features = torch.zeros((fused_color.shape[0], 3, (self.max_sh_degree + 1) ** 2)).float().cuda()
        features[:, :3, 0 ] = fused_color # (n_points, RGB 3 channel, sh 0번째 계수)
        features[:, 3:, 1:] = 0.0 # (n_points, RGB 3 channel의 인덱싱 범위를 넘어감, sh 1번째~44번째 계수)

...

    def load_ply(self, path):
        plydata = PlyData.read(path)

        xyz = np.stack((np.asarray(plydata.elements[0]["x"]),
                        np.asarray(plydata.elements[0]["y"]),
                        np.asarray(plydata.elements[0]["z"])),  axis=1)
        opacities = np.asarray(plydata.elements[0]["opacity"])[..., np.newaxis]

        features_dc = np.zeros((xyz.shape[0], 3, 1))
        features_dc[:, 0, 0] = np.asarray(plydata.elements[0]["f_dc_0"])
        features_dc[:, 1, 0] = np.asarray(plydata.elements[0]["f_dc_1"])
        features_dc[:, 2, 0] = np.asarray(plydata.elements[0]["f_dc_2"])

...

```
























