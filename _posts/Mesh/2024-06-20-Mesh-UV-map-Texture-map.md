---
title: "[Mesh] UV map & Texture map (Albedo map, Normal map, Specular map, Displacement map, Ambient Occlusion map, Metalness map, Roughness map"
last_modified_at: 2024-06-20
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - 3D modeling
  - UV map
  - texture map
  - albedo map
  - normal map
  - specular map
  - displacement map
  - ambient occlusion map
  - metalness map
  - roughness map
excerpt: "UV map & Texture map (Albedo map, Normal map, Specular map, Displacement map, Ambient Occlusion map, Metalness map, Roughness map"
use_math: true
classes: wide
---

# 3D 모델링과 텍스처링에서 사용되는 다양한 맵들

## UV Map
**UV Map**: 3D 모델의 표면을 2D 평면으로 펼친 것입니다. 모델의 3D 좌표(X, Y, Z)를 2D 텍스처 좌표(U, V)로 변환하여 텍스처 이미지를 모델의 각 부분에 정확하게 맞출 수 있도록 합니다.

## Texture Maps
**Texture Maps**: 3D 모델의 표면에 적용되는 이미지 파일로, 모델의 외형을 더욱 현실적으로 보이게 합니다. 주요 텍스처 맵의 종류는 다음과 같습니다:

- **Albedo Map**: 모델의 기본 색상 정보를 포함한 이미지. 조명 효과를 배제한 순수한 색상을 제공합니다.
- **Normal Map**: 표면의 요철이나 세부 질감을 표현하는 이미지. 픽셀 단위로 표면의 방향 정보를 제공하여 입체감을 추가합니다.
- **Specular Map**: 표면의 반사 특성을 정의하는 이미지. 모델의 특정 부분이 얼마나 빛을 반사할지를 결정합니다.
- **Displacement Map**: 모델의 표면을 실제로 변형시키는 이미지. 텍스처 정보를 기반으로 모델의 지오메트리를 변형하여 더 높은 세부 사항을 제공합니다.
- **Ambient Occlusion (AO) Map**: 모델의 특정 부분에 그림자 효과를 추가하는 이미지. 직접 조명에 의한 그림자 외에도 주변 환경에 의해 발생하는 그림자를 시뮬레이션합니다.
- **Metalness Map**: 모델의 금속성을 정의하는 이미지. 금속 부분과 비금속 부분을 구분하여 금속의 반사 특성을 시뮬레이션합니다.
- **Roughness Map**: 표면의 거칠기 정도를 정의하는 이미지. 표면이 얼마나 매끄럽고 반사적인지 또는 거칠고 확산적인지를 결정합니다.

  ### 예시
  3D 모델이 자동차라면:
  - **Albedo Map**: 자동차의 기본 색상과 패턴을 포함합니다.
  - **Normal Map**: 자동차 표면의 세부 질감과 요철을 표현합니다.
  - **Specular Map**: 자동차의 광택과 반사 특성을 정의합니다.
  - **Displacement Map**: 타이어의 트레드 패턴이나 차체의 굴곡을 실제로 변형합니다.
  - **AO Map**: 자동차의 음영과 그림자를 더 깊고 현실감 있게 만듭니다.
  - **Metalness Map**: 자동차의 금속 부분과 비금속 부분을 구분합니다.
  - **Roughness Map**: 자동차 표면의 거칠기 정도를 정의합니다.






