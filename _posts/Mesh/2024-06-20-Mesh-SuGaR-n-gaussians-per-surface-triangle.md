---
title: "[Mesh] SuGaR coarse mesh & refined mesh extraction"
last_modified_at: 2024-06-20
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - Mesh extraction
  - SuGaR
  - surface mesh
  - binding
  - 바인딩
  - n_gaussians_per_surface_triangle
  - 무게중심
  - barycentric coordinates
excerpt: "SuGaR에서 coarse mesh와 refined mesh를 extraction하는 방법"
use_math: true
classes: wide
---


# Gaussian Splatting을 통한 surface mesh 바인딩 방법

이 문서는 SuGaR 모델에서 Gaussian splats를 surface mesh에 바인딩하는 과정을 설명합니다. SuGaR 모델에서 Gaussian splats는 점들로 구성된 3D 공간상의 객체를 나타내며, 이 점들이 특정 표면에 바인딩될 수 있습니다. 이 과정은 모델의 학습과 렌더링 품질에 큰 영향을 미칩니다.

## 바인딩 과정

SuGaR 모델에서는 surface mesh의 각 triangle(=face)에 특정 개수의 Gaussian splats를 할당하여 표면의 형태와 텍스처를 보다 정밀하게 표현할 수 있습니다. 코드에서 각기 다른 `n_gaussians_per_surface_triangle` 값에 대해 다양한 방법으로 바인딩을 수행하는 이유는 다음과 같습니다:

#### 용어 정리
- **triangle**: surface mesh를 구성하는 기본 단위입니다. 각 삼각형은 세 개의 꼭지점을 가지며, 이 꼭지점을 연결하는 변으로 구성됩니다.
- **face**: SuGaR에서는 face가 surface mesh의 triangle과 동일한 의미로 사용됩니다. ***(즉, SuGaR에서는 face = triangle)***
  - **face의 종류**:
    - **triangle (삼각형)**: 세 개의 꼭지점과 세 개의 변으로 구성됩니다.
    - **Qaud (사각형)**: 네 개의 꼭지점과 네 개의 변으로 구성됩니다.
    - **Polygon (다각형)**:  다섯 개 이상의 꼭지점과 변으로 구성됩니다. 예를 들어, pentagon(오각형), hexagon(육각형) 등이 있습니다.
- **Surface Mesh**: 여러 개의 삼각형으로 구성된 3D 모델의 표면을 의미합니다.

------

### `sugar_model.py`에서 각 triangle마다 Gaussian Splats은 무게중심(barycentric coordinates)을 활용하여 배치합니다.

### 1. 1개의 Gaussian splat per 삼각형
각 삼각형의 무게중심을 사용하여 Gaussian splat을 배치합니다. 이 경우 단일 Gaussian splat이 삼각형의 전체를 대표하게 됩니다.

```python
if n_gaussians_per_surface_triangle == 1:
    self.surface_triangle_circle_radius = 1. / 2. / np.sqrt(3.)
    self.surface_triangle_bary_coords = torch.tensor(
        [[1/3, 1/3, 1/3]],
        dtype=torch.float32,
        device=nerfmodel.device,
    )[..., None]
```


### 2. 3개의 Gaussian splats per 삼각형
각 삼각형의 중심과 각 모서리에 가까운 위치에 Gaussian splats를 배치하여 삼각형을 보다 정밀하게 표현합니다.

```python
if n_gaussians_per_surface_triangle == 3:
    self.surface_triangle_circle_radius = 1. / 2. / (np.sqrt(3.) + 1.)
    self.surface_triangle_bary_coords = torch.tensor(
        [[1/2, 1/4, 1/4],
        [1/4, 1/2, 1/4],
        [1/4, 1/4, 1/2]],
        dtype=torch.float32,
        device=nerfmodel.device,
    )[..., None]
```

### 3. 4개의 Gaussian splats per 삼각형
중심과 각 모서리에 더해, 삼각형 내부의 다른 두 점에도 Gaussian splats를 추가로 배치합니다.

```python
if n_gaussians_per_surface_triangle == 4:
    self.surface_triangle_circle_radius = 1 / (4. * np.sqrt(3.))
    self.surface_triangle_bary_coords = torch.tensor(
        [[1/3, 1/3, 1/3],
        [2/3, 1/6, 1/6],
        [1/6, 2/3, 1/6],
        [1/6, 1/6, 2/3]],
        dtype=torch.float32,
        device=nerfmodel.device,
    )[..., None]  # n_gaussians_per_face, 3, 1
```

