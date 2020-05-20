---
title: 함수형 프로그래밍 (map)
date: 2019-11-26 02:11:97
category: FP
---
> ## Map 함수



### 1. 구현

```javascript
const products = [
  {name : '과자', prices : '1000'},
  {name : '사탕', prices: '500'},
  {name : '아이스크림', prices : '1200'}
]
```

products 라는 배열에서 이름 따로 가격 따로 수집 한다고 해보자.

```javascript
let names = []
for(const a of products){
  names.push(a.name)
}
log(names)

let prices = []
for(const a of products){
  names.push(a.prices)
}
log(prices)
```

이런 경우에 사용하는 함수가 map 함수이다.

```javascript
const map = () => {
  let names = []
	for(const a of products){
  	names.push(a.name)
	}
	log(names)
}
```

**여기서 log()는 함수 밖 외부에 직접적으로 변화를 일으킨다. 함수형 프로그래밍 에서는 인자와 리턴값으로 소통 하는것을 권장한다. 따라서 names를 변화를 일으키는 다른 함수나 메소드에 전달하는게 아니라 리턴하는 것을 권장한다. (리턴 후 개발자가 변화를 일으키는 것을 권장)** 

```javascript
const map = () => {
	let res = []
  for(const a of products){
    names.push(a.name)
  }
  return res
}
```

map함수는 이터러블 이터레이터 프로토콜을 따르고 products 뿐만 아니라 어떠한 이터러블도 올 수 있기 때문에 파라미터에 이터레이터를 받는다.

```javascript
const map = iter => {
  let res = []
  for(const a of iter){
    res.push(a.name)
  }
  return res
}
```

위에서는 어떤 값을 수집할지 a.name 이라고 명시적으로 적어 줬지만 map함수에서는 수집하는 값을 함수로 추상화 한다.

```javascript
const map = (f, iter) => {
  let res = []
  for(const a of iter){
    res.push(f(a))
  }
  return res
}
```

만들어진 map 함수를 이용해서 name과 prices를 뽑는 코드를 다시 작성 해보면

```javascript
const names = map(p=>p.name,products)
log(names) // ['과자', '사탕', '아이스크림']
log(map(p=>p.prices,products)) // ['1000', '500', '1200']
```

**따라서 map 함수의 보조함수를 통해서 이터러블 안에 1:1로 맵핑되는 어떠한 값을 수집할지 정해준다**

그리고 map함수는 함수를 인자로 받고 원하는 시점에서 값으로 사용하기 때문에 고차함수이다.



### 2. Map 함수의 다형성

```javascript
const queries = document.querySelectorAll('*')
log(queries) // NodeList(4) [html, head, script, script]
log(queries.map(ele => ele.nodeName)) //Type Error: queries.map is not a function
```

queries이지만 (내장)map 함수를 사용하면 에러가 나온다. 이유는 document.querySelector 객체는 Array를 상속받지 않아서 프로토타입에 map함수가 없기 때문이다

![image](https://user-images.githubusercontent.com/39187116/69561130-2ecd5e00-0ff0-11ea-8031-5ac6e758e35b.png)

하지만 위에서 직접 구현한 map 함수를 구현하면 리스트를 순회 할 수 있다.

```javascript
log(map(ele=>ele.nodeName, document.querySelectorAll('*')))
//["html", "head", "script", "script"]
```

그 이유는 document.querySelectorAll이 이터러블, 이터레이터 프로토콜을 따르기 때문이다.

```javascript
const iter = document.querySelectorAll('*')[Symbol.iterator]()
log(iter) // // Array Iterator {}
iter.next() // {value : "html", done : false}
```

다른 예시로는

```javascript
function *gen(){
  yield 1
  if(false) yield 3
  yield 5
}
log(map(a=>a*a, gen())) //[1, 25]
```

따라서 querySelectorAll 같이 이터러블 프로토콜을 따르는 모든 값들에 map 함수를 사용할 수 있고, gen() 같이 '문장' 에도 사용 가능 하다. ( 사실상 어디든 적용 가능 )



### 3. 자료구조 Map과 같이 사용

```javascript
const m = new Map()
m.set('a',10)
m.set('b',20)
m.set('c',30)
```

[이곳](https://doonguk.github.io/javascript/%ED%95%A8%EC%88%98%ED%98%95-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D(%EC%9D%B4%ED%84%B0%EB%9F%AC%EB%B8%94,-%EC%9D%B4%ED%84%B0%EB%A0%88%EC%9D%B4%ED%84%B0)/) 에서 공부한 듯이 자료구조 Map은 이터러블 이다. 따라서 위에서 구현한 map함수의 두번째 파타미터인 이러터블로 전달이 가능하다.

```javascript
log(map(([k,v]) => [k,v*v], m)) // [['a',100],['b',400],['c',900]]
```

첫번째 파라미터로 전달되는 함수에 구조 분해로 key, value를 나눠서 [k,v]로 전달 하였다.

이렇게 만든 Map객체로 새로운 Map객체를 만들 수 있다.

```javascript
const new_map = Map(map(([k,v]) => [k,v*v], m))
log(new_map) // [['a',100],['b',400],['c',900]]
```


