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

### ✅ COLMAP은 기본적으로 Intrinsics를 공유해줘버린다 (Share intrinsics)
COLMAP은 같은 카메라로 찍은 여러 이미지가 같은 intrinsics를 공유하도록 할 수 있습니다.

이건 이미지가 동일한 camera_id를 갖는 경우 자동으로 적용됩니다.

즉, 한 번 측정된 intrinsics를 여러 이미지에 재사용할 수 있도록 지원합니다.

이 설정은 Database Management 툴을 통해 조정 가능합니다.

### ✅ COLMAP은 기본적으로 Intrinsics를 알아서 최적화해줘버린다 (Fix intrinsics)
COLMAP은 재구성 과정에서 intrinsic 파라미터(Principal point는 제외)를 자동으로 최적화합니다.

이미지 수가 충분하고, 여러 이미지가 같은 intrinsics를 공유할 때 COLMAP의 self-calibration이 꽤 정확합니다..

하지만 복잡한 distortion 모델의 경우, 자동 최적화가 퇴화된(degenerate) 파라미터로 수렴할 위험이 있습니다.

**그래서 이미 측정된 intrinsics가 있다면, refine_* 옵션을 통해 **intrinsics를 고정(fix)**할 수 있습니다.**

경로: Reconstruction > Reconstruction options > Bundle Adj. > refine_*

재구성 후 전체 번들조정(global bundle adjustment) 시에도 refine 여부를 지정할 수 있다.

### ✅ COLMAP은 기본적으로 Principal Point는 손대지 말아버린다 (Principal point refinement)
주점(principal point)은 일반적으로 추정이 까다롭기 때문에 COLMAP은 기본적으로 이를 고정시킵니다.

하지만 모든 이미지가 재구성된 이후에는 구조가 충분히 결정되므로, global bundle adjustment 단계에서 주점을 refine할 수 있습니다.

이때도 intrinsics를 여러 이미지가 공유하는 경우에 안정적으로 수행 가능합니다.

### 🔧 요약: "Extrinsics만 추정하고 싶을 때" 실질적인 설정 방법
Camera calibration이 미리 되어 있고, COLMAP에 정확한 intrinsics를 제공한다면:

COLMAP GUI 또는 CLI에서:

refine_focal_length, refine_principal_point, refine_extra_params 등을 False로 설정해 intrinsics를 고정.

이미지를 불러올 때 camera_id를 동일하게 하여 intrinsics 공유.

이후 COLMAP은 해당 intrinsics를 기반으로 extrinsics만 추정하게 됩니다.
