---
title: 2022-04-01 TIL Typescript
date: 2022-04-01 21:97:00 +0900
categories: [TIL]
tags: [Typescript]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1578807458/noticon/tr4etvb9imzxc3xxnq3x.jpg
  width: 100
  height: 100
  alt: TIL
---


## 새로 알게된 튜플타입 정의 방법

<br/>

```ts
type CallExpression = MathCall
type Expression = number | string | CallExpression;

interface MathCall {
  0: '+' | '-' | '*' | '/' | '>' | '<';
  1: Expression;
  2: Expression;
  length: 3;
}

// '["+", number, number, number]' 형식은 'MathCall' 형식에 할당할 수 없습니다. 'length' 속성의 형식이 호환되지 않습니다. '4' 형식은 '3' 형식에 할당할 수 없습니다.
const mathExpression1: MathCall = ['+', 1, 20, 30]; 

// ok
const mathExpression2: MathCall = ['+', 1, 20]; 
```

<br/>

`MathCall` 타입과 같이 튜플을 선언할 수도 있다. (Array의 속성과 유사하게!)