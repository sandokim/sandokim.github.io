---
title: "[3D CV] NeRF Coordinate System"
last_modified_at: 2024-07-02
categories:
  - 공부
tags:
  - Multiple View Geometry
  - NeRF
  - coordinate system
  - COLMAP
  - viewing frustum
  - viewing direction
excerpt: "NeRF Coordinate system 이해 정리"
use_math: true
classes: wide
---

### NeRF의 Coordinate System을 이해해보자

### NeRF 데이터셋은 기본적으로 각 카메라 포즈에 대한 정보는 World에서 각 Camera좌표까지의 변환을 모은 것이라고 생각하면 됩니다.

- 코드에 따라 다를 수 있지만, 기본적으로 W2C로 정의하여 불러 옵니다. (사람에 따라 C2W라고 정의해버리면 헷갈릴 수 있습니다. 주의해야합니다.)

- MipNeRF360(NeRF++) 데이터를 불러오는 부분을 봅시다. `cam`마다 `cam_info`에서 `R`과 `T`를 불러와서 `W2C (world to camera)` 변환을 정의해줍니다.
- `C2W (camera to world)`는 `W2C (world to camera)`를 역변환하여 정의합니다.
- 만약 카메라의 center을 알고 싶다면 World Coordinate System을 기준으로 정의해야합니다.
- 그러므로 카메라가 World Coordinate System을 기준으로 할 때의 translation이 카메라의 center가 됩니다.
- `C2W (camera to world)`는 Camera Coordinate System에서 World Coordinate System으로 변환해줍니다.
- 즉, `C2W (camera to world)`는 World Coordinate System이 기준일 때, 카메라의 Rotation과 Translation을 포함하는 행렬입니다.
- 따라서, `C2W (camera to world)`에서 마지막 열을 인덱싱하면 World Coordinate System에서 카메라의 center 위치를 얻는 것입니다.

### C2W를 수식으로 쓰면 다음과 같습니다.

- `C2W` 변환 행렬은 카메라 좌표계에서 월드 좌표계로 변환하는 행렬입니다. 이 행렬은 보통 회전 행렬 $R$과 변환 벡터 $T$로 구성됩니다.
$$
C2W = \begin{bmatrix}
R & T \\
0 & 1
\end{bmatrix}
$$

- 여기서 $R$은 3x3 회전 행렬이고, $T$는 3x1 변환 벡터입니다. 보다 구체적으로 표현하면:

$$
C2W = \begin{bmatrix}
R_{11} & R_{12} & R_{13} & T_x \\
R_{21} & R_{22} & R_{23} & T_y \\
R_{31} & R_{32} & R_{33} & T_z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

- 여기서 `C2W # shape 4x4`에서, `C2W[:3, 3:4]`와 같이 취하면 World Coordinate System 기준 camera center 위치를 구할 수 있습니다.

$$
camera \ center = \begin{bmatrix}
T_x \\
T_y \\
T_z \\
\end{bmatrix}
$$

- 이와 같이 camera center를 다룰 때에는 World Coordinate System로 기준 좌표계로 바꿔주고 계산을 해야합니다.
- 그런 다음 다시 Camera Coordinate System으로 좌표계를 변경해줘야 합니다.
### 즉, 카메라의 포즈를 조작할 때는
1) 먼저 `C2W (camera to world)`에서 Rotation과 Translation을 변경해주고,
2) `C2W (camera to world)`에 inverse를 취하여 다시 `W2C (world to camera)` 변환을 얻어주어 사용합니다.

- `getWorld2View(R, t)`는 단순히 `W2C`을 구성하는 `R`, `t`로 `W2C (world to camera)` 4x4 변환을 반환하는 함수
```python
# 3dgs/utils/graphics_utils.py

def getWorld2View(R, t):
  Rt = np.zeros((4, 4))
  Rt[:3, :3] = R.transpose()
  Rt[:3, 3] = t
  Rt[3, 3] = 1.0
  return np.float32(Rt)
```

