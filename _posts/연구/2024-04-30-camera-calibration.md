---
title: "[3D CV 연구] Camera Calibration"
last_modified_at: 2024-05-27
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - camera calibration
  - pixels
excerpt: "camera calibration"
use_math: true
classes: wide
---

> RealSense D435 Camera와 OpenCV로 구한 Camera Calibration에서 focal length는 pixel 단위로 저장 됩니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/945fdf82-1279-403d-a315-0867dbe72ef8" alt="Image">
</p>

[좌측] RealSense Viewer에서 제공하는 RealSense D435 Depth camera calibration data

[우측] OpenCV에서 camera calibration으로 구한 RealSense RGB Camera calibration data

### FOV(HFOV, VFOV)와 focal length와 시야각

1) FOV가 커지면 더 넓은 시야각 --> focal length는 짧아지고, 더 넓은 범위를 한 번에 포착 --> Depth Camera
  
2) FOV가 작아지면 더 좁은 시야각 --> focal length는 길어지고, 이미지의 세부사항을 더 선명하게 포착 --> RGB Camera

### RealSense D435의 RGB 카메라의 HFOV, VFOV를 구해봅시다.

먼저 RealSense Document에 따르면 focal length는 pixels 단위로 구하게 됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a99d3673-be87-45ea-b882-818f3705fd89)

[[resolution(pixels)과 HFOV(degree)로 focal length(pixels)를 구하는 공식](https://dev.intelrealsense.com/docs/white-paper-subpixel-linearity-improvement-for-intel-realsense-depth-cameras)]

다음으로 RealSense D435의 RGB camera의 HFOV와 VFOV의 TECH SPECS를 참고하여, 이론적인 focal length(pixels)를 구해보고, OpenCV의 cv2.calibrateCamera()로 구한 focal length(pixels)인 f_x, f_y와 비교해봅니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a196cf2b-7e59-4042-9905-ddf65f7464b3)

OpenCV로 구한 code snippet의 f_x, f_y와 TECH SPCES를 참고하여 이론적으로 구한 focal length x, focal length y가 비슷함을 알 수 있습니다.

[[RealSense D435 Tech Specs](https://www.intelrealsense.com/depth-camera-d435/)]


### Custom Dataset

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b5fd04f1-d60b-4ec1-9434-0afc8b5d807e)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/11eb8b26-af30-48f2-a579-610fe27a1725)

