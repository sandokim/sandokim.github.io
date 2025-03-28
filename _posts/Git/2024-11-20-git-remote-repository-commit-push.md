---
title: "[Git] Github repository, git commit, git push"
last_modified_at: 2024-11-20
categories:
  - Git
tags:
  - git
  - github
  - github repository
  - git init
  - git commit
  - git push
  - submodules
  - modules
  - .gitmodules
  - commit hash
  - heads/main
  - 특정 커밋
  - 커밋 해시
  - 서브모듈
  - 깃허브 레퍼지토리
excerpt: "Git Tips"
use_math: true
classes: wide
comments: true
---

> [깃, 깃허브 한번에 이해시켜드리고 포트폴리오 올리는 법까지 알려드림. 15분안에 당신은 Github 전문가가 됩니다](https://www.youtube.com/watch?v=lelVripbt2M)

### 먼저 git의 환경 설정을 해줘야합니다.

현재 서버에서 다음을 추가해줍니다.

```
git config --global user.name "sandokim"
git config --global user.email "comsicomet@gmail.com" # 깃허브 가입시 사용한 이메일
```

`git config --list`로 확인해줍니다.

<img width="437" alt="image" src="https://github.com/user-attachments/assets/0fa0c329-548c-42bc-a62a-4f3048c36431">

### 현재 작업중인 소스코드를 깃허브에 올리기 위해, terminal에서 `git init`으로 초기화 해줍니다.

<img width="464" alt="image" src="https://github.com/user-attachments/assets/3f9bfda3-9bf1-47dd-8bda-c7ba512a3cbf">

### 아래와 같이 로컬 프로젝트에서 파일 일부(i.e. index.html)만 올리겠다면, 파일 이름을 명시해줍니다.

```terminal
git add index.html
```

실무에서는 전체 파일을 올리는 것을 자주 쓰게 됩니다. `git add .`으로 하면 됩니다.

```terminal
git add . 
```

<img width="510" alt="image" src="https://github.com/user-attachments/assets/ae4ef465-0df0-4d22-8814-ee63b5f85d3e">

### `git commit`은 히스토리를 만드는 것입니다.

git commit에서 커밋 메시지에 해당하는 "first commit"은 히스토리 이름이라고 봐도 됩니다.

```terminal
git commit -m "first commit"
```

커밋 메시지를 위과 같이 입력하고, github에 `git push origin master`로 push했다면

<img width="501" alt="image" src="https://github.com/user-attachments/assets/c1413061-0f40-4999-bca5-0e2f4f775124">

깃허브에는 아래와 같이 커밋 메시지가 표시됩니다. (로컬 프로젝트와 깃허브가 연결된 상태일 때, 연결고리 만드는 법은 바로 아래 설명에 있습니다.)

<img width="847" alt="image" src="https://github.com/user-attachments/assets/8bc9005b-dc58-428c-b679-13a1e6364409">


### 히스토리를 깃허브에 보내는 법

본인의 로컬 프로젝트랑 깃허브랑 연결고리가 있어야 합니다. 

연결고리는 아래와 같이 처음 깃허브 repository를 만들 때 생성된 아래 커맨드 라인을 그대로 복사하고

<img width="1166" alt="image" src="https://github.com/user-attachments/assets/66b1d65c-29e5-4e0a-8f45-27f213fb4774">

프로젝트 터미널에 붙여넣습니다.

```terminal
git remote add origin https://github.com/sandokim/STAR-GS.git
```

이 커맨드는 `https://github.com/sandokim/STAR-GS.git`이 repository로 내 소스코드를 보낸다라는 뜻입니다. (연결고리가 생성됩니다, 엔터를 누르고 아무일도 안일어나면 성공입니다.)

<img width="709" alt="image" src="https://github.com/user-attachments/assets/6c79e3a7-6a5a-456d-bde7-3a1478e721ce">

연결이 제대로 됐는지 확인하고 싶으면 `git remote -v`로 확인할 수 있습니다.

<img width="397" alt="image" src="https://github.com/user-attachments/assets/7fc9f9b3-233d-49ff-9bf7-01a493fea1bd">

git commit한 내용은 push로 깃허브로 보낼 수 있습니다.

```terminal
git push origin master
```

이때 Username과 Password를 입력하라고 나오는데, 

<img width="442" alt="image" src="https://github.com/user-attachments/assets/546bed2c-1317-4c95-92be-4c65e3a5ce0e">

- Username은 본인의 깃허브 유저이름을 넣어주고,
- Password는 Personal access tokens (PAT)를 만들어서 복붙해줍니다.

<img width="1060" alt="image" src="https://github.com/user-attachments/assets/214ff4e5-098a-47ac-85b6-24c28cf78526">

이를 통해 다음과 같이 정상적으로 github에 push가 되었습니다.

<img width="460" alt="image" src="https://github.com/user-attachments/assets/81f91a19-9276-4d45-9996-e2a571cd767b">

### 로컬 프로젝트에서 Push가 완료된 깃허브 페이지 모습

<img width="823" alt="image" src="https://github.com/user-attachments/assets/9c2c5089-f109-4b07-a4db-60693289aafe">


### Push가 완료된 후, 로컬 프로젝트에서 코드를 바꾸고, 깃허브에 계속 업데이트 하는 방법

- `git init`은 이미 했으므로 할 필요 없습니다.
- `git add . `
- `git status`로 수정한 파일을 확인합니다.
- `git commit -m "second commit"` 깃커밋으로 히스토리를 만들어 줍니다.
- `git push origin master`


