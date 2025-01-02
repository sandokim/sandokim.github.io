---
title: "[Registration] Superpoints"
last_modified_at: 2024-12-31
categories:
  - Registration
tags:
  - Registration
  - 정합
  - superpoints
  - downsample the input point clouds
  - Geotransformer
excerpt: "downsample the input point clouds into superpoints and then match them through examining whether their local neighborhood (patch) overlaps."
use_math: true
classes: wide
comments: true
---

> Reference

> [Geometric Transformer for Fast and Robust Point Cloud Registration](https://openaccess.thecvf.com/content/CVPR2022/papers/Qin_Geometric_Transformer_for_Fast_and_Robust_Point_Cloud_Registration_CVPR_2022_paper.pdf)

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://openaccess.thecvf.com/content_cvpr_2018_workshops/papers/w9/DeTone_SuperPoint_Self-Supervised_Interest_CVPR_2018_paper.pdf)

포인트 클라우드를 다운샘플링하고 슈퍼포인트로 변환하는 과정은 다음과 같습니다:

### 다운샘플링 (Downsampling)
다운샘플링은 포인트 클라우드의 공간 해상도를 줄이는 과정입니다.
- 원본 3D 표현을 유지하면서 포인트 수를 줄입니다.
- 데이터를 더 관리하기 쉬운 크기로 변환합니다.
- 저장 및 처리 요구 사항을 줄입니다.


### 슈퍼포인트 (Superpoints)
- 슈퍼포인트는 다음과 같은 특징을 가집니다:
- 유사한 기하학적 특성을 가진 포인트들을 그룹화한 것입니다.
- 포인트 클라우드의 중복성을 포착하고 처리 비용을 크게 줄입니다.
로컬 기하학적 구조가 유사한 포인트들을 효과적으로 그룹화합니다.

포인트 클라우드를 슈퍼포인트로 다운샘플링하는 것은 원본 데이터의 크기를 줄이면서도 중요한 기하학적 특성을 보존하는 효과적인 방법입니다. 이는 포인트 클라우드 등록 및 매칭과 같은 작업에서 계산 효율성을 높이고 처리 속도를 향상시킵니다.

### SuperPoint는 한 이미지에서 다른 이미지로 Homography H를 통해 변환한 image pair에서 correct matches를 가장 많이 찾았습니다.

SuperPoint tends to produce a larger number of correct matches which densely cover the image, and is especially effective against illumination changes.

![image](https://github.com/user-attachments/assets/db590a3f-2eb4-4edd-b68a-900784bdf577)

SIFT performs well for sub-pixel precision homographies $\epsilon = 1$ and has the lowest mean localization error (MLE). 

This is likely due to the fact that SIFT performs extra sub-pixel localization, while other methods do not perform this step.

### Repeatability가 높다고해서 최종 Homography 추정이 잘되는 것은 아닙니다.

**반복성(Repeatability)**는 이미지 간의 조건(조명, 각도 등)이 바뀌더라도 동일한 위치에서 특징점을 검출하는 능력을 의미합니다.

ORB는 이 반복성이 높아서 동일 위치에서 특징점을 잘 찾지만, 특징점의 배치가 고르게 분포되지 않고 특정 영역에 몰려 있어서 최종 호모그래피 추정 작업에서 낮은 점수를 기록합니다.

**ORB achieves the highest repeatability (Rep.); however, its detections tend to form sparse clusters throughout the image as shown in Figure 8, thus scoring poorly on the final homography estimation task. **

This suggests that **optimizing solely for repeatability does not result in better matching or estimation further up the pipeline.**

### SuperPoint는 descriptor-focused metrics인 nearest neighbor mAP (NN mAP)와 macthing score (M. score)에서 높은 점수를 기록했습니다.

**SuperPoint** scores strongly in descriptor-focused metrics such as nearest neighbor mAP (NN mAP) and matching score (M. Score), which confirms findings from both Choy et al. [3] and Yi et al. [32] which **show that learned representations for descriptor matching outperform hand-tuned representations.**
