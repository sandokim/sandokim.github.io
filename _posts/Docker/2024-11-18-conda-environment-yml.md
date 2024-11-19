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

docker로 pytorch와 cuda를 맞춘 컨테이너에서, 

![image](https://github.com/user-attachments/assets/4a157571-f1c0-43fa-a4e2-a2333ad17e35)

git clone을 통해 코드를 내려받고, `conda env create --file environment.yml`을 실행했을 때, 위와 같이 느껴지기에 무한로딩이 되는 경우가 있습니다.

기다리다보면 env 깔리긴 합니다.

![image](https://github.com/user-attachments/assets/6c093364-9f77-4655-b30e-e64a7e898f85)

깔린 가상환경을 `conda activate dngaussian`으로 activate합니다.

- `conda list`에서 `environment.yml`에 있는 `cudatoolkit=11.3`과, `pytorch=1.12.1`이 깔린 것을 확인할 수 있습니다.
- `plyfile=0.8.1` 같은 라이브러리도 버전에 맞게 설치된 것을 확인할 수 있습니다.

![image](https://github.com/user-attachments/assets/f5c80750-5902-4be1-8f95-bb57062e4191)

![image](https://github.com/user-attachments/assets/ac6f8410-4912-4714-8625-a9d881f19636)

![image](https://github.com/user-attachments/assets/1c6c7cbc-809c-4f24-9c07-bea2eea93e94)



