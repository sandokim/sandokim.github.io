---
title: "[3D CV 연구] 3DGS SuGaR joint refinement of mesh and gaussians"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - flat gaussian
  - gaussians
  - joint refinement
excerpt: "3DGS SuGaR joint refinement of mesh and gaussians"
use_math: true
classes: wide
comments: true
---

## 최적화 과정에서 새로운 Gaussian을 메시에 바인딩한 후 최적화 과정에 대한 질문 요약

### 질문 1: 새로운 Gaussian의 초기 파라미터(SH, opacity, scale, rotation) 값은 어떻게 선택되나요? 새로운 Gaussian은 이전 최적화 단계의 Gaussian과 연결되나요?

새로운 Gaussian은 이전 Gaussian과 직접적으로 연결되지 않습니다. 대신, 추출된 메시를 사용하여 초기화됩니다. 각 파라미터의 초기화 방식은 다음과 같습니다:

- **위치/3D 중심**: 모든 메시의 삼각형에 여러 개의 Gaussian을 생성합니다. ***삼각형당 Gaussian의 수는 추출된 메시의 해상도에 따라 달라집니다.*** 예를 들어, low-poly mesh(예: 200k vertices)에서는 더 높은 값을 선택하고, high-poly mesh(예: 1M vertices)에서는 더 낮은 값을 선택합니다. **일반적으로 모든 해상도에서 1M에서 2M 개의 Gaussian을 목표로 합니다.**
- **Opacity(불투명도)**: 모든 Gaussian은 초기 불투명도는 3DGS original paper와 같 0.1로 초기화됩니다. 메시는 장면의 불투명한 표면을 나타내기 때문에 더 높은 불투명도를 선택하는 것이 논리적일 수 있습니다. 그러나 Gaussian을 낮은 불투명도로 초기화하면 최적화 과정에서 메시 표면의 세부 사항을 더 잘 조각하고 텍스처링 세부 사항을 캡처하며 메시의 해상도보다 더 섬세한 매우 얇은 기하학적 구조를 재구성하는 데 도움이 됩니다. 예를 들어, 논문의 그림 8에서 자전거의 바퀴살과 같은 매우 얇은 구조를 재구성할 수 있습니다.
- **Scale(크기)**: 모든 Gaussian은 두 개의 학습 가능한 크기 조정 요소만 가지고 있습니다. 세 번째 요소는 고정되어 있으며 매우 작습니다. 이는 Gaussian을 평평하게 하고 메시의 삼각형에 정렬되도록 하기 위함입니다. 최적화 시작 시 두 개의 학습 가능한 값은 동일하며, 초기 값은 해당 삼각형의 크기에 따라 달라집니다.
- **Rotation(회전)**: Gaussian은 삼각형의 평면에 정렬되도록 2D 회전만 최적화합니다. 초기화 시 회전은 0입니다.
- **SH(구형 조화 함수)**: 구형 조화 함수는 Poisson 재구성을 통해 얻어진 vertex colors output을 사용하여 초기화되며, 무게중심(barycentric) 좌표를 사용하여 interpolation됩니다.

### 질문 2: 최적화 과정에서 Gaussian이 항상 메시 삼각형에 정렬되고 표면 위에서 자유롭게 이동할 수 있는 경우, 메시의 삼각형 위치도 이동하거나 정제되나요? 아니면 표면을 매끄럽게 만드는 과정인가요?
- ***Gaussian의 위치가 mesh의 정점 좌표의 미분 가능한 함수로 계산되기 때문에 mesh triangles도 이동하고 정제됩니다.*** 이는 Gaussian Splatting 렌더링을 사용하여 메시 표면을 정제하는 과정입니다. 실제로 이 단계는 메시 표면을 매끄럽게 하는 데 도움을 줍니다. (특히 low-poly meshes에서 매끄럽게 해줍니다.)

## 주요 키포인트
- **Gaussian 초기화**: **새로운 Gaussian은 이전 단계의 Gaussian과 직접적으로 연결되지 않고, 추출된 메시를 기반으로 초기화**됩니다.
- **Gaussian의 파라미터**: 위치는 모든 메시 삼각형에 대해 여러 Gaussian을 생성하며, 불투명도는 0.1로 설정되고, 크기는 삼각형 크기에 따라 초기화됩니다. 회전은 삼각형 평면에 정렬되며, 구형 조화 함수는 무게중심 좌표를 사용해 초기화됩니다.
- **Refinement of mesh**: **Gaussian의 위치가 mesh의 정점 좌표의 함수로 계산**되므로, mesh triangles도 이동 및 정제됩니다. 이는 메시 표면을 매끄럽게 하는 데 도움을 줍니다.
- **해상도와 Gaussian 수**: low-poly mesh(e.g. 200k vertices)에서는 삼각형당 더 많은 Gaussian을 사용하고, high-poly mesh(e.g. 1M vertices)에서는 더 적은 Gaussian을 사용하여 전체 Gaussian 수를 일정하게 유지합니다. 저해상도에서 더 높은 값을 사용하는 이유는 세부 사항을 더 잘 포착하기 위해서입니다.

https://github.com/Anttwo/SuGaR/issues/6

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/796a38f8-6c5e-4555-a175-c03afb6736f4)


