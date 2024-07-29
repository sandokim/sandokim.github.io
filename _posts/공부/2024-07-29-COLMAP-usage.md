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

# COLMAP 사용법

1. 윈도우에서 COLMAP.exe 실행
2. Reconstruction -> Automatic reconstruction
   
   - **Workspace folder**: C:/Users/KHS/nerfs/data/output
   - **Image folder**: C:/Users/KHS/nerfs/data/images

3. Run

### output

![image](https://github.com/user-attachments/assets/ad85e759-3777-46bd-b862-5c7582a7fb53)

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

# 3DGS 폴더구성

- images
- sparse

  ![image](https://github.com/user-attachments/assets/63cd72a4-a59c-44cb-8a40-ffed2956c638)

Run 3DGS training
