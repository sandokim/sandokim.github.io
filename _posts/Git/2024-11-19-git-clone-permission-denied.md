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

![image](https://github.com/user-attachments/assets/53065e9c-6396-4050-993d-279dbb95d7f9)

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

#### Add SSH Key를 눌러줍니다. (Title 입력할 필요는 없습니다.)

![image](https://github.com/user-attachments/assets/c1e7e9cb-c7c6-4b94-935f-32c2c3ed0447)

#### 다시 git clone시 정상적으로 clone이 됩니다.

![image](https://github.com/user-attachments/assets/4aeecda8-15c8-4092-9060-8ecc46b939ff)

-단 중간에 `git config --global --add safe.dircetory `와 같은 커맨드를 추가하라고 에러가 발생하는데 그대로 커맨드 창에 복붙하여 해결한 뒤, 생성됐던 폴더는 제거하고, 다시 clone하면 됩니다.
