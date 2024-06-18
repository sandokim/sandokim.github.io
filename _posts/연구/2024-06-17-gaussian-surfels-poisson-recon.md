---
title: "[3D CV 연구] 3DGS Gaussian Surfels Poisson Reconstrucion"
last_modified_at: 2024-06-17
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - poisson reconstruction
  - poisson surface reconstruction
  - depth
excerpt: "3DGS Gaussian Surfels Poisson Reconstruction"
use_math: true
classes: wide
---


https://github.com/turandai/gaussian_surfels

Tain a scene:
```shell
python train.py -s path/to/your/data/directory
```
Trained model will be save in ```output/```.
To render images and reconstruct mesh from a trianed model:
```shell
python render.py -m path/to/your/trained/model --img --depth 10
```
We use screened Poisson surface reconstruction to extract mesh, at this line in ```render.py```:
```python
poisson_mesh(path, wpos, normal, color, poisson_depth, prune_thrsh)
```
- **path**
  - 파일의 경로를 나타냅니다.
  - 생성된 메쉬 파일이 저장될 위치를 지정합니다.

- **wpos**
  - 'weighted positions'의 약어로, 점들의 3D 좌표(x, y, z) 정보를 포함합니다.
  - 메쉬를 생성할 때 사용됩니다.

- **normal**
  - 점들의 법선 벡터를 포함합니다.
  - 각 점에서 표면의 방향을 나타내며, 메쉬의 평활도와 방향성을 결정합니다.

- **color**
  - 점들의 색상 정보를 포함합니다.
  - RGB 색상값을 가지며, 각 점에 대응되는 색상을 지정합니다.

- **poisson_depth**
  - Poisson 재구성의 깊이를 나타냅니다.
  - 기본 깊이는 10으로 설정되어 있습니다.
  - 더 큰 장면에서는 더 높은 깊이 수준을 사용할 수 있습니다.

- **prune_thrsh**
  - 메쉬를 프루닝(가지치기)할 때 사용되는 임계값을 설정합니다.
  - 특정 임계값으로 Poisson 메쉬를 프루닝하여 외곽 면을 제거합니다.
  - 임계값이 낮으면 더 많은 점들이 제거되고, 높으면 더 적은 점들이 제거됩니다.
  - 임계값이 너무 낮으면 과도한 프루닝으로 메쉬에 구멍이 생길 수 있습니다.
  - 이러한 경우 "prune_thrsh" 파라미터를 증가시킬 수 있습니다.


In your output folder, ```xxx_plain.ply``` is the original mesh after Poisson reconstruction with the default depth of 10. For scenes in larger scales, you may use a higher depth level.

We prune the Poisson mesh with a certain threshold to remove outlying faces and output ```xxx_pruned.ply```. This process sometimes may over-prune the mesh and results in holes. You may increase the "prune_thrsh" parameter accordingly.

