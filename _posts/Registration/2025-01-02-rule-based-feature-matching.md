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
  - LIFT
  - SuperPoint
  - SuperGlue
excerpt: "이러한 규칙 기반의 특징 점 매칭은 결국 특징점의 디스크립터간의 관계를 파악하는 구조에서 그칩니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [특징 추출을 이용한 이미지 매칭 기술의 최근 동향 분석](https://ksbe-jbe.org/xml/37415/37415.pdf)

> [Brief: Binary robust independent elementary features](https://www.cs.ubc.ca/~lowe/525/papers/calonder_eccv10.pdf)

> [ORB: An efficient alternative to SIFT or SURF](https://ieeexplore.ieee.org/document/6126544)

> [LIFT: Learned Invariant Feature Transform](https://arxiv.org/pdf/1603.09114)

> [SuperPoint: Self-Supervised Interest Point Detection and Description](https://openaccess.thecvf.com/content_cvpr_2018_workshops/papers/w9/DeTone_SuperPoint_Self-Supervised_Interest_CVPR_2018_paper.pdf)

> [Superglue: Learning feature matching with graph neural networks](https://openaccess.thecvf.com/content_CVPR_2020/papers/Sarlin_SuperGlue_Learning_Feature_Matching_With_Graph_Neural_Networks_CVPR_2020_paper.pdf)

> [LoFTR: Detector-free local feature matching with transformers](https://openaccess.thecvf.com/content/CVPR2021/papers/Sun_LoFTR_Detector-Free_Local_Feature_Matching_With_Transformers_CVPR_2021_paper.pdf)

### 규칙 기반 특징점 매칭

특징점 검출은 과정 이후 찾은 특징점을 벡터로 저장하거나 표현하기 위해서 디스크립터를 생성하고, 

이 디스크립터를 사용하여 특징점 매칭을 진행합니다. 

SIFT를 기반(SIFT, SURF)으로하는 이미지 매칭 기법은 디스크립터 측정 후, 측정된 디스크립터 사이의 유클리드 거리를 계산하여 가장 가까운 두 특징점을 매칭합니다. 

SIFT의 디스크립터는 주변 영역을 16개의 구간으로 나누어 각 구간의 그래디언트를 집계하여값을 생성하기 때문에 **추출된 디스크립터의 크기는 128차원의 벡터**이므로 연산량이 크다는 단점이 있다. 

이를 보완하기 위해 Binary Robust Independent Elementary Features(BRIEF) 알고리즘이 고안되었습니다. 

BRIEF는 이미지의 디스크립터를 이진 특징 벡터로 기술하여 계산이 필요한 차원의 수를 크게 줄이는 알고리즘으로 SIFT 보다 빠른 연산이 가능하게 했습니다. 

Oriented FAST and Rotated BRIEF(ORB)는 특징점 추출 과정에 FAST 알고리즘을 사용하고 특징점 매칭 과정에 BRIEF 알고리즘을 사용하여 SIFT 보다 개선된 수행시간을 보였습니다. 

BRIEF는 간단하고 빠르게 계산될 수 있지만 특징점의 방향과 강도를 표현하는 능력은 SIFT보다 제한적이다. 

**또한 이러한 규칙 기반의 특징점 매칭은 결국 특징점의 디스크립터간의 관계를 파악하는 구조에서 그칩니다.**

**따라서 특징점을 기술하는 디스크립터가 표현하지 못하는 이미지 전체의 맥락은 파악할 수 없다는 단점을 가질 수밖에 없습니다.**

### 이후 이미지의 전체 맥락을 파악할 수 있도록 딥러닝 기반 특징점 추출 방식이 도입되었다라는 흐름입니다.

딥러닝 기법이 발달함에 따라 여러 가지 훈련 가능한 특징 추출 모델들이 등장하였습니다. 

그 중 Learned Invarient Feature Transform (LIFT)는 Convolution Neural Network(CNN)을 사용하여 훈련이 가능하며 SIFT와 유사한 특징점 추출과 디스크립터 생성 기법을 제안했습니다.

### SuperPoint는 LIFT 이후 등장한 특징추출 모델입니다. (특징점 추출 모델과 특징점 매칭 모델은 구분하여 기억합시다.)

이미지를 입력 받아 CNN을 통해 LIFT처럼 특징점 검출과 디스크립터 생성을 동시에 수행하지만, 

SuperPoint는 LIFT와 달리 이미지 크기의 제한이 없으며, 훈련 과정에서 레이블이 있는 데이터로 훈련을 진행한 뒤 레이블이 없는 이미지 쌍을 데이터로 사용하여 자체 감독 훈련을 수행합니다.

기존 딥러닝을 사용한 특징추출 모델은 훈련하기 위해 지도된 결과 값을 가진 훈련 데이터를 사용해야 했지만 SuperPoint는 자체 감독 훈련을 적용하여 이를 극복했습니다. 

아래 그림은 SuperPoint의 자체 감독 훈련 개요를 보여줍니다.

![image](https://github.com/user-attachments/assets/4e513c0c-8ac2-4225-9b0e-1035ce3fd25f)

SuperPoint는 레이블이없는 데이터에 대해 기본적인 도형 이미지를 통해 훈련된 기준 특징추출 모델과 Homographic 적응 절차를 적용하여 레이블을 자동으로 지정합니다. 

이후 생성된 레이블을 통해 CNN를 훈련합니다. 

### 딥러닝을 사용한 특징점 매칭

딥러닝을 사용하여 특징점을 추출하는 방법뿐만 아니라 딥러닝을 사용하여 특징점을 매칭하는 방법 또한 연구되고 있습니다. 

기존의 연구들은 두 이미지의 특징점의 디스크립터를 통해서만 관계를 파악하는 한계가 있었기 때문에 딥러닝을 사용하여 이러한 한계를 극복하려 했습니다. 

특히 **특징점 매칭 시 특징점의 디스크립터만을 고려하는 것이 아닌** **특징점 사이의 관계도 고려하여 매칭 하는 연구**가 진행되고 있습니다. 

SuperGlue는 SuperPoint의 후속 연구로 Graph Neural Networks (GNN)을 사용하여 두 이미지 사이 특징점들 간 관계뿐만 아니라 같은 이미지에 있는 특징점의 관계 또한 고려하여 특징점 매칭을 수행합니다. 

SuperGlue는 이미 추출된 특징점과 디스크립터를 입력데이터로 받아 매칭을 수행하며 추출된 특징점에 노드로 같은 이미지에 존재하는 특징점간의 엣지를 생성한 그래프인 self 그래프와 매칭 쌍 이미지에 존재하는 특징점간 엣지를 생성한 cross 그래프를 두 개 생성하여 GNN을 적용합니다. 

이를 통해 SuperGlue는 본 이미지에서의 특징점의 맥락과 상대 이미지의 특징점과의 맥락을 전부 반영하여 이미지 매칭을 수행할 수 있습니다. 

아래 그림은 SuperGlue의 동작 과정을 보여줍닌다. 

![image](https://github.com/user-attachments/assets/35aee1bc-4b5d-4033-a423-41e388ca56e1)

**SuperGlue는** 추출된 특징점과 디스크립터를 입력으로 받는 기법이므로 추출된 특징점간의 관계를 고려하여 매칭을 수행하지만 **특징점이 검출되지 않은 구역이나 이미지의 전체적인 맥락은 고려하지 못합니다. **

또한 다른 위치의 특징점이 같은 디스크립터를 가진다면 둘 사이의 차이를 구별하는 위치 정보를 고려하지 못합니다.

이를 해결하기 위해 제안된 Detector-Free Local Feature Matching with Transformers (LoFTR)은 SuperGlue나 SIFT같은 기존방법처럼 추출된 특징점을 매칭하는 것이 아니라 

이미지에 CNN을 통해 추출된 feature map을 사용한 뒤 관계성을 판단하고 

추후 coarse-to-fine module을 통해 특징점을 지정합니다.

아래 그림은 LoFTR의 동작 과정을 보여줍니다. 

![image](https://github.com/user-attachments/assets/482f5c0a-3e3b-4d2e-bc68-3776ffed5241)

**LoFTR은** 추출된 feature map을 같은 이미지의 특징 사이의 관계를 나타내는 self attention layer와 매칭 쌍 이미지의 특징 사이의 관계를 나타내는 cross attention layer를 생성한 뒤 **디스크립터 없이 바로 이미지 매칭을 수행한다.**

더불어 매칭된 Feature map에서 Coarse-to-Fine Module을 제안하여 상세히 특징점의 위치를 추정합니다. 

LoFTR는 SuperGlue와 같이 self attention layer, cross attention layer를 통해 본 이미지내부의 관계와 매칭 대상 이미지와의 관계를 고려하지만

GNN이 아닌 transformer를 사용하여 성능을 발전시키고 **추출된 특징점이 아니라 feature map을 사용하여 특징의 위치적 정보를 고려할 수 있는 기법**입니다. 

또한 LoFTR의 높은 성능 때문에 이후에도 LoFTR을 토대로 사용한 다양한 이미지 매칭 기법들이 등장하였습니다.

### 이미지 매칭 기법 결과

![image](https://github.com/user-attachments/assets/b3dbcef4-04b0-4467-967e-3190dc75a222)

**실험결과에서 SIFT와 ORB는 이미지의 추출된 특징점의 위치는 코너나 엣지가 있는 부분으로 한정되어 있으며**

특히 ORB의 경우 SIFT에 비해 상대적으로 흐릿한 영역의 특징점을 추출하는 능력이 부족하다는 것을 확인할 수 있습니다. 

**또한 딥러닝을 사용한 기법이 그렇지 않은 기법보다 많은 특징점을 매칭 시키는 것을 실험 결과에서 확인할 수 있습니다.**

**특히, LoFTR의 경우 코너나 엣지가 아닌 평탄한 부분에서도 특징점을 매칭 시킬 수 있는 성능**을 보여주고 있으며 특징점 간 공간적인 정보를 더 잘 유지하는 것을 확인할 수 있습니다. 

### 결론

본 논문에서는 특징 추출을 통한 이미지 매칭 기법에 대해서 특징 점 추출과 특징 점 매칭의 두 단계로 나누어 각 절차를 설명하고 널리 알려진 이미지 매칭 기법에 대해 살펴보았습니다. 

이후 실험을 통해 각 기법의 성능을 평가하고 이미지 매칭의 정성적 결과를 논의하였습다. 

특징 추출을 통한 homography 추정에서는 SuperPoint가 가장 우수한 성적을 보여주었으며. 특징점 매칭 기법 중에서는 LoFTR이 가장우수한 매칭 성능을 보여주었습니다. 

또한 **딥러닝을 사용한 기법들이 규칙기반의 기법보다 더 많은 특징점을 정확히 매칭시켰으며**

LoFTR의 경우 특징점 간의 공간적 정보를 보존하여 매칭이 어려운 영역에서도 우수한 성능을 보여주었습니다. 

이미지 매칭은 두 이미지 안에서 특징점 간 관계, 전체적인 이미지에서 특징점의 맥락 등을 고려해야 하는 복잡하고 까다로운 작업입니다.

딥러닝 기술의 발전과 함께 기존 기법들의 단점을 해결하고 다양한 이미지의 특징을 고려하는 새로운 기법들이 등장하고 있습니다. 

그러나 논문에 제시된 기존의 기법들은 각 프로젝트의 목적과 매칭 이미지의 특성에 따라 제약사항이 발생할 수 있습니다. 

따라서 이러한 목적과특성을 고려하여 기존 기법의 단점을 분석하고 극복할수있는 새로운 이미지 매칭 방법에 대한 연구가 진행되어야 합니다.
