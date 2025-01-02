---
title: "[Registration] Rule based feature matching vs Deep Learning based feature matching"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - local feature descriptor
  - SIFT
  - SURF
  - FAST
  - BRIEF
  - ORB
excerpt: "이러한 규칙 기반의 특징 점 매칭은 결국 특징점의 디스크립터간의 관계를 파악하는 구조에서 그칩니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [특징 추출을 이용한 이미지 매칭 기술의 최근 동향 분석](https://ksbe-jbe.org/xml/37415/37415.pdf)

> [Brief: Binary robust independent elementary features](https://www.cs.ubc.ca/~lowe/525/papers/calonder_eccv10.pdf)

> [ORB: An efficient alternative to SIFT or SURF](https://ieeexplore.ieee.org/document/6126544)

### 규칙 기반 특징점 매칭

특징점 검출은 과정 이후 찾은 특징점을 벡터로 저장하거나 표현하기 위해서 디스크립터를 생성하고, 

이 디스크립터를 사용하여 특징점 매칭을 진행합니다. 

SIFT를 기반(SIFT, SURF)으로하는 이미지 매칭 기법은 디스크립터 측정 후, 측정된 디스크립터 사이의 유클리드 거리를 계산하여 가장 가까운 두 특징점을 매칭합니다. 

SIFT의 디스크립터는 주변 영역을 16개의 구간으로 나누어 각 구간의 그래디언트를 집계하여값을 생성하기 때문에 **추출된 디스크립터의 크기는 128차원의 벡터**이므로 연산량이 크다는 단점이 있다. 

이를 보완하기 위해 Binary Robust Independent Elementary Features(BRIEF)[5] 알고리즘이 고안되었습니다. 

BRIEF는 이미지의 디스크립터를 이진 특징 벡터로 기술하여 계산이 필요한 차원의 수를 크게 줄이는 알고리즘으로 SIFT 보다 빠른 연산이 가능하게 했습니다. 

Oriented FAST and Rotated BRIEF(ORB)는 특징점 추출 과정에 FAST 알고리즘을 사용하고 특징점 매칭 과정에 BRIEF 알고리즘을 사용하여 SIFT 보다 개선된 수행시간을 보였습니다. 

BRIEF는 간단하고 빠르게 계산될 수 있지만 특징점의 방향과 강도를 표현하는 능력은 SIFT보다 제한적이다. 

**또한 이러한 규칙 기반의 특징점 매칭은 결국 특징점의 디스크립터간의 관계를 파악하는 구조에서 그칩니다.**

**따라서 특징점을 기술하는 디스크립터가 표현하지 못하는 이미지 전체의 맥락은 파악할 수 없다는 단점을 가질 수밖에 없습니다.**
