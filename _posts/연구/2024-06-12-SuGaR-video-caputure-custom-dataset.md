---
title: "[3D CV 연구] 3DGS SuGaR video capture & custom dataset"
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
  - video capture
  - custom dataset
  - border artifacts
  - seen area
  - unseen area
  - capture of an object
  - image resolution
excerpt: "3DGS SuGaR video capture & custom dataset"
use_math: true
classes: wide
---

- SuGaR (또는 3dgs) 코드는 어떠한 image resolution도 process가 가능합니다. 다만 HD resolution (such as 1080x1920)에서 잘됩니다.
- 비디오를 촬영할 때, shooting spped에 대한 requirement는 없습니다. (그래도 촬영시, blur를 일으키는 motion blur 같은 건 피해야합니다.)
- shooting angle/trajectory는 당신에게 달렸습니다. real surface처럼 mesh를 만들고 싶으면 다양한 angle들에 대해 촬영해야 합니다. (예를 들어 360도 capture of an object는 잘 됩니다.)
- 만약 object의 한 side에 대한 이미지들만 존재한다면, reconstructed mesh는 frotiner에서 "seen area"와 "unseen area"에 대한 border artifacts가 관찰될 수 있습니다.
- 하지만 hybrid representation (mesh + Gaussians)은 오직 한 side로만 촬영된 object에 대해서도 일반적으로 good입니다.
- `README.md`에서 설명했듯, SuGaR에서는 scene에 따라 150~300 이미지들로 학습했습니다. (이는 NeRFs의 standard numeber입니다.)
- 더 큰 scene에 대해서 reconstruct를 하면 더 많은 이미지들이 필요합니다. 그러나 이는 더 많은 메모리를 요구할 것입니다.
- 첫 번째와 마지막 frame에 대한 consistent같은 건 없습니다. (이 질문 자체가 의미가 없음 사실)

https://github.com/Anttwo/SuGaR/issues/61

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/950e3f42-1f4b-4049-ae0e-6e4a2bf661c3)
