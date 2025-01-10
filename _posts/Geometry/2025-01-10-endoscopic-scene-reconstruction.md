---
title: "[Geometry] Endoscopic scene reconstruction"
last_modified_at: 2025-01-10
categories:
  - Geometry
tags:
  - Geometry
  - RAFT
  - optical flow
  - endoscopic scene reconstruction
  - surgical scene reconstruction
  - deform3dgs
  - stereo depth
  - StereoMIS
  - EndoNeRF
excerpt: "optical flow 기반 stereo depth estimation model인 RAFT로 surgical scene에 대한 stereo depth를 얻습니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [Deform3DGS: Flexible Deformation for Fast Surgical Scene Reconstruction with Gaussian Splatting](https://arxiv.org/pdf/2405.17835)

> [RAFT: Recurrent All-Pairs Field Transforms for Optical Flow](https://arxiv.org/pdf/2003.12039)

> [A Survey on 3D Gaussian Splatting](https://arxiv.org/pdf/2401.03890)

### Endoscopic scene reconstruction의 개요는 [A Survey on 3D Gaussian Splatting](https://arxiv.org/pdf/2401.03890)를 참조하기 바랍니다.

-----

### [Deform3DGS official github](https://github.com/jinlab-imvr/Deform3DGS?tab=readme-ov-file)

StereoMIS dataset을 사용할 때 EndoNeRF와 똑같은 depth, masks, images, intrinsics, extrinsics 파라미터를 얻습니다.

stereo depth는 specular & textureless surface에서 robust한 optical flow 기반 stereo depth estimation model인 RAFT를 사용하여 얻습니다.

EndoNeRF와 마찬가지로 StereoMIS 데이터도 fixed-view settings로 합니다. (single-view point video reconstructios)

### Datasets 
To use the StereoMIS dataset, please follow this github repo to preprocess the dataset. 

After that, run the provided script stereomis2endonerf.py to extract clips from the StereoMIS dataset and organize the depth, masks, images, intrinsic and extrinsic parameters in the same format as EndoNeRF. 

In our implementation, **we used RAFT to estimate the stereo depth for StereoMIS clips.** 

**Following EndoNeRF dataset, this script only supports fixed-view settings.**


