---
title: "[3D CV 연구] 3DGS Tips for using COLMAP"
last_modified_at: 2024-06-14
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - COLMAP
  - custom data for 3dgs
  - capture images or videos
excerpt: "3DGS Tips for using COLMAP"
use_math: true
classes: wide
comments: true
---

## Estimating Camera Poses with COLMAP

### Install COLMAP
- 최신 버전의 COLMAP (가능하면 CUDA 지원)을 설치합니다.
- 사용하려는 이미지를 <location>/input 디렉토리에 넣습니다.

### Run conversion script
- COLMAP을 사용하여 이미지에서 카메라 포즈를 계산하려면 Gaussian Splatting의 원래 구현에서 gaussian_splatting/convert.py 스크립트를 실행합니다:

```python
python gaussian_splatting/convert.py -s <location>
```

- `gaussian-splatting/convert.py`를 보시면, **촬영한 input 이미지들에 대해 Image undistortion까지 수행**하는 것을 볼 수 있습니다.
  
```python
### Image undistortion
## We need to undistort our images into ideal pinhole intrinsics.
img_undist_cmd = (colmap_command + " image_undistorter \
    --image_path " + args.source_path + "/input \
    --input_path " + args.source_path + "/distorted/sparse/0 \
    --output_path " + args.source_path + "\
    --output_type COLMAP")
exit_code = os.system(img_undist_cmd)
if exit_code != 0:
    logging.error(f"Mapper failed with code {exit_code}. Exiting.")
    exit(exit_code)
```


## Handling multiple sub-models
- COLMAP이 모든 이미지를 동일한 모델로 재구성하지 못하고 여러 서브 모델을 생성하는 경우가 있습니다.
- ***기본적으로 convert.py 스크립트는 첫 번째 서브 모델에만 이미지 왜곡 제거를 적용합니다.***
- 첫 번째 서브 모델에 이미지가 몇 개 없는 경우, 가장 큰 서브 모델만 남기고 나머지는 삭제하는 것이 좋습니다.

## Keep the largest sub-model
- 입력 이미지가 있는 소스 디렉토리를 열고, <Source_directory>/distorted/sparse/ 하위 디렉토리를 엽니다.
- 0/, 1/ 등으로 이름 붙여진 여러 하위 디렉토리가 보일 것입니다.
- 가장 큰 파일을 포함하는 하위 디렉토리를 제외한 모든 하위 디렉토리를 삭제하고, 해당 디렉토리를 0/으로 이름을 변경합니다.
- 그런 다음 매칭 과정을 건너뛰고 convert.py 스크립트를 다시 실행합니다:

```python
python gaussian_splatting/convert.py -s <location> --skip_matching
```

## Merging sub-models (optional)
- 서브 모델들이 공통으로 등록된 이미지를 포함하는 경우, COLMAP을 사용하여 단일 모델로 병합할 수 있습니다.
- 단, 서브 모델 병합 후에는 전역 번들 조정을 다시 실행해야 하므로 시간이 많이 소요될 수 있습니다.

https://github.com/Anttwo/SuGaR?tab=readme-ov-file



