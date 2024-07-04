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
    {'params': model[0].parameters(), 'lr': 0.01, 'name': 'layer1'},  # 첫 번째 Linear 레이어
    {'params': model[2].parameters(), 'lr': 0.001, 'name': 'layer2'}  # 두 번째 Linear 레이어
], lr=0.01)
```
- group["name"]은 각 파라미터 그룹에 이름을 부여하여 식별할 수 있도록 합니다. 
- model[0]은 첫 번째 Linear 레이어를 나타내며, 이 레이어의 파라미터는 학습률 0.01로 설정됩니다.
- model[2]은 두 번째 Linear 레이어를 나타내며, 이 레이어의 파라미터는 학습률 0.001로 설정됩니다.

### 학습률 확인
- 각 파라미터 그룹에 설정된 학습률을 확인하려면 다음과 같이 할 수 있습니다:

```python
# 첫 번째 param group 출력
print("첫 번째 그룹:", optimizer.param_groups[0])
# 두 번째 param group 출력
print("두 번째 그룹:", optimizer.param_groups[1])

```

- 출력값:
```css
첫 번째 그룹: {'params': [Parameter containing: tensor([...])], 'lr': 0.01, 'name': 'layer1'}
두 번째 그룹: {'params': [Parameter containing: tensor([...])], 'lr': 0.001, 'name': 'layer2'}
```

- 첫 번째 param group은 params, lr, name 키를 포함하고 있습니다. params 키는 이 그룹에 포함된 파라미터를 나타내고, lr은 학습률, name은 그룹의 이름입니다.
- 두 번째 param group도 동일한 구조를 가지며, 각각의 설정이 다릅니다.
- 이런 방식으로 PyTorch에서 모델의 학습률을 세밀하게 조절하여 효율적인 학습을 수행할 수 있습니다.

### 여러 파라미터가 있는 경우 예시
- PyTorch에서 optimizer의 param group에 여러 파라미터를 포함할 수도 있습니다.
- 이를 통해 특정 하이퍼파라미터(예: learning rate)를 동일하게 적용하고자 하는 경우에 유용합니다.
  
아래는 여러 파라미터를 하나의 param group에 포함시키는 예시입니다:

```python
import torch
import torch.nn as nn
import torch.optim as optim

# 모델 정의
model = nn.Sequential(
    nn.Linear(2, 2),
    nn.ReLU(),
    nn.Linear(2, 1),
    nn.Linear(2, 1)
)

# 여러 파라미터를 하나의 그룹으로 설정
optimizer = optim.SGD([
    {'params': model[0].parameters(), 'lr': 0.01, 'name': 'layer1'},  # 첫 번째 Linear 레이어
    {'params': model[2].parameters(), 'lr': 0.001, 'name': 'layer2'},  # 두 번째 Linear 레이어
    {'params': [model[0].weight, model[2].weight, model[3].weight], 'lr': 0.01, 'name': 'shared_weights'}  # 여러 파라미터 그룹
], lr=0.01)

# 세 번째 param group 출력
print("세 번째 그룹:", optimizer.param_groups[2])
```

- model은 네 개의 레이어를 가진 신경망입니다.
- optimizer는 세 개의 param group을 가지고 있습니다.
- 첫 번째 그룹(layer1): model[0]의 파라미터를 포함하며, 학습률이 0.01로 설정됩니다.
- 두 번째 그룹(layer2): model[2]의 파라미터를 포함하며, 학습률이 0.001로 설정됩니다.
- **세 번째 그룹(shared_weights): model[0].weight, model[2].weight, model[3].weight 파라미터를 포함하며, 학습률이 0.01로 설정됩니다.**
  
- 출력값:
```css
세 번째 그룹: {'params': [Parameter containing: tensor([...]), Parameter containing: tensor([...]), Parameter containing: tensor([...])], 'lr': 0.01, 'name': 'shared_weights'}
```
- 위 출력은 **세 번째 param group에 여러 파라미터가 포함**되어 있는 것을 보여줍니다. **이 그룹 내 모든 파라미터는 동일한 학습률 0.01을 사용합니다.**

### Optimizer의 param_groups 초기화 및 초기 상태

- **PyTorch에서 optimizer를 초기화할 때, 각 param_group에 대한 텐서의 초기 상태는 보통 0으로 시작합니다.**
- 이는 특히 Adam과 같은 모멘텀 기반 Optimizer에서 중요합니다. 
- 이런 Optimizer들은 파라미터 업데이트를 위해 과거 그라디언트의 지수 이동 평균(exponential moving average)을 추적하기 때문입니다.

```python
import torch.optim as optim

# 모델 정의
model = torch.nn.Linear(2, 2)

