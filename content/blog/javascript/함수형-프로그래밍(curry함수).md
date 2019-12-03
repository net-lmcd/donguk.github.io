---
title: 함수형 프로그래밍(curry)
date: 2019-12-03 01:12:07
category: javascript
---

> ## Curry



커리함수는 함수를 리턴하는 함수인데, 받아둔 함수를 원하는 시점에 평가하여 값을 리턴하는 기능을 갖는다.

1. 커리 함수는 함수를 리턴

```javascript
const curry = () => () => {}
```

2. 함수를 받아서 원하는 시점에 평가

```javascript
const curry = f => (a, ..._) => _.length ? f(a,..._) : (..._) => f(a, ..._)
```

인자가 2개 이상이면 받아둔 함수를 인자와 함께 즉시 실행 시키고 인자가 2개 미만 이라면 인자를 더 받아 후에 실행 시키는 함수를 리턴한다.

curry함수는 다음과 같이 사용된다.

```javascript
const log = console.log
const mult = curry((a,b) => a*b)
log(mult(3)) // (..._) => f(a, ..._)
log(mult(3)(3)) // 9
const mult3 = mult(3)
log(mult3(3)) //9
log(mult3(4)) //12
```

이 curry 함수는 이전 포스트에 있는 map, filter, reduce 함수에도 적용 할 수 있다.

```javascript
const map = curry((f, iter) =>{
  let res = []
  for(const a of iter) res.push(f(a))
  return res
})
log(map) // (..._) => f(a, ..._)

const filter = curry((f,iter)=>{
  let res = []
  for(const a of iter){
    if(f(a)) res.push(a)
  }
  return res
})

const reduce = curry((f, acc, iter)=>{
  if(!iter){
    iter = acc[Symbol.iterator]()
    acc = iter.next().value
  }
  for(const a of iter){
    acc = f(acc, a)
  }
  return acc
})

const products = [
  {name : '반팔티', price : 15000},
  {name : '흰티', price : 20000},
  {name : '긴팔티', price : 15000},
  {name : '남방', price : 30000},
  {name : '바지', price : 25000},
]
```

이전 포스트에서 go 함수를 이용하여 가격이 2만원 미만인 products의 총합을 다음과 같이 나타냈었다.

```javascript
const go = (...args) => reduce((a, f) => f(a), args)
const add = (a,b) => a+b
go(
	products,
  products => filter(p => p.price<20000, products),
  products => map(p => p.price, products)
  prices => reduce(add, prices),
  log
)
```

현재 map, filter, reduce 함수에는 curry함수가 적용되어 있기 때문에 다음과 같이 표현이 가능하다.

```javascript
go(
	products,
  products => filter(p => p.price<20000)(products),
  products => map(p => p.price)(products),
  prices => reduce(add)(prices),
  log
)
```

동작원리를 살펴본다면,

<code>filter(p => p.price<20000)</code> 만 실행 시킨 경우 curry 함수가 리턴하는 함수의 인자로 <code>p => p.price<20000</code> 함수 한개만 받았기 때문에

```javascript
const curry = f => (a, ..._) => _.length? f(a,..._) : (..._) => f(a,..._)
```

다음 코드에서 a만 받은 case 이다. 따라서 함수를 리턴하게 되고 후에 인자로 받은 products를 통해서 원해 함수의 기능을 실행하게 된다. 따라서 아래의 코드는 다음과 같은 의미를 갖는다.

```javascript
products => filter(p => p.price<20000)(products)
```

<code>filter(p => p.price<20000)</code> 는 인자를 더 받았다가 들어오면 값을 평가하는 **함수** ( <code>..._) => f(a,..._)</code>) 이다. 따라서 축약이 가능하다.

```javascript
filter(p => p.price<20000)
```

전체적인 코드를 축약하면 다음과 같다.

```javascript
go(
	products,
  filter(p => p.price<20000),
  map(p => p.price),
  reduce(add),
  log
)
```



> ## go, pipe, curry 응용



다음과 같은 두개의 함수가 있다.

