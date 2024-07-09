---
title: "[3D CV 연구] 3DGS SuGaR synthetic datasets"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - synthetic datasets
  - lego
excerpt: "3DGS SuGaR synthetic datasets"
use_math: true
classes: wide
comments: true
---

## SuGaR는 오직 real scenes에 대해서만 test되었고, masked background 그리고 random initial point cloud를 사용하는 synthetic datasets에 대해서는 test되지 않았습니다.

그러므로, 어떤 hyperparameters는 synthetic scenes에 대해서는 optimal하지 않을 수 있습니다.

왜냐면, synthetic scenes은 background를 가지고 있지 않고, spatial extension은 extremely limited되어 있기 때문입니다. (그리고 기본적으로 main object만 존재합니다.)

SuGaR website에서 보여준 lego는 synthetic scene이 아니라 Mip-NeRF360 dataset의 real "kitchen" scene에 존재하는 lego입니다.

SuGaR의 저자는 Synthetic datasets에서 mesh의 surface에서 SuGaR의 refine을 하고나서 많은 노이즈가 관찰되는 것은 foreground에 있는 isolated object에 너무 많은 triangles 때문이라고 생각합니다.

## Noisy한 mesh를 가지면 다음을 확인해보세요.

### - mesh의 geometry의 문제인가?
### - mesh의 texture의 문제인가?

https://github.com/Anttwo/SuGaR/issues/39

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/780cade9-bd02-4793-9c27-d35be039c588)

## 1) coarse mesh의 color는 good-looking인 것 같습니다. 그러나 이는 보통 vertex colors만으로는 가능한 일이 아닙니다. 보통 vertex colors는 충분히 조밀하지 않아서 good-looking texture를 만들 수 없기 때문입니다.

제시한 synthetic lego에서는 ***coarse mesh가 good-looking***인걸로 보이는데, 이 의미는 ***당신의 scene이 아마 필요한 triangle 수보다도 매우 많은 triangles을 가진다는 것을 의미***합니다.

SuGaR에서 refined texture를 computing하기 위해 사용한 UV unwrapping 방법은 별로입니다. 그리고 너무 많은 triangles을 가진 object에 대해서는 작은 artifacts들을 생성할 것으로 보입니다.

이럴 땐, 한번 untextured geometery of mesh를 확인해보세요.

## 2) mesh의 geometry 문제일 경우 resolution을 줄여보세요.

만약 object가 너무 많은 triangles를 가진다면, refinement stage (which slightly moves the vertices to smooth the surface)는 triangle들끼리의 self-collisions을 발생시킬 수 있습니다. 그리고 그게 color noise를 발생시킬 수 있습니다.

SuGaR의 default setting에서는 scene은 1,000,000 vertices를 가집니다. `train.py`에서 `--low_poly flag`를 사용하면, 200,000 vertices mesh에 대한 default paramters를 사용할 수 있습니다.

혹은 `--n_vertices_in_mesh option`을 사용하여 vertices의 개수를 manual하게 바꿀 수 있습니다. 자세한 설명은 README에 있습니다.

## 요약

### 1. noise가 생기는 원인이 texture인지 geometery인지 untextured mesh로 확인해보세요.
### 2. 만약 geometry에 의한 문제라면, mesh의 resolution을 줄여보세요. (`--low_poly`를 사용하거나 `--n_vertices_in_mesh`를 조절해보세요.)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/d3918aef-8ba5-492e-891c-8461452e3634)





