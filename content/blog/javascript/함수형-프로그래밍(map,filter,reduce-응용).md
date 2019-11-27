---
title: 함수형 프로그래밍(map,filter,reduce 응용)
date: 2019-11-27 13:11:95
category: javascript
---

> ### map, filter, reduce 응용



```javascript
const log = console.log

const map = (f, iter) => {
  let res = []
  for(const a of iter){
    res.push(f(a))
  }
  return res
}

const filter = (f, iter) => {
  let res = []
  for(const a of iter){
    if(f(a)) res.push(a)
  }
  return res
}

const reduce = (f, acc, iter) => {
  if(!iter){
    iter = acc[Symbol.iterator]()
    acc = iter.next().value
  }
  for( const a of iter){
    acc = f(acc, a)
  }
  return acc
}

const products = [
  {name : '반팔티', price : 15000},
  {name : '흰티', price : 20000},
  {name : '긴팔티', price : 15000},
  {name : '남방', price : 30000},
  {name : '바지', price : 25000},
]
```



1. 가격들만 뽑아보기

```javascript
log(map(p=>p.price, products))
```

map함수를 이용하여 가격만 뽑았다.



2. 특정 가격 이하의 가격만 뽑기

```javascript
log(map(p=>p.price, filter(p => p.price < 20000, products))
```

map함수의 파라미터로 받는 이터러블을 fliter 함수로 축약하여 ( 가격이 2만원 미만  ) 전달 하였다.



3. 특정 가격 이하에 해당하는 물품의 가격을 모두 합치기

```javascript
const add = (a,b) => a+b
log(reduce(add, 0, map(p=>p.price, filter(p => p.price < 20000, products)))
//0 생략가능
log(
  reduce(add, 
         map(p=>p.price, 
             filter(p => p.price < 20000, products)))
// 오른쪽에서 왼쪽으로 읽어 나가면 된다.
```

위와 같은 기능을 하는 코드로

```javascript
log(
  reduce(add, 
         filter(n => n < 20000,
             map(p => p.price, products)))
```

다음과 같이 작성할수도 있다.



4. 위 기능을 구현하기 위한 함수형 프로그래밍 관점에서의 생각

```javascript
log(
	reduce(add,
        [100,100] ))
```

모든 숫자를 더할꺼니까 reduce와 add 함수를 사용한다. 그리고 파라미터로 받는 이터러블로 원소들이 숫자로 평가된 이터러블이 오면 reduce 통해 모두 더할 수 있다.

```javascript
log(
	reduce(add,
        	map(p => p.price, products )))
```

map 함수를 사용해 reduce 함수의 인자로 숫자로만 이루어진 이터러블을 만들었다 ( 평가했다. )

이제 map 함수의 파라미터로 받는 이터러블을 특정가격 이상의 price를 가진 이터러블로 평가하면 되겠다.

```javascript
log(
	reduce(add,
        map(p => p.price, 
           filter(p => p.price<20000, products)))))
```

