---
title: "[Registration] Evaluation Metircs: RRE, RTE, RSE, RDE, RR, ATE"
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
  - RDE
  - RR
  - ATE
excerpt: "Relative Rotation Error (RRE), Relative Translation Error (RTE), Relative Scale Error (RSE), Relative Depth Error (RDE), Absolute Translational Error (ATE)"
use_math: true
classes: wide
comments: true
---

> Reference

> [GaussReg: Fast 3D Registration with Gaussian Splatting]()

> [DReg-NeRF]()

#### Relative Rotation Error (RRE), Relative Translation Error (RTE), and Relative Scale Error (RSE) between the estimated transformation and ground truth transformation as the evaluation metric of the registration accuracy.

- **Relative Rotational Error (RRE)**, the geodesic distance between the estimated and ground-truth rotation matrix.
  - $\text{RRE} = \arccos\left(\frac{\text{Tr}(\mathbf{R}\_\text{est}^\top \mathbf{R}\_\text{gt}) - 1}{2}\right)$
    
- **Relative Translation Error (RTE)**, the ratio of the Euclidean distance between the estimated and ground-truth translation vectors to the norm of the ground-truth translation vector.
  - $\text{RTE} = \frac{\| \mathbf{t}\_\text{est} - \mathbf{t}\_\text{gt} \|}{\| \mathbf{t}\_\text{gt} \|}$
    
- **Relative Scale Error (RSE)**, the ratio of the Euclidean distance between the estimated and ground-truth scale factors to the ground-truth scale factor.
  - $\text{RSE} = \frac{| s_\text{est} - s_\text{gt} |}{s_\text{gt}}$
    
- **Relative Depth Error (RDE)**, the ratio of the Euclidean distance between the estimated and ground-truth depth to the ground-truth depth.
  - $\text{RDE} = \frac{\| \text{estimated depth} - \text{ground-truth depth} \|}{\text{ground-truth depth}}$

- **Recall Rate(RR)** to evaluate the ratio of successful registration.

- **Absolute Translational Error (ATE)**, the Euclidean distance between the estimated and ground-truth translation vectors.



