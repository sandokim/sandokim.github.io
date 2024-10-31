---
title: "[Docker] Docker container attach to VSCode"
last_modified_at: 2024-11-01
categories:
  - Docker
tags:
  - docker
  - container
  - attach to VScode
  - docker group
excerpt: "Docker container를 VScode에 attach하는 법을 알아봅시다."
use_math: true
classes: wide
comments: true
---

![image](https://github.com/user-attachments/assets/7c28b5ff-22f3-4fe0-8ba2-41e352bb218b)

![image](https://github.com/user-attachments/assets/eb649dd9-8a96-4b41-87b4-0cbac4b8721d)

Running중인 docker container를 VScode에 atttach할 때 다음과 같은 에러가 발생할 수 있습니다.

![image](https://github.com/user-attachments/assets/ef8c6185-f2a6-4df5-aecc-3ca9af898893)

### docker 그룹에 현재 사용자 추가를 해줍니다.

```terminal
sudo usermod -aG docker $USER
# 터미널 재시작 필요
```

![image](https://github.com/user-attachments/assets/56eef740-b92a-48f3-8a4a-abb758d6646d)

### 정상적으로 VScode에 실행중인 docker container를 attach 하였습니다.

![image](https://github.com/user-attachments/assets/98af20f8-97ae-43ab-9a27-f3bc03c8ee9a)

이제 이 container에서 새로운 독립적인 컴퓨터에서 작업하듯 코딩할 수 있습니다.

Extension으로 python debugger도 설치해줍니다.

![image](https://github.com/user-attachments/assets/76575c14-b845-4d6d-ab2e-b3294a5f26b3)

이 세팅을 통해 앞으로는 VScode에 attach한 docker container 내에서 환경변수 충돌없이, 디버깅도 하며 개발을 할 수 있습니다.

감사합니다.
