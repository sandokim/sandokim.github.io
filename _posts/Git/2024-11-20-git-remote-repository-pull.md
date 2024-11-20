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

### 신입사원을 위한 branch를 따로 만들어줘야 합니다.



### Pull request(PR)는 다른 branch에서 작업한 코드가 master에 합쳐질 수 있도록 허락해달라는 요청입니다.


### Merge pull request는 엄청난 책임이 따릅니다.

Merge pull request를 하는 경우, pull request를 한 다른 branch가 master branch와 합쳐집니다.



### 현재 본인이 작업중인 코드와 협업중인 다른 사용자에 의해 바뀐 Github의 master branch 코드가 동기화되도록 해줘야 합니다.

- `git add .`
- `git commit -m "second commit"`
- `git pull origin master`로 github master branch에서 업데이트 된 내용을 현재 본인이 작업한 코드와 동기화 해줍니다.
- `git push origin master`로 github master branch를 업데이트 해줍니다.

