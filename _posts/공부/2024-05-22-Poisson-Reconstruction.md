---
title: "[3D CV] Poisson Surface Reconstruction"
last_modified_at: 2024-05-22
categories:
  - 공부
tags:
  - Poisson Surface Reconstruction
  - Poisson reconstruction
  - Screened Poisson Surface Reconstruction
  - Octree
  - poisson depth
excerpt: "Poisson Reconstruction 정리"
use_math: true
classes: wide
comments: true
---

## Poisson Reconstruction 빠른 사용 방법

### Poisson detph

Poisson 재구성은 포인트 클라우드(point cloud) 데이터로부터 매끄러운 표면을 생성하는 방법 중 하나입니다. 이 알고리즘은 포인트 클라우드에서 추출한 normal 벡터를 사용하여 최적의 표면을 계산합니다.

**Poisson Depth = Depth of Octree** 

"poisson_depth"는 옥트리의 최대 깊이를 의미합니다. 깊이가 깊을수록 공간을 더 세밀하게 분할하여 작은 세부 사항까지 표현할 수 있습니다.
- 높은 깊이: 더 작은 셀로 공간을 분할하므로, 더 많은 세부 사항을 캡처할 수 있지만, 노이즈와 작은 결함도 더 많이 나타날 수 있습니다.
- 낮은 깊이: 더 큰 셀로 공간을 분할하므로, 큰 구조를 매끄럽게 표현할 수 있지만, 작은 세부 사항은 놓칠 수 있습니다.
- ***Octree의 깊이를 증가시키면 더 높은 해상도의 함수를 사용하여 indicator function을 맞출 수 있게 되어, 재구성된 표면이 더 세밀한 디테일을 포착할 수 있습니다.***

### Practical Usage of Poisson depth value
- 깊이 감소: 옥트리 깊이를 줄이면, 표면을 덜 세밀하게 분할하게 되어, 작은 결함이 덜 드러나게 됩니다.
- 메쉬 표면에 지저분한 타원형 돌기가 나타나는 문제를 해결하기 위해 옥트리 깊이를 줄이는 것이 권장됩니다.
```python
poisson_depth = 7
```

## [Poisson Surface Reconstruction](https://hhoppe.com/poissonrecon.pdf)
많은 이전 연구들처럼, Poisson Surface Reconstruction은 표면 재구성 문제에 대해 implicit function 프레임워크를 사용하여 접근합니다. 구체적으로, [Kaz05]와 같이, 우리는 3D indicator function χ를 계산합니다 (모델 내부의 점에서는 1로 정의되고, 외부의 점에서는 0으로 정의됩니다). 그런 다음 적절한 isosurface를 추출하여 재구성된 표면을 얻습니다. 우리의 주요 통찰력은 모델 표면에서 샘플링된 oriented points와 모델의 indicator function 사이에 적분적 관계가 있다는 것입니다. 구체적으로, indicator function의 gradient는 거의 모든 곳에서 0인 벡터 필드입니다(이는 indicator function이 거의 모든 곳에서 상수이기 때문입니다). 그러나 표면 근처의 점에서는 gradient가 내부 표면 normal과 같습니다. 따라서 oriented point 샘플은 모델의 indicator function gradient의 샘플로 볼 수 있습니다(그림 1 참조).

- **Poisson Surface Reconstruction**: 많은 이전 연구들처럼, Poisson Surface Reconstruction은 표면 재구성 문제에 대해 implicit function 프레임워크를 사용하여 접근합니다.
- **3D Indicator Function χ**:
  - 모델 내부의 점에서는 1로 정의되고, 외부의 점에서는 0으로 정의되는 3D indicator function χ를 계산합니다.
- **Isosurface 추출**:
  - 계산된 indicator function을 사용하여 적절한 isosurface를 추출함으로써 재구성된 표면을 얻습니다.
- **주요 통찰력**:
  - 모델 표면에서 샘플링된 oriented points와 모델의 indicator function 사이에는 적분적 관계가 있습니다.
