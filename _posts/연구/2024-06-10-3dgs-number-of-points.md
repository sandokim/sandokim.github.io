---
title: "[3D CV 연구] 3DGS The number of points is decreasing when training large-scale scenes"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - large-scale scenes
  - number of points
excerpt: "3DGS The number of points is decreasing when training large-scale scenes"
use_math: true
classes: wide
comments: true
---

large-scale scenes에 대해서 gaussian의 수가 크게 줄어드는 경우, 어떻게 해결하나요?

Large gaussian들이 culling되는 것을 comment out 한 다음 결과를 봐보면 됩니다.

The algorithm has the potential for a draining effect due to the culling of large gaussians. 

I will see if i can make a quickfix for it, but in the meantime, you could go and comment this line

```python
prune_mask = torch.logical_or(torch.logical_or(prune_mask, big_points_vs), big_points_ws)
```

in gaussian_model.py and that should prevent it

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ac13660a-5905-452c-9835-d686e1febefe)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/19d95720-4d75-4d60-a09f-9547882602e3)

