---
title: "[Registration] Inverse depth loss in SurgicalGS, EndoGaussian, Deform3dgs"
last_modified_at: 2025-01-09
categories:
  - Registration
tags:
  - Registration
  - 정합
  - surgical scene reconstruction
  - inverse depth map
  - SurgicalGS
  - EndoGaussian
  - Deform3dgs
excerpt: "Inverse depth maps compress the dynamic range of depth values, reducing the disparity between the binocular and rendered depth maps"
use_math: true
classes: wide
comments: true
---

> Reference

> [EndoGaussian: Real-time Gaussian Splatting for Dynamic Endoscopic Scene Reconstruction](https://arxiv.org/pdf/2401.12561)

> [Deform3DGS: Flexible Deformation for Fast Surgical Scene Reconstruction with Gaussian Splatting](https://arxiv.org/pdf/2405.17835)

> [SurgicalGS: Dynamic 3D Gaussian Splatting for Accurate Robotic-Assisted Surgical Scene Reconstruction](https://arxiv.org/pdf/2410.09292)

Previous methods (EndoGaussian, Deform3dgs) incorporate inverse depth maps into the loss computation, effectively stabilising the optimisation process. 

The depth loss in these approaches is formulated as:

$$
L^{−1}_{\hat{D}} = \Vert M \odot (\hat{D}^{−1} − D^{−1|) \Vert, (11)
$$

where $D$ and $\hat{D}$ are the depth map from stereo-matching and rendered depth, respectively. **Inverse depth maps compress the dynamic range of depth values, reducing the disparity between the binocular and rendered depth maps.**

This compression minimises the risk of over-density and enhances the stability of the optimisation process. 

However, there is little variation in the depth map of endoscopic videos. **Using inverse depth maps can overly homogenise the depth values, resulting in inaccurate and inconsistent rendered depth maps.**

To address this problem, our observation is that normalisation can bring both binocular and rendered depth maps in a consistent scale, ensuring training stability while preserving depth variability. 

Our normalised depth loss is formulated as:

$$
L_{\hat{D} = \Vert M \odot (\hat{D}\_{norm} − D\_{norm}) \Vert. (12)
$$


### Surgical Scene에 depth loss 적용시, mask 부분은 제외하기 위해 binary mask를 element-wise multiplication한 다음 계산함.


