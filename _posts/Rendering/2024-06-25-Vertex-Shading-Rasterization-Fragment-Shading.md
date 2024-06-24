---
title: "[Rendering] Vertex Shading, Rasterization, Fragment Shading"
last_modified_at: 2024-06-25
categories:
  - Rendering
tags:
  - Rendering
  - 렌더링
  - vertex shading
  - rasterization
  - fragment shading
  - model space
  - wordl space
  - camera space
  - view space
  - visibility z buffer depth buffer
  - triangle
  - surface normal
  - vertices
excerpt: "컴퓨터 그래픽스 전체 프로세스를 설명하는 최고의 영상"
use_math: true
classes: wide
---

# [How do Video Game Grpahics Work?](https://youtu.be/C8YtdC8mxTU?si=_gpbb-TD1xGGxmrS)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e8ab0a57-9f95-4bea-bdfa-ab419ac0cf9b)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d8631600-d1b9-4651-b099-8b4a27fa4aca)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7a894c47-e99c-43f3-af51-cd93d038805a)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/166ef1ec-b325-4c62-86df-448c3ac3815d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1366dec4-69b8-4e29-a6ab-e0a96ebde7bc)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6aac8331-2e79-48b1-96e6-e03391acd085)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e594200a-577d-404c-b26a-92b1b29dab6d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1e622d79-a835-47ea-827b-892ec437ce86)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/05cdfadf-e748-4cdd-8191-e8c1ee1c6ab4)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4d80fedd-b0b2-4d73-ac01-32d02cb76691)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/609ac510-b33f-4f85-86ee-5c40a71814aa)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3200d10c-431e-4095-a268-a641b7e671d3)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3577c9be-c9b2-4c12-96e0-787d3705aa53)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2e17b3e0-c026-4e8f-9467-df53ad32f954)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1decb903-3abb-4e3b-8284-a13e8484aceb)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/234c0971-d3e8-4212-9bf4-f3934a59b7f9)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/aa4e9ae3-e30e-44ae-bd4e-a594cdf5d12a)























