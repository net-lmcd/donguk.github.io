---
title: 모듈(Module)
date: 2020-06-05 10:06:73
category: javascript
---

## 모듈(module)이란?

모듈이란 **여러 기능들에 관한 코드가 모여있는 파일**로 다음과 같은 기능이 있다.

- **유지보수성** : 기능들이 모듈화가 잘 되어있다면, **의존성을 그만큼 줄일 수 있**기 때문에 어떤 기능을 개선한다거나 수정할 때 훨씬 편하게 할 수있다.
- **네임스페이스화** : 자바스크립트에서 전역변수는 전역공간을 가지기 때문에 코드의 양이 많아질수록 겹치는 네임스페이스가 많아질 수 있다. 그러나 **모듈로 분리하면 모듈만의 네임스페이스를 갖기** 때문에 그 문제가 해결된다.
- **재사용성** : 똑같은 코드를 반복하지 않고 모듈로 분리시켜서 필요할 때마다 재사용 할 수 있다.

<br/>

## CommonJS

- 자바스크립트의 공식 스펙은 브라우저만 지원하는데 이를 서버사이드 및 데스크탑 어플리케이션에서 지원하기 위한 노력이 있었고 이를 위해 만든 그룹이 `CommonJS`이다. 현재 NodeJS에서는 이 방식을 사용하고 있다.

- 다른 모듈을 사용할 때는 `require`를, 모듈을 해당 스코프 밖으로 보낼 때에는 `module.exports`를 사용한다. 
- `webpack`은 CommonJS를 지원하기 때문에 `NodeJS`가 아닌 환경에서도 사용 가능하다.
- **webpack 없이 spec을 구현한 벤더의 라이브러리를 가져와서 사용할 수 있는지는 확인이 필요하다.**

### 예시

```javascript
//a.js
const printHelloWolrd = () => {
  console.log('hello world')
}
module.exports = { printHelloWorld }

//b.js
const aObj = require('./a.js') // 같은 dir라고 가정.
aObj.printHelloWorld() // 'hello world'
```

`module.exports`의 `module`은 현재 모듈에 대한 정보를 갖고있는 객체이다. `id`, `path`, `parent`등 의 속성이 있고 `exports`라는 프로퍼티를 갖고 있다.

### `exports` vs `module.exports` 

- `module.exports`는 빈 객체를 참조한다.
- `exports`는 `module.exports`를 참조한다.
- `require`는 항상 `module.exports`를 리턴받는다.

즉, 함수를 모듈 밖으로 내보내고자 할 때는 아래 2가지를 모두 사용할 수 있다.

```javascript
exports.printHelloWolrd = printHelloWolrd
module.exports = { printHelloWolrd }
```

이는 `exports` 에 어떤 값을 할당하거나 새로운 객체를 할당했다고 하더라도 결국 `require` 는 `module.exports` 를 리턴받기 때문에 가능하다.

### 문제점

**CommonJS의 모듈 명세는 모든 파일이 로컬 디스크에 있어 필요할 때 바로 불러올 수 있는 상황을 전제로 한다. 다시 말해 서버사이드 JavaScript 환경을 전제로 한다.**

따라서 필요한 모듈들을 다운받을 때 까지 브라우저에서는 아무것도 할 수 없는 문제가 생긴다.  CommonJS 그룹은 처음에 `<script>` 태그를 동적으로 삽입하는 방법을 사용했다. 하지만 브라우저에서는 **파일들간에 scope가 구분되지 않기** 때문에 전역변수가 오염될 가능성이 높다. 이를 해결하기 위해 CommonJS 그룹은 **모듈 전송 포맷** 이라는 것을 만들었다.

