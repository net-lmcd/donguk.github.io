---
title: Set - JavaScript
date: 2020-04-21 13:04:87
category: javascript
---


<br/>

`Set` 객체는 자료형에 관계 없이 원시값 ( 객체도, 메소드도 아닌 값 ) 과 객체 참조 모두 유일한 값을 저장 할 때 사용한다.

### 선언

```javascript
new Set([iterable])
```

이터러블이 인자로 전달되면 그 원소 값들이 모두 Set에 추가된다. `null`을 전달하면 비어있게 된다.

### 설명

1. `Set` 객체는 값 컬렉션으로, **삽입 순서대로** 요소를 순회할 수 있다.
2.  하나의 `Set` 에서 모든 원소들은 **유일하다.**

Set내에서 몇가지 유일함과 관련하여 특징이 있다.

- `NaN` 과 `undefined` 도 Set에 저장할 수 있고, 이 값은 한개만 저장된다. (유일)

- +0 과 -0은 구분하지 않는다. ( 둘다 0으로 저장 )

### 속성

1. `Set.length` :  값이 0인 속성. (**인스턴스의 `size` 와 다르다.**)
2. `Set.prototype`  : `Set` 생성자의 프로토타입을 나타낸다. 모든 `Set` 객체에 속성을 추가할 수 있다. 

<br/>

### Set 인스턴스

#### 속성

1. `Set.prototype.constructor`  : 인스턴스의 프로토타입을 만든 함수를 반환한다. 이는 기본으로 `Set` 함수이다.
2. `Set.prototype.size` : 객체 내 값의 개수를 리턴한다.

#### 메소드

```javascript
const s = new Set();
s.add('A')
s.add('B')
s.add('C')
```

1. `add(value)` : Set에 원소를 추가한다.

2. `clear()` : Set 객체에서 모든 요소를 제거한다.
3. `delete(value)` : Set에서 value원소를 제거하고 있는 값이 제거되면 true를 리턴한다. 아니면 false

4. `entries()` : (삽입 순으로) 객체 내 값에 대한 `[value, value]` 새로운 이터레이터를 리턴한다.

```javascript
for (const v of s.entries()) console.log(v)
// ['A','A'], ['B', 'B'], ['C', 'C']
```

5. `has(value)` : Set 객체 내 value 원소가 있는지 체크한다.
6. `keys()`  === `values()` :  (삽입 순으로)둘은 같은 함수로 Set 객체 내 각 요소에 대한 값을 포함하는 새로운 이터레이터를 리턴한다.

### 참고

https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Set
