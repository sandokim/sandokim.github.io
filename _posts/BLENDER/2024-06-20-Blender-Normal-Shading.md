---
title: "[BLENDER] Nomral & Shading"
last_modified_at: 2024-06-20
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - Polygon
  - 폴리곤
  - Face
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

## Polygon (폴리곤)
- **정의**: 다각형으로, 컴퓨터 그래픽스에서는 **주로 삼각형이나 사각형 형태로 구성된 3D 모델의 기본 단위.**
- **특징**: **여러 개의 선분으로 이루어진 닫힌 도형. 각 폴리곤은 여러 개의 정점(vertex)과 변(edge)으로 구성됨.**
- **사용처**: 3D 모델을 구성하는 기본 단위로, 복잡한 형상을 단순한 폴리곤의 집합으로 표현함.

## Face (페이스)
- **정의**: **폴리곤의 한 면.**
- **특징**: 3D 모델의 외형을 구성하는 단위로, **여러 개의 페이스가 모여 폴리곤 메쉬를 이룸.** 일반적으로 삼각형이나 사각형 형태로 구성됨.
- **사용처**: 3D 모델을 구성하며, **각 페이스는 표면 노말과 페이스 노말을 가짐.**

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
- **특징**: 폴리곤의 모든 정점이 공유하는 동일한 노말 벡터로, 각 폴리곤의 중심에서 외부로 향함. **점의 순서에 따라 노말의 방향이 달라지므로 점의 순서가 중요.** 반시계 방향으로 점의 순서가 주어질 때 노말이 앞면(표면의 바깥 방향)을 나타냄.
- **사용처**: 주로 평면 셰이딩(flat shading) 기법에서 사용됨. 폴리곤의 평면적 특성을 나타내며, 조명 계산에 사용됨.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f6051d7e-790e-404a-8b81-3fdbda299928)

### 차이점
- **적용 범위**: **Surface Normal은 특정 점**에서의 노말을 의미하며, **Face Normal은 폴리곤 전체**에서의 노말을 의미함.
- **계산 방식**: Surface Normal은 각 정점이나 면에서 수직으로 계산되며, Face Normal은 폴리곤의 모든 정점이 공유하는 노말로 폴리곤의 평면에 수직임.
- **사용 목적**: **Surface Normal은 세부적인 곡면의 방향**을 나타내며, **Face Normal은 폴리곤의 전반적인 방향**을 나타냄.

-----

## Shading (셰이딩)
- **정의**: 컴퓨터 그래픽스에서 폴리곤이 차지하는 각 픽셀의 색을 결정하는 과정.
- **사용처**: 조명을 적용하여 색과 명암을 표현하는 데 사용됨.
- **종류**:
  - Flat Shading,
  - Smooth Shading
    - Gouraud Shading
    - Phong Shading

#### Flat Shading (플랫 셰이딩)
- **정의**: 각 폴리곤의 하나의 face normal을 사용하여 폴리곤 전체의 색을 동일하게 계산하는 셰이딩 기법.
- **특징**: 각 픽셀별로 계산하는 것이 아니라 **폴리곤 하나에 대해 한 번만 조명 계산**을 수행함. 따라서 계산이 적어 빠르지만, **곡면(mesh)에서는 각져 보이는 단점**이 있음.
- **사용처**: 단순한 형태의 물체나 빠른 렌더링이 필요한 경우에 적합.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2d6255bf-b405-419d-8a0e-357979058df9)

#### Smooth Shading (스무스 셰이딩)
- **정의**: 인접한 폴리곤(i.e. 인접한 삼각형) 사이의 color transition(색상 전이)를 부드럽게 하는 셰이딩 기법.
- **특징**: **face normal 대신 정점(vertex) 노말을 사용함. vertex normal 인접한 폴리곤들의 페이스 노말의 평균 벡터. 계산량이 많지만 곡면 표현에 적합함.**
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fcac374d-f2fe-4102-82ce-7e6ea270b606)

- **종류**:
  - Gouraud Shading
  - Phong Shading
  
#### Gouraud Shading (구로 셰이딩)
- **정의**: 각 정점에서 조명을 계산한 후 폴리곤 내부를 보간(interpolation)하여 셰이딩하는 기법.
- **특징**: 각 정점에서 색을 계산하고, 폴리곤 내부는 vertex들의 색을 보간(interpolation)하여 색을 채움. 이 과정에서 Barycentric interpolation을 이용함. interpolation을 하기 때문에 색상이 부드러워지지만, specular highlight가 평균화되기 때문에 부정확해질 수 있음. 폴리곤의 개수를 늘려서 개선할 수 있지만, 계산량이 증가함.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/49ec18ad-7fcd-4e84-b1b1-de32b4b4f0e9)
- **사용처**: 실시간 렌더링이 필요한 경우나 부드러운 색 전환이 필요한 경우에 적합.

#### Phong Shading (퐁 셰이딩)
- **정의**: 각 픽셀에서 노말 벡터를 보간하여 조명을 계산하는 셰이딩 기법.
- **특징**: 각 정점의 노말(vertex normal)을 보간하여 폴리곤 내부의 ***각 픽셀별로 조명을 계산함.*** interpolate된 normal이 실제 surface normal에 인접하기 때문에 훨씬 더 자연스러운 하이라이트 표현이 가능. ***그러나 폴리곤(즉, 삼각형) 내부의 각 픽셀에 대해 조명 계산을 수행해야 하기 때문에 계산량이 많음.***
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/dd5476b5-8845-4ede-8081-a499d673b2ea)
- **사용처**: 고품질의 그래픽 표현이 필요할 때 적합.

### Gouraud Shading vs. Phong Shading
- **Gouraud Shading**: **삼각형 하나당 3번의 조명 계산을 수행.** 계산량이 적고 빠르지만, specular highlight가 부정확할 수 있음.
- **Phong Shading**: **각 픽셀에서 조명 계산**을 수행하여 매우 부드러운 결과를 얻을 수 있음. 폴리곤(즉, 삼각형) 내부의 각 픽셀에 대해 조명 계산을 수행하기 때문에 계산량이 많아지지만, 자연스러운 하이라이트를 표현할 수 있음.

### Reference
[혼자하는 코딩 님의 Shading 정리 블로그](https://gofo-coding.tistory.com/entry/Shading#title-3)

