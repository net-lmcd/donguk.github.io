---
title: Woowahan Typescript 세미나 정리(1)
date: 2020-10-19 21:10:70
category: typescript
---

## 목차

1. <a href="#1">작성자와 사용자</a>
2. <a href="#2">interface 와 type alias</a>
3. <a href="#3">서브 타입과 슈퍼 타입 </a>
4. <a href="#4">타입 추론 이해하기</a>
5. <a href="#5">Type Guard 로 안전함을 파악하기</a>
6. <a href="#6">Class 를 안전하게 만들기</a>

<br/>

<br/>

<h2 id="1">작성자와 사용자</h2>

1. Type System?

```
- 컴파일러에게 사용하는 타입을 명시적으로 지정하는 시스템
- 컴파일러가 자동으로 타입을 추론하는 시스템
```

2. **Typescript의 Type System**

```
- 타입을 명시적으로 지정할 수 있다.
- 타입을 명시적으로 지정하지 않으면, 타입스크립트 컴파일러가 자동으로 타입을 추론한다.
```

3. **타입이란 해당 변수가 할 수 있는 일을 결정한다.**
4. 자바스크립트는 함수 사용법에 대해 오해를 야기한다.

```javascript
function f2(a) {
  return a * 38
}

// 사용자는 사용법을 숙지하지 않은 채, 문자열을 사용하여 함수를 실행했다.
console.log(f2('Mark')) // NaN
```

5. 타입스크립트의 추론에 의지하는 경우

```javascript
function f2(a) {
  return a * 38
}

// 사용자는 a가 any 이기 때문에, 사용법에 맞게 문자열을 사용하여 함수를 실행했다.
console.log(f2('Mark')) // NaN
```

이런 경우, error를 방출하지 않지만 사용자 입장에서 예측하지 못한 결과를 얻는다.

이때! **`noImplicityAny` 옵션을 사용하면**, 타입을 명시적으로 지정하지 않는 경우, **타입스크립트가 추론 중 `any` 라고 판단하게 되면, 컴파일 에러를 발생시켜 명시적으로 지정하도록 유도한다.**

6. `number` 타입으로 추론된 리턴 타입

매개변수의 타입은 명시적으로 지정했다. 명시적으로 지정하지 않은 (아래)함수의 리턴 타입은 `number` | `undefined` 로 추론된다.

```javascript
function f4(a: number) {
  if (a > 0) {
    return a * 38
  }
}
console.log(f(-5) + 5) // NaN
```

사용자는 음수를 넣으면 `undefined` + 5가 실행되어 예측하지 못한 결과를 얻는다. 즉 여기서는 함수내에 return이 되지 않는 것 까지 타이핑이 되어있다(런타임과 컴파일 타임의 타입이 다르네)

이때! **`strictNullChecks` 옵션을** 사용하면, **모든 타입에 자동으로 포함되어 있는 `null` 과 `undefined` 를 제거해준다.** 따라서 위와 같은 코드에서, 타이핑에 따른 연산을 바로 할 수 없게된다.

추가적으로 **`noImplicitReturns` 옵션** 을 켜면, **함수 내에서 모든 코드가 값을 리턴하지 않으면( 위 코드의 경우 `if` 문 아래에서 return이 없음) 컴파일 에러를 발생**시킨다.

<br/>

<h2 id="2">interface 와 type alias</h2>

1. structual type system : 구조가 같으면, 같은 타입이다.
2. nominal type system : 구조가 같아도 이름이 다르면, 다른 타입이다.
3. `function`

```typescript
type EatType = (food: string) => void
interface IEat {
  (food: string): void
}
```

4. `array`

```typescript
type PersonList = string[]
interface IPersonList {
  [index: number]: string
} // indexable 타입이라고 부름
```

5. `intersection`

```typescript
interface ErrorHandling {
  success: boolean
  error?: { message: string }
}

interface ArtistsData {
  artists: { name: string }[]
}

// type alias
type ArtistResponseType = ArtistsData & ErrorHandling
// interface
interface IArtistsResponse extends ArtistsData, ErrorHandling {}

let art: ArtistsResponseType
let iar: IArtistsResponse
```

6. `union types`

```typescript
interface Bird {
  fly(): void
  layEggs(): void
}

interface Fish {
  swim(): void
  layEggs(): void
}

type PetType = Bird | Fish
```

