---
title: 함수형 프로그래밍(range, L.range)
date: 2019-12-05 00:12:47
category: javascript
---

### 인자로 받은 값 만큼 길이를 가지는 range 함수와 L.range 함수(느긋한 range) 구현

### 1. range

```javascript
const range = l => {
  let i = -1
  let res = []
  while(++i < l){
    res.push(i)
  }
  return res
}

log(range(5)) // [0,1,2,3,4]
```

뒤에 구현할 L.range 함수와 차이점을 알아보기 위해 배열 원소들의 총합을 구해보자

```javascript
const add = (a,b) => a+b
const list = range(4)
log(reduce(add, list)) // 6
```



### 2. L.range

```javascript
const L = {}
L.range = fuction *(l){
  let i = -1
  while(++i < l){
    yield i
  }
}
```

제너레이터 함수를 이용해서 i가 증가하는대로 yield를 하게 했다.

```javascript
const list2 = L.range(4)
log(reduce(add, list2)) // 6
```

원소의 총합을 구해보면 결과는 range 함수와 같다.



# 3. 차이점 ( 중요 )

```javascript
const list = range(4)
log(list) // [0, 1, 2, 3]
```

range함수의 결과 값을 확인 해보면 배열이다. 하지만 제너레이터가 생성한 값을 찍어보면

```javascript
const list2 = L.range(4)
log(list2) // L.range <suspended> 이것은 이터레이터
```

배열이 아니고 이터레이터 이다. 하지만 reduce 함수의 결과가 같은 이유는 reduce 함수가 이터러블을 받기 때문이다. ( reduce함수 내부에서 이터러블이 이터레이터를 생성하고 반복문을 순회한다. )

좀 더 자세히 차이점을 살펴보면

```javascript
const range = l => {
  let i = -1
  let res = []
  while(++i < l){
    log('range hi~~~~~')
    res.push(i)
  }
  return res
}

const list = range(4)
log(list)
```

range 함수의 내부에 로그를 찍어서 확인했을 때

![image](https://user-images.githubusercontent.com/39187116/70153638-a1c28d00-16f2-11ea-9815-93c7f4126243.png)

다음과 같이 함수를 실행시킨 순간 내부적인 코드가 동작 하면서 로그가 받은 인자만큼 찍힌다. 

```javascript
const range = l => {
    let i = -1
    let res = []
    while(++i < l){
      log('range hi~~~~~')
      res.push(i)
    }
    return res
  }
  const list = range(4)
  log(list)

  const L = {}
  L.range = function *(l) {
    let i = -1
    log('pass here?')
    while(++i < l){
      log('L.range hi~~~~~')
      yield i
    }
  }
  const list2 = L.range(4)
  log(list2)
```

하지만 L.range의 경우는 다르다.

![image](https://user-images.githubusercontent.com/39187116/70153876-f82fcb80-16f2-11ea-9189-651505d47ef3.png)

제너레이터 함수 내부에 정의한 어떠한 로그도 찍히지 않았다. 그 이유는 **제너레이터 함수의 내부 코드는 이터러블이 생성한 이터레이터가 next() 함수를 호출 할 때 동작하기 때문이다.(즉, 맨위에 예제 코드에 있는 reduce함수를 순회 할 때 제너레이터 내부의 코드가 동작하면 이터레이터.next().value 값이 yield되는 것이다.)** 따라서 다음과 같이 제너레이터 함수에 의해 생선된 이터레이터에 next() 함수를 찍어보면

```javascript
...코드 생략
 const L = {}
  L.range = function *(l) {
    let i = -1
    log('pass here?')
    while(++i < l){
      log('L.range hi~~~~~')
      yield i
    }
  }
  const list2 = L.range(4)
  list2.next()
  log(list2)
```



![image](https://user-images.githubusercontent.com/39187116/70154100-4a70ec80-16f3-11ea-9ebf-e1e7391598d5.png)

위와 같이 제너레이터 함수 내부를 순회한 것 을 확인할 수 있다. 여기서는 next() 함수를 한번만 호출 하였기 때문에 로그가 한개씩만 찍혔다. 결론은 **range 함수는 평가되는 순간 배열을 생성 하지만, L.range 함수는 배열을 생성하지 않고 이터레이터가 순회 될 때 필요한 값을 yield 하는 개념이다.**

추가적으로 다른점이 있다면 range 함수에 생성된 배열은 reduce 함수를 순회 할 때 함수 내부에서 이터레이터를 생성한다.

```javascript
const reduce = (f, acc, iter){
  if(!iter){
    iter = iter[Symbol.iterator]()
    log(iter) // Array iterator {}
    acc = iter.next().value
  }
  for(const a of iter){
    acc = f(acc, a)
  }
  return acc
}

const list = range(4)
log(reduce(add, list))
```

예전 포스트에서 for of 문을 순회 할 때 다음과 같이

```javascript
for(const a of iter[Symbol.iterator]()){
  ...
}
```

이터러블을 이터레이터로 변환 한다고 배웠다. 따라서 range() 함수가 평가된 값은 아래 순서를 거친다.

- Array ( 이터러블 ) => 이터레이터로 변환 => next() 함수를 호출하며 for of 순회 

하지만 L.range() 함수에 의해 평가된 값은 이터레이터 이기 때문에( 제너레이터가 평가된 값은 이터레이터 ) 이터러블이 이터레이터로 변환 되는 작업을 거치지 않는다.



### 4. 성능

```javascript
const test = (time , name, f) => {
  console.time(name)
  	while(time--) f()
  console.timeEnd(name)
}
test(10, 'range', () => reduce(add, range(100000)))
test(10, 'L.range', () => reduce(add, L.range(100000)))
```

![image](https://user-images.githubusercontent.com/39187116/70156720-b9e8db00-16f7-11ea-9b10-0ab3dfc9b73e.png)

