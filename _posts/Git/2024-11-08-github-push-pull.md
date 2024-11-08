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

# 깃허브를 활용하여 코드를 업데이트 하게 된 계기

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

# 깃허브로 코드를 업데이트 하는 방법

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


# 실전예제

### 깃허브 프로젝트 파일 저장소의 코드들을 로컬서버에서 작업한 코드들들로 업데이트하는 방법

`git status`를 입력하면 아래와 같이 modified된 파일들이 나열되고, untracked된 것들은 따로 표시가 됩니다. 

![image](https://github.com/user-attachments/assets/15d4b28b-4306-4b95-9b7f-6e31fc8acf9a)

변경된 파일들을 커밋하기 위해 다음과 같은 단계를 따르면 됩니다:

1. 먼저 __pycache__ 파일들과 불필요한 파일들을 .gitignore에 추가:

- `.gitignore`은 현재 프로젝트 경로에 `.gitignore` 이름으로 파일을 생성해주면 됩니다.
- `.gitignore`에 불필요한 파일들을 다음과 같이 추가해줍니다.

```bash
__pycache__/
*.pyc
.vscode/
```

2. 현재 변경사항을 확인하고 커밋할 파일들만 선택:
```bash
# 삭제된 파일들 git에서 제거
git rm graphics_utils.py
git rm scripts/copy_mask_dtu_r4_3v.sh
git rm scripts/copy_mask_dtu_r4_6v.sh
git rm scripts/copy_mask_dtu_r4_9v.sh

# 변경된 주요 파일들 추가
git add arguments/__init__.py
git add scene/__init__.py
git add scene/gaussian_model.py
git add utils/camera_utils.py
git add utils/general_utils.py
git add utils/graphics_utils.py
git add utils/pose_utils.py
git add utils/tps_utils.py
git add train_tps_deform_all.py
git add scripts/run_*.sh
```

- Change할 파일들(기존 코드관련 파일을 삭제하였거나, 또는 새로 만든 코드관련 파일들)만 위처럼 `git rm 삭제된 파일`, `git add 변경된 파일, 혹은 새로 추가된 파일`을을 `terminal`에 입력해줍니다.
- `git status`로 확인하면 Change가 적용되어 깃허브로 commit될 파일들만 초록색 목록으로 다시 나오게 됩니다.

![image](https://github.com/user-attachments/assets/59912086-4843-433c-b356-7ae863463c91)

3. 커밋 메시지와 함께 커밋합니다.
   
```bash
git commit -m "Update TPS deformation with margin-based filtering and code reorganization"
```

![image](https://github.com/user-attachments/assets/9df43f43-49c4-4bae-b64e-edf2cef16145)

4. 변경사항을 원격 저장소에 push합니다:
```bash
git push origin main
```

#### 새로 추가된 파일을 제대로 커밋하려면 `git status`에서 Untracked files 목록에서 확인하고 `add` 해야합니다.

- 아래와 같이 새로 추가된 scripts 관련 파일 3개만 깃허브 저장소에 파일로써 추가되도록 하려면,
- `add`를 다음과 같이 해주고,
  
![image](https://github.com/user-attachments/assets/9ee0d892-9041-4d56-bd43-19bc2b4a9cce)

- 이전과 동일하게 `commit`하고, `push`하면 됩니다.

![image](https://github.com/user-attachments/assets/7801b6b3-70ef-4d8a-83c8-e9d837cd8289)

### 만약 위 과정을 거치지 않고, 모든 변경사항을 한 번에 스테이징하려면 다음과 같이하면 됩니다:
```bash
git add -A
git commit -m "Update TPS deformation with margin-based filtering and code reorganization"
git push origin main
```

단 `git add -A`의 경우 untracked인 파일들은 staging되지 않기 때문에, untracted 파일들까지 더해서 깃허브에 업로드하여 코드를 업데이트 하려면, 이 부분을 따로 해줘야합니다. 

다만, 경험삼 때로는 untracked 파일들은 output과 같이 용량이 크고, submodules와 같이 용량은 크면서 코드 작업동안 바꾸지 않는 것들은 untracked 파일들로 두는게, git push, pull에서 코드를 올리고 내려받을 때 시간적으로 효율적입니다.

### 업데이트 된 깃허브 저장소 코드들을 로컬서버에서 내려받는 방법

```bash
git pull origin main
```

감사합니다.





