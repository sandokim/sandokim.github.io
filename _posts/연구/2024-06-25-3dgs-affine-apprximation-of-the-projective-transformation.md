---
title: "[3D CV 연구] 3DGS affine approximation of the projective transformation"
last_modified_at: 2024-06-25
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - affine transform
excerpt: "3DGG affine apprximiation of the projective transformation"
use_math: true
classes: wide
---

### 3dgs의 original paper 혹은 3dgs의 관련 페이퍼를 찾아보면 affine apprxomiation 이라는 개념이 나옵니다.

- [3D Gaussian Splatting for Real-Time Radiance Field Rendering](https://repo-sam.inria.fr/fungraph/3d-gaussian-splatting/)
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1077270-fb9a-4ef4-bfaa-121256bb2ed2)

- [High-quality Surface Reconstruction using Gaussian Surfels](https://arxiv.org/abs/2404.17774)
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fb723212-839d-4163-a85c-5a335f88742f)

***`affine approximation of the projective transformation`: projective transformation 3x3 행렬에서 z축에 따른 perspective distortion이 그냥 없다고 가정하여 affine transformation 3x3 행렬에서 3행을 [0, 0, 1]로 대체하는 것을 의미합니다.***

### Affine Approximation of the Projective Transformation

Affine approximation of a projective transformation is a simplified version of the projective transformation that can be used when the transformation can be approximated by an affine transformation. This approximation is particularly useful in computer vision and image processing when the full projective transformation is not necessary, making computations faster and simpler.

### Projective Transformation
A projective transformation (also called a homography) can map points in one plane to points in another plane, allowing for a perspective change. The transformation can be represented by a 3x3 matrix \( H \) and can be written as:
\[ 
\begin{bmatrix}
x' \\
y' \\
w'
\end{bmatrix}
=
H
\begin{bmatrix}
x \\
y \\
w
\end{bmatrix}
=
\begin{bmatrix}
h_{11} & h_{12} & h_{13} \\
h_{21} & h_{22} & h_{23} \\
h_{31} & h_{32} & h_{33}
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
1
\end{bmatrix}
\]
After transforming, we need to normalize the coordinates by dividing by \( w' \):
\[ 
x' = \frac{h_{11}x + h_{12}y + h_{13}}{h_{31}x + h_{32}y + h_{33}} \\
y' = \frac{h_{21}x + h_{22}y + h_{23}}{h_{31}x + h_{32}y + h_{33}} 
\]

### Affine Transformation
An affine transformation is a special case of the projective transformation where the third row of the matrix is fixed to \([0, 0, 1]\), meaning there's no perspective distortion:
\[ 
A = 
\begin{bmatrix}
a_{11} & a_{12} & a_{13} \\
a_{21} & a_{22} & a_{23} \\
0 & 0 & 1
\end{bmatrix}
\]
Thus, the affine transformation equations become:
\[ 
x' = a_{11}x + a_{12}y + a_{13} \\
y' = a_{21}x + a_{22}y + a_{23} 
\]

### Affine Approximation
When the projective transformation matrix \( H \) is such that the values of \( h_{31} \) and \( h_{32} \) are very small (close to zero), the transformation can be approximated by ignoring these values. Essentially, we assume:
\[ 
h_{31} \approx 0 \quad \text{and} \quad h_{32} \approx 0
\]
This simplifies the projective transformation to an affine transformation:
\[ 
\begin{bmatrix}
x' \\
y' \\
1
\end{bmatrix}
\approx
\begin{bmatrix}
h_{11} & h_{12} & h_{13} \\
h_{21} & h_{22} & h_{23} \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
1
\end{bmatrix}
\]

### Example
Consider a projective transformation matrix:
\[ 
H =
\begin{bmatrix}
1 & 0.5 & 20 \\
0.3 & 1 & 10 \\
0.001 & 0.001 & 1
\end{bmatrix}
\]
To approximate this by an affine transformation, we can ignore the small values \( h_{31} = 0.001 \) and \( h_{32} = 0.001 \):
\[ 
A \approx
\begin{bmatrix}
1 & 0.5 & 20 \\
0.3 & 1 & 10 \\
0 & 0 & 1
\end{bmatrix}
\]

Using this affine matrix \( A \), we can transform a point \( (x, y) = (100, 50) \):
\[ 
\begin{bmatrix}
x' \\
y' \\
1
\end{bmatrix}
=
A
\begin{bmatrix}
100 \\
50 \\
1
\end{bmatrix}
=
\begin{bmatrix}
1 & 0.5 & 20 \\
0.3 & 1 & 10 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
100 \\
50 \\
1
\end{bmatrix}
=
\begin{bmatrix}
1 \cdot 100 + 0.5 \cdot 50 + 20 \\
0.3 \cdot 100 + 1 \cdot 50 + 10 \\
1
\end{bmatrix}
=
\begin{bmatrix}
145 \\
90 \\
1
\end{bmatrix}
\]
Thus, the transformed point \( (x', y') \) is \( (145, 90) \).

### Summary
Affine approximation of a projective transformation is a practical approach when perspective distortion is minimal. It simplifies the transformation by ignoring small values in the transformation matrix, making computations easier and faster while still providing a reasonable approximation.

**Q1:** What are the key differences between affine and projective transformations in terms of geometric properties?

**Q2:** How does the accuracy of an affine approximation of a projective transformation depend on the specific values in the transformation matrix?

**Q3:** Can you provide an example of an application where affine approximation is preferable over full projective transformation?





