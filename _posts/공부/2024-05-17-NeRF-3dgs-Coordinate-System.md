---
title: "[3D CV] NeRF, 3dgs, C2W, W2C 카메라 포즈 조작"
last_modified_at: 2024-07-02
categories:
  - 공부
tags:
  - Multiple View Geometry
  - NeRF
  - coordinate system
  - NeRF custom dataset
  - COLMAP
  - transform_matrix
  - c2w
  - w2c
  - getWorld2View
  - getWorld2View2
  - avg_cam_center
  - radius
  - diagonal
excerpt: "NeRF Coordinate system 이해 정리"
use_math: true
classes: wide
---

## NeRF의 Coordinate System을 이해해보자

### 주의: $^{C}{T}_W$ = world-to-camera = `w2c`, $^{W}{T}_C$ = camera-to-world = `c2w`

- 좌표계 변환에서 수식을 읽을 때는, 우측하단의 좌표계에서 좌측상단의 좌표계로 이동하는 변환으로 정의됩니다.
- $^{C}{T}_W$는 따라서 우측하단의 World Coordinate System에서 좌측상단의 Camera Coordinate System으로의 변환입니다.
- 그 의미로 인해 $^{C}{T}_W$는 변수명으로는 world-to-camera인 `w2c`으로 정의합니다.
- 반대의 경우도 마찬가지입니다.
- $^{W}{T}_C$은 우측하단의 Camera Coordinate System에서 좌측상단의 World Coordinate System으로의 변환입니다.
- 그 의미로 인해 $^{W}{T}_C$는 변수명으로는 camera-to-world인 `c2w`으로 정의합니다.
- 코딩 스타일에 따라 `w2c`를 `W2C`로, `c2w`를 `C2W`로 대문자로 변수를 쓰는 사람도 있습니다.

### NeRF 데이터셋은 기본적으로 각 카메라 포즈에 대한 정보는 World에서 각 Camera까지의 변환을 모은 것이라고 생각하면 됩니다.

- 코드에 따라 다를 수 있지만, 기본적으로 W2C로 정의하여 불러 옵니다. (사람에 따라 W2C을 C2W라고 정의해버리는 경우도 있으니 주의해야합니다.)

- MipNeRF360(NeRF++) 데이터를 불러오는 부분을 봅시다. `cam`마다 `cam_info`에서 `R`과 `T`를 불러와서 `W2C (world to camera)` 변환을 정의해줍니다.
- `C2W (camera to world)`는 `W2C (world to camera)`를 역변환하여 정의합니다.
- 만약 카메라의 center을 알고 싶다면 World Coordinate System을 기준으로 정의해야합니다.
- 그러므로 카메라가 World Coordinate System을 기준으로 할 때의 translation이 카메라의 center가 됩니다.
- `C2W (camera to world)`는 Camera Coordinate System에서 World Coordinate System으로 변환해줍니다.
- 즉, `C2W (camera to world)`는 World Coordinate System이 기준일 때, 카메라의 Rotation과 Translation을 포함하는 행렬입니다.
- 따라서, `C2W (camera to world)`에서 마지막 열을 인덱싱하면 World Coordinate System에서 카메라의 center 위치를 얻는 것입니다.

### `C2W`를 수식으로 쓰면 다음과 같습니다.

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

### 즉, World Coordinate System에서 카메라의 포즈를 조작할 때는

1. 먼저 `C2W (camera to world)`에서 Rotation과 Translation을 변경해주고,
2. `C2W (camera to world)`에 inverse를 취하여 다시 `W2C (world to camera)` 변환을 얻어주어 사용합니다.

### `getWorld2View(R, t)`

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

### `getWorld2View2(R, t, translate=np.array([.0, .0, .0]), scale=1.0)`

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

## 카메라 포즈의 translate와 scale을 조작할 수 있는 `getWorld2View2(R, t, translate=np.array([.0, .0, .0]), scale=1.0)`의 활용 예시를 알아봅시다.

