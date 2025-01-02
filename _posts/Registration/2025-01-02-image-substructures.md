---
title: "[Registration] Image substructures"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - image substructures
excerpt: "Image substructures는 이미지 내에서 의미 있는 부분이나 영역을 나타내는 개념입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://openaccess.thecvf.com/content_cvpr_2018_workshops/papers/w9/DeTone_SuperPoint_Self-Supervised_Interest_CVPR_2018_paper.pdf)

Image substructures는 이미지 내에서 의미 있는 부분이나 영역을 나타내는 개념입니다. 

이는 다음과 같은 특징을 가집니다:
- 정의와 특징
  - 이미지의 일부분: 전체 이미지에서 특정한 의미나 특성을 가진 부분 영역을 말합니다.
  - 객체 또는 영역: 이미지 내의 개별 객체나 의미 있는 영역을 포함할 수 있습니다.
  - 픽셀 집합: 유사한 특성(색상, 강도, 텍스처 등)을 공유하는 픽셀들의 집합으로 구성됩니다.
- 응용 분야
  - 이미지 세그멘테이션: 이미지를 여러 의미 있는 부분으로 나누는 과정에서 중요한 역할을 합니다.
  - 객체 인식: 이미지 내의 특정 객체를 식별하고 분류하는 데 사용됩니다.
  - 컴퓨터 비전: 다양한 컴퓨터 비전 작업에서 이미지 substructures를 분석하고 활용합니다.
- 접근 방식
  - 전통적 방법: 히스토그램 분석, 엣지 검출 등의 기법을 사용합니다.
  - 머신러닝 기반: **딥러닝 모델을 사용하여 이미지 substructures를 자동으로 식별하고 분석합니다.**
  - 세그멘테이션 기법: 시맨틱 세그멘테이션, 인스턴스 세그멘테이션 등의 기법을 통해 이미지 substructures를 식별합니다.
