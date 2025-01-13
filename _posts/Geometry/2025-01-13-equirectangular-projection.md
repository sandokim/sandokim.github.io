---
title: "[Geometry] Equirectangular projection"
last_modified_at: 2025-01-13
categories:
  - Geometry
tags:
  - Geometry
  - Geometric constraints
  - 3D Gaussian Splatting
  - equirectangular projection
  - 360 degree scene reconstruction
excerpt: "Geometric constraint in 3DGS"
use_math: true
classes: wide
comments: true
---

> Reference

> [360-GS: Layout-guided Panoramic Gaussian Splatting For Indoor Roaming](https://arxiv.org/pdf/2402.00763)

> [Equirectangular Image (등장방형 이미지) 설명 | 이미지 좌표 변환 | 구면좌표 벡터 계산](https://mvje.tistory.com/211)

Equirectangular image는 구면 좌표계를 직각 좌표계로 매핑한 이미지입로 구면 좌표계의 각도를 직각 좌표계의 픽셀로 일대일 매핑하여 표현합니다.

Equirectangular Projection(등 장방형 도법)은 구체 형태의 파노라마 이미지를 표시하기 위해 주로 사용되는 투영법입니다.

구면 좌표계에서는 위도(latitude)와 경도(longitude)로 이미지를 표현하는데, 위도는 구의 수평 방향을 나타내며, -90에서 90도까지의 범위를 가지고 경도는 구의 수직 방향을 나타내며, -180에서 180도까지의 범위를 가집니다. 

Equirectangular 이미지는 이러한 구면 좌표계를 사용하여 이미지를 표현합니다. 

이미지의 너비는 360도의 경도 범위에 매핑되고, 높이는 -90에서 90도의 위도 범위에 매핑됩니다. 따라서 이미지의 크기는 경도와 위도의 해상도에 따라 결정됩니다.

이러한 Equirectangular 이미지는 주로 360도 동영상, 가상 현실(VR) 이미지, 행성 표면 이미지 등과 같은 구형 데이터를 표현하는 데 사용되는데, 이러한 이미지는 구면 좌표계의 특성을 보존하면서도 직각 좌표계에서의 이미지 처리 및 분석을 가능하게 합니.

![image](https://github.com/user-attachments/assets/33edaad8-ab12-472e-a591-da73d2f231d4)
