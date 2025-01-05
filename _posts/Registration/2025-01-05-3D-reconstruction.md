---
title: "[Registration] 3D reconstruction process"
last_modified_at: 2025-01-05
categories:
  - Registration
tags:
  - Registration
  - 정합
  - 3D reconstruction
  - COLMAP
  - SIFT
  - bundle adjustment
  - large-scale scene reconstruction
  - triangulate across images
  - sparse 3D point cloud
excerpt: "3D reconstruction pipeline: Extract 2D features, matche features across different images, jointly optimize a set of 3D points and camera poses to be consistent with these matches."
classes: wide
comments: true
---

> Reference

>[Block-NeRF: Scalable Large Scene Neural View Synthesis](https://openaccess.thecvf.com/content/CVPR2022/papers/Tancik_Block-NeRF_Scalable_Large_Scene_Neural_View_Synthesis_CVPR_2022_paper.pdf)

### 3D Reconstruction

3D reconstruction techniques have been developed and refined over decades, with modern methods relying on mature software like COLMAP.

- Most reconstruction methods follow a common pipeline:
  - Extract 2D image features (e.g., SIFT).
  - Match features across different images.
  - **jointly optimize a set of 3D points and camera poses to be consistent with these matches** (the wellexplored problem of bundle adjustment).

### Bundle Adjustement

Bundle Adjustment 설명
**Bundle Adjustment (BA)**는 3D 재구성과 컴퓨터 비전에서 사용되는 최적화 기법으로, **카메라의 자세(pose)와 3D 포인트를 동시에 조정**하여 이미지와의 일관성을 극대화합니다.

- 입력 데이터:
  - 여러 카메라에서 촬영된 이미지와 추출된 2D 특징점.
  - 초기 추정된 카메라 자세 및 3D 점.

- 목표:
  - 재투영 오류(Reprojection Error)를 최소화.
  - 재투영 오류란, 3D 점을 카메라 모델을 통해 2D 이미지로 투영했을 때 예상 위치와 실제 위치 간의 차이.

- 최적화 대상:
  - 카메라 파라미터 (위치, 방향, 내재 파라미터).
  - 3D 포인트 좌표.
 
- 작동 방식
  - 최적화 알고리즘(예: Levenberg-Marquardt)을 사용하여,
  - **카메라 모델과 3D 포인트가 주어진 이미지와 최대한 일치하도록 조정.**
  - 이 과정은 모든 이미지를 한꺼번에 고려하므로, "번들(Bundle)"이라는 용어가 사용됨.

- 특징
  - 고정밀도: 이미지 간의 일관성을 최대화하여 정밀한 3D 재구성 가능.
  - 연산 비용: 전체 이미지와 3D 포인트를 고려하므로 계산량이 많아 대규모 데이터에는 병렬화가 필요.

- 응용 분야
  - 3D 장면 재구성.
  - Structure-from-Motion(SfM) 및 SLAM.
  - 컴퓨터 그래픽스에서의 카메라 추적 및 보정.
  
쉽게 말해, **Bundle Adjustment는 여러 카메라 이미지가 관찰한 3D 구조를 가장 잘 설명하는 카메라 위치와 장면을 동시에 미세 조정하는 과정입니다.**


### Structure-from-Motion(SfM)과 같은 전통적인 3D 재구성 기법는 camera pose와 sparse 3D point cloud를 생성합니다.
- 특징:
  - 입력 이미지로부터 camera pose와 sparse 3D point cloud를 생성.
  - 2D 이미지 특징 추출(예: SIFT), 이미지 간 특징 매칭, bundle adjustment를 통해 수행.

- 한계:
  - 결과는 sparse 3D point cloud로, 완전한 3D 장면 모델을 위해 추가 처리 필요.
  - multi-view stereo algorithm(예: PMVS)으로 dense point cloud나 triangle mesh 생성.
  - 텍스처 부족이나 반사 표면으로 인해 artifacts 및 holes 발생.
  - 계산량이 많아 확장성과 처리 속도에 제약.

- 후처리:
  - photo-realistic 모델 시각화를 위해 추가적인 후처리 필요.
  - 주로 기하학적 정확성에 초점.
 
