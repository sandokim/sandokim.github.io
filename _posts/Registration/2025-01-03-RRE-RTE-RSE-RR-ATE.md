---
title: "[Registration] Evaluation Metircs: RRE, RTE, RSE, RDE, RR(Success Ratio), ATE"
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
  - RR(Success Ratio)
  - ATE
excerpt: "Relative Rotation Error (RRE), Relative Translation Error (RTE), Relative Scale Error (RSE), Relative Depth Error (RDE), Absolute Translational Error (ATE)"
use_math: true
classes: wide
comments: true
---

> Reference

> [GaussReg: Fast 3D Registration with Gaussian Splatting](https://www.ecva.net/papers/eccv_2024/papers_ECCV/papers/02380.pdf)

> [DReg-NeRF](https://openaccess.thecvf.com/content/ICCV2023/papers/Chen_DReg-NeRF_Deep_Registration_for_Neural_Radiance_Fields_ICCV_2023_paper.pdf)

#### Relative Rotation Error (RRE), Relative Translation Error (RTE), and Relative Scale Error (RSE) between the estimated transformation and ground truth transformation as the evaluation metric of the registration accuracy.

- **Relative Rotational Error (RRE)**, the geodesic distance between the estimated and ground-truth rotation matrix.
  - $\text{RRE} = \arccos\left(\frac{\text{Tr}(\mathbf{R}\_\text{est}^\top \mathbf{R}\_\text{gt}) - 1}{2}\right)$
    
- **Relative Translation Error (RTE)**, the ratio of the Euclidean distance between the estimated and ground-truth translation vectors to the norm of the ground-truth translation vector.
  - $\text{RTE} = \frac{\| \mathbf{t}\_\text{est} - \mathbf{t}\_\text{gt} \|}{\| \mathbf{t}\_\text{gt} \|}$
    
- **Relative Scale Error (RSE)**, the ratio of the Euclidean distance between the estimated and ground-truth scale factors to the ground-truth scale factor.
  - $\text{RSE} = \frac{| s_\text{est} - s_\text{gt} |}{s_\text{gt}}$
    
- **Relative Depth Error (RDE)**, the ratio of the Euclidean distance between the estimated and ground-truth depth to the ground-truth depth.
  - $\text{RDE} = \frac{\| \text{estimated depth} - \text{ground-truth depth} \|}{\text{ground-truth depth}}$

- **Recall Rate(RR) or Success Ratio**, the ratio of successful registration. --> Dataset에 대해 registration이 실패 없이 수행된 비율
  - The quantitative results are shown in Table 1, where the **Success Ratio indicates the portion of successful registrations**. As shown in Table 1, for 82 scenes in ScanNet-GSReg, HLoc only registers 75.6% of them successfully, while our method achieves a **100% success ratio.**
  - We first compared our method with GaussReg on the ScanNet-GSReg dataset. As is shown in Tab. 2, both methods can handle this dataset with a **100% success ratio**, ...

- **Absolute Translational Error (ATE)**, the Euclidean distance between the estimated and ground-truth translation vectors.





