---
title: 함수형 프로그래밍(take, 지연성 평가)
date: 2019-12-06 13:12:02
category: FP
---

> # take 함수와 지연평가

# take 함수

```javascript
const take = (l, iter) => {
  let res = []
  for(const a of iter){
    res.push(a)
    if(res.length === l) return res
  }
  return res
}
```

take 함수는 파라미터로 받은 length 만큼 이터러블을 자르는 간단한 함수이다.

이전 포스트에서 공부했던 range 함수와 같이 사용한다면,

```javascript
log(take(5, range(100))) // [0,1,2,3,4]
log(take(5, L.range(100))) // [0,1,2,3,4]
```

위와 같은 결과를 얻는다. 다음으로 지연성에 관한 설명을 한다면

만약 range에 무한수열이 들어간다고 생각해보자.

```javascript
log(take(5, range(Infinity))) 
```

range 함수에 위와같이 무한수열이 들어가면 range 함수의 경우 무한대 길이의 배열로 평가되려고 하기 때문에 브라우저가 멈춘다.

하지만!

```javascript
log(take(5, L.range(Infinity)))
```

제너레이터 함수를 사용한 지연성 range 함수가 무한수열을 인자로 평가되면 L.range 함수 인자로 전달된 이터러블이 이터레이터를 생성한 뒤에 next() 함수를 5번만 호출하기 때문에 브라우저가 멈추지 않는다.

결론은 지연성 함수를 사용 한다면 이터러블이 Array 자체를 생성하지 않고 Array가 값이 조회되어 필요한 로직을 처리하는 과정을 생략할 수 있기 때문에 효율성 에서도 좋다.



# 지연평가( Lazy Evaluation )



게으름 평가라고도 하지만 **영리한 평가** 라는 뜻도 있다. 따라서 제때 계산법이라고 표현 되기도 하는데 가장 필요할 때 까지 계산을 미루다가 필요한 시점에 평가를 하는 방식이다.

앞에 이야기 했던 range 함수처럼 필요한 배열을 미리 만들어 놓는게 아니라, 내부의 값이 필요한 시점에 ( reduce ) next() 함수로 호출하여 값을 계산하는 방식이다. 따라서 필요없는 연산을 줄일 수 있다.
