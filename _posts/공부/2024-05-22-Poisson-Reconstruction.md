---
title: "[3D CV] Poisson Reconstruction"
last_modified_at: 2024-05-22
categories:
  - 공부
tags:
  - Poisson Reconstruction
  - Octree
  - 옥트리
  - poisson depth
excerpt: "Poisson Reconstruction 정리"
use_math: true
classes: wide
---

## Poisson Reconstruction

### Poisson detph

Poisson 재구성은 포인트 클라우드(point cloud) 데이터로부터 매끄러운 표면을 생성하는 방법 중 하나입니다. 이 알고리즘은 포인트 클라우드에서 추출한 정상(normal) 벡터를 사용하여 최적의 표면을 계산합니다.

**Poisson Depth = Depth of Octree**

"poisson_depth"는 옥트리의 최대 깊이를 의미합니다. 깊이가 깊을수록 공간을 더 세밀하게 분할하여 작은 세부 사항까지 표현할 수 있습니다.
- 높은 깊이: 더 작은 셀로 공간을 분할하므로, 더 많은 세부 사항을 캡처할 수 있지만, 노이즈와 작은 결함도 더 많이 나타날 수 있습니다.
- 낮은 깊이: 더 큰 셀로 공간을 분할하므로, 큰 구조를 매끄럽게 표현할 수 있지만, 작은 세부 사항은 놓칠 수 있습니다.

### Practical Usage of Poisson depth value
- 깊이 감소: 옥트리 깊이를 줄이면, 표면을 덜 세밀하게 분할하게 되어, 작은 결함이 덜 드러나게 됩니다.
- 메쉬 표면에 지저분한 타원형 돌기가 나타나는 문제를 해결하기 위해 옥트리 깊이를 줄이는 것이 권장됩니다.
```python
poisson_depth = 7
```
