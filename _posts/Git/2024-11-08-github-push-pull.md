---
title: "[Github] 깃허브를 통해 코드 업데이트 및 로컬서버에서 내려받기"
last_modified_at: 2024-07-09
categories:
  - Git
tags:
  - git
  - github
  - submodules
  - modules
  - .gitmodules
  - commit hash
  - heads/main
  - 특정 커밋
  - 커밋 해시
  - 서브모듈
excerpt: "GitHub를 활용하여 코드를 공유하며 프로젝트를 진행하는 방법을 알아봅시다."
use_math: true
classes: wide
comments: true
---

### 깃허브를 활용하여 코드를 업데이트 하게 된 계기

로컬서버에서 여러명이 코딩 작업을 따로따로 하다보면 코드를 공유하고 업데이트 하는 과정에서 코드가 일치하지 않아 문제가 발생하는 경우가 있습니다.

글쓴이의 경우, 정부출연연구소와 대학원을 왔다갔다 하면서 생활하고 있기에, 두 기관에서 코드의 불일치 없이 서버의 GPU을 제대로 활용하려면 코드를 손쉽게 업데이트 할 수 있는 방법이 필요했습니다.

그러다 발견한 방법이 깃허브를 통해 코드를 업로드하고 각 로컬서버 (정부출연연구소 인공지능 서버 또는 대학원 인공지능 서버)에 내려받아서 작업하고, 작업을 완료한 코드를 깃허브에 업로드하여 코드를 업데이트하는 방법이었습니다.

좀 더 쉽게 예를 들어, 프로젝트 하나에 대한 코딩을 할 때,

**하루는 정출연 인공지능 서버에서 코드를 변경하고, 다음날은 대학원 인공지능 서버에서 코드를 변경하는 일이 잦았습니다.**

이와 같은 경우, 매번 코드를 usb에 넣어가서 코드를 옮기고 하다보니 다시 그 과정에서 설치해야하는 submodules며, 데이터 옮기는 시간이 너무 아깝다고 느껴졌습니다.

이를 한번에 해결해줬던 방법이, 깃허브를 활용하여 코드를 업데이트하는 것이었습니다.

깃허브의 장점은 제가 생각하기에 아래와 같습니다.

- 하나의 프로젝트에 대해 여러명이 동시에 코드를 수정하고 업데이트할 수 있으며, 코딩은 각 사용자가 본인의 로컬서버에서 진행할 수 있다.
- 코드를 바꾸더라도, 보통 실험한 output과 같이 용량이 큰 파일은 github에 올릴 필요가 없으므로, 업데이트 과정에서 제외할 수 있다. (다른 말로 업데이트에 추가하지 않으면 된다, `add`를 하지 않는다.)

### 깃허브로 코드를 업데이트 하는 방법

### GitHub 원격 저장소에 푸시하기 위한 명령어 순서

#### 1. 사용자 정보 설정 (처음 한 번만 필요):
```terminal
git config --global user.name "사용자 이름"
git config --global user.email "사용자의 이메일"
```

#### 2. 원격 저장소 추가 (처음 한 번만 필요):
```terminal
git remote add origin https://github.com/sandokim/프로젝트명.git
```

#### 3. 원격 브랜치 목록 확인:
먼저, 원격 저장소에 어떤 브랜치가 있는지 확인해보세요. 

다음 명령어를 사용하여 원격 브랜치 목록을 확인할 수 있습니다:

```terminal
git fetch origin
git branch -r
```

#### 4. 현재 브랜치와 원격 브랜치 연결 (처음 한 번만 필요):
```terminal
git push --set-upstream origin main
```

### 파일 수정된 것들을 일일히 github repository에 반영 하는 법

- git status에서 staging이 안된 파일들을 확인합니다.
- 이 파일들을 staging에 올리기 위해 add를 먼저하고,
- 모든 변경 사항을 commit한 후,
- 원격저장소로 push합니다.

```terminal
git status
git add train_tps.py
git commit -m "Update train_tps.py"
git push origin main
```

### 더 간단하게 수정된 파일들이 알아서 staging되도록 하는 법
Git에서 변경 사항을 자동으로 스테이징(staging)하는 방법은 기본적으로 제공되지 않지만, git commit 명령어에 -a 플래그를 사용하면 수정된 파일을 자동으로 스테이징할 수 있습니다. 이 방법은 추적되고 있는 파일에 대해서만 작동합니다. 즉, 새로 추가된 파일이나 서브모듈의 변경 사항은 자동으로 스테이징되지 않습니다.

#### 파일 수정된 것을 한번에 github repository에 반영 하는 법

- git commit 시 -a를 붙입니다.
- 원격저장소로 push합니다.

```terminal
git status
git commit -a -m "Update train_tps.py"
git push origin main
```


감사합니다.



