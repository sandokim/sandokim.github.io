---
title: "[Registration] Large-scale scene reconstruction's challenge"
last_modified_at: 2024-12-31
categories:
  - Registration
tags:
  - Registration
  - 정합
  - city scale
  - large-scale scene reconstruction
  - transient objects
  - a single capture
  - variance in both scene geometry and appearance
excerpt: "transient objects, highly unlikely to be collected in a single caputre under consistent conditions, variance in both scene geometry and appearance"
use_math: true
classes: wide
comments: true
---

> Reference

>[Block-NeRF: Scalable Large Scene Neural View Synthesis](https://openaccess.thecvf.com/content/CVPR2022/papers/Tancik_Block-NeRF_Scalable_Large_Scene_Neural_View_Synthesis_CVPR_2022_paper.pdf)

### Large-scale scene reconstruction에서 어려운 점

- the presence of transient objects (cars and pedestrians)
- highly unlikely to be collected in a single capture under consistent conditions
- variance in scene geometry (e.g., construction work and parked cars)
- variance in appearance (e.g., weather conditions and time of day)

Reconstructing such large-scale environments introduces additional challenges, including **the presence of transient objects (cars and pedestrians)**, limitations in model capacity, along with memory and compute constraints. 

Furthermore, training data for such **large environments is highly unlikely to be collected in a single capture under consistent conditions.**

Rather, data for different parts of the environment may need to be sourced from different data collection efforts, introducing **variance in both** **scene geometry (e.g., construction work and parked cars)**, as well as **appearance (e.g., weather conditions and time of day).**

- 대규모 환경 재구성에는 여러 도전 과제가 있음:
  - 일시적 객체(자동차, 보행자)의 존재.
  - 모델 용량, 메모리, 계산 리소스의 한계.
- 대규모 환경의 학습 데이터는 일관된 조건에서 단일 캡처로 수집되기 어렵고,
  - 다른 데이터 수집 노력에서 각기 다른 환경 데이터를 조합해야 할 가능성이 높음.
  - 이로 인해 장면 기하학(예: 공사, 주차된 차량)과 외형(예: 날씨, 시간)에서 변동성 발생.
 

### Block-NeRF의 확장 및 한계:

- 네트워크 용량을 확장하면 더 큰 장면을 표현 가능.
  - 그러나 다음과 같은 제약 발생:
    - 렌더링 시간이 네트워크 크기에 비례해 증가.
    - 네트워크가 단일 컴퓨팅 장치에 적합하지 않음.
    - 환경 업데이트나 확장은 네트워크 전체 재학습(retraining)이 필요.
- 문제 해결 방안:
  - 대규모 환경을 **개별 Block-NeRF로 분할 및 독립적 학습**.
  - 추론 시 관련된 Block-NeRF만 렌더링하여 동적으로 결합 및 생성.
  - 이를 통해:
    - 유연성 극대화, 임의의 대규모 환경 처리 가능.
    - 환경의 부분적 업데이트 및 확장 가능(**재학습 불필요**).
   
  
