---
title: "[Rendering] Pytorch3D rendering"
last_modified_at: 2024-06-20
categories:
  - Rendering
tags:
  - Rendering
  - 렌더링
  - Rasterization
  - 레스터화
  - 3D mesh
  - Phong Shading
  - pytorch3d
  - AmbientLights
  - RasterizationSettings
  - faces_per_pixel
  - MeshRenderer
  - MeshRasterizer
  - SoftPhongShader
  - BlendParams
  - SuGaR
  - 3dgs
excerpt: "Pytorch3D 소개"
use_math: true
classes: wide
---

[Pytorch3D](https://youtu.be/MOBAJb5nJRI?si=uN_ITZQe1Tn4WpVI)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0011abef-8dcb-4314-92b0-72572d8f861a)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d18471f3-ee79-4fa9-bcc4-17131f75156e)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/36d3fe7b-722b-4de9-abe1-a2a1a65ec64d)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0f599c95-9492-4354-ad9d-aef29c6e7e33)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7a4caa1e-1905-4601-a9ff-058bdf6c032b)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4c940ad9-9431-40af-a445-c0a2991bf2f7)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8c5d1428-c0a1-41c7-9b73-640c59e38620)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0f4cd9ed-7fcc-496d-9c74-af9899a373f0)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/14e48c15-40f5-4abf-87de-246250955b53)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7087034e-eb00-4784-b23a-14a9f0307a1a)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1027c830-96ca-4963-a97f-49e03df07510)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1f060d0-fc1e-4f5d-9b6e-7ac2d93cf1c7)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1c19815-fb86-4972-b91a-e2fd5e790316)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a57acd94-6800-4cb7-846f-a6240c6f6c29)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b3f10070-4876-4d56-be0f-b2728e9bc0a5)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/617ab86d-edcf-43c9-93d3-cc6f9f23488d)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ce45919f-e9f5-4325-8098-ee956da31986)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/49003eec-52cc-4d4c-a7c8-95fdef3fadf5)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4c00e676-3257-492c-a141-fe1682421938)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/323ff775-93a9-4578-8bbd-4dad1b3280b4)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/031b7375-3567-4c9e-b9c3-7513d666603a)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/765eb964-0db1-4166-af88-4cb492e94171)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d1b7497d-75c4-4d03-ab5d-10efdf0903a6)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4666775e-ee82-4a7a-ba41-da7dcd3b7331)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1a64d557-e11f-4a41-9552-8933260ef093)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1bfe9be6-a29e-486f-b502-4d898215d262)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/98621aa4-94d1-4967-9769-7ad4cd9ed74b)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/12cd2966-bcc3-4010-9751-07856c3862f1)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a8494387-feab-4668-8297-8b12835140fa)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/37048919-9a7a-4d3d-b643-6d55e3bad2fa)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/893592e3-8107-4e85-be3e-39ceebea3f4f)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/81fc3384-f62e-4527-a878-5dd604ab2093)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/05437381-2fc8-48a2-977c-b1ed5762f335)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e69e5445-d1fe-4ea5-b5d5-5dcd933dad66)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/13817d1e-ee9c-4b55-a1a8-9b0a368e591b)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a9df0b7e-bd03-4bb9-8e6f-995652739dfc)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c97df62f-7380-43b5-856a-c24ee33d9da5)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a7d3e4de-83d5-48c8-baea-4278b71f7892)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4c441b50-afb4-4581-a279-99c9fb938e0f)

### 3D annotation을 구하기 어렵다. 따라서 2D로 render한 이미지에서 loss를 구하여 3D mesh를 학습시킨다.

![bandicam 2024-06-21 10-22-20-681](https://github.com/sandokim/sandokim.github.io/assets/74639652/aee65025-1350-482d-b181-7fb0b249d7a9)

![bandicam 2024-06-21 10-22-17-183](https://github.com/sandokim/sandokim.github.io/assets/74639652/175edbcd-6ac3-422f-922b-41ee94292aae)





