---
title: "[3D CV 연구] 3D Gaussian Splatting background color masking with RGBA images"
last_modified_at: 2024-06-04
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - camera calibration
  - 3dgs
  - 3d gaussian splatting
  - blender dataset
  - background color
  - RGBA
excerpt: "3DGS background color removal & RGBA"
use_math: true
classes: wide
---

### How to remove the background

https://github.com/graphdeco-inria/gaussian-splatting/issues/101

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f961fab7-46c8-44b9-9f82-dc19c2a3887b)

오직 foreground object에 대해서만 관심이 있고, background에 대한 reconstruction을 하지않고 피하기 위해서는, original RGB image와 mask를 merge하면 됩니다. 이때, Alpha에 대해서는 mask해서 없애버려야 하는 영역을 모두 0으로 만들면 됩니다.

이는 Python script와 imagemagick을 사용하여 할 수 있습니다.

RGB 3채널 이미지에 추가로 Alpha channel을 Object 영역이외의 부분을 0으로 하는 Mask를 주면, 이미지 자체는 white background를 갖는 RGBA 이미지로 저장됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c455451f-2c64-4fcc-9195-cce20e18b133)

이는 실제 Blender 데이터셋에서 lego의 RGBA 이미지가 white background를 갖는 것과 일치함을 알 수 있습니다.

<div style="text-align: center;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/55f1a77a-03e0-4b9f-94cb-afeb36739e5b" alt="Image 1" style="width: 45%; margin-right: 5%;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/aafb9c38-3e0d-4ec2-8f4e-5ddcf00c9a49" alt="Image 2" style="width: 45%;">
</div>

### Blender dataset (lego scene)에 대한 alpha mask 분석

Blender와 같은 데이터셋의 경우 RGBA로 RGB + Alpha channel이 존재하는 4채널 png 파일입니다.

```python
from PIL import Image
import numpy as np
import matplotlib.pyplot as plt
import pandas as pd

# Open the image file
file_path = 'r_59.png' # Set path to Your image
image = Image.open(file_path)

# Check if the image is RGBA
is_rgba = image.mode == 'RGBA'

if is_rgba:
    # Convert image to numpy array
    image_array = np.array(image)
    # Extract the alpha channel
    alpha_channel = image_array[:, :, 3]
    
    # Count the occurrences of each alpha value
    unique, counts = np.unique(alpha_channel, return_counts=True)
    
    # Print alpha values and counts
    for u, c in zip(unique, counts):
        print(f"Alpha Value: {u}, Count: {c}")
    
    # Visualize the alpha channel
    plt.figure(figsize=(8, 8))
    plt.imshow(alpha_channel, cmap='gray')
    plt.title('Alpha Channel Visualization')
    plt.axis('off')
    plt.show()
else:
    print("The image is not in RGBA format.")
```

<div style="text-align: center;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/aafb9c38-3e0d-4ec2-8f4e-5ddcf00c9a49" alt="Image 1" style="width: 45%; margin-right: 5%;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/736c47e7-edad-4243-9ad6-1029c18cac39" alt="Image 2" style="width: 45%;">
</div>

***Alpha 채널은 0부터 255까지의 연속적인 값을 갖고 있으며, 투명도를 나타냅니다.***

- 최소값: 0 (완전히 투명한 부분, 검정색으로 표시됨)
- 최대값: 255 (완전히 불투명한 부분, 흰색으로 표시됨)

***Blender 데이터셋 같은 경우 Alpha channel에서 alpha value로 0과 255 값이 대부분을 차지하므로, 사실상 binary mask로써 존재함을 시각적 그리고 정량적으로 관찰할 수 있습니다.***
  
<div style="text-align: center;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/e0f449c9-528b-4920-a289-c70d2e606a47" alt="Image 1" style="width: 45%; margin-right: 5%;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/cb6a6649-94ce-471e-a6a6-a6fc84ed2785" alt="Image 2" style="width: 45%;">
</div>

<div style="text-align: center;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/a1b86a13-67f8-411d-ba2c-e31eb3674b49" alt="Image 3" style="width: 45%; margin-right: 5%;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/e1028c41-3f5e-4fe0-bf60-79c6d07fea9c" alt="Image 4" style="width: 45%;">
</div>

<div style="text-align: center;">
  <img src="https://github.com/sandokim/sandokim.github.io/assets/74639652/795a0e36-0cc6-4aba-bb39-1ad293288e46" alt="Image 5" style="width: 45%;">
</div>

-----------------------------------------------------------

### 3DGS code에서 RGBA 이미지를 처리하는 방식

3DGS 코드에서 Blender 데이터셋 같은 RGBA 이미지를 불러왔을 때,

즉, RGB image의 shape이 4일 경우, RGB image에 Alpha channel이 마지막 채널로 존재하게 됩니다.

이 경우, 4번째 channel인 Alpha mask를 indexing해서 loaded_mask 변수로 따로 분리하고, gt_alpha_mask 변수에 넘겨줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/74ceca4c-72d6-4767-a56a-216850fef84b)

그리고 cameras.py에서 아까 불러온 gt_alpha_mask를 받아서 original_image에 곱하여 배경 부분을 0으로 삭제해줍니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/c3aed915-b6de-43e1-a5d4-86e206bc4c2f)

***그러나 실제 3DGS original 코드상에서는 image를 "RGBA" 정보가 아닌 "RGB"로 불러오고 있으며, shape을 고려하는 부분도 [3, W, H], [4, W, H]일 것 중에서 shape(1)인 W로 일부로 indexing하고 있게 되어있습니다.***

**즉, 애초에 "RGBA" 이미지도 "RGB"로만 고려해서 학습하도록 하드 코딩되어 있습니다.**

https://github.com/graphdeco-inria/gaussian-splatting/issues/64

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/17fe83c5-b0b6-411f-a757-d54ed0a96f81)




