---
title: CSS 최적화
date: 2022-01-09 10:30:00 +0900
categories: [웹 성능 최적화]
tags: [CSS 최적화]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---

다른 요소들과 마찬가지로 CSS도 최적화를 할 수 있다.

![image](https://user-images.githubusercontent.com/52060742/148664679-08dd2c43-f267-4cae-b359-8d63573e0e72.png)

개발자도구의 `커버리지`탭을 보면 각 요소들에 대한 사용량을 보여준다. (빨간색 그래프가 사용하지 않는 부분임)

<br/>

해당 내용을 더 자세하게 보기 위해선 위에 `소스`탭을 켜두고 자세히 보고자 하는 요소를 아래 `커버리지`탭에서 클릭하면 확인할 수 있다.

여기도 마찬가지로 빨간색과 파란색으로 표시된다.

![image](https://user-images.githubusercontent.com/52060742/148664717-0c339d49-fd39-4c2d-97ea-a69ee43e3e63.png)

<br/>
<br/>

## 사용하지 않는 CSS 코드를 줄이는 방법

사용하지 않는 CSS 코드를 줄이는 방법은 여러가지가 있는데 먼저 [PurgeCSS](https://purgecss.com/)를 사용하는 방법에 대해 알아보겠다. 

친절하게도 [공식사이트](https://purgecss.com/CLI.html#installation)에 설명이 되어 있다. 하나씩 따라가보자

1. `yarn add -D purgecss`
2. purge를 실행하는 스크립트 명령어 추가
  - `"purge": "purgecss --css ./build/static/css/*.css --output ./build/static/css/ --content ./build/index.html ./build/static/js/*.js"`
  - --css: 어떤 css 파일들을 대상으로 할 건지
  - --output: 결과물을 내보내는 디렉토리 (지금은 해당 결과물을 기존 파일에 덮어 씌운다는 의미이다)
  - --content: 어떤 컨텐츠와 비교할 건지

<br/>
<br/>

이렇게 끝나면 물론 좋겠지만 문제가 발생할 수도 있다. 그 문제는 `purgecss`를 실행 시키고 나서 스타일이 깨질 수 있기 때문이다. 예를 들어 `tailwindcss`를 사용할 경우 `lg:md-8`과 같은 클래스 명을 사용할 수 있다. 하지만 purge가 클래스명을 분석할 때 `lg:md-8`을 통째로 분석하는 것이 아니라 `lg`, `md-8` 각각 따로 분석할 수도 있기 때문에 해당 부분에 css가 제대로 적용되지 않을 수 있다.

<br/>

이 문제를 해결하기 위해선 [configuration](https://purgecss.com/configuration.html) 문서를 살펴보자

```ts
interface UserDefinedOptions {
  content: Array<string | RawContent>;
  css: Array<string | RawCSS>;

  // 이 부분이 우리가 필요한 부분이다
  defaultExtractor?: ExtractorFunction;

  extractors?: Array<Extractors>;
  fontFace?: boolean;
  keyframes?: boolean;
  output?: string;
  rejected?: boolean;
  stdin?: boolean;
  stdout?: boolean;
  variables?: boolean;
  safelist?: UserDefinedSafelist;
  blocklist?: StringRegExpArray;
}
```

<br/>

1. purgecss.config.js 파일을 생성한다
    ```js
    module.exports = {
    // content는 우리가 지정한 content 파일을 의미한다.
    // 어디까지 클래스 네임으로 인정할 것인지 정규표현식으로 설정해야 한다.
    // \w : [0-9a-zA-Z_]
    defaultExtractor: content => content.match(/[\w\:\-]+/g) || [],
    };
    ```

2. config 파일을 추가했기에 스크립트 명령어로 아래와 같이 수정한다.
  - `"purge": "purgecss --css ./build/static/css/*.css --output ./build/static/css/ --content ./build/index.html ./build/static/js/*.js --config ./purgecss.config"`

<br/>
<br/>

**결과**

![image](https://user-images.githubusercontent.com/52060742/148665709-e5bec7d0-951e-43a7-bd0c-2178a18fa03f.png)

Passed 😁