- `getWorld2View2(R, t, translate=np.array([.0, .0, .0]), scale=1.0)`은 `W2C`을 구성하는 `R`, `t`로 `W2C (world to camera)` 4x4 변환을 구성하고, inverse를 취하여 `C2W (camera to world)`로 변환한 후에 camera center에 대해 translate와 scale을 조정하고, 다시 inverse를 취한 `W2C (world to camera)` 4x4 변환을 반환하는 함수

```python
# 3dgs/utils/graphics_utils.py

def getWorld2View2(R, t, translate=np.array([.0, .0, .0]), scale=1.0):
    Rt = np.zeros((4, 4))
    Rt[:3, :3] = R.transpose()
    Rt[:3, 3] = t
    Rt[3, 3] = 1.0

    C2W = np.linalg.inv(Rt)
    cam_center = C2W[:3, 3]
    cam_center = (cam_center + translate) * scale
    C2W[:3, 3] = cam_center
    Rt = np.linalg.inv(C2W)
    return np.float32(Rt)
```

- 카메라 포즈를 조작하는 예시를 살펴봅시다.
- 아래 코드에선 `getWorld2View2(cam.R, cam.T, translate=np.array([.0, .0, .0]), scale=1.0)`이므로 따로 camera center에 대한 translate과 scale을 조절하지 않았습니다.
- `get_center_and_diag(cam_centers)`
  - `cam_centers`는 `C2W[:3, 3:4]`로 모든 카메라에 대한 cam_centers를 list 형태로 모아줍니다.
  - `C2W`의 모든 translation에 대한 평균을 구하여, `avg_cam_center`를 구합니다.
  - `avg_cam_center`와 가장 거리가 멀리 떨어진 카메라까지의 거리를 `diagonal`로 구합니다.
    - 반환된 `avg_cam_center`은 `center`로 반환되어 평균적으로 카메라까지의 거리를 구할 때 `translate = -center`로써 사용됩니다.
    - 반환된 `diagonal`은 `avg_cam_center`에서 가장 멀리 떨어진 카메라까지의 거리이고, 이에 1.1을 곱하여 scene의 최대 반지름 반경을 설정하는데 사용합니다.
    - 이는 `nerf_normalization` 변수로 반환되어 `readColmapSceneInfo`와 `readNeRFSyntheticInfo`의 `SceneInfo`에 전달되어 사용됩니다.

```python
# 3dgs/scene/dataset_reader.py

def getNerfppNorm(cam_info):
    def get_center_and_diag(cam_centers):
        cam_centers = np.hstack(cam_centers)
        avg_cam_center = np.mean(cam_centers, axis=1, keepdims=True)
        center = avg_cam_center
        dist = np.linalg.norm(cam_centers - center, axis=0, keepdims=True)
        diagonal = np.max(dist)
        return center.flatten(), diagonal

    cam_centers = []

    for cam in cam_info:
        W2C = getWorld2View2(cam.R, cam.T)
        C2W = np.linalg.inv(W2C)
        cam_centers.append(C2W[:3, 3:4])

    center, diagonal = get_center_and_diag(cam_centers)
    radius = diagonal * 1.1

    translate = -center

    return {"translate": translate, "radius": radius}
```


