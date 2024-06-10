---
title: "[3D CV 연구] 3DGS densification and pruning"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - densification
  - pruning
  - densification and pruning
  - densify_until_iter
excerpt: "3DGS densify_until_iter"
use_math: true
classes: wide
---

**--densify_until_iter**: 0으로 설정하면 densification과 pruning이 모두 일어나지 않습니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/404

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b65b164a-b935-4865-b817-73056083e635)

arguments -> __ init __.py -> self.densify_until_iter 는 train.py에 opt.densify_until_iter로써 사용하여 densification과 pruning을 수행합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/59b10ec1-df8e-4034-a3fa-fa470294a2d6)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7d6730d7-7c22-46de-bb23-19af3fd3174a)