- **Gradient의 특성**:
  - Indicator function의 gradient는 거의 모든 곳에서 0인 벡터 필드입니다.
  - 이는 indicator function이 모델 내부에서는 1, 외부에서는 0으로 상수이기 때문에, 그 점들에서의 gradient는 0이 됩니다.
  - 표면 근처의 점에서는 indicator function이 0에서 1로 급격히 변합니다. 이 변화는 표면 normal 방향으로 일어나므로, 이 지점에서의 gradient는 내부 표면 normal과 같습니다.
- **표면 근처의 점에서는**:
  - 표면 근처의 점에서 indicator function의 gradient가 non-zero입니다. 이는 표면에서의 변화(경계 조건) 때문입니다. 이 변화는 표면 normal 방향으로 일어나므로, 이 지점에서의 gradient는 표면 normal의 방향과 크기를 반영합니다.
- **Oriented Point 샘플**:
  - Oriented point 샘플은 모델의 표면에서 가져온 점들과 그 점들에 수직인 normal들을 포함합니다.
  - 이 샘플들은 모델의 indicator function gradient의 샘플로 볼 수 있습니다. 이는 표면 근처의 gradient가 표면 normal과 일치하기 때문입니다.
  - 따라서, oriented points는 표면의 기하학적 특성을 잘 반영하며, 이를 통해 표면을 재구성할 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/45a463ba-40c7-4db9-8d63-03e635e807dd)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a58c7bdf-b406-4e4f-8ffb-9f0145b075f9)

...

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0ed42b76-ac9f-4e79-8bfe-8b7378f3a779)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e90babb4-8f57-4d6f-8583-965631927ad8)

### 4.1. Problem Discretization  
먼저, 문제를 discretize할 함수 공간을 선택해야 합니다. 가장 단순한 접근 방식은 regular 3D grid를 사용하는 것이지만, 이는 고해상도 세부 재구성에 비실용적입니다. 해상도가 높아질수록 공간의 차원이 급격히 증가하여 계산 복잡도가 커지기 때문입니다. 

**다행히도, implicit function의 정확한 표현은 재구성된 표면 근처에서만 필요합니다.** 이는 implicit function을 표현하고 Poisson 시스템을 해결하기 위해 adaptive octree를 사용하는 동기를 제공합니다. Poisson에서 Octree를 사용하는 이유는 computational cost를 줄이기 위해서입니다.

- **Problem Discretization**:
  - 문제를 discretize할 함수 공간 선택이 필요합니다.
    
- **기본 접근 방식**:
  - Regular 3D grid로 시작하는 것이 가장 단순한 접근 방식입니다.
    
- **균일한 구조의 문제점**:
  - 균일한 구조는 고해상도 세부 재구성에 비실용적입니다.
  - 해상도에 따라 공간의 차원이 세제곱으로 증가합니다.
  - surface triangle의 수는 이차적으로 증가합니다.

- **Implicit Function의 정확한 표현**:
  - Implicit function의 정확한 표현은 재구성된 표면 근처에서만 필요합니다.

- **Adaptive Octree 사용 동기**:
  - Implicit function을 표현하고 Poisson 시스템을 해결하기 위해 adaptive octree를 사용합니다.
  - Poisson에서 Octree를 사용하는 이유는 computational cost를 줄이기 위해서입니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c315fa85-9efb-4cd8-a808-dd54781451c0)

### 4.4. Isosurface Extraction

Poisson reconstruction에서는 implicit function을 사용하여 표면을 재구성하고, isosurface extraction을 통해 최종 표면을 얻습니다. 이는 level set 방법의 개념과 유사하지만, Poisson reconstruction 자체는 특정한 방법론이고, isosurface extraction은 이 방법론의 한 부분입니다.

1. **Implicit Function**:
   - Poisson reconstruction의 기본 개념은 implicit function을 사용하는 것입니다. 이 함수는 주로 indicator function으로 정의됩니다.

2. **Indicator Function**:
   - Indicator function은 모델의 내부와 외부를 구분합니다. 모델 내부의 점에서는 1로 정의되고, 외부의 점에서는 0으로 정의됩니다.

