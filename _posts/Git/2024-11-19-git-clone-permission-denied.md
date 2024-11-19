---
title: "[Github] git@github.com: Permission denied (publickey) 에러 해결"
last_modified_at: 2024-07-09
categories:
  - Git
tags:
  - git
  - github
  - submodules
  - git clone
excerpt: "git@github.com: Permission denied (publickey) 에러 해결"
use_math: true
classes: wide
comments: true
---

# git@github.com: Permission denied (publickey)

### git push 또는 git pull 할 때 발생하는 에러

```terminal
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.

Please make sure you have the correct access rights
and the repository exists.
```

### 새로 ssh key를 만들어줘야 합니다.

#### ssh key 생성
```terminal
 ssh-keygen -t rsa -C "{본인의 깃허브 이메일}"
``` 

위 명령어를 입력합니다.

1. ssh key 저장 위치 지정 (엔터하면 기본 지정된 위치에 저장) 

2. 비밀번호 지정 (엔터해서 넘기기 가능)


#### ssh키 확인하기

```terminal
cat ~/.ssh/id_rsa.pub
```

![image](https://github.com/user-attachments/assets/7883b4b6-89bb-414f-bb42-77b731f392f6)

위 명령어로 나온 키를 GitHub에 등록합니다.

#### Settings -> SSH and GPC Keys -> New SSH key -> Key 부분에 위에서 확인한 ssh키 복붙하기

![image](https://github.com/user-attachments/assets/6762f45f-12dd-4cd9-b524-d94c72b3cbc8)

