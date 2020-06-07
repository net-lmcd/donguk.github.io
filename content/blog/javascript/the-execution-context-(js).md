---
title: 실행 컨텍스트
date: 2020-05-04 17:05:01
category: javascript

---

> https://poiemaweb.com/js-execution-context 를 공부하고 정리한 글

<br/>

## 1. 실행 컨텍스트란?

실행 가능한 코드를 형상화하고 구분하는 추상적인 개념. 즉, **실행 컨텍스트는 실행 가능한 코드가 실행되기 위해 필요한 환경** 이라고 말할 수 있다. 여기서 실행 가능한 코드는 아래 3가지가 있다.

- 전역 코드: 전역 영역에 존재하는 코드 
- Eval 코드: `eval()`로 실행되는 코드
- 함수 코드: 함수 내에 존재하는 코드

자바스크립트 엔진은 코드를 실행하기 위해 여러가지 정보가 필요하다. 실행에 필요한 정보는 아래 4가지가 있다.

- 변수: 전역변수, 지역변수, 매개변수, 객체의 Property
- 함수 선언
- 변수의 유효범위 (Scope)
- this

이와같이 실행에 필요한 자료를 형상화하고 구분하기 위해 **자바스크립트 엔진은 실행 컨텍스트를 물리적 객체의 형태로 관리한다.** ( 3가지 프로퍼티를 소유. 밑에서 설명 )

``` javascript
var x = 'xxx';

function foo(){
  var y = 'yyy';
  function bar(){
    var z = 'zzz';
    console.log(x+y+z);
  }
  bar();
}
foo();
```

위 코드를 실행하면, **실행 컨텍스트 스택이 생성하고 소멸한다.**

**현재 실해중인 컨텍스트에서 이 컨텍스트와 관련없는 코드(예를들면 다른 함수)가 실행되면 새로운 컨텍스트가 생성된다. 이 컨텍스트는 스택에 쌓이게 되고 컨트롤(제어권)이 이동한다.**

