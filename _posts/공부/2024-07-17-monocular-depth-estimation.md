---
title: "[3D CV] Monocular Depth Estimation"
last_modified_at: 2024-07-18
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


### depth sensor로 얻는 ground truth는 Light Sensor로 빔을 쏘고 반사되어 나오는 것을 IR sensor (Infrared sensor, 적외선 센서)로 capture하여 depth를 얻습니다.
- depth sensor (realsense depth camera, LiDAR, etc.)는 항상 scene에 존재하는 material properties에 의하여 반사되는 빛의 영향을 받습니다.
- 이때 반사되는 빛이 IR sensor에 도달할 때, 그 값이 너무 크게 반사되면 IR sensor가 capture할 수 있는 value range를 넘어가서 하얗게 나옵니다.
![image](https://github.com/user-attachments/assets/9cbe65ad-940f-4b18-a415-1b78a4fb8116)
- 예시로, 아래 Ground Truth에서 relect가 심한 즉, shiny한 material에 대해선 하얗게 표시된 것을 볼 수 있습니다.
![image](https://github.com/user-attachments/assets/75c1fbc8-b8d7-4266-8482-3402c4fb260b)
- realsense depth sensor같은걸로 얻은 Ground Truth normal도 굉장히 퀄리티가 떨어집니다.
![image](https://github.com/user-attachments/assets/c59e838e-6f79-4728-8d07-75255d0eb653)

### true depth needs scale

- fake depth로 표현하고, depth를 rgb color로 표현하면 아래와 같습니다.
- 문제는 카메라부터 강아지까지 거리와 카메라부터 건물까지 거리는 단위부터가 다른데, fake depth에서는 -1 ~ 1로 normalize하여 표현하므로, true depth를 알 수가 없습니다.
![image](https://github.com/user-attachments/assets/314cd810-3ca9-48bc-b73a-74e68806e648)

### 'scale ambiguity problem'(스케일 모호성 문제)은 깊이 추정에서 자주 발생하는 문제입니다.
- 이 문제는 장면에서 개체의 크기나 거리를 추정할 때 실제 물리적 크기나 거리와 비교할 수 있는 기준이 없을 때 발생합니다.
- 다시 말해, 깊이 추정 모델은 물체 간의 상대적인 거리는 잘 알 수 있지만, 이 거리들이 실제 세계에서 얼마나 큰지, 즉 절대적인 스케일을 알 수 없다는 것입니다.
- ZoeDepth와 DepthAnything 같은 모델들은 장면의 각 픽셀마다 깊이를 추정하는 데 집중하고 있습니다. 하지만 이 깊이 정보는 상대적일 뿐, 절대적인 거리를 제공하지 않습니다.
- 그래서 이러한 깊이 정보를 실제 세계의 스케일과 맞추기 위해 비교 기준이 필요합니다. 여기서 'sparse SfM points'와 비교하는 방법이 사용됩니다.
- 이는 SfM(Structure from Motion) 알고리즘을 통해 얻은 장면의 일부 포인트들의 절대적인 깊이 정보를 활용하여 전체 깊이 지도의 스케일을 조정하는 방식입니다.
- SfM 포인트를 카메라 뷰에 투영하여 얻은 sparse depth map의 스케일과 일치하도록 정렬합니다.
- 이를 위해, 각 이미지에 대해 scale parameter(a)와 shift parameter(b)를 닫힌 형태의 선형 회귀 솔루션을 사용하여 해결합니다
![image](https://github.com/user-attachments/assets/88903602-bda8-4b8f-9a97-b08869f411bd)
- [DN-Splatter: Depth and Normal Priors for Gaussian Splatting and Meshing](https://arxiv.org/abs/2403.17822)


### Depth Evaluation Metrics

![image](https://github.com/user-attachments/assets/8d7f9350-b8c9-4752-8519-efdafa7cb36f)

![image](https://github.com/user-attachments/assets/407d6802-f723-4e73-806b-1f087d5940f0)


### 사람이 한쪽 눈을 잃어서 다른 한쪽 눈으로만 물체를 보면, 그 물체에 대한 깊이를 추정하는데 굉장히 어렵다고 합니다.

사람처럼 scene에 대한 이해가 있으면, 한쪽 눈만으로도 깊이를 어느정도 추정할 수는 있긴 하지만 어렵다고 합니다.

즉, 하나의 이미지만 사용하여 depth를 추정하는 monocular depth estimation을 수행할 때, scene에 대한 이해가 있는 모델이라도 view가 하나만 존재할 때는 depth를 추정하는 것이 어렵다는 의미입니다.

### monocular depth estimation is a dense, structured regression task

- monocular depth estimation은 **모든 pixel에 대해 depth value를 prediction해야하므로 dense한 task**입니다.
- -1 ~ 1 사이의 값 중에 맞는 depth value로 regress해야하므로 regression task입니다.

### Ground Truth Depth는 depth sensor로 촬영한 것을 사용합니다. (i.e. iPhone으로 capture한 depth를 gt로 사용함)

![image](https://github.com/user-attachments/assets/d76f4ddb-6371-46b0-8aac-15f39b298e7a)

![image](https://github.com/user-attachments/assets/7ac53c4e-3ad2-4e35-8bd5-c619a2c93b77)


# Sensor depth

- LiDAR 또는 센서로 측정한 깊이가 포함된 데이터셋의 depth map에 깊이 정규화를 직접 적용합니다..
- 일반적인 상업용 깊이 센서는 물체 경계에 가장자리가 매끄럽지 않으며 매끄러운 표면에서 부정확한 값을 제공하는 경우가 많습니다.
- 따라서 저자들은 RGB 이미지를 기반으로 적응형 깊이 정규화를 위한 gradient-aware depth loss를 제안하였습니다.
- 물체의 가장자리와 같이 이미지 gradient가 큰 영역에서는 depth loss가 낮아지며, 매끄러운 영역에서 정규화가 더 강화됩니다.
  
$$
L_\hat{D} = g_{rgb} \frac{1}{|\hat{D}|} \sum \log \left( 1 + \| \hat{D} - D \|_1 \right)
$$

여기서

$$
g_{rgb} = \exp \left( - \nabla I \right)
$$

  
### Reference
- [[논문리뷰] DN-Splatter: Depth and Normal Priors for Gaussian Splatting and Meshing](https://kimjy99.github.io/%EB%85%BC%EB%AC%B8%EB%A6%AC%EB%B7%B0/dn-splatter/)
- [DN-Splatter: Depth and Normal Priors for Gaussian Splatting and Meshing](https://arxiv.org/abs/2403.17822)
- [monocular depth estimation](https://www.youtube.com/live/WoiI_Pn9yHw?si=TWAW4JpuLppNH5I9)
