---
title: 함수형 프로그래밍(queryStr, join)
date: 2019-12-09 22:12:01
category: javascript
---



> ## map, filter 계열 함수 vs reduce, take 함수

### 1.  map, filter

- map, filter 계열 함수는 이터러블 같은 모나딕한 값의 안쪽에 있는 원소에 함수를 합성
- 지연성을 갖는다.

### 2. reduce, take

- 실제로 값의 계산이 일어나는 함수
- 지연성을 갖지 않는다.
- take 같은 함수는 지연성을 줄 수 있지만, 배열을 축약 하는 성질을 있기 때문에 값의 종결의 기능이 더 강하다.



> ## Query String 함수 만들기



```javascript
log({limit : 10, offset : 10, type : 'notice'})

const queryStr = (obj) => go(...)
```

받은 객체를 url query string 으로 만들어주는 함수를 만들어보자.

```javascript
const queryStr = obj => go(
	Object.entries(obj)
)
```

Object.entries() 함수로 key와 value의 리스트를 만들자.

```javascript
const queryStr = obj => go(
	Object.entries(obj),
  map(([k,v]) => `${k}=${v}`),
)
```

리스트 구조분해를 이용해 key=value 형식으로 맵핑 해준다.

```javascript
const queryStr = obj => go(
	Object.entries(obj),
  map(([k,v]) => `${k}=${v}`),
  reduce((a,b) => `${a}&${b}`)
)
```

key=value형식으로 이루어진 리스트를 & 문자로 합쳐준다. 이제 로그를 찍어보면

```javascript
log(queryStr({limit : 10, offset : 10, type : 'notice'}))
//limit=10&offset=10&type='notice'
```

마지막으로

```javascript
const queryStr = pipe(
	Object.entries,
  map(([k,v]) => `${k}=${v}`),
  reduce((a,b) => `${a}&${b}`)
)
```

pipe 함수의 구조상 obj 을 받고 Object.entries()는 같은 obj을 받으니 생략이 가능하다.

```javascript
// 복습
const pipe = (f, ...fs) => (...as) => go(f(...as), ...fs)
```

queryStr 함수를 보면 마지막에 reduce 함수에서 구분자 &로 key, value 쌍을 합쳐줬다. 이거를 함수화 한다면 Array.porototype.join 함수와 같은 기능을 하는 함수를 구현 가능하다.



> ## join 함수



join 함수는 Array 객체의 프로토타입에 있는 함수로 Array 들만 사용할 수 있는 함수이다. 이를 이터러블 이터레이터 프로토콜을 따르는 모든 값들이 사용할 수 있게 구현 해보자.

```javascript
const join = (sep = ',', iter) => reduce((a,b) => `${a}${sep}${b}`, iter)
```

위 join 함수는 Array 뿐만 아니라 모든 이터러블 프로토콜을 따르는 값들이 사용할 수 있기 때문에 다형성이 기존의 join 함수보다 높다.

go, pipe 함수에서 사용하게 curry함수로 감싸주면 100점

```javascript
const join = curry((sep=',', iter) => reduce((a,b)=>`${a}${sep}${b}`, iter))
```

그럼 구현한 함수를 이용해서 queryStr 함수도 수정 해보자.

```javascript
const queryStr = pipe(
	Object.entries,
  map(([k,v]) => `${k}=${v}`),
  join('&')
)
```

위 코드는 현재 이전 포스트에서 정리 했던 엄격한 계산 방식을 사용한다. 하지만 map 함수에 지연성을 준다고 해도 코드는 같은 결과값을 만든다. ( join 함수는 이터러블-이터레이터 프로토콜을 따르니까! )

Objec.entries 함수도 직접 구현하여 지연성을 줄 수 있다.

```javascript
L.entries = function *(obj){
  for(const key in obj) yield [key, obj[key]]
}
```

마지막으로 queryStr 함수를 지연성을 갖는 제너레이터 함수들로만 구성한다면

```javascript
const queryStr = pipe(
	L.entries,
  L.map(([k,v] => `${k}=${v}`)),
  join('&')
)
```