3. **Poisson Reconstruction**:
   - Implicit function을 계산하는 방법론으로, 주어진 데이터에서 표면을 재구성합니다.
   - 계산된 implicit function으로부터 적절한 isovalue를 선택합니다.

4. **Isovalue**:
   - Isovalue는 isosurface를 추출하기 위해 선택되는 값입니다. 이 값은 주로 입력 샘플의 위치를 가깝게 근사하도록 선택됩니다.

5. **Isosurface Extraction**:
   - Poisson reconstruction에서 계산된 implicit function과 선택된 isovalue를 사용하여 isosurface를 추출하는 단계입니다.
   - 추출된 isosurface는 재구성된 표면을 나타냅니다.

6. **Level Set 방법**:
   - Level set 방법은 implicit function을 사용하여 표면을 추적하는 방법론입니다.
   - Poisson reconstruction에서 사용될 수 있으며, isosurface extraction과 개념적으로 유사합니다.

### Poisson Reconstruction, Level Set 방법 및 Isosurface Extraction의 관련성

- **Poisson Reconstruction**:
  - Implicit function을 사용하여 표면을 재구성하는 포괄적인 방법론입니다.
  - 계산된 implicit function으로부터 isosurface를 추출하여 최종 표면을 얻습니다.

- **Level Set 방법**:
  - Implicit function을 사용하는 방법론 중 하나로, Poisson reconstruction의 일부로 간주될 수 있습니다.
  - Isosurface extraction과 유사하게 implicit function의 특정 값을 기준으로 표면을 추출하는 과정입니다.

- **Isosurface Extraction**:
  - Poisson reconstruction의 한 단계로, 계산된 implicit function에서 isosurface를 추출합니다.
  - Level set 방법과 유사하게, isosurface extraction은 implicit function의 특정 값을 기준으로 표면을 정의합니다.

따라서, **Poisson reconstruction에서 isosurface extraction과 level set 방법은 동일한 원리를 공유합니다.** **두 방법 모두 implicit function을 사용하여 표면을 정의하고, 이를 통해 재구성된 표면을 얻는 과정입니다.**

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4e8f534b-dc76-47f8-a19c-2149beefa2d4)

...

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3071774f-d92f-48b5-bb0e-3c8d7c2efa41)

### 5.1. Resolution
최대 octree 깊이가 재구성된 표면에 미치는 영향을 고려해봅시다.

- **Resolution**:
  - 최대 octree 깊이가 재구성된 표면에 미치는 영향을 고려합니다.
  - Octree 깊이 6, 8, 10에서 "dragon" 모델의 재구성 결과를 보여줍니다.
  - 정규 그리드에서 이는 각각 64³, 256³, 1024³ 해상도에 해당합니다.

- **트리 깊이 증가의 효과**:
  - ***트리 깊이가 증가함에 따라 더 높은 해상도의 함수가 indicator function을 맞추는 데 사용됩니다.***
  - 그 결과, 재구성된 표면이 더 세밀한 디테일을 포착합니다.

- **세밀한 디테일 포착 예시**:
  - 가장 낮은 해상도에서는 포착하기 어려운 용의 비늘이 트리 깊이가 증가함에 따라 나타나기 시작합니다.
  - 더 높은 해상도에서는 용의 비늘이 더 선명하게 표현됩니다.

***즉, octree의 깊이를 증가시키면 더 높은 해상도의 함수를 사용하여 indicator function을 맞출 수 있게 되어, 재구성된 표면이 더 세밀한 디테일을 포착할 수 있습니다.***

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/88b306fc-5209-4cc4-a8b8-899a87fc3b96)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/57589834-6eb6-43c2-bc2b-40ad93c8e701)


## [Poisson Surface Reconstruction User Guide](https://doc.cgal.org/latest/Poisson_surface_reconstruction_3/index.html)

