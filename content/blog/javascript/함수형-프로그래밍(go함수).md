---
title: 함수형 프로그래밍(go, pipe)
date: 2019-12-02 22:12:64
category: javascript
---

> ## go, pipe



## go



함수형 프로그래밍에서는 코드를 값으로 다루는 아이디어를 많이 사용한다.

코드인 함수를 받아서 평가하는 시점을 원하는대로 다룰 수 있다. 따라서 코드의 표현력, 가독성을 높일 수 있다.

[이전 포스트](https://doonguk.github.io/javascript/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D(map,filter,reduce-%EC%9D%91%EC%9A%A9)/) 에서 가격이 2만원 미만인 물건의 총 합을 계산하는 코드로

```javascript
const log = console.log
const products = [  
  {name : '반팔티', price : 15000},
  {name : '흰티', price : 20000},
  {name : '긴팔티', price : 15000},
  {name : '남방', price : 30000},
  {name : '바지', price : 25000},
]

const add = (a,b) => a+b
log(...reduce(add, 
             filter( p => p.price < 20000,
                   map( p => p.rice, products))))
```

다음과 같이 작성 하였다. 위와 같이 작성하면 원하는 값을 얻을 수는 있지만 여러 함수를 중첩하여 사용하였기 때문에 가독성이 떨어진다.

go 함수를 만들어서 가독성을 높여보자!

```javascript
const go = () => {}

go(
	0,
  a => a+1, // 0이 a로
  a => a+10, // 이전 파라미터의 결과가 a로
  a => a+100,
  log)

```

go 라는 함수는 파라미터로 받은 함수들을 하나씩 실행 시키는데 실행된 파라미터 함수의 결과가 다음 파라미터 함수의 인자로 사용되는 구조이다.

go 함수의 파라미터로 list를 받는다고 하자.

```javascript
const go = (...args) => {
  log(args) // [0, f, f, f, f]
}
```

여기서 파라미터로 받는 함수들을 축약하기 위해서는 reduce를 사용해야 한다.

```javascript
const go = (...args) => reduce( (a, f) => f(a) ,args)
```

이렇게 구현 한다면 0이 a => a+1 함수의 파라미터로 전달되어 1을 리턴하고 이 값이 다음 파라미터인 a => a+10에 전달되어 값을 평가할 것이다.

이처럼 reduce를 사용하면 특정 리스트의 코드를 축약해 나가는 코드를 작성할 때 쉽게 코딩이 가능하다.



## pipe



pipe 함수는 go함수 와 다르게 함수를 리턴하는 함수이다. go 함수는 파라미터로 전달된 함수들이 즉시 평가되지만, **pipe함수는 함수들이 나열되어 있는 합성된 함수를 만드는 함수이다.**

pipe함수는 함수를 리턴하기 때문에 다음과 같이 시작할 수 있다.

```javascript
const pipe = () => () => {}
```

pipe함수는 나열된 함수들의 축약형을 리턴하는 함수를 리턴한다. 따라서 내부적으로 go 함수를 사용한다.

```javascript
const pipe = (...fs) => (a) => go(a, ...fs)
const f = pipe(
  a => a+1,
  a => a+10, 
  a => a+100,
)
log(f(0)) // 111, f()함수의 인자가 pipe함수의 a에 전달된다.
```

만약 pipe함수가 리턴하는 함수에 인자를 여러개 주고 싶다면,

```javascript
const pipe = (f,...fs) => (...as) => go(f(...as), ...fs)

const f = pipe(
	(a,b) => a+b,
  a => a+10, 
  a => a+100,
)
log(f(0,1))
```

pipe함수의 첫번째 함수를 꺼내서( <code>(f, ...fs)</code> ) go 함수의 파라미터로 오는 첫번째 함수에 여러개의 인자를 전달해 주면된다.

위 예제에서는 2개의 인자 전달만 가능하다. 2개이상을 하고 싶다면 pipe함수의 인자로 전달되는 첫번째 함수를 수정해주자.

```javascript
const pipe = (f,...fs) => (...as) => go(f(..as), ...fs)

const f = pipe(
	(...as) => reduce( (a,b) => a+b, as),
  a => a+10, 
  a => a+100,
)
log(f(0,1,2,3,4,5)) // 125
```



## go 함수 응용

이전 포스트에서 map, filter, reduce를 사용하여 작성한 코드를 go함수를 이용해 가독성을 높여보자

기존의 코드는 아래와 같다.

```javascript
const products = [
  {name : '반팔티', price : 15000},
  {name : '흰티', price : 20000},
  {name : '긴팔티', price : 15000},
  {name : '남방', price : 30000},
  {name : '바지', price : 25000},
]

log( reduce((a,b) => a+b, 
            map( p => p.pice, 
               filter(p => p.pice < 20000, products))))
```

위 코드를 go 함수를 이용해 나타낸다면 다음과 같이 나타낼 수 있다.

```javascript
const go = (...args) => reduce( (a,f) => f(a), args)
const add => a+b
go(
	products,
  products => filter(p => p.price<20000, products),
  products => map(p => p.price, products),
  prices => reduce(add, prices),
  log
)
```



