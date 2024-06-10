---
title: "[3D CV 연구] 3DGS random shuffle significantly impact the performance"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - scene_info
  - train_cameras
  - test_cameras
  - random shuffling
excerpt: "3DGS random shuffling으로 input images들을 shuffle하지 않으면 performance가 하락합니다."
use_math: true
classes: wide
---

scene_info.train_camera에서 random.shuffle을 하지 않으면 성능이 크게 하락합니다.

다시말해, random shuffle된 view들로 학습할 때보다, camera가 가까운 view들로 먼저 학습하면서 overfitting과 그 view들에 대한 bias가 생길 수 있고, 이로 인해 novel view synthesis의 성능이 하락할 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/69ff89ad-5a64-4de0-b5b8-e4a7df72b0d6)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a8cfcf1d-c0fc-4a6d-97db-c52dc80921cc)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9a9240b6-e55a-4fd0-970d-6cedaf457506)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/81d78712-1646-4050-9dde-bbc2d1f6a1b4)
