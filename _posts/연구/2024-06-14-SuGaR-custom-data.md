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
excerpt: "3DGS Tips for using SuGaR your custom data and obtain better reconstructions"
use_math: true
classes: wide
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

https://github.com/Anttwo/SuGaR?tab=readme-ov-file

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/16d933ba-53b7-4e1e-a7e7-91b2ea8db85a)



