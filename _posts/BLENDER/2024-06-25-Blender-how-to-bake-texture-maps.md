---
title: "[BLENDER] How To Bake Texture Maps"
last_modified_at: 2024-06-25
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - baking
  - bake
  - texture maps
  - texture bake
  - create procedural material
  - uv unwrapping
  - procedural material
  - diffuse map
  - roughness map
  - normal map
  - metallic
excerpt: "diffuse map(=color map), roughness map, normal map, metallic map을 baking 해봅시다."
use_math: true
classes: wide
comments: true
---

[How To Bake Texture Maps | Blender 3.5 Beginners Tutorial](https://www.youtube.com/watch?v=zFLxWRfs4Ak)

# Baking 이란?

- Texture baking is a process of transferring texture and material data into image textures.
- We can bake color maps, roughness maps, normal maps, metallic maps, etc.

There can be many reasons to bake textures.

Let's say we designed a 3D model in Blender and created procedural materials and textures with Blender nodes.

In order to export the model to another 3D software with textures, we need to bake the textures before exporting.

Because procedural materials can not work outside of Blender. They need to be converted to image textures.

In this way, we can export the model to game engines such as Unity and Unreal or another 3d software.

Complex materials slow down Blender and other software especially when we work with large scenes.

**We can bake the materials into image textures.** **So, 3D programs won't spend lots of time computing and rendering.**

It's very important for game developers.

**Texture baking can also be used to optimize a high poly mesh for game engines.** **We can add some details to low poly models without high polygon numbers.**

**In other words, we can transfer the high poly mesh data to a low poly mesh.** It is a very useful technique for making animation and game characters.

![bandicam 2024-06-25 14-20-31-652](https://github.com/sandokim/sandokim.github.io/assets/74639652/e698c7ad-a89a-46ba-80cd-d4ea2fb6056e)

Alright.

In this tutorial, we will create a **procedural material** for the barrel model, and bake the material into image textures.

### Baking Diffuse(Color) Map

![bandicam 2024-06-25 14-04-36-428](https://github.com/sandokim/sandokim.github.io/assets/74639652/8bfd16a9-fe75-48f2-8f24-c85cd45abf5c)
![bandicam 2024-06-25 14-05-22-661](https://github.com/sandokim/sandokim.github.io/assets/74639652/b3070ef9-5d1d-4908-9218-411b1ba1bdc3)

### Baking Roughness Map

![bandicam 2024-06-25 14-05-51-990](https://github.com/sandokim/sandokim.github.io/assets/74639652/7a3cc8f8-84b6-4059-a88b-eb3177053cc5)
![bandicam 2024-06-25 14-06-02-960](https://github.com/sandokim/sandokim.github.io/assets/74639652/b949b124-bb4d-4705-9fc7-76321a862979)
![bandicam 2024-06-25 14-06-12-074](https://github.com/sandokim/sandokim.github.io/assets/74639652/f92d84fc-fa5f-4414-aab3-ac46c0e33deb)

### Baking Normal Map

![bandicam 2024-06-25 14-15-18-112](https://github.com/sandokim/sandokim.github.io/assets/74639652/7f0492c5-ed0d-42a0-9983-a619e4ea9ea6)
![bandicam 2024-06-25 14-15-28-588](https://github.com/sandokim/sandokim.github.io/assets/74639652/bde39648-02e8-43c4-97c1-4b3cadc25be1)

### Baking Metallic Map
![bandicam 2024-06-25 14-16-02-996](https://github.com/sandokim/sandokim.github.io/assets/74639652/80177d23-8562-439d-82f3-fba7be51c7d8)
![bandicam 2024-06-25 14-16-07-403](https://github.com/sandokim/sandokim.github.io/assets/74639652/3455eabf-5a5b-4b27-816e-691f6f22fb58)
![bandicam 2024-06-25 14-16-13-117](https://github.com/sandokim/sandokim.github.io/assets/74639652/716885a0-f209-454d-9c51-6aa6b6014171)

------

# Procedural texture란?

In computer graphics, ***a procedural texture is a texture created using a mathematical description***(i.e. an algorithm) rather than directly stored data. The advantage of this approach is low storage cost, unlimited texture resolution and easy texture mapping. These kinds of textures are often used to model surface or volumetric representations of natural elements such as wood, marble, granite, metal, stone, and others.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fd514edf-ed28-49c0-8510-9f2b1b7cae4b)

[출처: 위키피디아 Procedural texture](https://en.wikipedia.org/wiki/Procedural_texture)







