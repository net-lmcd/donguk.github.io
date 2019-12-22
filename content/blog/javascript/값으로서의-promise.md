---
title: 값으로서의 Promise
date: 2019-12-21 15:12:43
category: javascript
---

># 값으로서의 Promise





```javascript
const go1 = (a, f) => f(a)
const add5 = a => a+5
log(go1(10, add5)) // 15
```

위 예제에서 go1이 제대로 동작 하려면 **f 라는 함수는 동기적으로 동작해야 하는 함수이고, a라는 값도 동기적으로 값을 알 수 있어야 한다. 다른말로는 비동기 상황이 일어난 일급값이 아닌 일반값 즉 프로미스가 아닌 값이어야 한다.**

만약 a라는 값이 어떤 상황 이후에 알 수 있는 값이라면?

```javascript
log(go1(Promise.resolve(10), add5)) // [Object Promise]5
```

정상적으로 동작하지 않는다. 여기서 10이라는 값은 이렇게 나타낼 수 있다. ( 예시로 )

```javascript
const delay100 = a => new Promise( resolve => setTimeout(()=>resolve(a),100))
```

100ms 이후에 받아뒀던 값을 리턴하는 ( 어떤 상황 후 값을 알 수 있다는 말과 같음 ) 함수로 나타낼 수 있다.

```javascript
log(go1(10, add5)) //15
log(go1(delay100(10), add5))
```

그럼 위 두 함수가 동일한 값을 리턴하려면 어떻게 go1 함수를 바꿔야 할까?

go1함수로 받는 인자 a가 Promise인지 체크 하는 방법으로 해결할 수 있다.

```javascript
const go1 = (a,f) => a instanceof Promise ? a.then(f) : f(a)
```

이렇게 작성을 한다면 <code>log(go1(delay100(10), add5))</code> 결과 값이 Promise가 된다.

![image](https://user-images.githubusercontent.com/39187116/71303709-d5462c80-23ff-11ea-8141-a71e38b898dc.png)

따라서 아래와 같이 수정하면 결과 값을 같게 만들 수 있다.

```javascript
const r1 = go1(10, add5)
log(r1) //15
const r2 = go1(delay100(10), add5) // Promise {<pending>}
r2.then(log) // 15
```

또다른 표현도 가능하다.

```javascript
var n1 = 10
go1(go1(n1, add5), log) // 15
var n2 = delay100(10)
go1(go1(n2, add5), log) // 15 (100ms 이후)
```

마지막으로..

```javascript
var n1 = 10
log(go1(go1(n1, add5), log)) // 15 undefined
var n2 = delay100(10)
log(go1(go1(n2, add5), log)) // Promise {<pending>} 15 (100ms 이후)
```

첫번째 함수의 조합은 즉시 평가되어 마지막에 log까지 실행된 값을 나타내 주지만, 두번째 함수의 조합(Promise)은 비동기를 값으로 평가하여 값을 이어준다. 이렇게 **어떤 일을 한 결과의 상황을 일급 값으로 평가하여 지속적으로 어떤 일들을 연결해 나갈수 있게 하는것이 Promise의 가장 중요한 특징이다.**

