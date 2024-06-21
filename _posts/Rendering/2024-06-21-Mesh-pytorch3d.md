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
  - textured mesh rendering (flat shading, phong shading, gouraud shading)
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

### SoftRasterizer와 비교했을 때, Pytorch3D에서 달라진점
![bandicam 2024-06-21 10-35-50-341](https://github.com/sandokim/sandokim.github.io/assets/74639652/7c81bfa7-ff6c-42a7-b01f-04e53e458640)
![bandicam 2024-06-21 10-35-56-130](https://github.com/sandokim/sandokim.github.io/assets/74639652/bc440dbf-1b60-415e-8571-466ef4487e34)
![bandicam 2024-06-21 10-36-00-276](https://github.com/sandokim/sandokim.github.io/assets/74639652/6c120847-73da-4c9c-9952-9562baa17f44)
![bandicam 2024-06-21 10-36-06-182](https://github.com/sandokim/sandokim.github.io/assets/74639652/ddfc69ea-08d7-4937-b587-41f9484da188)
![bandicam 2024-06-21 10-36-10-936](https://github.com/sandokim/sandokim.github.io/assets/74639652/087b7b99-3e70-4ede-8ba4-27e787707744)
![bandicam 2024-06-21 10-36-15-828](https://github.com/sandokim/sandokim.github.io/assets/74639652/12de3936-6345-4f92-a554-bc62363d5eca)
![bandicam 2024-06-21 10-37-30-092](https://github.com/sandokim/sandokim.github.io/assets/74639652/84ea93cd-9f47-413b-8297-46d54a81dded)

![bandicam 2024-06-21 10-39-28-053](https://github.com/sandokim/sandokim.github.io/assets/74639652/0d84460b-698c-479b-9def-15892239f455)

### Unsupervised Shape Prediction
![bandicam 2024-06-21 10-39-03-501](https://github.com/sandokim/sandokim.github.io/assets/74639652/c687d214-f316-4790-a65c-a7118c91b02b)
![bandicam 2024-06-21 10-39-06-450](https://github.com/sandokim/sandokim.github.io/assets/74639652/727dd06c-b3ef-432f-86f5-180c4359be17)
![bandicam 2024-06-21 10-39-09-438](https://github.com/sandokim/sandokim.github.io/assets/74639652/861c0f76-c237-42d0-954c-84aa34c9aed0)

![bandicam 2024-06-21 10-42-02-978](https://github.com/sandokim/sandokim.github.io/assets/74639652/d8507821-24d7-4b34-9b61-4343fc8dfe3c)
![bandicam 2024-06-21 10-42-08-773](https://github.com/sandokim/sandokim.github.io/assets/74639652/2aeef9db-1d43-4166-8fd7-15a70015dfcf)
![bandicam 2024-06-21 10-42-12-397](https://github.com/sandokim/sandokim.github.io/assets/74639652/1016dc40-0a7f-46b3-9bca-5107df25ee50)
![bandicam 2024-06-21 10-42-17-422](https://github.com/sandokim/sandokim.github.io/assets/74639652/96383280-63f4-400e-99df-51e2a13b4f09)
![bandicam 2024-06-21 10-42-21-040](https://github.com/sandokim/sandokim.github.io/assets/74639652/81eb0e6c-dead-4153-b10e-1a78b26fa347)

### Textured Mesh Rendering (Flat shading, Phong shading, Gouraud shading)
![bandicam 2024-06-21 10-42-24-155](https://github.com/sandokim/sandokim.github.io/assets/74639652/5fcb1cd7-3bd7-40a2-a3ac-d0c24cd9fed6)

### Pytorch3D는 differentiable mesh renderer 뿐만 아니라 differentiable point cloud renderer도 제공합니다.
![bandicam 2024-06-21 10-44-11-183](https://github.com/sandokim/sandokim.github.io/assets/74639652/b9e4b733-96b6-46ea-ae34-69d1f966aac2)
![bandicam 2024-06-21 10-45-12-376](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1a84bce-8cab-4306-925e-8464f99787af)

### Single Image View Synthesis
![bandicam 2024-06-21 10-45-25-992](https://github.com/sandokim/sandokim.github.io/assets/74639652/d6022cef-4b4a-4377-9c49-f7533c2e5772)
![bandicam 2024-06-21 10-45-29-247](https://github.com/sandokim/sandokim.github.io/assets/74639652/b5ebb8d9-195c-4e36-9cd2-6828b4bca9c3)
![bandicam 2024-06-21 10-45-40-668](https://github.com/sandokim/sandokim.github.io/assets/74639652/670b059b-4d16-4e95-ae50-eb04339d81e4)
![bandicam 2024-06-21 10-46-15-283](https://github.com/sandokim/sandokim.github.io/assets/74639652/170768b7-da33-4f37-9f58-3e7cce7933bf)
![bandicam 2024-06-21 10-46-13-426](https://github.com/sandokim/sandokim.github.io/assets/74639652/a871c9f1-1291-4c16-8412-425079504670)
![bandicam 2024-06-21 10-47-07-910](https://github.com/sandokim/sandokim.github.io/assets/74639652/45c2474c-f4ef-44db-aabd-54c0ab117dfc)

#### Point cloud의 depth를 예측합니다.
![bandicam 2024-06-21 10-51-16-256](https://github.com/sandokim/sandokim.github.io/assets/74639652/ebd9599c-f915-4134-9599-ca7f4d18bdc8)
![bandicam 2024-06-21 10-51-18-811](https://github.com/sandokim/sandokim.github.io/assets/74639652/ebc588c3-e5e5-4e9e-ba7d-eba96ade652e)
![bandicam 2024-06-21 10-51-23-197](https://github.com/sandokim/sandokim.github.io/assets/74639652/9fc2aa24-0417-490e-97d1-556dd7b81c32)
![bandicam 2024-06-21 10-51-34-733](https://github.com/sandokim/sandokim.github.io/assets/74639652/0645ad4c-cedf-4817-945b-22bc90a2a278)
![bandicam 2024-06-21 10-51-38-359](https://github.com/sandokim/sandokim.github.io/assets/74639652/c1dc6669-9213-44be-a29c-2ab730eac8be)
![bandicam 2024-06-21 10-51-44-558](https://github.com/sandokim/sandokim.github.io/assets/74639652/c7c4e166-dcac-423d-bb03-e1f7c5901104)
![bandicam 2024-06-21 10-51-47-412](https://github.com/sandokim/sandokim.github.io/assets/74639652/78c173d1-9640-4c63-a2df-6ddedf9aafcd)
![bandicam 2024-06-21 10-51-50-188](https://github.com/sandokim/sandokim.github.io/assets/74639652/f2dc0d05-6a75-42f2-bcfb-b94b06a0e04f)
![bandicam 2024-06-21 10-51-55-273](https://github.com/sandokim/sandokim.github.io/assets/74639652/aa654420-1415-469c-b9f7-cbabdd1e35f0)
![bandicam 2024-06-21 10-48-03-423](https://github.com/sandokim/sandokim.github.io/assets/74639652/08466c19-4574-4ba8-90e3-8f401de2faf7)
![bandicam 2024-06-21 10-48-30-120](https://github.com/sandokim/sandokim.github.io/assets/74639652/03f93ba9-32cc-45c9-99f1-3b60c754ddc5)
![bandicam 2024-06-21 10-48-50-250](https://github.com/sandokim/sandokim.github.io/assets/74639652/91a37bf2-53d3-48b4-97f5-7aab4b940c75)
![bandicam 2024-06-21 10-49-14-482](https://github.com/sandokim/sandokim.github.io/assets/74639652/c2391f2a-c6c4-4b1c-a623-64d4c24b67ad)
![bandicam 2024-06-21 10-49-23-921](https://github.com/sandokim/sandokim.github.io/assets/74639652/d735f554-a2f1-4f02-ae70-ef2edbaa9448)
![bandicam 2024-06-21 10-49-43-601](https://github.com/sandokim/sandokim.github.io/assets/74639652/87c13ad2-2893-497d-b2cf-fc5b3b5b743c)
![bandicam 2024-06-21 10-49-53-741](https://github.com/sandokim/sandokim.github.io/assets/74639652/ceffb70a-6aca-4808-9fc4-bc8be79ee260)

-----
![bandicam 2024-06-21 10-54-44-530](https://github.com/sandokim/sandokim.github.io/assets/74639652/0ecc42fe-a1f4-4f48-b64b-863b389da3e1)
![bandicam 2024-06-21 10-55-00-608](https://github.com/sandokim/sandokim.github.io/assets/74639652/0f3f88b1-90f3-48c0-9610-e96330963985)
![bandicam 2024-06-21 10-56-36-996](https://github.com/sandokim/sandokim.github.io/assets/74639652/11f527ba-0253-46b1-b46c-961c39b265a0)
![bandicam 2024-06-21 10-57-48-165](https://github.com/sandokim/sandokim.github.io/assets/74639652/69f2cdb9-e001-4a91-bd4f-4ca6001c114c)
![bandicam 2024-06-21 10-58-02-486](https://github.com/sandokim/sandokim.github.io/assets/74639652/3c8ecabd-465b-48ff-96df-2f3cbfc17386)
![bandicam 2024-06-21 10-58-09-460](https://github.com/sandokim/sandokim.github.io/assets/74639652/db8cc96e-6dbe-486b-abb1-8ad4ae79c6fd)
![bandicam 2024-06-21 10-58-38-721](https://github.com/sandokim/sandokim.github.io/assets/74639652/193a6b3a-9c0b-4bd8-a589-28c867d22c0b)
![bandicam 2024-06-21 10-58-45-931](https://github.com/sandokim/sandokim.github.io/assets/74639652/747ac3f5-3537-4bb4-ab2c-89dd8d8cb6f3)
![bandicam 2024-06-21 10-59-17-939](https://github.com/sandokim/sandokim.github.io/assets/74639652/65f05307-9002-4d0b-b6e0-e0b2acd60811)

![bandicam 2024-06-21 10-59-55-244](https://github.com/sandokim/sandokim.github.io/assets/74639652/9892b002-2f7f-497c-bbc9-57d3d02bbe69)
![bandicam 2024-06-21 11-00-03-899](https://github.com/sandokim/sandokim.github.io/assets/74639652/c328b8c4-cb05-4319-8e43-5bdd063f9878)
![bandicam 2024-06-21 11-00-12-586](https://github.com/sandokim/sandokim.github.io/assets/74639652/9e7a775e-524b-406d-8c9c-cc0b4ea74f40)
![bandicam 2024-06-21 11-00-19-273](https://github.com/sandokim/sandokim.github.io/assets/74639652/5dcb0820-fd67-4e26-9d61-40720b983c64)
![bandicam 2024-06-21 11-00-27-314](https://github.com/sandokim/sandokim.github.io/assets/74639652/b8f67fa6-61de-442b-bbf9-cb47103f1b82)
![bandicam 2024-06-21 11-01-33-123](https://github.com/sandokim/sandokim.github.io/assets/74639652/643c2165-2b79-4bbe-9582-65910a699b10)
![bandicam 2024-06-21 11-01-52-802](https://github.com/sandokim/sandokim.github.io/assets/74639652/8f8c983e-63c8-44f1-b210-f90e7007b776)

![bandicam 2024-06-21 11-02-30-397](https://github.com/sandokim/sandokim.github.io/assets/74639652/5acea4b7-36d7-4c44-86c6-5fa676a3ae38)
![bandicam 2024-06-21 11-03-56-761](https://github.com/sandokim/sandokim.github.io/assets/74639652/1742e935-1811-4050-bd80-ec0df35dc1bd)
![bandicam 2024-06-21 11-05-00-189](https://github.com/sandokim/sandokim.github.io/assets/74639652/1188a4de-585b-4a3d-95b0-6a9d921f6e3b)
![bandicam 2024-06-21 11-05-25-678](https://github.com/sandokim/sandokim.github.io/assets/74639652/4bb2e5f9-afec-410a-9e34-796f2b6daa88)

![bandicam 2024-06-21 11-06-23-892](https://github.com/sandokim/sandokim.github.io/assets/74639652/a04caf63-b656-49fa-ae3a-d82cd2b27726)
![bandicam 2024-06-21 11-06-35-364](https://github.com/sandokim/sandokim.github.io/assets/74639652/5c3aef14-6aa2-4ace-9e0f-b914177fe3c7)
![bandicam 2024-06-21 11-06-47-195](https://github.com/sandokim/sandokim.github.io/assets/74639652/00f87077-9694-4398-bfaf-a2419c98fe9a)
![bandicam 2024-06-21 11-06-53-282](https://github.com/sandokim/sandokim.github.io/assets/74639652/8936b563-b28b-4038-be22-d3b7762e8a1c)
![bandicam 2024-06-21 11-07-08-767](https://github.com/sandokim/sandokim.github.io/assets/74639652/a4c222a0-8d03-4852-b594-154944aacca0)

![bandicam 2024-06-21 11-07-23-969](https://github.com/sandokim/sandokim.github.io/assets/74639652/c59ab49b-0534-4a52-8d05-dd911238c266)
![bandicam 2024-06-21 11-07-27-534](https://github.com/sandokim/sandokim.github.io/assets/74639652/08879918-dab7-419b-9df3-975626d86120)
![bandicam 2024-06-21 11-07-43-108](https://github.com/sandokim/sandokim.github.io/assets/74639652/ef178bd7-998c-44d9-95a5-34ae595e045d)
![bandicam 2024-06-21 11-07-48-796](https://github.com/sandokim/sandokim.github.io/assets/74639652/290a56f4-3a8c-4414-ba42-b558fd946cec)
![bandicam 2024-06-21 11-07-52-842](https://github.com/sandokim/sandokim.github.io/assets/74639652/06a26aa9-721b-4441-8c82-b19303837b95)
![bandicam 2024-06-21 11-08-01-900](https://github.com/sandokim/sandokim.github.io/assets/74639652/bbfc319a-93b6-4551-8b17-950c7b45558d)
![bandicam 2024-06-21 11-08-05-585](https://github.com/sandokim/sandokim.github.io/assets/74639652/a9dcaeed-b345-4d38-adb8-6feac36ca242)
![bandicam 2024-06-21 11-08-21-151](https://github.com/sandokim/sandokim.github.io/assets/74639652/7ed8314b-a600-4eb4-aedb-2e996214329f)
![bandicam 2024-06-21 11-08-54-439](https://github.com/sandokim/sandokim.github.io/assets/74639652/1f1bfd79-eb5e-4444-9ed5-06e413bcc027)
![bandicam 2024-06-21 11-09-02-266](https://github.com/sandokim/sandokim.github.io/assets/74639652/a9ffefa3-6497-4016-ad55-dc4b20735c63)
![bandicam 2024-06-21 11-09-21-408](https://github.com/sandokim/sandokim.github.io/assets/74639652/b414f968-5872-47f6-ade1-9c32be8d7ad4)
![bandicam 2024-06-21 11-09-37-517](https://github.com/sandokim/sandokim.github.io/assets/74639652/01d37085-64a4-4b7f-b993-19f04763a194)
![bandicam 2024-06-21 11-09-47-415](https://github.com/sandokim/sandokim.github.io/assets/74639652/49409420-8629-42c2-be3b-50b58f23e63d)

















































