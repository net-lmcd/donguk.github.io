---
title: 함수형 프로그래밍(L.flatMap)
date: 2019-12-16 02:12:37
category: javascript
---

># L.flatMap



## 1. Array.prototype.flatMap

L.flatMap 함수를 구현하기 전에 자바스크립트에 내장된 Array.prototype.flatMap 함수에 대해 알고 지나가 보자.

flatMap 함수는 map 함수와 비교하여 생각하며 이해하기 쉽다.

```javascript
const animals = ['dog', 'cat', 'snake']
const fruits = ['apple', 'melon', 'orange']
const a = animals.map( (el, idx) => [el, fruits[idx]])
log(a) // [['dog','apple'],['cat','melon'], ['snake','orange']]
```

map 함수는 array의 각 원소를 파라미터로 전달된 함수의 return 값에 맞게 맵핑시킨다.

```javascript
const b = animals.flatMap( (el, idx) => [el, fruits[idx]])
log(b) // ['dog','apple','cat','melon','snake','orange']
```

flatMap 함수는 **map함수의 return 값에 flat() 함수를 적용한 것 과 같은 결과**를 보여주는 함수이다.

이 함수는 다음과 같이도 사용될 수 있다. ( 맵핑되는 원소 안 하나하나를 맵핑! )

```javascript
const result = [[1,2],[3,4],[5,6,7]].flatMap( a => a.map(a => a*a))
log(result) // [1,4,9,16,25,36,49]
```

이 코드는 다음과 같이도 변경될 수 있다. 이전 포스트에서 공부했던 지연성을 갖는 flatten 함수를 이용하는 것 이다.

```javascript
const result = flatten([[1,2],[3,4],[5,6,7]].map( a=> a.map(a => a*a)))
log(result) // [1,4,9,16,25,36,49]
```

하지만!! 위 코드는 **비효율적**인 부분이 있다

**map 함수에서 Array의 모든 값을 순회 하면서 새로운 배열을 만든다.**

```javascript
const arrayResult = [[1,2],[3,4],[5,6,7]].map(a => a.map( a => a*a))
log(arrayResult) // [[1,4],[9,16],[25,36,49]]
```

즉, 말그대로 map 함수에서 모든 값을 순회 하면서 새로운 배열을 만든 뒤, flatten 함수에서 또 모든 원소를 순회하기 때문에 약간의 비효율이 발생한다.

참고

```javascript
log([[1,2],[3,4],[5,6,7]].flatMap( a=> a.map(a => a*a))))
log(flatten([[1,2],[3,4],[5,6,7]].map( a=> a.map(a => a*a))))
```

위 두개의 코드는 시간복잡도 면에서는 차이가 없다. 두 코드 모두 순회해야할 것 들을 모두 순회하는 것 이기 때문에 그렇다. 만약 두 코드에 결과값의 길이를 제한하는 take 함수 같은 함수를 사용한다면 아래 코드가 더 효율성이 좋다.

따라서 효율성이 좋은( 지연성을 갖는 ) L.flatMap을 구현해보자!



## 2. L.flatMap



- ### 구현

```javascript
L.flatMap = pipe(L.map, L.flatten)
```

L.flatMap은 L.map 함수를 한번 하고 L.flatten 함수를 한번 돌린것과 같다. ( flatten 함수는 즉시평가니까 x ) 나중에 curry도 할 수 있도록 묶어주자.

```javascript
L.flatMap = curry(pipe(L.map, L.flatten))
```

만든 함수로 예제도 풀어보자!

```javascript
const it = L.flatMap(map( a => a*a), [[1,2],[3,4],[5,6]])
log(it.next()) { value : 1, done : false}
log(it.next()) { value : 4, done : false}
```

이 예제코드를 이해하는데 시간이 많이 걸렸다..분석 해보자

L.flatMap 함수의 파라미터로 두개의 값 map(a => a*a) , [[1,2],[3,4],[5,6]] 을 받아서 curry 함수에 의해 나중에 처리되지 않고 pipe 함수를 실행 한다.

