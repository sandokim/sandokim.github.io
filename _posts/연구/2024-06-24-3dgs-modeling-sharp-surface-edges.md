---
title: "[3D CV 연구] 3DGS alpha blending & inaccurate surface edge & Modeling sharp surface edges"
last_modified_at: 2024-06-24
categories:
  - 연구
tags:
  - 연구
  - 3D CV
  - 3dgs
  - 3d gaussian splatting
  - sharp surface edges
  - alpha blending process
excerpt: "3DGS alpha blending & inaccurate surface edge & Modeling sharp surface edges"
use_math: true
classes: wide
---

[High-quality Surface Reconstruction using Gaussian Surfels](https://arxiv.org/abs/2404.17774)에서 주장하기로 Gaussian들의 alpha blending process에서 reconstructed되는 surface edge에 bias가 생겨, edge 부분이 날카롭지 않고 부드럽게 나타나는 문제가 발생한다고 합니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/ff266e8d-447a-47bd-9e01-87b2bee0dd9a)

# Modeling Sharp Surface Edges and Alpha Blending Bias

## Alpha Blending 과정

**Alpha Blending**은 컴퓨터 그래픽스에서 반투명한 객체를 렌더링할 때 사용하는 기법입니다. 각 픽셀의 색상을 계산할 때 불투명도(alpha)를 고려하여 여러 색상을 혼합(blend)합니다. 이 과정은 반투명 객체의 색상이 배경 색상과 혼합되어 나타나도록 합니다.

## Gaussian Points와 Surface Edge

**Gaussian Points**는 주로 가우시안 분포를 따르는 포인트 클라우드 데이터를 의미합니다. 이들은 객체의 표면이나 공간의 샘플링 지점을 나타낼 수 있습니다.

**Surface Edge**는 3D 객체의 표면에서 날카롭고 분명한 가장자리 부분을 의미합니다. 날카로운 가장자리는 표면이 급격히 변화하는 부분으로, 이 부분을 정확하게 재구성하는 것이 중요합니다.

## 문제 설명

알파 블렌딩 과정에서 발생하는 문제는 다음과 같습니다:

1. **Gaussian Points의 영향**: 가우시안 분포를 따르는 포인트들이 표면 가장자리나 그 근처에서 샘플링됩니다.
2. **Blending의 범위**: 알파 블렌딩 과정에서는 이 포인트들의 색상 정보를 혼합합니다. 이때, **표면 가장자리로부터 멀리 떨어진 포인트들도 혼합에 포함될 수 있습니다.**
3. **편향 발생**: **가장자리로부터 멀리 떨어진 포인트들이 혼합 과정에 포함되면, 실제 날카로운 가장자리가 부드럽게 나타나는 현상이 발생합니다.** 이는 원래의 날카로운 가장자리가 왜곡되어 나타나게 됩니다.

이 과정을 요약하면, 가우시안 분포를 따르는 포인트들이 표면 가장자리 주변에서 샘플링되고, 이 포인트들이 알파 블렌딩 과정에서 혼합될 때 표면 가장자리에 편향이 생길 수 있다는 것입니다. 결과적으로, 원래 날카로워야 할 가장자리가 부드럽게 나타나는 문제가 발생합니다.

- 즉, Gaussian을 Alpha Blending하는 과정에서 surface edge 부분의 색을 accumulate하는 과정에서 surface에서 멀리 떨어진 Gaussian의 색이 alpha blending과정에 포함되면, surface edge 부분이 blur하게 생성될 수 있습니다.
- 이를 그림으로 나타내면 다음과 같이 표현할 수 있습니다.
  
  ![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/b11518a9-a255-41df-8340-2f34a0595e3c)

## 요약

알파 블렌딩 과정에서 가우시안 분포를 따르는 포인트들이 표면 가장자리로부터 멀리 떨어진 경우에도 혼합에 포함되면서, 재구성된 표면 가장자리가 원래보다 부드럽게 나타나는 편향이 발생할 수 있습니다. 이는 날카로운 표면 가장자리를 정확하게 재구성하는 데 있어서 문제를 일으킬 수 있습니다.