### 3 Poisson
***Poisson Surface Reconstruction 방법은 3D 점과 그에 대한 노말 정보를 활용하여 추론된 고체의 표면을 정확하게 재구성하는 알고리즘입니다. 이 알고리즘은 주어진 노말 벡터와 잘 맞는 gradient를 가진 indicator function을 계산함으로써 표면의 형태를 추정합니다.***

Given a set of 3D points with oriented normals (denoted oriented points in the sequel) sampled on the boundary of a 3D solid, the Poisson Surface Reconstruction method [2] solves for an approximate indicator function of the inferred solid, whose gradient best matches the input normals. The output scalar function, represented in an adaptive octree, is then iso-contoured using an adaptive marching cubes.

CGAL implements a variant of this algorithm which solves for a piecewise linear function on a 3D Delaunay triangulation instead of an adaptive octree. The algorithm takes as input a set of 3D oriented points. It builds a 3D Delaunay triangulation from these points and refines it by Delaunay refinement so as to remove all badly shaped (non-isotropic) tetrahedra and to tessellate a loose bounding box of the input oriented points. The normal of each Steiner point added during refinement is set to zero. It then solves for a scalar indicator function $f$ represented as a piecewise linear function over the refined triangulation. More specifically, it solves for the Poisson equation

$$ \Delta f = \text{div}(n) $$

at each vertex of the triangulation using a sparse linear solver. Eventually, the CGAL surface mesh generator extracts an isosurface with function value set by default to be the median value of $f$ at all input points.

### 4 Reconstruction Function
A global function poisson_surface_reconstruction_delaunay() is provided. It takes points with normals as input and handles the whole reconstruction pipeline :

- it computes the implicit function
- it reconstructs the surface with a given precision using the CGAL surface mesh generator based on Delaunay refinement [4] [1]
- it outputs the result in a polygon mesh.
  
This function aims at providing a quick and user-friendly API for Poisson reconstruction. Advanced users may be interested in using the class (see Reconstruction Class) which allows them, for example, to use another surface mesher or a different output structure.

...

### 6 Case Studies
The surface reconstruction problem being inherently ill-posed, the proposed algorithm does not pretend to reconstruct all kinds of surfaces with arbitrary sampling conditions. This section provides the user with some hints about the ideal sampling and contouring conditions, and depicts some failure cases when these conditions are not matched.

#### 6.1 Ideal Conditions
**The user must keep in mind that the Poisson surface reconstruction algorithm comprises two phases (computing the implicit function from the input point set and contouring an iso-surface of this function). Both require some care in terms of sampling conditions and parameter tuning.**

#### 6.2 Point Set
Ideally the current implementation of the Poisson surface reconstruction method expects a dense 3D oriented point set (typically matching the epsilon-sampling condition [1]) and sampled over a closed, smooth surface. **Oriented herein means that all 3D points must come with consistently oriented normals to the inferred surface.** Figure 63.3 and Figure 63.4 illustrate cases where these ideal conditions are met.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b9e714e3-d400-4ade-9117-44559c40d40f)

Figure 63.3 Poisson reconstruction. Left: 120K points sampled on a statue (Minolta laser scanner). Right: reconstructed surface mesh.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/36fa135e-3620-4e40-86c6-6e48eb520fc1)

Figure 63.4 Left: 120K points sampled on a statue (Minolta laser scanner). Right: reconstructed surface mesh.

The algorithm is fairly robust to anisotropic sampling and to noise. **It is also robust to missing data through filling the corresponding holes as the algorithm is designed to reconstruct the indicator function of an inferred solid** (see Figure 63.5).

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/737df213-db98-40fc-b81f-5eccc5bc1086)

Figure 63.5 Top left: 65K points sampled on a hand (Kreon laser scanner). Bottom left: the point set is highly anisotropic due to the scanning technology. Right: reconstructed surface mesh and closeup. The holes are properly closed.

The algorithm is in general not robust to outliers, although a few outliers do not always create a failure, see Figure 63.6.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f842579d-5104-4762-b845-70ebbda7a3e8)

Figure 63.6 Left: 70K points sampled on an elephant with few outliers emphasized with disks. Right: reconstructed surface mesh.

