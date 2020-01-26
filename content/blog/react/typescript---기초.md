---
title: TypeScript - 기초
date: 2020-01-15 19:01:22
category: react
---
# TypeScript 기초 연습

> [TypeScript HandBook(한글)](https://typescript-kr.github.io/) 참조
>
> [Velopert님 블로그](https://velog.io/@velopert/typescript-basics) 참조



### 1. 타입스크립트 설정파일 생성

```bash
$ yarn init -y 
```

프로젝트 폴더에서 yarn init을 통해 package.json 을 생성 해준다. y 옵션은 그냥 yarn init 했을 경우 나오는 질문들을 yes 하는 옵션

```bash
$ yarn global add typescript
$ tsc --init
```

typescript를 글로벌로 설치 후 <code>tsc --init</code> 으로 타입스크립트 설정파일 tsconfig.json을 생성한다.

```json
//tsconfig.json
{
  "compilerOptions": {
    "target": "es5", 
    "module": "commonjs",
    "strict": true,
    "esModuleInterop": true,
    "forceConsistentCasingInFileNames": false
  }
}
```

- <code>target</code> : 컴파일된 코드를 어떤 환경에서 실행할 지 정해준다. 예를들어 화살표 함수를 사용하고 es5로 설정한다면 function키워드로 바꿔준다. es6로 하면 화살표 함수를 유지한다.
- <code>module</code> : 컴파일된 코드가 어떤 모듈 시스템을 적용할 지 정해준다. export default target 으로 코드를 작성하고 commonjs로 설정 하면 module.export = target 으로 변경 해준다. es2015로 한다면 export default가 유지 된다.
- <code>strict</code>: 모든 타입 체킹 옵션을 활성화 해준다는 의미.
- <code>esModuleInterop</code> : commonjs 모듈 형태로 이루어진 파일을 es2015 모듈 형태로 불러 올 수 있게 해준다.
- <code>forceConsistentCasingInFileNames</code> : 파일명에 대소문자 구분하지 않아도 되는지 여부 체크

```json
//tsconfig.json
{
  ...
  "outDIr" : "/dist"
}
```

컴파일된 코드를 확인하기 위해 출력 저장소 옵션을 추가 해준다.

<br/>

### 타입스크립트 파일 생성

```typescript
//test.ts
const test: string = "this variable must be string"
```

- 타입스크립트 파일의 확장자는 ts 이다.
- 타입의 선언은 <code>const 변수:type = value</code> 와 같은 형식이다.
- test 변수에 string 타입이 아닌 다른 타입의 값이 들어가면 오류가 뜬다.

### 타입스크립트 컴파일

```bash
$ yarn add typescript
```

컴파일을 위해 프로젝트 내에 typescript 패키지를 설치한다. ( 굳이 로컬로 설치하지 않아도 됌. )

```json
//package.json
...
"scripts":{
  "build" : "tsc"
}
```

타입스크립트의 컴파일 CLI 명령어는 <code>tsc</code> 이다.  추후 빌드를 위해 package.json에 build 스크립트로 tsc를 추가 해줬다.

```javascript
// dist/test.js
"use strict";
var test = "this variable must be string";
```

컴파일을 하면 아까 tsconfig.json에 지정한 옵션에 의해 dist 폴더 내부에 js파일이 생성된다.

<br/>

### 기본타입

> [기본타입 자세히 알아보기](https://typescript-kr.github.io/pages/Basic%20Types.html)

```typescript
const test: string = "this variable must be string type" // 문자열
console.log('message',test)

let count: number = 1
count +=1 // 숫자

let isTrue: boolean = true // boolean

let numbers: number[] = [1,2,3,4,5] // 숫자배열

let messages: string[] = ['1','2','3'] // 문자열 배열

messages.push(1)//숫자 넣으려고 하면 안돼

let mightBeUndefined: undefined | number  = undefined // 숫자 or undefined

let nullableNumber: null | number = 3 // 숫자 or null

let color: 'green' | 'orange' | 'blue' // 셋 중 하나
color = 'blue'
color = 'red' // error
```

타입이 잘못 된 상태에서 컴파일을 하려고하면 에러 때문에 컴파일이 되지 않는다.( Good.. )

### 함수에서 타입

> [함수타입 자세히 알아보기](https://typescript-kr.github.io/pages/Functions.html)

```typescript
function testFunc(x: number, y:number): number{
  return x+y
}
```

변수의 선언과 비슷하게 파라미터들의 타입을 지정 한다. 중괄호 옆에 <code>: number</code>는 함수 리턴값의 타입을 지정한다. 만약에 위와 같은 코드에서 리턴 값이 null 이면 오류가 발생한다.

```typescript
function noReturn(): void{
  console.log('hi void')
}
```

만약 함수에서 아무것도 반환하지 않는다면 리턴 타입을 void로 지정해 주면 된다.

<br/>

### interface

<code>interface</code>는 객체나 클래스의 타입을 지정할 때 사용한다.

#### 1. Class

- 클래스에서 interface를 implements 하는 것은 클래스가 특정 interface의 요구사항을 구현하는 것을 명시한다.

```typescript
interface Shape {
    getArea(): number
  // Shape 인터페이스에는 getArea 함수가 꼭 있어야 하며 이 함수의 리턴값은 number이다.
}

class Circle implements Shape{
    radius:number
    constructor(radius: number){
        this.radius = radius
    }
    getArea(){
        return this.radius * this.radius * Math.PI
    }
}

class Rectangle implements Shape{
    width: number
    height: number
    constructor(width: number, height: number){
        this.width = width
        this.height = height
    }
    getArea(){
        return this.width * this.height
    }
}

const shapes: Shape[] = [new Circle(3), new Rectangle(2,3)]
//shapes를 인터페이스[] 타입으로 지정
shapes.forEach( shape => {
    console.log(shape.getArea())
})
```

**interface를 implements 하여 구현한 클래스를 통해 생성된 객체는 타입을 해당 interface로 지정해 줄 수 있다.** 그리고 

타입스크립트 에서는 <code>public, priavte accessor</code>를 이용하면 클래스 내부 멤버변수를 선언할 때 위와 같이 하나하나 지정해 주지 않아도 된다. 

```typescript
interface Shape{
  getArea(): number
}
class Circle{
  constructor(public radius: number){
    this.radius = radius
  }
  getArea(){
    return this.radius * this.radius * Math.PI
  }
}
class Rectangle{
  constructor(private width: number, private height: number){
    this.width = width
    this.height = height
  }
  getArea(){
    return this.width * this.height
  }
}
```

하지만 멤버변수를 private로 설정하게 되면 클래스 외부에서 멤버변수 조회가 불가능하다!

```js
const circle = new Circle(2)
const rectangle = new Rectangle(2,3)
console.log(circle.radius) // 2
console.log(rectangle.width) //error
```

private 멤버변수를 조회 하려고 하면 컴파일 단계에서 오류가 발생한다. 



#### 2. 일반 객체

```typescript
interface Person {
    name: string
    age?: number
}

interface Developer extends Person{
    skills: string[]
}

const donguk: Person = {
    name :'donguk',
    age : 27
}

const velopert: Developer = {
    name : 'velopert',
    age : 30,
    skills : ['React', 'Javascript']
}
```

Developer interface는 Person interface를 상속 받았다. Person interface의 age옆 ? 는 설정을 해도 안해도 된다는 의미이다.

<br/>

### Type Alias 

**<code>type</code>은 특정 타입에 별칭을 붙이는 용도로 사용한다.** 이를 사용하여 객체를 위한 타입을 설정 할 수 있고, 배열, 또는 어떤 타입이든 별칭을 지어 줄 수 있다.

```typescript
type Person2 = {
    name: string
    age?: number
}

type Developer2 = Person2 & {
    skills: string[]
}

const person: Person2 = {
    name: '배민',
}

const developer: Developer2 = {
    name: '김배민',
    skills: ['react', 'js']
}

type People = Person2[] // Person2[] 을 People 타입으로 사용 가능.
const people: People = [person, developer]

type Color = 'yellow' | 'red' | 'orange'
const c: Color = 'yellow'

const colors: Color[] = ['red', 'orange']

```

<code>&</code> 는 두개 이상의 타입을 합칠 때 사용한다.  Type alias 를 사용할 때

```typescript
type People = Person2[] // Person2[] 을 People 타입으로 사용 가능.
```

위와같은 선언도 가능하다.

**type alias와 interface 중 무엇을 사용하든 일관성 있게만 사용하면 된다. 단 외부 라이브러리의 타입을 지정 해야 할 때에는 interface를 사용하자.**



### Generic



#### 1. Generic 이란,

제네릭(Generics)은 타입스크립트에서 함수, 클래스, `interface`, `type`을 사용하게 될 때 여러 종류의 타입에 대하여 호환을 맞춰야 하는 상황에서 사용하는 문법이다. `any type`을 사용 할 수 있지만 이러면 타입이 깨진거나 다름이 없다.

```typescript
function sampleGeneric<A,B>(a: A, b: B): A & B {
    return{...a,...b}
}
const sampleResult = sampleGeneric({foo:1}, {bar : 2})
```

Generic은 위와 같이 `<A>` 꺽쇠 안에 타입을 지정하여 사용한다. 이렇게 하면 타입이 깨지지 않게 되고 어떤 타입이든 활용 할 수 있게된다. 그리고 Generic의 타입은 실제 함수의 파라미터로 주입한 값의 타입을 따라간다.



#### 2. Interface + Generic

```typescript
interface Items<T>{
  list: T[]
}
const items: Items<string> = { list : ['a','b','c']}
```

만약 `Items<string>` 타입을 갖는다면 이 타입을 가지는 list라는 배열은 `string[]` 타입을 갖게 된다. 만약 `Items<number>` 이라면 list는 `number[]` 타입을 갖게된다. 이와 같이 Generic `<T>`의 타입으로 number나 다른 어떤 타입을 전달 하여 하나의 인터페이스를 재사용 할 수 있다. 

#### 3. type alias + Generic

```typescript
type Items2<T> = {
    list: T[]
}
const items2: Items2<number> = {list : [1,2,3,4]}
```

Interface + Generic 구조와 같다.

#### 4. Class + Generic

```typescript
class Queue<T>{
    list: T[] = []
    enqueue(item: T){
       this.list.push(item)
    }
    dequeue(){
        return this.list.shift()
    }
}
```

Generic + Class를 이용하여 구현한 자료구조 Queue이다.


