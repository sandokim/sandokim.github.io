---
title: "[3D CV 연구] 3DGS SuGaR benchmark dataset 360_v2 experiments"
last_modified_at: 2024-06-14
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation
  - refinement
  - 360_v2
  - benchmark dataset
  - mesh
excerpt: "3DGS SuGaR benchmark dataset 360_v2 experiments"
use_math: true
classes: wide
---

# COLMAP MipNeRF360 dataset (360_v2)

## garden scene

### 3dgs 7,000 iters output PLY File
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1e8cce0e-12d7-45c3-a6e1-259797a916d9)

### SuGaR coarse_mseh PLY file
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/da9df865-cd56-4516-999e-4e9d965c862c)

### SuGaR refine_mesh OBJ file
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f653288c-5d63-4a73-abdc-980f94b98b99)

refine_mesh에는 verticies lines가 남습니다. 이는 기본적으로 렌더링 소프트웨어가 linear interpolation을 사용하기 때문입니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/15685d90-d1c6-4282-af6e-159bd5e6e4d9)

[Question about how to remove the verticies lines](https://github.com/Anttwo/SuGaR/issues/119)

#### 삼각형 경계선을 부드럽게 만드는 방법: Blender에서 Texture Interpolation 설정하기
3D 모델링과 렌더링을 할 때, UV unwrapping 과정에서 삼각형 경계선에 하얀 선이 나타나는 경우가 종종 있습니다. 이는 Texture Interpolation 방법과 UV 맵핑 방식 때문에 발생할 수 있는 문제입니다. 이 글에서는 이 문제를 해결하는 방법과 그 이유에 대해 설명하겠습니다.

#### 1. 간단한 해결책:
- 삼각형 사이에 나타나는 하얀 선은 렌더링 소프트웨어에서 사용하는 Texture Interpolation 방법과 UV unwrapping 방식 때문에 발생합니다.
- 기본적으로 렌더링 소프트웨어는 Linear Interpolation을 사용합니다.
- 이 문제를 해결하려면 Texture Interpolation 방법을 "Closest pixel"로 변경하면 됩니다.
- 이렇게 하면 삼각형 경계선의 "화이트 노이즈"가 사라지고 메쉬가 훨씬 더 깨끗하게 보일 것입니다.

#### 2. 상세한 설명 (문제의 원리 이해하기):
- **3D 메쉬에 텍스처를 적용하려면 먼저 UV 맵을 사용하여 메쉬를 "unwrapping"해야 합니다.**
- 이 과정은 메쉬의 각 삼각형을 텍스처 이미지의 픽셀 집합에 매핑합니다.
- 이상적으로는 3D 메쉬에서 가까운 삼각형들이 UV 맵에서도 가깝게 매핑되어야 합니다. 그러나 이 문제를 해결하는 것은 매우 어렵습니다.

따라서, 여기서는 매우 단순한 방법을 사용합니다: 모든 삼각형을 UV 맵의 무작위 위치에 나란히 매핑합니다. 이 방법은 간단하지만 사용하는 Texture Interpolation 방법에 따라 artifacts를 생성할 수 있습니다.

#### Texture Interpolation 방법은 3D 메쉬에서 삼각형의 색상을 텍스처의 픽셀 색상을 사용하여 렌더링하는 방법을 결정합니다. 
- 선형 보간(Linear Interpolation)은 텍스처에서 인접 픽셀의 색상을 혼합하는 방식
- 가장 가까운 픽셀(Closest Pixel)은 가장 가까운 픽셀의 색상을 사용하는 방식입니다.
- Blender에서 이 차이를 확인할 수 있습니다.
- "Closest" 설정을 사용하면 텍스처가 올바르게 렌더링됩니다.
- 그러나 "Linear" 설정을 사용하면 삼각형 사이에 하얀 선이 나타납니다. 이는 UV 맵에서 인접한 픽셀이 실제로는 메쉬에서 다른 삼각형에 매핑되어 있기 때문입니다.

예시:
- Linear Interpolation: 삼각형 사이에 하얀 선이 나타나는 것을 볼 수 있습니다.
- Closest Pixel: 하얀 선이 없고 텍스처가 훨씬 더 깨끗하게 보입니다.

추가 팁:
- **Blender에서 SuGaR의 메쉬를 시각화할 때는 "Emission Shader"를 사용하는 것이 좋습니다. 이는 텍스처를 다른 조명/그림자 효과 없이 렌더링하는 가장 간단한 방법입니다.**
- 또 다른 방법은 환경 조명을 사용하는 것이지만, Blender에서는 Emission Shader가 더 간단합니다.

#### 결론
- UV unwrapping 후 삼각형 경계선의 하얀 선 문제는 Texture Interpolation 방법을 "Closest Pixel"로 변경함으로써 해결할 수 있습니다. 
- 이를 통해 메쉬가 더 부드럽고 깨끗하게 보일 것입니다. 
- Emission Shader를 사용하면 더욱 효과적으로 시각화할 수 있습니다.

### SuGaR refined_ply PLY file
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f82511e8-2b3d-453c-aef8-db1e69f4ed53)