The algorithm works well even when the inferred surface is composed of several connected components, provided that both all normals are properly estimated and oriented (the current CGAL normal orienter algorithm may fail in some cases, see mst_orient_normals()), and that the final contouring algorithm is properly seeded for each component. When the inferred surface is composed of several nested connected components care should be taken to orient the normals of each component in alternation (inward/outward) so that the final contouring stage picks a proper contouring value.

#### 6.3 Contouring Parameters
Our implementation of the Poisson surface reconstruction algorithm computes an implicit function represented as a piecewise linear function over the tetrahedra of a 3D Delaunay triangulation constructed from the input points then refined through Delaunay refinement. For this reason, any iso-surface is also piecewise linear and hence may contain sharp creases. As the contouring algorithm make_surface_mesh() expects a smooth implicit function these sharp creases may create spurious clusters of vertices in the final reconstructed surface mesh when setting a small mesh sizing or surface approximation error parameter (see Figure 63.7).

**One way to avoid these spurious clusters consists of adjusting the mesh sizing and surface approximation parameters large enough compared to the average sampling density** (obtained through compute_average_spacing()) **so that the contouring algorithm perceives a smooth iso-surface.** We recommend to use the following contouring parameters:

- Max triangle radius: at least 100 times the average spacing.
- Approximation distance: at least 0.25 times the average spacing.

#### 6.3 한국어 해석
Poisson 표면 재구성 알고리즘을 사용할 때는 표면의 부드러움을 유지하고 메쉬의 품질을 보장하기 위해 파라미터 설정에 주의해야 합니다.

- Implicit Function의 특성: Poisson 표면 재구성 알고리즘은 3D Delaunay triangulation의 tetrahedra 위에 piecewise linear function으로 표현된 implicit function을 계산합니다. 이러한 특성 때문에 모든 iso-surface는 piecewise linear이며, 따라서 날카로운 주름을 포함할 수 있습니다. 이로 인해 표면이 매끄럽지 않게 보일 수 있습니다.
- Contouring 알고리즘의 기대: Contouring 알고리즘인 make_surface_mesh()는 부드러운 implicit function을 기대합니다. 그러나 입력된 iso-surface가 날카로운 주름을 포함하고 있을 경우, contouring 알고리즘은 이를 제대로 처리하지 못해 최종 재구성된 표면 메쉬에 spurious한 클러스터가 생성될 수 있습니다. 이는 특히 작은 mesh 크기 또는 표면 근사 오류 파라미터를 사용할 때 발생할 가능성이 높습니다.
- 파라미터 설정의 중요성: 이러한 문제를 피하기 위해서는 파라미터 설정에 주의해야 합니다. 파라미터를 적절히 설정하면 contouring 알고리즘이 부드러운 iso-surface를 인식할 수 있게 되어, 결과적으로 부드러운 표면과 높은 품질의 mesh를 얻을 수 있습니다. 예를 들어, Max triangle radius와 Approximation distance를 평균 샘플링 밀도에 비해 충분히 크게 설정함으로써, contouring 알고리즘이 부드러운 iso-surface를 인식할 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0a856723-1c2c-4154-bede-91e90aaed839)

Figure 63.7 Left: surface reconstructed with approximation distance = 0.25 * average spacing. Right: surface reconstructed with approximation distance = 0.15 * average spacing. Notice the spurious cluster on the cheek.

#### 6.4 Degraded Conditions
The conditions listed above are rather restrictive and in practice not all of them are met in the applications. We now illustrates the behavior of the algorithm when the conditions are not met in terms of sampling, wrongly oriented normals, noise and sharp creases.

#### 6.5 Sparse Sampling
The reconstruction algorithm expects a sufficiently dense point set. Although there is no formal proof of correctness of the algorithm under certain density conditions due to its variational nature, our experiments show that the algorithm reconstructs well all thin features when the local spacing is at most one tenth of the local feature size (the distance to the medial axis, which captures altogether curvature, thickness and separation). When this condition is not met the reconstruction does not reconstruct the thin undersampled features (see Figure 63.8).

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9bc95c19-a9cc-45ba-9f52-cc37a911b4bc)

