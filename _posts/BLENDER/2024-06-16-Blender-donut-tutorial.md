---
title: "[BLENDER] Donut tutorial"
last_modified_at: 2024-06-16
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - donut tutorial
excerpt: "Blender donut tutorial"
use_math: true
classes: wide
---

1. `shift+a`로 torus를 선택해 생성하고 default값을 low poly로 생성합니다. (나중에 subdivision을 하여 high res로 만들 것입니다.)

2. 🔧를 눌러 Add modifier에서 Subdivision modifier로 high res으로 만들어줍니다. (Levels Viewport, Render에 같은 값으로 설정해줍니다.)

3. Tab키로 Object mode에서 Edit mode로 바꿔줍니다.

4. ⦿ Proportional Editing 모드로 한번에 동그란 영역에 대해 `g`, `r`, `s`, grab, rotate, scale을 할 수 있고, 마우스 휠로 영향을 미칠 영역의 크기를 조절합니다.

5. `Alt`를 누르고 vertex를 하나 누르면 그 라인에 있는 모든 vertices들이 선택됩니다. `s`를 눌러 scale하고 마우스 왼쪽 클릭으로 confirm 혹은 우클릭으로 취소합니다. (`ctrl+` & `ctrl-`로 포함하는 vertices 개수 조절도 가능합니다)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f84205c4-b0c9-4784-aa36-ef705d12af48)
선택된 vertices들은 `h`를 눌러 hide할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5b16cbfd-ce24-4c5d-9d5d-57d6e7a55544)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0cf8c9bf-e5a0-4a15-8488-e2f9b8b8599d)
`Alt+h`를 하면 hide된 vertices들을 다시 불러옵니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f2734556-eb4d-4d50-9003-afcae085be0b)

7. `shift+d`로 duplicate하여 선택한 object를 복제할 수 있습니다. 마우스 좌클릭으로 confirm하거나 우클릭으로 취소합니다.

8. `Snap`을 키고, Snap Individual Elements to `Face Project`로 선택하고, ⦿ Propotional Editing 상태에서 `g`로 잡아내리면 donut위에 icing 효과를 줄 수 있습니다. 이때, icing으로 사용하는 object에 Add modifier로 subdivision modifer를 주는 것이 좋습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/dd938f91-3a6e-48ff-b88c-c870cbfdc011)


9. Toggle X-ray mode를 선택하면, 마우스 좌클릭으로 box 영역으로 선택할 때 object 뒤쪽까지 한번에 선택이 가능합니다. (gizmo로 x, y, z축에서 선택하면 잘못 vertices를 선택할 일을 방지할 수 있습니다.)

10. 두 개 이상의 vertices를 `shift+마우스 좌클릭`으로 선택하고 `e`를 눌러 extrude하여 새로운 Face가 생성됩니다. 이는 Wireframe 모드에서 보면 확실하게 보입니다.
#### Object mode
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2b69abc0-1482-4248-bab4-582d78383403)
#### Wireframe mode
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3b822872-2733-40df-a267-118514c2ddb9)

11. Sculpt mode
- `inflate`의 hotkey는 `i`입니다. `f`는 적용할 영역의 크기, `shift+f`는 strength를 결정합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5d8c11b3-2261-4223-b72d-dc04fe8ab241)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3e8fc4be-c01c-4629-b4b9-2e47954909e3)
- `grab`의 hotkey는 `g`입니다. 이건 마치 손가락으로 푹 찌르듯이 움직여 모양을 만들어줄 때 유용합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/aaf831f0-0c6f-40eb-9341-e92cff9390af)
- `mask`로 칠하면 검정색 부분이 되고, 이 영역은 다른 sculpt tool의 영향을 받지 않게됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f2c31cb1-7a4e-451a-9159-adee13584e94)
mask 영역을 반전시키는 것을 photoshop과 비슷하게 `ctrl+i`로 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e47a6467-47bf-460e-b1e0-0b46a26bc6f6)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/68c567f0-99e6-4ebb-a55b-b6ef13514615)

