---
title: 문제해결 패턴 - 빈도수 세기
date: 2021-12-28 09:48:00 +0900
categories: [알고리즘, 기본내용]
tags: [빈도수 세기]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1577524878/noticon/gzl7ru4i4vv3phyv34y3.png
  width: 100
  height: 100
  alt: Algorithm
---

빈도수 세기 패턴은 여러개의 데이터와 입력값이 있고 이 데이터들이 비슷한 값을 갖거나 anagram인지 확인할 때, 서로 비슷한 값을 가졌는지 확인할 때 등등 데이터와 빈도를 비교할 때 사용하면 좋은 패턴이다. 이 패턴을 사용하면 `O(n)`의 시간 복잡도를 가질 수 있다.

<br/>
<br/>

## 문제

2개의 배열이 있고 두 번째 배열에는 첫 번째 배열의 각 요소에 제곱을 한 값들이 빈도수에 맞게 존재하면 `true`를 리턴하는 함수를 작성해야 한다. (순서는 상관 없다)

예시)
```js
const array1 = [1, 1, 4, 5]
const array2 = [16, 1, 25, 1]

check(array1, array2) // true

const array3 = [1, 1, 3, 4]
const array4 = [9, 1, 16, 49]

check(array1, array2) // false -> 49의 제곱근인 7이 없을 뿐 더러, 1의 개수가 맞지 않음
```

<br/>
<br/>

## 나쁜 풀이

```js
const check = (array1, array2) => {
  if (array1.length !== array2.length) {
    return false;
  }

  for (let i = 0; i < array1.length; i += 1) {
    const value = array1[i];

    let correctIndex = array2.indexOf(value ** 2);

    if (correctIndex === -1) {
      return false;
    }

    array2.splice(correctIndex, 1);
  }

  return true;
};
```

이 풀이는 `O(n ** 2)`의 시간복잡도를 가지는데 그 이유는 for loop 안에서 `indexOf`, `splice`를 사용하기 때문이다.

<br/>
<br/>

## 좋은 풀이

```js
const check = (array1, array2) => {
  if (array1.length !== array2.length) {
    return false;
  }

  const counter1 = {};
  const counter2 = {};

  for (const value of array1) {
    counter1[value] = (counter1[value] || 0) + 1;
  }

  for (const value of array2) {
    counter2[value] = (counter2[value] || 0) + 1;
  }

  for (const key in counter1) {
    // 제곱인 요소가 없으면 false
    if (!(key ** 2 in counter2)) {
      return false;
    }

    // 개수가 맞지 않으면 false
    if (counter2[key ** 2] !== counter1[key]) {
      return false;
    }
  }

  return true;
};
```

이 풀이는 for loop의 개수는 늘어났지만 시간복잡도로 따지면 `O(n)`이다.