---
title: "[3D CV] Marching Cubes"
last_modified_at: 2024-05-30
categories:
  - 공부
tags:
  - Marching Cube
  - NeRF
  - 3D mesh
excerpt: "Marching Cubes 정리"
use_math: true
classes: wide
---

[[WIKIPEDIA Marching cubes](https://en.wikipedia.org/wiki/Marching_cubes)]

### Marching Cubes 알고리즘
Marching Cubes 알고리즘은 3차원 격자 데이터를 입력으로 받아 isosurface를 추출하는 방법입니다. Isosurface는 특정 임계값을 가지는 점들로 구성된 표면입니다. 이 알고리즘은 각 격자 셀을 큐브로 간주하고, 큐브의 각 꼭짓점이 임계값보다 높은지 낮은지를 기준으로 큐브를 삼각형으로 분할합니다.

#### 알고리즘의 단계
1) 큐브 분할: 3차원 격자 데이터를 작은 큐브(셀)들로 나눕니다. 각 큐브는 8개의 꼭짓점을 가지며, 각 꼭짓점은 샘플링된 데이터 값(스칼라 값)을 가집니다.

2) 꼭짓점 평가: 각 큐브의 꼭짓점이 임계값보다 큰지 작은지를 평가하여 이진 코드를 생성합니다. 이진 코드는 8비트로 표현되며, 각 비트는 꼭짓점의 상태(임계값보다 큰지 작은지)를 나타냅니다. 각 꼭짓점은 객체에 포함되어 있는지 여부에 따라 0 또는 1로 표시됩니다. 포함되어 있으면 1, 포함되지 않으면 0입니다.

3) 삼각형 패턴 매칭: 각 큐브의 8개의 꼭짓점이 두 가지 상태를 가질 수 있기 때문에, 큐브는 총 2^8(=256)개의 경우의 수를 가집니다. 이진 코드에 따라 미리 정의된 삼각형 패턴을 참조하여 큐브 내부에 isosurface를 표현하는 삼각형을 생성합니다. 이 256가지 경우의 수 각각에 대해, 어떻게 표면 다각형을 배치할지 미리 정의된 테이블이 존재합니다. 이 테이블은 각 경우의 수에 대해 해당하는 다각형을 구성하는 방법을 설명합니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/9980e895-822a-49f9-b01f-21ec86d21487" alt="Image">
</p>
   
4) 보간: 큐브의 변을 따라 선형 보간을 통해 꼭짓점의 정확한 위치를 계산하여 삼각형의 정점을 결정합니다.

5) 삼각형 연결: 각 큐브에서 생성된 삼각형들을 연결하여 연속적인 표면을 형성합니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/a242c159-7baf-4122-8a7c-e4a32a04c8b8" alt="Image">
</p>

#### 활용 방안
- SDF (Signed Distance Fields): SDF는 점이 표면으로부터의 거리를 나타내는 필드입니다. Marching Cubes 알고리즘은 SDF 데이터에서 isosurface를 추출하여 3D 모델을 생성할 수 있습니다. 이는 게임 엔진에서 물체의 충돌 감지, 3D 모델링 소프트웨어에서 부드러운 표면 생성 등에 활용됩니다.

- NeRF (Neural Radiance Fields): NeRF는 신경망을 이용해 3D 장면의 광선 필드를 학습하는 방법입니다. 학습된 NeRF 모델에서 샘플링된 3차원 데이터로부터 isosurface를 추출하여 장면의 3D 구조를 시각화할 수 있습니다. Marching Cubes 알고리즘은 이러한 데이터로부터 메쉬를 생성하는 데 사용됩니다.

- 메디컬 이미징 (CT, MRI): Marching Cubes 알고리즘은 CT, MRI 데이터에서 인체 장기나 구조물의 3D 모델을 생성하는 데 널리 사용됩니다. 예를 들어, 의사는 CT 스캔 데이터를 통해 환자의 장기를 3D로 시각화하여 수술 계획을 세우거나, 병변의 위치와 크기를 정확히 파악할 수 있습니다.

#### Marching Cube 장점
- 효율성: 알고리즘이 비교적 간단하고, 병렬 처리에 적합합니다.
- 일관성: isosurface가 연속적이고 매끄러운 표면을 형성하도록 보장합니다.

#### Marching Cube 단점
- 구멍 문제: 특정 경우에 잘못된 삼각형 패턴이 선택되어 구멍이 생길 수 있습니다.
- 정확도: 보간 과정에서 정확도가 떨어질 수 있습니다.
