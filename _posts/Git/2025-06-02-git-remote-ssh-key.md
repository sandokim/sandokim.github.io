---
title: "[Git] Github pull 이후 코드 변경후 로컬 컴퓨터에서 push 할 수 있도록 셋업하기"
last_modified_at: 2024-11-20
categories:
  - Git
tags:
  - git
  - github
  - github repository
  - git pull
  - git remote -v
  - git ssh
  - git ignore
excerpt: "Github 로컬 컴퓨터에서 pull, push 하도록 셋업하기"
use_math: true
classes: wide
comments: true
---

#### 로컬 프로젝트 폴더에서 `git init`으로 초기화하고 원격 저장소 `.git`과 연결해줍니다.
```terminal
git init
git remote add origin https://github.com/sandokim/gaussian-splatting.git
```

#### 로컬 저장소 수정 내용을 깃 원격저장소에 push하기 위해서는 ssh가 필요합니다.
```terminal
ssh-keygen -t ed25519 -C "YourEmail@example.com"
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
cat ~/.ssh/id_ed25519.pub
```
이 출력된 내용을 `GitHub > Settings > SSH and GPG keys > New SSH key`에 붙여넣기 하시면 됩니다.

#### SSH 키를 생성했지만 실제로는 여전히 https:// 주소를 사용하고 있어서, 매번 Username과 Password를 입력하라는 메시지가 뜰 수 있습니다.
```terminal
git remote -v
origin  https://github.com/sandokim/gaussian-splatting.git (fetch)
origin  https://github.com/sandokim/gaussian-splatting.git (push)
```

#### ✅ 해결 방법: 원격 저장소 URL을 SSH 방식으로 변경
다음 명령어를 실행해서 기존 https:// 원격 저장소 URL을 SSH URL로 변경하면, 더 이상 인증을 반복하지 않아도 됩니다 (SSH 키를 이미 설정했기 때문에):
```terminal
git remote set-url origin git@github.com:sandokim/gaussian-splatting.git
git remote -v
```
출력이 아래처럼 나오면 성공입니다:
```terminal
origin  git@github.com:sandokim/gaussian-splatting.git (fetch)
origin  git@github.com:sandokim/gaussian-splatting.git (push)
```

#### Git으로 push하기 전에 Tip
- output 폴더와 같이 용량이 큰 파일은 .gitignore에서 제외시킵니다.
  ![image](https://github.com/user-attachments/assets/62306afc-aac1-4efd-8b10-a3785e37e521)

#### 이제 로컬 서버에서 코드를 변경하고 원격저장소로 push할 수 있습니다.
```terminal
git add . # 불필요한 용량 큰 output 파일은 .gitignore에서 제외시키기
git commit -m "initial commit"
git push
```

감사합니다.


