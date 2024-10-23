---
title: "[3D CV] Barycentric Coordinates with Applications in Mesh Deformation"
last_modified_at: 2024-10-23
categories:
  - 공부
tags:
  - Barycentric coordinates
  - mesh deformation
excerpt: "Barycentric Coordinates with Applications in Mesh Deformation"
use_math: true
classes: wide
comments: true
---

> [Barycentric Coordinates with Applications in Mesh Deformation](https://www.cs.wustl.edu/~taoju/research/bary.htm)

Constructing a function that interpolates a set of values defined at vertices of a mesh is a fundamental operation in computer graphics. For example, Gouraud shading computes intensity $f[x]$ at an interior point $x$ of a triangle from intensities at the triangle vertices $f_i$ using affine combination

![image](https://github.com/user-attachments/assets/597cd956-159e-45ef-8e07-31c32b853cd8)

The affine weights $b_i[x]$ are often called barycentric coordinates. In a triangle, such coordinates are unique, and $b_i$ has the geometric interpretation as the ratio of the area of the triangle formed by $x$ and the opposite edge to vertex $i$ over the area of the original triangle. Barycentric coordinates have many uses in applications such as shading, parameterization and deformation. However, computing such coordinates is not an easy task, especially for non-convex shapes, continuous shapes, or shapes in 3D or higher dimensions.
