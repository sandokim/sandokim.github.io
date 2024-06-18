---
title: "[BLENDER] Shader Fundamentals & Normal mapping"
last_modified_at: 2024-06-18
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - Unity
  - 유니티
  - normal map
  - normal mapping
  - Shader
  - Shading
excerpt: "Shader Fundamentals & Normal mapping"
use_math: true
classes: wide
---

- vector가 normalize된다는 것은 vector의 값의 크기가 1인 unit length를 가진다는 의미입니다.
- 두 점을 잇는 한 라인에는 2개 normal vector가 존재합니다.
 
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a7b1beb3-a17b-4a47-92ba-4b3504c78893)

- 우리는 이 normal vectors들로 physics calculations이 가능합니다.
  - 예를 들어, ball이 주어진 angle을 가진 surface에서 어떻게 bounce off 할 것인지
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/afd35de7-0cf8-43d7-bbf6-695948b3eab2)

  - 아니면 얼마나 빠르게 캐릭터가 slope를 slide down할 것인지
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fc4b6391-c1e5-4b78-9cfe-b9dcd3dd6f26)

### 이제 point를 2개에서 3개로 늘려서 dimension을 3차원으로 가져가 봅시다.

- 같은 직선 위에 2개의 점이 모두 위치하는 coilinear한 성분은 사라졌지만, coplanar 성질을 가지게 되므로 같은 평면에 3개의 점이 모두 위치하게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/253b4b17-0b27-4900-b96c-e4a1e90eaa80)

- 2D line과 마찬가지로, 3D plane도 평면인 surface에 수직(perpendicular)인 2개의 orthogonal vectors가 존재합니다. (=plane에 orthogonal한 vectors가 2개 존재함)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8fba7d4b-67f7-47ec-93a1-a584c049ace5)

- 이 orthogonal vectors는 plane 위에 있는 2개의 line들을 cross product해서 구하고 normalize하여 구합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b443f6ac-7bab-4fe5-9c3b-85578ba8d3ae)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1a54c172-bdfb-4d23-b212-ab2ab46288ae)

-  mesh normals은 사실 vertex마다 주어지게 됩니다. (위의 원리처럼 surface마다 주어지는 것이 아님)
-  즉, 하나의 triangle이 3개의 different normals을 가지게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/baea3767-5502-4c7f-a2e7-a6292d59145f)
- 그리고 3개의 different normals로부터 interpolate하여 다른 normals을 구합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/248eceab-0492-496b-968b-a6c088825652)
- 어떤 triangle들끼리는 vertices를 공유할 수 있도록 해주기 위해 이렇게 vertex에서 normal을 구해줍니다.
- 그럼 같은 vertex에서 normal을 중복하여 정의하는 것을 막을 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6313bac4-8f11-4d0e-855c-c45c61e7f4c1)

### 그렇지만 primary motivation 자체는 우리가 surface가 faceted한 것 대신 smooth할 수 있도록 illusion을 만들어주는 것이었습니다.
- Left (faceted one) / Right (Smooth one)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/041d6648-3c0a-4978-8227-2c5bf622bb9e)
- Shader code를 잠깐 살펴봅시다.
  - vertex Shader는 object space normal을 fragment Shader로 pass합니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6f35a3a2-9c4d-4a6b-9b05-d74e65818e26)
  - fragment Shader는 vector를 renormalize합니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3cb8d3f5-f88e-4de4-9381-32ed6702a295)
  - 그리고 XYZ values를 remap하여 -1 ~ 1의 값을 0 ~ 1값으로 바꿔주어, normals을 a nicer gamut(영역) of colors로 visualize하도록 해줍니다.
  - 다시말해 -1 ~ 1의 값을 가지는 3개의 normals을 RGB color의 색영역인 0 ~ 1 사이 값으로 매핑해주어 색을 표현합니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7572658d-4329-46a7-98ab-557ae377d30e)
  - 왼쪽은 triangle마다 존재하는 normals을 smoothing없이 표현한 sphere입니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e0a2182a-52ff-4a42-9b63-cfa99fa8fa63)
  - 오른쪽은 visually smoothing한데, 그 이유는 각 vertex에 있던 normal들은 neighboring triangles에 의해 average되고 각 triangle의 surface를 across하여 interpolated되었기 때문입니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9d464a7f-b1fe-49e9-a453-73b6bd5e25d1)





### Reference
[Shader Fundamentals - Normal Mapping](https://www.youtube.com/watch?v=6_-NNKc4lrk)
[Change Your Understanding of Normals In 8 Minutes](https://www.youtube.com/watch?v=g57mNKE8IYc&t=37s)
