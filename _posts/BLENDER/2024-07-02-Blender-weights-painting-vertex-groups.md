---
title: "[BLENDER] Weight Painting = Vertex Groups"
last_modified_at: 2024-07-02
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - vertex groups
  - weights
  - weight painting
excerpt: "weights는 vertex groups과 같은 말이고, weights를 조절하여 함께 움직일 vertex groups를 만듭니다."
use_math: true
classes: wide
---

## [Vertex Groups - Blender 2.80 Fundamentals](https://www.youtube.com/watch?v=dKZrzG5r13g)

Weight Paint Mode로 들어가면 Mesh가 파란색으로 변하는 것을 확인할 수 있습니다. 0의 Weight값을 가지는 정점은 이처럼 파란색으로 표시됩니다.﻿

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/43c7236c-92c8-4e15-a870-58c97450934c)

이러한 색상은 각 정점이 활성화된 그룹에 얼마의 영향력을 갖는지를 나타냅니다. 위의 사진처럼 파란색으로 표시되는 정점은 해당 정점이 0의 weight값을 갖거나 활성화된 정점 그룹에 속해 있지 않다는 것을 나타냅니다.