`union type` 으로 만들어진 것에 이름을 붙일 때에는, `type alias` 를 쓴다. 주의할 점은!

`interface IPet extends PetType` 와 같이 `interface`가 상속 받거나, `class Pet implements PetType {}` 처럼 class가 구현하는 것은 허용되지 않는다.

7. `Declaration Merging - interface`

`interface` 에는 같은 이름으로 타입이 선언 되어있으면, 기본적으로 Merge 된다.

```typescript
interface MergingInterface {
  a: string
}
interface MergingInterface {
  b: string
}
let mi: MergingInterface // mi 는 a 와 b 모두 타입으로 갖고있다.
```

**단, 이 merging 기능은 `type alias` 에서는 지원되지 않는다(오류 뱉음)**

8. 강연자님은, 보통 새로운 타입을 만들 때 `interface` 를 `union type` 이이나 `intersection type` 또는 말 그대로 무언가에 '별칭'을 줘야 할 때 `type alias`를 사용하심.

<br/>

<h2 id="3">서브 타입과 슈퍼 타입 </h2>

- case1

```typescript
let sub1: 1 = 1
let sup1: number = sub1
sub1 = sup1 // error
```

sub1은 **리터럴 타입**이다. 여기선 1 말고 다른 어떤 것(2, 3 등등)도 할당 될 수 없다. 1은 number의 부분집합이다.

- case2

```typescript
let sub2: number[] = [1]
let sup2: object = sub2
sub2 = sup2 // error
```

`array`는 `object`의 한 종류이다. 따라서 `sup2 = sub2` 는 가능하다.

- case3

```typescript
let sub3: [number, number] = [1, 2]
let sup3: number[] = sub3
sub3 = sup3 // error
```

sub3은 `number` 타입 원소 2개를 갖는 **튜플**이다. 따라서 `number` 를 원소로 갖는 `array`의 부분집합이다.

- case4

```typescript
let sub5: never = 0 as never
let sup5: number = sub5
sub5 = sup5 // error
```

