---
title: "[3D CV 연구] 3DGS SuGaR entropy loss"
last_modified_at: 2024-06-11
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - SuGaR
  - entropy loss
excerpt: "3DGS SuGaR entropy loss"
use_math: true
classes: wide
comments: true
---

### entropy loss 계산시 ground truth density를 어떻게 계산하나요?

***entropy loss는 unsupervised technique을 사용***하여서 optimization 동안 gaussian opacity values αg가 binary (fully opaque or fully transparent)하도록 encourage합니다.

***따라서 어떠한 ground truth density data도 필요로 하지 않습니다.***

구체적으로는 entropy term을 다음과 같이 loss function에 더합니다.

$$
H(\alpha_g) = -\alpha_g \log(\alpha_g) - (1-\alpha_g) \log(1-\alpha_g)
$$

#### SuGaR entropy loss code snippet
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/697f6ac5-9088-4bc0-b629-e9caf7122dfe)

#### SuGaR entropy loss graph visualize code snippet

```python
import numpy as np
import matplotlib.pyplot as plt

# Define the function
def H(alpha_g):
    return -alpha_g * np.log(alpha_g) - (1 - alpha_g) * np.log(1 - alpha_g)

# Create an array of alpha_g values from 0 to 1
alpha_g_values = np.linspace(0.001, 0.999, 1000)

# Calculate H(alpha_g) for each alpha_g value
H_values = H(alpha_g_values)

# Plot the function without using LaTeX in labels, but with proper alpha symbol
plt.figure(figsize=(8, 6))
plt.plot(alpha_g_values, H_values, label='H(α) = -α log(α) - (1 - α) log(1 - α)')
plt.xlabel('α')
plt.ylabel('H(α)')
plt.title('H(α) = -α log(α) - (1 - α) log(1 - α)')
plt.axvline(0, color='grey', linestyle='--', linewidth=0.5)
plt.axvline(1, color='grey', linestyle='--', linewidth=0.5)
plt.axhline(0, color='grey', linestyle='--', linewidth=0.5)
plt.legend()
plt.grid(True)
plt.show()
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/093cc05e-4fb5-45e7-962c-46b0e6e865a6)

위 식은 each gaussian의 scalar opacity value αg를 계산합니다.

이 entropy term은 

- αg의 trends가 0 또는 1로 갈 때 minimize되는 term입니다. (즉, αg가 0 또는 1일때 loss가 작아짐)
- αg가 0과 1 사이에 있으면 higher entropy/uncertainty를 가집니다. (즉, αg가 0과 1 사이일 때 loss가 커짐)

**따라서 모든 가우시안의 opacities에 대한 entropy term을 minimize하면서, 가우시안들이 극단적인 binary 0 또는 1 values를 가지도록 encourage합니다. (다른말로, 0과 1사이의 transparent values를 가지지 않게 함)**

이는 가우시안들이 불투명도인 αg에 따라,

- αg가 1이 되어 surface content를 완전히 나타내거나,
- αg가 0이 되어 surface content에서 완전히 제외되어야 한다

는 SuGaR 저자의 mesh extraction에 대한 모델링 가정과 일치합니다.

https://github.com/Anttwo/SuGaR/issues/4

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/61926385-759c-4dc8-9df2-e7c63ce32f22)


