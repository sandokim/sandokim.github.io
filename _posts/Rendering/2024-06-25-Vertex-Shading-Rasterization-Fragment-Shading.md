---
title: "[Rendering] Vertex Shading, Rasterization, Fragment Shading"
last_modified_at: 2024-06-25
categories:
  - Rendering
tags:
  - Rendering
  - 렌더링
  - vertex shading
  - rasterization
  - fragment shading
  - model space
  - wordl space
  - camera space
  - view space
  - visibility z buffer depth buffer
  - triangle
  - surface normal
  - vertices
excerpt: "컴퓨터 그래픽스 전체 프로세스를 설명하는 최고의 영상"
use_math: true
classes: wide
---

# [How do Video Game Grpahics Work?](https://youtu.be/C8YtdC8mxTU?si=_gpbb-TD1xGGxmrS)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e8ab0a57-9f95-4bea-bdfa-ab419ac0cf9b)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d8631600-d1b9-4651-b099-8b4a27fa4aca)

### model space -> world space -> camera space -> view space
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7a894c47-e99c-43f3-af51-cd93d038805a)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/166ef1ec-b325-4c62-86df-448c3ac3815d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1366dec4-69b8-4e29-a6ab-e0a96ebde7bc)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6aac8331-2e79-48b1-96e6-e03391acd085)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e594200a-577d-404c-b26a-92b1b29dab6d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1e622d79-a835-47ea-827b-892ec437ce86)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/05cdfadf-e748-4cdd-8191-e8c1ee1c6ab4)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4d80fedd-b0b2-4d73-ac01-32d02cb76691)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/609ac510-b33f-4f85-86ee-5c40a71814aa)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3200d10c-431e-4095-a268-a641b7e671d3)

### z buffer, depth buffer

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/aae80475-1180-42aa-ad6d-b83e7b3ab7b8)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5d6c300f-ae13-4d40-88d1-6582699b8621)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4116cc4e-c24a-467e-b4ae-fbf193e4ebf8)
- lowest z value를 가지는 triangle만 screen에 표시됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8c24c9f9-30e3-4fed-87d5-0d14affb758b)

- image of the z 혹은 depth buffer로 불리우는 이미지는 아래와 같습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3577c9be-c9b2-4c12-96e0-787d3705aa53)
- triangle이 3차원 공간에 존재하므로 triangle의 3개의 vertices도 보통 서로 다른 값을 가지게 됩니다.
- 이때, triangle 내부의 z value은 3개의 vertices로 interpolation하여 얻습니다.
- 그러고나면 triangle끼리에서도 z value가 작은 것이 screen에 표시됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2e17b3e0-c026-4e8f-9467-df53ad32f954)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1decb903-3abb-4e3b-8284-a13e8484aceb)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/234c0971-d3e8-4212-9bf4-f3934a59b7f9)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/aa4e9ae3-e30e-44ae-bd4e-a594cdf5d12a)

###  pixel fragment, Shading
- As a reminder, fragments are groups of pixels formed from a single rasterized triangle.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4401d2d0-113d-4dfc-aad8-ea1c9de5d43d)
- 만약 각 pixel fragments에 대해 같은 color를 적용하면 realistic하지 않습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c4c9d0e5-8fcc-4e12-bbc9-f9bd3a04cb8a)
- shading을 적용하여 아래는 검정색 위는 하얀색으로 만들거나
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7e6b7130-b876-4175-b12f-48fcd9583836)
- 그리고 specular highlights를 더하고
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8d7814f3-7ceb-4401-822b-5087fb81b361)
- shininess를 더하여 lights가 surface에서 bounce off하게 하면
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9c9d486d-b565-4c9e-9e50-dd079affa913)
- realistic train을 얻습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/998ab25c-15b0-48dd-b409-b0cd195b757d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/01a82639-746a-4c60-ad31-bf3145134aa2)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a8da6126-4159-44c6-9b13-3225dcf3bd1b)

### framgent shading(=triangle shading)
- 그럼 실제로 shading이 어떻게 진행되는지 알아봅시다.
- 기본적으로 surface가 light source를 directly하게 pointing at하면 밝아집니다.
- 반대로 surface가 light source와 perpendicular하거나 반대방향에 있으면 어두워집니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/87722a04-1a62-4111-964f-c1ac49f1092e)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5bcfa32a-e7fc-40f2-b625-450ab2d87b26)


- direction of the light, direction of triangle's surface을 가지고 계산합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ddd4ca20-499c-4ff7-88b8-b3eae5b503a5)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7bad4d50-1e4b-45fc-b2cf-6a46a615d6d1)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/33251dea-52a3-4574-a5ca-1779af680aac)

### surface normal
- 아시다시피 아래 train은 762,000개의 `flat triangle`로 이루어져있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0d49999d-dae4-4e1b-8e87-37be8d4465c4)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1576ea8c-a31e-48f5-befd-1f46ef88c7c2)
- 그리고 triangle들은 다른 방향을 바라보고 있고, 각각의 triangle이 바라보는 방향을 surface normal이라고 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4df7c3fd-664c-4fe2-a072-4f50ba26b4b2)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6ba19d2a-2413-40d5-9be6-8fa330310264)

### surface normal과 light source로 cosine 계산

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fac31a35-0e24-452f-9f14-83a7c723cbcd)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/35cef61b-aa13-45dd-bc59-493ac17559d4)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f815109d-3b57-4b5f-9aa6-349537f8858f)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/649ba906-2755-409e-ab45-3e34ff0737a8)

- cos(θ)에 light intensity와 material의 color를 곱해줘서 properly shaded된 triangle을 얻습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/535cafbd-6ddc-49ce-afa5-2dba6eade392)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1d86a9cc-0779-4569-96d8-70ade552aaa7)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/dfc4227b-23c6-498d-b46d-87b649cf0f67)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4972e7e0-3502-4c92-ab36-786999304cbe)

- 90도에서 270도 사이까진 cos값이 음수이고, RGB color는 음수로 쓸 수 없으므로, minimum을 0으로 제한합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/821c3849-9c4e-4af1-897f-d22a804cc559)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f48750dd-baf6-4b15-8695-58621f2c9a34)
- 하지만 0으로 제한하더라도, 이 90도에서 270도 사이로light source에 대해 돌아간 triangle은 pitch-black surface를 가집니다.
- 즉, triangle의 표면이 매우 어두워 빛을 아예 반사하지 않게 되어 이상해보입니다.
- 이를 해결하기 위해 Ambient light intensity와 surface color를 곱한 값을 더해줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d97fa504-6985-4e37-83ef-cd4cd2e58a93)






















