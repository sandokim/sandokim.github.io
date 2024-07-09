---
title: "[3D CV 연구] 3DGS human rendering ideas"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - human rendering
excerpt: "3DGS human rendering ideas"
use_math: true
classes: wide
comments: true
---

https://github.com/graphdeco-inria/gaussian-splatting/issues/63

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/e1837883-3b95-45df-b606-2a9fabf1f206)


### Specular reflections와 transparency 문제

Q:

- Specular reflections는 ellipsoids의 뒤쪽에 배치되어 view-dependent reflections를 생성합니다
- ***Gaussians가 너무 크면 반사광이 투명하게 보이게 되어 피부가 투명한 것처럼 보입니다.***
- ***어두운 영역에서는 Gaussians가 더 크고, 밝은 영역에서는 더 밀집되어 있습니다.***

A:

- **초기 포인트 클라우드는 매우 중요하지 않으며, 무작위 데이터셋으로도 시작할 수 있습니다.**
- Regularization을 통해 ***Gaussians의 크기를 제한***
- ***큰 Gaussians에 페널티를 주는 추가 손실 항목을 추가***
- ***Gaussians의 크기를 균일하게 만들려면, 각 Gaussian의 크기가 평균 크기에서 벗어나는 정도를 페널티 주는 방식을 사용할 수 있습니다.***
- ***이 경우 개별 xyz extents보다는 전체 volume에 중점을 두는 것이 좋습니다.***

### Gaussians의 밀도가 높은 경우
- Gaussian당 색 해상도를 낮춰 장면을 더 가볍게 만들 수 있습니다.
- 각 Gaussian의 view-dependent 색 구성 요소는 Gaussian당 파라미터 양을 결정합니다.
- **scene이 더 diffuse할수록 적은 파라미터로도 충분**하며, 이는 **sh_degree 파라미터로 조정**합니다.