```javascript
//curry 복습
const curry = f => (a, ..._) => ..._.length ? f(a, ..._) : (..._) => f(a, ..._)
```

다음으로 가기전에 pipe 함수를 살펴보고 가겠다.

```javascript
//pipe 복습
const pipe = (f, ...fs) => (...as) => go(f(...as), ...fs)
```

pipe 함수는 실행되면 (...as) => go(f(...as), ...fs) 를 리턴한다. 위에서 map( a=> a*a ) 와 이터러블은  ...as 부분에 해당되고

L.map 함수는 f 에 해당된다. 따라서 아래와 같이 코드가 동작한다.

```javascript
L.map(map(a => a*a, [[1,2], [3,4], [5,6]]))
```

L.map 함수는 지연성을 갖는 함수니까 바로 실행이 되지 않지만 나중에 L.map 함수가 리턴한 이터레이터의 next() 함수가 호출 됐을경우를 미리 알아보면 ( 첫번째 순회 때 !!)

```javascript
// 이터레이터 첫번쨰 순회 떄 ! 
// 설명용 L.map 
L.map = function *(f, iter){
  for(const a of iter){
    yield map(b => b*b)[1,2]
  }
}
```

위와 같이 표현 된다. 여기서 파라미터로 받은 map 함수 역시 이~~~전 포스트에서 L.map 함수와 take 함수를 이용해서 구현 했었다.

```javascript
//map 복습
const map = curry(pipe(
	L.map,
  take(Infinity)
))

```

여기에 위에서 받은 인자를 적용해보면,

```javascript
const map = ( b => b*b, [1,2]) => go(
	L.map(b => b*b, [1,2]),
  take(Infinity)
)
```

위와 같이 나타낼수 있고 map 함수는 [1,4] 로 평가된다. 이 평가된 값을  L.map ( 설명용 L.map ) 에서 yield 하게 된다.

이 [1,4] 로 yield 된 값을 L.flatten 함수에서 받아서 1,4 로 두번 yield 하게 되는 구조이다. 이와 같은 과정이 총 3번 일어난다. (  [[1,2],[3,4],[5,6]] 이니까 !)



- ### 즉시평가 flatMap

```javascript
const flatMap = pipe(L.flatMap, take(Infinity))
//또는
const flatMap = curry(pipe(L.map, L.fletten, take(Infinity)))
const flatMap = curry(pipe(L.map, fletten))
```

># 실무에 적용한 이터러블 예제


```javascript
 const users = [
    {
      name: 'a', age: 21, family: [
        {name: 'a1', age: 53}, {name: 'a2', age: 47},
        {name: 'a3', age: 16}, {name: 'a4', age: 15}
      ]
    },
    {
      name: 'b', age: 24, family: [
        {name: 'b1', age: 58}, {name: 'b2', age: 51},
        {name: 'b3', age: 19}, {name: 'b4', age: 22}
      ]
    },
    {
      name: 'c', age: 31, family: [
        {name: 'c1', age: 64}, {name: 'c2', age: 62}
      ]
    },
    {
      name: 'd', age: 20, family: [
        {name: 'd1', age: 42}, {name: 'd2', age: 42},
        {name: 'd3', age: 11}, {name: 'd4', age: 7}
      ]
    }
  ];
```



### - family 중에서 성인 유저 4명의 나이 모두 더하기

```javascript
const result = go(
	L.map( u => u.family),
  L.flatten, // L.flatMap( u => u.family ) 가능
  L.filter(u => u.age > 20),
  L.map(u => u.age),
  take(4),
  reduce((a,b) => a+b)
)
```

- 객체지향 프로그래밍은 데이터를 먼저 만들고(가공하고) 그 이후에 메소드를 만들어 간다면, 함수형 프로그래밍은 이미 만들어져 있는 함수가 있다면 그 함수들에 맞는 데이터 구성을 하는 방식 즉 함수가 프로그래밍 우선순위에 있는 방식!

