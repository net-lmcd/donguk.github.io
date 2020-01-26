---
title: 지연성+Promise(map)
date: 2019-12-26 09:12:04
category: javascript
---

> # 지연평가 + Promise



## 1. L.map, map, take

```javascript
go([1,2,3],
   L.map(a => a+10),
   take(2),
   log
  ) // [11, 12]
```

이 코드에서 go 함수의 첫번째 인자가 Promise라면?

```javascript
go([Promise.resolve(1), Promise.resolve(2), Promise.resolve(3)],
   L.map(a => a+10),
   take(2),
   log
  ) // ["[Object Promise]10", "[Object Promise]10"]
```

보이는 결과 같이 코드가 제대로 동작하지 않는다. Promise도 처리할 수 있게 코드를 수정 해 보자!

기존에 작성했던 L.map함수를 먼저 보겠다.

```javascript
L.map = function* (f, iter){
  for(const a of iter){
    yield f(a)
  }
}
```

여기서 a 값이 Pormise인 경우 인데 이전 포스트에서 작성했던 go1함수를 이용해서 수정을 하면 된다.

```javascript
const go1 = (a,f) => a instanceof Promise ? a.then(f) : f(a)

L.map = function* (f, iter){
  for(const a of iter){
    yield go1(a, f)
  }
}
```

위와 같이 수정한 뒤에 코드를 실행 해 보면,

- [Promise, Promise, Promise]

resolve될 예정인 Promise값이 결과로 찍힌다. 이를 해결하기 위해 take 함수를 수정 해 주자.

```javascript
const take = curry((l, iter) => {
  let res
  iter = iter[Symbol.iterator]()
  let cur
  while(!(cur = iter.next()).done){
    const a = cur.value // 이 값이 Promise
    res.push(a)
    if(res.length === l) return res
  }
  return res
})
```

위 코드에서 cur.value값이 Promise라는 이야기다.

```javascript
const take = curry((l,iter) => {
  ...
  let cur
  while(!(cur = iter.next()).done){
    const a = cur.value
    if(a instanceof Promise) return a.then( a => { // 아래 코드 2번 실행 방지를 위해 return
			res.push(a)
      if(res.length === l) return res
    })
    res.push(a)
    if(res.length === l) return res
  }
  return res
})
```

중복되는 코드를 2번 실행하지 않기 위해 Promise의 인스턴스인지 체크하는 if문 다음에 return을 하였다. 하지만 return을 하게되면

반복문의 순회가 종료되게 된다. 이럴 때 **재귀**를 사용하면 좋다!

```javascript
const take = curry((l,iter) => {
  ...
	return function recur(){
    let cur
    while(!(cur = iter.next()).done){
      const a = cur.value
      if(a instanceof Promise) return a.then( a => { // 아래 코드 2번 실행 방지를 위해 return
			  res.push(a)
        if(res.length === l) return res
        return recur() // 재귀 실행
    })
    res.push(a)
    if(res.length === l) return res
  }
  return res
  }();
})
```

위와같이 재귀를 이용하여 구현 한다면 return을 하더라도 while문을 현재 이터레이터가 next() 함수를 호출한 만큼의 뒤 부터 순회가 다시 시작 될 것 이다.

여기서 코드의 축약이 가능한데,,

```javascript
res.push(a)
if(res.length === l) return res
return recur()
```

위 코드는 다음과 같이 축약이 가능하다.  

```javascript
(res.push(a), res).length === l ? res : recur()
```



결론 코드.

```javascript
const take = curry((l, iter) => {
  let res
  iter = iter[Symbol.iterator]()
	return function recur(){
    let cur
    while(!(cur = iter.next()).done){
      const a = cur.value
      if(a instanceof Promise) return a.then( a => { // 아래 코드 2번 실행 방지를 위해 return
        return (res.push(a), res).length === l ? res : recur() // 코드 간소화
    })
    res.push(a)
    if(res.length === l) return res
  }
  return res
  }();	
})	

```

위와같이 작성된 코드는

```javascript
go([Promise.resolve(1), Promise.resolve(2), Promise.resolve(3)],
   L.map(a => a+10),
   take(2),
   log
  ) // [11,12]
```

Promise로 시작된 연산에서도 잘 동작하고,

```javascript
go([1,2,3],
   L.map(a => Promise.resolve(a+10)),
   take(2),
   log
  ) // [11,12]
```

중간에 Promise가 생기더라도 제대로 동작한다!