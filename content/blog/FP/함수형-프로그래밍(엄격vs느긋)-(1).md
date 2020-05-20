---
title: (중요) 함수형 프로그래밍(연속된 제너레이터)
date: 2019-12-07 23:12:01
category: FP
---


연속된 제너레이터의 계산 순서를 알아보기 전에 map 함수와 filter 함수도 제너레이터를 이용하여 구현해보자.

#L.map

```javascript
const L = {}
L.map = function *(f, iter){
  for(const a of iter){
    yield f(a)
  }
}
```

기존 map함수와 다른 점은 

```javascript
const mapList = L.map(a => a + 10, [1,2,3])
```

위와 같이 이터레이터를 생성한 것 만으로는 내가 원하는 값을 구할 수 없고 next() 함수를 호출 한 만큼의 값만 얻을 수 있다.

```javascript
log(mapList.next()) // {value : 11, done : false}
```

따라서 array를 직접 생성하지 않기 때문에 내가 원하는 대로 평가가 가능하다

```javascript
log(...mapList) // 1 2 3 
log([...mapList]) // [1, 2, 3]
```

스프레드 연산자로 next() 함수를 호출하여 평가할수도 있고

```javascript
log([mapList.next().value]) // [11]
```

직접 next() 함수를 호출하여 평가도 가능하다.

# L.filter

L.map과 비슷한 구현 코드를 갖는다.

```javascript
const L.filter = function *(f, iter){
  for(const a of iter) if(f(a)) yield a
}
const it = L.filter( a => a%2, [1,2,3,4] )
log(it.next()) // { value : 1, done : false }
log(it.next()) // { value : 3, done : false }
log(it.next()) // { value : undefined, done : true }
```



# 엄격한 계산 순서 vs 느긋한 계산 순서 (중첩사용)

- 기존에 사용했던 함수들을 이용

```javascript
go(
  range(10),
  map(a => a+10),
  filter(a => a % 2),
  take(2),
  log
) // [11, 13]
```

- 느슨한 함수들을 이용

```javascript
go(
	L.range(10),
  L.map(a => a+10),
  L.filter(a => a % 2),
  take(2),
  log
) // [11, 13]
```

**두 가지 방식의 코드의 결과는 같지만 두 코드가 동작하는 방식, 순서가 다르고 리턴되는 값, 함수들이 평가되는 순서도 다르다.**



> ## 엄격한 계산의 순서



먼저 엄격한 방식부터 살펴보면 range(10) 함수부터 실행되게 된다. 크롬 개발자 도구의 break point를 이용하여 함수에서 변수들이 어떻게 평가되는지 살펴보자.

