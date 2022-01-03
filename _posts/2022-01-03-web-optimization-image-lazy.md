---
title: Intersection Observer를 활용한 이미지 lazy 로딩
date: 2022-01-03 13:24:00 +0900
categories: [웹 성능 최적화]
tags: [이미지 lazy 로딩]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1571795671/noticon/ncgxzfzuzo0ygwniagek.png
  width: 100
  height: 100
  alt: Performance
---


`Intersection Observer`를 사용한 이미지 Lazy 로딩은 해당 이미지가 뷰포트에 보여졌을 때 로딩을 하는 방법이다.

예를 들어 어떤 홈페이지의 메인 컨텐츠로 보여야할 `동영상`이 하나 있고, 그 아래쪽에는 여러 이미지들이 있는 페이지라고 가정해보자

![image](https://user-images.githubusercontent.com/52060742/147900104-82444e45-dbe1-4f70-8be3-26753f2eb9f2.png)

<br/>
<br/>

메인 컨텐츠가 동영상 이기 때문에 사용자가 가장 먼저 봐야 할 컨텐츠는 `동영상`이어야 한다. 하지만 아래와 같은 상황 때문에 동영상을 바로 못 볼수도 있다. (이미지를 다운로드 하고 있을 동안 동영상은 `대기 중` 상태이다)

![image](https://user-images.githubusercontent.com/52060742/147900161-a589dbc5-6f7e-465e-9c12-fa9dfe49ed72.png)


<br/>
<br/>

시간이 지나고 이미지 다운로드가 얼추 완료되면 그제서야 동영상이 다운로드 되는 것을 알 수 있다.

![image](https://user-images.githubusercontent.com/52060742/147900233-ba96a90d-a502-455d-926f-c552eccc76cd.png)

<br/>
<br/>

즉, 화면에 보이지 않는 이미지 때문에 정작 사용자가 메인으로 봐야 할 컨텐츠를 제대로 못 보는 경우가 생길 수도 있다. 그렇기 때문에 이미지가 Viewport에 보일 때 다운로드 함으로써 사용자 경험을 좋게 할 수 있다.

이것이 바로 `이미지 Lazy 로딩` 기법이다.

<br/>
<br/>

## 사용 방법

```jsx
const Card = (props) => {
  const imageRef = useRef(null);

  useEffect(() => {
    const options = {};
    const callback = (entries, observer) => {
      entries.forEach(entry => {
        if (entry.isIntersecting) {
          entry.target.src = entry.target.dataset.src;

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
      <!-- 이미지가 Viewport에 들어왔을 때 src를 지정해 줄것이다 -->
      <img data-src={props.image} ref={imageRef} />
    </div>
  );
}

export default Card;

```