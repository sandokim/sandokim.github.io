---
title: "[BLENDER] Shader Fundamentals & Normal mapping"
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
excerpt: "Shader Fundamentals & Normal mapping"
use_math: true
classes: wide
---

- vector가 normalize된다는 것은 vector의 값의 크기가 1인 unit length를 가진다는 의미입니다.
- 두 점을 잇는 한 라인에는 2개 normal vector가 존재합니다.
 
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a7b1beb3-a17b-4a47-92ba-4b3504c78893)

- 우리는 이 normal vectors들로 physics calculations이 가능합니다.
  - 예를 들어, ball이 주어진 angle을 가진 surface에서 어떻게 bounce off 할 것인지
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/afd35de7-0cf8-43d7-bbf6-695948b3eab2)

  - 아니면 얼마나 빠르게 캐릭터가 slope를 slide down할 것인지
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fc4b6391-c1e5-4b78-9cfe-b9dcd3dd6f26)

### 이제 point를 2개에서 3개로 늘려서 dimension을 3차원으로 가져가 봅시다.

- 같은 직선 위에 2개의 점이 모두 위치하는 coilinear한 성분은 사라졌지만, coplanar 성질을 가지게 되므로 같은 평면에 3개의 점이 모두 위치하게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/253b4b17-0b27-4900-b96c-e4a1e90eaa80)

- 2D line과 마찬가지로, 3D plane도 평면인 surface에 수직(perpendicular)인 2개의 orthogonal vectors가 존재합니다. (=plane에 orthogonal한 vectors가 2개 존재함)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8fba7d4b-67f7-47ec-93a1-a584c049ace5)

- 이 orthogonal vectors는 plane 위에 있는 2개의 line들을 cross product해서 구하고 normalize하여 구합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b443f6ac-7bab-4fe5-9c3b-85578ba8d3ae)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1a54c172-bdfb-4d23-b212-ab2ab46288ae)

-  mesh normals은 사실 vertex마다 주어지게 됩니다. (위의 원리처럼 surface마다 주어지는 것이 아님)
-  즉, 하나의 triangle이 3개의 different normals을 가지게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/baea3767-5502-4c7f-a2e7-a6292d59145f)
- 그리고 3개의 different normals로부터 interpolate하여 다른 normals을 구합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/248eceab-0492-496b-968b-a6c088825652)
- 어떤 triangle들끼리는 vertices를 공유할 수 있도록 해주기 위해 이렇게 vertex에서 normal을 구해줍니다.
- 그럼 같은 vertex에서 normal을 중복하여 정의하는 것을 막을 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6313bac4-8f11-4d0e-855c-c45c61e7f4c1)

### 그렇지만 primary motivation 자체는 우리가 surface가 faceted한 것 대신 smooth할 수 있도록 illusion을 만들어주는 것이었습니다.
- Left (faceted one) / Right (Smooth one)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/041d6648-3c0a-4978-8227-2c5bf622bb9e)
- Shader code를 잠깐 살펴봅시다.
  - vertex Shader는 object space normal을 fragment Shader로 pass합니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6f35a3a2-9c4d-4a6b-9b05-d74e65818e26)
  - fragment Shader는 vector를 renormalize합니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3cb8d3f5-f88e-4de4-9381-32ed6702a295)
  - 그리고 XYZ values를 remap하여 -1 ~ 1의 값을 0 ~ 1값으로 바꿔주어, normals을 a nicer gamut(영역) of colors로 visualize하도록 해줍니다.
  - 다시말해 -1 ~ 1의 값을 가지는 3개의 normals을 RGB color의 색영역인 0 ~ 1 사이 값으로 매핑해주어 색을 표현합니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7572658d-4329-46a7-98ab-557ae377d30e)
  - 왼쪽은 triangle마다 존재하는 normals을 smoothing없이 표현한 sphere입니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e0a2182a-52ff-4a42-9b63-cfa99fa8fa63)
  - 오른쪽은 visually smoothing한데, 그 이유는 각 vertex에 있던 normal들은 neighboring triangles에 의해 average되고 각 triangle의 surface를 across하여 interpolated되었기 때문입니다.
    ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9d464a7f-b1fe-49e9-a453-73b6bd5e25d1)

