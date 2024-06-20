---
title: "[BLENDER] Nomral & Shading"
last_modified_at: 2024-06-20
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - Normal
  - Surface Normal
  - Face Normal
  - Flat Shading
  - Smooth Shading
  - Gouraud Shading
  - Phong Shading
  - Shader
  - Shading
excerpt: "Surface Normal, Face Normal, Flat Shading, Smooth Shading, Gouraud Shading, Phong Shading"
use_math: true
classes: wide
---

## Normal (노말)
- **정의**: 표면의 방향을 나타내는 벡터.
- **특징**: 보통 길이가 1인 유닛 벡터(unit vector)를 사용. 표면의 기울기나 방향을 나타냄.
- **사용처**: 조명 계산, 충돌 감지, 텍스처 매핑 등에서 사용됨.

### Surface Normal과 Face Normal의 차이점

#### Surface Normal (표면 노말)
- **정의**: 어떤 점에서 표면에 수직인 벡터.
- **특징**: 보통 길이가 1인 유닛 벡터(unit vector)를 사용. 디퓨즈 반사(diffuse reflection)와 스페큘러 반사(specular reflection)에서 중요한 역할을 함.
- **사용처**: 개별 점에서의 표면 방향을 정의하며, 다양한 그래픽 연산에 사용됨.

#### Face Normal (페이스 노말)
- **정의**: 하나의 폴리곤(face)에서의 표면 노말.
- **특징**: 폴리곤의 모든 정점이 공유하는 동일한 노말 벡터로, 각 폴리곤의 중심에서 외부로 향함. 점의 순서에 따라 노말의 방향이 달라지므로 점의 순서가 중요. 반시계 방향으로 점의 순서가 주어질 때 노말이 앞면(표면의 바깥 방향)을 나타냄.
- **사용처**: 주로 평면 셰이딩(flat shading) 기법에서 사용됨. 폴리곤의 평면적 특성을 나타내며, 조명 계산에 사용됨.

### 차이점
- **적용 범위**: Surface Normal은 특정 점에서의 노말을 의미하며, Face Normal은 폴리곤 전체에서의 노말을 의미함.
- **계산 방식**: Surface Normal은 각 정점이나 면에서 수직으로 계산되며, Face Normal은 폴리곤의 모든 정점이 공유하는 노말로 폴리곤의 평면에 수직임.
- **사용 목적**: Surface Normal은 세부적인 곡면의 방향을 나타내며, Face Normal은 폴리곤의 전반적인 방향을 나타냄.

-----

## Shading (셰이딩)
- **정의**: 컴퓨터 그래픽스에서 폴리곤이 차지하는 각 픽셀의 색을 결정하는 과정.
- **사용처**: 조명을 적용하여 색과 명암을 표현하는 데 사용됨.
- **종류**: Flat Shading, Smooth Shading (Gouraud Shading, Phong Shading 포함)

#### Flat Shading (플랫 셰이딩)
- **정의**: 각 폴리곤의 하나의 페이스 노말을 사용하여 폴리곤 전체의 색을 동일하게 계산하는 셰이딩 기법.
- **특징**: 각 픽셀별로 계산하는 것이 아니라 폴리곤 하나에 대해 한 번만 조명 계산을 수행함. 따라서 계산이 적어 빠르지만, 곡면(mesh)에서는 각져 보이는 단점이 있음.
- **사용처**: 단순한 형태의 물체나 빠른 렌더링이 필요한 경우에 적합.

#### Smooth Shading (스무스 셰이딩)
- **정의**: 인접한 폴리곤 사이의 색상 전이를 부드럽게 하는 셰이딩 기법.
- **특징**: 페이스 노말 대신 정점(vertex) 노말을 사용함. 정점 노말은 인접한 폴리곤들의 페이스 노말의 평균 벡터. 계산량이 많지만 곡면 표현에 적합함. 대표적으로 Gouraud Shading과 Phong Shading이 있음.

#### Gouraud Shading (구로 셰이딩)
- **정의**: 각 정점에서 조명을 계산한 후 폴리곤 내부를 보간하여 셰이딩하는 기법.
- **특징**: 각 정점에서 색을 계산하고, 폴리곤 내부는 보간(interpolation)하여 색을 채움. 이로 인해 색상이 부드러워지지만, specular highlight가 부정확할 수 있음. 폴리곤의 개수를 늘려서 개선할 수 있지만, 계산량이 증가함.
- **사용처**: 실시간 렌더링이 필요한 경우나 부드러운 색 전환이 필요한 경우에 적합.

#### Phong Shading (퐁 셰이딩)
- **정의**: 각 픽셀에서 노말 벡터를 보간하여 조명을 계산하는 셰이딩 기법.
- **특징**: 각 정점의 노말을 보간하여 폴리곤 내부의 각 픽셀별로 조명을 계산함. 보간된 노말이 실제 표면 노말에 가깝기 때문에 자연스러운 하이라이트 표현이 가능. 그러나 계산 비용이 큼.
- **사용처**: 고품질의 그래픽 표현이 필요할 때 적합.

### Gouraud Shading vs. Phong Shading
- **Gouraud Shading**: 삼각형 하나당 3번의 조명 계산을 수행. 계산량이 적고 빠르지만, 스페큘러 하이라이트가 부정확할 수 있음.
- **Phong Shading**: 각 픽셀에서 조명 계산을 수행하여 매우 부드러운 결과를 얻을 수 있음. 계산량이 많아지지만 자연스러운 하이라이트를 표현할 수 있음.





### Reference


