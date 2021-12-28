---
title: 문제해결 패턴 - 슬라이딩 윈도우
date: 2021-12-28 18:14:00 +0900
categories: [알고리즘, 기본내용]
tags: [슬라이딩 윈도우]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1577524878/noticon/gzl7ru4i4vv3phyv34y3.png
  width: 100
  height: 100
  alt: Algorithm
---

슬라이딩 윈도우는 배열이나 문자열에서 데이터의 연속성 구조를 찾을 때 유용하다

<br/>
<br/>

## 문제

정수로된 배열과 n이 주어졌을 때 배열에서 n개의 연속된 요소들 중 최대 합을 구해라


예시)
```js
maxSum([1, 2, 5, 2, 8, 1, 5], 2); // 10
maxSum([], 4); // null
```

<br/>
<br/>


## 풀이

```js
const maxSum = (arr, num) => {
  let maxSum = 0;
  let tempSum = 0;

  if (arr.length < num) {
    return null;
  }

  for (let i = 0; i < num; i += 1) {
    maxSum += arr[i];
  }

  tempSum = maxSum;

  for (let i = num; i < arr.length; i += 1) {
    tempSum = tempSum - arr[i - num] + arr[i];
    maxSum = Math.max(maxSum, tempSum);
  }

  return maxSum;
};
```

말 그대로 슬라이딩 하듯이 풀리는 방법이다.

예를 들어 배열이 `[7, 5, 2, 9, 1, 2, 7, 10, 31]`이고, num이 `3`일 경우 최초 7, 5, 2 요소에 대한 합(tempSum)을 구하고 그 다음에 5, 2, 9의 합을 또 바로 구하는 것이 아니라 기존에 구한 tempSum의 값에서 단지 7을 빼고 9를 더하면 되는 것이다.

이런식으로 계속 해결하는 것이 바로 슬라이딩 윈도우 기법이다!