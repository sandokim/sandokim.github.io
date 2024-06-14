---
title: "[3D CV 연구] 3DGS needs undistorted images following a pinhole model"
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
  - COLMAP
  - undistorted images
  - undistortion
excerpt: "3DGS needs undistorted images following a pinhole model"
use_math: true
classes: wide
---

## 3dgs나 SuGaR나 code는 pinhole camera model을 따르는 undistorted image들을 필요로 합니다.

이는 pinhole camera model을 따르는 undistorted image들은 image들을 COLMAP으로 돌려서 바로 얻을 수 있습니다.

https://github.com/Anttwo/SuGaR/issues/125

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1c964623-bb3b-41fb-a8a9-476c507a0322)



