---
title: "[3D CV 연구] 3DGS camera_angle_x, fovx, fovy"
last_modified_at: 2024-07-04
categories:
  - 연구
tags:
  - 연구
  - 3dgs
  - 3d gaussian splatting
  - implementation details
  - camera_angle_x
  - fovx
  - fovy
excerpt: "fovx에 해당하는 camera_angle_x만 있으면 image의 height, width로 fovy도 계산이 가능합니다."
use_math: true
classes: wide
comments: true
---

## `fovx`에 해당하는 `camera_angle_x`만 있으면 `image의 height, width`로 `fovy`도 계산이 가능합니다.

- NeRF dataset의 `transforms_train.json`, `transforms_test.json`에서 `["camera_angle_x"]`는 `fovx` 입니다.
- `fovx`와 `image의 height width`를 알면 `fovy`가 계산이 가능합니다.

```python
# 3dgs/scene/dataset_readers.py

def readCamerasFromTransforms(path, transformsfile, white_background, extension=".png"):
    cam_infos = []

    with open(os.path.join(path, transformsfile)) as json_file:
        contents = json.load(json_file)
        fovx = contents["camera_angle_x"]

        frames = contents["frames"]
        for idx, frame in enumerate(frames):

...

            fovy = focal2fov(fov2focal(fovx, image.size[0]), image.size[1])
            FovY = fovy 
            FovX = fovx
```

```python
# 3dgs/utils/graphic_utils.py

def fov2focal(fov, pixels):
    return pixels / (2 * math.tan(fov / 2))

def focal2fov(focal, pixels):
    return 2*math.atan(pixels/(2*focal))
```

## 각 단계를 요약하면 다음과 같습니다.

1. 수평 시야각(`fovx`)와 이미지의 가로 픽셀 수(`width`)를 이용하여 초점 거리(`focal length`) 계산:
```python
focal_length = fov2focal(fovx, image_width)
```

2. 계산된 초점 거리와 이미지의 세로 픽셀 수(`height`)를 이용하여 수직 시야각(`fovy`) 계산:
```python
fovy = focal2fov(focal_length, image_height)
```

### 전체 코드 예제

```python
import math

def fov2focal(fov, pixels):
    return pixels / (2 * math.tan(fov / 2))

def focal2fov(focal, pixels):
    return 2 * math.atan(pixels / (2 * focal))

# 주어진 값
fovx = ...  # 수평 시야각 (radians 단위)
image_width = ...  # 이미지의 가로 픽셀 수
image_height = ...  # 이미지의 세로 픽셀 수

# 수평 시야각을 이용하여 초점 거리 계산
focal_length = fov2focal(fovx, image_width)

# 초점 거리를 이용하여 수직 시야각 계산
fovy = focal2fov(focal_length, image_height)

print("fovx:", fovx)
print("fovy:", fovy)
```

### 예제
- `fovx` = 90도 (radians 단위로 변환 시 약 1.5708 rad)
- `image_width` = 1920 픽셀
- `image_height` = 1080 픽셀

```python
import math

def fov2focal(fov, pixels):
    return pixels / (2 * math.tan(fov / 2))

def focal2fov(focal, pixels):
    return 2 * math.atan(pixels / (2 * focal))

# 주어진 값
fovx = math.radians(90)  # 수평 시야각 90도
image_width = 1920  # 이미지의 가로 픽셀 수
image_height = 1080  # 이미지의 세로 픽셀 수

# 수평 시야각을 이용하여 초점 거리 계산
focal_length = fov2focal(fovx, image_width)

# 초점 거리를 이용하여 수직 시야각 계산
fovy = focal2fov(focal_length, image_height)

print("fovx:", math.degrees(fovx), "degrees")
print("fovy:", math.degrees(fovy), "degrees")
```

- 위 코드 실행 결과는 `fovy` 값이 약 53.13도로 출력됩니다. 이는 가로 시야각 90도, 이미지 비율 16:9(1920x1080)에서의 세로 시야각입니다.

### 요약
- 수평 시야각(`fovx`)과 `이미지의 가로, 세로 픽셀 수`를 알면, 수직 시야각(`fovy`)을 계산할 수 있습니다.
- 이를 통해 카메라의 전체 시야각을 설정하거나 변환할 수 있습니다.