12. Material Preview는 rendered mode와 같긴한데, 다른 lighting setup입니다. HDRI로 일종의 fake light와 reflection map을 사용해서 빠르게 Material preview를 가능하게 합니다.
#### Material Preview
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7fad99f7-97db-4816-bcda-8b30d0eed05a)
#### Rendered mode
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3a571e50-2f7c-406a-9507-066b623192dd)

13. Shading에서 `shift+a`로 추가하고 싶은 `node`를 선택하고, 넣어주어서 손쉽게 기능을 추가할 수 있습니다.
- 아래에서는 Hue/Saturation/Value node를 추가하고 Hue값을 파란색으로 바꿔본 예시입니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/44532dcc-7085-49b3-9742-ffea90dcbbdc)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2c69a623-d581-41db-853d-a64108b74615)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d73fea8f-865a-4007-8c2b-05a66b6d486f)
- normal에서 마우스 좌클릭으로 drag out하고 놓아줘서 Image texture node를 추가하고 normal texture를 불러옵니다. [자세한 설정은 영상을 참고](https://youtu.be/fsLO1F5x7yM?si=YAJxZNTXMY2tS7LT&t=682)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c761c25a-7b4e-43d4-99c8-d520522f8c1d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/85f13b31-2369-40cb-8e93-b6803afc35e5)
- `shift+a`로 normal map node을 사이에 추가해줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3c37b532-1bf1-4b28-ad45-d262c084fc7e)

14. Texture Painting에서 Object를 `/`로 isolated하게 선택한 상태에서, Material Preview로 선택합니다. Material 탭에서 base color의 • 을 클릭하고 Image Texture를 선택합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/522023cb-37be-43ce-a8d7-3d5ea74cb71a)
- `+ New`를 선택하여 image texture를 직접 만들어줍시다. (혹은 불러옴) resolution은 donut 본체니까 그냥 낮은 resol을 사용해도 되므로 default값을 사용합니다. 이름은 `donut base`로 저장해봅시다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/13ef12ce-0cef-40ad-aa71-cdaa65773035)
- drop down에서 `donut base` 이미지를 선택합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/537951ea-a98f-4193-b05b-4a88dbc8d27c)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a893a47b-2881-4395-902b-12a74a08ff09)
- 이제 2D에서든 3D에서든 paint를 할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/94a72e6a-7440-4d4b-8b19-d9f96551a973)
- 이는 3D의 texture가 uv unwrapping되어 2D image texture가 되기 때문에 가능합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5da75816-4935-4d4b-b283-c5efe6268b7c)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a817176c-d2df-4af3-b1e6-b841c0322f4d)
- Image* 로 * 표시가 있으면 저장이 안된 상태입니다. Texture painting을 한 뒤에는 꼭 이 이미지를 저장을 해줍시다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c616006e-05a3-420d-92bf-1a26e55533e6)
- Local은 isolated mode라는 뜻입니다. isolated object에 대해 모든 작업을 완료한 후에는 `/`로 돌아가줍시다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/49c6fe66-ef0f-41e4-9bf7-d9395937ae07)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ec97a5f2-5e3a-4308-bd6b-9998e435910b)

15. Render Engine
- EEVEE는 rasterize하는 engine이므로 rendering이 빠르지만 퀄리티가 떨어집니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4f8eaff8-8284-451f-a674-4ebfc47a9dc2)
- Cycles은 path tracer, ray tracing하는 engine이므로 느리지만 realistic합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8deb37fa-ff0c-4529-8cf5-4593b1eea14c)
- EEVEE(rasterizer)와 Cycles(ray tracing) 속도 & 퀄리티 비교
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5d8340da-b682-440d-89d4-9b942f5ef7df)
- Cycles을 render engine으로 사용할 때, Device는 GPU로 선택하고, Preferences -> System -> CUDA에서 사용중인 Nvidia GPU를 선택해줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1e908d1b-b87c-4757-aecd-158e19066a88)


