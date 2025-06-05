---
title: "[COLMAP] SolvePnP와 Stereo Calibration의 차이"
last_modified_at: 2025-06-05
categories:
  - 공부
tags:
  - Poses
  - Camera Extrinsics
  - SolvePnP
  - Stereo Calibration
  - multi camera
excerpt: "Multi Camera 세팅에서 extrinsics를 구할때, SolvePnp와 Stereo Calibration으로 풀 때 차이를 알아봅시다."
use_math: true
classes: wide
comments: true
---

### 📸 여러 대의 카메라로 체커보드 촬영 후 Extrinsics 구하기
복수의 카메라를 사용해 외부 파라미터(extrinsics)를 추정할 때, 특히 체커보드를 동시에 촬영한 경우 어떻게 처리할 수 있을지 정리해보겠습니다.

### 🧩 문제 설정
4대의 카메라가 동시에 체커보드를 촬영했다고 가정합니다. 각 카메라는 미리 자체적으로 내부 파라미터(intrinsics)를 구한 상태입니다. 이때 extrinsics를 어떻게 구할 수 있을까요?

<p align="center">
  <img src="https://github.com/user-attachments/assets/f85ba118-8911-437d-a0c7-7d213b0c3019" />
  <br/>
  <em>그림 1. 4대의 카메라로 동시에 체커보드를 촬영된 이미지</em>
</p>

### ✅ 기본 아이디어
각 카메라에서 체커보드를 촬영한 이미지를 이용해, solvePnP를 통해 각 카메라 기준 체커보드의 pose를 구합니다. 즉, 체커보드가 각 카메라 좌표계에서 어디에 있는지를 계산합니다.

<p align="center">
  <img src="https://github.com/user-attachments/assets/78d515fc-4b5a-40ac-ae74-122814a4c9ea" />
  <br/>
  <em>그림 2. SolvePnP로 각 카메라 프레임에서 체커보드의 위치가 계산됨</em>
</p>

여기서 한 발 더 나아가면, 역변환을 통해 체커보드 기준에서의 카메라 pose를 얻을 수 있습니다. 예를 들어, 카메라 1 기준에서 체커보드의 pose가 있다면, 그 역행렬을 취하면 체커보드 기준에서 카메라 1의 pose가 됩니다.

이렇게 구한 각 카메라의 pose를 체커보드 좌표계 상에 위치시키면, 서로 다른 카메라들 간의 상대 pose 역시 계산할 수 있습니다. 예를 들어:

```python
T_cam1_to_cam2 = T_board_to_cam2 * inv(T_board_to_cam1)
```

### 🔄 Stereocalibration과의 비교
그렇다면 OpenCV의 stereoCalibrate를 사용하는 방식과는 어떤 차이가 있을까요?

stereocalibration은 각 카메라에서 동시에 촬영된 **여러 장의 체커보드 이미지 쌍을 사용**합니다.

이 과정에서는 **최적화가 수행**되어, 두 카메라 간의 상대 pose를 보다 정밀하게 추정합니다.

즉, 반복적으로 촬영하고 최적화를 수행함으로써 오차를 줄이는 방식입니다.

반면, solvePnP를 사용한 방식은 한 번의 촬영만으로도 각 카메라의 pose를 얻을 수 있습니다. 따라서 간편하고 빠르지만, 정확도 면에서는 최적화 과정을 거치는 stereocalibration에 비해 상대적으로 떨어질 수 있습니다.

