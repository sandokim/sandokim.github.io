![bandicam 2024-06-19 18-30-58-676](https://github.com/sandokim/sandokim.github.io/assets/74639652/af395f0f-6549-4bb8-822d-e8a324701539)---
title: "[BLENDER] Blender Flat Shading, Phong Shading & Normals"
last_modified_at: 2024-06-19
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - flat shading
  - phong shading
  - normals
excerpt: "Blender Phong Shading & Normals"
use_math: true
classes: wide
---

#### phong shading이랑 thong shading이랑 헷갈리면 안됩니다.

### Phong Shading이란?

Phong Shading: Normals are interpolated across the polygon --> normals이 polygon 전체에 걸쳐 보간됩니다.

Phong Shading을 Normal Interpolation Shading이라고 말할 수 있습니다.


### Phong shading을 

- flat shading의 문제는 객체를 구성하는 개별 폴리곤이 쉽게 보일 수 있다는 것입니다. 부드럽게 보이더라도 말이죠.
![bandicam 2024-06-19 18-14-29-701](https://github.com/sandokim/sandokim.github.io/assets/74639652/4da36fd1-aa78-45d1-8e46-fb360f473860)

- 우리는 메쉬에 더 많은 geometry를 추가할 수 있지만 이는 작업하기 어렵고 성능 및 렌더링 시간에 영향을 미치며 카메라가 충분히 가까이 접근하면 여전히 개별 면을 볼 수 있습니다. 
![bandicam 2024-06-19 18-14-29-701](https://github.com/sandokim/sandokim.github.io/assets/74639652/56747e95-7363-4d5a-a9c6-6fb6d9712041)

- 이를 해결하기 위해 Phong shading을 사용할 수 있습니다. Phong shading에서는 각 면이 개별 픽셀로 나뉘고 각 면의 노멀이 픽셀 전체에 걸쳐 보간됩니다. 이는 각 면의 노멀 방향이 주변 모든 면의 방향을 기준으로 평균화된다는 것을 의미합니다.
- 이로 인해 갑작스러운 노멀 방향의 변화 없이 부드러운 그라데이션이 생깁니다. 
![bandicam 2024-06-19 18-16-27-572](https://github.com/sandokim/sandokim.github.io/assets/74639652/f484bc98-fd87-486f-9d21-4d270d4ae7bb)

- Phong shading은 subdivision surface modifier와 비슷하게 생각할 수 있지만 ***실제 geometry를 부드럽게 하는 대신 shading을 부드럽게 합니다.*** 
![bandicam 2024-06-19 18-17-23-300](https://github.com/sandokim/sandokim.github.io/assets/74639652/70fd6020-e01a-4706-8638-66fcb88936b8)

- **Phong shading은 대부분의 3D 패키지에서 smooth shading 또는 유사한 이름으로 불리며** 몇 가지 제한 사항이 있습니다. 인접한 면의 방향이 매우 유사한 경우에 잘 작동합니다.
![bandicam 2024-06-19 18-19-01-164](https://github.com/sandokim/sandokim.github.io/assets/74639652/ec96e3b2-b665-4f34-96a4-56d26481e780)

- 예를 들어, 인접한 면의 방향이 매우 유사한 구의 경우에는 Phong Shading (Blender에서는 Shade Smooth로 불림)은 효과가 좋습니다.
![bandicam 2024-06-19 18-19-39-671](https://github.com/sandokim/sandokim.github.io/assets/74639652/5587772e-1d52-4403-80e4-1842f246cf68)
- 아래와 같이 구에 Phong Shading을 적용했을 때, 실제 geometery의 변화를 주는 것이 아니라 shading을 부드럽게 해줍니다.
![bandicam 2024-06-19 18-21-00-034](https://github.com/sandokim/sandokim.github.io/assets/74639652/a28c6180-bb82-45cd-a8c5-16ff3afbae33)

- 하지만 인접한 면이 유사하지 않은 복잡한 객체에 smooth shading (=phong shading)을 적용할 때는 문제점이 생깁니다.
- 예를 들어 이 실린더에 smooth shading을 적용하면
![bandicam 2024-06-19 18-23-14-310](https://github.com/sandokim/sandokim.github.io/assets/74639652/7151f5a1-22ee-40d3-b92b-dfba2d204cd4)
- 위아래에 물결 모양의 그림자가 생깁니다. 
![bandicam 2024-06-19 18-23-33-627](https://github.com/sandokim/sandokim.github.io/assets/74639652/29097f7d-2e0b-4a6d-b447-e1660ccda14a)
- 이는 Blender가 측면의 면과 위아래 면의 노멀 방향을 평균화하려 하기 때문입니다.
![bandicam 2024-06-19 18-23-53-319](https://github.com/sandokim/sandokim.github.io/assets/74639652/4fd7d9d1-84d9-4f50-9d14-65688ea0e88b)
- Blender는 이들이 연결되어 있으므로 노멀에 영향을 주어야 한다고 생각합니다.
![bandicam 2024-06-19 18-24-08-686](https://github.com/sandokim/sandokim.github.io/assets/74639652/7119d2d1-5342-4060-abf6-098be9aea5f5)
- 그러나 우리는 그것이 발생하는 것을 원하지 않습니다. 이 문제를 해결하기 위해 대부분의 프로그램에는 auto smooth 기능이 있습니다.
![bandicam 2024-06-19 18-24-47-368](https://github.com/sandokim/sandokim.github.io/assets/74639652/28d4c1e6-8f28-4ba9-82d9-632333e7f79e)
- auto smooth 기능은 smooth shading을 적용하는 방법에 제한을 설정합니다. 
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c2464a05-58d7-43a7-b63f-2bb7d5d3e42b)
- Blender의 기본 auto smooth 설정은 35도입니다. 이는 이웃한 면과의 각도가 35도 미만인 면에만 smooth가 적용된다는 것을 의미합니다. 각도가 35도보다 크면 smoothing 알고리즘에 의해 무시되고 날카로운 가장자리를 얻게 됩니다.
- 아래와 같이 auto smooth의 설정을 4.6도까지 줄이면, 4.6도보다 큰 각도에 대해서는 smoothing 알고리즘에 의해 무시되고  날카로운 가장자리를 얻게 됩니다.
![bandicam 2024-06-19 18-28-48-106](https://github.com/sandokim/sandokim.github.io/assets/74639652/e4badd2e-0cfe-4822-b765-b730e17bd1e5)
- 대부분의 프로그램에는 Phong shading의 분포를 제어하는 더 복잡한 방법도 있습니다.
  - 예를 들어, Blender에는 사용자가 설정한 다양한 기준에 따라 더 적극적으로 면을 부드럽게 하는 weighted normal modifier가 있습니다.
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/82e1a657-2d7b-4375-958b-5a6f5c17f7fc)
  - weighted normal modifier를 적용하고 Weight의 값에 따른 결과입니다.
  <div style="display: flex; justify-content: space-between;">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/16d62c5c-70ef-49d3-9c88-8060ef166a5a" alt="Image 1" width="30%">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/516ab236-35a5-40cf-a28d-345cc10757ee" alt="Image 2" width="30%">
      <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/ae8d0543-35ad-47d5-9be5-6ed704adcaef" alt="Image 3" width="30%">
  </div>





### Reference
[Change Your Understanding of Normals In Eight Minutes](https://www.youtube.com/watch?v=g57mNKE8IYc&t=37s)
