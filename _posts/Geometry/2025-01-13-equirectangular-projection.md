---
title: "[Geometry] 360 degree scene reconstruction, panoramic inputs, equirectangular projection"
last_modified_at: 2025-01-13
categories:
  - Geometry
tags:
  - Geometry
  - Geometric constraints
  - 3D Gaussian Splatting
  - equirectangular projection
  - 360 degree scene reconstruction
  - panoramic inputs
  - sparse-view inputs
  - room layout
  - panoramic room layout estimation
  - layout-guided point cloud
  - layout point cloud
  - depth point cloud
excerpt: "Geometric constraint in 3DGS"
use_math: true
classes: wide
comments: true
---

> Reference

> [360-GS: Layout-guided Panoramic Gaussian Splatting For Indoor Roaming](https://arxiv.org/pdf/2402.00763)

> [Equirectangular Image (등장방형 이미지) 설명 | 이미지 좌표 변환 | 구면좌표 벡터 계산](https://mvje.tistory.com/211)

> [[논문리뷰] 360-GS: Layout-guided Panoramic Gaussian Splatting For Indoor Roaming](https://kimjy99.github.io/%EB%85%BC%EB%AC%B8%EB%A6%AC%EB%B7%B0/360-gs/)

_파노라마 이미지는 가로로 360° 전체와 세로로 180° 전체를 포함합니다. 결과적으로, 이미지의 상단과 하단은 심각하게 왜곡되어 나타납니다._

Equirectangular image는 구면 좌표계를 직각 좌표계로 매핑한 이미지입로 구면 좌표계의 각도를 직각 좌표계의 픽셀로 일대일 매핑하여 표현합니다.

Equirectangular Projection(등 장방형 도법)은 구체 형태의 파노라마 이미지를 표시하기 위해 주로 사용되는 투영법입니다.

구면 좌표계에서는 위도(latitude)와 경도(longitude)로 이미지를 표현하는데, 위도는 구의 수평 방향을 나타내며, -90에서 90도까지의 범위를 가지고 경도는 구의 수직 방향을 나타내며, -180에서 180도까지의 범위를 가집니다. 

Equirectangular 이미지는 이러한 구면 좌표계를 사용하여 이미지를 표현합니다. 

이미지의 너비는 360도의 경도 범위에 매핑되고, 높이는 -90에서 90도의 위도 범위에 매핑됩니다. 따라서 이미지의 크기는 경도와 위도의 해상도에 따라 결정됩니다.

이러한 Equirectangular 이미지는 주로 360도 동영상, 가상 현실(VR) 이미지, 행성 표면 이미지 등과 같은 구형 데이터를 표현하는 데 사용되는데, 이러한 이미지는 구면 좌표계의 특성을 보존하면서도 직각 좌표계에서의 이미지 처리 및 분석을 가능하게 합니다.

![image](https://github.com/user-attachments/assets/33edaad8-ab12-472e-a591-da73d2f231d4)

----------

### 파노라마 투영에서 3D-2D 가우시안 매핑의 한계

Since the local affine approximation relies on projective transformation, it is not suitable for mapping 3D Gaussians to 2D Gaussians on panoramic images. A panoramic image covers the whole 360◦ horizontally and the whole 180◦ vertically.

Consequently, the top and bottom of the image appear severely distorted. 

**As illustrated in Fig. 3, the panoramic projection assumes distinct shapes that can not be modeled with Gaussians under varying configurations.**

Employing a 2D Gaussian for fitting such a projection would lead to significant errors.

**로컬 아핀 근사는 투영 변환(projective transformation)에 의존하기 때문에, 3D 가우시안(3D Gaussians)을 파노라마 이미지(panoramic images) 상의 2D 가우시안(2D Gaussians)으로 매핑하는 데 적합하지 않습니다.**

**파노라마 이미지는 가로로 360° 전체와 세로로 180° 전체를 포함합니다. 결과적으로, 이미지의 상단과 하단은 심각하게 왜곡되어 나타납니다.**

**그림 3에 나타난 것처럼, 파노라마 투영(panoramic projection)은 다양한 구성(configuration) 하에서 가우시안으로 모델링할 수 없는 독특한 형태를 가정합니다.**

이러한 투영에 대해 2D 가우시안을 사용하여 적합(fitting)하면 상당한 오류가 발생할 수 있습니다.

![image](https://github.com/user-attachments/assets/f2bf93fb-c12a-4e17-bb76-3985976916fb)

_**An alternative approach for the application of 3D-GS to panoramic inputs involves transforming the panoramas into perspective images before optimizing 3D Gaussians.**_

**3DGS를 panoramic inputs에 적용하는 대안적인 접근법은, panormaic inputs을 perspective image로 변환한 후 3D 가우시안을 최적화하는 방식입니다.**

**그러나 이처럼 naive하게 panoramic inputs을 perspective image로 변환후 3DGS에 넣어 3DGS를 최적화하고,**

**rendering된 perspective image들을 다시 panormaic inputs으로 stiching하면 stichiting artifacts가 발생합니다.**

그렇다고 단순히 panormaic inputs을 3DGS에 넣어 최적화하면 3DGS가 파노라마 데이터를 효과적으로 처리하지 못해, 학습이 실패합니다.

![image](https://github.com/user-attachments/assets/7b836655-332c-4d9f-a11e-591847da4aeb)

1. Left (왼쪽 파이프라인)

- 파노라마 이미지를 여러 개의 원근(perspective) 이미지로 나눔:
  - 파노라마 데이터를 작은 조각으로 쪼개서 일반적인 원근 투영 이미지를 만듭니다.
  - 각각의 조각은 파노라마 데이터를 특정 카메라 위치에서 본 것처럼 처리됩니다.
- 3D-GS에 입력:
  - 이렇게 생성된 원근 이미지를 3D-GS 모델에 입력으로 넣어 학습을 진행합니다.
- 파노라마 재구성:
  - 3D-GS를 사용해 원근 이미지를 렌더링한 후, 이를 다시 파노라마로 합칩니다.
  - 문제점: 이 과정에서 이미지 조각들을 이어붙이는(stitching) 과정에서 연결 부위에 인공적인 왜곡(stitching artifacts)이 생깁니다.

2. Right (오른쪽 파이프라인)
   
- 파노라마 데이터를 그대로 입력:
  - 파노라마 데이터를 원근 이미지로 쪼개지 않고, 그대로 3D-GS에 직접 입력합니다.
- 문제점:
  - 3D-GS는 기본적으로 원근 투영 데이터를 처리하도록 설계되어 있기 때문에, 파노라마 입력에 적합하지 않아서 학습이 실패합니다.

#### 요약
- Left 방법: 파노라마를 작은 원근 이미지로 나눈 뒤 학습하고, 이를 다시 합치는 방식 → "이어붙이는 과정에서 왜곡 발생"
- Right 방법: 파노라마를 그대로 입력으로 사용 → "3DGS가 파노라마 데이터를 제대로 처리하지 못해 학습 실패"
- 따라서, 두 방법 모두 파노라마 데이터를 효과적으로 처리하기에는 문제가 있다는 점을 지적한 내용입니다.

**360-GS의 저자는 위와 같이 panormaic inputs을 그대로 쓰는 것에 문제가 있음을 착안하고, 이를 해결하기 위해 tangent plane에 먼저 3d gaussian을 splatting하고 spherical surface로 mapping하는 방식을 사용했습니다.**

목표는 파노라마 집합에서 3D 가우시안 표현을 최적화하고, 직접적인 파노라마 렌더링을 가능하게 하는 것입니다. 

구면 투영을 직접적으로 표현하는 데 있어 발생하는 어려움을 고려하여, 저자는 구면 표면(spherical surface)에 대한 splatting을 두 단계로 분해하는 새로운 splatting 기법을 소개합니다. 

- 첫 번째 단계는 단위 구의 접선 평면(tangent plane)에서 splatting을 수행하고, 
- 두 번째 단계는 이를 구면 표면(spherical surface)으로 매핑하는 것입니다. 

이를 통해 3D 가우시안을 2D 가우시안으로 투영하여 렌더링을 가능하게 합니다. 

360° 가우시안 splatting 기법의 개요는 그림 5에 나와 있습니다.

![image](https://github.com/user-attachments/assets/4d0198be-7569-4fd8-a894-1f2a10b7f8cf)

