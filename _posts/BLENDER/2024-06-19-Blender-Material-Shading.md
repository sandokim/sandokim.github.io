![bandicam 2024-06-19 11-22-24-352](https://github.com/sandokim/sandokim.github.io/assets/74639652/ad253a4b-54ad-45b2-aec8-55ddae220c51)---
title: "[BLENDER] BASICS of Material Shading"
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
  - tangent vector
  - bitangent vector
  - tangent space
  - world space
  - object space
  - surface normal
  - face normal
  - pbr (physically based rendering)
  - Render Engine (Cycles, EEVEE)
excerpt: "BASICS of Material Shading"
use_math: true
classes: wide
---

이 튜토리얼에서 Render Engine은 모두 Cycles로 진행합니다.

먼저 Material Property Pannel에서 시작해봅시다.
![bandicam 2024-06-19 11-17-04-979](https://github.com/sandokim/sandokim.github.io/assets/74639652/bc60f9d9-bc7e-459f-96d0-f4a8954b2f8b)

Objects는 하나 이상의 material을 가질 수 있고, 이를 material slots에서 작업해줍니다.

`+` 아이콘으로 새로운 material slot을 만들어줍니다. 하지만 새로 만든 2번째 material slot에 material이 없습니다.
![bandicam 2024-06-19 11-18-09-824](https://github.com/sandokim/sandokim.github.io/assets/74639652/4d9def24-3dd3-4b34-bec5-33842f52f0a2)

- 당신은 drop down box를 사용해서 이미 blender scene에 존재하는 material을 선택해주거나,
![bandicam 2024-06-19 11-20-45-655](https://github.com/sandokim/sandokim.github.io/assets/74639652/f2c36639-7158-4975-ab8f-235c841aae32)
- 혹은 `"New"`를 눌러 새로운 material을 생성할 수 있습니다.
![bandicam 2024-06-19 11-21-42-375](https://github.com/sandokim/sandokim.github.io/assets/74639652/570451cd-80f0-415c-ae90-797cdfdefd06)

- 새로운 material을 만들어서 해봅시다.
- Preview, Surface, Volume, Displacement, Settings와 같은 새로운 탭이 생깁니다.
- Default로 Displacement, Volume에는 아무것도 없습니다. 
![bandicam 2024-06-19 11-22-24-352](https://github.com/sandokim/sandokim.github.io/assets/74639652/2ceb6bb7-9d0d-41a8-bddc-6b04423fe169)
- 하지만 Surface에는 많은 세팅이 생깁니다.
![bandicam 2024-06-19 11-23-53-915](https://github.com/sandokim/sandokim.github.io/assets/74639652/8a5f0cb3-2d7d-427c-8147-ffb6e1cd8e58)
- Preview는 material의 preview를 보여주고, 다른 shape으로 material의 preview를 볼 수도 있습니다.
![bandicam 2024-06-19 11-24-19-850](https://github.com/sandokim/sandokim.github.io/assets/74639652/67ed2594-3415-4812-9342-376f49fbbd55)

- Surface, Volume, Displacement가 Material이 가질 수 있는 Properties입니다.
  - Surface: everything on the outside surface of the object. i.e. paint, wood, metal
  ![bandicam 2024-06-19 11-26-43-409](https://github.com/sandokim/sandokim.github.io/assets/74639652/2cacb453-2f18-4f21-af07-2a49aa896a66)
  - Volume: how you want the three dimensional space inside the object to be filled. i.e. smoke, fog, inside of an object or liquid, cloud, etc.
  - Displacement: physical movement or displacement of the mesh. i.e. you
  ![bandicam 2024-06-19 11-28-52-723](https://github.com/sandokim/sandokim.github.io/assets/74639652/16982f91-ae2b-4236-a9d8-157de93643cf)
  ![bandicam 2024-06-19 11-28-58-692](https://github.com/sandokim/sandokim.github.io/assets/74639652/5dff9819-f3cf-4058-9409-d3fcdb1d02f7)



### Reference
- [Learn the BASICS of Material Shading in BLENDER (Part 1)](https://www.youtube.com/watch?v=Wg244y2f9Fw&t=981s)
- [Blender PBR Material Shading (Material Series Part 2)](https://www.youtube.com/watch?v=jBT6MD7IzHU&t=9s)



