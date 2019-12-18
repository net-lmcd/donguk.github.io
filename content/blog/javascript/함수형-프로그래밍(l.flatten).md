---
title: 함수형 프로그래밍(L.flatten)
date: 2019-12-15 23:12:71
category: javascript
---

> # L.flatten

L.flatten 함수는 다음과 같은 기능을 갖는다.

```javascript
[[1,2,3],4,5,[6,7],[8,9,10]]
```

위 와 같은 2차원 배열을 depth가 1인 1차원 배열로 펼쳐주는 함수이다. 

그럼 구현을 해보자. 먼저 L.flatten 함수는 지연성을 갖기 때문에 제너레이터를 사용하고, 인자로 이터러블을 받는다.

```javascript
L.flatten = function* (iter){
  for(const a of iter){
    if(isIterable(a)) for(const b of a) yield b
    else yield a
  }
}
```

이터러블을 인자로 받아서 for of문을 순회 한다. 만약 순회되는 이터레이터의 value값이 이터러블 이면 다시한번 for of문을 순회하고, 아닐 경우에는 원소를 yield하는 구조이다.

여기서 코드를 조금 간략하게 변경할 수 있다.

```javascript
L.flatten = function* (iter){
  for(const a of iter){
    if(isIterable(a)) yield *a // yield *a는 for(const var of a) yield var
    else yield a
  }
}
```

**yield *a는 for(const var of a) yield var와 같은 기능을 하는 코드이다. ** 이제 로그를 찍어보자.

```javascript
const it = L.flatten([[1,2,3],4,5,[6,7],[8,9,10]])
log(it) // L.flatten iterator
```

L.flatten 함수로 생성된 이터레이터는 next() 함수를 호출하여 원하는 value를 구할 수 있고, for of 문, 전개연산자 등 다양하게 사용될 수 있다.

```javascript
log(it.next()) // {value : 1, done : false}
log([...it])// [2,3,4,5,6,7,8,9,10]
```

이전 포스트에서 공부했던 take함수도 함께 사용할 수 있다.

```javascript
log(take(5, it)) // [1,2,3,4,5]
```

```javascript
//take함수 복습
const take = curry((l,iter) => {
  let res = []
  for(const a of iter){
    res.push(a)
    if(res.length === l) return res
  }
  return res
})
```

만약 **즉시평가를 원하는 flatten 함수**를 사용하고 싶다면?

```javascript
const flatten = pipe(
	L.flatten,
  take(Infinity)
)
log(flatten([[1,2,3],4,5,[6,7],[8,9,10]])) //1,2,~,8,9,10
```

마지막으로 depth가 1이 아니고 깊게 있는 이터러블을 모두 펼치고 싶다면?

```javascript
L.deepFlatten = function *deep(iter){
	for(const a of iter){
    if(isIterable(a)) yield *deep(a) // 재귀적으로 수행
    else yield a
  }
}
```

재귀함수를 이용하여 깊이가 깊은 배열도 펼칠 수 있다.