### 1. 먼저 `SceneInfo`에서 `getWorld2View2(R, t, translate=np.array([.0, .0, .0]), scale=1.0)`로 계산한 `nerf_normalization` 변수로 Scene의 범위를 조작하는 예시를 살펴봅시다.
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

### 2. CameraInfo에서 카메라 포즈를 `getWorld2View2(R, t, translate=np.array([.0, .0, .0]), scale=1.0)`로 계산하여 `world view transform`에서 `translate`와 `scale`을 조작할 수 있습니다.

- `self.world_view_transform = torch.tensor(getWorld2View2(R, T, trans, scale)).transpose(0, 1).cuda()`에서 `getWorld2View2(R, t, translate=np.array([.0, .0, .0]), scale=1.0)` 함수에서 `translate`와 `scale`로 `world coordinate system`에서 카메라의 포즈의 `translate`와 `scale`을 조작할 수 있습니다.

```python
# 3dgs/utils/graphcis_utils.py

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

```python
# 3dgs/scene/cameras.py

from utils.graphics_utils import getWorld2View2, getProjectionMatrix

class Camera(nn.Module):
    def __init__(self, colmap_id, R, T, FoVx, FoVy, image, gt_alpha_mask,
                 image_name, uid,
                 trans=np.array([0.0, 0.0, 0.0]), scale=1.0, data_device = "cuda"
                 ):
        super(Camera, self).__init__()

        self.uid = uid
        self.colmap_id = colmap_id
        self.R = R
        self.T = T
        self.FoVx = FoVx
        self.FoVy = FoVy
        self.image_name = image_name

        try:
            self.data_device = torch.device(data_device)
        except Exception as e:
            print(e)
            print(f"[Warning] Custom device {data_device} failed, fallback to default cuda device" )
            self.data_device = torch.device("cuda")

        self.original_image = image.clamp(0.0, 1.0).to(self.data_device)
        self.image_width = self.original_image.shape[2]
        self.image_height = self.original_image.shape[1]

        if gt_alpha_mask is not None:
            self.original_image *= gt_alpha_mask.to(self.data_device)
        else:
            self.original_image *= torch.ones((1, self.image_height, self.image_width), device=self.data_device)

        self.zfar = 100.0
        self.znear = 0.01

        self.trans = trans
        self.scale = scale

        self.world_view_transform = torch.tensor(getWorld2View2(R, T, trans, scale)).transpose(0, 1).cuda()
        self.projection_matrix = getProjectionMatrix(znear=self.znear, zfar=self.zfar, fovX=self.FoVx, fovY=self.FoVy).transpose(0,1).cuda()
        self.full_proj_transform = (self.world_view_transform.unsqueeze(0).bmm(self.projection_matrix.unsqueeze(0))).squeeze(0)
        self.camera_center = self.world_view_transform.inverse()[3, :3]

```

- 추가적으로 `self.camera_cetner`는 `self.world_view_transform.inverse()[3, :3]`로 얻는 것을 볼 수 있습니다.
- `camera`와 `view`는 같은 의미로 사용됩니다.
- 따라서 `self.world_view_transform`은 `w2c`를 의미합니다.
- inverse를 취하면 `self.world_view_transform.inverse()`은 `c2w`를 의미하게 됩니다.
- `c2w`는 **`camera`를 `world coordinate system`으로 좌표변환을 했을 때 camera가 어디에 어떻게 좌표축이 돌아간 상태로 위치하는가?** 를 의미합니다. 이는 `world coordinate system`에서 `camera의 pose`를 의미합니다.
- `self.world_view_transform.inverse()[3, :3]`는 `world coordinate system`에서 `camera의 pose`중, 마지막 열에 해당하는 `translate`성분이므로, `world coordinate system`에서 `camera_center`를 의미합니다.
- 이때 일반적으로 4x4 행렬에서 마지막 열인 `translate`를 인덱싱하는 `[:3, 3]`가 아니라 `[3, :3]`으로 코딩된 이유는, 앞에서 `self.world_view_transform`를 `transpose(0, 1)`하였기 때문입니다.
  ```python
        self.world_view_transform = torch.tensor(getWorld2View2(R, T, trans, scale)).transpose(0, 1).cuda()
  ...
        self.camera_center = self.world_view_transform.inverse()[3, :3]
  ```
  - `transpose`된 `w2c`인 `self.world_view_transform`에서 `[3, :3]`으로 인덱싱하면 마지막 행을 얻으므로 `translate` 정보를 얻을 수 있습니다.
  - 아래와 같은 행렬에서 `[T_x, T_y, T_z]`을 인덱싱하여 얻어 `self.camera_center`를 정의하는 것입니다.

$$
W2C^T = \begin{bmatrix}
R_{11} & R_{21} & R_{31} & 0 \\
R_{12} & R_{22} & R_{32} & 0 \\
R_{13} & R_{23} & R_{33} & 0 \\
T_x & T_y & T_z & 1
\end{bmatrix}
$$

### `dataset_readers.py`은 `R`에서부터 CUDA code 연산을 위해 미리 `R`이 `transpose()`가 되어있습니다.

- `R`이 `transpose()`된 부분은 아래의 `dataset_readers.py`의 `readColmapCameras`와 `readCamerasFromTransforms` 함수에서 모두 확인 가능합니다.
  
```python
# 3dgs/scene/dataset_readers.py