Figure 63.8 Left: 50K points sampled on the Neptune trident. The reconstruction (not shown) is successful in this case. Right: point set simplified to 1K points then reconstructed (all input points are depicted with normals). The thin feature is not reconstructed.

#### 6.6 Large Holes
The reconstruction is devised to solve for an implicit function which is an approximate indicator function of an inferred solid. For this reason the contouring algorithm always extracts a closed surface mesh and hence is able to fill the small holes where data are missing due, e.g., to occlusions during acquisition (see Figure 63.9).

#### 6.6 한국어 해석
**Poisson reconstruction은 추론된 solid의 근사 indicator function인 implicit function을 풀기 위해 고안되었습니다. 이러한 이유로, contouring 알고리즘은 항상 closed surface mesh를 추출**하여, 예를 들어 데이터 취득 중 **occlusions로 인해 발생한 작은 구멍을 메울 수 있습니다** (그림 63.9 참조).

- **Implicit function 계산: Poisson reconstruction은 추론된 solid의 근사 indicator function인 implicit function을 계산합니다. 이는 재구성된 표면이 고체의 구조를 잘 반영하도록 합니다.**
- **Closed surface mesh 추출: Contouring 알고리즘은 항상 closed surface mesh를 추출합니다. Closed surface mesh란 모든 구멍이 메워진 완전한 표면을 의미합니다.**
- 작은 구멍 메우기: Closed surface mesh를 생성함으로써, 데이터 취득 중 발생한 작은 구멍을 메울 수 있습니다. 예를 들어, 데이터 취득 시 occlusions로 인해 데이터가 누락된 부분을 채울 수 있습니다.
- 결론적으로, Poisson reconstruction 알고리즘은 데이터 취득 과정에서 발생할 수 있는 결함을 보완하여 보다 완전하고 신뢰할 수 있는 표면 재구성을 제공합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/350dc178-bc95-4d02-bcc9-d86fdc433007)

Figure 63.9 Left: 65K points sampled on a hand with no data captured at the wrist base. Right: reconstructed surface mesh. The surface is properly closed on the fingers and also closed at the wrist but in a less plausible manner.

In case of large holes the algorithm still closes them all but the resulting piecewise linear implicit function may exhibit large triangle patches and sharp creases as the 3D Delaunay triangulation used for solving is very coarse where the holes are filled. This can be avoided by a two pass approach. The first pass for a subset of the points serves to get an approximation of the surface at the holes. This surface then serves to compute a smoother 3D Delaunay triangulation for the second pass with the full set of points.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/46361dd9-0b17-4afb-871b-85f7a1bc2135)

Figure 63.10 Left: The wrist. Middle: one pass. Right: two passes.

#### 6.7 Wrongly Oriented Normals
**The Poisson surface reconstruction approaches solves for an implicit function whose gradient best matches a set of input normals.** Because it solves this problem in the least squares sense, it is robust to few isolated wrongly oriented (flipped) normals. Nevertheless a cluster of wrongly oriented normals leads to an incorrect implicit function and hence to spurious geometric or even topological distortion (see Figure 63.11).

#### 6.7 한국어 해석
Poisson surface reconstruction 방법은 입력된 normal 세트와 가장 잘 맞는 gradient를 가지는 implicit function을 풉니다. 이 문제를 최소 제곱 방식으로 풀기 때문에, 일부 고립된 잘못된 방향(뒤집힌) normals에 대해서는 견고합니다. 그러나 잘못된 방향의 normals가 클러스터로 존재할 경우, 잘못된 implicit function이 생성되어 기하학적 왜곡이나 심지어 위상적 왜곡이 발생할 수 있습니다 (그림 63.11 참조).

