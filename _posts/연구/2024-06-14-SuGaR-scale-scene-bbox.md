---
title: "[3D CV 연구] 3DGS SuGaR Adapt the scale and the bounding box of the scene"
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
  - adapt the scale of the scene
  - bounding box of the scene
excerpt: "3DGS SuGaR Adapt the scale and the bounding box of the scene"
use_math: true
classes: wide
---

## Adapting Scale and Bounding Box for Scene Reconstruction in SuGaR

### 1. Scaling for Large scenes
- 장면이 매우 크면 학습률을 낮추어야 합니다.
- 즉, Gaussians의 positions와 scaling factors의 학습률을 낮춥니다.
- 특히, 도시 구역과 같은 큰 데이터셋의 scene reconstruction 때 이 값들을 낮추는 것이 좋습니다.

### 2. Bounding Box for Foreground and Background Gaussians

#### Default bounding box
- 기본적으로, 모든 카메라 중심의 bounding box로 계산됩니다.
- 이 접근 방식은 3D Gaussian Splatting의 학습률 스케일링 방법과 일치합니다.
- 논문 및 프레젠테이션 비디오에 표시된 모든 scene reconstruction에서는 이 기본 bounding box를 사용했습니다.

#### Custom bounding box
- 특정 object를 고해상도로 재구성하거나 장면이 매우 크거나 카메라 중심이 장면에서 멀리 떨어져 있는 경우 기본 bounding box가 최적이 아닐 수 있습니다.
- 사용자 정의 bounding box를 train.py 스크립트에 제공할 수 있습니다.
- --bboxmin 및 --bboxmax 파라미터를 사용하여 bounding box를 지정합니다.
- bounding box는 문자열로 제공되어야 하며, "(x,y,z)" 형식으로 최소 및 최대 점의 좌표를 지정합니다.

```python
python train.py --bboxmin "(x_min,y_min,z_min)" --bboxmax "(x_max,y_max,z_max)"
```






