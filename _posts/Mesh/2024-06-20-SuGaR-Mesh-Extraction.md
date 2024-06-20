---
title: "[Mesh] SuGaR coarse mesh & refined mesh extraction"
last_modified_at: 2024-06-20
categories:
  - Mesh
tags:
  - Mesh
  - 메시
  - Mesh extraction
  - SuGaR
  - coarse mesh
  - refined mesh
excerpt: "SuGaR에서 coarse mesh와 refined mesh를 extraction하는 방법"
use_math: true
classes: wide
---

# Coarse Mesh Extraction (`coarse_mesh.py`)와 Refined Mesh Extraction (`refined_mesh.py`)의 차이점

두 스크립트는 Gaussian Splatting 기반의 3D 재구성 시스템에서 메시를 추출하는 데 사용됩니다. 그러나 각 스크립트는 다르게 접근하며, 각각의 목적과 세부 작업이 다릅니다. 아래에서 주요 차이점과 핵심 요소를 설명합니다.

## 주요 차이점

1. **모델의 단계**:
   - **Coarse Mesh Extraction (`coarse_mesh.py`)**: 이 스크립트는 초기 모델(Coarse Model)을 사용하여 기본적인 3D 메시를 추출합니다. 주로 저해상도의 초기 재구성을 위해 사용됩니다.
   - **Refined Mesh Extraction (`refined_mesh.py`)**: 이 스크립트는 정제된 모델(Refined Model)을 사용하여 고해상도의 세밀한 3D 메시와 텍스처를 추출합니다. 주로 최종적인 세밀한 재구성을 위해 사용됩니다.

2. **텍스처 처리**:
   - **Coarse Mesh Extraction**: 텍스처에 대한 처리를 명시적으로 다루지 않습니다.
   - **Refined Mesh Extraction**: ***텍스처를 추출하고 이를 메시에 적용합니다. 텍스처 맵과 UV 맵을 생성하여 보다 현실감 있는 3D 모델을 만듭니다.***

3. **후처리 과정**:
   - **Coarse Mesh Extraction**: 메시를 추출한 후, 저해상도의 메시에 대해 다양한 정리 작업(예: 저밀도 버텍스 제거, 메시 정리)을 수행합니다.
   - **Refined Mesh Extraction**: 메시의 후처리를 통해 경계면의 저밀도 가우시안을 제거하고 메시를 더 깔끔하게 만듭니다.

## 핵심 요소 요약

### `coarse_mesh.py`

1. **모델 및 데이터 로드**:
   - Gaussian Splatting Wrapper와 초기 Coarse Model을 로드합니다.

2. **가우시안 필터링 및 메시 생성**:
   - 저해상도의 가우시안 포인트 클라우드에서 삼각형 메시를 생성합니다.
   - 저밀도 가우시안을 제거하여 메시를 정리합니다.

3. **메시 정리 및 저장**:
   - Poisson surface reconstruction을 통해 포인트 클라우드로부터 삼각형 메시를 생성합니다.
   - 메시를 정리하고, 디시메이션(decimation)을 통해 간소화한 후 저장합니다.

### `refined_mesh.py`

1. **모델 및 데이터 로드**:
   - refined model을 로드합니다.
   - 이전에 추출된 Coarse Mesh를 로드합니다.

2. **텍스처 추출**:
   - refined model에서 텍스처와 UV 맵을 추출합니다.
   - 텍스처 이미지를 생성하고 이를 메시에 적용합니다.

3. **메시 후처리 및 저장**:
   - 경계면의 저밀도 가우시안을 제거하여 메시를 정리합니다.
   - 텍스처가 적용된 메시를 저장합니다.

## 종합 비교

- **Coarse Mesh Extraction**:
  - 목표: 초기 3D 메시를 생성하고, 저밀도 가우시안을 제거하여 정리된 저해상도 메시를 얻는 것.
  - 특징: 포인트 클라우드와 Poisson Surface Reconstruction을 사용.

- **Refined Mesh Extraction**:
  - 목표: 고해상도의 세밀한 3D 메시와 텍스처를 추출하여 최종 모델을 생성하는 것.
  - 특징: ***텍스처 맵과 UV 맵을 생성하여 보다 현실감 있는 모델을 구성.***

이 두 스크립트는 3D 모델링의 서로 다른 단계에서 사용되며, 각각의 스크립트는 해당 단계의 목표에 맞추어 최적화되어 있습니다. `coarse_mesh.py`는 초기 재구성 단계에서 사용되고, `refined_mesh.py`는 최종 단계의 세밀한 재구성 및 텍스처링에 사용됩니다.

------

## 코드 분석

### 기본 logging 툴 (CONSOLE)

**CONSOLE은 rich 라이브러리에서 제공하는 Console 객체로, 터미널에서 더 나은 출력을 제공하기 위해 사용됩니다.** rich 라이브러리는 터미널에서 컬러 텍스트, 표, 트리, 로깅 등을 쉽게 출력할 수 있는 기능을 제공합니다.

- CONSOLE 객체는 스크립트 실행 중 주요 정보를 콘솔에 출력하고, 텍스트를 컬러로 출력하거나 특정 너비를 가지는 형식으로 출력할 수 있게 합니다. 예를 들어, Console(width=120)는 출력 너비를 120 글자로 설정하여 가독성을 높입니다. 이를 통해 모델 로드 상태, 메시 생성 상태, 텍스처 추출 상태 등을 사용자에게 알립니다.

  ```python
  # coarse_sdf.py
  from rich.console import Console
  
  def coarse_training_with_sdf_regularization(args):
      CONSOLE = Console(width=120)

      ...
  
      CONSOLE.print("-----Parsed parameters-----")
      CONSOLE.print("Source path:", source_path)
      CONSOLE.print("   > Content:", len(os.listdir(source_path)))
      CONSOLE.print("Gaussian Splatting checkpoint path:", gs_checkpoint_path)
      CONSOLE.print("   > Content:", len(os.listdir(gs_checkpoint_path)))
      CONSOLE.print("SUGAR checkpoint path:", sugar_checkpoint_path)
      CONSOLE.print("Iteration to load:", iteration_to_load)
      CONSOLE.print("Output directory:", args.output_dir)
      CONSOLE.print("SDF estimation factor:", sdf_estimation_factor)
      CONSOLE.print("SDF better normal factor:", sdf_better_normal_factor)
      CONSOLE.print("Eval split:", use_eval_split)
      CONSOLE.print("White background:", use_white_background)
      CONSOLE.print("---------------------------")

      ...
  ```
#### 출력 결과
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/67447a0c-a5ab-4852-bae0-294f54aceb87)