![image](https://user-images.githubusercontent.com/39187116/70369536-81ffb480-18fe-11ea-8d53-a7bba032ed70.png)

range(10)을 하였을 때 함수가 실행되고 지역변수 l 값이 10으로 평가된다.

![image](https://user-images.githubusercontent.com/39187116/70369562-f0447700-18fe-11ea-8be8-f82e115828eb.png)

그 뒤 while 문에 들어가면서 i 값이 증가되고 res 배열에 하나씩 담고 l 값와 i 값이 같아질 때 함수를 종료한다.

다음으로 실행되는 map 함수를 break point로 확인 하기전에 이터러블을 인자로 받는 함수들의 내부 코드를 좀더 세세하게 확인할 수 있게 수정해보자. ( 명령형 코드로 전환 )

```javascript
const map = curry((f, iter) => {
  let res = []
  for(const a of iter) res.push(f(a))
  return res
})
```

기존의 코드를!

```javascript
const map = curry((f, iter) => {
  iter = iter[Symbol.iterator]() // 이터러블 => 이터레이터
  let res = []
  let cur // 현재 처리되는 값을 담을 곳
  while(!(cur = iter.next()).done){
    const a = cur.value
    res.push(f(a))
  }
})

... 나머지 함수들도!
```

위와 같이 명령형 코드로 전환하여 개발자 도구로 확인해 보자.

![image](https://user-images.githubusercontent.com/39187116/70369838-9b0a6480-1902-11ea-8be5-6bb182cf75f3.png)

range(10) 함수가 평가된 값이 map 함수의 이터러블 인자로 온다. ( go 함수 내부에서 전달 ) 인자로 전달 받은 배열은 

6번 줄을 거쳐

![image](https://user-images.githubusercontent.com/39187116/70369858-dad14c00-1902-11ea-9fca-0177071d1536.png)

위와 같이 Array Iterator {} 로 변환 된다.

![image](https://user-images.githubusercontent.com/39187116/70369894-3d2a4c80-1903-11ea-8f12-f8c7b61b4123.png)

그 뒤에 while문 안에 들어가여 cur 값에 next() 함수의 결과값을 담고 done이 true가 나오기 전까지 a 값에 next() 함수의 value값을 담으면서 res에 push 하는 구조이다.

filter, take도 map 과 같은 방식으로 동작한다. 결론은 **전체적인 함수의 흐름이 순차적으로 실행 된다.( 가로 방향 )**



> ## 느긋한 계산의 순서



우선 Lazy 함수들도 직관적인 계산 확인이 가능하게 명령형 코드로 수정을 해보자.

```javascript
const L.map = curry(function *(f, iter){
  iter = iter[Symbol.iterator]()
  let cur
  while(!(cur = iter.next()).done){
    const a = cur.value
    yield f(a)
  }
})

const L.filter = curry(function *(f, iter){
  iter = iter[Symbol.iterator]()
  let cur
  while(!(cur = iter.next()).done){
    const a = cur.value
    if(f(a)) yield a
  }
})
```

위와 같이 수정을 한 뒤 엄격한 계산과 같은 결과를 출력하는 코드를 실행 시키자.

```javascript
go(
	L.range(10),
  L.map(a => a + 10),
  L.filter(a => a % 2),
  take(2),
  log
) // [11, 13]
```

**엄격한 방식의 계산의 순서**는

- range(10) => map(a => a+10) => filter( a => a % 2) => take(2) => log

처럼 **가로방향으로 순차적으로 이전 함수의 결과가 다음 함수에 전달되는 형식으로 실행** 되었다.

그럼 느긋한 계산 방식은 어떤점이 다를까? 결론부터 말하면 느긋한 계산방식은 위 코드를 실행 시켰을 때 **가장 먼저 실행되는 함수는 take 함수이다.** 그 이유를 살펴보자.

![image](https://user-images.githubusercontent.com/39187116/70371878-da917a80-191b-11ea-84e9-a33d6b7ee0a4.png)

break point를 찍었을 때 가장 먼저 break가 걸린 구간을 확인한 결과이다. 보이는 것과 같이 take 함수에서 break 포인트가 걸렸다.

이유는 **L.range, L.map, L.filter 함수는 제너레이터이다. 제너레이터는 함수 자체를 실행 했을때는 내부 코드가 동작하지 않고 제너레이터에 의해 생성된 이터레이터가 next() 함수를 호출 했을 때 제너레이터 내부의 코드가 동작한다.**

따라서 L.range(10) 함수를 실행 시켰을 때 함수 내부의 코드가 동작하지 않고 일단 이터레이터를 리턴한다. 이 이터레이터는 L.map 함수의 파라미터로 전달 된다. 하지만 L.map 함수 역시 제너레이터기 때문에 L.map 함수가 리턴하는 이터레이터의 next() 함수가 호출 될 때 까지 함수 내부의 코드가 동작하지 않고 바로 이터레이터를 리턴한다. 이 이터레이터는 L.filter 함수의 파타미터로 전달 되는데 L.filter 함수 역시 제너레이터 이기 때문에 이터레이터를 리턴하고 take 함수가 이것을 받는다.

정리해보면

- L.range(10) 실행 => L.range 함수가 이터레이터 리턴 => L.map 함수가 이터레이터 리턴 => L.filter 함수가 이터레이터 리턴 => take 함수 실행

그 다음 동작을 살펴보자.

```javascript
//take 함수
iter = iter[Symbol.iterator]()
let cur
while(!(cur = iter.next()).done){ // iter.next() => filter 함수 내부코드 동작
  ...
}
```

take 함수는 L.filter 함수의 이터레이터를 받았기 때문에  iter.next() 함수를 호출 하였을 때 L.filter 함수의 내부코드가 동작한다.

```javascript
//L.filter 함수
while(!(cur = iter.next()).done){ // iter.next() => Map 함수 내부코드 동작
  ...
}
```

L.filter 함수의 내부코드가 동작 하는데 이 함수가 받은 이터레이터는 L.map 함수의 이터레이터이다. 따라서 L.filter 함수 내부의 iter.next()는 L.map 함수 내부의 코드를 동작 시킨다. 같은 방식으로 L.map 함수의 인자 이터레이터가 next() 함수를 호출하면 L.range() 함수내부 코드가 동작한다. 

```javascript
// L.map 함수
let cur // range 함수에서 yield 한 { value : 0, done : false} 값 저장
while(!(cur = iter.next()).done){
  const a = cur.value // 0
  yield(f(a))
}
```

L.range 함수에서는 인자로 받은 이터레이터가 없기 때문에 내부 코드가 동작하면 L.map 함수 내부의 cur값에 자신이 yield 한 { value : 0, done : false } 값이 저장된다. 

L.map 함수는 cur.value 값이 저장된 a 를 자신이 받은 함수로 평가하여 yield 하고 이를 L.filter 함수 내부의 cur 값에 저장한다.

```javascript
// L.filter 함수
let cur // map 함수에서 yield 한 { value : 10, done : false } 값 저장
while(!(cur = iter.next()).done){
  const a = cur.value // 10
  if(f(a)) yield a
}
```

여기서 평가된 a는 if 조건문을 만족 시키지 않기 때문에 ( 10 => 10 % 2 ) L.filter 함수는 take 함수로 값을 yield 하지 않고 다시 while 문을 돌게 된다. 그럼 iter.next() 함수에 의해 다시 L.map 함수의 제너레이터 내부 코드 실행 시키고 이는 L.range 함수의 내부코드를 실행시켜 L.range함수는 { value : 1, done : false }를 yield 한다. 이 값을 L.map 함수에서는 { value : 11, done : false } 로 yield 하고 L.filter 함수에서 f(a) 조건문을 총족시켜 take 함수로 값을 yield 한다.

이것이 느긋한 방식으로 코딩된 함수들의 계산 순서이다.

결론을 지으면 **엄격한 계산은 각 함수에서 리스트를 생성하여 다음 함수로 리스트를 전달하는 계산 순서를 갖는다면 ( 가로방식 ), 느긋한 계산 방식은 값 하나씩 yield 하는 방식 즉,**

- 1 => 11 => 11
- 3 = > 13 => 13

**최종값 [11, 13] 라는 계산 순서를 갖는다.**



> # 효율성



엄격한 방식과 느슨한 방식의 계산 효율을 따져보자.

```javascript
console.time('')
go(
	range(10000),
  map(a => a+10),
  filter(a => a%2),
  take(2),
  log
)
console.timeEnd('')
```

엄격한 계산 방식에서는 range 함수로 생성하는 Array의 크기가 클 수록, 각 함수에서 Array를 처리하는데 걸리는 시간이 길어진다.

```javascript
console.time('L')
go(
	L.range(10000),
  L.map(a => a+10),
  L.filter(a => a%2),
  take(2),
  log
)
console.timeEnd('L')
```

하지만 느슨한 방식에서는 원소 하나씩 yield 하는 방식을 사용하기 때문에 성능상에 차이가 없다.

![image](https://user-images.githubusercontent.com/39187116/70386629-6f5cac80-19de-11ea-91ad-358fa151a18b.png)

따라서 수행시간 에서 차이가 크게 발생한다. 
