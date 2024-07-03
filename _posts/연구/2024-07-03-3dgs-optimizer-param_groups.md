---
title: "[3D CV 연구] 3DGS self.optimizer.param_groups로 파라미터별로 다른 lr 설정"
last_modified_at: 2024-07-03
categories:
  - 연구
tags:
  - 연구
  - 3D CV20200
  - 3dgs
  - 3d gaussian splatting
  - implementation details
  - self.optimizer.param_groups
  - cat_tensors_to_optimizer
excerpt: "optimizer.param_groups로 파라미터별로 다른 lr 설정이 가능합니다."
use_math: true
classes: wide
---

## optimizer.param_groups로 optimize하는 parameter들의 learning rate 조작이 가능합니다.

- **모델 파라미터의 업데이트는 PyTorch에서 optimizer에 의해 처리됩니다.**
- optimizer를 정의할 때 모델 파라미터를 서로 다른 그룹으로 분할할 수 있으며, 이를 param groups라고 합니다. 
- 각 param group은 서로 다른 optimizer 설정을 가질 수 있습니다. 예를 들어, 한 그룹의 파라미터는 learning rate를 0.1로 설정하고, 다른 그룹은 learning rate를 0.01로 설정할 수 있습니다.

## PyTorch에서 모델 파라미터 그룹 설정 및 학습률 조절

### 모델 정의
- PyTorch에서 Sequential 컨테이너를 사용하여 간단한 신경망을 정의할 수 있습니다. 예를 들어, 아래와 같은 모델을 정의해 보겠습니다:

```python
import torch
import torch.nn as nn
import torch.optim as optim

# 모델의 파라미터 정의
model = nn.Sequential(
    nn.Linear(2, 2),
    nn.ReLU(),
    nn.Linear(2, 1),
)
```

### 파라미터 그룹 설정
- **모델의 각 레이어에 대해 다른 학습률을 설정하기 위해 파라미터 그룹을 사용할 수 있습니다.** 
- 예를 들어, 첫 번째 선형 레이어와 두 번째 선형 레이어의 학습률을 다르게 설정해 보겠습니다.

```python
# 각 레이어의 파라미터를 다른 그룹으로 분류
optimizer = optim.SGD([
    {'params': model[0].parameters(), 'lr': 0.01},  # 첫 번째 Linear 레이어의 학습률을 0.01로 설정
    {'params': model[2].parameters(), 'lr': 0.001}  # 두 번째 Linear 레이어의 학습률을 0.001로 설정
], lr=0.01)
```

- model[0]은 첫 번째 Linear 레이어를 나타내며, 이 레이어의 파라미터는 학습률 0.01로 설정됩니다.
- model[2]은 두 번째 Linear 레이어를 나타내며, 이 레이어의 파라미터는 학습률 0.001로 설정됩니다.

### 학습률 확인
- 각 파라미터 그룹에 설정된 학습률을 확인하려면 다음과 같이 할 수 있습니다:

```python
# 첫 번째 param group의 learning rate 출력
print("첫 번째 그룹 lr: ", optimizer.param_groups[0]['lr'])
# 두 번째 param group의 learning rate 출력
print("두 번째 그룹 lr: ", optimizer.param_groups[1]['lr'])
```

- 이런 방식으로 PyTorch에서 모델의 학습률을 세밀하게 조절하여 효율적인 학습을 수행할 수 있습니다.


## 3dgs에서 optimizer

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
        self.xyz_scheduler_args = get_expon_lr_func(lr_init=training_args.position_lr_init*self.spatial_lr_scale,
                                                    lr_final=training_args.position_lr_final*self.spatial_lr_scale,
                                                    lr_delay_mult=training_args.position_lr_delay_mult,
                                                    max_steps=training_args.position_lr_max_steps)

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
```



### Reference
- [What exactly is meant by param_groups in pytorch?](https://stackoverflow.com/questions/73629330/what-exactly-is-meant-by-param-groups-in-pytorch)

