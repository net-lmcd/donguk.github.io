---
title: Javascript 동치연산자
date: 2020-04-30 17:04:05
category: javascript
---

## Equality 연산자

- `==` 와  `!=` 는 equality 연산자
- 이 연산자를 사용하면 **연산이 되기 전에 피연산자들을 먼저 비교할 수 있는 형태로 변환 시킨다.**

```javascript
999 == '999' // true
undefined = null // true
true == 1 // true
'string' == new String('string') // true
null == false // false
'true' == true // false
true == 2 // false
```

#### 참고

`undefined` 와 `null`의 차이는 값의 할당에 있다. `undefined`는 변수는 선언되었지만, **값이 할당되지 않은 것**이고, `null`은 **값이 명시적으 변수에 할당된 상태**이다.

<br/>

## Identity 연산자

- `===` 와 `!==` 는 identity 연산자

- equality 연산자와 반대로 **형변환을 하지 않고 연산한다.**

```javascript
254 === '254' // false
true === 1 // false
undefined === null // false
'string' === new String('string') // false
```