### 이제 normals이 triangle의 vertex로부터 만들어지고, 나머지가 normals은 인접한 triangle들의 average와 interpolation을 통해 smoothing되어 만들어진다는 것까지 이해했습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/21e7a222-4091-4f99-8af9-abb08758772a)

### 그럼 이 normals이 어떻게 쓰이는지 알아봅시다.
- 가장 흔하게 normals이 쓰이는 경우는 lighting 입니다.
- 어떻게 우리가 normals로 objects를 비추는지 알기 위해 우리는 dot product을 알아야 합니다.
- dot product는 간단하게 설명하면, 2개의 vectors가 서로 얼마나 닮았는지를 계산합니다.
<div style="text-align: center;">
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/53c095b3-6eab-4f9f-aec1-5b17af833c65" alt="left image" style="display: inline-block; width: 30%; margin-right: 1%;" />
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/e3b73e07-c212-4dbd-ae0b-94827b3a9885" alt="middle image" style="display: inline-block; width: 30%; margin-right: 1%;" />
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/b1fbfc41-6368-4835-bcb3-aba2ec5deaea" alt="right image" style="display: inline-block; width: 30%;" />
</div>

#### incoming light와 surface normal이 주어졌을 때, 우리는 normal과 lighting direction이 얼마나 닮았는지를 질문해볼 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2f9667df-f32c-45b5-ad25-d06b9e4a3844)
- directional light source를 flip하여 생각봐야 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f1e3576c-a9c2-4b60-b4b6-d815fca1abdf)
- 즉, light가 shines하는 direction이 아니라
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/31b2057a-6d91-4892-9b27-a3b60f06749a)
- light source를 향하는 direction을 생각해야 합니다. (every sinlge point in the world에서)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d76be6ec-8069-4a9e-9eed-33021d4b7ca1)

### 우리는 dot product를 사용하여 surface normal (N)과 lighting direction (L)이 얼마나 닮았는지 계산해야 합니다.
- 만약 normal과 lighting direction이 같은 방향을 가리키고 있다면, point는 fully illuminated 됩니다.
  <div style="text-align: center;">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/5a9960fc-f9ce-4450-a64a-1da3a1eea783" alt="centered image" style="display: inline-block; width: 45%;" />
  </div>
- 만약 normal과 lighting direction이 서로 orthogonal 하다면, point는 fully unlit 됩니다.
  <div style="text-align: center;">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/65a05a1c-72bd-4e65-9335-57c82b757c92" alt="centered image" style="display: inline-block; width: 45%;" />
  </div>
- 일반적으로 negative light를 model하는 건 useful하지 않기 때문에, negative range는 무시되어 0으로 saturate 시킵니다.
  <div style="text-align: center;">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/9dc911cd-b2cc-4071-b67d-801b9c02b8ba" alt="image 1" style="display: inline-block; width: 45%; margin-right: 2%;" />
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/f9731fdd-33a1-40c7-b2e2-4e5e8c6ac4a6" alt="image 2" style="display: inline-block; width: 45%;" />
  </div>

### normals를 object space에 남겨져있고, lighting direction은 world space에 정의되어 있습니다. 따라서 object를 rotate해도 lighting이 incorrectly하게 계산됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fdbffffd-24f1-4d23-843c-766d77baf0eb)
- 이는 vertex Shader에서 object space normals을 world space로 transform하여 해결할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/cca27849-af2e-461e-8df7-3b08ce2aea99)
- 해결된 결과
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a73e9014-2473-41b7-8dcb-4ca77109c692)


### 이제 우리가 흔히 접하는 2D normal map이 정확히 뭔지 이해해봅시다.

***3D object의 surface normals이 texture로 captured된 것이 normal map입니다.***
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/2fa0ac46-0a8e-466d-ac2c-2d84ddca5e46)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/91724012-747b-4108-937f-cf11e03bb7d9)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b341aff0-4303-49be-b630-4ab50072283d)