### Tips
- object를 선택한 상태에서 `F2`를 누르면 이름 변경이 가능합니다.
- Blender의 modifier는 맨 위쪽에서 아래쪽 순서로 적용됩니다. Solidify 하고 Subdivision 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d622c3a5-9211-4e3b-89e0-1c6a68bdb0d5)
- Sculpt mode에서 isolated로 object를 보고 싶을 땐 `/`를 눌러줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5949c971-05cb-4788-a5e9-3de486c2242b)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/552b63e7-8237-4190-9ec1-125bfd68be55)
- `/`로 isolated object를 볼때 lamp까지 없애버리기 때문에, rendered mode에서 어둡게 보입니다. 이때는 Material Preview로 보면됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a08986ab-5055-4e79-bbf3-de94a993a6e2)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/66f921a7-1b88-4535-8d9a-17f725adfef7)
- `g`, `r`, `s`로 object의 이동, 크기, 스케일을 조절할 때, `ctrl`을 누르면 incremental하게 (0.1씩 증감)할 수 있고, 혹은 직접 숫자를 입력해줄 수 있습니다. 하지만 이는 사실 local에만 적용된 상태입니다. global에 적용하려면, 즉 real world의 실제 크기로 조절해줘야 햡니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/975cc705-d60a-4afc-b30a-6bc7d3e7cafe)
- 예를 들어 object들을 선택하고 `s`로 scale을 바꾼 상태이면 local에서만 바뀐 상태입니다. global에 적용하려면 조절하려면 변화시킨 object들을 선택하고 `ctrl+a`를 누르고 Scale을 Apply합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/87c3acfc-5878-44f8-a16b-de48d86d0a38)
- Face를 선택하고 Cylinder의 위아래면을 `shift`로 좌클릭 선택한 상태에서 `ctrl+b`로 Bevel을 넣을 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b58f9d34-a15d-4bb6-966f-f9c80c373779)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/07586dcc-600b-4dcd-9f92-6ecd0248ac86)
- 마우스 휠 up & down으로 bevel의 수를 선택할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1685d3fb-a67e-4845-9474-5a83f231d257)
- `1`, `2`, `3`로 vertices, edge, face select mode로 바꿀 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/45e349b6-0bd5-48bf-ab02-ecd0f55c1c78)
- i.e. `shift+a`에서 circle을 선택하여 생성하고, `1`인 vertices select mode에서 `Alt+마우스 좌클릭` 후 `e`로 extrude를 하는데, `z`로 z-axis를 따라 extrude 해줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/054f1dbe-3902-423a-b6cf-c29a6afed66d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/984250c1-913f-4f46-b6ce-39fcdf5c49b3)
- `s`로 바깥쪽으로 scale out하고, `e`로 extrude하면서 접시의 모양을 만들어 나갑니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e07e082c-0296-4e3b-b990-59caa96954ea)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/457036e5-3c2b-4c6f-8583-309d25c207d8)
- `z`로 shading mode를 빠르게 왔다갔다 할 수 있습니다. Wireframe mode에서 접시의 안쪽을 채워봅시다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/adc7237e-8cb0-4c75-b394-a4a17e95dd13)
- `Alt+마우스 좌클릭`으로 Circle의 안쪽 vertices를 클릭하고, `f`를 눌러 접시의 바닥면에 해당하는 `face`를 추가해줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/18bb5944-4a69-4360-9f99-3cc109975441)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a71272c7-a1f4-439e-a3b4-f5e35fadf56e)
- `2`인 Edge Select mode에서 `Alt+Shift+마우스 좌클릭`으로 edge들을 선택해주고, `ctrl+b`로 bevel을 만들어줍니다. bevel을 `high poly`로 만드려면 마우스 휠을 올려 vertices수를 늘려줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9147c3f8-2ff6-4346-a7ce-9e168682f748)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c16db682-ac9b-4982-b615-2704a069f415)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5e1d8d07-6898-4257-8462-1113cb8b82c6)
- `Add modifier`에서 `Solidify`를 object에 적용해 접시에 두께감을 줍니다. Thickness를 조절합니다. 이후 `Apply`까지 눌러줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e7c02622-43f2-404a-b4b2-bdb55028a85d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e6acf943-725e-4693-8ba7-84c50e25a87d)




