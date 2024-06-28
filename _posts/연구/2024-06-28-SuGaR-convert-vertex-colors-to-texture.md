---
title: "[3D CV 연구] 3DGS SuGaR sugar_model.py _covert_vertex_colors_to_texture"
last_modified_at: 2024-06-23
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - triangle
  - texture image
  - uv map
  - faces_uv
  - verts_coords
  - verts_uv
  - texture_img
  - offset
excerpt: "3DGS SuGaR sugar_model.py _covert_vertex_colors_to_texture"
use_math: true
classes: wide
---

## faces_uv를 정의하여 texture_img로 저장할 때, trinagle의 vertices가 서로 겹치지 않게 하기 위하여 4씩 offset을 줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4733b668-1738-4389-9aaf-e8afb3fa1c21)








