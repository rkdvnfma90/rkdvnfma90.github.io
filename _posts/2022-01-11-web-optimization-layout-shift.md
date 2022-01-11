---
title: Layout Shift 피하기
date: 2022-01-11 22:08:00 +0900
categories: [웹 성능 최적화]
tags: [Layout Shift]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---

## Layout Shift란?

단어 그대로 화면상에서 어떤 각 `요소`들이 로딩 속도나 다른 이유를 통해 화면에 보여지는 시점이 달라지면서 발생하는 `요소 밀림` 현상을 말한다.

![image](https://user-images.githubusercontent.com/52060742/148943209-96e77248-a4b8-48d3-8552-6b787e645b6e.png)

크롬 개발자 도구의 `성능`탭에서도 확인할 수 있다. 빨간색으로 표시된 부분에 마우스를 올리면 친절하게도 어떤 요소에 `Layout Shift`가 발생했는지 확인할 수 있다.

`Layout Shift`는 당연히 성능, 사용자 경험에 영향을 끼친다. 그 이유는 이 현상이 일어나는 것 자체가 화면상에서 위치값을 다시 계산하는 것이기 때문이다. 

![image](https://user-images.githubusercontent.com/52060742/148943819-cfa7c323-c2a1-43e2-9d50-2e977c44bb1f.png)

개발자도구에서도 `Layout Shift(레이아웃 변경)`이 일어나면 `레이아웃, 페인트`가 발생하는 것을 확인할 수 있다. 

<br/>
<br/>

## Layout Shift의 원인

1. 사이즈가 지정되지 않은 이미지
2. 동적으로 삽입된 컨텐츠
3. 웹 폰트 (FOIT, FOUT)

이 현상을 해결하기 위해선 간단하다 `Layout Shift`가 발생할 것 같은 요소에 `사이즈를 지정`하는 것이다.

<br/>
<br/>

### 이미지를 비율에 맞춰 렌더링 하는 방법

```jsx
const ImageWrapper = styled.div`
  position: relative;
  width: 100%;
  /* 16:9로 맞추기 위해 9/16 === 0.5625 => 56.25% */
  /* 너비의 56.25% 만큼 세팅 */
  padding-bottom: 56.25%;
`;

const Image = styled.img`
  position: absolute;
  cursor: pointer;
  width: 100%;
  height: 100%;
  top: 0;
  left: 0;
`;

// 생략

return (
  <ImageWrapper>
    <Image src={image.src} alt={image.alt} />
  </ImageWrapper>
);
```

위 코드와 같이 `Image` 컴포넌트를 감싸는 `ImageWrapper`에 원하는 비율 or 크기를 미리 지정해 주고 이미지는 자신을 감싸고 있는 부모의 크기를 갖기 위해 width, height 모두 100%로 지정해 주고 position을 `absolute`로 하여 `ImageWrapper`영역이 깨지지 않도록 한다.

물론 이 방법 외에도 여러 방법들이 있을 것이다!