def readColmapCameras(cam_extrinsics, cam_intrinsics, images_folder):
    cam_infos = []
    for idx, key in enumerate(cam_extrinsics):
        sys.stdout.write('\r')
        # the exact output you're looking for:
        sys.stdout.write("Reading camera {}/{}".format(idx+1, len(cam_extrinsics)))
        sys.stdout.flush()

        extr = cam_extrinsics[key]
        intr = cam_intrinsics[extr.camera_id]
        height = intr.height
        width = intr.width

        uid = intr.id
        R = np.transpose(qvec2rotmat(extr.qvec))
        T = np.array(extr.tvec)

...

def readCamerasFromTransforms(path, transformsfile, white_background, extension=".png"):
    cam_infos = []

    with open(os.path.join(path, transformsfile)) as json_file:
        contents = json.load(json_file)
        fovx = contents["camera_angle_x"]

        frames = contents["frames"]
        for idx, frame in enumerate(frames):
            cam_name = os.path.join(path, frame["file_path"] + extension)

            # NeRF 'transform_matrix' is a camera-to-world transform
            c2w = np.array(frame["transform_matrix"])
            # change from OpenGL/Blender camera axes (Y up, Z back) to COLMAP (Y down, Z forward)
            c2w[:3, 1:3] *= -1

            # get the world-to-camera transform and set R, T
            w2c = np.linalg.inv(c2w)
            R = np.transpose(w2c[:3,:3])  # R is stored transposed due to 'glm' in CUDA code
            T = w2c[:3, 3]

...

```
- 위처럼 CUDA code 연산을 위해, `dataset_readers.py`의 `readColmapCameras`와 `readCamerasFromTransforms`에서 불러온 `R`을 `R.transpose()`해버린 상태입니다.
- 따라서 CUDA code가 아닌` transpose`되지 않은 4x4 일반적인 카메라 포즈 연산을 하기위해 `getWorld2View`, `getWorld2View2`에서는 `R`을 다시 `transpose()`하여 사용합니다.  
- 즉, `getWorld2View`에서는 `R`이 `transpose`를 하여 4x4 카메라 포즈로써 계산하기 위해, `dataset_readers.py`에서 `transpose`되었던 `R`을 다시 `R.transpose()`를 하고, `W2C` 형태로 구성하여 반환합니다.
- `getWorld2View2`는 `R`이 `getWorld2View`처럼 `R.transpose()`하고 최종적으로 `W2C`을 반환하는 것은 동일하지만, 중간에 `W2C`을 `C2W`로 inverse하여 `world coordinate system`에서 `camera의 pose`의 `translate`, `scale` 값으로 조절하고, 이를 다시 inverse한 `W2C`로 반환합니다.
- `transpose`되지 않은 일반적인 4x4 `W2C`의 형태는 아래와 같습니다.
  
$$
W2C = \begin{bmatrix}
R_{11} & R_{12} & R_{13} & T_x \\
R_{21} & R_{22} & R_{23} & T_y \\
R_{31} & R_{32} & R_{33} & T_z \\
0 & 0 & 0 & 1
\end{bmatrix}
$$

```python
# 3dgs/utils/graphics_utils.py

