---
title: "[논문리뷰] High Resolution Triplane feature vectors"
last_modified_at: 2024-05-30
categories:
  - 논문리뷰
tags:
  - Computer Vision
  - AI
  - 3DGS
  - NeRF
  - triplane
  - high resolution triplane feature vector
  - single shot generation
  - elevation
  - azimuth
excerpt: "triplane을 이해해봅시다"
use_math: true
classes: wide
---

> [[Youtube](https://www.youtube.com/watch?v=IsRHGf2rGCs)] [[Docs](https://github.com/hu-po/docs)]

[VFusion3D: Learning Scalable 3D Generative Models from Video Diffusion Models](https://arxiv.org/pdf/2403.12034.pdf)

[SV3D](https://arxiv.org/pdf/2403.12008.pdf)

[GaussianFlow: Splatting Gaussian Dynamics for 4D Content Creation](https://arxiv.org/pdf/2403.12365.pdf)

[Generic 3D Diffusion Adapter Using Controlled Multi-View Editing](https://arxiv.org/pdf/2403.12032.pdf)

[Compress3D: a Compressed Latent Space for 3D Generation from a Single Image](https://arxiv.org/pdf/2403.13524.pdf)

[LN3Diff: Scalable Latent Neural Fields Diffusion for Speedy 3D Generation](https://arxiv.org/pdf/2403.12019.pdf)

[ComboVerse: Compositional 3D Assets Creation Using Spatially-Aware Diffusion Guidance](https://arxiv.org/pdf/2403.12409.pdf)

[DreamReward: Text-to-3D Generation with Human Preference](https://arxiv.org/pdf/2403.14613.pdf)

[GRM: Large Gaussian Reconstruction Model for Efficient 3D Reconstruction and Generation](https://arxiv.org/pdf/2403.14621.pdf)

### 위 9개의 논문을 참고하여 NeRF, Gaussian Splatting에서 등장하는 개념 중 하나인 Triplane feature vector의 개념을 이해해봅시다.

triplane encoder는 3D representation에 대한 position embedding처럼 생각해도 됩니다. 3 axes aligned plane에 glue시킵니다. 그러면 3D representation의 voxel 안 어느 위치에서든 query를 할 수 있고, 그 query point에 대한 specific feature point를 얻을 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e688385d-5b7c-4371-9c9f-72d5937c493c)

### Camera orbit elevation을 input으로 주고 싶다면 어떻게 표기할까요?

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/48ff129b-5b3c-41be-94d0-8172c04e4c53)

camera orbit elevation and azimuth angles (e, a)로 표기합니다.

camera orbit elevation: 카메라가 어느정도 높이에서 궤도를 가지고, azmiuth angle: 어 각도로 돌아가는가 

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7e3cbdf5-f128-4959-a929-fc5c07ed1f52)



