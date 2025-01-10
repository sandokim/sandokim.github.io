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

  - **3D Gaussian의 densification(split & clone)이 tangent space위에서 일어나도록 constraint를 적용하였습니다.**

  - Based on the design, we encourage these thin ellipsoids to lie on the surface of smooth regions.

### Tangent space (Normal vector, Tangent vector, Binormal vector)

Tangent space는 3D 모델의 표면에서 정의되는 좌표계를 의미합니다. 

이는 일반적으로 다음 세 가지 벡터로 구성됩니다:

![image](https://github.com/user-attachments/assets/15fffc25-63a1-40e6-94bc-ddbed3fc2d86)

**Normal 벡터**
- 정의: 표면에서 "바깥쪽"을 가리키는 방향 벡터
- 특징: 표면의 평면이나 접선에 수직(90도 각도)

**Tangent 벡터**
- 정의: **표면의 평면 내에 존재하거나 곡면의 한 점에서 접하는 벡터**
- 특징: Normal 벡터와 수직

**Binormal (Bitangent) 벡터**
- 정의: **Normal 벡터와 Tangent 벡터 모두에 수직**인 "다른" 접선 벡터
- 특징: **Normal, Tangent와 함께 직교 좌표계 형성**

이 논문에서는 특히 smooth한 영역에서 thin ellipsoid를 사용할 때 tangent space를 활용합니다:

- Ellipsoid의 회전 행렬 $R$의 세 번째 열을 normal 벡터로 초기화합니다.
- 이를 통해 thin ellipsoid가 smooth한 영역의 표면에 놓이도록 유도합니다.
  
### Co-planar Constraint

**Co-planar constraint는 여러 점이나 벡터가 같은 평면 상에 있도록 하는 제약 조건입니다.**

이 논문에서는 이 개념을 다음과 같이 활용합니다:

- Gaussian 분할: 큰 Gaussian 점을 두 개의 작은 co-planar Gaussian으로 분할할 때, 이들이 원래 Gaussian의 위치와 normal 벡터로 정의된 평면 상에 놓이도록 합니다.
- Gaussian 복제: 새로 생성된 Gaussian이 원본과 같은 평면 상에 있도록 합니다. 이는 원본의 normal 벡터에 수직인 방향으로만 이동을 허용함으로써 달성됩니다.
- 기하학적 일관성 제약: Smooth한 영역에 있는 thin ellipsoid들에 대해, 가장 가까운 이웃들이 co-planar하도록 유도하는 새로운 제약 조건을 제안합니다.

## Method
### 3.2 Gaussian Initialization and Densification

#### Initialization.

we assume that the third column $r_3$ of the rotation matrix from the covariance matrix $\Sigma$ represents the normal while the other two columns are set as a group of normalized basis vectors perpendicular to $r_3$.

#### Densification in the tangential space.

**Cloning.**

Cloning 과정의 수식

The gradient of the position is calculated over 10 iterations [19]. The position $\mu_{i+1}$ of the new Gaussian is obtained when the accumulated gradient ($\delta \mu_i$) exceeds the threshold $\gamma$. 

However, instead of being set along the direction of the gradient, the new position of the clone Gaussian is determined as follows:

$$
\mu^{i+1} = \mu^i + \delta \mu^i - r_3^{\intercal} \delta \mu^i
$$

1. $\mu^i$
- 현재 Gaussian의 위치 (중심점).
- 기존 Gaussian의 중심 위치에서 업데이트 시작.

2. $\delta \mu^i$:
- gradient vector
- 3D Gaussian의 위치를 업데이트하기 위한 방향과 크기를 제공.

3. $r_3 \delta \mu^i$:
- $\delta \mu^i$의 법선 벡터 $r_3$ 방향 성분.
- 그래디언트 벡터가 법선 벡터 r_3에 정렬된 방향 성분을 계산.

4. $\delta \mu^i - r_3 \delta \mu^i$:
- 그래디언트에서 법선 벡터 방향 성분을 제거하여, 법선 벡터에 수직인 방향만 남김.
- 결과: 새로운 Gaussian의 중심 $\mu^{i+1}$은 원래 Gaussian의 평면(법선 $r_3$에 수직)에 위치하도록 보장.

5. 목적:
- 새롭게 clone된 Gaussian이 원래 Gaussian의 평면에 위에 머물도록 하여, 표면의 기하학적 정합성을 유지.  

**Splitting.**

$S_{co}$는 매끄러운 표면(smooth surface)에 위치한 포인트들의 집합입니다. 이 집합은 포인트 클라우드에서 추출된 법선 벡터(normal vector)를 기반으로 분류되며, 다음 과정을 통해 생성됩니다:

법선 벡터 추출:
- 포인트 클라우드 데이터에서 법선 벡터를 추출합니다.
- 법선 벡터의 신뢰도를 평가하는 두 가지 기준:
  - 주변 포인트들과의 거리가 너무 먼 점(외딴 점)은 제거.
  - 가까운 이웃의 법선 벡터와 큰 각도를 이루는 점도 제거.
  - 이러한 과정을 통해 신뢰할 수 있는 포인트만 선택됩니다.

두 그룹으로 분류:

- $S_{co}$ : 매끄러운 표면(smooth surfaces)에 위치한 신뢰도 높은 포인트들의 집합.
- $S_{ind}$ : 독립적이고 구조적 연결성이 부족한 점들의 집합. (geometry 제약이 없는 일반적인 3D Gaussian들의 집합)

Splitting 수식 설명

내적의 성질을 이용해서 새로 복제된 Gaussian이 같은 평면에 있다고 정의합니다. 

먼저 내적은 다음과 같습니다.

![image](https://github.com/user-attachments/assets/c72a10a9-3628-4478-b448-6501b221d9dd)




Splitting 과정은 $S_{co}$ 에 속한 Gaussian들이 매끄러운 표면의 기하학적 정렬성을 유지하면서 분리되도록 설계되었습니다. 이를 수학적으로 표현하면 다음과 같습니다:

$$
[\mu^{i+1} | \mu^{i+1 \intercal} r_3^{i+1} = \mu^{i \intercal}r_3^i, \ \mathcal{G}\_{\theta}^{i+1} \in S_{co}]
$$

1. $\mu^{i+1 \intercal}r_3^{i+1} = \mu^{i \intercal}r_3^i$:
- 새로운 Gaussian $G_{\theta}^{i+1}$의 중심 $\mu^{i+1}$이 기존 Gaussian $G_{\theta}^i$의 법선 벡터 $r_3^i$와 동일한 투영값을 가져야 한다는 조건.
- 이는 새로운 Gaussian이 기존 Gaussian과 동일한 평면에 존재함을 보장합니다.

2. $G_{\theta}^{i+1} \in S_{co}$:
- 새로운 Gaussian $G_{\theta}^{i+1}$은 반드시 매끄러운 표면 영역 $S_{co}$에 속해야 합니다.
- 이는 새로운 Gaussian이 매끄러운 표면의 구조와 정렬성을 유지해야 함을 의미합니다.

Splitting 과정의 의의
- Splitting은 기존 Gaussian을 두 개의 더 작은 Gaussian으로 분리하면서도, 공평면(co-planar) 조건과 매끄러운 표면 정렬성을 유지합니다.
- 새로운 Gaussian들은 기존 Gaussian의 평면 위에 위치하며, $S_{co}$의 기하학적 특성을 따릅니다.



