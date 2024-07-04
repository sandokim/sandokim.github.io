---
title: "[3D CV 연구] 3DGS self.xyz_gradient_accum"
last_modified_at: 2024-07-03
categories:
  - 연구
tags:
  - 연구
  - 3D CV20200
  - 3dgs
  - 3d gaussian splatting
  - implementation details
  - add_densification_stats
  - self.xyz_gradient_accum
  - viewspace_point_tensor
  - update_filter
  - denom
excerpt: "self.xyz_gradient_accum 설"
use_math: true
classes: wide
---

## `self.xyz_gradient_accum`은 `add_densification_stats`에서 업데이트 됩니다.

- `self.xyz_gradient_accum`은 `self.get_xyz.shape[0]`로 point cloud에서 point의 수인 `n_points 만큼 정의`됩니다.

```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:

...

    @property
    def get_xyz(self):
        return self._xyz

...

    def training_setup(self, training_args):
        self.percent_dense = training_args.percent_dense
        self.xyz_gradient_accum = torch.zeros((self.get_xyz.shape[0], 1), device="cuda")
```


- `self.xyz_gradient_accum`은 `prune_points()`에서 `valid_points_mask`로 masking이 가능합니다.
  - `_prune_optimizer()`에선 `valid_points_mask`로 prune된 `optimizable_tensors`는 point cloud에서 point 수인 `n_point`의 수에는 변화가 없고 단순히 `[mask]`하여 0으로 만듭니다.
  ```python
  
      def _prune_optimizer(self, mask):
          optimizable_tensors = {}
          for group in self.optimizer.param_groups:
              stored_state = self.optimizer.state.get(group['params'][0], None)
              if stored_state is not None:
                  stored_state["exp_avg"] = stored_state["exp_avg"][mask]
                  stored_state["exp_avg_sq"] = stored_state["exp_avg_sq"][mask]
  
                  del self.optimizer.state[group['params'][0]]
                  group["params"][0] = nn.Parameter((group["params"][0][mask].requires_grad_(True)))
                  self.optimizer.state[group['params'][0]] = stored_state
  
                  optimizable_tensors[group["name"]] = group["params"][0]
              else:
                  group["params"][0] = nn.Parameter(group["params"][0][mask].requires_grad_(True))
                  optimizable_tensors[group["name"]] = group["params"][0]
          return optimizable_tensors
  
      def prune_points(self, mask):
          valid_points_mask = ~mask
          optimizable_tensors = self._prune_optimizer(valid_points_mask)
  
          self._xyz = optimizable_tensors["xyz"]
          self._features_dc = optimizable_tensors["f_dc"]
          self._features_rest = optimizable_tensors["f_rest"]
          self._opacity = optimizable_tensors["opacity"]
          self._scaling = optimizable_tensors["scaling"]
          self._rotation = optimizable_tensors["rotation"]
  
          self.xyz_gradient_accum = self.xyz_gradient_accum[valid_points_mask]
  
          self.denom = self.denom[valid_points_mask]
          self.max_radii2D = self.max_radii2D[valid_points_mask]
  ```

- `self.xyz_gradient_accum`은 `densification_postfix()`에서 `cat_tensors_to_optimizer(d)`로 확장된 `self._xyz`에 해당하는 point cloud에서 point 수인 `n_points + 추가된 3d gaussian 수만큼 정의`됩니다.
- `densification_postfix`에서 `new_xyz`, `new_features_dc`, `new_features_rest`, `new_opacities`, `new_scaling`, `new_rotation`을 학습에 추가할 학습가능한 tensor로써 정의합니다
- 이는 새로 추가되는 3d gaussian들의 properties에 해당합니다.
- 이를 dictionary 형태인 `d`의 key값들로 정의하여 `cat_tensors_to_optimizer`로 넘겨줍니다.
- 기존의 `self.optimizer.param_groups`에서 새로 정의한 3d gaussian의 properties인 tensor인  `new_xyz`, `new_features_dc`, `new_features_rest`, `new_opacities`, `new_scaling`, `new_rotation`를 `extension_tensor`로 추가할 때는 `self.optimizer`에 넘겨준 각 param_group의 `"name"`에 해당하는 `"xyz"`, `"f_dc"`, `"f_rest"`, `"opacity"`, `"scaling"`, `"rotation`"에 concat 합니다.
- 처음에  `"xyz"`, `"f_dc"`, `"f_rest"`, `"opacity"`, `"scaling"`, `"rotation`"는 `l`로 정의하여 각 param_group별로 `"name"`을 지어준 것을 확인할 수 있습니다.
- 여기서 `'params'`는 각 param_group에 해당합니다.
- **즉, 우리는 3d gaussian의 properties를 param_group 별로 쪼개어 학습률을 다르게 설정하여 학습할 수 있습니다.**
```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:

...

      def training_setup(self, training_args):
        self.percent_dense = training_args.percent_dense
        self.xyz_gradient_accum = torch.zeros((self.get_xyz.shape[0], 1), device="cuda")
        self.denom = torch.zeros((self.get_xyz.shape[0], 1), device="cuda")

        l = [
            {'params': [self._xyz], 'lr': training_args.position_lr_init * self.spatial_lr_scale, "name": "xyz"},
            {'params': [self._features_dc], 'lr': training_args.feature_lr, "name": "f_dc"},
            {'params': [self._features_rest], 'lr': training_args.feature_lr / 20.0, "name": "f_rest"},
            {'params': [self._opacity], 'lr': training_args.opacity_lr, "name": "opacity"},
            {'params': [self._scaling], 'lr': training_args.scaling_lr, "name": "scaling"},
            {'params': [self._rotation], 'lr': training_args.rotation_lr, "name": "rotation"}
        ]

        self.optimizer = torch.optim.Adam(l, lr=0.0, eps=1e-15)

...

    def cat_tensors_to_optimizer(self, tensors_dict):
        optimizable_tensors = {}
        for group in self.optimizer.param_groups:
            assert len(group["params"]) == 1
            extension_tensor = tensors_dict[group["name"]]
            stored_state = self.optimizer.state.get(group['params'][0], None)
            if stored_state is not None:

                stored_state["exp_avg"] = torch.cat((stored_state["exp_avg"], torch.zeros_like(extension_tensor)), dim=0)
                stored_state["exp_avg_sq"] = torch.cat((stored_state["exp_avg_sq"], torch.zeros_like(extension_tensor)), dim=0)

                del self.optimizer.state[group['params'][0]]
                group["params"][0] = nn.Parameter(torch.cat((group["params"][0], extension_tensor), dim=0).requires_grad_(True))
                self.optimizer.state[group['params'][0]] = stored_state

                optimizable_tensors[group["name"]] = group["params"][0]
            else:
                group["params"][0] = nn.Parameter(torch.cat((group["params"][0], extension_tensor), dim=0).requires_grad_(True))
                optimizable_tensors[group["name"]] = group["params"][0]

        return optimizable_tensors

    def densification_postfix(self, new_xyz, new_features_dc, new_features_rest, new_opacities, new_scaling, new_rotation):
        d = {"xyz": new_xyz,
        "f_dc": new_features_dc,
        "f_rest": new_features_rest,
        "opacity": new_opacities,
        "scaling" : new_scaling,
        "rotation" : new_rotation}

        optimizable_tensors = self.cat_tensors_to_optimizer(d)

...
  
```
  


```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:

...

    def add_densification_stats(self, viewspace_point_tensor, update_filter):
        self.xyz_gradient_accum[update_filter] += torch.norm(viewspace_point_tensor.grad[update_filter,:2], dim=-1, keepdim=True)
        self.denom[update_filter] += 1
```
