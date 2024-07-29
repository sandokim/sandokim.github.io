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
- `llffhold`의 default 값을 바꾸어주어 사용합시다.

```python
# scene/__init__.py

...

        if os.path.exists(os.path.join(args.source_path, "sparse")):
            scene_info = sceneLoadTypeCallbacks["Colmap"](args.source_path, args.images, args.eval) # no llffhold arg

...
```
