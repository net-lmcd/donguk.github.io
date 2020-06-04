---
title: Babel
date: 2020-06-04 20:06:24
category: javascript
---

## babel?

- `babel`은 자바스크립트 **트랜스파일러**다. 

- 입력은 자바스크립트 코드고 출력도 자바스크립트 코드다. 

- **최신 버전의 자바스크립트 문법은 브라우저가 이해하지 못하기 때문에 babel이 브라우저가 이해할 수 있는 문법으로 변환해준다.**

  

## babel-polyfill

babel을 사용한다고 자바스크립트 최신 함수를 사용할 수 있는게 아님. **babel은 문법을 변환해주는 역할만 할 뿐이다.**

- `polyfill`은 프로그램이 처음에 시작될 때 현재 브라우저에서 지원하지 않는 함수를 검사해서 각 object의 prototype에 붙여주는 역할을 한다.

- 즉, `babel`은 **컴파일 타임**에 실행되고 `babel-polyfill`은 **런타임**에 실행된다.
- `babel-polyfill`을 사용하고 싶다면 별도로 설정해줘야 한다.



## .babelrc

- `babel` 설정 파일이다. `plugins`와 `preset`을 설정할 수 있다.
- 예를들면 `plugin` 은 es2015의 문법 하나하나를 말한다.
- `preset`은 `plugin`이 여러개가 묶여있는 개념이다. 대표적으로 ES6 문법을 모아놓은 es2015 preset과 react 문법을 모아놓은 react preset이 있다.

## Reference

1. [https://medium.com/@ljs0705/babel-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-a1d0e6bd021a](https://medium.com/@ljs0705/babel-이해하기-a1d0e6bd021a)

2. https://steemit.com/javascript/@noreco/babel
