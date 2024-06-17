---
title: "[BLENDER] 3DGS SuGaR UV map texturing with Blender"
last_modified_at: 2024-06-16
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - animation
  - blender animation
  - uv map
  - 3dgs
  - SuGaR
excerpt: "3DGS SuGaR UV map texturing with Blender"
use_math: true
classes: wide
---

### SuGaR로 original 3dgs으로 7,000 iters 학습시켜 나온 coarse mesh를 refine하면 refined_mesh를 얻을 수 있습니다.

- OBJ 파일: 3D mesh (without texture)
- png 파일: UV map (3D mesh에 입혀지는 2D texture)

- playroom scene을 SuGaR로 학습하여 얻게된 UV map 이미지 크기
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9bfc0c36-32d4-49fa-adb0-75032eb8bdd1)

- SuGaR에서 얻은 refined_mesh인 OBJ 파일은 UV map 이미지와 같은 폴더 안에 들어있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c036bd0c-aca9-48f4-b363-29aa961cd2f5)

- 이처럼 UV map이 mesh와 같은 폴더에 존재하는 경우 textured mesh로 Rendered 모드에서 볼 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f914d662-6d2a-4af5-b083-930de935a27e)

- 하지만 UV map을 mesh와 함께 있던 폴더에서 제거해주게 되면, Blender에서 자동으로 같이 불러오던 UV map이 사라져 Rendered mode에서 texture가 사라지게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8129e4f7-3a49-46bd-8f9b-f55a70b821b0)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1a6a95a3-bf0a-4f2f-9558-8ea87e121218)


