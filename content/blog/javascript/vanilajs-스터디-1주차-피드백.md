---
title: VanilaJS 스터디 1주차 피드백
date: 2020-05-11 15:05:46
category: javascript
---

_코드: https://github.com/doonguk/VanilaJS-Study_

<br/>

### 1.  h1 Tag

- 글씨 크기를 위해 제목 태그를 사용하면 안된다.

- 제목 단계를 건너뛰는 것을 피하자. 언제나 `<h1>` 으로 시작해서, `<h2>`로 **순차적으로 기입**해야 한다.

- **페이지 당 하나의 `<h1>` 만 사용 해야한다.** ( `h1` 은 가장 중요한 제목이니까 논리적으로 여러개 있을 수 없지!)

## 2.  Variable naming

`readable` 한 변수, 함수명을 지으려고 노력하자.

함수의 경우, 내부의 구현을 보기 전에 함수의 이름을 먼저 읽고 기능을 유추하기 때문에, **최대한 동작과 리턴값에 맞추어 함수 이름을 정하는 것이 좋다!!**

### Array

배열은 보통 복수의 같은 특징을 갖는 원소들의 모임(?)이다. 따라서 **복수**형을 사용하자.

```javascript
const fruit = ['apple', 'banana'] // bad
const fruits = ['apple', 'banana'] // good
```

만약 위와같은 배열에서 `fruitNames` 는 가장좋은 선택이다. `Names`이라는 단어는 원소들이 `string` 타입인지 알수 있기 때문이다.

```javascript
const fruitNames = ['apple', 'banana'] // great
```

### Booleans

`is`, `has` , `can` 을 접두어로 붙여서 변수가 갖고있는 특징을 명확히 해주자.

```javascript
const open = true // bad
const isOpen = true //good
const write = false // bad
const canWrite = false // good
const fruit = false // bad
const hasFruit = false // good
```

만약 함수에서 `boolean` 값을 리턴한다면 `check` 나 `get`을 사용하자

```javascript
const user = { fruit: ['banana'] }

const checkHasFruit = (user, fruitName) => user.fruit.includes(fruitName)
const hasFruit = checkHasFruit(user, 'banana')
```

### Functions

함수는 동사와 명사를 이용해서 `naming`을 하는데, 만약 함수가 어떤 `Resource`에 대해 액션을 수행한다면 그 `Resource`를 명시해주는게 좋다.

```javascript
userData(userId) // bad
getUser(userId) // good
```

변수를 변환해주는 역할을 갖는 함수를 만든다면 `to`를 붙여주자

```javascript
toDollars('euros', 20)
```

이터러블을 순회할 때 함수안 원소의 `naming`은 배열이름의 단수형을 사용하자.

```javascript
const newFruits = fruits.map(v => doSomething(v)) // bad
const newFruits = fruits.map(fruit => doSomething(fruit)) // good
```

### 그외..

```javascript
const data = []
```

위와 같은 `data`에서 

```javascript
if (!data.length)
```

0이 falsy이 값이여서 괜찮지만, 명시적으로 `data.length === 0`으로 표현해주는게 더 좋다.



## Reference

1. https://hackernoon.com/the-art-of-naming-variables-52f44de00aad
2. https://developer.mozilla.org/ko/docs/Web/HTML/Element/Heading_Elements

