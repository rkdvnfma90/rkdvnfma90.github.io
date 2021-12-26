---
title: 코드 스플리팅
date: 2021-12-26 20:00:00 +0900
categories: [웹 성능 최적화]
tags: [웹 성능 최적화, 코드 스플리팅]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---


코드 스플리팅 `Code Splitting`이란 말 그대로 코드를 분할하여 큰 번들파일을 쪼개서 작은 사이즈의 파일로 만드는 것이다. 그렇다면 코드를 왜 분할하는 것일까?

![image](https://user-images.githubusercontent.com/52060742/147405938-0bd13f01-f3a9-4d5f-a386-d9e768d64d09.png)

위의 사진은 [cra-bundle-analyzer](https://www.npmjs.com/package/cra-bundle-analyzer)을 사용한 번들 분석 결과이다.

> [webpack-bundle-analyzer](https://www.npmjs.com/package/webpack-bundle-analyzer)도 있지만 지금은 CRA로 만든 프로젝트를 분석해본 것이기 때문에 cra-bundle-analyzer를 사용했다. 물론 eject를 하거나 craco 같은 별도의 라이브러리를 통해 webpack-bundle-analyzer를 사용해도 된다.

분석 결과를 보면 `refractor`라는 것이 번들의 절반을 차지하고 있다. 그럼 이것이 무엇인지 확인하려면 `package-lock.json`을 한 번 살펴보자!

확인해 보니 코드 문법을 하이라이팅 시키는 `react-syntax-highlighter`라이브러리에 필요한 라이브러리로써 사용되고 있었다.

```json
"react-syntax-highlighter": {
  "version": "12.2.1",
  "resolved": "생략",
  "integrity": "생략",
  "requires": {
    "@babel/runtime": "^7.3.1",
    "highlight.js": "~9.15.1",
    "lowlight": "1.12.1",
    "prismjs": "^1.8.4",
    // 여기서 사용 됨
    "refractor": "^2.4.1"
  }
},
```

<br/>
<br/>

이것을 확인했다고 해서 최적화를 어떻게 진행할 수 있을까? 먼저 이 라이브러리가 어디에서 쓰이는 지 확인해 봐야 할것이다. 현재 이 라이브러리는 게시글의 `상세화면`에서만 사용하고 있다.

즉, 게시물 전체를 보여주는 화면에서는 굳이 로딩될 필요가 없는 것이다! 이 때 바로 `Code Splitting`을 사용하여 불필요한 리소스를 줄이고 페이지의 로딩속도를 향상시킬 수 있다.

<br/>
<br/>

## Code Splitting의 패턴

코드 스플리팅은 몇 가지의 패턴을 가지고 있다.

1. 페이지 별로 코드를 분할
2. 모듈 별로 코드를 분할 (여러 페이지에서 같은 모듈을 사용할 경우)
3. 위의 두 가지 방법을 적절히 섞은 방법

코드 스플리팅의 가장 핵심은 불필요한 코드, 중복된 코드 없이 `적절한 사이즈`의 코드가 `적절한 타이밍`에 로드되도록 하는 것이다.


<br/>
<br/>

## Code Splitting 방법

자세한 내용은 [React 공식 홈페이지](https://ko.reactjs.org/docs/code-splitting.html)에서 확인할 수 있다.

가장 간단하게는 `React Router`라이브러리를 사용해서 `라우트 기반`의 코드 스플리팅을 하는 것이다.

```js
import React, { Suspense, lazy } from 'react';
import { BrowserRouter as Router, Route, Switch } from 'react-router-dom';

const Home = lazy(() => import('./routes/Home'));
const About = lazy(() => import('./routes/About'));

const App = () => (
  <Router>
    <Suspense fallback={<div>Loading...</div>}>
      <Switch>
        <Route exact path="/" component={Home}/>
        <Route path="/about" component={About}/>
      </Switch>
    </Suspense>
  </Router>
);
```

하지만 이렇게 한다고 해서 바로 사용할 수 있는 것이 아니다! 😱 코드 스플리팅을 하는 주체는 리액트가 아니라 `웹팩`이기 때문에 웹팩에서 별도의 설정을 해줘야 한다!

> CRA로 프로젝트를 구성했을 경우 별도의 설정을 할 필요는 없다!

[웹팩 공식사이트](https://webpack.js.org/guides/code-splitting/)

위 코드와 같이 코드 스플리팅을 했을 경우 번들 분석결과는 아래와 같다.

![image](https://user-images.githubusercontent.com/52060742/147407938-91a9cff0-51eb-4cd0-8ba3-f3ef3b7011c1.png)

처음과 다르게 `react-dom` 부분이 별도로 분리된 모습을 볼 수 있다!

<br/>
<br/>

## Lighthouse 결과

![image](https://user-images.githubusercontent.com/52060742/147408002-07a29b16-185b-4e68-b48f-5949288156fd.png)

오우 🚀 중간에 진행했던 몇몇의 최적화와 코드 스플리팅을 하는 것 만으로도 51점에서 `96점`이 되었다!!


하지만 이것은 production 모드에서 측정을 한 것이 아니라 development 모드에서 진행한 것이다. 성능 측정은 production 모드에서 측정을 해야한다. 그 이유는 웹팩 설정에 따라 production 모드에서는 코드를 `minify` 하거나 `uglify` 하기 때문에 development와 성능차이가 있을 수 있기 때문이다

<br/>
<br/>

## 텍스트 압축

서버에서 보내는 리소스를 압축해서 서비스를 하는 것이다! 이렇게 하면 다운로드 하는 리소스의 크기가 줄어들어 더 빠르게 컨텐츠를 로드할 수 있다.


![image](https://user-images.githubusercontent.com/52060742/147408339-93646536-2098-426b-9025-e02e3dd74094.png)

네트워크 탭에서 현재 요청하고 있는 API의 `응답 헤더`를 살펴보면 `Content-Encoding`에 `gzip`이라고 되어 있는 부분이 있다 이 의미는 gzip이라는 인코딩 방식을 통해 `압축`되어 있다는 의미이다.

<br/>
<br/>

![image](https://user-images.githubusercontent.com/52060742/147408419-316a568b-ebb6-4e7e-be86-e9c6117102e0.png)

이와 반대로 빌드된 `main.js`를 보면 응답 헤더에서는 `Content-Encoding`를 찾아볼 수 없다. 이 의미는 번들링된 파일은 압축되어 있지 않다는 의미이다.


> 웹상에서 사용하는 압축 알고리즘에는 보통 `gzip`과 `Deflate`이 있다. gzip이 Deflate보다 높은 압축률을 가지고 있다.


그리고, 압축여부를 확인할 때 `응답 헤더`에서 확인되는 것으로 유추했을 때 클라이언트에서 하는게 아니라 번들 파일을 서비스 해주는 서버에서 해줘야 한다. 

> 유의해야 할 점
> 
> 서버에서 압축 설정이 되어 있는데 네트워크 탭을 보면 어떤 파일은 gzip으로 압축이 잘 되어 있고, 어떤 파일은 압축이 되어있지 않는 경우도 있을 것이다. 그 이유는 서버에서 압축을 하면 클라이언트에서는 압축을 해제해야 하는데 무분별하게 모든 파일을 압축해버리면 오히려 시간이 더 걸릴 수도 있기 때문이다.
>
> 보통 `2kb`를 기준으로 압축 유무를 결정한다.

<br/>
<br/>

## 느낀점

텍스트 압축은 정말 가성비 좋은 최적화 방법인것 같다. 물론 서버에서도 설정해야 하지만





