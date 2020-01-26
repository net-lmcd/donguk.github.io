---
title: javascript some, every 함수
date: 2020-01-26 14:01:80
category: javascript
---



_알고리즘을 풀다가 내가 몰랐던 함수들에 대해 알게 되었다. 정리하자!_

# some

배열 요소에서 하나라도 특정 조건을 만족하는지 알아볼 때 유용한 메소드이다. 다른 배열 메소드와 비슷하게 요소들에 `callback` 함수들을 적용시켜 하나라도 `true`를 반환하면 함수를 **즉시 중단한다.** 만약 모든 요소를 순회 했는데 `true`를 반환 하지 않았다면 `false`를 반환한다.

`callback` 함수의 파라미터로 전달되는 값은 왼쪽부터 순회하는 `index` , 순회되는 값, 순회되는 배열 이다.

```javascript
const test = [1,2,3]
test.some((el, index, array) => {
  return el === 1
}) // 한번만 순회 후 즉시 중단
//true 리턴
```



<br/>

# every

`some`함수와 반대되게 배열의 **모든** 요소가 특정 조건을 만족하는지 알고 싶을 때 적합한 메소드이다. 배열을 순회하다가 한 요소라도 `false` 값을 리턴하면 **즉시 중단** 된다. 만약 모든 요소가 조건을 만족한다면 `true`를 리턴한다.

`callback`의 파라미터로 전달되는 값은 `some` 함수와 같다.

```javascript
const test = [1,2,3]
test.some((el, index, array) => {
  return typeof el === number
})
//true 리턴
```