### normals는 object space가 아니라 tangent space에 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1489bf4f-eeda-4311-b344-cdd8a6960b32)
- 따라서, world space의 lighting direction을 사용하려면, normals을 world space로 transform해야합니다.
- 이를 위해서는 normal과 다른 2개의 벡터인 Tangent와 Bitangent가 필요합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0977f8fe-0a3d-4b74-8cbe-4152fea839c9)
- 이 3개의 벡터들을 set으로 사용하여 tangent space coordinates에서 world space로 transform할 수 있습니다.
- 이 3개의 벡터들은 서로 orthogonal 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b48d4687-9387-4b9a-9204-cc4171ec7208)
- 하지만 이런 벡터들의 조합은 무한정으로 많이 존재합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ceb74117-2add-4a53-93ca-13da3f522065)
- 그리고 이들을 surface위에 interpolate를 smoothly하여 normals이 했던 것처럼 하려면 이들이 consistent하게 해줄 필요가 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e1016cbe-e079-4f0a-87fe-cbc409cff20d)
- 이를 위해 우리는 texture coordinates의 direction을 사용하여 Tangent와 Bitangent의 direction을 알려줘봅시다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c428985a-458b-4a2f-8ff6-6dbf7233922d)
- 이를 벡터들을 계산하는 것은 normals을 계산했던 것만큼 쉽지는 않지만, 3D modeling software나 당신이 선택한 engine은 계산하는 걸 가능하게 합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/033e45a8-eded-4aa0-b473-648f7c6c4e4c)
- Uniy에서는 tangent vector는 pre-calculated 되어있고, bitangent는 보통 vertex Shader에서 normal과 tangent의 cross product로 calculated됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4e60ed4a-e515-4cf2-894a-6257ce4df47d)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c0db9805-e438-41f7-8b2e-8dd244342782)
- 우리가 이렇게 unpacked normal과 3개의 tangent basis vectors (tangent space를 구성하는 3개의 벡터)를 얻었는데, 그래서 어떻게 사용할 건가요?
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f8fe0ff3-8f6a-4914-92e4-2ae4892c4c40)

### 가장 기본적인 케이스인 normal map을 살펴봅시다.

#### normal map
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4cb3f838-2223-432d-83de-9cafef3c8592)
- red와 green values는 0.5이고, blue value는 1.0입니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/06d4b681-5ec1-4a14-95b1-1d5c3f93632b)
- 이를 unpack할 시에 우리는 (0, 0, 1) vector를 얻게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6836b591-9f96-4f85-80cc-568dcaf7eeab)
- 우리는 이 value가 unperturbed surface normal로 return되기를 기대합니다.
- 따라서 당신은 unpacked normal의 z component를 surface normal에 곱하는 걸 생각해볼 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a4f51e40-8d4f-4dd0-b76a-0fbfe30f03c7)
- 이 경우, normal map의 blue channel은 얼만큼의 surface normal을 사용하고 싶은지를 encoding하고 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/3dfe06ce-920b-4bb9-9984-113c73b08e73)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6ca0c0ef-dff3-434c-b4f2-fa37339a7586)
- 비슷한 방식으로 red는 얼만큼의 tangent를 사용하는지, green은 얼만큼의 bitangent를 사용하는지를 encoding합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/da2ff780-1682-486f-b61d-f1b7cebdb78d)
- 그러므로 unpacked normal과 3개의 bases vectors (Tangent, Bitangent, Normal)이 주어졌을 때, 우리는 a world space normal을 각 vector를 그에 associateded된 color를 normal map으로부터 가져와 곱함으로써 얻을 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/dda5c3eb-a6da-4f15-91c2-b5ed56078d08)

