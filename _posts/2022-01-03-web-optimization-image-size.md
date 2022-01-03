---
title: 이미지 사이즈 최적화
date: 2022-01-03 16:34:00 +0900
categories: [웹 성능 최적화]
tags: [이미지 사이즈 최적화]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---


이미지 사이즈 최적화는 너무 당연한 얘기지만 간단히 말하면 이미지의 크기를 줄이고 이미지 용량을 낮추는 작업이라고 할 수 있다.

<br/>
<br/>

## 이미지 포맷

- PNG : 무손실 이미지 압축으로써 이미지 용량이 상대적으로 크다
- JPG : 압축을 하기 때문에 PNG에 비해서 용량이 작다. 하지만 약간의 화질 저하가 있을 수 있다 (이미지 최적화를 위해 PNG보다 JPG를 사용하는 것이 권장 된다. 투명도가 필요하지 않는 이상)
- WEBP : 구글에서 개발한 차세대 이미지 포맷이다. 보통 PNG, JPG 대비 더 작은 크기의 이미지를 만들 수 있다 (투명도도 지원한다. 하지만 지원하지 않는 브라우저가 있다)

<br/>
<br/>

## 이미지 압축

그렇다면 압축을 통해 이미지 사이즈를 어떻게 줄일 수 있을까? 구글에서 만든 [squoosh](https://squoosh.app/)를 사용하면 기존에 사용하던 이미지를 압축할 수 있다.

![image](https://user-images.githubusercontent.com/52060742/147906541-68b6cfd1-1cec-424e-8d49-4e092c797363.png)

위 사진은 squoosh에서 직접 테스트를 해본 결과인데 왼쪽 영역이 원본이고 오른쪽 영역이 압축이 된 부분이다. width와 height를 줄이고 Quality는 75정도, 포맷을 `WEBP`로 바꿨더니 기존 용량 대비 `98%`나 감소했다.

썸네일이나 크게 보여주지 않아도 괜찮은 이미지 같은 경우 이것을 활용하여 최적화를 할 수 있을것이다.

<br/>
<br/>

## 주의사항

아까 위에도 말했듯이 `WEBP`를 지원하지 않는 브라우저도 있다. 그렇기 때문에 브라우저에 따라 분기처리를 해야 하는데 `<picture>` 태그를 사용하여 이를 해결할 수 있다.

```html
<picture>
  <!-- 브라우저가 먼저 webp 확장자를 사용할 수 있는지 판단하고 사용할 수 있다면 source의 이미지를 보여준다 -->
  <source srcset="logo.webp" type="image/webp">

  <!-- 만약 webp를 지원하지 않는다면 img의 이미지를 보여준다 -->
  <img src="logo.png" alt="logo">
</picture>
```

<br/>
<br/>

## 적용 모습

```jsx
const Card = (props) => {
  const imageRef = useRef(null);

  useEffect(() => {
    const options = {};
    const callback = (entries, observer) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          const target = entry.target;
          const previousSibling = target.previousSibling;

          target.src = target.dataset.src;
          previousSibling.src = previousSibling.dataset.srcset;

          // 이미 감지되어서 해당 로직이 실행되었기 때문에 감지를 해제시켜준다.
          observer.unobserve(entry.target);
        }
      });
    };

    const observer = new IntersectionObserver(callback, options);

    observer.observe(imageRef.current);
  }, []);

  return (
    <div>
      <picture>
        <source data-srcset={props.webp} type="image/webp">
        <img data-src={props.image} ref={imageRef} />
      </picture>
    </div>
  );
}

export default Card;
```