---
title: "[Git] Github repository, git pull"
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


### Pull request(PR)는 master에 갈 수 있도록 허락해달라는 요청입니다.



### Merge pull request는 엄청난 책임이 따릅니다.

pull request를 하는 경우, master branch와 합쳐집니다.
