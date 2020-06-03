---
title: JS의 프로토타입
date: 2020-06-03 14:06:71
category: javascript
---

자바스크립트는 프로토타입 기반 언어라고 불린다. 프로토타입이라는게 뭘까?

## 간단한 예시

```javascript
function Person() {
	this.eyes = 2
  this.nose = 1
}

const a = new Person()
const b = new Person()
```

a 와 b 인스턴스는 `eyes`와 `nose`를 **공통**으로 가지고 있는데 메모리에는 eyes와 nose가 각각 2개씩 총 4개가 할당된다. 만약 100개의 인스턴스를 만들면 200개의 변수가 메모리에 할당된다. 이런 문제를 `Prototype` 으로 해결할 수 있다!

```javascript
function Person() {}
Person.prototype.eyes = 2
Person.porotoype.nose = 1
const a = new Person()
const b = new Person()
console.log(a.eyes) // 2
```

이게 가능한 이유는 `Person.prototype`이라는 빈 객체가 존재하고 `Person`함수를 통해 생성된 객체를 이 객체를 참조할 수 있기 때문이다.

## Prototype LInk 와 Prototype Object

>  JS에는 Prototype Link와 Prototype Object가 존재하고 이 둘을 통틀어 Prototype이라고 부른다.

### Prototype Object

우선 JS에서 **객체는 언제나 함수**로 생성된다.

```javascript
function Person() {}
const pObj = new Person() // 함수로 객체를 생성
```

객체 리터럴 방식도 마찬가지다.

```javascript
const a = {}
```

위 코드는 `const a = new Object()` 와 같다. `Object`라는 함수를 통해서 객체가 생성되는 것이다. ( 기본적으로 내장된 함수 )

`Object`와 마찬가지로 `Array`, `Function` 모두 함수로 정의되어 있다. 이것이 Prototype Object와 무슨 관계일까?

**함수가 정의될 때는 2가지 일이 동시에 이루어진다.**

1. 해당 함수에 `constructor` 자격 부여 ( 객체를 만들 수 있는 자격 부여 )

이것이 함수가 new 키워드를 사용할 수 있는 이유이다.

2. 해당 함수의 `Prototype Object` 생성 및 연결

![image](https://user-images.githubusercontent.com/39187116/83596781-7edd3c80-a5a0-11ea-9cab-b3b7992ce2e4.png)

생성된 함수는 `prototype` 프로퍼티를 통해 `Prototype Object`에 접근할 수 있다. `Prototype Object`는 일반적인 객체와 같고 기본적으로 `constructor`, `__proto__` 프로퍼티를 갖고있다.

`constructor`는 Prototype Object와 같이 생성되었던 함수를 가리키고 있다.

`__proto__`는 `Prototype Link` 이다.

### Prototype Link

```javascript
function Person() {}
Person.prototype.eyes = 2
Person.prototype.nose = 2
const a = new Person()
console.log(a.eyes) // 2
```

다시 위에서 했던 예시를 보면 a 에는 eyes 라는 프로퍼티가 없는데 어떻게 `Prototype Object`에 있는 속성을 참조할 수 있을까?

그 이유는 a 가 갖고있는 `__proto__` 속성 때문이다.

`prototype` 속성은 함수만 갖고있지만 `__proto__` 속성은 모든 객체가 빠짐없이 갖고 있다.

**이 속성은 객체가 생성될 때 조상이었던 함수의 `Prototype Object`를 가리킨다.** ( 여기서 a 인스턴스는 Person 함수로부터 생성되었으니 Person.Prototype을 가리킨다. )

![image](https://user-images.githubusercontent.com/39187116/83597309-f790c880-a5a1-11ea-9407-6b65f7a0f6fa.png)

kim 객체는 eyes를 직접 가지고 있지 않기 때문에 eyes 속성을 찾을 때 까지 상위 프로토타입을 탐색한다.

최상위인 Obejct의 Prototype Object까지 도달했는데도 못찾았을 경우 `undefined`를 리턴한다.

이렇게 **`__proto__` 속성을 통해 상위 프로토타입과 연결되어있는 형태를 프로토타입 체인이라고 한다.**

이런 프로토타입 체인 구조 때문에 모든 객체는 `Object`의 자식이라고 불리고, `Object.Prototyp`에 있는 모든 속성을 사용할 수 있다.

## Reference

https://medium.com/@bluesh55/javascript-prototype-%EC%9D%B4%ED%95%B4%ED%95%98%EA%B8%B0-f8e67c286b67
