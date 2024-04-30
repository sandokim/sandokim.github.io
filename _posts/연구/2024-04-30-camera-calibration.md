
---
title: "[3D Vision 연구] camera calibration"
last_modified_at: 2024-04-30
categories:
  - 연구
tags:
  - 연구
  - 3D Vision
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

1) RealSense Viewer에서 제공하는 RealSense D435 Depth camera calibration data

2) OpenCV에서 camera calibration으로 구한 RealSense RGB Camera calibration data

> FOV(HFOV, VFOV)와 focal length와 시야각
1) FOV가 커지면 더 넓은 시야각 --> focal length는 짧아지고, 더 넓은 범위를 한 번에 포착 --> Depth Camera
  
2) FOV가 작아지면 더 좁은 시야각 --> focal length는 길어지고, 이미지의 세부사항을 더 선명하게 포착 --> RGB Camera

### RealSense D435의 RGB 카메라의 HFOV, VFOV를 구해봅시다.

먼저 RealSense Document에 따르면 focal length는 pixels 단위로 구하게 됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a99d3673-be87-45ea-b882-818f3705fd89)

[[resolution(pixels)과 HFOV(degree)로 focal length(pixels)를 구하는 공식](https://dev.intelrealsense.com/docs/white-paper-subpixel-linearity-improvement-for-intel-realsense-depth-cameras)]

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8f87b2c1-ee9c-41b4-a561-ca59d27062ff)

[[RealSense D435 Tech Specs](https://www.intelrealsense.com/depth-camera-d435/)]
