---
title: "[COLMAP] COLMAP with known intrinsics"
last_modified_at: 2025-06-03
categories:
  - 공부
tags:
  - COLMAP
  - Poses
  - Camera Extrinsics
  - Camera Intrinsics
  - 카메라 외부 파라미터
  - 카메라 내부 파라미터
excerpt: "COLMAP with known intrinsics"
use_math: true
classes: wide
comments: true
---

> [Using COLMAP to find the camera extrinsics in the case that the intrinsics are known #682](https://github.com/colmap/colmap/issues/682)
>
> [How to use pre-constructed DB for reconstruction from known poses #433](https://github.com/colmap/colmap/issues/433)
> 
> [Question: How to format cameras.txt for Reconstruct sparse/dense model from known camera poses #428](https://github.com/colmap/colmap/issues/428)

### COLMAP에서 카메라의 Intrinsics를 알 때, Extrinsics만 추정하는 법을 알아봅시다.

[COLMAP FAQ: Share intrinsics, Fix intrinsics, Principal point refinement](https://colmap.github.io/faq.html#faq-share-intrinsics)

### ✅ COLMAP은 기본적으로 Intrinsics를 공유할 수 있습니다 (Share intrinsics)
COLMAP은 같은 카메라로 찍은 여러 이미지가 같은 intrinsics를 공유하도록 할 수 있습니다.

이건 이미지가 동일한 camera_id를 갖는 경우 자동으로 적용됩니다.

즉, 한 번 측정된 intrinsics를 여러 이미지에 재사용할 수 있도록 지원합니다.

이 설정은 Database Management 툴을 통해 조정 가능합니다.

### ✅ COLMAP은 기본적으로 Intrinsics를 알아서 최적화하지만, Intrinsics를 최적화 과정에서 제외할 수 있습니다. (Fix intrinsics)
COLMAP은 재구성 과정에서 intrinsic 파라미터(Principal point는 제외)를 자동으로 최적화합니다.

이미지 수가 충분하고, 여러 이미지가 같은 intrinsics를 공유할 때 COLMAP의 self-calibration이 꽤 정확합니다.

하지만 복잡한 distortion 모델의 경우, 자동 최적화가 퇴화된(degenerate) 파라미터로 수렴할 위험이 있습니다.

그래서 이미 측정된 intrinsics가 있다면, refine_* 옵션을 통해 intrinsics를 고정(fix)할 수 있습니다.

경로: Reconstruction > Reconstruction options > Bundle Adj. > refine_*

재구성 후 전체 번들조정(global bundle adjustment) 시에도 refine 여부를 지정할 수 있다.

### ✅ COLMAP은 기본적으로 Principal Point는 최적화하지 않습니다. (Principal point refinement)
주점(principal point)은 일반적으로 추정이 까다롭기 때문에 COLMAP은 기본적으로 이를 고정시킵니다.

<div style="text-align: center;">
  <img src="https://github.com/user-attachments/assets/aaa4c20c-d6ac-4c22-8356-fa502359906f" width="450" alt="Reconstruction > Reconstruction options > Bundle > Camera parameters" />
  <p><em>Reconstruction > Reconstruction options > Bundle > Camera parameters</em></p>
</div>

하지만 모든 이미지가 재구성된 이후에는 구조가 충분히 결정되므로, global bundle adjustment 단계에서 주점을 refine할 수 있습니다.

이때도 intrinsics를 여러 이미지가 공유하는 경우에 안정적으로 수행 가능합니다.

### 🔧 요약: "Extrinsics만 추정하고 싶을 때" 실질적인 설정 방법
Camera calibration이 미리 되어 있고, COLMAP에 정확한 intrinsics를 제공한다면:

COLMAP GUI 또는 CLI에서:

`refine_focal_length, refine_principal_point, refine_extra_params` 등을 False로 설정해 intrinsics를 고정.

이미지를 불러올 때 camera_id를 동일하게 하여 intrinsics 공유.

이후 COLMAP은 해당 intrinsics를 기반으로 extrinsics만 추정하게 됩니다.

### 📐 COLMAP의 Reference Frame과 월드좌표계
COLMAP을 사용해 Structure-from-Motion 재구성을 수행하면, 모든 카메라 포즈와 3D 포인트는 하나의 좌표계 기준에서 정의됩니다. 이 좌표계가 어떻게 정해지고, 어떤 의미를 가지며, 어떻게 다룰 수 있는지를 이해하는 것은 COLMAP 결과를 제대로 활용하기 위한 필수 조건입니다.

#### ✅ Reference Frame은 어떻게 정해지는가?
_"The reference frame is initialized to correspond to the first camera that is reconstructed."_

COLMAP은 재구성 과정에서 처음으로 성공적으로 모델에 등록된 이미지를 기준으로 reference frame(기준 좌표계)을 초기화합니다. 이 이미지는 사용자 파일 목록의 첫 이미지가 아니라, 실제로 feature matching과 triangulation 조건을 만족해 최초로 구조화된 이미지입니다.

이 이미지의 카메라 좌표계가 재구성 초기에는 월드 좌표계의 기준으로 사용됩니다. 즉, 해당 카메라는 원점 (0, 0, 0)에 위치하고, 회전은 단위행렬이 됩니다.

📌 어떤 이미지가 초기 기준이 되었는지는 images.txt 파일의 가장 첫 번째 이미지 정보 줄을 보면 됩니다.

#### 🔁 그러나 Reference Frame은 고정되어 있지 않다
중요한 점은, 이 초기 기준 이미지가 최종적으로도 월드 좌표계의 중심으로 남는다는 보장은 없다는 것입니다.

COLMAP은 reconstruction이 끝난 후, Bundle Adjustment(BA)를 통해 전체 모델의 최적화를 수행합니다. 이 과정에서 reconstruction 전체가 회전되거나 이동될 수 있으며, 이에 따라 기준 이미지의 위치도 변할 수 있습니다.

즉,

COLMAP의 reference frame은 초기 좌표계 설정을 위한 임시 기준일 뿐,

최종 결과에서의 world coordinate system은 정확히 어떤 이미지 기준이라고 단정 지을 수 없습니다.

다시 말해, 초기에는 특정 이미지가 기준이 되더라도, 최종적으로는 전체 모델이 조정된 하나의 일관된 좌표계 안에 위치하게 됩니다. 이 좌표계는 절대적인 의미의 “월드”가 아니라, 내부적으로 정렬된 상대적인 월드 프레임입니다.

#### 🧭 원하는 기준 좌표계로 바꾸고 싶다면?
COLMAP이 정의한 좌표계는 상대적이므로, 다음과 같은 후처리를 통해 의미 있는 좌표계로 재정렬해야 할 수 있습니다:

특정 카메라(예: cam1)를 원점에 두고 Z축이 위를 향하도록 전체 reconstruction을 rigid transform으로 변환

또는 외부 좌표계(예: GPS, 로봇 로컬 프레임 등)와 대응점 기반으로 정합

### 📐 COLMAP에서 카메라 포즈(pose)와 위치(position)의 정의
_"The pose of each image is defined with respect to a world coordinate system and the position of the image in the world coordinate system is given as -Rᵗ * T."_

이 문장에서 중요한 점은 pose와 position을 구분하는 것입니다.

- Pose: 월드 좌표계로부터 카메라 좌표계로 가는 변환(R, T)을 의미합니다. 즉, 카메라가 월드 공간 안에서 어떤 위치에 있고, 어떤 방향을 바라보는지를 함께 포함합니다.
- Position: 그중에서도 카메라 중심점의 좌표만을 의미합니다. 회전(R)은 고려하지 않고, 카메라의 월드 기준 위치를 나타냅니다.

#### 📌 좌표계 간 변환 수식
- 𝑋: 월드 좌표계에서의 3D 포인트
- 𝑥: 카메라(로컬) 좌표계에서의 3D 포인트
- 𝑅: 월드 → 카메라 변환 회전 행렬
- 𝑇: 월드 원점이 카메라 좌표계에서 보이는 위치 (translation vector)

COLMAP에서는 다음과 같은 카메라 중심 기준 모델을 사용합니다:

$$
x = R \cdot X + T
$$

즉, 월드 좌표계의 포인트 𝑋는 회전과 이동을 거쳐 카메라 좌표계의 𝑥로 변환됩니다.

이를 월드 좌표계 기준으로 풀어쓰면 다음과 같이 정리됩니다:

$$
X =R^t(x-T)
$$

📌 카메라의 위치(Position) 계산
카메라의 위치는, 월드 좌표계에서 카메라 원점이 어디에 있는가를 뜻하며, 위 수식에서 𝑥=0인 경우로 계산됩니다.

즉,

$$
Camera \ Position \ C=−R^t \cdot T 
$$

이는 COLMAP의 `images.txt`에서 주어진 R (쿼터니언 → 회전행렬로 변환)과 T를 통해 직접 계산할 수 있습니다.


