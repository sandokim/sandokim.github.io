# [My Blog](https://sandokim.github.io/)

### Toggle 목록 추가 방법 --> `_includes/nav_list` 수정 & `_pages/categories/ ~.md` 수정
- Toggle 목록 추가 후 post 작성시 다음과 같은 포멧으로 작성
```html
2024-06-19-category_name-xxx.md
```

### 전체 페이지 수정 방법 --> `_config.yml` 수정

### 글씨크기 수정 --> `_sass/minimal-mistakes/_reset.scss` 

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

-------

### [깃허브 블로그 댓글 기능 추가하기(minimal-mistakes)](https://0530hwi.github.io/custom_blog/Custom_GitBlog5/)

#### Utterances 깃허브 앱을 설치합니다.
- 댓글을 관리할 Repository로 `owner/owner.github.io`를 선택합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/0c281be4-03df-4fbf-981f-3b65d194bf1e)

- `owner.github.io`의 `_config.yml` 파일에서 `utterances`, `repository`, `theme`, `issue_term`를 설정합니다.
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/f84c1955-1eea-4aa0-82cc-380d424ca506)

- pathname 지정 시 유의사항
  - pathname은 포스트 `.md`파일의 이름으로 연결됩니다.
  - 예를 들어, 2020-07-23-postname.md라는 새 포스트를 작성하면, 블로그에서 해당 포스트의 주소는 `owner.github.io/postname`으로 연결됩니다.
  - 이미 발행한 포스트 `.md` 파일의 이름을 변경하면 `pathname`과 댓글이 연결되어 있으므로, 이전에 달렸던 댓글은 사라집니다.
  - 이 경우, blog repository에서 issue를 변경해야 합니다.

#### 블로그 포스트 시 `comments: true`로 설정합니다.
  
![image](https://github.com/sandokim/sandokim.github.io/assets/74639652/fee97d47-4fd8-4dac-91c9-17c8bf18e8a3)

#### `_sass/minimal-mistakes/_page.scss`에 다음 코드를 아무 곳에나 추가하여 글씨 크기를 조정합니다.
```css
.utterances {
  max-width: 100% !important;
}
```

-------
