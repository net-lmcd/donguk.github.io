---
title: Map - Javascript
date: 2020-04-21 21:04:38
category: javascript
---
`Map` 객체는 Key - Value Collection 이며, 삽입 순서도 기억한다. 어떠한 자료형(객체, 원시값)도 Key나 Value로 올 수 있다.

### 개발하다 알게된 것
- Typescript 에서 Map 자료구조의 타입은 `Map<K,V>`로 표현 가능하다.

<br/>

### 선언

```javascript
new Map([iterable])
```

`[key, value]` 를 원소로 갖는 이터러블을 인자로 받는다. 각 값들은 삽입 순서대로 Map 객체에 추가된다.

### new Map() vs Object ( Map을 사용하는 이유 )

1. Object는 프로토타입을 가지므로 기본 키가 존재할 수 있다. 하지만 Map은 명시적으로 추가시킨 키만 존재한다. (키 충돌 위험이 없다.)

2. Object는 키에 반드시 String 또는 Symbol 자료형이 와야한다. 하지만 Map은 어떤 값이 와도 된다.
3. Map의 키는 정렬 가능하다. 
4. Map은 `size`를 이용해서 크기를 쉽게 구할 수 있다. Object는 직접 알아내야 한다.
5. Map은 순회가 가능하다. 하지만 Object는 Key 배열을 구해서 순회해야 한다.
6. 잦은 key - value 쌍 추가와 제거에 성능이 더 좋다.

### 속성

1. `Map.length` :  값이 0인 속성. (**인스턴스의 `size` 와 다르다.**)
2. `Map.prototype`  : `Map` 생성자의 프로토타입을 나타낸다. 모든 `Map`  인스턴스에 속성을 추가할 수 있다. 

<br/>

### Map 인스턴스

#### 속성

1. `Map.prototype.constructor`  : 인스턴스의 프로토타입을 만든 함수를 반환한다. 이는 기본으로 `Map` 함수이다.
2. `Map.prototype.size` : 객체 내 Key-Value쌍의 수를 리턴한다.

#### 메소드

```javascript
const m = new Map();
m.set('A',1)
m.set((()=>{}, 'function'))
m.set({}, 'Object')
```

1. `set(key, value)` : Map에 Key-Value Pair원소를 추가한다.

2. `clear()` : Map 객체의 모든 Key-Value pair를 제거한다.
3. `delete(key)` : Map에서 Key와 해당되는 Value를 제거하고 있는 값이 제거되면 true를 리턴한다. 아니면 false

4. `entries()` : (삽입 순으로) 객체 내 값에 대한 `[key, value]` 새로운 이터레이터를 리턴한다.

```javascript
for (const v of m.entries()) console.log(v)
// ['A',1], [()=>{}, 'function'], [{}, 'Object']
```

5. `has(key)` : Map 객체 안에 주어진 Key/Value pair가 있는지 검사하고 boolean 값을 반환한다.
6. `get(key)` : 주어진 Key에 대응되는 value를 리턴하고 없다면 undefined를 리턴한다.
7. `keys()` , `values()` : 삽입 순서대로 key로만 이루어진 또는 value로만 이루어진 이터레이터를 반환한다.

### 참고

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Map
