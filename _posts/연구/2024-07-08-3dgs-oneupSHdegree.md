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
---

# 3dgs에서 1,000 iters마다 SH의 degree를 올려서 `features_rest`의 값을 추가적으로 학습합니다.

- 3dgs/train.py에서 `iteration` 1000마다 `oneupSHdegree()`를 하고 있습니다.
  
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

- `oneupSHdegree()`는 `self.max_sh_degree`보다 작을 경우, `self.active_sh_degree`를 `1`만큼 올려줍니다.
- 만약 `self.max_sh_degree`인 `self.sh_degree`가 default인 3이고, `iterations`이 30,000이라면
  - 0~999까지는 sh 0까지 학습
  - 1000~1999까지는 sh 0~1까지 학습
  - 2000~2999까지는 sh 0~2까지 학습
  - 3000~3999까지는 sh 0~3까지 학습 (`self.max_sh_degree = 3`에 도달하였음)
  - 4000~30000까지는 sh 0~3까지 학습 (`self.max_sh_degree = 3`에 도달하였으므로 `features_rest`에서 `sh`가 3보다 큰 feature에 대해서는 처음에 initialize한 `0`로 유지됨)

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









