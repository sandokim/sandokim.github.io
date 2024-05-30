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
  - SAM (Segment Anything Model)
  - Visibility Mask
  - Zero-1-2-3
  - initial gaussian points
  - optical flow
  - 4D Gaussian
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

"camera orbit elevation: 카메라가 어느정도 높이에서 궤도를 가지고, azmiuth angle: 어 각도로 돌아가는가" 로 해석합니다.

<p align="center">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/7e3cbdf5-f128-4959-a929-fc5c07ed1f52" alt="Image" width="50%" hight="50%">
</p>

### SAM을 사용하여 아래그림처럼 Visibility Mask를 생성할 수도 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/173c2c3f-d532-46cb-8af8-ec7ab9ec062d)


### Pretrained된 Zero-1-2-3로 initial Gaussian points를 생성할 수도 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4401bd3b-e1c3-4b7d-bd2d-3596c113cd69)

Optical flow는 video에 multiple frame이 있을 때, pixel level에서 video에서 움직이는 게 무엇인지 알려줍니다. 위 그림에서 background는 static이어서 아예 움직이지 않으므로 white 색을 가집니다. Optical flow는 intelligent하진 않고, optical flow는 보통 semantic understanding을 가지 않습니다. Optical flow에서는 그냥 blob of pixels이 다음 frame에서 어디로 이동했는지를 봅니다.

위 논문에서는 모든 consecutive pair of frames에 대해 optical flow를 run하고, Gaussian Flow라는 것으로 Distill해서 같은 concept이지만 pixel space가 아닌 gaussian splat space에서 하고 있습니다.


















