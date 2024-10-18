---
title: "[3D CV] Delaunay triangulation"
last_modified_at: 2024-10-19
categories:
  - 공부
tags:
  - Delaunay triangulation
  - 델로네 삼각분할
  - 외접원
excerpt: "Delaunay triangulation 정리"
use_math: true
classes: wide
comments: true
---

> [Delaunay Triangulation Youtube Explanation](https://www.youtube.com/watch?v=GctAunEuHt4)

Delaunay triangulation is used for creating **triangular meshes** given a set of points.

델로네 삼각분할에서는 삼각형의 외접원(삼각형을 포함하는 원, Circumcircle) 안에 다른 삼각형의 꼭짓점이 위치하지 않아야 하는 조건이 있습니다.

![image](https://github.com/user-attachments/assets/40cc7513-2322-4fa2-b2f2-25599f650964)

### Computing Delaunay Triangulations

One way to compute the triangualation is the **Bowyer-Watson algorithm**.

![image](https://github.com/user-attachments/assets/cf9b481b-c883-43a5-979f-0b1d76ce1ad0)

![image](https://github.com/user-attachments/assets/0638e6f4-ac49-4fd3-80d5-3f06c74eeec9)

![image](https://github.com/user-attachments/assets/711f3ebb-913c-4d1c-b4ad-c30dd41ce6c9)

![image](https://github.com/user-attachments/assets/a4fc6986-4b0e-47ac-a7aa-a1b042706bee)

![image](https://github.com/user-attachments/assets/b9896eb3-ed24-4f7f-8ac6-46ba07095834)

![image](https://github.com/user-attachments/assets/2e60cec0-e959-4693-b442-3dba2d1b1672)

![image](https://github.com/user-attachments/assets/768e2d50-c184-48e5-b27a-172a68e58712)

![image](https://github.com/user-attachments/assets/a4cbebfa-ac3f-4c77-8817-9ef2fe70e5f4)

![image](https://github.com/user-attachments/assets/657fdbda-3e45-47fa-b4ab-c7d5565eeb1c)

![image](https://github.com/user-attachments/assets/81a007ee-0a07-4f28-8aaf-084047ddb890)

![image](https://github.com/user-attachments/assets/94d1a17a-6e39-4d7d-a5eb-69f4601363dc)

![image](https://github.com/user-attachments/assets/5e97e326-0e98-4382-a8bb-fba84bd330f4)

![image](https://github.com/user-attachments/assets/511deb7f-49b4-4ef7-8d8b-6bfe46891285)
