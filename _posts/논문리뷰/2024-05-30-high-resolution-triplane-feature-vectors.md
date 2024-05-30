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

[Objaverse dataset](https://objaverse.allenai.org/) --> filtered Objaverse dataset으로 만들기도 합니다. As there are many low-quality 3D models in the original Objaverse dataset.

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


### Optical Flow(pixel space)를 Gaussian Flow(Gaussian Splat space)로 distill하여 사용한 예시

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4401bd3b-e1c3-4b7d-bd2d-3596c113cd69)

Pretrained된 Zero-1-2-3로 initial Gaussian points를 생성할 수도 있습니다.

Optical flow는 video에 multiple frame이 있을 때, pixel level에서 video에서 움직이는 게 무엇인지 알려줍니다. 위 그림에서 background는 static이어서 아예 움직이지 않으므로 white 색을 가집니다. Optical flow는 intelligent하진 않고, optical flow는 보통 semantic understanding을 가지 않습니다. Optical flow에서는 그냥 blob of pixels이 다음 frame에서 어디로 이동했는지를 봅니다.

위 논문에서는 모든 consecutive pair of frames에 대해 optical flow를 run하고, Gaussian Flow라는 것으로 Distill해서 같은 concept이지만 pixel space가 아닌 gaussian splat space에서 하고 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fd0abe38-a110-430e-8d12-cdfda0b9b8ad)

Optical flow의 단점은 예를 들어 all black인 object를 rotate한다면, 모든 것이 same color기 때문에 optical flow 입장에서는 악몽에 가깝습니다. 실제 무슨 일이 일어나는지 optical flow는 알아차리지 못하기 때문입니다. 이는 optical flow가 pixel space에서 동작하기 때문입니다.

Optical flow는 Pixel Space에서의 every single pixel에 대해서 flow를 줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0fb9f2f7-d83c-440b-9f16-0f4b3b265f69)

3D Gaussian을 Pixel Space로 2D로 splat 된것을 표현할 때 위 그림처럼 그려볼 수 있습니다. Gaussian들은 specific opacity를 가지고 respective to the camera position에 대해 sorting하여 overlapping on the top of each other합니다. 

### Triplane NeRF

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b2dd4c64-21ab-4acf-a83f-f16621a3b9eb)

위 figure에서 3개의 Plane을 feature plane이라고 볼 수 있고, 각 plane은 some little vector를 가진다. 만약 내가 what the feature vector is for a random point in 3D space, you can then project that into each of the plane and then get those 3 little vectors and then combine those vectors for that specific voxel in 3D space. 이것이 2 dimensional feature space에서 3 dimensional feature plane으로 가는 법입니다.

LoRA는 Low Rank Adaptor로 기본적으로 extra set of trainable weights입니다. 위 figure에서 Stable Diffusion (SD)는 frozen하고 LoRA만 업데이트합니다. LoRA가 있으면 original model에 combine 혹은 merge할 수 있습니다. A bunch of delta w로 생각하면 이를 original w에 더하는 것으로 생각할 수 있습니다.

### Triplane을 scaling할 수 있나요? 네 물론 원하는대로 scaling이 가능합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0d312cce-998a-4ca8-8800-3d22cd6a2803)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f8700dae-b8c4-48c9-ae99-d7ceeb7c4516)

high-resolution triplane은 128,128이고 normal triplane은 고작 32,32입니다. (channel dim은 32)

각 plane은 128x128 image이며 32 channel을 가집니다. 그러므로 each little dot here (xyz 축의 3차원 공간에서의 한점)은 하나의 32 dimensional vector입니다. 각 plane의 32 dimensional vector를 concatenate하면 32+32+32인 feature vector를 만듭니다. 이를 Color MLP(NeRF)에 feed합니다.

페이퍼에서 **utilizes low-resolution latent representations to query features from a high-resolution 3D feature volume**이라고 표현했습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b1833d83-00da-491d-8459-969ad4fb4b9b)

비슷한 예시로 40,40에서 high resolution인 80,80으로 디코딩한 것을 볼 수 있습니다. (channel dim은 최종적으로 16) 


### 대부분의 3D generation models은 밑에서 촬영한 view로 학습되지 않습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/22f57084-c451-458a-a262-c5463fb0da10)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/64abe9c1-2cfb-492e-bc39-90a214afa435)

보통 object에 대해서 위쪽에서 촬영한 영상으로 데이터셋이 구성되어 있기 때문입니다. 따라서 undershot view에서 single shot generation을 하면 퀄리티가 매우 떨어지는 문제가 있습니다. 위 figure에서 fish를 undershot view로 single shot generation을 하면 대부분 결과가 매우 안좋게 나오는 것을 볼 수 있습니다.

### Triplane size가 주어졌을 때, 3D output역시 triplane과 같은 사이즈인가요? 아닙니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b2944dd9-d087-403c-bd11-0bed1dea45fe)

NeRF는 defining the field합니다. NeRF는 neural network로 approximating하는 function이고, that function은 field로써 evaluating any point in space가 가능합니다. 따라서 NeRF는 infinite resolution을 가집니다. i.e. NeRF는 What is the value at (0,0,0)? or what is the value at (0,0,0.0000001)도 가능합니다.

Triplane feature는 NeRF에 넣어주는 값의 형태 중의 하나입니다. 

"I want the color at this specific point in space, then you would say "Ok that specific point in space corresponds to pixels of each triplane."

이 triplane의 normalized coordinate of the point in space를 Color MLP에 feed합니다.

**Final resolution of 3D object는 Marching cube의 hyperparameters나 얼마나 많은 query를 Color MLP(NeRF)에서 할 것이냐에 달려있습니다.** 

**만약 query를 100번하면 100개의 points가 생기고, 100개의 points로 mesh를 만들 수 있습니다. 같은 Color MLP(NeRF)로 1,000,000번 query하면 1,000,000의 points가 생기고 이를 marching cube에 넣으면 위 figure에서 최종결과에서 high resolution Colored 3D Model을 얻을 수 있습니다.**






