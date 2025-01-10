---
title: "[Geometry] Geometric Constraints on Tangent Space and Co-planar Constraint in 3DGS"
last_modified_at: 2025-01-10
categories:
  - Geometry
tags:
  - Geometry
  - Geometric constraints
  - 3D Gaussian Splatting
  - GeoGaussian
  - tangent space
  - co-planar constraint
excerpt: "Geometric constraint in 3DGS"
use_math: true
classes: wide
comments: true
---

> Reference

> [GeoGaussian: Geometry-aware Gaussian Splatting for Scene Rendering](https://arxiv.org/pdf/2403.11324)

### Geometric Constraints: Tangent Space Constraint & Co-Planar Constraint

In this paper, we propose a geometry-aware Gaussian Splatting method emphasizing rendering fidelity and geometry structure simultaneously. 

Initially, normal vectors are extracted from input point clouds, and then smoothly connected areas are detected based on normals. 

For general regions, we follow the traditional initialization process [19] that represents every point as a sphere. However, each point is parameterized as a thin ellipsoid with explicit geometric information for smoothly connected areas. 

- Specifically, **the third value of the scale vector $S$ is fixed to make the ellipsoid thin.**
- Additionally, **the third column of rotation matrix $R$ decoupled from the covariance matrix $\Sigma$ is initialized by the normal vector**, as shown in Figure 2.

  ![image](https://github.com/user-attachments/assets/3c9c2753-0e83-49bf-a03b-c78172070351)

Based on the design, we encourage these thin ellipsoids to lie on the surface of smooth regions.

### Tangent space

Tangent space는 3D 모델의 표면에서 정의되는 좌표계를 의미합니다. 

이는 일반적으로 다음 세 가지 벡터로 구성됩니다:

- Tangent 벡터: 표면의 "흐름" 또는 방향을 나타냅니다.
- Bitangent (또는 Binormal) 벡터: Tangent와 수직인 벡터입니다.
- Normal 벡터: 표면에 수직인 벡터입니다.

이 논문에서는 특히 smooth한 영역에서 thin ellipsoid를 사용할 때 tangent space를 활용합니다. 

- 구체적으로:
- Ellipsoid의 회전 행렬 R의 세 번째 열을 normal 벡터로 초기화합니다.
- 이를 통해 thin ellipsoid가 smooth한 영역의 표면에 놓이도록 유도합니다.
  
### Co-planar Constraint

**Co-planar constraint는 여러 점이나 벡터가 같은 평면 상에 있도록 하는 제약 조건입니다.**

이 논문에서는 이 개념을 다음과 같이 활용합니다:

- Gaussian 분할: 큰 Gaussian 점을 두 개의 작은 co-planar Gaussian으로 분할할 때, 이들이 원래 Gaussian의 위치와 normal 벡터로 정의된 평면 상에 놓이도록 합니다.
- Gaussian 복제: 새로 생성된 Gaussian이 원본과 같은 평면 상에 있도록 합니다. 이는 원본의 normal 벡터에 수직인 방향으로만 이동을 허용함으로써 달성됩니다.
- 기하학적 일관성 제약: Smooth한 영역에 있는 thin ellipsoid들에 대해, 가장 가까운 이웃들이 co-planar하도록 유도하는 새로운 제약 조건을 제안합니다.

### 3D Gaussian의 splitting & cloning에서 Tangent space & co-planar constraint의 역할
tangent space와 co-planar constraint의 활용은 3D 모델의 기하학적 구조를 보존하면서 렌더링 품질을 향상시키는 데 중요한 역할을 합니다. 특히 smooth한 표면을 더 정확하게 표현하고, Gaussian의 분포를 기하학적으로 의미 있게 제어하는 데 도움을 줍니다.