### 만약 우리가 normal value를 lighting calculations에 사용하고, Unity에서 보면 x-axis가 flipped된 것처럼 보입니다.
- 우리가 normal map을 capture할때, z facing toward us, y facing up으로 했기 때문에, Unity에서는 X direction이 left를 가리키게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/25253c9f-7753-4610-b0c5-6309cbe201ac)
- 반면에 tangent values는 UV coordinates에 based로 encoded되어서 X direction이 right을 가리키게 됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9653ac73-d0f3-487a-85f0-c21d8e7b104c)
- 이를 해결하기 위해서는 normal texture에서 red channel을 invert합니다.
- Unity에서는 green light가 top, red light가 right hand side에 오도록 합니다.
- 아래 normal map을 참고하여, 당신이 구한 normal map의 direction이 맞는지 체크할 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7ca0a348-8aa8-4050-a740-9d5fc578a0e2)

### 우리는 이제 textrue를 받아 object를 shade하는 simpe Shader를 구했습니다. 
- normal map으로부터 informed되는 basic lighting으로 object를 shading하는 simple Shader를 구한 것입니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0c89b701-f27c-466a-8d6b-229dd5708390)

-----
## A normal is basically the vector or the direction that a face points in 3d space

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0b97870c-ffb9-4ec9-a67f-2736384b6c89)
- faces는 2개의 다른 normal directions을 가집니다.
- positive normal은 red / negative normal은 blue로 표현됩니다.
<div style="text-align: center;">
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/bad0f0e2-d518-445f-8a12-925314997335" alt="image 1" style="display: inline-block; width: 45%; margin-right: 2%;" />
    <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/47a37029-8175-44cc-9f39-04d8018fe798" alt="image 2" style="display: inline-block; width: 45%;" />
</div>
-

### 일반적으로 당신은 positive normals이 camera에 visible하기를 원합니다.
- 만약 당신의 object가 negative normals을 visible하게 가진다면 shading에서 안좋은 일이 일어납니다. (아래에서 오른쪽 같은 monkey가 됩니다.)
- 아래 그림은 동일한 material을 가졌지만, 오른쪽 monkey는 flipped normals을 가지고 있어서, blender software가 object의 lighthing과 shading을 계산할 때, 안에서 밖으로 계산하기 때문입니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/5577142a-2d56-45a6-9eea-950b191df33a)
- face의 normal의 direction은 그걸을 둘러싼 verts의 direction에 based하여 계산됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e243b1e3-d89b-4ab3-8550-b717e1e2a42a)
- blender는 handy(유용한) overlay mode가 있어서 normal의 direction을 보여줄 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/6b3f33ac-ac58-4e02-9b9d-59b4ddcc8761)
- 만약 이 모드에서 우리가 하나의 vert를 grab해서 움직이면, face는 이제 더 이상 flat하지 않습니다.
- normal direction도 따라서 영향을 받아 움직이는 것을 볼 수 있습니다. (verts의 direction에 따라 face normal도 바뀐다는 의미입니다.)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/636f06e5-0426-4668-bd82-5286491b866e)

### normal map은 기본적으로 우리가 face위의 있는 pixel의 normal의 directions을 바꿈으로써 object를 shading하는 것을 manipulate할 수 있도록 해줍니다.

- 우리는 3D program이 어떻게 object의 normal을 이해하는지 visualize 해볼 수 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/40718e4f-8b25-474e-859b-e678d23e78e6)
- 3D space에서 각 direction은 다른 color를 가집니다.
- front left는 blue, front light는 red, back left는 yellow, back right는 green, 
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d2a9bea4-8da2-45b1-8b5e-5e0a025170a9)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/011c8cf4-eda0-4cca-80f5-c451d7c19f28)
- face가 pointing up하면 lighter, face가 pointing down하면 darker 해집니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9f4330fa-bb1b-4da3-ad10-69e4a662f9f5)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/58ee7222-8f76-462a-88d2-45749e8933fb)

- **예를 들어, 왼쪽 아래 입의 색깔과, 왼쪽 위의 눈썹부분은 같은 분홍색을 가집니다. 이유는 그 영역의 face normal들이 같은 방향을 가리키고 있기 때문입니다.**
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/56de1b9d-9d1d-49ff-8bcb-735265668196)


