---
title: 함수형 프로그래밍(L.map->map)
date: 2019-12-10 22:12:11
category: javascript
---

> # L.map 으로 map 만들기



```javascript
const map = curry((f,iter) => {
  let res = []
  for(const a of iter){
    res.push(f(a))
  }
  return res
})
L = {}
L.map = curry(function* (f,iter){
  for(const a of iter){
    yield f(a)
  }
})
```

기존의 map 과 L.map 함수이다. **map 함수의 단점(?)은 만약 인자로 받은 이터러블의 원소 전체를 맵핑 하지 않고 일부를 맵핑 한다고 해도 for of 문을 끝까지 순회하며 전체 원소가 맵핑된 결과값을 리턴한다는 것 이다.** 하지만 결과 값이 값 그 자체이다. ( 예를들면 [1, 2, 3, 4, 5] )

**L.map 함수의 경우는 필요한 원소만 순회하는 장점이 있지만 L.map 함수가 리턴하는 이터레이터의 next() 함수가 호출 될 때 까지 값의 연산이 일어나지 않는다.**  따라서 두 장점을 모두 가지는 map 함수를 한번 만들어보자.

```javascript
const map = curry((f,iter) => go(
	iter,
  L.map(f),
  take(Infinity)
))
```

위와 같이 구현 한다면 L.map 함수의 지연성과 이터레이터 객체가 아닌 결과 값을 리턴하는 기능을 모두 가질 수 있다.

원리는 L.map (제너레이터)함수가 이터레이터를 리턴하면 take 함수에서 받은 이터레이터의 next() 함수를 호출 하면서 파라미터 iter의 길이 만큼 순회를 하고 값을 리스트로 반환 한다.

마지막으로 코드를 좀 더 축약한다면

```javascript
const map = curry((f,iter) => go(
	L.map(f, iter),
  take(Infinity)
))
```

인자로 받은 f, iter 값을 go 함수의 첫 함수에 넣어주기 때문에 다음과 같이 수정할 수 있다.

```javascript
const map = curry(pipe(
	L.map,
  take(Infinity)
))
```

pipe 함수 복습!

```javascript
const pipe = (f, ...fs) = (...as) => go(f(...as), ...fs)
```



> # L.filter 로 filter 만들기



```javascript
const filter = curry((f,iter) => {
  for(const a of iter) if(f(a)) yield a
})

L.filter = curry((f,iter) => {
  for(const a of iter){
    if(f(a)) yield a
  }
})
```

위에서 구현한 map 함수처럼 filter 함수도 구현 가능하다.

```javascript
const filter = curry((f,iter) => go(
	L.filter(f, iter),
  take(Infinity)
))
```

인자와, 첫 함수에 전달되는 인자가 같기 때문에 축약 시킨다면

```javascript
const filter = curry(pipe(
	L.filter,
  take(Infinity)
))
```