`never` 타입은 절대로 발생하지 않는 값의 타입을 나타낸다([여기](https://infoscis.github.io/2017/05/14/TypeScript-handbook-basic-types/)를 참고하자.) `never` 는 모든 타입의 서브 타입이며, 모든 타입에 assign 가능하다. 하지만 어떤 타입도 `never`의 서브 타입이 아니고 assign 할 수 없다.

`never` 타입 예시

```typescript
// Function returning never must have unreachable end point
function error(message: string): never {
  throw new Error(message)
}
// Inferred return type is never
function fail() {
  return error('Something failed')
}
// Function returning never must have unreachable end point
function infiniteLoop(): never {
  while (true) {}
}
```

- case5

자식 클래스는 부모 클래스의 서브 타입이다.

```typescript
class Animal {}
class SubDog extends Animal {}
let sub6: SubDog = new SubDog()
let sup6: Animal = sub6
sup6 = sub6 // error
```

### 그래서 법칙은? 같거나 서브 타입인 경우, 할당이 가능하다. => 공변성

- case6

```typescript
let sub8: { a: string; b: number } = { a: '', b: 1 }
let sup8: { a: string | number; b: number } = sub8

let sub9: Array<{ a: string; b: number }> = [{ a: '', b: 1 }]
let sup9: Array<{ a: string | number; b: number }> = sub9
```

**`object` 에서는 각각의 프로퍼티가 대응하는 프로퍼티와 같거나 서브타입이어야 한다.** 여기서는 sub8의 a 프로퍼티가 `string`으로 `string | number`의 서브타입 이기 때문에 assign이 가능하다.

- case7

**함수의 매개변수 타입만 같거나 슈퍼타입인 경우, 할당이 가능하다.(반병)** 서브 타입은 안된다!

```typescript
class Person {}
class Developer extends Person {}
class StartupDeveloper extends Developer {}

function tellme(f: (d: Developer) => Developer) {}

// okay
tellme(function dToD(d: Developer): Developer {
  return new Developer()
})

// okay
tellme(function pToD(d: Person): Developer {
  return new Developer()
})

// so..so..
tellme(function sToD(d: StartupDeveloper): Developer {
  return new Developer()
})
```

3번째 케이스의 경우 오류를 뱉어야 하지만, 컴파일러 옵션 설정을 해주지 않으면 그냥 지나간다. 이때!

**`strictFunctionTypes` 옵션을 켜면** 3번째 케이스는 **sub type을 assign 해서 오류를 뱉는다.**

- any

입력은 마음대로, 함수 구현이 자유롭게 (자유가 항상 좋은건 아니다.) 어떤 값이든 허용하기에 작성자의 실수 가능성이 높다.

- **any 대신 unknown**

입력은 마음대로, 함수 구현은 문제 없도록.

```typescript
function funknown(a: unknown): number | string | void {
  a.toString() // error! Obejct is of type 'unknown'

  if (typeof a === 'number') {
    return a * 38
  } else if (typeof a === 'string') {
    return `Hello ${a}`
  }
}

console.log(funknown(10)) // 380
console.log(funknown('Mark')) // Hello Mark
console.log(funknown(true)) // undefined
```

`a.toString()` 을 하면 일단 `error`를 뱉는다. 하지만 그 밑에 코드처럼 `type`을 분기처리 하여 구현할 수 있다.

<br/>

<h2 id="4">타입 추론 이해하기</h2>

- Let 과 const의 타입 추론 그리고 `as const`

```javascript
let a = 'Mark' // string
const b = 'Mark' // 'Mark' => literal type

let c = 38 // number
const d = 38 // 38 => literal type

let e = false // boolean
const f = false // false => literal type
```

원시형 값을 할당 했을 때 타입을 지정하지 않으면 `let` 은 할당된 값을 기준으로 타입이 지정되고, `const`는 리터럴 타입으로 지정된다.

```typescript
let g = ['Mark', 'Haeun'] // string[]
const h = ['Mark', 'Haeun'] // string[]
```

`let`, `const` 모두 `array` 원소의 공통된 타입을 추론해서(Mark도 `string`, Haeun 도 `string`) 타입을 만들어낸다.

위와 같은 추론이 싫다면?

```typescript
const i = ['Mark', 'Haeun', 'Bokdang'] as const
```

`as const` 타입 assertion을 이용해서 `readonly ['Mark', 'Haeun', 'Bokdang']` 리터럴 타입을 만드는 방법도 있다.

- Best common type (가장 공통적인 타입을 추론해낸다.)

```typescript
let j = [0, 1, null] // (number | null)[]
const k = [0, 1, null] // (number | null)[]
```

각각을 정확하게 타입하고 싶다면 `as const` 이용하자.

```typescript
class Animal {}
class Rhino extends Animal {}
class Elephant extends Animal {}
class Snake extends Animal {}

let l = [new Rhino(), new Elephant(), new Snake()] // (Rhino | Elephant | Snake)[]
const m = [new Rhino(), new Elephant(), new Snake()] // (Rhino | Elephant | Snake)[]
const n = [new Animal(), new Rhino(), new Elephant(), new Snake()] // Animal[]
const o: Animal[] = [new Rhino(), new Elephant(), new Snake()] // Animal[]
```

Class에서도 Best common type 추론이 사용되는데, n 같은 경우 `Animal`이 Best Common Type으로 추론되어 `Animal[]` 로 타이핑된다.

- Contextual Typing - 위치에 따라 추론을 다르게 한다.

```javascript
const click = e => {
  e // any
}
document.addEventListener('click', click)
```

여기서 `e`는 `any` 지만,

```javascript
document.addEventListener('click', e => {
  e // MouseEvent
})
```

여기서는 `MouseEvent`로 추론한다!

<br/>

<h2 id="5">Type Guard 로 안전함을 파악하기</h2>

- `typeof` Type Guard - 보통 Primitive 타입일 경우

```typescript
function getNumber(value: number | string): number {
  value // number | string
  if (typeof value === 'number') {
    value // number
    return value
  }
  value // string
  return -1
}
```

`if` 문 안의 `return value`에 의해서(+ Type Guard에 의해서) `number` 타입이 떨어져 나간다. 따라서 아래에서는 `string` 타입만 남는다. 만약 `return`을 하지 않으면 아래에서 당연히(?) `number, string` 둘 다 남겠지..

- `instanceof` Type Guard - 보통 Error 객체 구분에 많이 쓰인다.

```typescript
class NegativeNumberError extends Error {}

function getNumber(value: number): number | NegativeNumberError {
  if (value < 0) return new NegativeNumberError()
  return value
}

function main() {
  const num = getNumber(-10)
  if (num instanceof NegativeNumberError) {
    return
  }
  num // number
}
```

`main` 함수 내에 `num` 을 보면, 런타임 과정에서는 `num` 이 Error 객체인걸 알 수 있지만, **컴파일 단계** 에서는 알 수 없다. 따라서 `instanceof` 를 통해 type guard를 했다.

- `in` operator Type Guard - Object의 프로퍼티 유무로 처리하는 경우

```typescript
interface Admin {
  id: string
  role: string
}

interface User {
  id: string
  email: string
}

function redirect(user: Admin | User) {
  if ('role' in user) {
    routeToAdminPage(user.role)
  } else {
    routeToHomePage(user.email)
  }
}
```

- Literal Type Guard - Object의 프로퍼티가 같고, 타입이 다른 경우

```typescript
interface IMaching {
  type: string
}

class Car implements IMachine {
  type: 'CAR'
  wheel: number
}

class Boat implements IMachine {
  type: 'BOAT'
  motor: number
}

function getWhellOrMotor(machine: Car | Boat): number {
  if (machine.type === 'CAR') {
    return machine.wheel
  }
  return machine.motor
}
```

`Redux`의 `reducer`에서 자주 사용되는 기법

- Custom Type Guard - 앞에 소개 된 Type Guard가 먹히지 않을 때!

직접 Type Guard를 만드는 방식이다.

```typescript
function getWheelOrMotor(machine: any): number {
  if (isCar(machine)) {
    return machine.wheel
  } else if (isBoat(machine)) {
    return machine.motor
  }
  return -1
}

function isCar(arg: any): arg is Car {
  return arg.type === 'CAR'
}

function isBoat(arg: any): arg is Boat {
  return arg.type === 'BOAT'
}
```

`isCar` 함수 내부에서, `arg is Car`의 의미는 함수가 return 하는 값이 `true`라면 `arg`의 type은 Car로 타이핑 된다.

이를 `type predicate`이라고 한다. [여기](https://www.typescriptlang.org/docs/handbook/advanced-types.html#user-defined-type-guards) 참고하자!

<h2 id="6">Class 를 안전하게 만들기</h2>

**v3.9.7**

```typescript
class Square2 {
  area // error! implicit any
  sideLength // error! implicit any
}
```

`noImplicitAny` 옵션에 의해 타입을 지정하지 않으면 error를 발생

```typescript
class Square2 {
  area: number
  sideLength: number
}

const squre = new Square2()
console.log(squre.area)
```

`console.log` 에서 `squre.area`는 컴파일 단계에서는 `number`인데 런타임 단계에서는 `undefined`이다. 따라서 에러를 발생

이를 방지하기 위해 **strictPropertyInitialization** 옵션을 켜면 **`class`의 프로퍼티가 생성자 혹은 선언에서 값이 지정되지 않으면, 컴파일 에러를 발생시켜 주의를 준다.**

<br/>

**v4.0.2**

```typescript
class Square {
  area
  sideLength

  constructor(sideLength: number) {
    this.sideLength = sideLength
    this.area = sideLength ** 2
  }
}
```

4버전 부터는 클래스 내부의 프로퍼티의 선언부에 타입을 지정하지 않아도, any로 평가되지 않고 생성자에 의해 추론된다.

이건 다른 예시,

```typescript
class Square {
  sideLength;

  constructor(sideLength: number) {
    if (/* conditional */) {
      this.sideLength = sideLength;
    }
  }

  get area() {
    return this.sideLength ** 2;
  }
}
```

위 코드에서 `sideLength`는 생성자에서 `number` 또는 `undefined`로 추론된다(if 문에 안들어 올 수 있으니) 따라서 `getter` 부분에서 에러를 발생시킨다.( 컴파일 단계에서 알려주는 듯? `error! Object is possibly undefined`)

그리고 한계가 있음. **여전히 생성자를 벗어나면 추론되지 않는다.**

```typescript
class Square {
  sideLength // !로 의도를 표현

  constructor(sideLength: number) {
    this.initialize(sideLength)
  }
  initialize(sideLength: number) {
    this.sideLength = sideLength
  }

  get area() {
    return this.sideLength ** 2
  }
}
```

생성자를 벗어나면 추론이 안되기 때문에 위 코드에서는 `!`로 주의를 나타냈다. 여기서의 의도는 '생성자에서 초기화는 안되지만, 어딘가에서 초기화가 될거야'이다.
