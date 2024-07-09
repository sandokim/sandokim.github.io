---
title: "[Mesh] Chamfer distance"
last_modified_at: 2024-06-24
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - Chamfer distance
excerpt: "Chamfer distance"
use_math: true
classes: wide
comments: true
---

### [PyTorch3D @ SIGGRAPH ASIA 2020 Course: Chamfer Distance 유튜브 설명](https://youtu.be/MOBAJb5nJRI?si=3D74eTL2r6C7DYdl&t=1847)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1e087ef4-5bc1-468e-8949-a07f4b23fd97)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/447c6d0e-3f81-49f9-9c7f-01648c251b2c)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/acd04d29-f678-436c-9646-54226290718c)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2453d395-c480-41af-93ae-e2477b640325)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/bbb0a64f-4f76-442b-8265-831ad94ca331)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ce8c680d-6021-42e1-bf60-01529fa137ad)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e2b1f7bc-9f4f-49b2-801f-002e7f55cef9)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/09061653-d80d-4339-a06b-12fe5eecbb1f)

### barycentric coordinates of point p라고도 불리는 것으로 triangle 내부의 점을 sampling 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/778090e8-f873-4b92-8726-bb2e4cc7d094)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b4d8b27c-7d76-46eb-89b8-49888cbe2271)

### Chamfer distance를 구현시에는 각 target mesh와 new source mesh에서 sampling할 point 개수를 정히고, target mesh와 new source mesh에서 각각 sampling한 point sets 2개 대해 chamfer_distance를 구합니다.
- 아래 예시에선 5000개의 points를 각 mesh에서 sampling 한 다음 chamfer distance를 구했습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ff64c51d-3499-4fba-840c-39639f6352e8)



