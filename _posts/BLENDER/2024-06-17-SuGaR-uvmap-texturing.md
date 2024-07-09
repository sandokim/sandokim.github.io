---
title: "[BLENDER] 3DGS SuGaR UV map texturing with Blender"
last_modified_at: 2024-06-16
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - animation
  - blender animation
  - uv map
  - uv unwrapping
  - 3dgs
  - SuGaR
  - mtl file
  - obj file
excerpt: "3DGS SuGaR UV map texturing with Blender"
use_math: true
classes: wide
comments: true
---

- **OBJ 파일**
  - OBJ는 3D 모델이 저장되는 파일의 포맷 중 하나입니다.
  - Vertex, texture 좌표, normal, face 정보(한 면을 구성하는 vertex, texture, normal) 등이 담겨 있습니다.
  - 3D 모델링 툴(3dmax, 스케치업, maya 등등)에서 .obj로 Export를 하게 되면 .obj 파일을 생성할 수 있습니다.

- **MTL 파일**
  - .mtl 파일은 Material Library File입니다.
  - .mtl 파일은 .obj에서 사용되는 재질 속성들에 대한 정보를 포함하고 있습니다.
  - 예를 들어, 텍스처 맵, 반사율, 투명도 등의 정보가 포함됩니다.

- **OBJ와 MTL 파일의 관계**
  - 따라서 .obj 파일과 .mtl 파일은 세트라고 볼 수 있습니다.
  - MTL이라는 별도의 material 파일을 사용하기 때문에 3D 모델의 형상 외에 texture, material 정보를 함께 옮길 때는 MTL 파일과 함께 옮겨야 합니다.

------

### SuGaR로 original 3dgs으로 7,000 iters 학습시켜 나온 coarse mesh를 refine하면 refined_mesh를 얻을 수 있습니다.

### SuGaR에서 export하는 파일 3개
- OBJ 파일: 3D mesh (without texture)
- MTL 파일: OBJ 파일에 적용되는 재질 속성에 대한 정보를 포함
- png 파일: UV map (3D mesh에 입혀지는 2D texture)

- playroom scene을 SuGaR로 학습하여 얻게된 UV map 이미지 크기
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9bfc0c36-32d4-49fa-adb0-75032eb8bdd1)

### SuGaR에서 export한 MTL 파일이 재질을 정의하고, 재질에 texture 이미지를 매핑하도록 작성되어 있습니다.

***따라서 OBJ 파일을 열었을 때, 자동으로 mesh에 대한 재질을 정의하고 UV map으로 사용할 이미지가 재질에 매핑되는 것입니다.***
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fa02da0c-eb70-4de5-bd84-c59a59907817)

- newmtl mesh
  - newmtl은 새로운 재질(Material)을 정의하는 키워드입니다.
  - mesh는 이 재질의 이름입니다.
  - 이 라인은 "mesh"라는 이름의 새로운 재질을 정의하고 있음을 나타냅니다.
- map_Kd sugarfine_3Dgs7000_sdfestim02_sdfnorm02_level03_decim1000000_normalconsistency01_gaussperface1.png
  - map_Kd는 텍스처 맵을 적용하는 키워드입니다. 여기서 Kd는 Diffuse 색상, 즉 확산광을 의미합니다. 이 재질의 확산광에 텍스처를 적용합니다.
  - sugarfine_3Dgs7000_sdfestim02_sdfnorm02_level03_decim1000000_normalconsistency01_gaussperface1.png는 텍스처 이미지 파일의 이름입니다.
  - 이 라인은 "mesh" 재질에 확산 텍스처로 주어진 PNG 파일을 적용하고 있음을 나타냅니다.

### i.e. 이 원리로 인해 SuGaR로 recon된 playroom scene가 textured mesh로 나타나는 것을 볼 수 있습니다.
- SuGaR에서 얻은 refined_mesh인 OBJ 파일은 MTL파일 & UV map과 같은 폴더 안에 들어있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c036bd0c-aca9-48f4-b363-29aa961cd2f5)

- MTL 파일로 인해 OBJ 파일을 textured mesh로 Rendered 모드에서 볼 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f914d662-6d2a-4af5-b083-930de935a27e)

- 하지만 UV map을 mesh와 함께 있던 폴더에서 제거해주게 되면, Blender에서 자동으로 같이 불러오던 UV map이 사라져 Rendered mode에서 texture가 사라지게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8129e4f7-3a49-46bd-8f9b-f55a70b821b0)
- 이 경우, Rendered 모드에서 texture가 사라져서 보라색으로 표현이 되는 것을 볼 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4df0d2ff-692c-4056-bfb8-9fd132c30ab0)

### Texture Paint를 UV map을 통해 해봅시다.

- 먼저 Texture Paint 모드로 패널을 바꿔주고, Rendered 모드에서 작업해줍니다. (Material Preview 모드에서 해도 됩니다.)
- Material -> Surface -> Base Color를 보면 이미 UV map이 같이 불러와져서 Image Texture로 지정되어 있는 것을 확인할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8efab586-07ad-4c93-888a-8e8d56be46e0)

- UV map의 drop down에서도 UV map으로 SuGaR에서 output해서 나온 UV map 이미지로 지정되어 있습을 확인할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d9b078b2-1b67-4a13-b5a5-1da0a1d22411)


- 이제 2D 혹은 3D에서 Paint을 해주면 UV unwarpping (3D --> 2D) 되어 있기 때문에 페인팅이 잘 되는 것을 확인 할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7093b50f-27c6-42e4-b765-82fc21bd8b57)
