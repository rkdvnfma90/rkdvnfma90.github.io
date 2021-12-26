---
title: Opportunities
date: 2021-12-26 19:00:00 +0900
categories: [웹 성능 최적화]
tags: [웹 성능 최적화, Opportunities]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---


지난 글에 이어서 `Opportunities` 항목을 하나씩 최적화 해보자!

내가 확인하고 있는 코드의 Opportunities 결과는 다음과 같다.

![image](https://user-images.githubusercontent.com/52060742/147401752-bebc7872-23ff-4339-af48-3fcde0fd79de.png)

<br/>
<br/>

그럼, 하나하나 살펴보면서 최적화를 진행해 보자!

<br/>
<br/>

## Properly size images

`Properly size images` 항목은 셀룰러 데이터를 절약하고 로드 시간을 단축하기 위해 적절한 사이즈의 이미지를 제공해야 한다는 것을 알려준다. 우측 화살표를 눌러보면 자세한 내용을 볼 수 있다.


![image](https://user-images.githubusercontent.com/52060742/147401861-6417811d-f86b-4da5-baed-2cc756ccf618.png)

우측에 `Potential Savings`는 해당 이미지를 이정도 까지 save 할 수 있다는 것을 의미한다. 

<br/>
<br/>

개발자도구의 inspect로 해당 이미지를 보면

![image](https://user-images.githubusercontent.com/52060742/147401940-1382a1c1-f2f2-41d3-a99e-4236651d4f96.png)

화면에 렌더링 된 사이즈는 `120 x 120`지만 실제 이미지 원본의 사이즈는 `1200 x 1200`라는 것을 알 수 있다. 그럼 이럴 때 어떤 사이즈를 불러와서 화면에 렌더링 시켜야 할까? `120 x 120`의 사이즈로 불러와 렌더링 하는 것도 맞지만 맥이나 레티나 디스플레이를 사용하는 경우 더 많은 픽셀을 그릴 수 있으므로 `240 x 240`로 렌더링 하는 것이 좋다.

그럼 이미지 용량을 어떻게 줄일 수 있을까? 만약 프로젝트에서 `assets`로 관리하는 파일이라면 직접 압축을 하거나 이미지의 물리적인 크기를 줄여서 용량을 줄일 수 있을 것이다. 하지만 API를 통해 받아오는 이미지 같은 경우는 어떻게 해야할까? 바로 `Image CDN`을 사용하는 것이다.

개발을 하다가  image.com?src=`[image src]`&`width=240`&`height=240` 이러한 형태로 개발자가 원하는 사이즈로 가공하여 받을 수 있다.


<br/>
<br/>

## Minify JavaScript

`Minify JavaScript`는 자바스크립트 코드를 minify하라는 의미이다. minify란 자바스크립트 코드에서 공백이나 줄바꿈 등을 줄이는 것인데 리액트 코드를 `CRA`로 생성했거나 별도로 웹팩 설정을 하고 production 모드로 빌드를 할 때 minify할 수 있다!


