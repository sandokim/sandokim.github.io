---
title: "[3D CV] Mahalanobis Distance"
last_modified_at: 2024-05-23
categories:
  - 공부
tags:
  - Mahalanobis distance
  - 마할라노비스 거리
  - gaussian function
  - gaussian distribution
  - 공분산
  - covariance
excerpt: "마할라노비스 거리 정리"
use_math: true
classes: wide
---

### Mahalanobis distance (마할라노비스 거리)

본 게시물은 [공돌이의 수학정리노트 마할라노비스 거리](https://angeloyeo.github.io/2022/09/28/Mahalanobis_distance.html#google_vignette)를 참조하여 작성하였습니다.

유클리디안 거리로 보면 주황점들 간의 거리와 노란점들 간의 거리는 같지만,

데이터를 정규화하여 하여 거리를 측정하면 주황점들 간의 거리가 노란점들 간의 거리보다 가깝습니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f0e67aa5-a370-427b-8126-9db02f5565ea" alt="Image">
</p>

SuGaR: Surface-Aligned Gaussian Splatting for Efficient 3D Mesh Reconstruction and High-Quality Mesh Rendering에서 Mahalanobis Distance로 3D Gaussian로부터 3D point p까지의 거리를 측정하고, density function으로 변환 후, density function을 Signed Distance Function (SDF)로 변환하여 사용함을 확인해볼 수 있습니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/b6de35ec-d1c8-40cb-9064-1628b2262ab3" alt="Image">
</p>


