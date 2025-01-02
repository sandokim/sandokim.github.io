---
title: "[Registration] Edge, Corner 정의"
last_modified_at: 2025-01-02
categories:
  - Registration
tags:
  - Registration
  - 정합
  - edge
  - corner
excerpt: "이미지에서 일반적으로 가장 중요하고 구분하기 쉬운 특징점들은 코너와 엣지입니다."
use_math: true
classes: wide
comments: true
---

> Reference

> [특징 추출을 이용한 이미지 매칭 기술의 최근 동향 분석](https://ksbe-jbe.org/xml/37415/37415.pdf)

이미지에서 일반적으로 가장 중요하고 구분하기 쉬운특징점들은 코너와 엣지이다. 

- 엣지란 한 방향 이상에서 픽셀 값의 변화가 급격하게 나타나는 점입니다.

- 코너란 두방향 이상에서 변화가 급격하게 나타나는 점입니다.

이러한 변화가 일어나는 지점은 일반적으로 이미지 내부 물체의 경계선이나 모서리와 관련되어 있기 때문에 많은 이미지 매칭 알고리즘에서 코너나 엣지를 특징점으로 사용 한다.
