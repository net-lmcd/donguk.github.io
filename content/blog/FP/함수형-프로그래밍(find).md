---
title: 함수형 프로그래밍(find)
date: 2019-12-10 22:12:80
category: FP
---

> # find 함수


이전 포스트에서 queryStr 함수는 reduce를 통해 만들어진 것 처럼 함수들은 어떤 계보, 계열을 통해 만들어진다. 이번에 만들어볼 find 함수는 take 함수를 통해 만들어진다.

```javascript
const users = [
  {age : 30},
  {age : 31},
  {age : 32},
  {age : 33},
  {age : 34},
  {age : 35}
]

const find = (f, iter) => go(
	iter,
  filter(f)
)
log(find(a => a.age<30, users)) // 리스트
```

하지만 find 함수는 조건을 만족하는 원소 한개만 찾는 함수이다.

```javascript
const find = (f, iter) => go(
	iter,
  filter(f),
  take(1),
  ([v]) => v // take 함수는 리턴값이 리스트니 구조분해
)
```

따라서 위와 같이 구현할 수 있는데, 위 함수는 효율성에 문제가 있는 코드가 있다. 결국 find 함수는 조건을 만족하는 원소 1개만 찾으면 되는 원소인데 위와같이 구현하면 filter 함수에서 조건을 만족하는 원소들을 모두 찾는다.  따라서

```javascript
const find = (f, iter) => go(
	iter,
  L.filter(f),
  take(1),
  ([a]) => a
)
```

**filter 함수에 지연성을 줘서 값을 계산하는 take 함수를 만날때 까지 연산을 미루고 나중에 조건을 만족하는 원소 1개만 yield 하는 방법**이 좋다. 이렇게 구현한 코드에 curry 함수까지 사용 하면 완벽.

```javascript
const find = curry((f,iter) => go(
	iter,
  L.filter(f),
  take(1),
  ([a]) => a
))

go(
	users,
  map(p => p.age),
  find(a => a<30),
  log
)//31
```

