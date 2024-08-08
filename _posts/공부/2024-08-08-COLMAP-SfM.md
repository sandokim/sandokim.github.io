---
title: "[3D CV] COLMAP SfM"
last_modified_at: 2024-08-08
categories:
  - 공부
tags:
  - Multiple View Geometry
  - COLMAP
  - Structure from Motion
  - SfM
  - Structure-from-Motion Revisited
  - CVPR
excerpt: "COLMAP의 Structure from Motion (SfM) 알고리즘 설명"
use_math: true
classes: wide
comments: true
---

# COLMAP의 Structure from Motion (SfM)

SfM is the process of reconstructing 3D structure from its projections into a series of images taken from different viewpoints.

![image](https://github.com/user-attachments/assets/0a714d7a-c49b-4564-8994-80289922bff6)

-----

SfM은 먼저 다음 3가지를 순서대로 수행하고, scene graph를 Incremental Reconstruction에 넘겨줍니다.

# Correspondence Search
1. **feature extraction**
2. **matching**
3. **geometric verification**

-----

# Incremental Reconstruction

reconstruction stage에서
- **input**: scene graph
- **outputs**:
  - pose estimates: $\mathcal{P}=$ { $\mathbf{P}_c \in \mathbf{SE}(3) \mid c=1...N_P$ } for registered images.
  - the reconstructed scene structure as a set of points $\mathcal{X}=$ { $\mathbf{X}_k \in \mathbb{R}^3 \mid k=1...N_X$ }.

## Incremental Reconstruction의 workflow는 다음과 같습니다.
Correspondence Search의 결과물인 Scene Graph는 재구성 단계의 기초가 되며, 모델을 신중하게 선택된 두 개의 뷰 재구성으로 초기화한 후, 점진적으로 새로운 이미지를 등록하고, 씬 포인트를 삼각측량하고, 아웃라이어를 필터링하며, Bundle Adjustment (BA)을 사용하여 재구성을 정제합니다.

## Incremental Reconstruction의 단계를 설명하겠습니다. 

### Initialization, Image Registration, Triangulation, Bundle Adjustment

#### 1. **Initialization**: 모델을 신중하게 선택된 두 개의 뷰(이미지)를 사용하여 초기화합니다. 여기서 "초기화"란, 모델의 시작점을 설정하는 과정으로, **선택된 두 개의 이미지를 기반으로 초기 3D 구조를 만드는 것을 의미합니다.**

- SfM initializes the model with a carefully selected two-view reconstruction.
- Choosing a suitable initial pair is critical, since the reconstruction may never recover from a bad initialization.
- reconstruction performance의 robustness, accuracy, performance는 incremental process의 seed location에 의해 좌우됩니다.
- image graph에서 많은 overlapping cameras를 가지는 dense location에서 initailize를 하면, increased redundancy에 의해 더 robust하고 accurate한 reconstruction이 가능합니다.
- 반면에 카메라가 많이 겹치지 않는 sparser location에서 initialize를 하면, BAs에서 다뤄야할 points가 적어져서 sparser problems이 되므로 reconstruction process의 runtime이 짧아집니다.


#### 2. **Image Registration**: 기하학적 재구성으로 시작하여, 새로운 이미지는 이미 등록된 이미지의 삼각 측량된 점에 대한 특징 대응을 사용하여 Perspective-n-Point (PnP) 문제를 해결함으로써 현재 모델에 등록될 수 있습니다 (2D-3D 대응).  

- PnP 문제는 포즈 $\mathbf{P}_c $와, uncalibrated 카메라의 경우, 그 내부 매개변수를 추정하는 것을 포함합니다.
- 따라서 집합 $\mathcal{P} $는 **새로 등록된 이미지의 포즈** $\mathbf{P}_c $에 의해 확장됩니다.
- 2D-3D 대응이 종종 **outlier**로 오염되어 있기 때문에, calibrated 카메라의 포즈는 **일반적으로 RANSAC과 a minimal pose solver를 사용하여 추정**됩니다.
- uncalibrated 카메라의 경우, 다양한 minimal solvers 또는 sampling-based approaches가 존재합니다.
- 우리는 정확한 포즈 추정과 신뢰할 수 있는 삼각 측량을 위해 새로운 견고한 다음 최적 이미지 선택 방법을 Sec. 4.2에서 제안합니다.


#### 3. **Triangulation**: **새로운 이미지들이 등록될 때**, 3D 씬 포인트들이 삼각측량을 통해 계산됩니다. **삼각측량은 두 개 이상의 이미지를 사용하여 3D 공간의 점을 계산하는 기법입니다.**

- **새로 등록된 이미지는 기존의 장면 점들을 관찰해야 합니다.**
- 또한, 삼각 측량을 통해 점 집합 $\mathcal{X}$을 확장함으로써 scene coverage를 증가시킬 수도 있습니다.
- 새로운 장면 점 $\mathbf{X}_k$는 다른 시점에서 새로운 장면 부분을 커버하는 추가 이미지가 최소 하나 이상 등록되면 삼각 측량되어 $\mathcal{X}$에 추가될 수 있습니다.
- **삼각 측량은 중복성을 통해 기존 모델의 안정성을 증가**시키고 **추가적인 2D-3D 대응을 제공함으로써 새로운 이미지 등록을 가능하게 하기** 때문에 SfM에서 중요한 단계입니다.
- 다중 뷰 삼각 측량을 위한 다양한 방법들이 존재합니다.
- 이러한 방법들은 SfM에서 사용하기에는 제한된 robustness 또는 high computational cost의 문제를 겪고 있으며, 우리는 Sec. 4.3에서 강인하고 효율적인 삼각 측량 방법을 제안합니다.


#### Image registration과 triangulation은 별개의 절차이지만, 그 결과물은 매우 밀접하게 연관되어 있습니다. 

- 카메라 포즈의 불확실성은 triangulated 점들에 전파되고,
- 반대로 triangulated 점들의 불확실성도 카메라 포즈에 영향을 미치며,
- 추가적인 triangulations은 중복성을 증가시켜 초기 카메라 포즈를 개선할 수 있습니다.
- **further refinement가 없으면, SfM은 보통 빠르게 복구 불가능한 상태로 drift합니다.**
- **BA는 futher refinement를 해줍니다.**

#### 4. **Bundle Adjustment, BA**: 전체 재구성 결과를 최적화하여 정확도를 높이는 과정입니다. **BA는 여러 뷰로부터 얻어진 포인트들의 위치와 카메라 매개변수를 동시에 조정하여 오류를 최소화합니다.**

- BA는 카메라 파라미터(포즈) $\mathbf{P}_c$와 점 파라미터 $\mathbf{X}_k$를 동시에 non-linear refinement를 하여 reprojection error(재투영 오차)를 최소화하는 기법입니다.

$$
\mathit{E} = \Sigma_j \rho_j (||\pi(\mathrm{P}_c, \mathrm{X}_k - x_j||^2_2)
$$

- $\pi$: scene points를 image space로 project하는 function.
- $\rho_k$: outliers를 잠재적으로 down-weight 하기 위한 loss function.

-----

# Challenge
현재 최첨단 SfM 알고리즘은 대규모 Internet photo collections에서 다양한 복잡한 이미지 분포를 처리할 수 있지만, completeness와 robustness 측면에서 완전히 만족스러운 결과를 자주 내지 못합니다. 종종 시스템은 경험적으로 등록 가능해야 하는 많은 이미지를 등록하지 못하거나, mis-registrations 또는 drift로 인해 broken models을 생성합니다. 

### 첫 번째 원인은 correspondence search가 불완전한 scene graph를 생성하기 때문일 수 있습니다.
예를 들어, matching approximations로 인해 완전한 모델에 필요한 연결성을 제공하지 못하거나 신뢰할 수 있는 추정을 위한 충분한 redundancy를 제공하지 못합니다.

### 두 번째 원인은 reconstruction 단계에서 scene structure가 누락되거나 부정확해서 images를 register하지 못하는 경우일 수 있습니다.
Image registration과 triangulation은 상호보완적 관계에 있어,

- **images는 기존 scene structure에만 register될 수 있고**,
- **scene structure는 registered images에서만 triangulate될 수 있습니다.**

Incremental reconstruction 과정에서 각 단계에서 accuracy와 completeness를 극대화하는 것이 SfM의 주요 과제입니다. 

본 논문에서는 이 과제를 해결하고 현재 최첨단 기술에 비해 completeness, robustness, accuracy 측면에서 결과를 크게 개선하면서 효율성을 높였습니다 (Sec. 5).


### Reference
- [Structure-from-Motion Revisited](https://openaccess.thecvf.com/content_cvpr_2016/papers/Schonberger_Structure-From-Motion_Revisited_CVPR_2016_paper.pdf)
  - COLMAP의 SfM을 설명하는 CVPR paper
