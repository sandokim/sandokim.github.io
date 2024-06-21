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

### 3D annotation을 구하기 어렵다. 따라서 2D로 render한 이미지에서 loss를 구하여 2D supervision으로 3D render한 결과를 학습시킨다.

![bandicam 2024-06-21 10-22-20-681](https://github.com/sandokim/sandokim.github.io/assets/74639652/aee65025-1350-482d-b181-7fb0b249d7a9)

![bandicam 2024-06-21 10-22-17-183](https://github.com/sandokim/sandokim.github.io/assets/74639652/175edbcd-6ac3-422f-922b-41ee94292aae)

### 2D에서 3D로 backpropagate하도록 만들어줍니다.

![bandicam 2024-06-21 10-23-58-573](https://github.com/sandokim/sandokim.github.io/assets/74639652/dc50b038-9fc8-4bb9-9011-7a2a8ca70e42)

### 그러면 rendering pipeline을 이해해야합니다.

![bandicam 2024-06-21 10-25-23-928](https://github.com/sandokim/sandokim.github.io/assets/74639652/03b7ab42-f6c4-43c0-8086-c84fb596050e)

- 첫번째로, shading 단계는 종종 자연스럽게 미분 가능하게 되는데, 이는 face의 내부에서 vertex data를 보간하기 위해 barycentric interpolation과 같은 방법을 포함하기 때문입니다. 이러한 계산은 미분 가능하며, normal과 position of lights를 포함한 다양한 유형의 lighting 계산도 자연스럽게 미분 가능합니다. 이러한 모든 종류의 계산은 기본적으로 미분 가능하며, back propagating을 통해 문제 없이 처리할 수 있습니다.
![bandicam 2024-06-21 10-25-26-147](https://github.com/sandokim/sandokim.github.io/assets/74639652/9559dd2c-68ff-4733-9b0a-c19df7b6df70)

- 하지만 rasterization 단계는 boundary effects와 occlusion effects로 인해 미분 가능성에 문제가 발생합니다. Boundary effects는 경계 부분에서의 비연속성을 의미하며, occlusion effects는 객체가 다른 객체에 의해 가려지는 현상을 의미합니다. 이 두 가지 효과는 미분 가능성을 방해하여, 역전파 과정에서 어려움을 초래할 수 있습니다.
![bandicam 2024-06-21 10-25-44-852](https://github.com/sandokim/sandokim.github.io/assets/74639652/8b93127b-c09c-4ea5-a5af-12750aca200e)
![bandicam 2024-06-21 10-32-56-705](https://github.com/sandokim/sandokim.github.io/assets/74639652/03f23cbc-99fb-4ecb-976f-85fc12f88d97)
  - rasterization 단계에서 non-differentiable한 것을 해결하는 방법은 blur를 하는 것입니다.
  ![bandicam 2024-06-21 10-33-50-035](https://github.com/sandokim/sandokim.github.io/assets/74639652/6919eefc-ed19-4a20-b6ca-4c4289638ca7)

### Pytorch3D에서 달라진점
![bandicam 2024-06-21 10-35-50-341](https://github.com/sandokim/sandokim.github.io/assets/74639652/7c81bfa7-ff6c-42a7-b01f-04e53e458640)
![bandicam 2024-06-21 10-35-56-130](https://github.com/sandokim/sandokim.github.io/assets/74639652/bc440dbf-1b60-415e-8571-466ef4487e34)
![bandicam 2024-06-21 10-36-00-276](https://github.com/sandokim/sandokim.github.io/assets/74639652/6c120847-73da-4c9c-9952-9562baa17f44)
![bandicam 2024-06-21 10-36-06-182](https://github.com/sandokim/sandokim.github.io/assets/74639652/ddfc69ea-08d7-4937-b587-41f9484da188)
![bandicam 2024-06-21 10-36-10-936](https://github.com/sandokim/sandokim.github.io/assets/74639652/087b7b99-3e70-4ede-8ba4-27e787707744)
![bandicam 2024-06-21 10-36-15-828](https://github.com/sandokim/sandokim.github.io/assets/74639652/12de3936-6345-4f92-a554-bc62363d5eca)
![bandicam 2024-06-21 10-37-30-092](https://github.com/sandokim/sandokim.github.io/assets/74639652/84ea93cd-9f47-413b-8297-46d54a81dded)

![bandicam 2024-06-21 10-39-28-053](https://github.com/sandokim/sandokim.github.io/assets/74639652/0d84460b-698c-479b-9def-15892239f455)

### Unsupervised Shape Prediction_
![bandicam 2024-06-21 10-39-03-501](https://github.com/sandokim/sandokim.github.io/assets/74639652/c687d214-f316-4790-a65c-a7118c91b02b)
![bandicam 2024-06-21 10-39-06-450](https://github.com/sandokim/sandokim.github.io/assets/74639652/727dd06c-b3ef-432f-86f5-180c4359be17)
![bandicam 2024-06-21 10-39-09-438](https://github.com/sandokim/sandokim.github.io/assets/74639652/861c0f76-c237-42d0-954c-84aa34c9aed0)







