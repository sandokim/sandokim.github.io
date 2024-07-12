---
title: "[3D CV] 3DGS, Dynamic 3DGS에서 Rotation 역변환을 곱하는 이유"
last_modified_at: 2024-07-12
categories:
  - 공부
tags:
  - Multiple View Geometry
  - world coordinate system
  - world space
  - 3dgs
  - 3d gaussian splatting
  - 4dgs
  - 4d gaussian splatting
  - dynamic 3d gaussian splatting
  - dynamic 3dgs
excerpt: "World Space Rotation undo and do"
use_math: true
classes: wide
comments: true
---

## 3D Gaussian Splatting

![image](https://github.com/user-attachments/assets/c5e7e21a-0961-4761-9342-f32b9b4f73da)

## Dynamic 3D Gaussians

![image](https://github.com/user-attachments/assets/803557f0-8e88-41ad-938c-69e0f1db07bb)

![image](https://github.com/user-attachments/assets/473dafb1-b678-45e5-8288-7285b504c5a3)

![image](https://github.com/user-attachments/assets/48479f55-acff-46f8-ba4f-d2413184a95e)

1. **Rotation and Difference Calculation**:
   - world space에서 회전의 차이를 구하려면:
     - world space에서 회전을 원래대로 되돌립니다.
     - 새로운 회전을 적용합니다.
   - Rotation과 다르게 vector는 단순히 차이를 구하면 됩니다.

2. **Distance Between Gaussians**:
   - t-1 시점에서 i-th Gaussian과 j-th Gaussian 간의 거리는 다음과 같이 계산합니다:
     - t 시점의 i-th Gaussian의 inverse rotation을 적용하여 회전 성분을 제거하고, 이를 identity rotation으로 만듭니다.
     - 그런 다음, t-1 시점의 i-th Gaussian의 rotation을 적용합니다.
     - 이러한 변환을 통해 t 시점의 i-th Gaussian과 j-th Gaussian 간의 거리를 t-1 시점의 공간에서 측정할 수 있습니다.

3. **Prediction and Learning**:
   - t 시점의 속성을 예측할 때:
     - Gaussians을 t 시점에서 t-1 시점으로 rigid body transformation(즉, 회전)한 값을 사용합니다.
     - 이는 t-1 시점의 최적화된 scene을 알고 있기 때문에, t 시점의 Gaussian 속성이 t-1 시점으로 rigid body transformation 되었을 때 일치해야 한다는 학습이 가능합니다.
     - 변환을 통해 t 시점의 Gaussian 속성이 t-1 시점에서 학습된 속성과 일치하도록 합니다.

4. **$L_{rigid}$ Loss**:
   - 손실은 t 시점에서 rigid body transformation을 적용한 후 i-th Gaussian과 j-th Gaussian 간의 거리 벡터가 t-1 시점에서의 거리 벡터와 일치해야 한다는 원칙에 기반합니다.
   - 이는 연속된 시점 간의 거리 변환 일관성을 보장합니다.

### 추가 세부 사항

1. **Top K-Nearest Gaussians at Time 0**:
   - 0 시점에서 i-th Gaussian과 가장 가까운 20개의 Gaussians 간의 거리를 가중치로 사용합니다.
   - 이 가중치는 i-th Gaussian이 가장 가까운 20개의 이웃과 일정한 거리를 유지하도록 강제합니다.
   - 거리가 멀어지면 가중치가 증가하여 손실 값이 커집니다.
   - 손실을 최소화하려면 i-th Gaussian과 가장 가까운 20개의 이웃 간의 거리를 유지하도록 모델이 학습됩니다.

2. **Quaternion Rotation and Loss**:
   - i-th Gaussian과 가장 가까운 20개의 이웃 Gaussians에 대해:
     - t-1 시점의 inverse quaternion rotation을 적용하여 회전을 identity로 만듭니다.
     - t 시점의 quaternion으로 회전시킵니다.
     - 이 손실은 t-1 시점에서 t 시점으로의 i-th Gaussian과 가장 가까운 20개의 Gaussians 각각의 rotation 차이가 없도록 보장합니다.
   - i-th Gaussian과 가장 가까운 20개의 이웃 Gaussians이 동일한 회전 방향을 가질 때 손실 값은 0이 되어 로컬 회전 일관성을 보장합니다.

### 초록색 실선과 점선에 대한 설명

![image](https://github.com/user-attachments/assets/688babb6-d762-4150-bce9-454385df2fe6)

- **초록색 실선**: t 시점에서 i-th Gaussian과 j-th Gaussian 간의 거리를 나타냅니다.
- **초록색 점선**: t-1 시점에서 i-th Gaussian과 j-th Gaussian 간의 거리를 나타냅니다.

$L_{rigid}$ Loss에서, t 시점에서 초록색 실선인 i-th Gaussian과 j-th Gaussian의 거리 벡터는 rigid body transformation(즉, 회전)을 통해 t-1 시점의 초록색 점선인 i-th Gaussian과 j-th Gaussian의 거리 벡터로 변환됩니다.

### 요약

- 회전 차이를 측정하기 위해 world space에서 회전을 원래대로 되돌린 후 새로운 회전을 적용합니다.
- 벡터는 단순히 차이를 구하면 됩니다.
- 다른 시점에서 Gaussians 간의 거리는 나중 시점의 공간을 이전 시점과 일치시키는 변환을 통해 측정합니다.
- t 시점의 속성은 t-1 시점의 최적화된 scene과 일치하도록 변환하여 예측합니다.
- $L_{rigid}$ Loss는 연속된 시점 간의 Gaussians 거리 변환이 일관되도록 보장합니다.
- 초기 거리와 로컬 회전 일관성을 유지하여 모델의 안정성을 유지합니다.








