---
title: "[3D CV] viewmatrix, poses_avg, normalize"
last_modified_at: 2024-09-24
categories:
  - 공부
tags:
  - viewmatrix
  - poses_avg
  - Poses
  - Camera Extrinsics
  - world to camera
  - camera to world
  - w2c
  - c2w
excerpt: "poses에서 자주 나오는 viewmatrix, poses_avg를 알아봅시다."
use_math: true
classes: wide
comments: true
---

```python
def normalize(x):
    return x / np.linalg.norm(x)


def viewmatrix(lookdir, up, position, subtract_position=False):
  """Construct lookat view matrix."""
  vec2 = normalize((lookdir - position) if subtract_position else lookdir)
  vec0 = normalize(np.cross(up, vec2))
  vec1 = normalize(np.cross(vec2, vec0))
  m = np.stack([vec0, vec1, vec2, position], axis=1)
  return m


def poses_avg(poses):
  """New pose using average position, z-axis, and up vector of input poses."""
  position = poses[:, :3, 3].mean(0)
  z_axis = poses[:, :3, 2].mean(0)
  up = poses[:, :3, 1].mean(0)
  cam2world = viewmatrix(z_axis, up, position)
  return cam2world
```

```
poses.shape: (N, 3, 5)
```

### poses의 shape은 N개의 이미지에 대한 pose들의 모음으로 구성되어 있습니다.
![bandicam 2024-09-24 19-57-38-321](https://github.com/user-attachments/assets/4d7969d9-164e-4483-8410-f6ffa3126130)
- camera extrinsics를 구성하는 rotation, translation 3x4
- camera intrinsics를 구할 수 있는 H, W, focal length 3x1
- 각 pose는 camera extrinsics와 camera intrinsics을 구하는데 필요한 parameter들을 포함하고 있습니다.
  
### poses[:, :3, 3] -> translation 벡터이므로 viewmatrix에서 `position`으로 변수명을 정의합니다.
![bandicam 2024-09-24 19-51-59-684](https://github.com/user-attachments/assets/0a0f10d5-b98c-421e-8fb6-b71c082cbdeb)

### poses[:, :3, 2] -> z방향 벡터이므로 viewmatrix에서 `z_axis`로 변수명을 정의합니다.
![bandicam 2024-09-24 19-52-03-684](https://github.com/user-attachments/assets/53c1e6e4-8ee8-4e91-a8bc-8e7aec17c9b7)

### poses[:, :3, 1] -> y방향 벡터는 viewmatrix에서 `up`으로 변수명을 정의합니다.
![bandicam 2024-09-24 19-52-04-918](https://github.com/user-attachments/assets/d53246a9-9e0b-49e3-a2ce-9199f95fc01b)

### viewmatrix에서는 결론적으로 다음과 같이 정의합니다.
- `poses[:, :3, 3].mean(0)`이므로 모든 poses들의 `position`의 평균을 translation 벡터로 정의합니다.
- `poses[:, :3, 2].mean(0)`이므로 모든 poses들의 `z_axis`의 평균을 z방향 벡터로 정의합니다.
- `poses[:, :3, 1].mean(0)`이므로 모든 poses들의 `up`의 평균을 up방향 벡터로 정의합니다.

### viewmatrix(z_axis, up, position)을 받는데, 일반적으로 `z_axis`는 카메라가 보는 방향을 가리키므로 `lookdir`로 받습니다.
- `z_axis`는 `lookdir`
- `up`은 `up`
- `position`은 `position`

1. `lookdir`을 먼저 normalize하고
2. normalize한 `lookdir`과 `up`의 cross product로 `right` 벡터(=`vec0`)를 구합니다.
3. normalize한 `right`벡터와 normalize한 `lookdir`벡터를 cross product하여 `up` 벡터를 제대로 직교하게 정의해줍니다.
4. 이를 stack하여 x방향, y방향, z방향 벡터, translation인 `position`을 쌓아 cam2world 3x4 matrix로 반환합니다.
   

