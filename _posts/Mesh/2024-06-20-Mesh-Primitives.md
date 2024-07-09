---
title: "[Mesh] Primitives (Point, Line, Triangle, Quad, Polygon, Diamond)"
last_modified_at: 2024-06-20
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - primitives
  - point
  - line
  - triangle
  - quad
  - polygon
  - diamond
excerpt: "Primitives (Point, Line, Triangle, Quad, Polygon, Diamond)"
use_math: true
classes: wide
comments: true
---

### Primitives 정의 및 특징

**Primitives**는 3D 모델을 구성하는 가장 기본적인 기하학적 요소입니다. 이들은 복잡한 형상을 구성하기 위해 결합되는 작은 단위입니다. 일반적인 3D primitive 유형은 다음과 같습니다:

1. **Point**
   - **정의**: 공간에서의 단일 좌표를 나타냅니다.
   - **특징**: 단순하지만 복잡한 구조를 만들기 어렵습니다.
   
2. **Line**
   - **정의**: 두 점을 연결한 선분입니다.
   - **특징**: 와이어프레임 모델에 유용합니다.
   
3. **Triangle**
   - **정의**: 세 점을 연결한 면입니다.
   - **특징**: 가장 널리 사용되며, 모든 3D 모델은 삼각형으로 분할될 수 있습니다.
   
4. **Quad**
   - **정의**: 네 점을 연결한 면입니다.
   - **특징**: 다루기 쉬우나, 삼각형으로 변환 후 처리됩니다.
   
5. **Polygon**
   - **정의**: 다수의 점을 연결한 면입니다.
   - **특징**: 복잡한 구조를 만들 수 있으나, 최종적으로 삼각형으로 분할됩니다.
  
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c73fa0f6-d521-4082-964c-66da384c9c0b)

6. **Diamond**
   - **정의**: 두 개의 삼각형을 결합하여 다이아몬드 형태를 이루는 primitive입니다.
   - **특징**: 특수한 그래픽스 알고리즘이나 렌더링 기법에서 사용됩니다. 복잡한 표면의 세부 사항을 더 정밀하게 표현할 수 있습니다.
   - OpenGL에서는 Diamond Primitive를 제공하지 않습니다. SuGaR에서는 triangle 2개로 diamond primitive를 수동으로 정의하여 사용합니다.

### Primitives의 사용 및 렌더링

#### 렌더링(Rendering)

렌더링은 3D 장면을 2D 이미지로 변환하는 과정입니다. 이 과정에는 다음 단계가 포함됩니다:

1. **모델링(Modeling)**: Primitives를 결합하여 3D 모델을 생성합니다.
2. **변환(Transformation)**: 모델 좌표를 카메라 좌표로 변환합니다.
3. **뷰잉(Viewing)**: 카메라 좌표를 정규화된 장치 좌표로 변환합니다.
4. **투영(Projection)**: 3D 좌표를 2D 화면 좌표로 투영합니다.

#### 래스터화(Rasterization)

래스터화는 투영된 2D 좌표를 실제 픽셀로 변환하는 과정입니다. 각 픽셀의 색상과 깊이를 결정하여 이미지를 생성합니다.

### 쉐이딩(Shading)

쉐이딩은 각 픽셀의 최종 색상을 계산하는 과정입니다. 주로 다음 세 가지 종류가 있습니다:

1. **Flat Shading**: 각 primitive에 대해 하나의 색상을 사용합니다.
2. **Gouraud Shading**: primitive의 각 정점에서 색상을 계산하고, 이를 선형 보간하여 내부 픽셀의 색상을 결정합니다.
3. **Phong Shading**: primitive의 각 픽셀에서 색상을 계산합니다. 더 정밀한 조명 효과를 얻을 수 있습니다.

### 각 Primitive의 특징 정리

- **Point**: 단순하지만 복잡한 구조를 만들기 어렵습니다.
- **Line**: 와이어프레임 모델에 유용합니다.
- **Triangle**: 가장 널리 사용되며, 모든 3D 모델은 삼각형으로 분할될 수 있습니다.
- **Quad**: 다루기 쉬우나, 삼각형으로 변환 후 처리됩니다.
- **Polygon**: 복잡한 구조를 만들 수 있으나, 최종적으로 삼각형으로 분할됩니다.
- **Diamond**: 두 개의 삼각형을 결합하여 다이아몬드 형태를 이룹니다. 주로 특수한 그래픽스 알고리즘이나 렌더링 기법에서 사용됩니다. 복잡한 표면의 세부 사항을 더 정밀하게 표현할 수 있습니다.



