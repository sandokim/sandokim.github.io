---
title: "[3D CV] Monocular Depth Estimation"
last_modified_at: 2024-03-27
categories:
  - 공부
tags:
  - Multiple View Geometry
  - Epipolar Geometry
  - 3D CV
  - depth
  - depth estimation
  - monocular depth estimation
  - scene representation
  - scene understanding
excerpt: "Monocular Depth Estimation"
use_math: true
classes: wide
comments: true
---

# $\bf{d} \in \mathbb{R}^{H \times W}$

- depth는 $H \times W$로 표현됩니다. 실제 코드에서도 `(H,W)` 2개의 채널을 가집니다.
- depth value는 카메라로부터 3D point까지의 거리입니다. (일반적으로 -1 ~ 1의 값은 아님)

### 대부분의 depth sensor도 near plane, far plane개념이 있습니다.
![image](https://github.com/user-attachments/assets/2366f3a5-fc28-4071-a32b-52fe54c4162e)
- LiDAR sensor도 minimum distance, max distance가 있습니다.
- near plane이 0.1 cm이면 0.1cm이내에 있는 점들에 대해서는 real value를 주지 않습니다.


### depth sensor로 얻는 ground truth는 Light Sensor로 빔을 쏘고 반사되어 나오는 것을 IR (Infrared, 적외선) sensor로 depth를 얻습니다.
- depth sensor (realsense depth camera, LiDAR, etc.)는 항상 scene에 존재하는 material properties에 의하여 reflect되는 빛이 영향을 받습니다.
- 이때 반사되는 빛이 IR sensor에 도달할 때, 그 값이 너무 크게 반사되면 IR sensor가 capture할 수 있는 value range를 넘어가서 하얗게 나옵니다.
![image](https://github.com/user-attachments/assets/9cbe65ad-940f-4b18-a415-1b78a4fb8116)
- 예시로, 아래 Ground Truth에서 relect가 심한 material에 대해선 하얗게 표시된 것을 볼 수 있습니다.
![image](https://github.com/user-attachments/assets/75c1fbc8-b8d7-4266-8482-3402c4fb260b)

### 사람이 한쪽 눈을 잃어서 다른 한쪽 눈으로만 물체를 보면, 그 물체에 대한 깊이를 추정하는데 굉장히 어렵다고 합니다.

사람처럼 scene에 대한 이해가 있으면, 한쪽 눈만으로도 깊이를 어느정도 추정할 수는 있긴 하지만 어렵다고 합니다.

즉, 하나의 이미지만 사용하여 depth를 추정하는 monocular depth estimation을 수행할 때, scene에 대한 이해가 있는 모델이라도 view가 하나만 존재할 때는 depth를 추정하는 것이 어렵다는 의미입니다.

### monocular depth estimation is a dense, structured regression task

- monocular depth estimation은 **모든 pixel에 대해 depth value를 prediction해야하므로 dense한 task**입니다.
- -1 ~ 1 사이의 값 중에 맞는 depth value로 regress해야하므로 regression task입니다.


### Reference
[monocular depth estimation](https://www.youtube.com/live/WoiI_Pn9yHw?si=TWAW4JpuLppNH5I9)
