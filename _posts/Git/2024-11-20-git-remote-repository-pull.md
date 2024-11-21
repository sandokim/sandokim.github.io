---
title: "[Git] Github repository, git pull, 깃으로 협업하는 법"
last_modified_at: 2024-11-20
categories:
  - Git
tags:
  - git
  - github
  - github repository
  - git pull
  - submodules
  - modules
  - .gitmodules
  - commit hash
  - heads/main
  - 특정 커밋
  - 커밋 해시
  - 서브모듈
  - 깃허브 레퍼지토리
excerpt: "Github repository를 로컬 프로젝트에 내려받기"
use_math: true
classes: wide
comments: true
---

> [회사에서 개발자들은 어떻게 일할까? 회사에서 쓰는 실전 깃 깃허브 한방에 끝내기! 15분만 투자해라 님들의 회사생활이 편해짐](https://www.youtube.com/watch?v=cwC8t9dno2s)
 
### master는 최종 branch이므로 개발할 때, 함부로 master에 push를 하면 안됩니다.

```terminal
git push origin master
```

> [[Git] Github repository, git commit, git push](https://sandokim.github.io/git/git-remote-repository-commit-push/)

이전 포스트를 참고하여 Github repository를 만든 상태에서 협업하는 법을 진행해봅시다.

### git clone을 먼저 해줍니다.

```terminal
git clone https://github.com/sandokim/STAR-GS.git {option 복사할 경로의 폴더이름}
```

<img width="828" alt="image" src="https://github.com/user-attachments/assets/e6137c06-13b0-4053-ad46-47a6943bbf98">

```terminal
cd STAR-GS
code .
```

### 코드를 수정합니다. git clone할 때 이미 세팅정보도 같이와서 init, remote는 필요없습니다.

```terminal
git add .
git commit -m "freshman first commit"
```

### `git push origin master`을 하지말고, 신입사원을 위한 branch를 따로 만들어줘야 합니다.

```terminal
git checkout -b freshman
```

<img width="505" alt="image" src="https://github.com/user-attachments/assets/456c1b84-ad60-4c0e-8ed2-7bd20e13d02c">

이제 freshman branch로 push합니다.

<img width="490" alt="image" src="https://github.com/user-attachments/assets/c7b26067-75f5-418f-b731-cdbbc9877b66">

새로운 freshman branch가 생긴 것을 확인할 수 있습니다.

<img width="524" alt="image" src="https://github.com/user-attachments/assets/1d8e5dd0-801c-4a31-8c76-17c039a158c5">

pull request를 해봅시다.

<img width="524" alt="image" src="https://github.com/user-attachments/assets/bcf69d6b-4168-430d-b0f9-74d403a37448">

<img width="790" alt="image" src="https://github.com/user-attachments/assets/9534d656-6961-4116-bea1-8bc023e4ba7a">

그러면 아래와 같이 Pull request가 생깁니다.

<img width="787" alt="image" src="https://github.com/user-attachments/assets/359e2dda-9f79-46ad-8a01-1ac9ee863977">

### Pull request(PR)는 다른 branch에서 작업한 코드가 master에 합쳐질 수 있도록 허락해달라는 요청입니다.

다시 Senior 입장에서 Pull request를 살펴봅시다.

<img width="788" alt="image" src="https://github.com/user-attachments/assets/9e0d5881-ce6b-4078-81ef-74359af011a7">

<img width="781" alt="image" src="https://github.com/user-attachments/assets/1c4fea6a-fa3b-4121-acb1-f5cbca4a231e">

<img width="789" alt="image" src="https://github.com/user-attachments/assets/14050da4-d3da-426f-b253-5987be7be0fd">

필요하다면 바뀐 코드에 대해 코멘트를 남겨줄 수 있습니다.

<img width="796" alt="image" src="https://github.com/user-attachments/assets/f0a82959-da78-48d3-97e3-e526516bb7af">

아래와 같이 코멘트가 표시됩니다.

<img width="786" alt="image" src="https://github.com/user-attachments/assets/b2b2274d-9e0c-402d-a101-5c68c4b1dd1d">

코드가 괜찮다고 생각된다면 이제 merge를 해줍니다.

### Merge pull request는 엄청난 책임이 따릅니다.

Merge pull request를 하는 경우, pull request를 한 다른 branch가 master branch와 합쳐집니다.

<img width="785" alt="image" src="https://github.com/user-attachments/assets/bb92bc8a-4613-4803-a80c-591d2eca5556">

<img width="785" alt="image" src="https://github.com/user-attachments/assets/000ce7f8-cab8-44df-b67f-481ff0128df3">

이를 통해 최종 master branch 프로덕트가 완성됩니다.

### 현재 본인이 작업중인 코드와 협업중인 다른 사용자에 의해 바뀐 Github의 master branch 코드가 동기화되도록 해줘야 합니다.

- `git add .`
- `git commit -m "second commit"`
- `git pull origin master`로 github master branch에서 업데이트 된 내용을 현재 본인이 작업한 코드와 동기화 해줍니다.
- `git push origin master`로 github master branch를 업데이트 해줍니다.

