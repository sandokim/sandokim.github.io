---
title: "[BLENDER] BASICS of Material Shading"
last_modified_at: 2024-06-19
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - texture paint
  - wireframe mode
  - Toggle X-Ray
excerpt: "BASICS of Material Shading"
use_math: true
classes: wide
---

### 복원한 scene들을 모아놓은 상태에서 하나의 object에 대해서만 texture paint 작업을 해봅시다.

- Texture Paint 패널로 들어갑니다.
- Object Mode로 Texture Paint 작업을 할 object를 선택하고 `/`를 눌러 그 object만 타게팅해줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/da873a9a-5dc7-4315-b44b-449911dbc7f1)

- `/`을 누르면 다음과 같이 선택한 object만 보이게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ab606626-1401-48f1-bf36-66ec11898e40)

- Material Properties 탭에서 image texture의 Base Color로 사용된 texture image에 해당하는 이미지 파일을 좌측에서도 drop down 메뉴에서 선택하여 골라줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/be7bd3a8-56c8-448d-9cc6-cf087f574b26)

- Wireframe mode로 바꾸어 mesh를 구성하는 vertices를 확인합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/77d1bbe4-3bba-46fb-8dd0-95fff44c39ce)

- 뒤쪽까지 더 자세히 보기 위해서 Toggle X-Ray를 켜줍니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/49883b1e-11cf-447d-ae42-37bf54b79a4e)

- 형태를 확인했으면, fake light source로 shading을 해주는 `Material Preview` 모드에서 페인팅 작업을 해줍시다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8084ed37-c474-47ef-8d0e-ddb9c0236b8d)

- 물론 `Rendered` 모드에서 작업해도 되나, computational burden이 있어서 조금 렉이 걸리고, light source의 위치에 따라 object의 밝기가 다르게 나타남으로 일관성이 부족하기 때문에, `Material Preview` 모드에서 작업하는 것이 수월합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5e8319ee-c2a2-4c54-a1d9-e5856945c37e)

- 이제 `Material Preview` 모드인 상태에서 `Textrue Paint` 모드로 설정합니다.
  - drop down 메뉴에서 골라도 되고, 
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e2a810c0-9090-43af-b728-e19859085187)
  - `ctrl+tab`으로 나타나는 것에서 골라도 됩니다.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/176916e2-3db6-4463-a7a6-8841bb15d75a)

- 빨간색 paint로 texture를 3D에서 칠해주면 2D인 texture map에서도 빨간색이 반영되는 것을 볼 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/cc18962f-b35f-4976-a3c0-a79ba13dc3f8)

### 현 상황에서는 3D geometry를 제대로 반영하여 3D의 texture가 2D로 UV unwrapping된 것이 아니기 때문에, paint 작업을 2D에서 하는 것은 무리가 있습니다.
- 3D mesh의 geometry를 제대로 2D로 unwrap하기 위해서는 경계선이 되는 Edge 부분을 여러개 선택하고 `Mark Seam`, `UV Mapping -> Unwrap`하는 과정을 거쳐야 합니다.
- 이와 같이 3D mesh의 uv unwrapping을 제대로 해야만 geometry를 이해한 상태의 2D uv map이 생성되고 여기 제대로 된 texture paint 작업이 가능합니다.
- 제대로 uv unwrap된 uv map의 예시는 아래와 같습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/11e0718c-d660-4313-9f85-a35f4f9ba7e1)

