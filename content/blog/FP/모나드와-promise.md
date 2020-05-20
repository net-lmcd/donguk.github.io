---
title: 모나드와 Promise
date: 2019-12-21 16:12:91
category: FP
---



# 1. 모나드 ( 개념 이해 )

함수를 안전하게 합성하기 위한 도구. 자바스크립트 에서는 모나드를 자세하게 설명하지는 않지만, Array나 Promise 같은거로 모나드가 무엇인지는 설명이 가능하다. 

모나드는 어떤 박스( Container ) 라고 생각하면 된다. 이 박스에 어떤 값이 들어있다. 이 박스를 이용해서 함수 합성을 안전하게 하는 것이라고 볼 수 있다.

```javascript
[1]
```

예시 설명을 하자면

```javascript
const g = a => a+1
const f = a => a*a
log(f(g(1))) // 4
```

함수 g와 f를 합성하여 원하는 값( **유의미한 값** )을 잘 얻었다. 그런데 만약 g함수의 인자로 아무것도 전달하지 않는다면

```javascript
log(f(g())) // NaN
```

무의한 값 이지만 로그가 찍혔다.( 외부 세계에 영향을 끼침 ) 이렇게 g 함수의 인자로 어떤 값이 들어올 수 있는지 알 수 없는 현실프로그래밍 에서 보면 위 함수의 합성은 안전한 함수의 합성이라고 볼 수 없다. 이를 해결하기 위한 아이디어가 모나드라고 볼 수 있다.

```javascsript
[1]
```

모나드는 박스가 갖고 있는 함수들을 이용해서 합성을 한다.

```javascript
[1].map(g).map(f)
```

여기까지가 함수를 합성한 결과이다. 위 값을 로깅 하려면 log 함수의 인자로 전달 할 수 있지만 그렇게 되면 [4] 가 리턴된다. 여기서 Array는 실제로 필요한 값이 아니다. Array는 개발자들이 데이터를 조작하기 위한 자료구조일 뿐이다. 실제로 필요한 값은 Array안에 있는 4 라는 값이다. 따라서

```javascript
[1].map(g).map(f).forEach(r=>log(r))
```

위 처럼 함수합성의 결과로 값이 있다면 외부세계에 영향을 줄 수 있게 forEach를 하였다. 그럼 이렇게 함수 합성을 했을 때 이점이 무엇이냐면

```javascript
[].map(g).map(f).forEach(r=>log(r)) 
```

**Array 안에 아무것도 전달하지 않는다면 forEach 구문안에서 r => log(r) 자체를 실행시키지 않기 때문에 외부세계에 영향을 끼치지 않는다.( 안전하다 )**

참고 )

```javascript
[1].map(g).map(f).forEach(r=>log(r))
Array.of(1).map(g).map(f).forEach(r=>log(r))
```

위 두개의 코드는 같은 코드이다. [1]을 함수로 만든 것 뿐.

# 2. Promise

- ###모나드 관점에서 Promise

Array는  .map을 이용해서 함수를 합성하지만 Promise는 then을 이용해서 함수 합성을 한다.

```javascript
Promise.resolve(2).then(g).then(g).then(r => log(r)) // 9
```

따라서 **Promise는 비동기 적인 상황에서 함수를 안전하게 합성하기 위한 도구** 이다.

```javascript
Promise.resolve().then(g).then(g).then(r => log(r)) // NaN
```

Array와 같은 관점에서 해석하려고 하면 안된다. Array처럼 값이 있거나 없거나 관점에서 해석 하는게 아니라 **비동기 상황(얼마 만큼의 딜레이가 필요한 상황), 즉 대기가 일어난 상황에서 함수를 안전하게 합성하기 위한 도구 이다.**

```javascript
new Promise( resolve => setTimeout(()=>resolve(2),100)).then(g).then(g).then(r => log(r))
```



**결론적으로 어떤 특정한 상황을 안전하게 합성하기 위한 도구를 모나드 라고 부른다. ( 비동기 상황은 Promise~)**



- ###Kleisli Composition 관점에서 Promise

오류가 있을 수 있는 상황에서 합수 합성을 안전하게 하기위한 규칙. 예를들어 함수의 인자로 들어오는 값이 오류가 있는 값이라 던지, 정확한 인자가 들어왔더라도, 어떤 함수가 의존하고 있는 외부의 상태에 의해서 결과를 정혹히 전달할 수 없는 상황일 때 해결하기 위한 함수합성의 규칙.