### 4. 6개의 Gaussian splats per 삼각형
더욱 촘촘하게 삼각형 내부에 Gaussian splats를 배치하여 삼각형을 더욱 정밀하게 표현합니다.

```python
if n_gaussians_per_surface_triangle == 6:
    self.surface_triangle_circle_radius = 1 / (4. + 2.*np.sqrt(3.))
    self.surface_triangle_bary_coords = torch.tensor(
        [[2/3, 1/6, 1/6],
        [1/6, 2/3, 1/6],
        [1/6, 1/6, 2/3],
        [1/6, 5/12, 5/12],
        [5/12, 1/6, 5/12],
        [5/12, 5/12, 1/6]],
        dtype=torch.float32,
        device=nerfmodel.device,
    )[..., None]
```

### 결론
이러한 방식을 통해 모델은 surface mesh의 디테일을 보다 잘 표현할 수 있으며, Gaussian splats의 수와 배치 방법을 조절함으로써 모델의 표현력을 유연하게 조정할 수 있습니다. 이 과정은 결과적으로 모델의 학습과 렌더링 품질에 큰 영향을 미칩니다.

-----

## `sugar_model.py`에서 surface_mesh에 binding된 모든 gaussian들의 densities 초기화 방법

```python
if self.binded_to_surface_mesh and (not learn_surface_mesh_opacity):
            all_densities = inverse_sigmoid(0.9999 * torch.ones((n_points, 1), dtype=torch.float, device=points.device))
            self.learn_opacities = False
        else:
            all_densities = inverse_sigmoid(0.1 * torch.ones((n_points, 1), dtype=torch.float, device=points.device))
        self.all_densities = nn.Parameter(all_densities, 
                                     requires_grad=self.learn_opacities).to(nerfmodel.device)
        self.return_one_densities = False
```

### 파라미터 설명 및 역할

- **self.binded_to_surface_mesh**: Gaussian들이 surface mesh에 바인딩되어 있는지를 나타냅니다.
- **learn_surface_mesh_opacity**: surface mesh에 존재하는 Gaussian들의 불투명도(opacity)를 학습할지 여부를 결정합니다. 불투명도를 학습하지 않으면, 불투명도 값이 고정된 채로 유지됩니다.

#### Gaussian들이 surface_mesh에 binding 되어있는지 여부와 surface_mesh_opacity 학습 여부에 따른 모든 gaussian들의 densities 초기화 방법

- CASE 1: `self.binded_to_surface_mesh`가 참이고 `learn_surface_mesh_opacity`가 거짓일 때
```python
if self.binded_to_surface_mesh and (not learn_surface_mesh_opacity):
    all_densities = inverse_sigmoid(0.9999 * torch.ones((n_points, 1), dtype=torch.float, device=points.device))
    self.learn_opacities = False
```
  - surface mesh에 바인딩되어 있고, surface mesh의 opacity를 학습하지 않도록 설정되어 있습니다. 이 경우, 모든 Gaussian들의 density값은 0.9999로 초기화됩니다. 역 시그모이드 함수가 적용되어 매우 큰 양의 값이 됩니다. 이는 밀도가 매우 높은 상태를 의미합니다. 즉, 모든 가우시안들의 densities 값을 매우 높은 값으로 설정하여 표면이 거의 완전하게 불투명하게 만듭니다.
    - 의미: Gaussian들이 surface mesh에 고정되어 있으며, Gaussian들의 opacities 값들이 학습할 필요가 없음을 나타냅니다. 

- CASE 2: 그 외의 경우 (Gaussian들이 surface mesh에 바인딩되지 않았거나, surface mesh에 존재하는 Gaussian들의 opacity를 학습하는 경우)
```python
else:
    all_densities = inverse_sigmoid(0.1 * torch.ones((n_points, 1), dtype=torch.float, device=points.device))
    self.learn_opacities = True
```
  - Gaussian들이 surface mesh에 바인딩되지 않았거나, surface mesh에 존재하는 Gaussian들의 불투명도(opacity)를 학습하도록 설정된 경우입니다. 이 경우, 모든 Gaussian들의 density값은 0.1로 초기화됩니다. 역 시그모이드 함수가 적용되어 음의 값이 됩니다. 이는 밀도가 낮은 상태를 의미합니다. 즉. 모든 Gaussian들의 densities 값을 낮은 값으로 설정하여 Gaussian들의 opacities를 학습하도록 합니다.
    - 의미: Gaussian들이 surface mesh에 고정되어 있지 않으며, Gaussian들의 opacieties 값들이 학습을 통해 조정될 필요가 있음을 나타냅니다. 

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3a4b4613-242f-47cc-a7a7-2ebe3f166608)







