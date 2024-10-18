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

최종적으로 아래와 같은 결과를 얻습니다. (자세한 알고리즘은 포스트 상단 유튜브 영상을 참고하세요.)

![image](https://github.com/user-attachments/assets/511deb7f-49b4-4ef7-8d8b-6bfe46891285)