```javascript
f(g(x)) === f(g(x))
```

일반적인 수학적 프로그래밍에서는 함수g로 들어온 인자가 같으면 두 함수는 항상 동일한 결과값을 갖는다. 하지만 실무에서는 우변의 코드를 평가할 때 좌변 g함수를 실행할 때와 달리 외부 상태에 의해 변화가 생길 수 있다. 따라서 순수한 함수형 프로그래밍을 할 수 없다.

```javascript
f(g(x)) === g(x)
```

클레이슬리 합성 관점에서는 만약에 g함수에 x를 전달 했을 때 g(x)에서 오류가 발생한다면, f(g(x)) 결과와 g(x)의 결과가 같을 수 있다.

다시 말하면 **g에서 오류가 발생 한다면 g(x)의 결과나 f(g(x)) 의 결과가 같에 함수를 합성하는 것을 클레이슬리 컴포지션 이라고 한다.**

1. 예시

```javascript
users = [
  {id : 1, name : 'aa'},
  {id : 2, name : 'bb'}
  {id : 3, name : 'cc'}
]

const getUserById = id => find(u => u.id === id, users)
const f = ({name}) => name // 객체에서 name 추출
const g = getUserById

const fg = f(g(id))
```

```javascript
log(f(g(2)) === f(g(2))) // 'bb'
```

users를 상태라고 생각한다면, 실세계의 프로그래밍 에서는 users의 상태가 변하기도 한다.

```javascript
const r = f(g(2)) // bb
users.pop()
users.pop()
const r2 = f(g(2)) // Type Error
```

중간에 유저의 상태가 변해서 같은 함수의 합성을 하더라도 에러가 생긴 상황이다. 에러가 생긴 이유는 g라는 함수는 users에 결과가 있다고 가정 했을 때 정상적으로 동작하고, f 라는 함수는 인자로 name이 잘 전달 됐을 때 동작하기 때문이다.

이를 해결하기 위해서는

```javascript
const fg = id => Promise.resolve(id).then(g).then(f)
fg(2).then(log)

users.pop()
users.pop()

fg(2).then(log) // Promise Type Error
```

이러한 상황에서 Promise는 함수의 합성을 안전하게 만들 수 있다. g라는 함수를 만들때  다음과 같이

```javascript
const getUserByid = id =>
	find(u.id === id, users) || Promise.reject('없어요!')
```

결과가 없다면 Promise.reject이라는 결과값을 나타나게 하면 된다. 다시 실행을 해보면

```javascript
fg(2).then(log) // bb
users.pop()
users.pop()

fg(2).then(log) // Promise {<reject> : '없어요!'} Uncaught in Promise
```

Promsie가 reject된 값을 리턴하게 된다. 이것을 좀 더 자세히 살펴보자.

## ( 중요 )

```javascript
g(2) // {id : 2, name : 'bb'}
users.pop()
users.pop()
g(2) // Promise {<reject> : '없어요!'} Uncaught in Promise
```

외부상태가 변경되어도 함수 fg(2).then(log)와 같은 결과가 나왔다. 여기서 알 수 있는 점은 우리가 함수의 합성을 실행한 결과가 g함수만 실행한 결과와 같다는 것이다. g 함수에서 reject된 프로미스를 f함수의 인자로 전달 해 보면

```javascript
// 외부상태 변경 된 후
f(g(2)) // undefined
```

reject된 Promise에서 name을 찾으려고 했기 때문에 undefined 값이 나오게 된다. 이 말은 함수의 합성으로 실행 했을 때

g함수에 에러가 있다면 f 함수가 실행 돼서 log를 찍는게 아니다는 말이다.  ( **애초에 f함수 then함수안에 있는 log 모두 실행되지 않는다.**)

reject된 값을 로깅하기 위해서는

```javascript
const fg = id => Promise.resolve(id).then(g).then(f).catch( a => a )
```

catch 함수를 넣어서 fg함수를 수정한 뒤 로깅을 해보면 된다.

```javascript
// 외부상태 변경 된 후
fg(2).then(log) // '없어요!'
```

