---
title: 폰트 최적화
date: 2022-01-05 16:35:00 +0900
categories: [웹 성능 최적화]
tags: [폰트 최적화]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---

## 웹 폰트의 문제점

웹 폰트를 사용할 경우 두 가지의 문제점이 있다

1. FOUT(Flash of Unstyled Text)
  - 폰트가 다운로드 되기 전에는 기본 폰트로 보인다
  - 폰트가 다운로드가 완료 되어야 폰트가 적용 된다
  - IE, Edge

2. FOIT(Flash of Invisible Text)
  - 폰트가 다운로드 되기 전에는 화면에서 Text를 안보여 준다
  - 폰트가 다운로드 완료 되어야 화면에 보인다
  - Chrome, Safari

<br/>
<br/>

## 폰트 최적화 방법

먼저, 폰트 최적화의 궁극적인 목표는 `FOUT`와 `FOIT`를 최소화 하는 것이다. 그럼 어떻게 최적화를 할 수 있을까? 크게 2가지의 방법이 있는데 다음과 같다

1. 폰트 적용 시점 컨트롤
  - `FOUT`를 선택할 것인지, `FOIT`를 선택할 것인지, 둘다 혼합할지, 아니면 아예 새로운 방법을 선택할지 상황에 맞게 개발자가 컨트롤 하는 것이다

2. 폰트 사이즈 줄이기
  - 폰트 사이즈를 물리적으로 줄여 다운로드 속도를 빠르게 하는 것이다

<br/>
<br/>

### 폰트 적용 시점 컨트롤

이것을 컨트롤 하기 위해선 css의 `font-display`라는 속성을 사용해야 한다

`font-display`
  - auto : 브라우저 기본 동작
  - block : FOIT (timeout = 3초) 여기서 timeout의 의미는 폰트를 다운로드 받는 데 3초가 지나면 기본 폰트를 보여준다는 의미
  - swap : FOUT
  - fallback : FOIT (timeout = 0.1초), 만약 3초 이후에도 불러오지 못했을 경우 기본 폰트로 유지한다 즉, 3초 이후에는 사용자에게 깜빡임을 보여주지 않는다. 다운로드가 완료되면 폰트를 캐시해놓는다
  - optional : FOIT (timeout = 0.1초), 네트워크 상황에 따라 기본폰트로 유지할지 웹폰트를 적용할지 결정하고 이후에 캐시한다 `(구글에서 권장하는 방법이다)`

<br/>

만약 `FOIT`를 사용할 경우 사용자 경험을 조금이라도 좋게하는 트릭을 사용할 수 있는데 그것은 폰트 다운로드가 완료되는 시점에 `fade-in`효과를 주는 것이다

폰트의 다운로드 여부를 알기 위해서 [Font Face Observer](https://www.npmjs.com/package/fontfaceobserver) 라이브러리의 도움을 받을 수 있다

```jsx
const Component = () => {
  const [isFontLoaded, setIsFontLoaded] = useState(false);

  useEffect(() => {
    const font = new FontFaceObserver('font family'); // 사용하는 font family를 명시

    font.load().then(() => {
      setIsFontLoaded(true);
    });
  }, []);

  return (
    ... 생략
  )
}
```

<br/>
<br/>

### 폰트 사이즈 줄이기

1. ttf가 아닌 효율적인 웹 폰트 사용
  - WOFF, WOFF2 등등
  - EOT, TTF/OTF, WOFF, WOFF2 중에서는 `WOFF2`가 파일 크기가 가장 작다
  - [https://transfonter.org/](https://transfonter.org/) 이곳에서 폰트 변환을 할 수 있다
  - WOFF2, WOFF등을 사용할 때 미지원하는 브라우저가 있을 수 있으므로 아래와 같이 적용한다
    ```css
    @font-face {
      font-family: Your Font Family;
      src: url('./assets/fonts/Your Font File.woff2') format('woff2'),
        url('./assets/fonts/Your Font File.woff') format('woff'),
        url('./assets/fonts/Your Font File.ttf') format('truetype');
      font-display: block;
    }
    ```

2. local 폰트 사용
  - 사용자 PC에 해당 폰트가 설치되어 있을 경우 `local()`을 사용하여 다운로드 없이 바로 사용할 수 있다.
    ```css
    @font-face {
      font-family: Your Font Family;
      src: local('Your Font Family'),
        url('./assets/fonts/Your Font File.woff2') format('woff2'),
        url('./assets/fonts/Your Font File.woff') format('woff'),
        url('./assets/fonts/Your Font File.ttf') format('truetype');
      font-display: block;
    }
    ```
    
3. Subset 사용
  - 폰트 파일에서 불필요한 글자를 제거하고 사용할 글자만 남겨둔 폰트이다
  - 위에서 폰트를 변환했던 사이트 [https://transfonter.org/](https://transfonter.org/) 에서 subset 설정도 할 수 있다
  - subset 뿐만 아니라 사용할 문자에 대해서도 지정할 수가 있다
    ![image](https://user-images.githubusercontent.com/52060742/148170589-7c32939b-1cc3-4e08-8be9-d063f3cbd674.png)

4. Unicode Range 적용
  - css 속성중 `unicode-range`를 사용하여 옵션으로 지정한 유니코드에 대해서만 폰트를 적용한다는 의미이다

5. data-uri 변환

6. Preload 사용
  - 웹에서 CSS가 로드가 되고 CSS를 읽는 순간 명시한 폰트가 필요하다는 것을 알게 되는데 그 시점보다 더 먼저 로드를 시키는 방법이다
  - HTML 파일의 `HEAD` 안에 명시해야 한다.
    `<link rel="preload" href="Your Font.woff2" as="font" type="font/woff2" crossorigin>`
  - 제대로 사용하기 위해선 `webpack`에서 폰트 Preload 설정을 따로 해주는 것이 좋다. 이 때 [preload-webpack-plugin](https://www.npmjs.com/package/preload-webpack-plugin) 해당 플러그인을 사용한다


[https://d2.naver.com/helloworld/4969726](https://d2.naver.com/helloworld/4969726) 여기에도 좋은내용이 많은것 같다
