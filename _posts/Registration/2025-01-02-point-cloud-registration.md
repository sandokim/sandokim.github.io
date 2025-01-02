---
title: "[Registration] Point cloud registration is for large-scale 3D scene reconstruction"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - HLoc
  - SuperPoint
  - SuperGlue
  - feature extractor
  - feature matcher
  - GaussReg
  - large-scale 3D scene reconstruction
excerpt: "Point cloud registration is a fundamental problem for large-scale 3D scene scanning and reconstruction"
use_math: true
classes: wide
comments: true
---

> Reference

> [GaussReg: Fast 3D Registration with Gaussian Splatting](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/02380.pdf)

> [From coarse to fine: Robust hierarchical localization at large scale](https://openaccess.thecvf.com/content_CVPR_2019/papers/Sarlin_From_Coarse_to_Fine_Robust_Hierarchical_Localization_at_Large_Scale_CVPR_2019_paper.pdf)

**Point cloud registration is a fundamental problem for larges-cale 3D scene scanning and reconstruction.**

### HLoc vs GaussReg

Our GaussReg is 44× faster than HLoc (SuperPoint as the feature extractor and SuperGlue as the matcher) with comparable accuracy.

