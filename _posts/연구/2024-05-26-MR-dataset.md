---
title: "[MR 연구] fastMRI & skm-tea Real & Simulation 데이터셋 정리"
last_modified_at: 2024-05-26
categories:
  - 연구
tags:
  - 연구
  - MR Reconstruction
  - fastMRI
  - skm-tea
  - MR dataset
  - 데이터셋
excerpt: "fastMRI & skm-tea Real & Simulation 데이터셋 정리"
use_math: true
classes: wide
---

> [[fastMRI Dataset](https://fastmri.med.nyu.edu/)] [[fastMRI Github](https://github.com/facebookresearch/fastMRI?tab=readme-ov-file)] [[skm-tea Dataset](https://stanfordaimi.azurewebsites.net/datasets/4aaeafb9-c6e6-4e3c-9188-3aaaf0e0a9e7)] [[skm-tea Github](https://github.com/StanfordMIMI/skm-tea)]

## fastMRI knee unfolded dataset
<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/4c4c30fd-2699-47c4-a74b-3326cf83bd36" alt="Image">
</p>

## skm-tea dataset
<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/9ab4e0ba-1d13-4a94-befa-c7915972d6cb" alt="Image">
</p>


### Multi-coil k-space가 8개 존재할 때, 모든 patient에 대한 k-space의 shape을 원하는 사이즈로 통일하는 법

Real data에서 Coilmap을 estimation안하고 raw k-space를 각각 ifft하고 image domain에서 crop 혹은 zero-padding한 뒤, 다시 fft로 raw k-space로 변환하면 real-data라고 볼 수 있습니다.

1) k-space 8개를 ifft
2) image domain으로 변환된 8개의 image에 대해 crop 혹은 zero padding하여 원하는 size로 통일 후, fft
3) 이를 통해 우리가 원하는 사이즈로 조정된 k-space 8개를 얻을 수 있습니다.

### FastMRI dataset에 대해서 multi-coil k-space 8개를 가지는 모든 patient에 대한 k-space shape을 640, 372로 통일해봅시다.

1) H는 640으로 모두 동일하니 그대로 두고
2) Width는 320, 356, 368, 372 사이즈를 갖는 patient에 대해서 372로 통일하도록 방향을 잡고
3) 먼저 multi-coil k-space 8개를 ifft로 image domain으로 변환한 뒤
4) image domain에서 모든 8개의 image에 대해 zero padding으로 (H,W) = (640,372) 사이즈로 맞춰줍니다
5) fft를 하여 (H,W) = (640,372)로 사이즈가 조정된 multi-coil k-space 8개를 얻습니다.