### 이제 우리는 blender가 어떤 색깔로 다른 방향들을 정의하는지 알았습니다.
- Blender에 텍스처 맵(예: 노멀 맵)을 가져와서 오브젝트의 normals을 변경하고, 이를 통해 조명과 셰이딩에 영향을 줄 수 있습니다.
- 아래 object는 다양한 object shapes이 있는 normal map을 가집니다.
- 각기 다른 색깔들은 특정 direction들에 대응합니다.
- 예를 들어 각 object의 아래부분은 dark purple입니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/66bf1b5e-a3ee-4d6b-b70c-9624d8e80131)

- Blender가 원래는 단일 평면 노멀만 가진 이 표면의 픽셀들을, 우리가 제공한 노멀을 기반으로 셰이딩하는 것을 볼 수 있습니다
  - Blender는 기본적으로 평면 표면의 모든 픽셀에 동일한 노멀 벡터를 할당하여 셰이딩을 수행합니다.
  - 그러나 우리가 노멀 맵을 사용하여 각 픽셀마다 다른 노멀 벡터를 제공하면, Blender는 이러한 새로운 노멀 벡터를 기반으로 셰이딩을 수행하게 됩니다.
  - 이로 인해 표면의 디테일이 더 현실적으로 보이게 됩니다.
  - **즉, 원래는 단순한 평면으로 인식되던 표면이 노멀 맵 덕분에 더 복잡하고 정교한 셰이딩을 가지게 되는 것입니다.**
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/418cf3ca-aa16-4579-9d8c-733bab0c59de)

  - 표면이 더 이상 평평하지 않은 것처럼 보일 뿐만 아니라, 이 point lamp(광원)를 표면 위에서 움직이면 정확한 그림자까지 얻을 수 있습니다.
  - 노멀 맵을 사용하면 표면이 실제로 평평하지 않은 것처럼 보이게 만들 수 있습니다.
  - 또한, 포인트 램프(점광원)를 움직일 때, 노멀 맵 덕분에 표면의 디테일을 반영한 정확한 그림자가 생성됩니다.
  - 이는 표면의 미세한 디테일과 질감을 더욱 현실감 있게 표현하는 데 도움이 됩니다.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8a424a0c-22bd-4b64-8583-9aa5a19fcf49)

  - 이 표면을 거울 표면으로 만들거나 재질 설정을 통해 거울 효과를 적용하면, 실제로는 평평한 평면일지라도 정확한 반사 효과를 얻을 수 있고, 빛과 원숭이 머리가 반사되는 것을 볼 수 있습니다.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9b531a0a-08d3-4ee3-911a-b4d92f566170)

### 노멀 맵의 멋진 점은 훨씬 더 단순한 기하학 구조에 세밀한 디테일을 추가할 수 있다는 것입니다.
- **이 기술을 사용하여 매우 낮은 폴리곤 모델을 훨씬 더 디테일하고 고해상도로 조각된 것처럼 보이게 만들 수 있습니다.**
- 노멀 맵을 사용하면 단순한 기하학적 형태(저폴리곤 모델)에 세밀한 디테일을 추가할 수 있습니다.
- 이는 복잡한 고해상도 모델을 만들지 않고도 시각적으로 디테일한 표면 효과를 구현할 수 있음을 의미합니다.
- 이 기술은 low poly 모델을 마치 high-res로 조각된 것처럼 보이게 하여, 그래픽 성능을 유지하면서도 시각적 품질을 높이는 데 사용됩니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ed10a794-c1fa-4b49-a4be-b451628d5595)
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/7ca49350-1e7d-4311-965b-56622ed651eb)





### Reference
[Shader Fundamentals - Normal Mapping](https://www.youtube.com/watch?v=6_-NNKc4lrk)
[Change Your Understanding of Normals In 8 Minutes](https://www.youtube.com/watch?v=g57mNKE8IYc&t=37s)
[Deconstructing a Normal Map (CGC Weekly #18)](https://www.youtube.com/watch?v=oOOeV3IU2Yo)
