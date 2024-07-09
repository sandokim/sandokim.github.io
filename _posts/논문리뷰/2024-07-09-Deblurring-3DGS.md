---
title: "[논문리뷰] Deblurring 3D Gaussian Splatting"
last_modified_at: 2024-07-09
comments: true
categories:
  - 논문리뷰
tags:
  - Computer Vision
  - 3dgs
  - 3d gaussian splatting
  - Deblurring 3d gaussian splatting
  - Structure from Motion
  - SfM
excerpt: "[논문리뷰] Deblurring 3D Gaussian Splatting"
use_math: true
classes: wide
---

> arXiv 2024. [[Paper](https://arxiv.org/abs/2401.00834)] [[Page](https://benhenryl.github.io/Deblurring-3D-Gaussian-Splatting/)]  
> Byeonghyeon Lee, Howoong Lee, Xiangyu Sun, Usman Ali, Eunbyung Park  
> Sungkyunkwan University | Hanhwa Vision  
> 1 Jan 2024  

## 본 포스트는 kimjy99님의 블로그를 참조하여 정리한 글임을 밝힙니다.

### 3D-GS에서 (SfM)는 depth of field가 크다면 Scene에서 z값이 큰 (깊이값이 큰) 영역에 대해서는 point를 거의 추출하지 않습니다.
- 3D-GS는 일반적으로 Structure-from-Motion (SfM)에서 얻은 sparse한 포인트 클라우드에서 3D 장면을 모델링합니다.
- SfM은 멀티뷰 이미지에서 feature들을 추출하고 이를 장면의 3D 포인트를 통해 연결합니다. 
- **주어진 이미지가 흐릿하면 SfM은 유효한 feature를 식별하는 데 크게 실패하고 결국 매우 적은 수의 포인트를 추출하게 됩니다.**
- 더 나쁜 것은 장면의 **depth of field가 더 크다면 SfM은 장면의 맨 끝에 있는 점을 거의 추출하지 않는다**는 것입니다.
- 흐릿한 이미지들로 구성된 포인트 클라우드의 과도한 sparsity로 인해 포인트 클라우드에 의존하는 3DGS를 포함한 기존 방법은 장면을 세밀하게 재구성하지 못합니다.
- 이러한 과도한 sparsity를 보상하기 위해 N-nearest-neighbor interpolation을 사용하여 포인트 클라우드에 유효한 색상 feature들을 가진 추가 포인트를 추가한다.
- 또한 먼 거리의 평면에 더 많은 Gaussian을 유지하기 위해 위치에 따라 Gaussian을 pruning합니다. 

### Deblurring 3DGS에서는 blurring은 분산이 큰 3D Gaussian이 담당할 것이라고 가정하고, 이에 대한 rotation `r`, scale `s`의 변화량을 MLP로 예측합니다.
- 저자들은 큰 크기의 3D Gaussian이 흐림을 유발하는 반면 상대적으로 작은 3D Gaussian은 선명한 이미지에 해당한다고 가정하였습니다.
- 이는 **분산이 큰 것일수록 이미지 공간에서 더 넓은 영역을 담당하므로 더 많은 인접 정보의 영향을 받아 인접 픽셀의 간섭을 나타낼 수 있기 때문**입니다.
- **3D 장면의 미세한 디테일은 더 작은 3D Gaussian들을 통해 더 잘 모델링될 수 있습니다.**


### Reference
[[논문리뷰] Deblurring 3D Gaussian Splatting](https://kimjy99.github.io/%EB%85%BC%EB%AC%B8%EB%A6%AC%EB%B7%B0/deblurring-3dgs/)
