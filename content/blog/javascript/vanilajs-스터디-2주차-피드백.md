---
title: VanilaJS 스터디 2주차 피드백
date: 2020-05-18 13:05:65
category: javascript
---

###함수가 생성자 함수로 사용된 건지 엄밀히 체크하자.

```javascript
function TodoList(){
  console.log(this)
}
const t1 = new TodoList()
const t2 = TodoList()
```

1. `window` 로 체크

생성자 함수로 호출되지 않은 함수안에서 `this` 는 전역 객체를 가리킨다.  하지만 좀 더 엄밀히 체크할 필요가 있는 것 같다.

```javascript
function TodoList() {
  if (this === window) throw new Error('new 연산자를 사용해주세요.')
}
```

2. `new.target` 로 체크 

함수가 생성자 함수로 호출되면, `new.target` 은 함수 자체를 가리킨다.

```javascript
function TodoList() {
  if (new.target !== TodoList) throw new Error('new 연산자를 사용해주세요.')
}
```

3. `instanceof` 로 체크

함수가 생성자 함수로 호출되면, `this` 는 생성자 함수의 `instance` 이다.

```javascript
function TodoList() {
  if (!(this instanceof TodoList)) throw new Error('new 연산자를 사용해주세요.')
}
```

같은 방법으로 조금 추상화 할 수 있다.

```javascript
function TodoList() {
  if (!(this instanceof arguments.callee)) throw new Error('new 연산자를 사용해주세요.')
}
```

`arguments` 는 함수가 호출될 때 함수가 `this` 와 함께 암묵적으로 전달받는 값 중 하나로 `arguments.callee` 는 함수 자체를 말한다. (`TodoList`)

### 분리 가능한 파일은 따로 관리하자.

예를들어 `data` 의 유효성을 검사하는 함수, `debounce` , `throttle` 등 재사용이 가능하거나, 기능성 함수들은 `utils.js` 파일로 따로 분리하는게 좋다.  

그리고 상수도 따로 관리하면 좋다고 하셨다. 

```javascript
this.$input.addEventListener('keypress', (e) => {
  if (e.keyCode === 13) {
    ...
  }
})
```

위와 같은 상황에서 keyCode가 13이면 `Enter` 를 의미하는건 대부분이 알고있는 사실이지만, 가독성이 떨어지기 때문에

```javascript
...
const ENTER_KEY_CODE = 13
if (e.keyCode === ENTER_KEY_CODE) { ... }
```

위와 같이 명시적으로 13이 Enter라는 것을 말해주고, 이 변수 자체도 따로 파일을 만들어 관리하면 좋다고 하셨다.

### 화살표 함수에서 `this` 는 상위 컨텍스트를 가리킨다.

`DOM Element` 에 이벤트를 바인드 할 때 콜백 함수로 전달되는 함수에서 `this` 는 바인딩되는 객체를 가리킨다고 알고 있었다. 근데 컴포넌트 내부에서 `window`에 화살표 함수를 이용하여 바인딩을 하면 `window` 가 아닌 해당 컴포넌트를 가리키는 이유가 궁금하였다.

공부를 해보니 화살표 함수가 아닌 일반 함수로 이벤트 핸들러를 선언하면 `this` 가 가리키는 값은 해당 이벤트 핸들러가 바인딩된 `target` 이 맞지만, 화살표 함수를 사용하여 선언하면 현재 스코프의 **상위 컨텍스트** 를 가리키기 때문이다. ( 이를 **Lexical this** 라고 한다. )

```javascript
function TodoList() {
  ...
  window.addEventListener('scroll', function(e){
  	console.log(this) // window
	})
}
```

위와 같이 일반 함수는 자신이 바인딩된 객체를 가리킨다. 즉 `window` 하지만 화살표 함수를 사용하면

```javascript
function TodoList() {
  ...
  window.addEventListener('scroll', (e) => {
    console.log(this) // TodoList
  })
}
```

현재 스코프의 상위 컨텍스트를 가리킨다, 즉 `TodoList` 

생각난 김에 `this` 다시 복습헤야겠다. [링크](https://doonguk.github.io/javascript/javascript-this/)
