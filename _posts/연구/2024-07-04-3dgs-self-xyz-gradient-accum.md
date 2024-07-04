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

## `self.xyz_gradient_accum`은 `self.get_xyz.shape[0]`로 point cloud에서 point의 수인 `n_points 만큼 정의`됩니다.

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

## pruning은 optimizable_tensors에 대해 각 param_group을 masking하여 수행합니다.

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

## 새로운 3D gaussian properties인 tensor를 추가할 때는, 기존의 optimizer에 각 properties `"name"`에 해당하는는 param_group별로 concat하여 추가합니다.

- `self.xyz_gradient_accum`은 `densification_postfix()`에서 `cat_tensors_to_optimizer(d)`로 확장된 `self._xyz`에 해당하는 point cloud에서 point 수인 `n_points + 추가된 3d gaussian 수만큼 정의`됩니다.
- `densification_postfix`에서 `new_xyz`, `new_features_dc`, `new_features_rest`, `new_opacities`, `new_scaling`, `new_rotation`을 학습에 추가할 학습가능한 tensor로써 정의합니다
- 이는 새로 추가되는 3d gaussian들의 properties에 해당합니다.
- 이를 dictionary 형태인 `d`의 key값들로 정의하여 `cat_tensors_to_optimizer`로 넘겨줍니다.
- 기존의 `self.optimizer.param_groups`에서 새로 정의한 3d gaussian의 properties인 tensor인  `new_xyz`, `new_features_dc`, `new_features_rest`, `new_opacities`, `new_scaling`, `new_rotation`를 `extension_tensor`로 추가할 때는 `self.optimizer`에 넘겨준 각 param_group의 `"name"`에 해당하는 `"xyz"`, `"f_dc"`, `"f_rest"`, `"opacity"`, `"scaling"`, `"rotation`"에 concat 합니다.
- 처음에  `"xyz"`, `"f_dc"`, `"f_rest"`, `"opacity"`, `"scaling"`, `"rotation`"는 `l`로 정의하여 각 param_group별로 `"name"`을 지어준 것을 확인할 수 있습니다.
- 여기서 `'params'`는 각 param_group에 해당합니다.
- **즉, 우리는 3d gaussian의 properties를 param_group 별로 쪼개어 학습률을 다르게 설정하여 학습할 수 있습니다.**
- 그리고 새로운 3d gaussian properties를 tensor인  `new_xyz`, `new_features_dc`, `new_features_rest`, `new_opacities`, `new_scaling`, `new_rotation`로 정의하여 처음의 `l`을 넘겨받은 Adam optimizer에 param_group별로 concat하여 추가해줍니다.
- 정리하면 다음과 같습니다.
  -  `new_xyz`는 `"name" : "xyz"`인 param_group에 추가
  -  `new_features_dc`는 `"name" : "f_dc"`인 param_group에 추가
  -  `new_features_rest`는 `"name" : "f_rest"`인 param_group에 추가
  -  `new_opacities`는 `"name" : "opacity"`인 param_group에 추가
  -  `new_scaling`는 `"name" : "scaling"`인 param_group에 추가
  -  `new_rotation`는 `"name" : "rotation"`인 param_group에 추가
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

## 왜 densification 후에 xyz_gradient_accum을 재정의하는가?

**`densification_postfix` 함수는 새로운 점들이 추가된 후 이 점들을 옵티마이저에 추가하고, 그래디언트 누적 값들을 0으로 초기화합니다.**

`self.xyz_gradient_accum`을 매 `densification` 과정 후에 재정의하여 최적화를 수행하는 이유는 그래디언트 누적 값을 초기화하고 새로 추가된 점들에 대해 올바른 그래디언트를 계산하기 위함입니다. `densification` 후에 **새로운 점들이 추가되거나 기존 점들이 제거될 수 있으므로, 기존의 그래디언트 누적 값을 초기화하여 다음 단계에서 정확한 그래디언트를 다시 누적할 수 있게 합니다.**

### 1. 새로운 점 추가
densification 과정에서 새로운 점들이 추가됩니다. 이 새로운 점들은 기존의 그래디언트 누적 값이 없기 때문에, 초기화된 상태에서 시작해야 합니다. 새로 추가된 점들은 아직 그래디언트를 축적하지 않았기 때문에, 초기화된 self.xyz_gradient_accum을 통해 새로 누적되는 그래디언트를 저장합니다.

### 2. 기존 점 제거
densification 과정에서 일부 점들이 제거될 수 있습니다. 이 경우, 제거된 점들의 그래디언트 누적 값은 더 이상 필요하지 않으므로, 초기화하여 남아있는 점들에 대해서만 정확한 그래디언트를 누적할 수 있도록 합니다.

### 3. 그래디언트의 정확성 유지
그래디언트 누적 값을 초기화하지 않으면, 이전 단계에서 누적된 그래디언트 값이 계속 남아있게 되어 새로운 점들에 대한 올바른 그래디언트를 계산하는 데 문제가 생길 수 있습니다. 따라서, densification 후에 xyz_gradient_accum을 초기화하여 다음 최적화 단계에서 정확한 그래디언트를 다시 누적할 수 있도록 합니다.

#### `densification_postfix`는 `densify_and_clone`과 `densify_and_split`에서 사용됩니다.

**즉, `densification` 후에 새로운 점들이 추가되거나 기존 점들이 제거된 후에, 기존의 그래디언트 누적 값을 0으로 초기화하여 다음 단계에서 정확한 그래디언트를 다시 누적할 수 있게 합니다.**

