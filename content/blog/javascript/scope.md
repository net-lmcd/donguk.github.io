---
title: Scope
date: 2020-06-07 18:06:45
category: javascript
---

## 전역변수 & 지역변수

```javascript
var x = 'global'
function ex(){
  var x = 'local'
  x = 'change'
}
ex();
console.log(x) // global
```

ex 함수 바깥의 x는 **전역변수** , ex 함수의 안의 x는 **지역변수**. 위의 상황에서 지역변수는 아무리 해도 전역변수에 영향을 끼칠 수 없다. 바로 **스코프** 때문이다. 

## 스코프 (Scope)

```javascript
var x = 'global'
function ex(){
  x = 'change'
}
ex()
console.log(x) // change
```

자바스크립트는 변수의 **범위**를 호출한 함수의 활성객체 부터 전역객체까지 점차 넓혀가며 찾는다. 함수 ex의 범위 안에 x가 없기 때문에 현재 실행컨텍스트의 스코프체인을 통해서 상위 컨텍스트( 여기서는 GEC )를 찾게된다.  

## 렉시컬 스코핑( Lexical Scoping )

1. 변수의 범위를 호출한 함수의 지역 스코프부터 전역 변수들이 있는 전역 스코프까지 점차 넓혀가며 찾는다. ( 스코프 체인 )
2. **스코프는 함수를 호출할 때가 아닌 선언할 때 생긴다(Lexical Scoping)** 따라서 정적 스코프라고도 불린다.
3. 현재 실행컨텍스트의 스코프 체인이 초기화될 때 스코프 체인의 선두에는 현재 실행컨텍스트가 참조하는 활성객체를 넣는건 모든 컨텐스트가 같지만, 그 뒤에 들어오는 활성 객체들 ( 현재 실행컨텍스트가 참조 가능한 객체들 )은 **함수가 선언된 시점에 의해서 결정된다.** (Lexical Scoping)

```javascript
var gVar = 'donguk'
function log(){
  gVar = 'dongdong'
}
function wrapper(){
  var gVar = 'ukuk'
  log()
}
wrapper()
```

JS에서는 함수를 실행할 때가 아닌 선언할 때 스코프가 생기기 때문에 위 `log` 함수 내부의 `gVar` 변수는 글로벌 객체의 `gVar`를 참조한다. ( log 함수의 실행 컨텍스트의 스코프체인에 1번째 인덱스에 `wrapper`함수가 아닌 `global execution context`가 있기 때문. )

## 스코프에 따른 문제 발생 > 전역변수 오염

무분별한 전역변수 사용은 여러 자바스크립트 파일이 참조하면서 오염될 가능성이 높다.

이를 해결하기 위한 방법은? **네임스페이스**를 만든다.

```javascript
var obj = {
  x: 'local'
}
```

위와 같이 객체로 오염될 가능성이 있는 변수를 종속시킨다. 하지만 위 방법도 `obj.x = 'hi'` 와 같은 코드에 무너질 가능성이 높다.

```javascript
var another = function() {
  var x = 'local'
  function y(){
    console.log(x)
  }
  return { y }
}
var newScope = another()
```

위와같은 방법으로 문제를 해결할 수 있다. `another` 함수를 실행시키면 `newScope`라는 **네임스페이스**를 통해 y를 참조할 수 있다. 하지만 x는 접근할 수 없다. ( x는 **비공개 변수** , y는 **공개 변수** )

### IIFE ( 즉시 호출 함수 표현식 )

위의 코드를 더 간단하게 할 수 있다. ( 함수 표현식만 가능하다.)

```javascript
var newScope = (function(){
  var x = 'local'
  return {
    y: function(){
      console.log(x)
    }
  }
})()
```

위와 같은 패턴을 **즉시 호출 함수 표현식**이라고 하고, **모듈 패턴**이라고 한다.( 많은 라이브러리에서 사용하고 있다. ) 함수를 선언하자마자 바로 실행하는 개념이다. 즉시 호출 함수 표현식은 **비공개 변수를 만들어주는 기능을 한다.**

> 즉시 호출 함수 표현식을 사용하면 **네임스페이스**를 확보할 수 있고, 비공개 변수를 만들 수 있다
>
> 그 외에도, 변수에 별칭을 줘서 **전역 변수가 오염되는 것을 막을 수 있다.**
>
> ```javascript
> (function($){...})(jQuery)
> ```


## Reference

1. https://github.com/baeharam/Must-Know-About-Frontend/blob/master/Notes/javascript/scope.md
2. https://www.zerocho.com/category/JavaScript/post/5740531574288ebc5f2ba97e
