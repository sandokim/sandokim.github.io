---
title: "[Docker] Docker container attach to VSCode"
last_modified_at: 2024-11-01
categories:
  - Docker
tags:
  - docker
  - container
  - attach to VScode
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
