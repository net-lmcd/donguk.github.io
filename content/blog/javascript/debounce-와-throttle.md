---
title: Debounce 와 Throttle
date: 2020-05-20 13:05:66
category: javascript
---

`debounce`와 `throttle`는 모두 **DOM 이벤트를 기반으로 실행하는 자바스크립트를 성능상의 이유로 `event` 를 제어**하는 방법이다.

### 사용예시

1. 사용자가 창 크기 조정을 멈출 때까지 기다렸다가 `resizing event` 사용
2. 사용자가 키보드 입력을 중지 할 때까지 `API` 요청을 발생시키지 않기 위해
3. 페이지의 스크롤에 따른 렌더링
4. 앱에서 요소를 드래그 할 때 좋은 성능을 보장하기 위해

등등..

### Debounce

**연이어 호출되는 함수들 중 마지막 함수(또는 제일 처음)만 호출하도록 하는 것**

좋은 코드는 아니지만 예시 작성코드.

```javascript
const debounce = (f, tick) => {
  let timer = ''
  return (...args) => {
    if (timer) {
      clearTimeout(timer)
    }
    timer = setTimeout(() => {
      f(...args)
    }, tick)
  }
}
```

### Throttle

**마지막 함수가 호출된 후 일정 시간이 지나기 전에 다시 호출되지 않도록 하는 것**

```javascript
const throttle = (f, tick) => {
  let timer = ''
  return (...args) => {
    if (!timer) {
      timer = setTimeout(() => {
        timer = ''
        f(...args)
      }, tick)
    }
  }
}
```

### 차이점

`debounce`와 `throttle`의 가장 큰 차이점은 `throttle`는 적어도 어느정도 밀리 초마다 정기적으로 기능 실행을 보장받는다. 하지만 `debounce`는 아무리 많은 이벤트가 발생해도 모두 무시하고 딱 한번만 이벤트를 발생시킨다.