# Optimizer 초기화
optimizer = optim.Adam(model.parameters(), lr=0.01)
```

- 이 초기화 과정에서 Adam 옵티마이저는 각 파라미터에 대해 다음과 같은 상태 값을 생성합니다:

- exp_avg (그라디언트의 지수 이동 평균): 0으로 초기화
- exp_avg_sq (그라디언트 제곱의 지수 이동 평균): 0으로 초기화

#### optimizer.state의 초기 상태
- 초기화된 옵티마이저의 상태는 다음과 같습니다:

```python
for group in optimizer.param_groups:
    for param in group['params']:
        state = optimizer.state[param]
        state['exp_avg'] = torch.zeros_like(param)
        state['exp_avg_sq'] = torch.zeros_like(param)
```


--------

## 3dgs에서 `cat_tensors_to_optimizer` 함수를 해석해봅시다.

- 3D Gaussian Splatting에서는 아래와 같이 param_groups를 정의하여 gaussian property 별로 optimize를 수행합니다.
- opacity에 대한 threshold를 설정하여 pruning하는 것, mask를 씌워 pruning하는 것도 모두 `optimizer.param_groups`에서 각 `group`의 `name`에 해당하는   `optimizer`에 대한 업데이트를 진행하게 됩니다.

```python
l = [
{'params': [self._xyz], 'lr': training_args.position_lr_init * self.spatial_lr_scale, "name": "xyz"},
{'params': [self._features_dc], 'lr': training_args.feature_lr, "name": "f_dc"},
{'params': [self._features_rest], 'lr': training_args.feature_lr / 20.0, "name": "f_rest"},
{'params': [self._opacity], 'lr': training_args.opacity_lr, "name": "opacity"},
{'params': [self._scaling], 'lr': training_args.scaling_lr, "name": "scaling"},
{'params': [self._rotation], 'lr': training_args.rotation_lr, "name": "rotation"}
]

self.optimizer = torch.optim.Adam(l, lr=0.0, eps=1e-15)
```

### `tensors_dict`와 `group["name"]`을 통한 텐서 추가 과정 요약

- 이 과정은 `tensors_dict`에서 확장할 텐서를 가져와 `optimizer`의 각 `param group`에 추가하는 작업을 수행합니다.
- 여기서 중요한 점은 `group["name"]`을 사용하여 `tensors_dict`에서 올바른 확장 텐서를 가져오는 것입니다.
- Optimizer가 초기화될 때, 각 파라미터의 상태는 0으로 초기화됩니다.
- **`optimizer.state`는 각 파라미터의 모멘텀, 지수 이동 평균 등을 포함하며, 초기값은 0입니다.**
- `cat_tensors_to_optimizer` 메서드에서 `stored_state`가 존재하면 확장 텐서를 추가하고, 초기 상태를 업데이트합니다.
- `stored_state`가 없으면 새로운 파라미터를 생성하고 초기 상태를 설정합니다.
- 이렇게 초기화된 상태는 모델 학습 초기에 `optimizer`가 적절하게 파라미터를 업데이트할 수 있도록 합니다.

### 과정 요약

#### 1. Param Group 순회:
```python
for group in self.optimizer.param_groups:
    assert len(group["params"]) == 1
```
- 각 param group을 순회하면서, 해당 그룹에 하나의 파라미터만 있는지 확인합니다.
  - 각 항목은 다음과 같이 구성되어 있습니다:
  - params: 하나의 파라미터(리스트 형태로 감싸져 있음).
  - lr: 학습률.
  - name: 파라미터 그룹의 이름.
  ```python
  l = [
      {'params': [self._xyz], 'lr': training_args.position_lr_init * self.spatial_lr_scale, "name": "xyz"},
      {'params': [self._features_dc], 'lr': training_args.feature_lr, "name": "f_dc"},
      {'params': [self._features_rest], 'lr': training_args.feature_lr / 20.0, "name": "f_rest"},
      {'params': [self._opacity], 'lr': training_args.opacity_lr, "name": "opacity"},
      {'params': [self._scaling], 'lr': training_args.scaling_lr, "name": "scaling"},
      {'params': [self._rotation], 'lr': training_args.rotation_lr, "name": "rotation"}
  ]

  self.optimizer = torch.optim.Adam(l, lr=0.0, eps=1e-15)
  ```
  ```python
  for group in self.optimizer.param_groups:
    assert len(group["params"]) == 1
  ```
  - 이 조건이 성립하는 이유는, 각 param group의 params 항목이 항상 하나의 파라미터 리스트를 포함하고 있기 때문입니다. 예를 들어, 첫 번째 항목을 보면:
  ```css
  {'params': [self._xyz], 'lr': training_args.position_lr_init * self.spatial_lr_scale, "name": "xyz"}
  ```
  - 여기서 `params`는 `[self._xyz]`로, **리스트 안에 단 하나의 파라미터 `self._xyz`만 포함**하고 있습니다.
  - 다른 항목들도 마찬가지로 params 리스트에 하나의 파라미터만 포함하고 있습니다.
  - 하나의 파라미터만 포함하므로 이 파라미터에 접근할 때는 `group["params"][0]`로 0번째로 인덱싱합니다.

#### 2. `tensors_dict`에서 확장 텐서 가져오기:
```python
    extension_tensor = tensors_dict[group["name"]]
