---
title: Javascript this
date: 2020-05-08 13:05:60
category: javascript
---

_https://poiemaweb.com/js-this 를 공부하고 정리_

<br/>

## 들어가기 전에,

JS의 함수는 호출될 때 매개변수로 전달되는 인자값 이외에 `arguments` 객체와 `this`를 암묵적으로 전달받음

```javascript
function hello(){ // 인자
  console.log(arguments)
  console.log(this) // window
}
console.log(hello('donguk'))
```

`arguments`는 함수로 전달된 매개변수의 값들이 유사배열의 형태로 저장되어 있다.

 `arguments`는 이터러블이다. 따라서 순회가 가능하다.

```javascript
function hello(){ return arguments }
for (const v of hello(1,2,3)) console.log(v) // 1 2 3
```

<br/>

## 함수 호출 방식과 this 바인딩

JS에서는 함수를 호출할 때 함수가 **어떻게 호출되었는지에 따라 ** this에 바인딩할 객체가 **동적**으로 결정된다.

함수의 호출하는 방식은 4가지가 있다.

- 함수 호출
- 메소드 호출( 객체의 Property )
- 생성자 함수 호출
- apply / call / bind 호출

### 1. 함수 호출

함수 호출에서 `this`는 전역객체에 바인딩된다. (Browser-Side 는 `window` , Server-Side (NodeJS)는 `global` )

```javascript
function foo() {console.log(this)} // window
```

내부함수의 경우도 `this`는 외부함수가 아닌 전역객체에 바인딩된다.

```javascript
function foo(){
  function bar(){console.log(this)} // window
  bar()
}
```

또한 **메소드의 내부함수일 경우**에도 `this`는 전역객체에 바인딩된다.

**콜백함수**의 경우에도 `this`는 전역객체에 바인딩된다.

```javascript
var name = 'ddddonguk'
var obj = {
  name: 'donguk',
  foo: function(){
    console.log(this.name) // donguk
    function bar(){console.log(this.name)} // ddddonguk
    bar()
    setTimeout(function(){console.log(this.name)}) // ddddonguk
  }
}
```

따라서 **내부함수는 일반함수, 메소드, 콜백함수 어디에서 선언되었든 관계없이 this가 전역객체를 바인딩한다.**

`this`가 전역객체를 참조하는 것을 회피하는 방법은?

```javascript
var name = 'ddddonguk'
var obj = {
  name: 'donguk',
  foo: function(){
    var that = this; // obj
    console.log(this.name) // donguk
    function bar(){console.log(that.name)} // donguk
    bar()
    setTimeout(function(){console.log(that.name)}) // donguk
  }
}
```

위와같이 `클로저`를 이용한 방법이 있고, 

> 클로저 복습!
>
> 외부함수의 실행컨텍스트가 스택에서 빠졌지만, 내부함수의 SC (스코프 체인)에서 외부함수의 AO (활성객체)를 참조하고 있어서 외부함수의 변수에 접근할 수 있다. 이를 클로저라고 한다!

```javascript
var name = 'ddddonguk'
var obj = {
  name: 'donguk',
  foo: function(){
    console.log(this.name) // donguk
    function bar(){console.log(this.name)} // ddddonguk
    bar.apply(obj)
    bar.call(obj)
    bar.bind(obj)()
  }
}
```

`apply`, `call`, `bind`를 이용해 bar 함수가 전달받은 `this`를 `obj`의 `this`에 바인딩하는 방법이 있다. 

<br/>

### 2. 메소드 호출

함수가 객체의 Property 값이면 메소드로서 호출된다. 이때 메소드 내부의 `this`는 **자신을 호출한 객체에 바인딩**된다.

```javascript
var obj = {
  this.name = 'donguk'
  foo: function() { console.log(this.name)} // donguk
}
obj.foo() // donguk
```

프로토타입 객체도 메소드를 가질 수 있다. **프로토타입 객체 메소드 내부에서 사용된 this도 일반 메소드 방식과 마찬가지로 해당 메소드를 호출한 객체에 바인딩된다.**

```javascript
function Person(name){ this.name = name }
Person.prototype.getName = function(){ return this.name }
var p = new Person('donguk')
p.getname() // donguk
```

<br/>

### 3. 생성자 함수 호출

JS에서는 `new` 연산자를 붙여서 함수를 실행하면 그 함수는 **생성자 함수로 실행된다.**

`new` 연산자로 실행시킨 생성자 함수는 다음과 같은 수순으로 객체를 생성해서 리턴한다.

1. 빈 객체를 생성, 그리고 this 바인딩
2. this가 가리키는 객체에 Property 추가
3. 객체 리턴

```javascript
function Person(name){
  // 이 구간에서 빈 객체를 생성, this는 이 객체를 가리킴
  this.name = name // this가 가리키는 객체에 Property 추가
  // return이 없어도 객체를 리턴
}
var p = new Person('donguk')
console.log(p.name) // donguk
```