```python
# 3dgs/scene/dataset_reader.py

def readColmapSceneInfo(path, images, eval, llffhold=8):
    try:
        cameras_extrinsic_file = os.path.join(path, "sparse/0", "images.bin")
        cameras_intrinsic_file = os.path.join(path, "sparse/0", "cameras.bin")
        cam_extrinsics = read_extrinsics_binary(cameras_extrinsic_file)
        cam_intrinsics = read_intrinsics_binary(cameras_intrinsic_file)
    except:
        cameras_extrinsic_file = os.path.join(path, "sparse/0", "images.txt")
        cameras_intrinsic_file = os.path.join(path, "sparse/0", "cameras.txt")
        cam_extrinsics = read_extrinsics_text(cameras_extrinsic_file)
        cam_intrinsics = read_intrinsics_text(cameras_intrinsic_file)

    reading_dir = "images" if images == None else images
    cam_infos_unsorted = readColmapCameras(cam_extrinsics=cam_extrinsics, cam_intrinsics=cam_intrinsics, images_folder=os.path.join(path, reading_dir))
    cam_infos = sorted(cam_infos_unsorted.copy(), key = lambda x : x.image_name)

    if eval:
        train_cam_infos = [c for idx, c in enumerate(cam_infos) if idx % llffhold != 0]
        test_cam_infos = [c for idx, c in enumerate(cam_infos) if idx % llffhold == 0]
    else:
        train_cam_infos = cam_infos
        test_cam_infos = []

    nerf_normalization = getNerfppNorm(train_cam_infos)

    ply_path = os.path.join(path, "sparse/0/points3D.ply")
    bin_path = os.path.join(path, "sparse/0/points3D.bin")
    txt_path = os.path.join(path, "sparse/0/points3D.txt")
    if not os.path.exists(ply_path):
        print("Converting point3d.bin to .ply, will happen only the first time you open the scene.")
        try:
            xyz, rgb, _ = read_points3D_binary(bin_path)
        except:
            xyz, rgb, _ = read_points3D_text(txt_path)
        storePly(ply_path, xyz, rgb)
    try:
        pcd = fetchPly(ply_path)
    except:
        pcd = None

    scene_info = SceneInfo(point_cloud=pcd,
                           train_cameras=train_cam_infos,
                           test_cameras=test_cam_infos,
                           nerf_normalization=nerf_normalization,
                           ply_path=ply_path)
    return scene_info
```

```python
# 3dgs/scene/dataset_reader.py

def readNerfSyntheticInfo(path, white_background, eval, extension=".png"):
    print("Reading Training Transforms")
    train_cam_infos = readCamerasFromTransforms(path, "transforms_train.json", white_background, extension)
    print("Reading Test Transforms")
    test_cam_infos = readCamerasFromTransforms(path, "transforms_test.json", white_background, extension)
    
    if not eval:
        train_cam_infos.extend(test_cam_infos)
        test_cam_infos = []

    nerf_normalization = getNerfppNorm(train_cam_infos)

    ply_path = os.path.join(path, "points3d.ply")
    if not os.path.exists(ply_path):
        # Since this data set has no colmap data, we start with random points
        num_pts = 100_000
        print(f"Generating random point cloud ({num_pts})...")
        
        # We create random points inside the bounds of the synthetic Blender scenes
        xyz = np.random.random((num_pts, 3)) * 2.6 - 1.3
        shs = np.random.random((num_pts, 3)) / 255.0
        pcd = BasicPointCloud(points=xyz, colors=SH2RGB(shs), normals=np.zeros((num_pts, 3)))

        storePly(ply_path, xyz, SH2RGB(shs) * 255)
    try:
        pcd = fetchPly(ply_path)
    except:
        pcd = None
    scene_info = SceneInfo(point_cloud=pcd,
                           train_cameras=train_cam_infos,
                           test_cameras=test_cam_infos,
                           nerf_normalization=nerf_normalization,
                           ply_path=ply_path)
    return scene_info
```

--------

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f7a817d0-9ee1-4cdf-9a0d-71d2deb1df18" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/64f409ab-9094-4ca3-9301-cd306d14a91a" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/e9a689f2-0c24-4094-9565-b619e0bb156c" alt="Image">
</p>

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/b255e68d-bfd9-48e0-a1e1-61e0fbec1820" alt="Image">
</p>


<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f87d491e-1d43-4c81-a019-590f5e5b5124" alt="Image">
</p>
