---
title: "[3D CV] 3DGS test 이미지 고르기"
last_modified_at: 2024-07-29
categories:
  - 공부
tags:
  - Multiple View Geometry
  - COLMAP
  - COLMAP 사용법
  - llffhold
  - colmap data
  - 3dgs
  - readColmapSceneInfo
  - 3dgs eval
  - dataset_readers.py
excerpt: "3DGS에서 test 이미지 골라내는 법"
use_math: true
classes: wide
comments: true
---

### `gaussian_splatting/scene/dataset_readers.py`의 `readColmapSceneInfo`의 `readColmapCamera`에서 원하는 카메라 포즈와 이미지 pair를 가져오도록 해봅시다.

```python
def readColmapSceneInfo(path, images, eval, llffhold=2): # default llffhold=8
    print(f'llffhold sets to {llffhold}..')
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
...
```


```python
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

        if intr.model=="SIMPLE_PINHOLE":
            focal_length_x = intr.params[0]
            FovY = focal2fov(focal_length_x, height)
            FovX = focal2fov(focal_length_x, width)
        elif intr.model=="PINHOLE":
            focal_length_x = intr.params[0]
            focal_length_y = intr.params[1]
            FovY = focal2fov(focal_length_y, height)
            FovX = focal2fov(focal_length_x, width)
        else:
            assert False, "Colmap camera model not handled: only undistorted datasets (PINHOLE or SIMPLE_PINHOLE cameras) supported!"

        image_path = os.path.join(images_folder, os.path.basename(extr.name))
        image_name = os.path.basename(image_path).split(".")[0]
        image = Image.open(image_path)
...
```  

- `readColmapCameras(cam_extrinsics, cam_intrinsics, images_folder)`에서 `cam_extrinsics`를 enumerate하여 extr, image의 pair를 불러오고 있습니다.
- `cam_extrinsics`는 `readColmapSceneInfo`에서 `read_extrinsics_binary`로 `images.bin`을 불러옵니다. (혹은 `read_extrinsics_text`로 `images.txt`을 불러옵니다.)
- `readColmapCameras`에서 `cam_extrinsics`는 for문을 돌며, `cam_extrinsics[key]`로 `extr`로 변수로 받아 사용하고, 여기서 `extr.name`이 `.jpg`, `.JPG`, `.png`, `.PNG`와 같은 extension을 포함한 `image`의 이름입니다.
  - 일반적으로 COLMAP은 `.txt`보다는 `.bin`을 선호하며, COLMAP을 돌릴 때, default로 `images.bin`으로 저장됩니다.

### 가장 간단하게 cam_extrinsics에서 view의 수를 줄여보는 방법은, cam_extrinsics를 index slicing해서 가져올 view의 수를 앞에서부터 자르는 것입니다.

- `cam_extrinsics`는 `camera extrinsics`와 그에 대응하는 `image name`을 짝으로 가지고 있습니다.
- `readColmapSceneInfo`에서 `cam_extrinsics`가 `readColmapCameras`에 들어가기 전에 index slicing을 해주면 됩니다.
- i.e. `cam_extrinsics` for `360_v2/bicycle`
  - `len(cam_extrinsics): 194`
  - `cam_extrinsics는 dictionary type`
  - `cam_extrinsics.keys(): dict_keys([1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30, 31, 32, 33, 34, 35, 36, 37, 38, 39, 40, 41, 42, 43, 44, 45, 46, 47, 48, 49, 50, 51, 52, 53, 54, 55, 56, 57, 58, 59, 60, 61, 62, 63, 64, 65, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90, 91, 92, 93, 94, 95, 96, 97, 98, 99, 100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115, 116, 117, 118, 119, 120, 121, 122, 123, 124, 125, 126, 127, 128, 129, 130, 131, 132, 133, 134, 135, 136, 137, 138, 139, 140, 141, 142, 143, 144, 145, 146, 147, 148, 149, 150, 151, 152, 153, 154, 155, 156, 157, 158, 159, 160, 161, 162, 163, 164, 165, 166, 167, 168, 169, 170, 171, 172, 173, 174, 175, 176, 177, 178, 179, 180, 181, 182, 183, 184, 185, 186, 187, 188, 189, 190, 191, 192, 193, 194])`
  - `keys`는 0이 아닌 1부터 시작하고 있습니다.
    
    ![image](https://github.com/user-attachments/assets/2b9459cc-8df1-4ca0-b15f-a80802a0ed77)


### llffhold=8 을 default로 8개 간격마다 test image를 사용하고, 나머지는 train image로 사용합니다.

- `llffhold`로 간격을 바꿔줍니다.

```python
# scene/dataset_readers.py

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

...

sceneLoadTypeCallbacks = {
    "Colmap": readColmapSceneInfo,
    "Blender" : readNerfSyntheticInfo
}
```

- `readColmapSceneInfo`를 불러쓰는 `scene/__init__.py`를 보면, `llffhold` 인자를 따로 주고 있지 않습니다.
- `readColmapSceneInfo`의 `llffhold`의 default 값을 바꾸어주어 사용합시다.

```python
# scene/__init__.py

...

        if os.path.exists(os.path.join(args.source_path, "sparse")):
            scene_info = sceneLoadTypeCallbacks["Colmap"](args.source_path, args.images, args.eval) # no llffhold arg

...
```
