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

## Meta 정보를 제외하고 kspace array만 저장하였을 때 h5, npy file format 용량 차이는 거의 없습니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/3917d328-bd68-4920-88b8-256d5e11fdd2" alt="Image">
</p>

## fastMRI knee unfolded dataset (Simulation Data)
<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/4c4c30fd-2699-47c4-a74b-3326cf83bd36" alt="Image">
</p>

## skm-tea dataset (Simulation Data)
<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/9ab4e0ba-1d13-4a94-befa-c7915972d6cb" alt="Image">
</p>

## fastMRI knee unfolded dataset (Real Data)

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/64068b39-49c7-4819-b4fb-b9cd051c1df7" alt="Image">
</p>

## skm-tea dataset (Real Data)

TBD..

### Multi-coil k-space가 8개 존재할 때, 모든 patient에 대한 k-space의 shape을 원하는 사이즈로 통일하는 법

Real data에서 Coilmap을 estimation안하고 raw k-space를 각각 ifft하고 image domain에서 crop 혹은 zero-padding한 뒤, 다시 fft로 raw k-space로 변환하면 Real k-space data라고 볼 수 있습니다.

1) k-space 8개를 ifft
   
2) image domain으로 변환된 8개의 image에 대해 crop 혹은 zero padding하여 원하는 size로 통일 후, fft

3) 이를 통해 우리가 원하는 사이즈로 조정된 k-space 8개를 얻을 수 있습니다.

### FastMRI dataset에 대해서 multi-coil k-space 15개를 가지는 모든 patient에 대한 k-space shape을 640, 372로 통일해봅시다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/991e712d-e89b-4fd0-b21b-b449d0af7e1a)

1) H는 640으로 모두 동일하니 그대로 두고
   
2) Width는 320, 356, 368, 372 사이즈를 갖는 patient에 대해서 372로 통일하도록 방향을 잡고
   
3) 먼저 multi-coil k-space 15개를 ifft로 image domain으로 변환한 뒤
   
4) image domain에서 모든 15개의 image에 대해 zero padding으로 (H,W) = (640,372) 사이즈로 맞춰줍니다
   
5) fft를 하여 (H,W) = (640,372)로 사이즈가 조정된 multi-coil k-space 15개를 얻습니다.

**주의할 점**

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d376253e-2095-4fb7-ad59-93241dd26ff6)

### MR Pulse Sequence를 얻는 과정 (k-space 취득 과정)

**Spiral Pattern**
- 3D에서 Spiral 패턴의 k-space 데이터를 얻을 수 있습니다.
- Spiral 패턴은 흔하지 않고, 기계의 안정성 문제로 인해 잘 사용되지 않습니다.
- 빠르게 움직이고 모션이 강한 심장 이미지에서는 Spiral 패턴을 사용하기도 합니다.
- 즉, spiral pattern을 무릎이나 뇌에 대해서 Spiral pattern으로 k-space를 얻는 경우는 거의 없습니다.
- Spiral Pattern을 얻는 평면에서 Poisson 샘플링 기법을 사용하여 noisy한 패턴을 만들 수 있습니다.
  
**Cartesian Pattern**
- 무릎이나 뇌의 이미지는 일반적으로 Cartesian 패턴을 사용하여 얻습니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/3a6f27cb-0906-4d85-9b62-bc979946dffe" alt="Image">
</p>
