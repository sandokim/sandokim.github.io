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

> [Complex Barycentric Coordinates with Applications to Planar Shape Deformation](https://mirelabc.github.io/publications/Complex_bary_coords.pdf)

Constructing a function that interpolates a set of values defined at vertices of a mesh is a fundamental operation in computer graphics. For example, Gouraud shading computes intensity $f[x]$ at an interior point $x$ of a triangle from intensities at the triangle vertices $f_i$ using affine combination

![image](https://github.com/user-attachments/assets/597cd956-159e-45ef-8e07-31c32b853cd8)

The affine weights $b_i[x]$ are often called barycentric coordinates. In a triangle, such coordinates are unique, and $b_i$ has the geometric interpretation as the ratio of the area of the triangle formed by $x$ and **the opposite edge to vertex $i$ over the area of the original triangle.** 

**여기서 The opposite edge to vertex of the area of the original triangle이란** **삼각형의 정점 $i$에 대한 "반대편 변"은 그 정점을 포함하지 않는 나머지 두 정점이 만드는 변을 의미합니다.** Barycentric 좌표에서는, 삼각형 내부의 점 $x$와 이 반대편 변으로 이루어진 작은 삼각형의 면적을, 원래 삼각형의 면적으로 나눈 비율로 정의됩니다.

Barycentric coordinates have many uses in applications such as shading, parameterization and deformation. However, computing such coordinates is not an easy task, especially for non-convex shapes, continuous shapes, or shapes in 3D or higher dimensions.

### Barycentric coordinates in computer graphics applications

Barycentric coordinates are a very useful mathematical tool for computer graphics applications. Since they allow to **infer continuous data over a domain from discrete or continuous values on the boundary of the domain**, barycentric coordinates are used in a wide range of applications - from shading, interpolation, and parameterization to, more recently, space deformations.

