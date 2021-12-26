# 서버 실행 방법

```shell
$ bundle install
$ bundle exec jekyll serve
```

Gemfile의 의존성을 설치하고 로컬에서 서버를 띄워 먼저 확인할 수 있다.

<br/>

# 글 작성 방법

[체오](https://github.com/datalater/datalater.github.io)의 글을 참조하였습니다 👍

```markdown
---
title: 제목
date: 2021-12-22
published: false -> false일 경우 비공개
categories: [Parent, Child] -> 이렇게 `,`를 사용하여 구분할 경우 계층구조를 가진다.
tags: [typescript] # TAG names should always be lowercase
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1566913457/noticon/eh4d0dnic4n1neth3fui.png
  width: 100 -> 픽셀 단위로 입력한다
  height: 100 -> 픽셀 단위로 입력한다
  alt: TypeScript
---
```

- 이미지는 [noticon](https://noticon.tammolo.com)에서 찾을 수 있다. 찾을 수 없는 이미지라면 깃허브 `issue`를 활용한 이미지 링크를 사용할 수 있다.
- 비공개 글은 


<br/>

# 코드블록에 파일경로 및 이름 명시

````markdown
```javascript
const message = "Hello :)";
```

{: file="path/to/file" }
````

위와 같은 형식으로 코드블록에 파일경로와 이름을 명시할 수 있다. 주의할 점은 ` ``` `바로 밑줄에 파일명을 명시해야 한다는 점이다.

<br/>

# 배포

- `origin/main`에 push하면 깃허브 action으로 `pages-deploy.yml`이 실행 된다.
