---
title: "[MR 연구] k-space value ranges & image intensity value ranges"
last_modified_at: 2024-05-26
categories:
  - 연구
tags:
  - 연구
  - MR Reconstruction
  - MR image domain
  - MR k-space domain
  - k-space center
excerpt: "fastMRI & skm-tea Real & Simulation 데이터셋 정리"
use_math: true
classes: wide
---

### k-space의 center point는 모든 이미지의 intensity를 더한것과 같습니다.

### k-space에서 max값은 k-space의 center에 존재합니다. $(e^{-i}$의 max가 1인 경우)

### Fourier transform (image domain ↔ k-space)

$\int_xe^{-if(x)}I(x)dx =k(f)$

- $f$: k-space domain 좌표
- $x$: image domain 좌표
- $I$: Image
- $k$: k-space

식에서 알 수 있듯이, scaling을 fourier, image domain에서 똑같이 나누면 성립합니다.

**만약, 특별하게 $f=0$(k-space에서 중심값)이라면 image domain의 전체 intensity를 알 수 있습니다.**

$\int_xI(x)dx=\int_xe^{-i0(x)}I(x)dx =k(0)=image \ intensity$

**또한 $f=0$인 경우가 k-space에서 max값을 의미합니다.**

- k-space를 ifft한 image에서 밝은 영역이 넓고 밝기가 클수록, k-space의 max 값이 크게 나타납니다.
- k-space를 ifft한 image에서 밝은 영역이 작고 밝기가 작을수록, k-space의 min 값이 작게 나타납니다.
- 따라서 k-space의 value range에서 min max의 값이 크게 다르다고 해서 normalization하지 않습니다.
- 왜냐면 k-space의 value는 image domain에서는 밝기의 분포의 총합을 의미하기 때문입니다.
- k-space에서 max 값의 차이가 많이 나타나더라도, image domain으로 ifft하면 image domain에서 value들을 대부분 0 ~ 1.2, 0 ~ 1.7 등의 range로 비슷하게 가짐을 실험적으로, 이론적으로 증명이 가능합니다.

![1718177639422](https://github.com/sandokim/sandokim.github.io/assets/74639652/d5a48b90-9993-4d87-8552-ff15424385bc)
![1718177641820](https://github.com/sandokim/sandokim.github.io/assets/74639652/a2da711e-6096-483f-ac0f-a272996ad976)
![1718177642817](https://github.com/sandokim/sandokim.github.io/assets/74639652/fc2aa2ca-53e1-457e-a2ed-b4a16d8c1753)
![1718177643910](https://github.com/sandokim/sandokim.github.io/assets/74639652/5048d675-5054-4e5b-b1ac-37f8071493cf)
![1718177644936](https://github.com/sandokim/sandokim.github.io/assets/74639652/4f4bbe16-a026-4a8a-a821-ac4d1197a9ec)
![1718177645923](https://github.com/sandokim/sandokim.github.io/assets/74639652/85f9d46c-1ed0-44f0-92af-3ab065fa03dd)

- MR에서 mean을 0으로 보내는 z-norm은 함부로 쓰면 안됩니다. k-space domain에서 최대값이 center에 위치하지 않게 되버릴 수도 있기 때문입니다. → diffusion model처럼 z-norm하지 않고 따로 처리해줘야 합니다.

### Coilmap normalization

만약 coil이 4개라면, 이에 대해 $S_{sos}$를 구하고, 각 코일 $S_1, S_2, S_3, S_4$를 $max(S_{sos})$로 나눠줍니다.

$S_{sos}=\sqrt{S_1^2+S_2^2+S_3^2+S_4^2}$

- $normalized \ S_1 = \frac{S_1}{max(S_{sos})}$
- $normalized \ S_2 = \frac{S_2}{max(S_{sos})}$
- $normalized \ S_3 = \frac{S_3}{max(S_{sos})}$
- $normalized \ S_4 = \frac{S_4}{max(S_{sos})}$

각 이미지 $S_1, S_2, S_3, S_4$의 모든 pixel에 대해 sum이 1이어야하는 조건을 강하게 줄 수 있습니다.

$\sum^4_{i=1}S_i^2=\mathbf{1}$

→ 단 이는 **ideal**한 것을 예시로 드는 것이기 때문에 **raw에서는 이렇게 하면 안될 것으로 보입니다.**

⇒ **coilmap의 합과 1 matrix의 차이가 0이 되게하는 loss로 constraint를 주는게 raw에서는 더 나을 것으로 보입니다**

$1 \ matrix \ loss =min(\sum^4_{i=1}S_i^2-\mathbf{1})$
