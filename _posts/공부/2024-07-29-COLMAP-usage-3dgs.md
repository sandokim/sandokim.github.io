---
title: "[3D CV] COLMAP 사용법 & 3DGS COLMAP Data 구성"
last_modified_at: 2024-07-29
categories:
  - 공부
tags:
  - Multiple View Geometry
  - COLMAP
  - COLMAP 사용법
  - 3DGS colmap data
excerpt: "COLMAP 사용법 & 3DGS COLMAP Data 구성"
use_math: true
classes: wide
comments: true
---

# 동영상 촬영 후 fps 설정하여 이미지를 먼저 추출

```css
ffmpeg -i "C:/Users/KHS/nerfs/data/colmap/rgb_vid.mp4" -qscale:v 1 -qmin 1 -vf "scale=1280:720, fps=1" C:/Users/KHS/nerfs/data/colmap/FaceHS_10_views/images/%04d.jpg
```

# COLMAP 사용법

1. 윈도우에서 COLMAP.exe 실행
2. Reconstruction -> Automatic reconstruction
   
   - **Workspace folder**: C:/Users/KHS/nerfs/data/output
   - **Image folder**: C:/Users/KHS/nerfs/data/images

3. Run

### output

![image](https://github.com/user-attachments/assets/ad85e759-3777-46bd-b862-5c7582a7fb53)

- dense
- sparse
- database.db

### output/sparse/0

![image](https://github.com/user-attachments/assets/019457fc-ca45-40e4-9bdb-055274b4c085)

  - cameras.bin
  - images.bin
  - points3D.bin
  - project

    ![image](https://github.com/user-attachments/assets/3fc17588-64f7-44df-9d21-5e6679446726)


### output/dense/0

![image](https://github.com/user-attachments/assets/252fbbbb-a28f-41d9-be2f-f00d6f854062)

  - images
  - sparse
  - stereo
  - fused.ply
  - fused.ply.vis
  - run-colmap-geometric
  - run-colmap-photometric

    ![image](https://github.com/user-attachments/assets/9ece5936-d0e3-418a-8ecf-c01f0a9af5ac)

------

# 3DGS 폴더 구성 및 Undistortion with 3DGS `convert.py` 

![image](https://github.com/user-attachments/assets/ff579e07-5422-4035-b67f-80b26c6e3e74)

- input <-- images 폴더의 이미지들 넣기
- distorted <-- output/sparse, output/database.db 넣기

Undistort images for 3DGS using `convert.py`

Then run 
```shell
python convert.py -s <location> --skip_matching [--resize] #If not resizing, ImageMagick is not needed
```
- `--skip_matching` 인자를 꼭 넣어야 합니다. (여기서는 COLMAP info로 image Undistortion만 하기 때문입니다.)

  ![image](https://github.com/user-attachments/assets/32847fbe-3e19-4ee1-81a1-646240caafa9)


### 최종 데이터 트리구조
- input 
- distorted
- images (input folder의 이미지들이 undistortion된 이미지들이 생성됨, 사이즈가 제 각각 다름)
  - 추후에 3dgs에 사용되는 RGB 이미지들은 readColmapSceneInfo에서 images 폴더에서 불러옵니다.
  
    ![image](https://github.com/user-attachments/assets/9c1203b8-acfe-40aa-b074-ac6a673608f6)

    ![image](https://github.com/user-attachments/assets/735710ce-72c2-432c-a18a-4887bda01af7)

- sparse (생성됨)
- stereo (생성됨)
- run-colmap-photometric.sh (생성됨)
- run-colmap-geometric.sh (생성됨) 

  ![image](https://github.com/user-attachments/assets/4a4eed7d-dff4-4470-9044-0adbd73377dd)


### 3dgs image load 하는법

아래 코드를 순서대로 수정합니다.

- `gaussian_splatting/scene/dataset_readers.py` (read 하는 부분)
- `gaussian_splatting/utils/camera_utils.py` (resize 및 torch.tensor로 바꾸는 부분)
- `gaussian_splatting/scene/cameras.py` (torch.clamp로 normalize하는 부분)
