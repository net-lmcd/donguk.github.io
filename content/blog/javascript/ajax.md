---
title: AJAX
date: 2020-06-06 16:06:26
category: javascript
---

## AJAX?

`Asynchronous Javascript And XML`의 약자로, **비동기적**으로 JS를 사용해서 데이터를 받아와 동적으로 DOM을 갱신 및 조작하는 웹 개발 기법을 의미한다. 여기서 XML이 있는 이유는 예전에 데이터 포맷으로 XML을 많이 사용했기 때문이다.

## 어떻게 동작?

![image](https://user-images.githubusercontent.com/39187116/83938956-ee0e9700-a813-11ea-8d1c-cda4f0fa3667.png)

사용자가 AJAX가 적용된 UI와 상호작용하면, 서버에 AJAX 요청을 보내게 된다. 서버는 DB에서 데이터를 가져와서 JS 파일에 정의되어 있는 대로 HTML/CSS와 데이터를 융합하여 만든 DOM 객체를 UI에 업데이트 시킨다. 비동기적으로 이루어지며, **기존의 페이지를 전부 로딩하는 방식이 아닌 일부만 업데이트 하는 방식이다.**

## 장단점?

### 장점

- 페이지를 전환하지 않고 동적으로 필요한 부분의 UI를 업데이트할 수 있다.
- 수신하는 데이터 양을 줄일 수 있고 클라이언트에게 처리를 일부 맡기기 때문에 서버의 부하가 줄어든다.
- 서버 처리를 기다리지 않고 비동기 요청이 가능하다.

### 단점

- 지원하지 않는 브라우저가 있다.
- 무분별하게 사용하면 역으로 서버의 부하가 늘어날 수 있다.
- SOP 문제가 발생할 수 있다.

## 사용법

### XMLHttpRequest

일반적으로 `XMLHttpRequest` 객체를 사용하여 인스턴스를 만들어 인스턴스의 `open()` , `send()` 등의 메소드를 이용한다.

```javascript
var ourRequest = new XMLHttpRequest();
ourRequest.open(
  "GET",
  "https://learnwebcode.github.io/json-example/animals-1.json"
);
ourRequest.onload = () => {
  var ourData = JSON.parse(ourRequest.responseText);
  console.log(ourData[0]);
};
ourRequest.send();
```

`open()` 으로 요청할 메소드와 URL을 설정한 뒤, 모두 로드되었을 경우의 콜백함수를 초기화한다.

### Fetch API

새로나온 `fetch` 를 사용해서 요청을 할 수도 있는데 IE를 지원하지 않는다는 점을 제외하고는 `XMLHttpReqeust` 보다 훨씬 직관적이다. ES6(ES2015) 에서 표준이 되었고, Promise를 리턴한다.

```javascript
fetch("https://learnwebcode.github.io/json-example/animals-1.json")
	.then(res => res.json())
	.then(resJson => console.log(resJson));
```

응답객체는 `json()` , `blob()` 과 같은 내장 메서드로 body를 추출해내고 이는 다시 Promise를 리턴한다.

## Reference

https://github.com/baeharam/Must-Know-About-Frontend/blob/master/Notes/javascript/ajax.md
