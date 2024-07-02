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
  - armature
excerpt: "weights는 vertex groups과 같은 말이고, weights를 조절하여 함께 움직일 vertex groups를 만듭니다."
use_math: true
classes: wide
---

## [Blender 4.0 - Weight Paint Workflow (& Techniques!!)](https://www.youtube.com/watch?v=PLWv9yjVaoU)

Weight Paint Mode로 들어가면 Mesh가 파란색으로 변하는 것을 확인할 수 있습니다. 0의 Weight값을 가지는 정점은 이처럼 파란색으로 표시됩니다.﻿

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/43c7236c-92c8-4e15-a870-58c97450934c)

이러한 색상은 각 정점이 활성화된 그룹에 얼마의 영향력을 갖는지를 나타냅니다. 위의 사진처럼 파란색으로 표시되는 정점은 해당 정점이 0의 weight값을 갖거나 활성화된 정점 그룹에 속해 있지 않다는 것을 나타냅니다.


## Armature in Blender

Armature는 Blender에서 애니메이터가 캐릭터를 애니메이팅하기 위해 사용하는 특수한 오브젝트 타입입니다. 

Blender 공식 매뉴얼에서 Armature는 골격(骨格, skeleton) 뼈대입니다. 하나의 Armature는 여러 개의 뼈(bone)로 구성되고, 각 뼈는 서로 연결되거나 연관되어 유사한 방식으로 움직이거나 변형됩니다.

#### Armature는 아래와 같이 여러 개의 뼈(bone)으로 구성됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2f4770ba-3dd2-4cde-af72-d97e29d7a89f)
