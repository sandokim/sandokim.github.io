---
title: "[3D CV 연구] 3DGS batch rendering using gaussian rasterizer"
last_modified_at: 2024-06-10
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - batch rendering using gaussian rasterizer
excerpt: "3DGS batch rendering using gaussian rasterizer"
use_math: true
classes: wide
---

**현재 3dgs에서 differentiable rasterizer는 오직 single image에 대해서만 loss를 계산하고 있습니다.**

하지만 batch rendering을 하여, 하나의 frame만이 아닌 여러 개의 frame에 대한 gaussian들의 properties를 optimize할 수도 있긴합니다.

다만, 이를 위해서는 CUDA coding을 해야하므로 저자도 이 부분에 대한 구현이 쉽지 않을 것임을 밝히고 있습니다.

https://github.com/graphdeco-inria/gaussian-splatting/issues/92

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/aee609a9-02e1-4bd1-8677-dbb6d7c43034)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4928d4b6-64af-4a47-8df5-25977255ee61)

저자의 답변

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ba39f82d-e757-49fd-8878-1dbe6a3618e4)

