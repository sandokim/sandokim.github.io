---
title: "[3D CV 연구] 3DGS SuGaR how to improve rendering result of simple object in monochrome background"
last_modified_at: 2024-06-12
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - implementation details
  - improve rendering result
  - monochrome
  - background
excerpt: "3DGS SuGaR how to improve rendering result of simple object in monochrome background"
use_math: true
classes: wide
---

## background가 단색이고 특정한 details이 없는 경우, 필연적으로 background에 small artifacts가 발생하게 됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/9abce77a-b6e2-4e0f-9f7c-3e0548b99a67)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4311ab88-d58e-4fc1-a6fa-b59a5231dbec)

하지만 지금 주어진 상황에서 이게 문제인 것은 아닙니다. 왜냐면 당신의 scene이 character에 heavily focused되어 있기 때문입니다.

지금 보기에 당신은 good-looking background보다는 good-looking character를 reconstruct하고 싶어 보입니다.

## Concerning the quality of the character

당신의 charater는 실제로 그렇게 나빠보이지는 않네요! rendering quality를 높이기 위한 tip을 드리겠습니다.

### 1. `--low_poly` flag를 사용하여 vertices의 수를 줄여보세요.

지금 surface를 보면 slightly irregular합니다. 이는 ***당신의 character가 simple하게 생겼기 때문입니다. (smooth surface and shapes, etc).***

따라서 당신은 ***이 scene에 대해서는 많은 vertices를 필요로 하지 않습니다.***

제안하기로, SuGaR 저자는 white and red kinght인 듀크몬에 대해서는 `--low_poly`를 사용하였고, white and blue robot인 건담에 대해서는 small details를 가지도록 `--high_poly`를 사용하였습니다.

### 2. lighting 을 바꿔보세요.

***SuGaR 저자는 lighting이 rendering 평가에 크게 작용한다고 생각합니다.***

당신의 경우, light source가 surface 아래 부분에 위치한 것으로 보이고, 이는 당신의 scene의 real lighting과 매치되는 것 같지 않아 매우 어색하게 보입니다.

현재 사용 중인 조명을 광원 대신 단일 주변 조명으로 교체해 보세요(소프트웨어에서 이 옵션을 사용할 수 있을 것입니다). 

이렇게 하면 컬러 텍스처로만 메쉬를 표시하고 추가적인 조명 계산은 하지 않게 됩니다. 

이렇게 하면 더 보기 좋고 실제 장면에 훨씬 더 가깝게 보일 것입니다. 

어차피 현재 당신의 ***scene의 그림자는 이미 텍스처에 포함***되어 있기 때문입니다.

Linux/MacOS(또는 WSL2를 사용하는 Windows)을 사용 중이라면, 전용 SuGaR 뷰어를 사용할 수 있습니다. 

이 뷰어를 통해 주변 조명(ambient lighting)과 함께 textured mesh를 시각화할 수 있으며, 하이브리드 표현(i.e. mesh와 표면에 3D Gaussian이 있는 형태)도 확인할 수 있습니다.

https://github.com/Anttwo/SuGaR/issues/96

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/728e80d8-3ccb-4e9b-be72-79034eb729bb)

SuGaR의 저자의 조언대로 simple한 object에 대해서 `--low_poly`를 사용하였더니 reconstruction quality가 훨씬 더 좋아짐을 확인했습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b3bc99d3-e926-473a-b820-934568d97d2f)

또한 SuGaR의 저자의 말대로 simple한 object에 대해서 `--high_poly`를 사용하였더니 reconstruction quality가 `--low_poly`보다 안 좋습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4edcf302-c311-4983-8bb2-f071f1bae999)

## `--middle_poly` flag를 SuGaR original code에서 config 파일에 추가하는 것도 생각해볼 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4a6d5e26-f800-47db-a053-480e4effcbbc)

하지만 코드를 바꾸지 않고 `--low_poly` flag를 주지 않은 상태에서 `--n_vertices_in_mesh 500_000`과 `--gaussians_per_triangle 3`을 command로 주어도 `--middle_poly`와 같은 결과를 볼 수 있습니다.

```python
python train.py -s <path to COLMAP dataset> -c <path to the Gaussian Splatting checkpoint> -r <"density" or "sdf"> --n_vertices_in_mesh 500_000 --gaussians_per_triangle 3
```

이는 SuGaR를 run하고 대략 500,000 vertices를 포함한 mesh를 extract하고 triangle당 3개의 Gaussian들을 binds하는 것입니다.

SuGaR의 조언대로 결과를 뽑아본 결과, simple object에 대해서 `--high_poly`, `--low_poly`보다도 `--middle_poly` 결과가 가장 좋았습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/48d6df7e-cc1f-4d26-8058-2f32fd15960f)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a0ab6c26-45fa-4931-ab81-ad814b775119)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/292c0cba-b72d-406f-aa87-06a54927ee20)



