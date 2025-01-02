---
title: "[Registration] Harris corner detection"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - edge
  - corner
  - Harris corner detection
excerpt: "Harris 코너 검출 알고리즘은 이미지에서 코너를 추출 하는 알고리즘의 대표적인 예입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [특징 추출을 이용한 이미지 매칭 기술의 최근 동향 분석](https://ksbe-jbe.org/xml/37415/37415.pdf)

**Harris 코너 검출 알고리즘은 이미지에서 코너를 추출 하는 알고리즘의 대표적인 예입니다.**

Harris 코너 검출 알고리즘은 이미지의 각 픽셀에서 해당 픽셀을 중심으로 하는 작은 윈도우 영역을 추출한 뒤, 

**해당 윈도우 영역의 픽셀 값 변화를 기반으로 코너 여부를 판단합니다.**

변화의 크기가 작은 경우는 평탄한 영역이라고 가정하고, 큰 경우에는 코너 또는 경계선(엣지)이라고 판단합니다.

