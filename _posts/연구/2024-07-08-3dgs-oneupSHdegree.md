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
  - 0\~999까지는 sh 0까지 학습 (`features_rest`의 값이 모두 초기값 `0`입니다.)
    
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/dc77da22-6407-4a3c-97b9-7d4920acd41c)

  - 1000\~1999까지는 sh 0~1까지 학습 (`features_rest`의 값이 학습되기 시작함, 이 중에서도 sh_degree=1에 해당하는 3개의 features에 대해서만 학습이 되고, 3개 이후의 값은 여전히 초기값인 `0`입니다.)

    - 0\~2 features dim에 대해서는 학습되기 시작하여, `0`이 아닙니다.

       ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/785a52aa-d613-46bd-b16a-efa53d9f5565)

    - 3 이상 features dim에 대해서는 아직 학습이 진행되지 않아, 모두 `0`입니다.
    
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d6480c18-fab8-4382-9b62-74adf3ceeafd)

  - 2000\~2999까지는 sh 0~2까지 학습 (sh_degree=2에 해당하는 8개의 features에 대해서만 학습이 되고, 8개 이후의 값은 여전히 초기값인 `0`입니다.)
    
    - 0\~7 features dim에 대해서는 학습에 포함되므로, `0`이 아닙니다.
   
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fe52b193-4de9-40c8-8f22-d99b23335e0b)
   
    - 8 이상 features dim에 대해서는 아직 학습이 진행되지 않아, 모두 `0`입니다.
      
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/781e9a5c-e245-4353-8801-e163833c4033)

  - 3000\~3999까지는 sh 0~3까지 학습 (`self.max_sh_degree = 3`에 도달하였습니다.)

    - 0\~14 features dim에 대해서는 학습에 포함되므로, `0`이 아닙니다.

      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/221d04a1-dbaa-4100-a16e-a686a2cbac24)
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e89571e9-afce-4bed-8ce2-2df872727d20)

    - 15 이상 features dim에 대해서는 아직 학습이 진행되지 않아, 모두 `0`입니다.
   
      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2652e12e-59e8-49d2-883a-ef330a03bcb7)
    
  - 4000\~30000까지는 sh 0~3까지 학습 (`self.max_sh_degree = 3`에 도달하였으므로 `features_rest`에서 `sh`가 3보다 큰 feature에 대해서는 처음에 initialize한 `0`로 유지됩니다.)
 
    - 0\~14 features dim에 대해서는 학습에 포함되므로, `0`이 아닙니다.
    - 15 이상 features dim에 대해서는 `self.max_sh_degree = 3`을 초과하는 features이므로 `iterations`을 늘려도 학습이 진행되지 않아, 모두 `0`입니다.

      ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/35f9260a-6051-4a31-8e50-d95db47535fc)


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









