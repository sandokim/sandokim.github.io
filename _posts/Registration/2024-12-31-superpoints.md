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