def getWorld2View(R, t):
    Rt = np.zeros((4, 4))
    Rt[:3, :3] = R.transpose()
    Rt[:3, 3] = t
    Rt[3, 3] = 1.0
    return np.float32(Rt)

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

- `utils/camera_utils.py`에서도 dataset_readers.py에서 불러올따 `transpose`했던 `R`을 다시 `R.transpose()`하여 일반적인 4X4 형태의 `W2C`으로 저장하고 있습니다.
- 이때, 이 함수가 넘겨받는 `R,t`는 `C2W`에 해당하므로 inverse를 취하면 `W2C`이 됩니다.

```python
# 3dgs/utils/camera_utils.py

...

def camera_to_JSON(id, camera : Camera):
    Rt = np.zeros((4, 4))
    Rt[:3, :3] = camera.R.transpose()
    Rt[:3, 3] = camera.T
    Rt[3, 3] = 1.0

    W2C = np.linalg.inv(Rt)
    pos = W2C[:3, 3]
    rot = W2C[:3, :3]
    serializable_array_2d = [x.tolist() for x in rot]
    camera_entry = {
        'id' : id,
        'img_name' : camera.image_name,
        'width' : camera.width,
        'height' : camera.height,
        'position': pos.tolist(),
        'rotation': serializable_array_2d,
        'fy' : fov2focal(camera.FovY, camera.height),
        'fx' : fov2focal(camera.FovX, camera.width)
    }
    return camera_entry
```


### `getWorld2View`, `getWorld2View2`로 반환된 일반적인 4x4 `w2c = world-to-camera = world-to-view = self.world_view_transform`에  `transpose(0, 1)`가 행해집니다.
```python
      self.world_view_transform = torch.tensor(getWorld2View2(R, T, trans, scale)).transpose(0, 1).cuda()
```
- transpose된 `w2c`은 아래와 같습니다.

$$
W2C^T = \begin{bmatrix}
R_{11} & R_{21} & R_{31} & 0 \\
R_{12} & R_{22} & R_{32} & 0 \\
R_{13} & R_{23} & R_{33} & 0 \\
T_x & T_y & T_z & 1
\end{bmatrix}
$$

### 같은 맥락으로 `getProjectionMatrix`도 일반적인 4x4 행렬 형태에서 `transpose(0, 1)`가 행해집니다.
```python
        self.projection_matrix = getProjectionMatrix(znear=self.znear, zfar=self.zfar, fovX=self.FoVx, fovY=self.FoVy).transpose(0,1).cuda()
```

$$
projection \ matrix ^T = \begin{bmatrix}
P_{11} & P_{21} & P_{31} & P_{41} \\
P_{12} & P_{22} & P_{32} & P_{42} \\
P_{13} & P_{23} & P_{33} & P_{43} \\
P_{14} & P_{24} & P_{34} & P_{44}
\end{bmatrix}
$$

- `self.world_view_transform`는 `world to view`입니다.
- `self.projection_matrx`는 `view to clip space`입니다.
- `self.full_proj_transform`은 `world to clip space = world to view @ view to clip space`입니다.
- 즉, `self.full_proj_transform`은 `wolrd to clip space`입니다.
- `self.world_view_transform`, `self.projection_matrix`는 모두 `transpose(0, 1)`이 된 상태에서 이 둘을 `batch matrix multilplication, bmm` 연산하여 `self.full_proj_transform`을 얻습니다.

```python
        self.world_view_transform = torch.tensor(getWorld2View2(R, T, trans, scale)).transpose(0, 1).cuda()
        self.projection_matrix = getProjectionMatrix(znear=self.znear, zfar=self.zfar, fovX=self.FoVx, fovY=self.FoVy).transpose(0,1).cuda()
        self.full_proj_transform = (self.world_view_transform.unsqueeze(0).bmm(self.projection_matrix.unsqueeze(0))).squeeze(0)
```