일반적으로 3. 구간에서 반환문을 사용하지 않는다. 만약 반환문에서 명시적으로 `this`를 반환하면 해당 함수는 생성자함수의 역할을 할 수 있지만, 다른 객체를 반환하면 생성자 함수로서 역할을 하지 못한다.

```javascript
var obj = {
  name: 'dongdong'
}

function Person(name){ this.name = name }
const p = new Person('donguk')
```

객체 리터럴 방식와 생성자 함수 방식의 차이는 **프로토타입 객체[[Prototype]]**에 있다.

- 객체 리터럴 방식의 경우, 생성된 객체의 프로토타입 객체는 **Object.prototype**이다.

- 생성자 함수 방식의 경우, 생성된 객쳉의 프로토타입 객체는 **Person.prototype**이다.

만약 `new` 키워드를 붙이지 않고 함수를 실행하면 일반 함수의 실행과 같다. 따라서 함수내 `this`는 전역객체 `window`에 바인딩 된다. 이런 경우 무조건 함수를 생성자 함수로 실행하게 하는 방법이 있다..

```javascript
function Person(name){
  this.name = name
  if (!(this instanceof arguments.callee)) return new arguemnt.callee(name)
}
var p = Person('donguk')
p.name // donguk
```

`callee`는 `arguments` 객체의 Property로서 함수 내에서 실행 중인 함수를 참조할 때 사용한다. (현재 실행 중인 함수의 이름을 리턴한다.)

<br/>

### 4. apply / call / bind 호출

JS엔진이 함수 호출 패턴에 의해 `this`를 바인딩 하지만, 개발자가 명시적으로 바인딩 해주는 방법이다.

이 세가지 메소드는 모두 `Function.prototype` 객체의 메소드이다.

```javascript
func.apply(obj, [argsArray])
```

`obj`은 `func` 함수의 `this`를 바인딩할 객체이다. 

`argsArray`는 함수에 전달할 argument 배열이다.

기억해야 할 점은 `apply()`메소드를 **호출하는 주체는 함수**이며 `apply()` 메소드는 `this`를 특정 객체에 바인딩할 뿐 본질적인 기능은 **함수 호출**이다.

```javascript
function Test(){
  this.a = arguments[0]
  this.b = arguments[1]
}
var obj = {}
Test.apply(obj, [1,2])
obj // {a: 1, b: 2}
```

`apply` 메소드의 대표적인 용도는 `arguments`객체와 같은 유사 배열 객체에 메소드를 사용하는 경우이다. `arguments` 객체는 배열이 아니기 때문에 `slice` 메소드를 사용할 수 없다. 하지만 `apply` 메소드를 이용하면 가능

```javascript
function coervrtArgsToArray(){
  var arr = Array.prototype.slice.apply(arguments)
  return arr
}
converArgsToArray(1, 2, 3)
```

위 코드는 다음과 같이 해석할 수 있다. `Array.prototyep.slice`를 실행해라, 단 `this`는 `arguments`에 바인딩한다.

`call()`메소드는 기능은 `apply()` 함수와 같다. 단 **배열 형태로 넘긴 인자를 각각 하나의 인자로 보낸다.**

#### 콜백함수에 사용 예시

```javascript
function Person(name)
this.name = name
Person.prototype.doSomething = function(callback){
  //1
  if (typeof callBack === 'function'){
    callback()
  }
}
function foo() {console.log(this.name)} // this -> window / undefined
const p = new Person('donguk')
p.doSomething(foo)// undefined
```

콜백함수에서 `this`는 함수 호출 방식과 관계없이 전역객체를 가리킨다. 따라서 위와같은 상황에서 문제가 생긴다.

```javascript
function Person(name)
this.name = name
Person.prototype.doSomething = function(callback){
  //1
  if (typeof callBack === 'function'){
    callback.call(this) // callback 함수의 this를 Person 객체로 바인딩
  }
}
function foo() {console.log(this.name)} // this -> Person
const p = new Person('donguk')
p.doSomething(foo)//  donguk
```

인자로 받은 함수를 `call` 메소드를 이용해서 자신을 호출하는 객체로 `this` 를 바인딩 했다.

`bind` 메소드를 사용하면 `this`를 바인딩 해주는 점에서 같지만 `bind` 메소드는 바인딩된 함수를 리턴하기 때문에 **명시적으로 함수를 호출해야 한다.**

```javascript
function Person(name)
this.name = name
Person.prototype.doSomething = function(callback){
  //1
  if (typeof callBack === 'function'){
		callback.bind(this)() // 바인딩된 함수를 실행
  }
}
function foo() {console.log(this.name)} // this -> Person
const p = new Person('donguk')
p.doSomething(foo)//  donguk
```