![image](https://user-images.githubusercontent.com/39187116/80937691-34d23100-8e11-11ea-84cf-018266ec0c8f.png)

다음은 스택이 생성하고 소멸하는 과정이다.

1. 컨트롤이 실행 가능한 코드 (전역코드, 함수코드, eval 코드)로 이동하면 논리적 스택 구조를 가지는 새로운 실행 컨텍스트 스택이 생성된다.
2. 전역코드로 컨트롤이 진입하면 전역 실행 컨텍스트(`global EC`)가 생성되고 실행 컨텍스트 스택에 쌓인다. 전역 실행 컨텍스트는 어플리케이션의 종료까지 유지된다.
3. 함수를 호출하면 (현재 컨텍스트와 관련없는 코드의 실행) 해당 함수의 실행 컨텍스트가 생성되며 직전에 실행된 코드 블록의 실행 컨텍스트 위에 쌓인다.
4. 함수 실행이 끝나면 해당 함수의 실행 컨텍스트를 파기하고 직전의 실행 컨텍스트에 컨트롤을 반환한다.

_글로벌의 경우 실행 이전에 생성되지만 함수의 경우 호출할 때 생성된다._

<br/>

## 2. 실행 컨텍스트의 3가지 Property

### Variable Object (VO / 변수객체 )

실행 컨텍스트가 생성  => 자바스크립트 엔진은 **실행에 필요한 여러 정보들을 담을 객체를 생성한다. 이를 Variable Object (VO)** 라고 한다. 여기에는 아래의 정보가 담겨있다.

- 변수
- 매개변수(parameter)와 인수 (arguments)
- 함수 선언(함수 표현식은 제외)

`Variable Object` 는 실행 컨텍스트의 프로퍼티이기 때문에 값을 갖는데 이 값은 다른 객체를 가리킨다. 여기서 **전역 코드 실행시 실행되는 전역 컨텍스트 객체와 함수를 실행할 때 생성되는 함수 컨텍스트의 경우, 가리키는 객체가 다르다.** ( 전역 코드와 함수의 내용이 다르기 때문에 )

`Variable Object`가  가리키는 객체는 아래와 같다.

- 전역 컨텍스트의 경우

`Variable Object`는 최상위에 위치하고 모든 전역변수, 전역 함수 등을 포함하는 **전역 객체(Global Object/ GO)** 를 카리킨다. **전역 객체는 전역에 선언된 전역 변수와 전역 함수를 프로퍼티로 소유한다.**

![image](https://user-images.githubusercontent.com/39187116/80938342-b5922c80-8e13-11ea-9d24-7fbeb513a6b9.png)

- 함수 컨텍스트의 경우

`Variable Object`는 **Activation Object / AO 활성객체** 를 가리키며 매개변수와 인수들의 정보를 배열의 형태로 담고 있는 객체인 `argument object`가 추가된다

![image](https://user-images.githubusercontent.com/39187116/80938408-fc802200-8e13-11ea-84f3-ca7c4d42e574.png)

<br/>

### Scope Chain (SC)

`Scope Chain`은 일종의 리스트로서, 해당 전역 또는 함수가 참조할 수 있는 변수, 함수 선언 등의 정보를 담고있는 전역 객체 (GO) 또는 활성 객체(AO)의 리스트를 가리킨다.

**현재 실행 컨텍스트의 활성 객체를 선두로 하여 순차적으로 상위 컨텍스트의 활성 객체를 가리키며 마지막으로 전역 객체를 가리킨다.**

![image](https://user-images.githubusercontent.com/39187116/80938696-0ce4cc80-8e15-11ea-88cb-eb11f766fc04.png)

_스코프 체인은 변수를 검색하는 메커니즘이다. 객체의 프로퍼티나 메소드를 검색하는 메커니즘은 프로토타입 체인이다._

스코프 체인을 통해 하위함수에서 상위함수의 스코프까지 참조가 가능하다. (**함수가 중첩되어 있으면 부모함수의 Scope가 자식함수의 스코프 체인에 포함되기 때문이다.**) 함수 실행 중 변수를 만나면 그 변수를 우선 현재 Scope, 즉 AO에서 검색해보고, 만약 검색에 실패하면 스코프 체인에 담겨진 순서대로 검색을 이어간다. (이것이 스코프 체인이라고 불리는 이유이다.)

스코프 체인은 함수의 프로퍼티인 `[[Scope]]`로 참조할 수 있다.

<br/>

### this value

`this` 프로퍼티는 this 값에 할당된다. 이는 함수 호출 패턴에 의해 결정된다.

<br/>

## 3. 실행 컨텍스트의 생성 과정

 ```javascript
var x = 'xxx';

function foo(){
  var y = 'yyy';
  function bar(){
    var z = 'zzz';
    console.log(x+y+z);
  }
  bar();
}
foo();
 ```

### 전역 코드에 진입

컨트롤이 실행 컨텍스트에 **진입하기 이전**에 유일한 **전역 객체가 생성된다.**  전역 객체는 다음과 같은 특징이 있다.

- 객체의 프로퍼티는 코드의 어떠한 곳에서도 접근 가능
- 초기 상태의 전역 객체에는 Built-in Object (Math, String, Array)와 BOM, DOM이 설정되어 있음

![image](https://user-images.githubusercontent.com/39187116/80939125-77e2d300-8e16-11ea-8787-edad3a7db9a3.png)

**전역 객체가 생성된 이후**, 전역 코드로 컨트롤이 진입하면 전역 실행 컨텍스트가 생성되고 실행 컨텍스트 스택에 쌓인다.

![image](https://user-images.githubusercontent.com/39187116/80939230-e32ca500-8e16-11ea-8072-10dcefab8e1a.png)

이후 실행 컨텍스트를 바탕으로 다음의 과정이 실행된다.

1. 스코프 체인의 생성과 초기화
2. Variable instantiation (변수 객체화) 실행
3. this value 설정

<br/>

### 스코프 체인의 생성과 초기화

실행 컨텍스트 생성된 이후 **가장 먼저 스코프 체인의 생성과 초기화가 일어난다.** 이때 스코프 체인은 전역 객체의 레퍼런스를 포함하는 리스트가 된다.

![image](https://user-images.githubusercontent.com/39187116/80939436-99908a00-8e17-11ea-99e5-9f18059eedd3.png)

<br/>

### Variable Instantiation (변수 객체화) 실행

`Variable Instantiation`은 실행 컨텍스트 객체 프로퍼티 중 하나인 `VO` 에 프로퍼티와 값을 추가하는 것을 의미한다. 이 과정에서 변수, 매개변수와 인수 정보, 함수 선언을 `VO` 에 추가하여 객체화 한다.

`Variable Instantiation`은 다음과 같은 순서로 `Variable Object`에 값을 설정한다. **(1->2->3)순서 유지)**

1. (함수 코드의 경우) **매개변수**가 VO의 프로퍼티로, 인수가 값으로 설정된다. (`argumetns`객체 초기화 )

2. 대상 코드 내의 **함수** 선언(함수 표현식 제외)을 대상으로 함수명이 VO의 프로퍼티로, 생성된 함수 객체가 값으로 설정된다. (**함수 호이스팅**)
3. 대상 코드 내의 **변수** 선언을 대상으로 변수명이 VO의 프로퍼티로, `undefined`가 값으로 설정된다. (**변수 호이스팅**)

*VO에 프로퍼티와 값을 설정하는 과정에서 호이스팅이 일어난다.*

<br/>

### 함수 foo의 선언 처리

함수 선언은 `Variable Instantiation` 실행순서 2와 같이 함수명 foo가 VO(전역 코드인 경우 GO)의 프로퍼티로, 생성된 함수가 값으로 설정된다.

![image](https://user-images.githubusercontent.com/39187116/80940743-a3b48780-8e1b-11ea-8654-256ab6ae8c82.png)

생성된 함수 객체는 `[[Scrope]]` 프로퍼티를 가지게 된다. 이는 함수 객체만이 소유하는 내부 프로퍼티로서 **함수 객체가 실행되는 환경**을 가리킨다. 따라서 현재 실행 컨텍스트의 스코프 체인이 참조하고 있는 객체를 값으로 설정한다.

내부 함수의 `[[Scope]]`의 프로퍼티는 자신의 실행 환경과 자신을 포함하는 외부함수의 실행 환경과 전역객체를 가리키는데 이때 자신을 포함하는 외부 함수의 실행 컨텍스트가 소멸하여도 `[[Scope]]` 프로퍼티가 가리키는 외부함수의 실행 환경(AO)는 소멸하지 않고 참조할 수 있다. 이것이 **클로저** 이다

_즉 **클로저**는 스택에서 외부함수의 실행 컨텍스트가 소멸했지만, 내부 함수의 스코프 체인에서 가리키는 외부함수의 활성 객체가 남아있는 상태라고 말할 수 있겠네_

여기까지 살펴본 실행 컨텍스트는 아직 코드가 실행되기 이전이다. 하지만, **스코프 체인이 가리키는 VO에 이미 함수가 등록** 되어 있으므로 이후 코드를 실행 할 때 함수 선언식 이전에 함수를 호출할 수 있게 되었다. **(함수 호이스팅)**

**함수선언식의 경우 VO에 함수 표현식과 동일하게 함수명을 프로퍼티로 함수객체를 값으로 할당 하지만**,,,

잠깐! 함수 선언식 vs 함수 표현식

```javascript
// 함수 선언식 (일반적인 프로그래밍 언어 방법)
function hello(){}
// 함수 표현식 (JS의 특징)
var funcExpression = function(){}
```

**함수 선언식**은 VO에 변수와 함수객체를 **즉시 할당하고**, 

**함수 표현식**은 **일반 변수의 방식을 따른다.** (즉시 할당하지 않고 코드가 실행되는 시점에 값이 할당) 이렇게 함수 선언식의 코드가 실행되기 이전 현재 실행 컨텍스트의 `Variable Instatiation` 과정에서 함수가 VO에 먼저 할당된다. 이를 **함수 호이스팅** 이라 한다.

```javascript
// 함수 호이스팅 
hello() // 'function hoisting'

function hello(){
  console.log('function hoisting')
}

var funcExpression
funcExpression() // Reference Error ㅜㅜ

funcExpression = function(){
  console.log('maybe occured Reference Error....')
}
```

<br/>

### 변수 x의 선언 처리

변수 선언은 `Variable Instantiation` 순서3과 같이 변수명(x)가 VO의 프로퍼티로, `undefined`가 값으로 설정된다. 이 작업은 아래와 같이 세분화된다.

1. 선언 단계

VO에 변수를 등록한다. 이 변수 객체는 스코프가 참조할 수 있는 대상이 된다.

2. 초기화 단계

VO에 등록된 변수를 메모리에 할당한다. 이 단계에서 `undefined`로 초기화된다.

3. 할당 단계

`undefined`로 초기화된 변수에 실제값을 할당한다.

**`var` 키워드로 선언된 변수는 선언과 초기화가 한번에 이루어진다.**  따라서 선언문 이전에 변수에 접근하여도 VO에 변수가 존재하기 때문에 에러가 발생하지 않는다. 단 `undefined`를 반환한다. 이러한 현상을 **변수 호이스팅** 이라 한다.

변수 x는 'xxx'로 아직 초기화되지 않았고, 이후 변수 할당문에 도달하면 값의 할당이 이루어진다.

![image](https://user-images.githubusercontent.com/39187116/80943384-0872e080-8e22-11ea-8dac-03811af2f46f.png)

_변수 호이스팅 샘플_

```javascript
// var는 선언과 동시에 undefined로 초기화 후 VO에 등록 -> 변수 호이스팅
console.log(a) // undefined
var a

// let은 선언과 동시에 초기화 x
console.log(b) // Reference error
let b
```

<br/>

### this value 결정

변수 선언처리가 끝나면 다음은 this value가 결정된다. **this value가 결정되기 이전에 this는 전역 객체를 가리키고 있다가 함수 호출 패턴에 의해 `this`  에 할당되는 값이 결정된다.** 전역 코드의 경우 this는 전역 객체를 가리킨다.

![image](https://user-images.githubusercontent.com/39187116/80943531-61db0f80-8e22-11ea-898a-6dbc8b95a6a1.png)

**전역 컨텍스트(전역 코드)의 경우 VO, SC, this의 값은 항상 GO이다.**

<br/>

### 전역 코드의 실행

예제의 코드에서 변수 x에 문자열 'xxx' 할당과 함수 foo의 호출이 실행된다.

### 변수 값의 할당

전역 변수 x에 문자열 'xxx'를 할당할 때, **현재 실행 컨텍스트의 스코프 체인을 0번 인덱스 부터 가리키고 있는 VO를 검색하여 변수명에 해당하는 프로퍼티가 있는지 찾는다.** 발견하면 'xxx'를 할당한다.

### 함수 foo의 실행

전역코드의 함수 foo가 실행되면 **새로운 함수 실행 컨텍스트가 생성된다.** 함수 foo의 실행 컨텍스트로 컨트롤이 이동하면 전역 코드의 경우와 마찬가지로 

1. 스코프 체인의 생성과 초기화
2. Variable Instantiation 실행
3. this value 결정

이 순차적으로 실행된다. 단, 전역 코드와 다르게 **함수 코드**가 실행되기 때문에 전역 코드의 룰이 아닌 함수코드의 룰이 적용된다.

![image](https://user-images.githubusercontent.com/39187116/80943976-6c49d900-8e23-11ea-90d7-ba98400ae691.png)

### 스코프 체인의 생성과 초기화

함수 코드의 **스코프 체인의 생성과 초기화**는 Activation Object를 생성하고 이 객체를 스코프 체인의 선두에 설정하는 것으로 시작한다.

Activation Object는 우선 **arguments 프로퍼티의 초기화**를 실행한다. 그 후 `Variable Instantiation` 가 실행된다. ( AO에는 프로그램이 직접 접근할 수 없다. 하지만 프로퍼티에는 접근 가능하다.)

_잠깐.. `arguments` 객체란 함수에 전달된 인수에 해당하는 `Array` 형태의 객체이다._

```javascript
function hello(a, b, c){
  console.log(arguments[0]) // 1
  console.log(arguments[1]) // 2
  console.log(arguments[2]) // 3
}
hello(1,2,3)
```

![image](https://user-images.githubusercontent.com/39187116/80944471-a1a2f680-8e24-11ea-9e9a-06fa7e040a1f.png)

그 후, `Caller ( 이 시점에서는 전역 컨텍스트 )` 의 SC이 참조하고 있는 객체(GO)가 현재 실행 컨텍스트의 스코프 체인에 `push` 된다. 

따라서 함수 foo를 실행한 직후, 현재 실행 컨텍스트의 스코프 체인은 AO-1 과 GO 를 순차적으로 참조한다.

![image](https://user-images.githubusercontent.com/39187116/80944653-052d2400-8e25-11ea-897d-36576300f159.png)

<br/>

### Variable Instantiation 실행

**스코프 체인의 생성과 초기화**에서 생성된 AO를 VO로서 `Variable Instantiation`이 실행된다. 이 과정을 제외하고는 전역 코드와 같은 처리가 실행된다. ( VO에 함수 설정 -> VO에 변수 설정 ) 따라서 VO에 프로퍼티 bar 값은 새로 생성된 Function Object 로 설정되고, `[[Scopes]`]는 현재 실행 컨테스트의 SC 리스트를 가리키게 된다.

![image](https://user-images.githubusercontent.com/39187116/80945115-0a3ea300-8e26-11ea-808b-147bb5ea9c6d.png) 

다음으로 변수 y를 프로퍼티로 추가하고 값은 `undefined`로 초기화 한다. ( `var`로 선언 했으니까 동시에 함)

![image](https://user-images.githubusercontent.com/39187116/80945182-31957000-8e26-11ea-9b46-8c58557fdfe5.png)

<br/>

### this value 설정

변수 처리가 끝나면 다음은 this value가 결정된다. (처음에는 다 GO 가리킴) this에 할당되는 값은 함수 호출 패턴에 의해 결정된다. **내부 함수의 경우 this의 value는 전역 객체이다.**

![image](https://user-images.githubusercontent.com/39187116/80945316-846f2780-8e26-11ea-9cf9-46359e779c62.png) 

<br/>

### foo 함수 코드의 실행

foo함수 내부에서는 변수 y에 문자열 'yyy'의 할당과 함수 bar가 실행된다.

### 변수 값의 할당

지역변수 'y'에 문자열 'yyy'를 할당할 때, 현재 실행 컨텍스트의 스코프 체인을 0번 인덱스 부터 순회하여 참조하고 있는 VO에 변수 y가 있다면 값 'yyy'를 할당한다.

![image](https://user-images.githubusercontent.com/39187116/80945532-f6e00780-8e26-11ea-9e00-ffcfd7f1c309.png)

### 함수 bar의 실행

함수 bar가 실행되기 시작하면 새로운 실행 컨텍스트가 생성되고 컨트롤이 함수 bar의 실행 컨테스트로 넘어가면 함수 foo의 코드와 같이 

1. 스코프 체인의 생성과 초기화 ( 활성객체 생성, SC 리스트 0번 인덱스가 VO 가리킴 )
2. Variable Instantiation 실행 
3. this value 결정

![image](https://user-images.githubusercontent.com/39187116/80945743-71a92280-8e27-11ea-919a-7210e2a338c1.png)

마지막 단계에서 `console.log(x + y + z)` 결과는 아래와 같은 과정을 거친다.

스코프 체인 변수 검색

1. x :  AO-2에서 x 검색 실패 -> AO-1에서 변수 검색 실패 -> GO에서 성공
2. y : AO-2에서 검색 실패 -> AO-1에서 검색 성공
3. z : AO-2 에서 검색 성공
