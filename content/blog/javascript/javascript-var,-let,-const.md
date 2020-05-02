---
title: Javascript var, let, const
date: 2020-04-30 20:04:36
category: javascript
---

`var`, `let` , `const` 의 차이점을 알아보자..!

## 1. scope 

`var`는 `function scoped` 이고 `let`, `const`는 `block-scoped` 이다.

###Var

<hr/>

변수의 범위와, `hoisting` 범위 모두 `function`

#### 예시1

```javascript
for (var i=0; i<10; i++){
  console.log('i', i)
}
console.log('after loop i', i) // 10
```

예시1 에서는 `i`가 `function scoped` 이기 때문에 loop가 끝난 후 `i`를 호출하면  출력이 잘 된다.

#### 예시2

```javascript
function example(){
  for (var i=0; i<10; i++){
  	console.log('i', i)
	}
}
example()
console.log('after loop i', i) // Reference Error: i is not defined
```

예시2 에서는 `function scoped` 밖에서 호출해서 에러가 뜬다.

위와 같은 방법을 이용해서.. 예전에는 이렇게 변수 관리를 했다고 한다.

```javascript
var gVariable = (function(name){
  for (var i=0; i<10; i++){
    console.log('i', i)
  }
  console.log(name) // 'kim'
})('kim')
console.log('after loop i', i) // Reference Error: i is not defined
```

즉시 실행 함수를 이용하여 함수 내부의 변수를 priavte화 하고 ( scope가 function 이니까!) `gVariable` 는 전역변수로 사용했다고 한다.

단 여기서도 알아둬야 할 점이 있다.

```javascript
var i
(function(name){
  for (i=0; i<10; i++){
    console.log('i', i)
  }
  console.log(name) // 'kim'
})('kim')
console.log('after loop i', i) // after loop i 10
```

예시2 에서 `var` 키워드를 뺀다면 위와 같이  `i` 가 `hoisting` 되어 `global variable`이 된다. 

예전에는 이를 막기위해 아래와 같은 방법을 썻다고 한다.

```javascript
(function(name){
  'use strict'
  for (i=0; i<10; i++){
    console.log('i', i)
  }
  console.log(name) // 'kim'
})('kim')
console.log('after loop i', i) // Reference Error
```

`use strict` 를 사용하여 `i`변수가 `hoisting` 되는 것을 막는다. `use strict`는 이처럼 `hoisting`을 막기도 하고, `this` 가 `window` 객체를 가리키지 못하게 한다.

<br/>

### let, const

<hr/>

변수의 범위와, `hoisting` 범위 모두 `blocked`

`let` 과 `const` 는 `blocked scoped` 이다.  `let`과 `const`의 차이는 변수의 `immutalbe` 여부이다. `let`은 변수에 재할당이 가능하지만, `const` 는 변수 재선언, 재할당 모두 불가능하다. 

위에서 사용했던 예시1을 비교해 보면, 

```javascript
for (let i=0; i<10; i++){
  console.log('i', i)
}
console.log('after loop i', i) // Reference Error
```

`i`가 `blocked scoped` 이기 때문에 참조 에러가 발생한다.

다른예시.

```javascript
for (var i=0; i<10; i++){
  setTimeout(function(){console.log(i)}, 3000)
}
```

위와 같은 코드를 실행하면 i가 10으로 10번 출력된다. 그 이유는 for문이 끝나는 시점의 `i` 변수는 `var`로 선언 되었기 때문에 `function scoped`를 갖게 되고 해당 시점의 `i`값은 10이기 때문이다.

- 해결방법1

선언을 `let`으로 한다면,

```javascript
for (let i=0; i<10; i++){
  setTimeout(function(){console.log(i)}, 3000)
}
```

자바스크립트 엔진이 실행될 때 블록이 10개 생기고 각 블록의 `i` 값을 참조하기 때문에 올바르게 원하는 값을 출력 할 수 있다. 

- 해결방법2

위에서 정리한 즉시실행함수(IIFE)를 이용

```javascript
for (var i=0; i<10; i++){
  (function(count){
    setTimeout(function(){
      console.log(count)
    },3000)
  })(i)
}
```

<br/>

## 2. 중복 선언

`var`는 중복 선언을 허용한다

`let` 과 `const`는 중복 선언을 허용하지 않는다.

```javascript
var a = 1
var a = 2 // okay
let b = 1
let b = 2 // Nooo 
```

<br/>

## 3. 선언과 초기화

- `var`는 변수의 선언과 동시에 `undefined`로 초기화가 된다.

- `let`은 변수 선언 이전에 참조를 하면 Reference Error가 발생한다. 선언과 동시에 `undefined`로 초기화 되는 `var`와 다르게 `let`은 선언만 되고 대입 연산자를 만날 때 까지 값이 없다. 따라서 값이 할당 되기 전까지 참조를 하게 되면 Reference Error를 발생하게 되고 이 구간을 `TDZ(temporal dead zone)` 이라고 한다.

```javascript
console.log(a) // undefined
var a = 'hi'
```

`a` 변수가 `hoisting` 되어 `scope` 상단에 선언과 동시에 `undefined`로 초기화 되었기 때문에 오류가 발생하지 않는다.

```javascript
console.log(b) // Uncaught ReferenceError: b is not defined
let b = 'hi'
```

`b` 변수가 `hoisting` 되어 `scope` 상단에 선언 되겠지만, 값이 초기화 되지 않기 때문에 참조하려고 하면 에러를 발생시킨다.

- `const`는 선언과 동시에 초기화 해줘야 한다! 
