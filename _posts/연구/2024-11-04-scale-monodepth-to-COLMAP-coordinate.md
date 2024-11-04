---
title: "[3D CV 연구] How to scale monodepth to COLMAP coordinate"
last_modified_at: 2024-11-04
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - coorindate systems
  - monodepth
  - COLMAP coordinate
  - monocular depth estimation
  - Depth Anything
  - DPT
  - mono depth estimation
excerpt: "Monodepth를 COLMAP coordinate로 rescaling하는 법을 알아봅시다."
use_math: true
classes: wide
comments: true
---

### Monocular depth estimation은 [0.0, 1.0] 사이의 값을 반환합니다.

Monocular depth estimation model은 RGB를 input으로 넣을 시 depth map을 output 합니다.

Output된 depth map은 보통 relative depth (인접 픽셀간의 상대적인 깊이 값)을 표현하므로 scale이 [0.0, 1.0]으로 맞춰져있습니다.

또한 monocular depth estimation으로 추정한 depth는 inverse depth로 반환하는 경우가 일반적이므로 주의합니다. 

```python
def normalize_depth(depth):
    return (depth-depth.min())/(depth.max()-depth.min())

pipe = pipeline(task="depth-estimation", model="depth-anything/Depth-Anything-V2-Small-hf", device='cuda')
depth = pipe(image)["depth"] # inverse depth 형태

depth = 1 - normalize_depth(depth)
```

X-ray에서도 실제 촬영한 이미지는 inverse로 값이 표현되는데, 반전된 X-ray value를 먼저 normalize한 다음 1에서 빼주는 방식으로 inverse를 취해 사용합니다.

### Depth map을 원하는 scene scale로 rescaling하는 방법 (i.e COLMAP coordinate로 변환)

depth map을 위처럼 처리하여 가장 가까운 값은 0.0, 가장 먼 값은 1.0으로 표현되게 정규화를 시켜주었다면,

우리가 원하는 좌표계의 scale에 맞게 rescale할 수 있습니다.

```python
'''
depth_map: Mono Depth from the src camera
rendered_depth_min/_max: 3dgs rendered depth min/max from the src camera in COLMAP coordinate
'''

depth_map = depth_map.detach().cpu().numpy()
# Scale monodepth to COLMAP coordinate
scaled_depth = (depth_map - depth_map.min())/(depth_map.max() - depth_map.min())
scaled_depth = scaled_depth * (rendered_depth_max - rendered_depth_min) + rendered_depth_min

# 코드 출처: https://github.com/ForMyCat/SparseGS/blob/master/scene/cameras.py#L106C1-L109C105
```

- `scaled_depth`는 monodepth estimation을 통해 얻은 `depth map`을 [0.0, 1.0]으로 normalize한 것이고, (이때 depth map은 inverse depth가 아니어야 합니다.)

- `rendered_depth_min`과 `rendered_depth_max`는 현재 source camera (src camera)를 기준으로 depth의 최소/최대 값입니다.

- [0.0, 1.0]의 값을 갖는 `scaled_depth`를 `render_depth_min`과 `rendered_depth_max`의 사이 길이에 곱해주고, `rendered_depth_min`만큼 src camerea로부터 shift 시켜주어, COLMAP coordinate에 맞게 depth map을 rescaling할 수 있습니다.