```
- `group["name"]`을 사용하여 `tensors_dict`에서 일치하는 키의 확장 텐서를 가져옵니다.
- 예를 들어, `group["name"]`이 `'layer1'`이면, `tensors_dict['layer1']`의 값을 `extension_tensor`로 가져옵니다.

#### 3. 파라미터 확장 및 추가:
```python
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
```
- 가져온 `extension_tensor`를 사용하여 기존 파라미터를 확장합니다.
- 확장된 파라미터는 학습 가능한 파라미터로 설정되고, `optimizer`의 상태가 갱신됩니다.
- 초기화 설명
  #### 1. `stored_state`가 `None`인 경우:
   ```python
    group["params"][0] = nn.Parameter(torch.cat((group["params"][0], extension_tensor), dim=0).requires_grad_(True))
    optimizable_tensors[group["name"]] = group["params"][0]
   ```
   - 새로운 파라미터가 생성됩니다:
     - `torch.cat((group["params"][0], extension_tensor), dim=0)`를 통해 기존 파라미터와 확장 텐서가 결합됩니다.
     - 이 결합된 텐서는 새로운 파라미터로 정의됩니다.
   - `requires_grad_(True)`를 호출하여 이 파라미터가 학습 가능하도록 설정합니다.
   - 이 새로운 파라미터는 `optimizable_tensors` 딕셔너리에 추가됩니다.

  #### 2. `stored_state`가 존재하는 경우:
    ```python
    stored_state["exp_avg"] = torch.cat((stored_state["exp_avg"], torch.zeros_like(extension_tensor)), dim=0)
    stored_state["exp_avg_sq"] = torch.cat((stored_state["exp_avg_sq"], torch.zeros_like(extension_tensor)), dim=0)
    del self.optimizer.state[group['params'][0]]
    group["params"][0] = nn.Parameter(torch.cat((group["params"][0], extension_tensor), dim=0).requires_grad_(True))
    self.optimizer.state[group['params'][0]] = stored_state
    optimizable_tensors[group["name"]] = group["params"][0]
    ```
    - 기존 파라미터의 상태(`stored_state`)가 존재하는 경우:
      - `exp_avg`와 `exp_avg_sq`를 확장하여 추가합니다.
      - 기존 파라미터를 삭제하고 확장된 텐서로 대체합니다.
      - 새로운 파라미터를 `optimizer` 상태에 추가하여 갱신합니다.
    - 이렇게 하면 기존의 학습 상태를 유지하면서 새로운 파라미터가 추가됩니다.


### 최종 요약
- `group["name"]`을 통해 `tensors_dict`에서 올바른 확장 텐서를 가져옵니다.
- **가져온 확장 텐서를 기존 파라미터에 추가하여 학습 가능한 파라미터로 설정합니다.**
- 이 과정을 통해 각 `param group`은 `tensors_dict`에서 가져온 확장 텐서를 포함하게 되어, 학습 과정에서 사용할 수 있게 됩니다.
- 전체 `cat_tensor_to_optimizer` 코드는 아래와 같습니다.
  
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

## optimizer.param_groups의 "name'을 알고 있으면, 우리가 원하는 3d gaussian들의 property만 골라서 학습률 업데이트가 가능합니다.

- 모든 3d gaussian들의 property는 이처럼 optimizer의 param_group으로 나누고

```python
l = [
{'params': [self._xyz], 'lr': training_args.position_lr_init * self.spatial_lr_scale, "name": "xyz"},
{'params': [self._features_dc], 'lr': training_args.feature_lr, "name": "f_dc"},
{'params': [self._features_rest], 'lr': training_args.feature_lr / 20.0, "name": "f_rest"},
{'params': [self._opacity], 'lr': training_args.opacity_lr, "name": "opacity"},
{'params': [self._scaling], 'lr': training_args.scaling_lr, "name": "scaling"},
{'params': [self._rotation], 'lr': training_args.rotation_lr, "name": "rotation"}
]

