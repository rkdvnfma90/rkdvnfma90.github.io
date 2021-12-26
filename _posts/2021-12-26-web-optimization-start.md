---
title: 웹 성능 최적화 시작
date: 2021-12-26
categories: [웹 성능 최적화]
tags: 
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---



## 시작

더 효율적인 프론트엔드 개발자가 되고싶어 웹 성능 최적화에 대해 학습하려 한다. 아직 익숙하지 않아 바로바로 적용하는 데에 어려울 수도 있지만 계속 하면서 습관화 할 수 있도록 해야겠다! 🔥🔥🔥

<br/>
<br/>

## lighthouse

가장 쉽게 성능을 확인할 수 있는 방법은 크롬 개발자 도구의 `lighthouse`이다. 


![image](https://user-images.githubusercontent.com/52060742/147401619-5c0d07e4-827f-45f8-b31e-303e625aa3f5.png)


자신이 원하는 설정을 체크하고 `보고서 생성`버튼을 누르기만 하면 아래와 같이 점수, 각 항목에 대한 설명과 최적화 가이드를 해준다.


![image](https://user-images.githubusercontent.com/52060742/147401643-261d65fe-b402-4319-9f61-64c5a7dbd96b.png)


우리가 개발한 프로젝트들의 해당 점수를 하나하나 높혀가면 된다!


해당 보고서 결과를 밑으로 조금 내려보면 `Opportunities` 항목과 `Diagnostics`이 보이는데 이것은 다음과 같다.

- Opportunities: `리소스의 관점`에서 가이드를 제시해 준다. 즉 `로딩성능 최적화`와 연관이 있는 항목들을 보여준다.
- Diagnostics: `페이지의 실행 관점`에서 가이드를 제시해 준다. 즉, `렌더링 관점`에서 연관이 있는 항목들을 보여준다.

그럼 다음 글에서 Opportunities와 Diagnostics에 대해 살펴보도록 하겠다.