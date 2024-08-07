---
title: "[3D CV 연구] 3DGS coordinate systems"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - coorindate systems
  - point cloud
excerpt: "3DGS point cloud coordinate systems"
use_math: true
classes: wide
comments: true
---

3dgs에서 사용하는 모든 카메라 모델은 COLMAP(OpenCV coordinate system)인 right, down, forward의 축방향을 가지도록 만들어서 사용합니다.

구체적으로는, 아래의 경우에 대해 COLMAP의 camera model로 맞춰줍니다.

- Dataset의 camera poses(=w2c transfomations) --> COLMAP camera model (right, down, forward)
- SIBR viewer에서 camera model(=world_view_transform) --> COLMAP camera model (right, down, forward)

1) COLMAP에서 불러온 w2c에서는 COLMAP camera model을 그대로 사용하므로, 축의 변환을 수행하지 않습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1f492b92-99ee-482f-9477-8bf7273a7710)

2) 반면, nerf-syntheic camera model에서 불러온 transform_matrix는 Blender(OpenGL coordinate system) camera model인 right, up, backward이므로 COLMAP의 camera model인 right, down, forward로 축방향을 바꿔줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b9e90565-6d54-48b5-a9d4-f7f57b7b9c0c)

3) 또한 SIBR viewer는 COLMAP과 다른 camera model을 가지기 때문에, SIBR의 camera들을 COLMAP camera model로 축을 바꿔줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a3434464-cb49-42e1-b0c1-fb17bdaa88dc)

https://github.com/graphdeco-inria/gaussian-splatting/issues/100

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4a66010f-c3b5-4ff0-8fe9-41c5af399a8a)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3417a1fb-f43f-4cdf-acdb-0ec11ed1df4b)

------

### world2camrea and full_transformation matrix를 transposed하여 cuda rasterizer에 넣는 이유

기존 코드와의 호환성 때문에, world2camera와 full_transformation 행렬을 CUDA 래스터라이저에 전달할 때 전치된 형태로 전달합니다.

- CUDA 코드가 행 우선(row-major) 방식을 사용
- Python 코드가 열 우선(column-major) 방식을 사용

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1e1b5461-fa9c-44f2-ad21-c65344a8bb74)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/70f0e5e5-cf29-4886-bdee-ed298b59e07b)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6232062d-6956-460a-a37d-c18edef02cd4)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ed75d011-d463-412d-93c3-e8b8d0808c23)