self.optimizer = torch.optim.Adam(l, lr=0.0, eps=1e-15)
```

- `"name"`으로 특정 param_group에 접근하여 optimize 합니다.
- 아래 예시에서는 `xyz`의 `"name"`을 가진 param_group에서 `iteration`을 조건으로 `lr`을 업데이트합니다.
  - 구체적으로는 `self.xyz_scheduler_args`에 저장된 스케줄링 함수를 호출하여 현재 `iteration`에 대한 학습률을 계산합니다.
  - 계산된 학습률(`lr`)을 해당 파라미터 그룹의 학습률로 설정합니다.최종적으로 새로운 학습률(`lr`)을 반환합니다.

```python
# 3dgs/scene/gaussian_model.py

class GaussianModel:

...

    def training_setup(self, training_args):

        ...

        self.xyz_scheduler_args = get_expon_lr_func(lr_init=training_args.position_lr_init*self.spatial_lr_scale,
                                                    lr_final=training_args.position_lr_final*self.spatial_lr_scale,
                                                    lr_delay_mult=training_args.position_lr_delay_mult,
                                                    max_steps=training_args.position_lr_max_steps)
...

    def update_learning_rate(self, iteration):
        ''' Learning rate scheduling per step '''
        for param_group in self.optimizer.param_groups:
            if param_group["name"] == "xyz":
                lr = self.xyz_scheduler_args(iteration)
                param_group['lr'] = lr
                return lr
```

## 3dgs에서 `replace_tensor_to_optimizer` 함수를 해석해봅시다.

- `replace_tensor_to_optimizer` 함수는 지정된 이름을 가진 텐서를 옵티마이저의 파라미터 그룹에서 교체하는 기능을 합니다. 이 함수는 특히 모델의 파라미터를 새로운 텐서로 교체하고, 옵티마이저의 상태를 새 텐서에 맞춰 초기화하는 데 사용됩니다.
- 예를 들어, 학습 도중 특정 파라미터를 재설정하거나 새로운 값으로 교체해야 하는 경우 사용할 수 있습니다.

```python
def replace_tensor_to_optimizer(self, tensor, name):
    optimizable_tensors = {}
    for group in self.optimizer.param_groups:
        if group["name"] == name:
            stored_state = self.optimizer.state.get(group['params'][0], None)
            stored_state["exp_avg"] = torch.zeros_like(tensor)
            stored_state["exp_avg_sq"] = torch.zeros_like(tensor)

            del self.optimizer.state[group['params'][0]]
            group["params"][0] = nn.Parameter(tensor.requires_grad_(True))
            self.optimizer.state[group['params'][0]] = stored_state

            optimizable_tensors[group["name"]] = group["params"][0]
    return optimizable_tensors
```

- 파라미터 설명
  - tensor: 새로 교체될 텐서입니다.
  - name: 교체할 파라미터 그룹의 이름입니다.

### 동작 과정
1. 초기화: 최종적으로 반환할 딕셔너리를 초기화합니다.
```python
optimizable_tensors = {}
```

2. 옵티마이저 파라미터 그룹 순회: 옵티마이저의 파라미터 그룹을 하나씩 순회합니다.
```python
for group in self.optimizer.param_groups:
```

3. 지정된 이름과 일치하는 그룹 찾기: 현재 그룹의 이름이 지정된 `name`과 일치하는지 확인합니다.
```python
if group["name"] == name:
```

4. 기존 상태 저장: 현재 파라미터의 옵티마이저 상태(모멘텀 등)를 가져옵니다.
```python
stored_state = self.optimizer.state.get(group['params'][0], None)
```

5. 상태 초기화: 새로운 텐서 크기에 맞춰 모멘텀 등의 상태를 초기화합니다.
```python
stored_state["exp_avg"] = torch.zeros_like(tensor)
stored_state["exp_avg_sq"] = torch.zeros_like(tensor)
```

6. 기존 파라미터 제거 및 교체: 기존 파라미터를 옵티마이저 상태에서 제거하고, 새로운 텐서로 교체합니다. 새로운 파라미터를 옵티마이저 상태에 추가합니다.
```python
del self.optimizer.state[group['params'][0]]
group["params"][0] = nn.Parameter(tensor.requires_grad_(True))
self.optimizer.state[group['params'][0]] = stored_state
```

7. 교체된 텐서 저장: 교체된 파라미터를 딕셔너리에 저장합니다.
```python
교체된 파라미터를 딕셔너리에 저장합니다.
```

8. 딕셔너리 반환: 최종적으로 교체된 텐서를 포함한 딕셔너리를 반환합니다.
```python
return optimizable_tensors
```



### Reference
- [What exactly is meant by param_groups in pytorch?](https://stackoverflow.com/questions/73629330/what-exactly-is-meant-by-param-groups-in-pytorch)

