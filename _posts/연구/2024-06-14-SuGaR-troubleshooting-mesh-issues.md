---
title: "[3D CV 연구] 3DGS SuGaR Troublshooting Mesh Issues"
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
  - mesh
  - hole in mesh
  - messy ellipsoidal bumps on the mesh surface
  - poisson reconstruction
  - poisson surface reconstruction
excerpt: "3DGS SuGaR Troublshooting Mesh Issues & Poisson Surface Reconstruction"
use_math: true
classes: wide
---

https://github.com/Anttwo/SuGaR?tab=readme-ov-file

## Troubleshooting Mesh Issues in SuGaR

### 1. Handling Holes in the Mesh
- 원인: Poisson mesh의 cleaning 단계가 장면에 비해 너무 과도합니다.
- 해결 방법: vertices_density_quantile을 줄입니다. sugar_extractors/coarse_mesh.py의 43번째 줄을 다음과 같이 변경합니다.

```python
vertices_density_quantile = 0.1
```
를
```python
vertices_density_quantile = 0.05
```
로 변경합니다.

### 2. Fixing Messy Ellipsoidal Bumps on the Mesh Surface
- 원인: Poisson reconstruction에 사용된 기본 하이퍼 파라미터가 Gaussian 크기에 비해 너무 세밀합니다. 특히, camera trajectory가 단순한 foreground object에 매우 가까운 경우 발생할 수 있습니다.
- 해결 방법: poisson_depth를 줄입니다. sugar_extractors/coarse_mesh.py의 42번째 줄을 다음과 같이 변경합니다

```python
poisson_depth = 10
```
를
```python
poisson_depth = 7
```
로 바꿉니다.

- 필요시 poisson_depth = 6 또는 poisson_depth = 8로 조정합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b5865d2b-8715-415d-8899-54b1bd78943a)
[Fig 출처: Poisson Surface Reconstruction](https://www.google.com/url?sa=t&source=web&rct=j&opi=89978449&url=https://hhoppe.com/poissonrecon.pdf&ved=2ahUKEwj1y_LGzOCGAxUqsFYBHamSAy8QFnoECDgQAQ&usg=AOvVaw2ITaXcDu9-WHIsaLHQuh7F)


