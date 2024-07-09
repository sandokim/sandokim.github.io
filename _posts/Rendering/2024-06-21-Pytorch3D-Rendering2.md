---
title: "[Rendering] Pytorch3D rendering 2"
last_modified_at: 2024-06-21
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
  - NeRF
excerpt: "Pytorch3D 소개 2"
use_math: true
classes: wide
comments: true
---

[Pytorch3D](https://youtu.be/MOBAJb5nJRI?si=uN_ITZQe1Tn4WpVI)

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

![bandicam 2024-06-21 11-10-42-412](https://github.com/sandokim/sandokim.github.io/assets/74639652/a6c73b5f-0191-49e8-ab33-0b59481e0e25)
![bandicam 2024-06-21 11-12-35-527](https://github.com/sandokim/sandokim.github.io/assets/74639652/55cb7a29-1ace-493f-a770-e68b68c3ea64)
![bandicam 2024-06-21 11-12-55-126](https://github.com/sandokim/sandokim.github.io/assets/74639652/ee74aa9b-4e5a-4e0c-ac7e-b6693d88b54b)
![bandicam 2024-06-21 11-13-05-788](https://github.com/sandokim/sandokim.github.io/assets/74639652/08a6b452-eeab-4ddb-839d-23e20a88c49c)
![bandicam 2024-06-21 11-13-09-032](https://github.com/sandokim/sandokim.github.io/assets/74639652/5476f823-2d0e-46ec-b6c1-f34860bb39bc)
![bandicam 2024-06-21 11-13-23-610](https://github.com/sandokim/sandokim.github.io/assets/74639652/633ebc67-eb8f-4866-8bb2-e60d9ee22837)
![bandicam 2024-06-21 11-13-32-760](https://github.com/sandokim/sandokim.github.io/assets/74639652/0e0ffd8d-28f6-49f2-a27a-3666e3171c93)
![bandicam 2024-06-21 11-13-53-187](https://github.com/sandokim/sandokim.github.io/assets/74639652/be9f71df-4bc2-419d-9f0d-9078cc2d62e9)

![bandicam 2024-06-21 11-14-18-404](https://github.com/sandokim/sandokim.github.io/assets/74639652/6bc9b8e8-838b-464a-9035-b1be21bdd679)
![bandicam 2024-06-21 11-23-26-309](https://github.com/sandokim/sandokim.github.io/assets/74639652/f43390d3-e00a-4057-8f1d-4ebf1ab668d6)
![bandicam 2024-06-21 11-23-50-291](https://github.com/sandokim/sandokim.github.io/assets/74639652/eb0f0151-2765-4e86-8d3a-fae1b8896421)
![bandicam 2024-06-21 11-23-56-906](https://github.com/sandokim/sandokim.github.io/assets/74639652/29e8d4b2-3e4a-4de4-bb4e-9c7cee842458)
![bandicam 2024-06-21 11-24-09-481](https://github.com/sandokim/sandokim.github.io/assets/74639652/6253ef17-a5ae-4f74-92a7-d4bdae07f596)
![bandicam 2024-06-21 11-24-19-708](https://github.com/sandokim/sandokim.github.io/assets/74639652/39b07742-d66e-4e74-b897-0b6c6ffc6ab9)
![bandicam 2024-06-21 11-24-38-677](https://github.com/sandokim/sandokim.github.io/assets/74639652/96e28cf5-25f2-4b21-99c2-95ab3c7dbd7a)
![bandicam 2024-06-21 11-25-20-605](https://github.com/sandokim/sandokim.github.io/assets/74639652/79283943-46d9-4dfc-b47a-b395f95ab6c3)


