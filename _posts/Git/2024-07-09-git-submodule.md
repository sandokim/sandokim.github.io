---
title: "[Git] Submodule installation"
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
excerpt: "GitHub에서 메인 레포지토리의 서브모듈을 특정 커밋(커밋 해시)에 맞게 설치하는 법을 알아봅시다."
use_math: true
classes: wide
comments: true
---

실전예제로 바로 이해해봅니다.

## 3dgs github에는 submodules까지 한번에 설치할 수 있도록 repository를 clone할 때, `--recursive` 인자를 줍니다.
- `--recursive`인자를 주고 `git clone`을 진행하면, `.gitmodules`에 존재하는 타 git repository에 존재하는 module도 한번에 clone이 되어 설치가 됩니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/065e43ec-8573-4cb1-9761-a173cc715148)

### `.gitmodules`를 참고해보면 `submodule` 다음 3가지가 존재합니다.
- **.gitmodules 파일**: **서브모듈의 경로와 URL 정보만을 포함**하고 있습니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/8da20a81-a0e8-40a5-8034-cbb4f4bce46b)

### 커밋 해시(Commit Hash): 특정 커밋을 식별하는 고유한 문자열. 서브모듈이 특정 커밋에 고정될 때 사용됩니다.
- `@` 기호 뒤에 오는 문자열이 커밋 해시(Commit Hash)입니다.
- `@` 기호 뒤에 오는 문자열은 서브모듈이 고정된 특정 커밋 해시를 의미합니다.
- 서브모듈의 특정 커밋 해시는 **메인 레포지토리**의 .git 디렉토리 내에 저장됩니다.
- 메인 레포지토리의 인덱스 파일: 서브모듈이 고정된 커밋 해시 정보를 포함하고 있습니다. 이는 메인 레포지토리의 .git/modules 디렉토리와 .git/config 파일에서 관리됩니다.
- **서브모듈이 특정 커밋 해시로 고정된 상태를 확인하기 위해 메인 레포지토리**의 .git/modules 디렉토리를 살펴보거나, **git submodule status 명령어를 사용**할 수 있습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/db918999-bf7c-4f53-883b-e92e64f93a94)

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/abb6015c-1e9e-48d3-b645-e6650c9734dc)

```python
git submodule status
```

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/59fb47aa-edca-4b49-ba1c-c42a258934ea)

- 여기서 각 해시 값은 서브모듈이 현재 고정된 커밋을 나타냅니다.
- **`git submodule status`로 확인한 설치된 서브모듈의 커밋 해시**가 **3dgs 메인 레포지토리의 `@의 뒤의 문자열`인 커밋 해시**와 같음을 알 수 있습니다.

  
### `--recursive`로 설치하면 메인 레포지토리에서 각 submodule의 커밋해시에 맞게 설치되나, 만약 submodule의 커밋해시가 submodules의 git repository에 존재하지 않을 경우 설치가 되지 않았습니다.
- 이때는, 미리 로컬 컴퓨터에 설치해놓았던 submodules 폴더를 설치하려는 해당 서버 폴더에 복사 붙여넣기 하고, `pip install -e .`인 개발모드로 설치하였습니다.
- `submodules/diff-gaussian-rasterization`, `submodules/simple-knn`을 `pip install -e .`로 설치하고, `git submodule status`로 확인한 결과 submodule의 커밋해시가 메인 레포지토리의 커밋해시 @의 뒤의 문자열과 같아 문제가 없음을 확인하였습니다.

![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/59fb47aa-edca-4b49-ba1c-c42a258934ea)

### `(heads/main)` = `main` 브랜치의 최신 커밋
- `(heads/main)`은 서브모듈이 현재 가리키고 있는 커밋이 main 브랜치의 최신 커밋임을 의미합니다. 이 정보가 출력되는 경우, 해당 서브모듈이 main 브랜치를 트래킹하고 있음을 나타냅니다.

### `(heads/main)`이 없음 = 특정 커밋 고정
- `(heads/main)`와 같은 브랜치 정보가 없는 경우, 서브모듈이 특정 브랜치를 트래킹하고 있지 않거나, 단순히 특정 커밋에 고정되어 있음을 의미합니다.
- 예를 들어:
  - `SIBR_viewers`: 특정 브랜치 정보 없이 **특정 커밋 해시 `4ae964a267cd7a844d9766563cf9d0b500131a22`에 고정**되어 있습니다.
  - `ubmodules/simple-knn`: 특정 브랜치 정보 없이 **특정 커밋 해시 `44f764299fa305faf6ec5ebd99939e0508331503`에 고정**되어 있습니다.

### 요약
- 서브모듈의 특정 커밋 해시 정보는 메인 레포지토리의 .git 디렉토리 내에서 관리됩니다.
- 이를 확인하기 위해서는 git submodule status 명령어를 사용하거나, 메인 레포지토리의 .git/modules 디렉토리를 살펴보아야 합니다.




