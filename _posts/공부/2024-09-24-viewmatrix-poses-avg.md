---
title: "[3D CV] viewmatrix, poses_avg, recenter_poses, poses_bounds.npy, generate_spiral_path, generate_ellipse_path"
last_modified_at: 2024-09-24
categories:
  - 공부
tags:
  - viewmatrix
  - poses_avg
  - recenter_poses
  - poses_bounds.npy
  - generate_spiral_path
  - generate_ellipse_path
  - focus_pt_fn
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

```
poses.shape: (N, 3, 5)
```

### poses의 shape은 N개의 이미지에 대한 pose들의 모음으로 구성되어 있습니다.
![bandicam 2024-09-24 19-57-38-321](https://github.com/user-attachments/assets/4d7969d9-164e-4483-8410-f6ffa3126130)
- camera extrinsics를 구성하는 rotation, translation 3x4
- camera intrinsics를 구할 수 있는 H, W, focal length 3x1
- 각 pose는 camera extrinsics와 camera intrinsics을 구하는데 필요한 parameter들을 포함하고 있습니다.

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

- `z_axis`는 `lookdir`
- `up`은 `up`
- `position`은 `position`

1. `lookdir`을 먼저 normalize하고. normalize한 `lookdir`과 `up`의 cross product로 `right` 벡터(=`vec0`)를 구합니다.
2. normalize한 `right` 벡터와 normalize한 `lookdir`벡터를 cross product하여 `up` 벡터를 제대로 직교하게 정의해줍니다.
3. 이를 stack하여 x방향, y방향, z방향 벡터, translation인 `position`을 쌓아 cam2world 3x4 matrix로 반환합니다.

![image](https://github.com/user-attachments/assets/78773a9f-9ad0-4efe-9ea5-fdb0afd6d9c2)

### recenter_poses

```python
def recenter_poses(poses: np.ndarray) -> Tuple[np.ndarray, np.ndarray]:
  """Recenter poses around the origin."""
  cam2world = poses_avg(poses)
  transform = np.linalg.inv(pad_poses(cam2world))
  poses = transform @ pad_poses(poses)
  return unpad_poses(poses), transform

def pad_poses(p):
    """Pad [..., 3, 4] pose matrices with a homogeneous bottom row [0,0,0,1]."""
    bottom = np.broadcast_to([0, 0, 0, 1.], p[..., :1, :4].shape)
    return np.concatenate([p[..., :3, :4], bottom], axis=-2)

def unpad_poses(p):
    """Remove the homogeneous bottom row from [..., 4, 4] pose matrices."""
    return p[..., :3, :4]
```

- `recenter_poses`는 위에서 poses_avg로 구한 하나의 cam2world에 역변환을 취하여 world2cam을 구하고
- 모든 poses에 대해 이 world2cam 변환을 matrix multiplication하여 모든 poese들이 world coordinate에 가깝도록 옮깁니다.
- `pad_poses`, `unpad_poses`는 단순히 4x4 transformation인 homogeneous coordinate에서 연산하고 return 하기 위해 사용합니다.

![image](https://github.com/user-attachments/assets/512c4e57-4e2c-40ef-aeaf-7379cc08d98c)


### backcenter_poses

- 이처럼 모든 poses에 대한 평균 pose인 cam2world를 구하고, 이의 역변환인 world2cam을 모든 poses들에 대해 매트릭스 연산하여, poses를 한번에 원하는 위치로 옮겨줄 수 있습니다.
- 응용으로는 아래와 같이 pose_ref의 cam2world를 구하여, 이의 역변환인 world2cam으로, poses를 모두 recenter할 수 있습니다.
  
```
def backcenter_poses(poses, pose_ref):
    """Recenter poses around the origin."""
    cam2world = poses_avg(pose_ref)
    poses = pad_poses(cam2world) @ pad_poses(poses)
    return unpad_poses(poses)
```

### poses_bounds.npy 

- poses_bounds.npy는 (N, 17)로 이미지 N 개수만큼 pose가 N개 있고, 3x5=15개와 near bound, far bound 2개가 더해서 17의 shape을 가집니다.
- poses_bounds.npy를 load하고 near, far는 사용하지 않기 위해 poses_arr[:, :-2]로 (N,15)의 shape을 취하고 reshape하여 (N, 3, 5)의 poses로 만듭니다.
- bounds는 따로 poses[:, -2:]로 인덱싱하여 (N, 2)로 N개의 pose에 대해 near, far bound를 정의해줍니다.

```python
  poses = poses_arr[:, :-2].reshape([-1, 3, 5])
  bounds = poses_arr[:, -2:]
```

### generate_spiral_path

```python
def generate_spiral_path(poses_arr,
                         n_frames: int = 180,
                         n_rots: int = 2,
                         zrate: float = .5) -> np.ndarray:
  """Calculates a forward facing spiral path for rendering."""
  poses = poses_arr[:, :-2].reshape([-1, 3, 5])
  bounds = poses_arr[:, -2:]
  fix_rotation = np.array([
      [0, -1, 0, 0],
      [1, 0, 0, 0],
      [0, 0, 1, 0],
      [0, 0, 0, 1],
  ], dtype=np.float32)
  poses = poses[:, :3, :4] @ fix_rotation

  scale = 1. / (bounds.min() * .75)
  poses[:, :3, 3] *= scale
  bounds *= scale
  poses, transform = recenter_poses(poses)

  close_depth, inf_depth = bounds.min() * .9, bounds.max() * 5.
  dt = .75
  focal = 1 / (((1 - dt) / close_depth + dt / inf_depth))

  # Get radii for spiral path using 90th percentile of camera positions.
  positions = poses[:, :3, 3]
  radii = np.percentile(np.abs(positions), 90, 0)
  radii = np.concatenate([radii, [1.]])

  # Generate poses for spiral path.
  render_poses = []
  cam2world = poses_avg(poses)
  up = poses[:, :3, 1].mean(0)
  for theta in np.linspace(0., 2. * np.pi * n_rots, n_frames, endpoint=False):
    t = radii * [np.cos(theta), -np.sin(theta), -np.sin(theta * zrate), 1.]
    position = cam2world @ t
    lookat = cam2world @ [0, 0, -focal, 1.]
    z_axis = position - lookat
    render_pose = np.eye(4)
    render_pose[:3] = viewmatrix(z_axis, up, position)
    render_pose = np.linalg.inv(transform) @ render_pose
    render_pose[:3, 1:3] *= -1
    render_pose[:3, 3] /= scale
    render_poses.append(np.linalg.inv(render_pose))
  render_poses = np.stack(render_poses, axis=0)
  return render_poses
```

### transform_poses_pca

- 이 코드는 카메라의 위치와 방향을 표현하는 poses 데이터를 주성분 분석(PCA, Principal Component Analysis)을 사용해 좌표계를 XYZ 축에 맞춰 변환하는 함수입니다.
- poses는 각 카메라의 월드 좌표계에서 카메라 좌표계로의 변환을 나타내는 행렬들로, 이 함수는 poses의 평균 위치를 원점으로 맞춘 뒤, 주성분을 찾아서 회전 행렬을 계산하고, 카메라의 좌표계를 변경하는 과정입니다.

```python
def transform_poses_pca(poses):
    """Transforms poses so principal components lie on XYZ axes.

  Args:
    poses: a (N, 3, 4) array containing the cameras' camera to world transforms.

  Returns:
    A tuple (poses, transform), with the transformed poses and the applied
    camera_to_world transforms.
  """
    t = poses[:, :3, 3]
    t_mean = t.mean(axis=0)
    t = t - t_mean

    eigval, eigvec = np.linalg.eig(t.T @ t)
    # Sort eigenvectors in order of largest to smallest eigenvalue.
    inds = np.argsort(eigval)[::-1]
    eigvec = eigvec[:, inds]
    rot = eigvec.T
    if np.linalg.det(rot) < 0:
        rot = np.diag(np.array([1, 1, -1])) @ rot

    transform = np.concatenate([rot, rot @ -t_mean[:, None]], -1)
    poses_recentered = unpad_poses(transform @ pad_poses(poses))
    transform = np.concatenate([transform, np.eye(4)[3:]], axis=0)

    # Flip coordinate system if z component of y-axis is negative
    if poses_recentered.mean(axis=0)[2, 1] < 0:
        poses_recentered = np.diag(np.array([1, -1, -1])) @ poses_recentered
        transform = np.diag(np.array([1, -1, -1, 1])) @ transform

    # Just make sure it's it in the [-1, 1]^3 cube
    scale_factor = 1. / np.max(np.abs(poses_recentered[:, :3, 3]))
    poses_recentered[:, :3, 3] *= scale_factor
    transform = np.diag(np.array([scale_factor] * 3 + [1])) @ transform
    return poses_recentered, transform
```
1. 평균 위치 맞추기 (t_mean): poses의 카메라 위치를 평균 내어 원점에 맞춥니다.
2. 주성분 계산 (eigval, eigvec): 카메라 위치들의 공분산 행렬에서 고유값 분해를 하여 주성분 축을 찾습니다. 고유값이 큰 축이 더 중요한 축이므로, 이를 기준으로 좌표계를 회전시킵니다.
3. 좌표계 회전 (rot): 가장 중요한 주성분이 X축에 맞도록 하고, 그 다음 축들이 Y와 Z축에 맞도록 회전합니다. 만약 회전 행렬의 행렬식이 음수라면, Z축 방향을 반대로 맞춥니다.
4. 변환 행렬 적용 (transform): 원래의 poses에 변환 행렬을 적용하여 카메라의 새로운 좌표계로 변환된 poses를 계산합니다.
5. 좌표계 플립: 변환된 좌표계의 Y축이 아래쪽을 향하고 있다면, 좌표계를 다시 반전시킵니다.
6. 크기 조정 (scale_factor): 변환된 좌표계를 크기 조정하여 [-1, 1] 범위에 맞춥니다.
- 이 과정에서 PCA를 통해 카메라의 위치를 보다 직관적으로 볼 수 있는 형태로 변환하여, 주성분 축이 XYZ 축에 맞게 좌표계를 회전하고 정렬하는 것입니다.
- 이 코드는 카메라의 배치가 어떻게 이루어져 있는지 시각적으로 더 잘 이해하고자 할 때 유용할 수 있습니다. 3D 공간에서 카메라들의 위치가 어떻게 분포하는지 파악할 수 있게 도와줍니다.

### focus_pt_fn

- 이 코드는 poses라는 카메라의 위치와 방향 정보를 입력받아, **모든 카메라의 초점 축(focal axis)에 가장 가까운 점을 계산**하는 함수입니다.
- **이 함수는 여러 카메라가 서로 다른 방향을 보고 있을 때, 각 카메라의 시선(축)에 가장 가까운 공통의 한 점(집중점, focus point)을 찾는 과정입니다.**

```python
def focus_pt_fn(poses):
  """Calculate nearest point to all focal axes in poses."""
  directions, origins = poses[:, :3, 2:3], poses[:, :3, 3:4]
  m = np.eye(3) - directions * np.transpose(directions, [0, 2, 1])
  mt_m = np.transpose(m, [0, 2, 1]) @ m
  focus_pt = np.linalg.inv(mt_m.mean(0)) @ (mt_m @ origins).mean(0)[:, 0]
  return focus_pt
```

#### 입력 poses:
- poses는 여러 카메라의 위치와 방향을 포함하는 행렬입니다. 각 pose는 카메라의 월드 좌표계에서 카메라 좌표계로 변환하는 정보가 들어 있습니다.
- poses[:, :3, 2:3]: 각 카메라의 z축(초점 축)을 나타냅니다. 이 축이 카메라가 바라보는 방향을 나타냅니다.
- poses[:, :3, 3:4]: 각 카메라의 위치(원점)를 나타냅니다.

#### 카메라 축 기반 계산:
- directions: 각 카메라의 z축 방향(즉, 초점 방향)을 추출합니다.
- origins: 각 카메라의 위치(원점)를 추출합니다.

#### 근사적인 카메라 축들로부터 평면 계산:
- m = np.eye(3) - directions * np.transpose(directions, [0, 2, 1]): 각 카메라의 초점 축을 이용하여 평면을 정의합니다.
- 여기서 np.eye(3)은 단위 행렬이고, 초점 축을 기준으로 카메라의 축에 수직한 평면을 계산하는 과정입니다.

#### 평면을 사용해 초점점(focus point) 계산:
- mt_m = np.transpose(m, [0, 2, 1]) @ m: 각 카메라 평면의 정보에서 변환된 행렬을 구합니다.
- focus_pt = np.linalg.inv(mt_m.mean(0)) @ (mt_m @ origins).mean(0)[:, 0]: 이 과정을 통해, 모든 카메라가 공통으로 바라보는 점, 즉 초점점(focus point)을 계산합니다.
- 각 카메라의 초점 축이 가리키는 평면에서 가장 가까운 점을 찾는 과정이라고 볼 수 있습니다.

#### 주요 개념:
- 초점 축 (Focal Axis): 각 카메라가 바라보는 방향입니다. 각 카메라의 z축을 기준으로 하여 이를 계산합니다.
- 평면 계산: 각 카메라의 z축에 수직인 평면을 정의하여, 이 평면들이 교차하는 지점 또는 근접한 지점을 구하는 방식입니다.
- 초점점 (Focus Point): 여러 카메라의 시선이 모이는 지점 또는 그 지점에 가장 가까운 점을 계산합니다.

#### 요약:
- 이 함수는 여러 카메라의 방향과 위치를 고려하여, 모든 카메라의 시선이 교차하는 가장 가까운 공통의 한 점을 계산하는 함수입니다.
- 이 계산은 카메라 배열에서 특정한 한 지점을 동시에 바라보도록 시각적 중심을 찾는 데 유용할 수 있습니다.