모듈 전송 포맷의 예시는 [여기](https://d2.naver.com/helloworld/12864)를 참고하자. 

<br/>

## AMD (Asynchronous Module Definition)

CommonJS 그룹에서 의견이 맞지 않아 나온 사람들이 만든 그룹으로 비동기 모듈에 대한 표준안을 다루는 그룹이다

- CommonJS는 서버쪽에서 장점이 많은 반면에 **AMD는 브라우저 쪽에서 더 큰 효과를 발휘한다.** 브라우저에서는 모든 모듈이 다 로딩될 떄까지 기다릴 수 없기 때문에 **비동기 모듈 로딩방식으로** 구현을 해놓았다.
- AMD의 명세는 `define` 함수( 클로저를 이용한 패턴)를 이용해 모듈을 구현하므로 전역변수 문제가 없다. 또한 필요할 때 모듈을 불러오는 방식으로 `lazy-loading` 기법을 응용할 수 있다.

- 이 방식에서 사용하는 함수는 `require` 와 `define` 이며, AMD 스팩을 가장 잘 구현한 모듈로더는 `RequireJS`이다.

### RequireJS 예시

#### index.html

```html
<DOCTYPE html>
<html lang="en">
<head>
	<title>hi</title>  
</head>
<body>
	<script data-main="index.js" src="require.js"></script>
</body>
</html>  
```

`require.js`가 로드되면 `index.js`가 실행되는 구조이다.

#### index.js

```javascript
require.config({
  baseUrl: '/',
  paths: {
    a: 'a',
    b: 'b'
  }
})
require(['a'], (a) => { a.printA() })
```

`require.config` 는 설정부분으로 기본 경로와 각 모듈에 해당하는 경로를 설정해준다. 그 다음 `require` 를 통해서 첫번째 인자에 해당하는 모듈이 로드되었을 경우에 그걸 `a` 로 받아서 `printA()` 함수를 호출하는 콜백함수를 실행한다. 의존성 모듈을 지정해주는 것이다.

#### a.js

```javascript
define(()=>{
  return {
    printA: () => console.log('a')
  } // 네임스페이스 객체 반환
})
```

모듈을 구현하는 파일이다. `define` 함수를 통해서 네임스페이스화 한다. 

<br/>

## ES6(ES2015) 모듈 포맷

- **ES6**에서 제공하는 브라우저 내장 포맷
- 모든 브라우저가 지원되는건 아니다. 이런 경우 트랜스 파일러 [`babel`](https://doonguk.github.io/javascript/babel/)의 `@babel/plugin-transform-modules-commonj`를 통해 변환시켜서 사용한다.  모듈 A,B가 있고 각각을 `export`로 내보내는 방식이다.

### 예제

```javascript
//A.js
const A = () => {}
export default A

//B.js
export const B = () => {}

//index.js
import A from './A.js'
import { B } from './B.js'
```

위와 같은 방법도 있고 `*` 와일드 카드를 이용해서 한번에 내보내고 가져오기도 한다.

<br/>

## UMD (Universal Module Definition)

그 밖의 모듈 시스템으로는 `UMD` 라는게 있다. 모듈 시스템을 `AMD`와 `CommonJS` 두 그룹으로 나누다보니 호환성에 문제가 많았다. 그래서 나온것이 `UMD`이다. 자세한 내용은 [여기](https://www.zerocho.com/category/JavaScript/post/5b67e7847bbbd3001b43fd73) 를 참고하자.

<br/>

지금까지가 모듈에 관한 개념이었다! 마지막으로 모듈 번들러와 트랜스파일러에 대해 정리해보자~~

## 모듈 번들러

모듈단위의 개발은 다음과 같은 문제를 발생시킬 수 있다.

- 수많은 모듈들이 순서를 어떻게 처리할 것인가? (의존성 처리)
- 모듈이 많아질수록 HTTP 요청이 많아질텐데 이로 이한 오버헤드는 어떻게 해결할 것인가?
- ES6+ 스펙의 코드는 어떻게 처리할 것인가?

위 문제들을 해결하기 위해 등장한 것이 **모듈 번들러(Module Bundler)로 각각의 모듈 의존성을 해결하여 하나의 자바스크립트 파일로 만듦과 동시에 ES6+ 스펙을 ES5로 변환해주는 도구**이다. 대표적인 번들러로는 **Webpack**, Parcel, Rollup 등이 있다.

## 트랜스파일러

- 트랜스 파일러는 **특정 언어로 작성된 코드를 비슷한 다른 코드로 변환시켜주는 역할**을 한다.
- 트랜스 파일러가 필요한 이유는 모든 브라우저가 개발자가 작성한 코드를 전부 이해할 수 없기 때문이다.( 말이 애매하네..)
- 예시로는 다음과 같다.
  - ES6+ 를 ES5로 변환 ( 바벨 )
  - 리액트의 JSX를 자바스크립트 코드로 변환 ( 바벨 )
  - 타입스크립트를 자바스크립트 코드로 변환 ( 타입스크립트 트랜스파일러 )



## Reference

1. https://d2.naver.com/helloworld/12864

2. [https://github.com/mjs0514/hokage-website/wiki/%EB%AA%A8%EB%93%88-%ED%8F%AC%EB%A7%B7](https://github.com/mjs0514/hokage-website/wiki/모듈-포맷)

3. https://babeljs.io/docs/en/babel-plugin-transform-modules-commonjs
