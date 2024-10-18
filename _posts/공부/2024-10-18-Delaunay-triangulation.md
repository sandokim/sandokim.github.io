---
title: "[3D CV] Delaunay triangulation"
last_modified_at: 2024-10-18
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

> [들로네 삼각분할(Delaunay triangulation) & 보로노이 다이어그램(Voronoi diagram)](https://darkpgmr.tistory.com/96)

들로네 삼각분할은 이러한 여러 삼각분할 중에서 각각의 삼각형들이 최대한 정삼각형에 가까운 즉, 길쭉하고 홀쭉한 삼각형이 나오지 않도록 하는 분할을 말합니다.

Delaunay triangulation is used for creating **triangular meshes** given a set of points.

## 들로네 삼각분할 활용

### Nearest Neighbor 구하기

**어떤 점들의 집합에 대한 들로네 삼각분할이 있으면 nearest neighbor를 곧바로 구할 수 있게 됩니다.** 

예를 들어, 아래 그림에서 p의 nearest neighbor를 구하고 싶으면 점 p와 연결된 edge들만 조사하면 이들 중에 nearest neighbor가 존재하게 됩니다.

![image](https://github.com/user-attachments/assets/67052cf8-3691-413e-98ca-bedbe340b2eb)

즉, 원래는 모든 점들과 일일히 거리를 비교해 봐야 하겠지만 들로네 삼각분할이 있으면 그 점과 연결된 점들과의 거리만 조사하면 된다는 의미입니다.

또한 k-nearest neighbor를 구할 때에도 들로네 삼각망에서 직접 연결된 점들로부터 시작해서 tree 형태로 점차 탐색범위를 확장해가면 됩니다.

-----------------------------------------------------------------------------------------

### 들로네 삼각분할 조건

들로네 삼각분할에서는 삼각형의 외접원(삼각형을 포함하는 원, Circumcircle) 안에 다른 삼각형의 꼭짓점이 위치하지 않아야 하는 조건이 있습니다.

![image](https://github.com/user-attachments/assets/40cc7513-2322-4fa2-b2f2-25599f650964)

### Computing Delaunay Triangulations

One way to compute the triangualation is the **Bowyer-Watson algorithm**.

![image](https://github.com/user-attachments/assets/cf9b481b-c883-43a5-979f-0b1d76ce1ad0)

최종적으로 아래와 같은 결과를 얻습니다. (자세한 알고리즘은 포스트 상단 유튜브 영상을 참고하세요.)

![image](https://github.com/user-attachments/assets/511deb7f-49b4-4ef7-8d8b-6bfe46891285)
