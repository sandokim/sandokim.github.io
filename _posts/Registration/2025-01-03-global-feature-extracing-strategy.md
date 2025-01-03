---
title: "[Registration] global feature-extracting strategy"
last_modified_at: 2025-01-03
categories:
  - Registration
tags:
  - Registration
  - 정합
  - GaussReg
  - global feature-extracing strategy
excerpt: "Relative Rotation Error (RRE), Relative Translation Error (RTE), Relative Scale Error (RSE), Relative Depth Error (RDE), Absolute Translational Error (ATE)"
use_math: true
classes: wide
comments: true
---

> Reference

> [GaussReg: Fast 3D Registration with Gaussian Splatting](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/02380.pdf)

> [nerf2nerf: Pairwise Registration of Neural Radiance Fields](https://arxiv.org/pdf/2211.01600)

> [DReg-NeRF: Deep Registration for Neural Radiance Fields](https://openaccess.thecvf.com/content/ICCV2023/papers/Chen_DReg-NeRF_Deep_Registration_for_Neural_Radiance_Fields_ICCV_2023_paper.pdf)

> [NeRFuser: Large-Scale Scene Representation by NeRF Fusion](https://arxiv.org/pdf/2305.13307)

> [CL-NeRF: Continual Learning of Neural Radiance Fields for Evolving Scene Representation](https://proceedings.neurips.cc/paper_files/paper/2023/file/6c7154e394e24c69409256ccf8bf0804-Paper-Conference.pdf)

NeRF2NeRF [14] utilizes human-annotated key points to obtain an initial transformation and refines it using a surface field distilled from a pre-trained NeRF. 

DReg-NeRF [6] extracts features from the occupancy grid of NeRF and applies a decoupling model [38] for NeRF registration, eliminating the need for human interaction in the registration process. 

However, it’s hard to generalize to larger scenes due to its global feature-extracting strategy. 

NeRFuser [10] directly uses the structure from motion method to estimate the transformation using rendered images from NeRF which is very time-consuming. 

CL-NeRF [36] concentrates on the continual learning of NeRF models and proposes an expert adaptor for learning newly changed scenes without finetuning the whole network.



