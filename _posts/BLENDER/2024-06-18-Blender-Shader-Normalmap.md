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

### 이제 normals이 triangle의 vertex로부터 만들어지고, 나머지가 normals은 인접한 triangle들의 average와 interpolation을 통해 smoothing되어 만들어진다는 것까지 이해했습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/21e7a222-4091-4f99-8af9-abb08758772a)

### 그럼 이 normals이 어떻게 쓰이는지 알아봅시다.
- 가장 흔하게 normals이 쓰이는 경우는 lighting 입니다.
- 어떻게 우리가 normals로 objects를 비추는지 알기 위해 우리는 dot product을 알아야 합니다.
- dot product는 간단하게 설명하면, 2개의 vectors가 서로 얼마나 닮았는지를 계산합니다.
<div style="text-align: center;">
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/53c095b3-6eab-4f9f-aec1-5b17af833c65" alt="left image" style="display: inline-block; width: 30%; margin-right: 1%;" />
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/e3b73e07-c212-4dbd-ae0b-94827b3a9885" alt="middle image" style="display: inline-block; width: 30%; margin-right: 1%;" />
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/b1fbfc41-6368-4835-bcb3-aba2ec5deaea" alt="right image" style="display: inline-block; width: 30%;" />
</div>

#### incoming light와 surface normal이 주어졌을 때, 우리는 normal과 lighting direction이 얼마나 닮았는지를 질문해볼 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2f9667df-f32c-45b5-ad25-d06b9e4a3844)
- directional light source를 flip하여 생각봐야 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1e3576c-a9c2-4b60-b4b6-d815fca1abdf)
- 즉, light가 shines하는 direction이 아니라
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/31b2057a-6d91-4892-9b27-a3b60f06749a)
- light source를 향하는 direction을 생각해야 합니다. (every sinlge point in the world에서)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d76be6ec-8069-4a9e-9eed-33021d4b7ca1)

### 우리는 dot product를 사용하여 surface normal (N)과 lighting direction (L)이 얼마나 닮았는지 계산해야 합니다.
- 만약 normal과 lighting direction이 같은 방향을 가리키고 있다면, point는 fully illuminated 됩니다.
  <div style="text-align: center;">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/5a9960fc-f9ce-4450-a64a-1da3a1eea783" alt="centered image" style="display: inline-block; width: 45%;" />
  </div>
- 만약 normal과 lighting direction이 서로 orthogonal 하다면, point는 fully unlit 됩니다.
  <div style="text-align: center;">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/65a05a1c-72bd-4e65-9335-57c82b757c92" alt="centered image" style="display: inline-block; width: 45%;" />
  </div>
- 일반적으로 negative light를 model하는 건 useful하지 않기 때문에, negative range는 무시되어 0으로 saturate 시킵니다.
  <div style="text-align: center;">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/9dc911cd-b2cc-4071-b67d-801b9c02b8ba" alt="image 1" style="display: inline-block; width: 45%; margin-right: 2%;" />
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f9731fdd-33a1-40c7-b2e2-4e5e8c6ac4a6" alt="image 2" style="display: inline-block; width: 45%;" />
  </div>






### Reference
[Shader Fundamentals - Normal Mapping](https://www.youtube.com/watch?v=6_-NNKc4lrk)
[Change Your Understanding of Normals In 8 Minutes](https://www.youtube.com/watch?v=g57mNKE8IYc&t=37s)
