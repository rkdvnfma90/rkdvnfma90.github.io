---
title: 캐시 최적화
date: 2022-01-07 13:02:00 +0900
categories: [웹 성능 최적화]
tags: [캐시 최적화]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---

크롬 개발자 도구의 lighthouse를 실행시켜 보면 아래와 같이 `Serve static assets with an efficient cache policy`라는 항목을 만날 수 있다

![image](https://user-images.githubusercontent.com/52060742/148484783-d027c19e-a767-4a85-9a82-8845e8143e3c.png)

<br/>

이 의미는 정적 파일들에 대해 캐시처리가 안되어 있을 경우 나타나는 것인데 이를 해결하기 위해 `Cache-Control`이라는 것을 사용해야 한다

<br/>
<br/>

## Cache-Control

캐시를 적용하려면 브라우저가 서버로 특정 리소스를 요청할 때 `서버에 해당 리소스의 캐시를 적용해 달라`라는 요청을 해야 하는데 그것이 바로 `Http Header`에 들어가는 `Cache-Control`이다

하지만 클라이언트 쪽에서 Cache-Control 요청을 한다고 해서 바로 캐시를 적용할 수 있는 것은 아니다. 그 이유는 서버에서 별도로 설정을 해야 하기 때문이다

<br/>

### Cache-Control에 설정할 수 있는 옵션

- no-cache : 캐시를 사용하기 전에 서버에서 사용 가능한 지 검사 후 사용 유무를 결정한다
- no-store : 캐시를 사용 안함
- public : 모든 환경에서 캐시 사용
- private : 브라우저 환경에서만 캐시를 사용 (외부 캐시 서버에서는 사용 불가)
- max-age : 캐시 유효시간 (초 단위)

사용 예시
- cache-control: max-age=120
- cache-control: public, max-age=600
- cache-control: no-cache (`max-age=0`과 같은 의미이다 -> max-age가 0이어도 브라우저에서 캐시를 바로 버리는 것이 아니라 가지고 있다. 즉, 만료가 되었다고 해서 서버에서 바로 해당 리소스를 불러오는 것이 아니라 서버에서 사용 가능한지 검사 후 사용 유무에 따라 결정되기 때문이다)


<br/>
<br/>

## 그럼 서버는 어떻게 브라우저가 가지고 있는 캐시데이터가 서버에 있는 최신의 데이터가 같은지 판단할 수 있을까?

바로 `etag`로 판단할 수 있다 etag는 해당 리소스에 대한 `hash`값이라고 생각하면 된다

![image](https://user-images.githubusercontent.com/52060742/148488052-6426f81b-4ae1-40e2-a309-a56d298c1c38.png)