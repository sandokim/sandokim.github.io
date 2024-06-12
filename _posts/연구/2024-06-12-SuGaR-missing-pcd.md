---
title: "[3D CV 연구] 3DGS SuGaR implementations missing point cloud"
last_modified_at: 2024-06-12
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - point cloud
  - density
excerpt: "3DGS SuGaR position_lr_init & spatial_lr_scale"
use_math: true
classes: wide
---

### SuGaR는 먼저 학습하고 output mesh를 생성하고, 이후 mesh와 gaussian에 대한 joint-refinement를 수행해야하므로 command를 두번 나눠서 쳐줍니다.

run GS firstly.
like this:
```python
python -Bu gaussian_splatting/train.py -s ../data/image/resin/output/ --iterations 7000 -m ./output/_guass_splat/
```

then:
```python
python -Bu train.py -s ../data/image/resin/output/ -c ./output/_guass_splat/ -r density
```

modify to your real paths: data path and checkpoint path.

https://github.com/Anttwo/SuGaR/issues/51