```javascript
go(
	products,
  filter(p => p.price<20000),
  map(p => p.price),
  reduce(add),
  log
)

go(
	products,
  filter(p => p.price>20000),
  map(p => p.price),
  reduce(add),
  log
)
```

위와 같은 함수에서 중복되는 map, reduce 함수를 줄인다면 pipe 함수를 이용하여 축약이 가능하다.

```javascript
const total_price = pipe(
  map(p => p.price),
  reduce(add)
)

go(
	products,
  filter(p => p.price<20000),
	total_price,
  log
)

go(
	products,
  filter(p => p.price>20000),
	total_price,
  log
)
```

만약 더 중복을 줄인다면?

```javascript
const total_price = pipe(
  map(p => p.price),
  reduce(add)
)

const base_total_price = f => pipe(
	filter(f),
  total_price
)

go(
	products,
  base_total_price(p => p.price<20000),
  log
)

go(
	products,
  base_total_price(p => p.price>20000),
  log
)
```



> ## go, pipe, curry 응용2

### 사용된 함수

```javascript
const log = console.log  
const curry = f => (a, ..._) => _.length ? f(a, ..._) : (..._) => f(a, ..._)
  const map = curry((f, iter) => {
      let res = []
      for(const a of iter){
        res.push(f(a))
      }
      return res
    })

    const reduce = curry((f, acc, iter) => {
      if (!iter) {
        iter = acc[Symbol.iterator]()
        acc = iter.next().value
      }
      for (const a of iter) {
        acc = f(acc, a)
      }
      return acc
    })
    const filter = curry((f, iter) => {
      let res = []
      for(const a of iter){
        if(f(a)) res.push(a)
      }
      return res
    })

    const go = (...args) => reduce((a, f) => f(a), args)
    const pipe = (f, ...fs) => (...as) => go(f(...as), ...fs)
```



### 에제

```javascript
   const products = [
      {name : '반팔티', price : 15000, quantity : 1},
      {name : '긴팔티', price : 25000, quantity : 2},
      {name : '코트', price : 10000, quantity : 3},
      {name : '양말', price : 30000, quantity : 4},
      {name : '모', price : 20000, quantity : 5}
    ]
```

1. 수량의 총합

```javascript
const a = a+b
go(
	products,
  map(p => p.quanty),
  reduce(add),
  log
) //15
```



2. 모든 가격의 총합

```javascript
const a = a+b
go(
	products,
  map(p => p.quanty * p.price),
  reduce(add),
  log
) //315000
```



두 코드 map함수의 파라미터를 제외하고는 중복되는 코드이다. 줄여보자.

```javascript
const sum = (f, iter) => go(
  iter,
	map(f),
  reduce(add),
)

const total_quantity = products => sum(p => p.quantity, products)
const total_prices = products => sum(p => p.quantity * p.price, products)
```



코드가 많이 줄어들었다. 여기서 curry 함수를 사용하여 더 줄여보자.

```javascript
const sum = curry((f,iter) => go(
	iter,
  map(f),
  reduce(add)
))

const total_quantity = products => sum(p =>p.quantity)(products)
const total_prices = products => sum(p => p.price * p.quantity)(products)
//curry함수를 사용하면 나중에 인자를 받아서, 받은 시점에 함수를 평가 할 수 있다. 따라서 위와같은 표현 가능
```

여기서 아래의 식은

```javascript
const total_quantity = products => sum(p =>p.quantity)(products)
const total_prices = products => sum(p => p.price * p.quantity)(products)
//curry함수를 사용하면 나중에 인자를 받아서, 받은 시점에 함수를 평가 할 수 있다. 따라서 위와같은 표현 가능
```

다음과 같이 축약이 가능하다.

```javascript
const total_quantity = sum(p => p.quantity)
const total_prices = sum(p => p.quantity * p.price)

log(total_quantity)// (..._) => f(a, ..._) 인자를 받으면 받은 인자와 함께 실행되는 함수로 평가됌.
log(total_quantity(products)) // 15
log(total_prices(products)) // 315000
```