- **Implicit function 계산: Poisson surface reconstruction 방법은 a set of input normals과 가장 잘 일치하는 gradient를 가지는 implicit function을 계산합니다. 이는 표면의 모양을 잘 반영하도록 합니다.**
- 최소 제곱 방식의 장점: 이 알고리즘은 최소 제곱 방식을 사용하기 때문에, 일부 고립된 잘못된 방향의 normals에 대해서는 견고합니다. 즉, 몇 개의 잘못된 normals가 있어도 전체 결과에 큰 영향을 미치지 않습니다.
- **클러스터로 존재하는 잘못된 방향의 normals의 문제점: 그러나 잘못된 방향의 normals가 클러스터로 존재하면 문제가 발생합니다. 이러한 클러스터는 잘못된 implicit function을 생성하게 되어, 결과적으로 기하학적 왜곡이나 위상적 왜곡을 초래할 수 있습니다.** 이는 재구성된 표면이 실제 표면과 크게 다를 수 있음을 의미합니다.
- 결론적으로, **Poisson surface reconstruction 알고리즘은 일부 고립된 잘못된 normals에 대해 견고하지만, 클러스터 형태의 잘못된 normals에 대해서는 취약합니다.**

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b0308cc6-d191-4ced-8bbd-915dbeec4502)

Figure 63.11 Left: points sampled on a sphere with a cluster of wrongly oriented normals. Right: reconstructed surface mesh with a spurious bump.

#### 6.8 Noise and Outliers
A large amount of noise inevitably impacts on the reconstruction (see Figure 63.12, top) and the current implementation does not provide any mean to trade data fitting for smoothness. Nevertheless if the signal-to-noise ratio is sufficiently high and/or the surface approximation and sizing parameters set for contouring the iso-surface is large with respect to the noise level the output surface mesh will appear smooth (not shown). If the user wants to produce a smooth and detailed output surface mesh, we recommend to apply smoothing through jet_smooth_point_set() (see Figure 63.12, bottom).

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a741aa30-63eb-44f4-9b61-0b4af877b6c8)

Figure 63.12 Top-left: points sampled on a sphere and corrupted with a lot of noise. Top-right: reconstructed surface mesh. Bottom-left: smoothed point set. Bottom-right: reconstructed surface mesh.

#### 6.9 Sharp Creases
The current reconstruction algorithm is not able to recover the sharp creases and corners present in the inferred surface. This translates into smoothed sharp creases.

#### 6.9 한국어 해석
현재의 Poisson Reconsrcution 알고리즘은 추론된 표면에 존재하는 sharp creases(주름)와 corners(모서리)를 복원할 수 없습니다. 이는 결과적으로 매끄럽게 처리된 sharp creases(주름)로 나타납니다.

- Sharp creases(주름)와 corners(모서리)의 복원 불가능: 현재 사용 중인 재구성 알고리즘은 추론된 표면에 존재하는 sharp creases(주름)와 corners(모서리)를 정확하게 복원할 수 없습니다. 이는 알고리즘이 날카로운 모서리나 주름을 인식하고 이를 그대로 유지하는 데 어려움을 겪는다는 것을 의미합니다.
- 매끄럽게 처리된 sharp creases(주름): 이러한 한계로 인해, sharp creases(주름)는 매끄럽게 처리되어 날카로운 모양이 사라지고 둥근 형태로 변형됩니다. 이는 복원된 표면이 원래의 날카로운 특징을 잃게 만드는 결과를 초래합니다.
- 결론적으로, **현재의 Poisson reconstruction 알고리즘은 날카로운 주름과 모서리를 정확히 복원하지 못해, 결과 표면이 부드럽게 처리**되는 한계가 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3f22a2b3-d0df-4ec2-b07e-3ea4038f1479)

Figure 63.13 Left: 5K points sampled on a mechanical piece with sharp features (creases, darts and corners). Right: reconstructed surface mesh with smoothed creases.


### Reference
- [Poisson Surface Reconstruction User Guide](https://doc.cgal.org/latest/Poisson_surface_reconstruction_3/index.html)
- [Poisson Surface Reconstruction](https://hhoppe.com/poissonrecon.pdf)
- [Screened Poisson Surface Reconstruction](https://www.cs.jhu.edu/~misha/MyPapers/ToG13.pdf)
