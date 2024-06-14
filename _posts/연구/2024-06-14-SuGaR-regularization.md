---
title: "[3D CV 연구] 3DGS Tips for using SuGaR regularization sdf or density"
last_modified_at: 2024-06-14
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - density
  - sdf
  - regularization
excerpt: "3DGS Tips for using SuGaR regularization sdf or density"
use_math: true
classes: wide
---

## Regularization Methods for SuGaR

### Density regularization
- 가장 단순한 방법으로, object centered 장면에 잘 작동합니다.
- object centered 장면을 재구성할 때 더 나은 mesh를 생성합니다.
- 360°로 object를 촬영한 경우, density regularization을 사용합니다.

```python
python train.py -r 'density'
```

### SDF regularization
- background regions에서 더 강한 regularization을 제공합니다.
- 표준 데이터셋에서 더 높은 평가 지표를 생성합니다.
- 더 도전적인 장면을 재구성하거나 background regions에서 더 강한 regularization을 적용할 때 사용합니다.

```python
python train.py -r 'sdf'
```

https://github.com/Anttwo/SuGaR?tab=readme-ov-file

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a7c06a82-7ef6-467b-a00d-bd77a5d268ee)


