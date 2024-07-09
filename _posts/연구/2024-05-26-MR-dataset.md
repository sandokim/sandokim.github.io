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
comments: true
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

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/909ed9aa-45b7-4849-ad83-0bafb9e6dbac)

1) H는 640으로 모두 동일하니 그대로 두고
   
2) Width는 320, 356, 368, 372 사이즈를 갖는 patient에 대해서 372로 통일하도록 방향을 잡고
   
3) 먼저 multi-coil k-space 15개를 ifft로 image domain으로 변환한 뒤
   
4) image domain에서 모든 15개의 image에 대해 zero padding으로 (H,W) = (640,372) 사이즈로 맞춰줍니다
   
5) fft를 하여 (H,W) = (640,372)로 사이즈가 조정된 multi-coil k-space 15개를 얻습니다.

**case: file1000007 26번째 slice의 multicoil k-space 15개를 visualize하여 관찰하여 봅시다.**

**왼쪽부터 순차적으로 Real k-space --> ifft image --> zero padded ifft image --> zero padded Real k-space**

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5b2cfae2-3f88-4f47-8673-308cda9ad018)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a644c2da-60ae-465c-a8ec-b2b280387719)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/15682ef0-7a88-4f10-b3cc-ecd83866d15f)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1ce37557-8814-44c3-89f3-a847461b0140)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/533924d2-0414-44e9-ad46-8917656c4c47)

***0번째, 1번쨰, 7번째 coil k-space는 매우 노이지해서 ifft했을 때, 이미지가 제대로 변환되지 않음을 볼 수 있습니다. 실제로 이런 코일들도 더러 있습니다.***

**normalize안하고 real kspace를 ifft한 영상을 봤을 때 노이지하다면, 원래 정보가 거의 없는 코일이라고 보면 됩니다.**


**npy 저장시 주의할 점**

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d99fb4bf-e95a-4900-bf41-108be6b14004)

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
