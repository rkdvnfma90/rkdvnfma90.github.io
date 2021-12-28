---
title: 문제해결 패턴 - 다중 포인터
date: 2021-12-28 09:48:00 +0900
categories: [알고리즘, 기본내용]
tags: [다중 포인터]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1577524878/noticon/gzl7ru4i4vv3phyv34y3.png
  width: 100
  height: 100
  alt: Algorithm
---

다중 포인터 패턴의 핵심은 `배열`이 있고, 인덱스에 맞게 2개이상의 `포인터`를 지정하고 조건에 따라 그 포인터의 위치를 하나씩 옮겨가며 문제를 해결하는 방법이다

<br/>
<br/>

## 문제 1

정수로 구성되어 있고 정렬된 배열이 주어질 때 두 수의 합이 0인 첫 번째 쌍의 정수를 구해야 한다.


예시)
```js
sumZero([-3, -2, -1, 0, 1, 2, 3]) // [-3, 3]
sumZero([-2, 0, 1, 3]) // undefined
sumZero([1, 2, 3]) // undefined
```

<br/>
<br/>


## 풀이

```js
const sumZero = array => {
  let left = 0;
  let right = array.length - 1;

  while (left < right) {
    let sum = array[left] + array[right];

    if (sum === 0) {
      return [array[left], array[right]];
    } else if (sum > 0) {
      // 두 수의 합이 0보다 크면 우측의 포인터를 감소 시킨다
      right -= 1;
    } else {
      // 두 수의 합이 0보다 작으면 좌측의 포인터를 증가 시킨다
      left += 1;
    }
  }
};
```

<br/>
<br/>

## 문제 2

정수로 구성되어 있고 정렬된 배열이 주어질 때 해당 배열의 유니크한 값의 개수를 리턴해라

예시)
```js
countUniqueValue([1, 1, 1, 1, 2]) // 2
countUniqueValue([1, 1, 2, 3, 4, 4, 4, 5]) // 5
countUniqueValue([]) // 0
```

<br/>
<br/>

## 나의 풀이

```js
function countUniqueValues(arr) {
  if (arr.length === 0) {
    return;
  }

  if (arr.length === 1) {
    return 1;
  }

  let left = 0;
  let right = 1;

  while (right < arr.length) {
    if (arr[left] === arr[right]) {
      right += 1;
      continue;
    }

    if (arr[left] !== arr[right]) {
      left += 1;
      arr[left] = arr[right];
    }
  }

  return left + 1;
}
```

<br/>
<br/>

## 더 간단한 풀이

```js
function countUniqueValues(arr) {
  if (arr.length === 0) return 0;

  let i = 0;

  for (let j = 1; j < arr.length; j += 1) {
    if (arr[i] !== arr[j]) {
      i += 1;
      arr[i] = arr[j];
    }
  }

  return i + 1;
}
```