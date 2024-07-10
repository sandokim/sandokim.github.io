---
title: "[3D CV 연구] 3DGS Tips for using SuGaR your custom data and obtain better reconstructions"
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
  - ffmpeg
excerpt: "3DGS Tips for using SuGaR your custom data and obtain better reconstructions"
use_math: true
classes: wide
comments: true
---

## Tips for 3D Scene Reconstruction

### Capture images or a video
- 스마트폰이나 카메라를 사용하여 3D 장면의 전체 표면을 촬영합니다.

### Move around the scene
- 장면 주위를 이동하며 영상을 녹화합니다.

### Move slowly and smoothly
- 모션 블러를 피하기 위해 천천히 부드럽게 움직입니다.

### Consistent reconstruction
- 일관된 재구성을 위해 일정한 초점 거리와 일정한 노출 시간을 유지합니다.

### Disable auto-focus
- 스마트폰의 자동 초점 기능을 비활성화하여 초점 거리가 일정하게 유지되도록 합니다.

### Cover objects from several angles
- 여러 각도에서 객체를 촬영하여 얇고 세부적인 부분도 충분히 덮이도록 합니다.

### Artifacts
- SuGaR는 매우 얇고 세부적인 객체를 재구성할 수 있지만, 이러한 얇은 객체가 충분히 덮여 있지 않고 학습 이미지에서 한쪽 면에서만 보이는 경우 일부 아티팩트가 나타날 수 있습니다.

## SuGaR의 scene reconstruction 자세한 설명

### Poisson reconstruction
- SuGaR는 학습 이미지에서 보이는 표면 부분에 3D 포인트를 샘플링하여 Poisson reconstruction을 적용합니다.

### Visibility constraint
- 가시성 제약은 장면 표면 뒤에 위치한 Gaussian 레벨 셋의 뒷면에 샘플링 포인트가 놓이는 것을 방지합니다.
- 이 제약이 없으면 self-collisions와 많은 불필요한 vertices를 생성하여 재구성 후 mesh에 많은 문제를 일으킬 수 있습니다.

### Limitations of visibility constraint
- 이 가시성 제약으로 인해 SuGaR는 학습 이미지에서 보이지 않는 표면 부분을 scene reconstruction할 수 없습니다.
- 얇은 객체가 학습 이미지에서 한쪽 면에서만 보이는 경우, Poisson reconstruction은 closed surface를 재구성하려 하며, 얇은 객체의 표면을 확장하게 되어 inaccurate mesh를 생성합니다.

### Artifacts in thin objects
- 얇은 객체가 한쪽 면에서만 보이는 경우, 표면이 확장되어 부정확한 mesh가 생성됩니다.
- 이러한 artifacts는 학습 이미지에서 충분히 덮여 있지 않은 얇은 객체에서 발생합니다.

### Hybrid representation
- 이러한 artifacts는 하이브리드 표현에서 보이지 않습니다.
- Gaussian texturing이 refinement 과정에서 이러한 artifacts에 low-opacity를 부여하기 때문입니다.

## Avoiding Artifacts in SuGaR

### Identifying new camera poses
- 새로운 카메라 포즈를 식별하여 학습 이미지에서 보이지 않는 표면 부분을 덮습니다.
- 이러한 포즈는 보이는 부분과 same level set에 있을 가능성이 높은 부분을 포함합니다.

### Adding camera poses
- Poisson reconstruction을 적용할 때 포인트 샘플링에 사용되는 카메라 세트에 이러한 카메라 포즈를 추가합니다.
- 이러한 아이디어를 코드에 반영하여 곧 업데이트할 예정입니다.

## Converting Video to Images with ffmpeg

###  Install ffmpeg
비디오를 이미지로 변환하려면 ffmpeg을 설치합니다.

### Run command
```python
ffmpeg -i <Path to the video file> -qscale:v 1 -qmin 1 -vf fps=<FPS> %04d.jpg
```
- 여기서 <FPS>는 비디오 이미지의 샘플링 속도를 나타냅니다.
- FPS 값이 1이면 초당 하나의 이미지를 샘플링합니다.
- **-qscale:v**는 비디오 품질을 제어하는 옵션입니다.
  - 값이 낮을수록 품질이 높고 파일 크기가 커집니다. 여기서 **1은 최고 품질을 의미**합니다.
- **-qmin**은 비디오의 최소 품질 수준을 설정합니다.
  - **1로 설정하면 최소 품질을 최고로 설정**합니다.  
-**vf fps=60**:
  - -vf 옵션은 다양한 비디오 필터를 적용할 수 있도록 하며, 그 중 하나가 fps=60인 것입니다.
  - fps=60은 비디오의 프레임 속도를 초당 60 프레임으로 설정하는 필터입니다.
 
### 샘플링 속도는 비디오 길이에 맞추어 조정하여 샘플링된 이미지 수가 100에서 300 사이가 되도록 하는 것이 좋습니다.

#### 60fps로 촬영된 21초의 비디오인 경우
- **총 프레임 수 계산**: 60 fps × 21 seconds = 1260 frames
- **프레임 간격 계산**: 원하는 이미지 수 \(N\)가 100 ~ 300 사이여야 하므로,
  - $1260 / 300 ≤ 프레임 간격 ≤ 1260 / 100$
  - 즉, $4.2 ≤ 프레임 간격 ≤ 12.6$
  - 프레임 간격을 정수로 만들어야 하므로,
  - $5 ~ 12 프레임 간격$이 될 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/16d933ba-53b7-4e1e-a7e7-91b2ea8db85a)



