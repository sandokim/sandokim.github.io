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
In your output folder, ```xxx_plain.ply``` is the original mesh after Poisson reconstruction with the default depth of 10. For scenes in larger scales, you may use a higher depth level. For a smoother mesh, you may decrease the depth value.
We prune the Poisson mesh with a certain threshold to remove outlying faces and output ```xxx_pruned.ply```. This process sometimes may over-prune the mesh and results in holes. You may increase the "prune_thrsh" parameter accordingly.
