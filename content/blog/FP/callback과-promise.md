---
title: Callback과 Promise
date: 2019-12-19 02:12:23
category: FP
---

># Callback 과 Promise



자바스크립트에서 비동기 동시성 프로그래밍을 하는 방법으로는 크게 Callback과 Promise 가 있다.

## 1. Callback

- 선언부

```javascript
function add10(a, callback){
  setTimeout(()=>callback(a+10), 100)
}
```

100ms 이후에 받은 인자 a에 10을 더한 값을 콜백 함수에 전달 하였다.

- 실행부

```javascript
add10(10, res=>{
  log(res) // 100ms 이후 15 로깅
})
```



## 2. Promise

- 선언부

```javascript
function add20(a){
  return new Promise( resovle => setTimeout(()=>resolve(a+20), 100))
}
```

프로미스 에서는 함수에서 사용할 인자만 받고 콜백함수 같은건 받지 않는다.  여기서 중요한건 add20 함수에서는 **프로미스 객체를 만들어서 리턴 하고 있다는 것 이다.**

- 실행부

```javascript
add(5)
	.then(log) // 100ms 이후 25 로깅
```

여기서 잠깐..

```javascript
add(5)
 .then(result => log(result)) // 이거랑 then(log) 같구나
```



## 3. 연속적인 실행 관점에서 차이점

```javascript
add20(5)
 .then(add20)
 .then(add20)
 .then(add20)
 .then(log)
```

프로미스는 그냥 then에 추가해주면 된다. 하지만 콜백함수는..

```javascript
add10(10, res=>{
 	add10(10, res=>{
 		 	add10(10, res=>{
 				log(res)
		})
	})
})
```

인덴트 관리하기도 어렵고 보기 복잡한 부분이 있다.



## ( 중요 ) 4. 일급 관점에서 차이점

3번에서 이야기 했던 것 처럼 프로미스는 콜백 보다 연속적인 실행관점에서 좀더 편하게 then, then, then 을 통해 값을 얻을 수 있다는 차이점이 있지만 **정말 중요한 이 둘의 차이점은 프로미스는 비동기 상황을 일급으로 핸들링 한다는 것이다.**

```javascript
function add20(a){
  return new Promise( resovle => setTimeout(()=>resolve(a+20), 100))
}
```

- **프로미스는 Promise 라는 클래스에서 만들어진 인스턴스를 반환하는데 이 값은 대기와 성공과 실패를 다루는 일급 값으로 이루어져 있다.( 비동기 상황을 값으로 만들어서 return 하였다. )** 

```javascript
//비동기 상황을 코드로 해결
function add10(a, callback){
  setTimeout(()=>callback(a+10), 100)
}
add10(10, res=>{
  log(res)
})
```

- 위 콜백함수 코드 같이 대기와 어떤 끝내는 값을 코드나, 컨텍스트로만 다루는것이 아니라 **대기 되어지고 있다는 값을 만드는 점에서 콜백과 가장 큰 차이점을 갖고 있다.**

```javascript
function add10(a, callback){
  setTimeout(()=>callback(a+10), 100)
}
let a = add10(10, res=>{
  log(res)
})
log(a) // undefined
```

- 콜백에서는 값을 로깅해보면 아무것도 나타나지 않는다. 
- 콜백에서는 setTimeout이 일어난다는 코드적인 상황과 100ms 이후에 콜백함수가 실행된다는 컨텍스트만 남아있는 상태라고 볼 수 있다.
- 콜백에서는 add10을 실행하면 그 뒤에 뭔가를 할 수 없다. 컨텍스트에 실린 함수만으로 이어나가기만 가능하다.

```javascript
let b = add20(5).then(add20)
log(b) // Promise {<pending>}
...// 잠시 뒤
log(b) // Promise {<resolved> : 30}
```

- 하지만 프로미스에서는 add20(5) 를 평가 했을 때 즉시 값이 리턴된다. 따라서 프로미스는 리턴 이후에 내가 원하는 일을 할 수 있는 점에서 콜백과 다르다. 따라서 아래와 같은 코드가 가능하다.

```javascript
let a = add20(5)
log(a) // Promise {<pending>}
...
log(a) // Promise {<resolved> : 25}
let b = a.then(c => c -5) 
log(b) // Promise {<pending>}
...
log(b) // Promise {<resolved> : 20}
let d = b.then(log)
```

위와 같이 b 자리에 또 프로미스가 떨어지고 then을 작업을 이어갈 수 있다. 

결론지으면 **프로미스는 비동기 상황을 값으로 다룰 수 있고, 값으로 다룰 수 있다는 것( 코드로 다뤄지는게 아닌 )은 일급이라는 것이다.** 일급이라는 이야기는 어떤 변수에 전달될 수 있고, 함수에 인자로 전달될 수 있고 전달 될 수 있다는 것은 어떤 작업을 계속해서 이어나갈 수 있다는 말이다.

### (중요) Promise.then의 중요한 규칙



- then 메소드를 통해 꺼낸 값은 반드시 Promise가 아니다.

```javascript
Promise.resolve(Promise.rosolve(Promise.resolve(1))).then(log)
```

위와 같이 프로미스가 중첩되어 선언해 있다 하더라도 단 **한번의 then** 으로 안에 있는 결과를 꺼내서 볼 수 있다는 점이다.

```javascript
Promise.resolve(Promise.resolve(1)).then(function(a){
  log(a)
})
```

일반적으로 생각 해보면, then 함수 내부 function에는 <code>Promise.resolve(1)</code> 이라는 프로미스가 떨어질 것 같은데 그렇지 않고 프로미스가 아무리 깊게 있더라도 그 안쪽에 있는 **값**이 함수 내부에 떨어진다.

이말은 **프로미스 체인이 연속으로 걸려 있어도 내가 원하는 곳에서 한번의 then으로 결과를 꺼내 볼 수 있다는 것이다.**  마찬가지로

```javascript
new Promise(resolve => resolve(new Promise( resolve1 => resolve1(1)))).then(log)
```

연속적으로 resolve를 하더라도 해당하는 결과는 한번의 then으로 꺼내 볼 수 있다.
