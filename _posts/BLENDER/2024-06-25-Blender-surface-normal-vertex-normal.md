---
title: "[BLENDER] Surface Normal & Vertex Normal"
last_modified_at: 2024-06-25
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - Polygon
  - 폴리곤
  - Face
  - Normal
  - surface normal
  - vertex normal
  - phong shading
excerpt: "Vertex Normal은 인접한 Surface Normal의 평균으로 계산됩니다."
use_math: true
classes: wide
---

[3D Basics - What are Normals?](https://www.youtube.com/watch?app=desktop&v=hkTjreiookM)

### Vertex Normal은 인접한 Surface Normal의 평균으로 계산됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ccf48411-9385-4176-9c46-ea08914f31e7)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/07060b75-fcd2-4ec0-8fc2-275bf901f5a7)

Blender에서는 normal의 색깔이 다음과 같습니다.
- face normal 하늘색
- vertex normal 보라색

- 인접한 face normal이 90도, 90도를 이루고 있기 때문에, vertex normal은 그의 평균인 45도로 계산됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6906bff4-276d-4119-8958-3ffae7042649)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/447ab1ae-cd1d-4426-a469-3b8a2de4bcd4)

### Normal은 Shading을 하는데 사용됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0bbc3a3d-4836-4c16-b99b-908660075076)

### Smooth shading(=Phong Shading)을 하면 Vertex Normal로 normals를 계산합니다. 

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ca94d5f5-d14c-4a63-9f26-e056ad21cd42)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f39eb839-5962-4997-84b5-21dab553f5d4)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2187c1e3-19e5-485e-882e-240577cb555c)

- 한 segment에 대한 normal들을 보면 인접한 vertex normal들 (2개의 vertex normal)로 계산되었습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/86709095-1c5e-4bfc-9f91-48c3bfe72aef)

- 이렇게 vertex normal들을 smooth shading하여 생성한 normal들을 모두 표시하면 다음과 같습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e5be954d-d28c-4732-9944-8d0da7ed7ed3)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/787d157b-4c21-462c-88b4-f2917ff3fd3e)

### face normal이 아래와 같이 하나는 안쪽으로, 하나는 밖으로 나와있으면, vertex normal는 옆쪽을 향하게 계산되어 버립니다.

Blender에서는 normal의 색깔이 다음과 같습니다.
- face normal 하늘색
- vertex normal 보라색

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/71be0591-983e-463f-afab-caeb569b2b32)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/dc913d60-35e2-46d7-b27e-0ffc1510843f)

### 이 상태에선 vertex normal이 screwed up 되어버렸기 때문에, smooth shading을 해도 screwed up 되버립니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e805c616-efc6-483e-99e7-5164f72ef74c)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8ec7c0e5-7a55-406d-b963-7e8df8ecd865)
