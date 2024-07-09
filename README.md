# [My Blog](https://sandokim.github.io/)

#### Toggle 목록 추가 방법 --> `_includes/nav_list` 수정 & `_pages/categories/ ~.md` 수정
- Toggle 목록 추가 후 post 작성시 다음과 같은 포멧으로 작성
```html
2024-06-19-category_name-xxx.md
```

#### 전체 페이지 수정 방법 --> `_config.yml` 수정

#### 글씨크기 수정 --> `_sass/minimal-mistakes/_reset.scss` 

```css
* { box-sizing: border-box; }

html {
  /* apply a natural box layout model to all elements */
  box-sizing: border-box;
  background-color: $background-color;
  font-size: 16px;

  @include breakpoint($medium) {
    font-size: 16px; // Default 18px
  }

  @include breakpoint($large) {
    font-size: 16px; // Default 20px
  }

  @include breakpoint($x-large) {
    font-size: 16px; // Default 22px
  }
```

- 첫번째가 기본 폰트 크기(16px)로 일반적으로 모바일에서 볼 수 있는 크기입니다.
- 나머지 세가지 크기(18,20,22px)는 PC에서 윈도우의 크기에 따라 medium, large, x-large로 구분하고 그에 맞추어 폰트의 사이즈를 변경하도록 되어 있습니다.
- 따라서 해당부분에 원하는 만큼의 크기로 수정하면 적용됩니다.

#### [깃허브 블로그 댓글 기능 추가하기(minimal-mistakes)](https://0530hwi.github.io/custom_blog/Custom_GitBlog5/)

- `_config.yml` 파일에서 `utterances`, `repository`, `theme`, `issue_term`를 설정합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/4fc8941a-7812-40db-a749-25b132f51e41)

