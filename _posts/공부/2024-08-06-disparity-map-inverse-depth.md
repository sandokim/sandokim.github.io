---
title: "[3D CV] Disparity map, Inverse Depth map, Depth map"
last_modified_at: 2024-07-04
categories:
  - 공부
tags:
  - Multiple View Geometry
  - disparity map
  - inverse depth map
  - depth map
  - depth budget
  - disparity range
  - positive disparity
  - negative disparity
  - stereo matching
  - single-image depth estimation
  - metric depth estimation (MDE)
  - relative depth estimation (RED)
  - global disparity shift
  - scale invariance
  - shift invariance
  - disparity space
excerpt: "Depth map visualize는 inverse depth로 표현합니다."
use_math: true
classes: wide
comments: true
---

# Metric Depth Estimation (MDE) & Relative Depth Estimation (RDE)

**There are two branches of work: metric depth estimation (MDE) and relative depth estimation (RDE). The dominant branch is MDE, where the goal is to estimate depth in absolute physical units, i.e. meters.**

### Metric Depth Estimation (MDE)는 물리적인 단위 (i.e. meters)를 표현합니다.
- 실제 물리적인 거리인 metric depth를 측정하므로 robotics에서 mapping, planning, navigation, object recognition, 3D reconstruction, image editing등에 사용됩니다.
- 그러나, single metric depth estimation model을 다양한 datasets에 대해 학습시키면 성능이 떨어집니다.
- 특히, image collection에서 depth scale에서 큰차이를 보이는 indoor and outdoor images를 포함한 경우 MDE 성능이 떨어져 depth를 잘 추정하지 못합니다.
- 결과적으로 MDE models은 보통 특정한 datasets에 overfit하고 다른 datasets으로 generalize를 잘하지 못합니다.
- Metric depth models are typically trained on singular datasets, are more prone to overfitting, and typically generalize poorly to unseen environments or across varying depth ranges.

### Relative Depth Estimation (RDE)는 image frame 내에서 상대적인 depth만 표현합니다.
- RDE는 scale factor를 고려하지 않음으로써, 다양한 환경에서 depth scale variation이 크게 다른 것들을 고려할 수 있습니다.
- 따라서 RDE는 disparity만 supervision으로 주어져도 충분하며, scale과 camera parameters가 요구되지 않습니다.
- In RDE, depth predictions per pixel are only consistent relative to each other across image frames and the scale factor is unknown.
- Relative depth models tend to generalize better as they can be trained on more diverse datasets with relative depth annotations **using scale-invariant losses.**

# Metric Depth & Relative Depth 데이터셋 취득 방법

