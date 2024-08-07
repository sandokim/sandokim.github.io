---
title: "[3D CV 연구] 3DGS Spherical Harmonics oneupSHdegree"
last_modified_at: 2024-07-08
categories:
  - 연구
tags:
  - 연구
  - 3d gaussian splatting
  - 3dgs
  - implementation details
  - spherical harmonics
  - sh degree
  - oneupSHdegree
excerpt: "3dgs spherical harmonics"
use_math: true
classes: wide
comments: true
---

# 3dgs에서 1,000 iters마다 SH의 degree를 올려서 `features_rest`의 값을 추가적으로 학습합니다.

## 3dgs/train.py에서 `iteration` 1000마다 `oneupSHdegree()`를 하고 있습니다.
  
```python
# 3dgs/train.py

def training(dataset, opt, pipe, testing_iterations, saving_iterations, checkpoint_iterations, checkpoint, debug_from):

...

        # Every 1000 its we increase the levels of SH up to a maximum degree
        if iteration % 1000 == 0:
            gaussians.oneupSHdegree()

...

        render_pkg = render(viewpoint_cam, gaussians, pipe, bg)
```

## `self.max_sh_degree`에 따른 `self._features_dc`, `self._features_rest`의 shape
- 만약 `self.max_sh_degree`인 `self.sh_degree`가 default인 3이면 그에 맞게 `features`가 정의가 됩니다.
- 이때 `features`의 shape은 `(n_points, RGB, (self.max_sh_degree + 1) ** 2) = (n_points, 3, (self.max_sh_degree + 1) ** 2)`입니다.
- `features`는 `self._features_dc`와 `self._features_rest`로 분리되고, `transpose(1, 2)`되면서 shape이 다음과 같아집니다.
  - `self._features_dc # (n_points, sh 0 degree, RGB) = (n_points, 1, 3)`
    
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/93d2ce7b-c25e-42f8-a503-fcdc155f325a)
    
  - `self._features_rest # (n_points, (sh 0 ~ sh max degree) - sh 0 degree, RGB) = (n_points, (self.max_sh_degree + 1) ** 2 - 1, 3) = (n_points, (3 + 1) ** 2 - 1, 3) = (n_points, 15, 3)`
    
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8feb8223-160b-40b6-b059-154745ace22c)


## `self._features_dc`, `self._feature_rest`의 초기값
- `self._features_dc`는 `pcd.colors`의 `RGB2SH`함수로 초기화
- `self._features_rest`는 `0`로 초기화

```python
# 3dgs/scene/gaussian_model.py

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
```

## `oneupSHdegree()`는 `self.max_sh_degree`보다 작을 경우, `self.active_sh_degree`를 `1`만큼 올려줍니다.
***`self.max_sh_degree = 3`일때, `iterations`이 30,000이면, 1,000 iters 마다의 `features_rest`는 다음과 같이 학습에 추가됩니다.***
  - 0\~999까지는 sh 0만 학습 (`features_rest`의 값이 모두 초기값 `0`입니다.)
    
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/dc77da22-6407-4a3c-97b9-7d4920acd41c)

  - 1000\~1999까지는 `self.active_sh_degree = 1`이므로 sh 1을 추가로 학습 (`features_rest`의 값이 학습되기 시작함, 이 중에서도 `(self.active_sh_degree + 1) ** 2 - 1 = (1 + 1) ** 2 - 1 = 3`인 3개의 features에 대해서만 학습이 되고, 3개 이후의 값은 여전히 초기값인 `0`입니다.)

    - 0\~2 features dim에 대해서는 학습되기 시작하여, `0`이 아닙니다.

       ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/785a52aa-d613-46bd-b16a-efa53d9f5565)

    - 3 이상 features dim에 대해서는 아직 학습이 진행되지 않아, 모두 `0`입니다.
    
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d6480c18-fab8-4382-9b62-74adf3ceeafd)

  - 2000\~2999까지는 `self.active_sh_degree = 2`이므로 sh 2을 추가로 학습 (`(self.active_sh_degree + 1) ** 2 - 1 = (2 + 1) ** 2 - 1 = 8`인 8개의 features에 대해서만 학습이 되고, 8개 이후의 값은 여전히 초기값인 `0`입니다.)
    
    - 0\~7 features dim에 대해서는 학습에 포함되므로, `0`이 아닙니다.
   
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fe52b193-4de9-40c8-8f22-d99b23335e0b)
   
    - 8 이상 features dim에 대해서는 아직 학습이 진행되지 않아, 모두 `0`입니다.
      
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/781e9a5c-e245-4353-8801-e163833c4033)

  - 3000\~3999까지는 `self.active_sh_degree = 3`이므로 sh 3을 추가로 학습 (`(self.active_sh_degree + 1) ** 2 - 1 = (3 + 1) ** 2 - 1 = 15`인 15개의 features에 대해서 모두 학습이 됩니다.)

    - 0\~14 features dim에 대해서 모두 학습에 포함되므로, `0`이 아닙니다.

      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/221d04a1-dbaa-4100-a16e-a686a2cbac24)
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e89571e9-afce-4bed-8ce2-2df872727d20)
    
  - 4000\~30000에서는 sh 0, 1, 2, 3에 대해 계속 학습 (`self.active_sh_degree`가 `self.max_sh_degree = 3`에 도달하였고, 앞서 초기화 할 때, `features_rest`에서 sh가 3보다 큰 features dim은 애초에 존재하지 않습니다.)

    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/46accb62-7430-41c0-9d79-a4c3dee8da0e)


```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:
...
    def oneupSHdegree(self):
        if self.active_sh_degree < self.max_sh_degree:
            self.active_sh_degree += 1
```

```python
# 3dgs/arguments/__init__.py

class ModelParams(ParamGroup): 
    def __init__(self, parser, sentinel=False):
        self.sh_degree = 3
...
```

- `override_color = None`, `pipe.convert_SHs_python = None`이 default이므로, `shs = pc.get_features` 부분을 사용합니다. (`pc`는 point cloud에 해당)
- `get_features`는 `features_dc`와 `features_rest`를 feature dimension에서 concat합니다.
  - `features_dc`의 shape # (n_points, n_features_dc, RGB)
  - `features_rest`의  shape # (n_points, n_features_rest, RGB)

```python
# 3dgs/gaussian_renderer/__init__.py

def render(viewpoint_camera, pc : GaussianModel, pipe, bg_color : torch.Tensor, scaling_modifier = 1.0, override_color = None):

...

    # If precomputed colors are provided, use them. Otherwise, if it is desired to precompute colors
    # from SHs in Python, do it. If not, then SH -> RGB conversion will be done by rasterizer.
    shs = None
    colors_precomp = None
    if override_color is None:
        if pipe.convert_SHs_python:
            shs_view = pc.get_features.transpose(1, 2).view(-1, 3, (pc.max_sh_degree+1)**2)
            dir_pp = (pc.get_xyz - viewpoint_camera.camera_center.repeat(pc.get_features.shape[0], 1))
            dir_pp_normalized = dir_pp/dir_pp.norm(dim=1, keepdim=True)
            sh2rgb = eval_sh(pc.active_sh_degree, shs_view, dir_pp_normalized)
            colors_precomp = torch.clamp_min(sh2rgb + 0.5, 0.0)
        else:
            shs = pc.get_features # default에서 이 부분을 사용합니다.
    else:
        colors_precomp = override_color

```

```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:
...
    @property
    def get_features(self):
        features_dc = self._features_dc
        features_rest = self._features_rest
        return torch.cat((features_dc, features_rest), dim=1)
```









