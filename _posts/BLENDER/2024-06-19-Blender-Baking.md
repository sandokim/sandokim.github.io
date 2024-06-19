---
title: "[BLENDER] Baking은 미리 계산된 텍스쳐 맵"
last_modified_at: 2024-06-19
categories:
  - BLENDER
tags:
  - BLENDER
  - 블렌더
  - bake
  - baking
  - baking normal map
  - normals
  - shadowmap
  - lightmap
  - Ambient Occlusion
excerpt: "Baking은 3D 그래픽스에서 복잡한 계산을 미리 수행하여 텍스처 맵에 저장하는 과정입니다."
use_math: true
classes: wide
---

# Bake (굽기)란 무엇인가?

**Baking**: 3D 그래픽스에서 복잡한 계산을 미리 수행하여 텍스처 맵에 저장하는 과정.

"Bake"라는 용어는 3D 그래픽스에서 일련의 계산 작업을 미리 수행하여 그 결과를 텍스처 맵에 저장하는 과정을 의미합니다. 이 과정을 굽기(baking)라고 부르는 이유는 오븐에서 재료를 구워서 완성품을 만드는 과정과 유사하기 때문입니다. 이를 통해 복잡한 계산을 미리 처리하여 최종 결과물을 빠르게 렌더링할 수 있게 됩니다.

## 왜 "굽기"라고 부르는가?

1. **미리 처리**: 마치 요리에서 재료를 미리 준비하고 굽는 것처럼, 3D 그래픽스에서는 복잡한 조명, 쉐이딩, 디테일 등의 계산을 미리 수행하여 저장합니다.
2. **최종 결과물**: 굽는 과정의 결과는 최종적으로 사용할 수 있는 텍스처 맵입니다. 이 텍스처는 즉각적으로 사용 가능하여 실시간 렌더링에서 성능을 향상시킵니다.
3. **유사한 개념**: 오븐에서 재료를 가공하여 먹을 수 있는 상태로 만드는 과정과, 3D 그래픽스에서 계산 결과를 적용하여 사용할 수 있는 상태로 만드는 과정이 유사합니다.

## 추가 예시

- **빛맵 굽기 (Lightmap Baking)**: 장면의 조명을 미리 계산하여 텍스처로 저장하는 과정.
- **섀도우 맵 굽기 (Shadowmap Baking)**: 그림자를 미리 계산하여 저장하는 과정.
- **AO 맵 굽기 (Ambient Occlusion Baking)**: 주변광 차폐를 미리 계산하여 텍스처로 저장하는 과정.
- **Normal Map 굽기 (Normal Map Baking)**: 고해상도 모델의 디테일을 저해상도 모델의 텍스처 맵에 저장하는 과정.

***"Baking" 과정은 모두 계산 결과를 미리 저장하여 실시간 렌더링의 효율성을 높이는 데 목적이 있습니다.***

 ### Bake는 precompute and store인 경우 사용하응 용어 입니다.
논문에서 "bake"라는 용어를 사용한 예시는 아래와 같습니다.

Our method “bakes” NeRF’s continuous neural volumetric scene representation into a discrete Sparse Neural Radiance Grid (SNeRG) for real-time rendering

 - [Baking Neural Radiance Fields for Real-Time View Synthesis](https://arxiv.org/abs/2103.14645)
 
