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

SfM은 먼저 다음 3가지를 순서대로 수행하고, scene graph를 Incremental Reconstruction에 넘겨줍니다.

### Correspondence Search
1. **feature extraction**
2. **matching**
3. **geometric verification**


### Incremental Reconstruction

reconstruction stage에서
- input: scene graph
- outputs:
  - pose estimates: $\mathcal{P} = \{\bf{P}_c \in \bf{SE}(3) \ | \ c=1...N_P\}$ for registered images
  - the reconstructed scene structure as a set of points $\mathcal{X} = \{\bf{X}_k \in \mathbb{R}^3 \ | \ k=1...N_X\}$.
  
Correspondence Search의 결과물인 Scene Graph는 재구성 단계의 기초가 되며, 모델을 신중하게 선택된 두 개의 뷰 재구성으로 초기화한 후, 점진적으로 새로운 이미지를 등록하고, 씬 포인트를 삼각측량하고, 아웃라이어를 필터링하며, Bundle Adjustment (BA)을 사용하여 재구성을 정제합니다.

- **초기화 (Initializing)**: 모델을 신중하게 선택된 두 개의 뷰(이미지)를 사용하여 초기화합니다. 여기서 "초기화"란, 모델의 시작점을 설정하는 과정으로, **선택된 두 개의 이미지를 기반으로 초기 3D 구조를 만드는 것을 의미합니다.**
- **삼각측량 (Triangulating Scene Points)**: **새로운 이미지들이 등록될 때**, 3D 씬 포인트들이 삼각측량을 통해 계산됩니다. **삼각측량은 두 개 이상의 이미지를 사용하여 3D 공간의 점을 계산하는 기법입니다.**
- **아웃라이어 필터링 (Filtering Outliers)**: 부정확한 데이터 포인트(아웃라이어)를 제거하여 재구성의 정확도를 높입니다.
- **Bundle Adjustment, BA**: 전체 재구성 결과를 최적화하여 정확도를 높이는 과정입니다. **BA는 여러 뷰로부터 얻어진 포인트들의 위치와 카메라 매개변수를 동시에 조정하여 오류를 최소화합니다.**


### Reference
- [Structure-from-Motion Revisited](https://openaccess.thecvf.com/content_cvpr_2016/papers/Schonberger_Structure-From-Motion_Revisited_CVPR_2016_paper.pdf)
  - COLMAP의 SfM을 설명하는 CVPR paper