------------------

## W2C, C2W 시각화

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/e9a689f2-0c24-4094-9565-b619e0bb156c" alt="Image">
</p>

### Custom Camera Poses 시각화

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/b255e68d-bfd9-48e0-a1e1-61e0fbec1820" alt="Image">
</p>

- Custom dataset에서 camera는 고정하고 face가 돌아가는 상태로 촬영하였습니다.
- 여기서 `f2c`이라고 정의한 것은 `c2f = c2w @ w2g @ g2f`의 연산을 정의한 것입니다.
- `c2f`를 inverse하여 `f2c`인 `face to camera`라고 정의하고 `wcs=np.eyes(4)`인 월드좌표계에서 plot 했습니다.
- 결과를 보면, world를 기준으로 `camera`의 pose들이 plot 됐습니다.
- 결론적으론, `camera의 좌표계 혹은 pose`를 `world 좌표계`에서 봤을 때, 카메라들이 어디에 어떻게 좌표축이 돌아간 상태로 위치하는지 plot한 것입니다.
- 이는 `c2w`와 같은 말입니다.
- 즉, 구한 `f2c`는 이름만 다를 뿐, 역할은 `c2w`의 변환과 동일합니다.
- `f2c`은 **얼굴좌표계에서 카메라좌표계까지의 변환을 수행했을 때, 변환된 pose의 위치**라고 생각하시면 편합니다.
- 즉, `c2w`와 같이 `world를 기준`으로한 `camera pose들`과 같은 맥락으로
- `f2c`은 `face의 위치를 world로써 기준`으로한 `camera pose들`로 해석하면 됩니다.

### NeRF synthetic dataset의 `transforms_train.json`, `transforms_test.json`에서 `frame["transform_matrix"]`로 정의된 `c2w` 시각화

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f87d491e-1d43-4c81-a019-590f5e5b5124" alt="Image">
</p>

## $^{C}{T}_W$ = world-to-camera = `w2c`, $^{W}{T}_C$ = camera-to-world = `c2w `

- `wcs=np.eyes(4)`로 Identity matrix로 정의하고, nerf의 `transform_matrix`를 그대로 plot해보면 `pose`가 `wcs`를 빙 둘러싼 모양으로 나옵니다.
- 방금 우리가 plot한 것은 **`wcs`에서 camera의 위치는 어디인가?** 라는 것과 같습니다.
- 따라서 이는 `camera의 좌표계 혹은 pose`를 `world 좌표계`에서 봤을 때, 카메라들이 어디에 위치하는지를 plot한 것입니다.
- 결론적으로, `camera to world, c2w`로 해석됩니다.
- 3dgs에서는 주석으로 `camera-to-world`에 대한 변환인 `transform_matrix`를 `c2w`라는 주석으로 달아놓은 것을 확인가능합니다.
  
```python
# 3dgs/scene/dataset_readers.py

...

def readCamerasFromTransforms(path, transformsfile, white_background, extension=".png"):
    cam_infos = []

    with open(os.path.join(path, transformsfile)) as json_file:
        contents = json.load(json_file)
        fovx = contents["camera_angle_x"]

        frames = contents["frames"]
        for idx, frame in enumerate(frames):
            cam_name = os.path.join(path, frame["file_path"] + extension)

            # NeRF 'transform_matrix' is a camera-to-world transform
            c2w = np.array(frame["transform_matrix"])
            # change from OpenGL/Blender camera axes (Y up, Z back) to COLMAP (Y down, Z forward)
            c2w[:3, 1:3] *= -1

            # get the world-to-camera transform and set R, T
            w2c = np.linalg.inv(c2w)
            R = np.transpose(w2c[:3,:3])  # R is stored transposed due to 'glm' in CUDA code
            T = w2c[:3, 3]
```

- 중요한 점은 사람에 따라 `world에서 camera로의 변환`을 `c2w`라고 표기할수도 있으므로 주의해야합니다.
- 따라서 항상 `c2w`, `w2c`이 의미하는게 무엇인지 제대로 체크하고 넘어가야합니다.
