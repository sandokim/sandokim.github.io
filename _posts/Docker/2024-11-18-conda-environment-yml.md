---
title: "[Conda] conda env create --file environment.yml"
last_modified_at: 2024-11-18
categories:
  - Docker
tags:
  - docker
  - conda
  - conda env
  - environment.yml
  - conda env create --file environment.yml
  - 무한로딩
excerpt: "conda env create --file environment.yml"
use_math: true
classes: wide
comments: true
---

### `conda env create --file environment.yml`에서 무한로딩 되는 경우 해결법

![image](https://github.com/user-attachments/assets/2fe5701b-bf05-4c7b-8334-1745d9972a59)

docker로 pytorch와 cuda를 맞춘 컨테이너에서, git clone을 통해 코드를 내려받고, `conda env create --file environment.yml`을 실행했을 때, 위와 같이 무한로딩이 되는 경우가 있습니다.
