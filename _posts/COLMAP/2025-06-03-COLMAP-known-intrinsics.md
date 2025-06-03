---
title: "[COLMAP] COLMAP with known intrinsics"
last_modified_at: 2024-07-04
categories:
  - 공부
tags:
  - COLMAP
  - Poses
  - Camera Extrinsics
  - Camera Intrinsics
  - 카메라 외부 파라미터
  - 카메라 내부 파라미터
excerpt: "COLMAP with known intrinsics"
use_math: true
classes: wide
comments: true
---

> [Using COLMAP to find the camera extrinsics in the case that the intrinsics are known #682](https://github.com/colmap/colmap/issues/682)
>
> [How to use pre-constructed DB for reconstruction from known poses #433](https://github.com/colmap/colmap/issues/433)
> 
> [Question: How to format cameras.txt for Reconstruct sparse/dense model from known camera poses #428](https://github.com/colmap/colmap/issues/428)

### COLMAP에서 카메라의 Intrinsics를 알 때, Extrinsics만 추정하는 법을 알아봅시다.
COLMAP은 기본적으로


