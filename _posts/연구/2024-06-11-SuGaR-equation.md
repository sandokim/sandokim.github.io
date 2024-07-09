---
title: "[3D CV 연구] 3DGS SuGaR equations"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - flat gaussian
  - splatted
excerpt: "3DGS SuGaR equations"
use_math: true
classes: wide
comments: true
---

https://github.com/Anttwo/SuGaR/issues/2

### Equation (4)

$$
(p - \mu_g)^T \Sigma_g^{-1} (p - \mu_g) \approx \frac{1}{s_g^2} \langle p - \mu_g, n_g \rangle^2
$$

- ***Gaussians가 'flat'하다는 것은 하나의 scaling factor가 다른 것들보다 훨씬 작다는 것을 의미합니다.***
- inverse covariance matrix를 대각선의 하나의 non-zero 값과 나머지 두 개의 작은 값만 있는 대각 행렬로 근사합니다.
- 이 non-zero 값이 투영된 결과를 스케일링합니다.

#### 질문: 왜 Equation (4)가 성립하는가?

**답변**:
Equation (4)는 Gaussians가 'flat'한 이상적인 경우의 근사에서 나옵니다. 'Flat'한 Gaussian은 하나의 scaling factor가 다른 것들보다 훨씬 작은 경우를 의미합니다. 이 작은 scaling factor를 $\lambda$라고 하겠습니다.

Rotation matrix $R$와 diagonal scaling matrix $S$의 역행렬을 사용하여 전체 inverse covariance matrix $\Sigma^{-1}$를 작성하면, $\Sigma^{-1}$를 대각선의 하나의 non-zero 값과 나머지 두 개의 작은 값만 있는 대각 행렬로 근사할 수 있습니다. 이 non-zero 값은 $\lambda$입니다.

따라서 $\Sigma^{-1}$를 $\alpha$로 곱하는 것은 $\alpha$를 $\Sigma^{-1}$의 가장 작은 scaling factor와 연관된 열에 투영하고 결과를 $\lambda$로 스케일링하는 것과 같습니다. 이 열이 $\alpha$와 연관된 scaling axis에 해당하기 때문에 Equation (4)가 성립합니다.

### Equation (5)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/a4f09683-a6f6-4ec8-a8c4-f74117ae5e42)

### Equation (6)

$$
\overline{f}(p) = \pm s_{g^*} \sqrt{-2 \log(\overline{d}(p))}
$$

- SDF를 사용하여 정규화하면 더 나은 재구성 결과를 제공합니다.
- density 함수에서 거리 함수로 변환하면 효과적으로 재구성할 수 있습니다.
- Equation (6)은 Equation (5)의 역공식입니다.

#### 질문: Equation (6)에서 opacity 대신 SDF를 정규화하는 이유는 무엇인가? 이 특정한 공식을 어떻게 도출했는가?

**답변**:
density 함수를 Signed Distance Function (SDF)으로 변환하는 것이 재구성을 더 잘 정규화합니다. SDF를 사용하면 정량적인 결과가 더 좋아지고 배경도 더 잘 정규화됩니다.

직관적으로 보면, Equation (5)는 평평하게 분포된 Gaussians의 density 함수를 나타내며, 지수 내부의 스칼라 곱은 3D 점 $p$와 3D Gaussian의 중심을 통과하는 평면 사이의 거리와 같습니다. 벡터 $v$는 Gaussian의 가장 작은 scaling factor와 연관된 축이므로, Gaussian이 평평할 때 가장 작은 scaling factor가 0에 가까워지고, 이 벡터를 표면의 normal로 간주하는 것이 매우 직관적입니다.

마지막으로, Equation (6)은 단순히 Equation (5)의 역공식입니다. 만약 Equation (5)가 $d(p) = h(\langle p - \mu_g, n_g \rangle)$와 같은 값을 제공한다면, Equation (6)은 $\langle p - \mu_g, n_g \rangle = h^{-1}(d(p))$를 제공합니다. 이 관계는 이상적인 경우에 성립하지만, 실제 비이상적인 density $\rho$를 사용하여 Equation (6)에 따라 $\rho$를 계산하여 density가 non-destructive way하게 이상적인 density 함수로 수렴하도록 강제합니다.

### Fig.5
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/1b6f25df-8478-42b7-b032-1e4b36b79497)
- depth map 근사치는 SDF를 계산하는 데 도움이 됩니다.
- 가장 가까운 표면 점 근사치는 depth map의 투영을 사용합니다.
- 정규화는 Gaussians가 표면과 일치하고 카메라 포즈를 향하게 하여 배경을 정규화합니다.

#### 질문: 왜 Fig. 5에서는 점 $p$의 깊이의 차이를 계산하여 $f(p)$를 계산할 수 있는가?

**답변**:
estimator $\hat{f}(p)$는 현재 장면과 연관된 실제 SDF의 대략적인 근사치입니다. 이 근사는 "splatted" depth maps의 맥락에서 의미가 있습니다.

최적화 중인 카메라 $c$에 대해 Gaussian Splatting rasterizer를 사용하여 depth map을 계산합니다. 비록 완벽하지는 않지만, 이 depth map은 장면의 표면을 잘 묘사합니다. Gaussian 분포의 곱을 사용하여 샘플링된 점 $p$는 실제 표면 근처에 위치할 가능성이 높으며, $p$의 SDF 값, 즉 표면까지의 거리는 $p$와 가장 가까운 depth map의 표면 점 $q$ 사이의 거리와 같습니다.

depth map이 "splatted"되어 있기 때문에 관찰된 표면은 카메라를 향한 작은 요소들로 구성되어 있습니다. 따라서 $p$와 동일한 시선 상에 있는 점이 depth map의 투영에 해당하는 점 $q$일 가능성이 높습니다.

이 접근법은 밀도에 대한 깊이 정규화를 포함시키기 위한 도구로, Gaussians가 표면과 일치하도록 유도할 뿐만 아니라, 카메라 포즈를 평균적으로 향하게 하는 유용한 우선 순위를 제공하여 배경을 정규화하는 데 도움을 줍니다.