```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:

...

    def densification_postfix(self, new_xyz, new_features_dc, new_features_rest, new_opacities, new_scaling, new_rotation):
        d = {"xyz": new_xyz,
        "f_dc": new_features_dc,
        "f_rest": new_features_rest,
        "opacity": new_opacities,
        "scaling" : new_scaling,
        "rotation" : new_rotation}

        optimizable_tensors = self.cat_tensors_to_optimizer(d)
        self._xyz = optimizable_tensors["xyz"]
        self._features_dc = optimizable_tensors["f_dc"]
        self._features_rest = optimizable_tensors["f_rest"]
        self._opacity = optimizable_tensors["opacity"]
        self._scaling = optimizable_tensors["scaling"]
        self._rotation = optimizable_tensors["rotation"]

        self.xyz_gradient_accum = torch.zeros((self.get_xyz.shape[0], 1), device="cuda")
        self.denom = torch.zeros((self.get_xyz.shape[0], 1), device="cuda")
        self.max_radii2D = torch.zeros((self.get_xyz.shape[0]), device="cuda")


    def densify_and_split(self, grads, grad_threshold, scene_extent, N=2):
        n_init_points = self.get_xyz.shape[0]
        # Extract points that satisfy the gradient condition
        padded_grad = torch.zeros((n_init_points), device="cuda")
        padded_grad[:grads.shape[0]] = grads.squeeze()
        selected_pts_mask = torch.where(padded_grad >= grad_threshold, True, False)
        selected_pts_mask = torch.logical_and(selected_pts_mask,
                                              torch.max(self.get_scaling, dim=1).values > self.percent_dense*scene_extent)

        stds = self.get_scaling[selected_pts_mask].repeat(N,1)
        means =torch.zeros((stds.size(0), 3),device="cuda")
        samples = torch.normal(mean=means, std=stds)
        rots = build_rotation(self._rotation[selected_pts_mask]).repeat(N,1,1)
        new_xyz = torch.bmm(rots, samples.unsqueeze(-1)).squeeze(-1) + self.get_xyz[selected_pts_mask].repeat(N, 1)
        new_scaling = self.scaling_inverse_activation(self.get_scaling[selected_pts_mask].repeat(N,1) / (0.8*N))
        new_rotation = self._rotation[selected_pts_mask].repeat(N,1)
        new_features_dc = self._features_dc[selected_pts_mask].repeat(N,1,1)
        new_features_rest = self._features_rest[selected_pts_mask].repeat(N,1,1)
        new_opacity = self._opacity[selected_pts_mask].repeat(N,1)

        self.densification_postfix(new_xyz, new_features_dc, new_features_rest, new_opacity, new_scaling, new_rotation)

        prune_filter = torch.cat((selected_pts_mask, torch.zeros(N * selected_pts_mask.sum(), device="cuda", dtype=bool)))
        self.prune_points(prune_filter)

    def densify_and_clone(self, grads, grad_threshold, scene_extent):
        # Extract points that satisfy the gradient condition
        selected_pts_mask = torch.where(torch.norm(grads, dim=-1) >= grad_threshold, True, False)
        selected_pts_mask = torch.logical_and(selected_pts_mask,
                                              torch.max(self.get_scaling, dim=1).values <= self.percent_dense*scene_extent)
        
        new_xyz = self._xyz[selected_pts_mask]
        new_features_dc = self._features_dc[selected_pts_mask]
        new_features_rest = self._features_rest[selected_pts_mask]
        new_opacities = self._opacity[selected_pts_mask]
        new_scaling = self._scaling[selected_pts_mask]
        new_rotation = self._rotation[selected_pts_mask]

        self.densification_postfix(new_xyz, new_features_dc, new_features_rest, new_opacities, new_scaling, new_rotation)

    def densify_and_prune(self, max_grad, min_opacity, extent, max_screen_size):
        grads = self.xyz_gradient_accum / self.denom
        grads[grads.isnan()] = 0.0

        self.densify_and_clone(grads, max_grad, extent)
        self.densify_and_split(grads, max_grad, extent)

...

```

## viewspace의 gradient는 render하는 view에서 viewing frustum에 포함되는 points를 `viewspace_point_tensor`로 정의하여 `self.xyz_gradient_accum`의 gradient 업데이트에 추가합니다.

```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:

...

    def add_densification_stats(self, viewspace_point_tensor, update_filter):
        self.xyz_gradient_accum[update_filter] += torch.norm(viewspace_point_tensor.grad[update_filter,:2], dim=-1, keepdim=True)
        self.denom[update_filter] += 1
```

```python
# 3dgs/train.py

def training(dataset, opt, pipe, testing_iterations, saving_iterations, checkpoint_iterations, checkpoint, debug_from):

...

        render_pkg = render(viewpoint_cam, gaussians, pipe, bg)
        image, viewspace_point_tensor, visibility_filter, radii = render_pkg["render"], render_pkg["viewspace_points"], render_pkg["visibility_filter"], render_pkg["radii"]

...

        with torch.no_grad():

...

            # Densification
            if iteration < opt.densify_until_iter:
                # Keep track of max radii in image-space for pruning
                gaussians.max_radii2D[visibility_filter] = torch.max(gaussians.max_radii2D[visibility_filter], radii[visibility_filter])
                gaussians.add_densification_stats(viewspace_point_tensor, visibility_filter)


```
