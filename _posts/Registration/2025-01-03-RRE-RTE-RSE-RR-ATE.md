---
title: "[Registration] Evaluation Metircs: RRE, RTE, RSE, RR, ATE"
last_modified_at: 2025-01-03
categories:
  - Registration
tags:
  - Registration
  - 정합
  - evaluation metric
  - RRE
  - RTE
  - RSE
  - RR
  - ATE
excerpt: "Relative Rotation Error (RRE), Relative Translation Error (RTE), and Relative Scale Error (RSE), Absolute Translational Error (ATE)"
use_math: true
classes: wide
comments: true
---

> Reference

> [GaussReg: Fast 3D Registration with Gaussian Splatting]()

> [DReg-NeRF]()

#### Relative Rotation Error (RRE), Relative Translation Error (RTE), and Relative Scale Error (RSE) between the estimated transformation and ground truth transformation as the evaluation metric of the registration accuracy.

- **Relative Rotational Error (RRE)**, the geodesic distance between the estimated and ground-truth rotation matrix.

- **Relative Translation Error (RTE)**, the ratio of the Euclidean distance between the estimated and ground-truth translation vectors to the norm of the ground-truth translation vector.

- **Relative Scale Error (RSE)**, the ratio of the Euclidean distance between the estimated and ground-truth scale factors to the ground-truth scale factor. 

- **Recall Rate(RR)** to evaluate the ratio of successful registration.

- **Absolute Translational Error (ATE)**, the Euclidean distance between the estimated and ground-truth translation vectors.



