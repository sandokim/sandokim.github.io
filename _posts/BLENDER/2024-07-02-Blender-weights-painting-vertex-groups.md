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

## Armature in Blender

Armature는 Blender에서 애니메이터가 캐릭터를 애니메이팅하기 위해 사용하는 특수한 오브젝트 타입입니다. 

Blender 공식 매뉴얼에서 Armature는 골격(骨格, skeleton) 뼈대입니다. 

하나의 Armature는 여러 개의 뼈(bone)로 구성되고, 각 뼈는 서로 연결되거나 연관되어 유사한 방식으로 움직이거나 변형됩니다.

#### Armature는 아래와 같이 여러 개의 뼈(bone)으로 구성됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2f4770ba-3dd2-4cde-af72-d97e29d7a89f)

블렌더에서 Armature는 캐릭터 애니메이션을 위한 중요한 구성 요소로, 여러 개의 뼈대(Bone)로 구성됩니다. 각 뼈대는 연결되어 캐릭터의 움직임과 변형을 제어할 수 있습니다

## Weight Painting

Weight Paint Mode에서 각 정점이 특정 뼈대에 의해 얼마나 영향을 받는지를 색상으로 표시합니다. 

이 색상은 파란색에서 빨간색까지 다양하며, 파란색은 0의 weight 값을 가지며, 빨간색은 최대 weight 값을 가집니다.

Weight Paint Mode로 들어가면 Mesh가 파란색으로 변하는 것을 확인할 수 있습니다. 

0의 Weight값을 가지는 정점은 이처럼 파란색으로 표시됩니다.﻿

#### 선택된 Bone에 대한 Weight를 아래와 같이 확인할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6aa55071-0803-45f1-989a-93d7e6f5ec3b)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/33e69658-ff8d-48f9-a4df-d4acb7b1a221)

#### 캐릭터 Rig에서 예제에서도 자세히 보시면 얇은 Bone이 선택되어 파란색인 상태고, 이에 대한 Weights가 허벅지 주변에서 빨갛게 나타나고 있습니다.
- 즉, 선택된 Bone을 움직일 때, 허벅지 주변의 vertex groups이 같이 유기적으로 움직일 수 있도록 해줍니다.
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/43c7236c-92c8-4e15-a870-58c97450934c)


### Reference
- [Blender 4.0 - Weight Paint Workflow (& Techniques!!)](https://www.youtube.com/watch?v=PLWv9yjVaoU)
- [Vertex Groups - Blender 2.80 Fundamentals](https://www.youtube.com/watch?v=dKZrzG5r13g)

