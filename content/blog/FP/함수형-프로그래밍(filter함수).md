---
title: 함수형 프로그래밍(filter)
date: 2019-11-27 02:11:92
category: FP
---

> Filter 함수는 이러터블의 원소들에서 특정한 원소들만 걸러내고 싶을 때 사용한다.



### 1. 구현

```javascript
  const products = [
     {name : '안경', price : 1000},
     {name : '안경2', price : 2000},
     {name : '안경3', price : 3000},
     {name : '안경4', price : 4000},
     {name : '안경5', price : 5000},
   ]
  
  const filter = () => {
    let under3000 = []
    for(const a of products){
      if(a.price < 3000) under3000.push(a) 
    }
    return under3000
  }
  
  log(...filter) // {name : '안경', price : 1000} {name : '안경2', price : 2000 }
```

위 처럼 이터러블의 원소들에서 특정한 원소만 걸러내고 싶을 때 사용한다.

이를 좀 더 추상화 시켜보자.

```javascript
const filter = (f, iter) => {
  let res = []
  for(const a of iter){
    if(f(a)) res.push(a)
  }
  return res
}

log(...filter(p => p.price < 3000, products)) 
// {name : '안경', price : 1000} {name : '안경2', price : 2000 }
```

filter 함수의 파라미터로 어떻게 원소를 필터링 할지 체크하는 역할을 갖는 보조함수와 이터러블을 받는다.

특징 이라면 map, reduce 함수와 비슷하게 필터링하는 기능을 보조함수에 위임 했다는 것이다.

예제로는,

```javascript
log(...filter(a => a%2, function *(){
  yield 1
  yield 2
  yield 3
  yield 4
  yield 5
}())) // 1 3 5
```







