---
title: "[Geometry] Geometric Constraints on Tangent Space and Co-planar Constraint in 3DGS"
last_modified_at: 2025-01-09
categories:
  - Geometry
tags:
  - Geometry
  - Geometric constraints
  - 3D Gaussian Splatting
  - GeoGaussian
  - tangent space
  - co-planar constraint
excerpt: "Geometric constraint in 3DGS"
use_math: true
classes: wide
comments: true
---

> Reference

> [GeoGaussian: Geometry-aware Gaussian Splatting for Scene Rendering](https://arxiv.org/pdf/2403.11324)

In this paper, we propose a geometry-aware Gaussian Splatting method emphasizing rendering fidelity and geometry structure simultaneously. 

Initially, normal vectors are extracted from input point clouds, and then smoothly connected areas are detected based on normals. 

For general regions, we follow the traditional initialization process [19] that represents every point as a sphere. However, each point is parameterized as a thin ellipsoid with explicit geometric information for smoothly connected areas. 

- Specifically, **the third value of the scale vector $S$ is fixed to make the ellipsoid thin.**
- Additionally, **the third column of rotation matrix $R$ decoupled from the covariance matrix $\Sigma$ is initialized by the normal vector**, as shown in Figure 2.

  ![image](https://github.com/user-attachments/assets/3c9c2753-0e83-49bf-a03b-c78172070351)

Based on the design, we encourage these thin ellipsoids to lie on the surface of smooth regions.