![image](https://github.com/user-attachments/assets/859a649c-e4c9-483a-8706-800e9d2fd540)

- **Metric Depth**: RGB-D, Laser, Synthetic, Laser/Stereo 로 데이터를 얻습니다.
- **Relative Depth**: SfM (No scale), Stereo (No scale & shift) 로 데이터를 얻습니다.

#### Depth Estimation 평가방법
- Relative depth를 gt로 갖는 datasets은 disparity space에서 root mean sqaure error를 측정합니다.
- accruate absolute depth를 gt로 갖는 datasetes은 depth space에서 mean absolute value of the realtive error (AbsRel)을 측정합니다.

$$
AbsRel = \frac{1}{M} \sum_{i=1}^{M} \frac{|z_i - z_i^{\*}|}{z_i^{\*}}
$$

- $M$: the number of pixels in an image with valid ground truth.
- $\theta$: the parameters of the prediction model.
- $d = d(\theta) \in \mathbb{R}^M$: a **disparity prediction.** --> depth가 아닌 disparity를 prediction하는 이유는 아래 수식 설명을 참고하세요.
- $d^{\*} \in \mathbb{R}^M$: the **corresponding ground-truth disparity.** --> depth가 아닌 disparity를 gt로 사용하는 이유는 아래 수식 설명을 참고하세요.
- $z_i = d_i^{-1}$ and $z_i^{\*} = (d_i^{\*})^{-1}$ are depths up to unknown scale. (unknown scale을 고려하지 않는 depths)

#### Training models for monocular depth estimation on diverse datasets presents a challenge because the ground truth comes in different forms (see Table 1).

아래와 같이 depth에 대한 ground truth 정보가 다릅니다.

- absolute depth (from laser-based measurements or stereo cameras with known calibration)
- depth up to an unknown scale (from SfM)
- disparity maps (from stereo cameras **with unknown calibration**)

The main requirement for a sensible training scheme is to carry out computations in an appropriate output space that is compatible with all ground-truth representations and is numerically well-behaved. We further need to design a loss function that is flexible enough to handle diverse sources of data while making optimal use of all available information.


#### depth의 ground truth의 form이 다른 것을 해결하려면 다음과 같은 3가지 major challenges를 풀어야합니다.
1. **Inherently different representations of depth**: direct vs. inverse depth representations.
2. **Scale ambiguity**: for some data sources, depth is only given up to an **unknown scale**.
3. **Shift ambiguity**: some datasets provide **disparity only up to an unkonwn scale** and global disparity shift that is a function of the unknown baseline and a horizontal shift of the principal points due to post-processing.

We propose to perform prediction in disparity space (inverse depth ***up to scale and shift***) together with a family of scale- and shift-invariant dense losses to handle the aforementioned ambiguities.
여기서 up to는 제외하고라는 의미로 사용되었습니다.

"우리는 스케일과 시프트 변환을 제외하고, disparity space(역수 깊이)에서 예측을 수행할 것을 제안합니다."

이는 예측 모델이 disparity space에서 작업을 수행하지만, 그 결과는 특정한 스케일(비율)과 시프트(이동)을 통해 조정될 수 있음을 의미합니다. 예를 들어, 예측된 disparity 값들은 일정한 상수 배율로 스케일 조정되거나, 일정한 값만큼 이동될 수 있지만, 그 외의 변환 없이 정확히 disparity space에서 예측이 이루어진다는 뜻입니다.

# ZoeDepth

- ZoeDepth는 MiDaS로 먼저 large datasets으로 Relative Depth Estimation을 학습하여, 일반화 성능을 얻고, 특정 데이터셋들에 대해 fine-tuning하여 Metric Depth Estimation을 학습하였습니다.
- 이로써, ZoeDepth를 사용하면 실제 물리적인 의미를 갖는 Metric Depth를 Estimation한 Depth map을 얻을 수 있습니다.


# Depth map은 Inverse Depth로 시각화합니다.

- depth map은 가까우면 0, 멀어지면 그 값이 inf까지 커질수 있습니다.
- inverse depth map은 가까우면 값이 크게, 멀어지면 값이 0이 되도록 표현합니다.

![image](https://github.com/user-attachments/assets/55ccbb24-c2fc-4741-92f1-7f22340ec691)


# Disparity와 Depth의 차이

### Disparity Map
- Disparity는 스테레오 비전에서 두 개의 카메라로 촬영한 이미지 사이의 동일한 물체의 위치 차이를 의미합니다.
- Disparity map은 이미지의 각 픽셀에 대해 disparity 값을 나타내는 맵입니다. 이는 픽셀 수준에서 두 이미지 간의 이동 차이를 반영합니다.

### Depth Map
- Depth는 카메라로부터 물체까지의 거리를 의미합니다.
- Depth map은 이미지의 각 픽셀에 대해 그 깊이 값을 나타내는 맵입니다. 이는 픽셀 수준에서 물체의 거리를 반영합니다.


Depth $Z$와 disparity $d$ 사이에는 역수 관계가 존재합니다. 

![image](https://github.com/user-attachments/assets/c4ad7034-9c39-4be7-9df7-32f8ea6fc96f)

기본적인 수식은 다음과 같습니다.

$$
Z = \frac{fB}{d}
$$

- $Z$는 depth (깊이)입니다.
- $f$는 focal length (카메라의 초점 거리)입니다.
- $B$는 baseline (두 카메라 간의 간격)입니다.
- $d$는 disparity (불일치)입니다.
  - $d = x-x'$ 입니다.

즉, disparity 값이 크면 물체는 카메라에 가깝고, disparity 값이 작으면 물체는 카메라에서 멀리 떨어져 있음을 의미합니다.

수학적으로 depth와 disparity는 **단순한 역수 관계는 아니지만**, depth가 disparity **역수에 비례합니다.** 구체적인 관계는 focal length와 baseline에 따라 달라집니다.

# Inverse Depth와 Disparity

stereo camera with unknown calibration data에서는 disparity만 구할 수 있습니다. 

inverse depth와 disparity $d$와의 수식은 다음과 같습니다. (inverse depth는 단순히 depth $Z$의 역수입니다.)

$$
inverse \ depth = \frac{1}{Z}  = \frac{d}{fB} = D
$$

- Depth $Z$의 역수인 **Inverse Depth $D$는 disparity에 비례**하게 됩니다.
- stereo camera로 획득된 dataset은 stereo rig의 calibration data가 없기 때문에, disparity(불일치)만 얻을 수 있습니다.
- disparity는 inverse depth (깊이의 역수)에 비례합니다.
- depth에서 inverse depth로 가는 것은 간단하게 역수를 취하면 되지만, disparity에서 depth로 가는 것은 $f$ focal length, $B$ baseline인 stereo camera에서 calibration data가 필요합니다.

원래 depth를 구하려면 disparity와 함께 calibration data인 focal length $f$, baseline $B$가 있어야 하지만, **unknown calibration 상황에서는 disparity만 얻을 수 있습니다.**

따라서 stereo camera with unknown calibration data에 대한 ground truth는 disparity만 존재하고, 딥러닝 모델 또한 disparity를 예측하는 모델로 설계하게 됩니다.

disparity gt는 다음과 같고, $f$, $B$에 대한 것은 실제로는 모르는 상황입니다.

$$
d_{gt} = \frac{D}{fB} = \frac{D}{f(x-x')}
$$

where unknown focal length $f$ and unknown baseline $B = (x-x')$.

***딥러닝 모델이 predict한 disparity*** $d_{pred}$ ***와 gt disparity*** $d_{gt}$ ***사이의 Loss를 구합니다.***

$$
Loss = d_{gt} - d_{pred}
$$

단, 이 상황에서는 $f$와 $B$를 모르기 때문에 정확한 depth를 구할 수 없습니다.
- focal length, $f$: 예측된 disparity가 정확한 깊이로 변환되기 위해서는 초점 거리가 필요하지만, 이 값이 정확히 알려지지 않은 경우 스케일 파라미터 $s$를 통해 보정합니다.
- baseline, $B$: 두 카메라 간의 물리적 거리로, 이 값 역시 정확히 알려지지 않은 경우 시프트 파라미터 $t$를 통해 보정합니다.

$$
Loss = d_{gt} - d_{pred} = \frac{D_{gt}}{f_{gt}B_{gt}} - \frac{D_{pred}}{f_{pred}B_{pred}} = 0
$$

$$
D_{gt} - (s \cdot D_{pred} + t)
$$

이를 모든 valid pixel $N$개에 대해 식을 쓰면 Metric Depth를 얻을 수 있습니다.

$$
Loss = \frac{1}{N}\sum_{i=1}|D_i-(s \cdot (\hat{D})_i + t)|
$$

여기서 $D$는 inverse depth입니다.


# Inverse Depth를 쓰는 이유

![image](https://github.com/user-attachments/assets/8315701b-aac6-4081-b89a-2a5932ba1d45)


# scale invaraince & shift invariance


### Depth 관련 상식
- dataset이 monocular cameras로 filmed 되었다면, ground-truth depth information이 존재할 수 없습니다.
- [MiDaS] The model shows a surprising capability to estimate plausible relative depth even on relatively abstract inputs. This seems to be true as long as **some (coarse) depth cues such as shading or vanishing points** are present in the artwork.
- [MiDaS] We identify common failure cases and biases of our model. Images have a natural bias where the lower parts of the image are closer to the camera than the higher image regions. This bias has also been learned by our network and can be observed in some extreme cases that are shown in the first row of Figure 9. In the example on the left, the model fails to recover the ground plane, likely because the input image was rotated by 90 degrees. In the right image, pellets at approximately the same distance to the camera are reconstructed closer to the camera in the lower part of the image.

  ![image](https://github.com/user-attachments/assets/6a1c3bb1-0198-47bb-94e6-2fd34efa7e50)

### Reference
- [ZoeDepth: Zero-shot Transfer by Combining Relative and Metric Depth](https://arxiv.org/abs/2302.12288)
- [Towards Robust Monocular Depth Estimation: Mixing Datasets for Zero-shot Cross-dataset Transfer, MiDaS model](https://arxiv.org/abs/1907.01341)
