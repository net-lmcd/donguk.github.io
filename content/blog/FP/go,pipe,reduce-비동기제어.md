---
title: go,pipe,reduce 비동기제어
date: 2019-12-24 16:12:67
category: FP
---

> # go, pipe, reduce 에서 비동기 제어



## 1. go

```javascript
go(1,
() => a+10,
() => Promise.resolve(a+100),
() => a+1000,
log) // [Object Promise]1000
```

만약 중간에 비동기 상황이 일어난 함수가 실행 된다면 기존에 만들었던 go 함수는 정상적으로 동작하지 않는다.  하지만!

[이전 포스트]([https://doonguk.github.io/javascript/%EA%B0%92%EC%9C%BC%EB%A1%9C%EC%84%9C%EC%9D%98-promise/](https://doonguk.github.io/javascript/값으로서의-promise/)) 에서 만들었던 go1 함수처럼 비슷하게 구현이 가능하다.

```javascript
//go 함수
const go = (...args) => reduce( (a,f) => f(a), args)
```

우선 go함수를 보면 사용된 함수는 reduce 함수 이므로 이를 수정 해 줘야한다. 

```javascript
//reduce
const reduce = curry((f, acc, iter) => {
  if(!iter){
    iter = acc[Symbol.iterator]()
    acc = iter.next().value
  }
  let cur
  while(!(cur = iter.next()).done){
    const a = cur.value
    acc = f(acc, a)
  }
  return acc
})
```

오랜만에 reduce 함수를 보면, 함수와 이터러블을 받고 만약 iter가 없다면( acc가 없다면 ) 이터레이터를 생성하여 원소를 하나 꺼내서 acc의 초기 값으로 설정하고 남은 원소를 순회 하면서 acc값에 인자로 받은 f를 실행한 값을 저장하는 함수 이다. 그렇다면 맨 위의 예제에서 <code>() => Promise.resolve(a+100)</code>이라는 값은 언젠가 acc에 저장될 것 이다. ( 즉, acc가 언젠가는 Promise가 될 것이다. 라는 말이다.)

먼저 간단한 프로미스를 처리하는 방법은

```javascript
... //reduce 함수
  while(!(cur = iter.next()).done){
    const a = cur.value
    acc = acc instanceof Promise ? acc.then( acc => f(acc, a)) : f(acc, a)
  }
```

하지만 이렇게 구현한다면, **중간에 Promise를 만났을 때 그 이후의 함수들은 Promise 체인에 의해 프로미스에 나머지 함수들을 합성하기 때문에 연속적으로 비동기가 일어나게 된다.** 그럼 당연히 성능도 떨어지게 된다.  따라서 중간에 Promise를 만나도, Promise가 아닐때는 동기적으로 while문을 통해 나아갈 수 있도록 해줘야 한다.

이럴땐 **재귀**를 이용하면 좋다. 

- 구현!

```javascript
const reduce = curry((f, acc, iter) => {
  if(!iter){
    iter = acc[Symbol.iterator]()
    acc = iter.next().value
  }
  return function recur(){
  }()
})
```

위와같이 유명함수를 선언하고 즉시 실행을 한다. 유명함수를 선언한 이유는 언제든 재귀를 하기 위해서 이다.

**( 참고 : 함수를 값으로 다루면서 이름을 짓는 기법을 유명함수라고 한다. )**

함수를 즉시 실행할 때 acc를 유명함수에 전달 해 주었다.

```javascript
...여기서 받은acc를
return function recur(acc){
  
}(acc)
```

위 코드처럼 즉시실행 할 때 파라미터로 값을 넣어주면, 즉시실행 되는 함수의 인자로 전달 된다.

```javascript
...생략
return function recur(acc){
  let cur
  while(!(cur = iter.next()).done){
    acc = f(acc, a)
  	if(acc instanceof Promise) return acc.then(recur)
  }
  return acc
}(acc)
```

일단 f(acc, a)로 합쳐보고 acc값이 프로미스면 프로미스가 처리된 값으로 재귀함수를 호출한다. ( 만약 프로미스가 아니면 while문을 다시 순회한다. 어차피 while문은 이터레이터의 done 값을 기준, 즉 next()함수를 몇번 호출 했는가 를 기준으로 순회하기 때문에 재귀함수로 while문을 다시 시작해도 전체 순회 횟수는 같다. )

하지만 위의 코드에도 커버하지 못하는 이슈가 있다. 만약에 acc의 **첫번째 값으로 Promise가 들어오는 case** 이다. 이 같은 경우는 [이전 포스트]([https://doonguk.github.io/javascript/%EA%B0%92%EC%9C%BC%EB%A1%9C%EC%84%9C%EC%9D%98-promise/](https://doonguk.github.io/javascript/값으로서의-promise/)) 에서 만들었던 go1 함수를 한번더 이용하면 된다.

```javascript
const go1 = (a, f) => a instanceof Promise ? a.then(f) : f(a)
```

```javascript
...생략
return go1(acc, function recur(acc){ 
  let cur
  while(!(cur = iter.next()).done){
    const a = cur.value
    acc = f(acc, a)
    if(acc instanceof Promise) return acc.then(recur)
  }
  return acc
}) // 애초에 처음 들어오는 acc가 Promise인 case
```

위와같이 구현하면 된다.

- Reject

```javascript
go(Promise.resolve(1,
	() => Promise.reject('나가자'),
	() => Promise.resolve(a+100),
	() => a+1000,
log).catch( e => console.log(e))
```

reject된 경우에는 아래 함수들이 실행되지 않고 바로 reject된다. reject된 값은 catch문으로 잡을 수 있다.

- 결론

프로미스를 값으로 다루면서 프로미스를 단순히 then~then 으로 콜백 지옥 해결 용도로만 사용하는게 아니라 프로미스라는 값을 가지고 내가 원하는 용도로 사용 하거나, 원하는 시점에 적절한 함수를 실행하는 용도로 사용할 수 있다.

