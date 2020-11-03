---
title: Woowahan Typescript 세미나 정리(진행중)
date: 2020-11-03 23:11:99
category: typescript
---

## Conditional Type 을 활용하기

```typescript
interface StringContainer {
  value: string
  format(): string
  split(): string[]
}

interface NumberContainer {
  value: number
  nearestPrime: number
  round(): number
}

type Item<T> = {
  id: T
  container: any // 이러지 말자.
}
```

목표는 T가 `string`이면 StringContainer, `number` 면 NumberContainer를 사용

첫번째 방법,

```typescript
type Item<T> = {
  id: T
  container: T extends string ? StringContainer : NumberContainer
}

const item: Item<string> = {
  id: 'abc',
  container: null, // Type 'null' is not assignable to type StringContainer
}
```

`extends`를 조건부 타입에서 사용한다면 'T 가 `string` 이면' 와 같이 이해하면 좋다.

> 핸드북에 의하면, `T extends K ? A : B` 는 T가 K에 서브타입이면 A 아니면 B라고 설명한다.
>
> 그 밖에도 `extneds` 는 **제약사항** 을 주기 위해 자주 사용된다. 예시로
>
> `type MyReturnType<T extneds (...args: any) => any> = ...` 에서 `MyReturnType` 은 Generic으로 함수 타입만 받는다고 제약을 걸었다.

더 타입을 자세하게 준다면!

```typescript
type Item<T> = {
  id: T extends string | number ? T : never
  container: T extends string
    ? StringContainer
    : T extends number
    ? NumberContainer
    : never
}

const item: Item<boolean> = {
  id: true, // Type boolean is not assignable to type 'never',
  container: null, // Type 'null' is not assignable to type 'never'
}
```

위와 같이 `never`를 이용해서 `string`도 `number` 도 아니면 사용 불가능하게 구현할 수 있다.

> `never`는 모든 타입의 sub type 이지만, 어떤 타입에도 할당 될 수 없다.

**never의 또다른 이용 ArrayFilter<T> 만들기**

```typescript
type ArrayFilter<T> = T extends any[] ? T : never
type StringsOrNumbers = ArrayFilter<string | number | string[] | number[]>
```

여기서 `T extends any[]` 의 의미는 'T가 배열이면'과 같다. `StringsOrNumbers` 타입은 아래와 같은 과정으로 컴파일된다.

1. string | number | string[] | number[]
2. never | never | string[] | number[]
3. string[] | number[]

**Generic을 이용해서 제약사항 걸기 (마치 never 처럼)**

```typescript
interface World {
  getItem<T extends string | number>(id: T): T extends string ? Table : Dino
}
const what = world.getItem(true) // error!
```

T는 `string` | `number` 인데 boolean을 넣었으므로 에러 발생.

**Flatten<T> 구현하기**

```typescript
type Flatten<T> = T extends any[]
  ? T[number]
  : T extends Object
  ? T[keyof T]
  : T // primitive형
```

우선 Array의 경우

```typescript
const array = [1, 2, 'hi']
type ArrayFlattened = Flatten<typeof array>
```

이 부분은 질문 해야하는 부분인데 array의 타입이 [number]와 함께 `(number | string)[][number]`로 추론되어 array의 각 인덱스를 참조한 값 즉 배열의 각 원소의 타입 `number | string` 으로 추론된다.

Object인 경우

```typescript
const person = { name: 'Mark', age: 38 }
type ObjectFlattened = Flatten<typeof person>
```

`keyof`는 Object의 프로퍼티를 열거한다. 즉 위 코드에서는 `name | age` 이다.

```
keyof T => 'name' | 'age'
T['name' | 'age'] => T['name'] | T['age'] => string | number
```

**infer**

단어의 의미와 비슷하게 `infer` 를 이용하면 타입을 추론할 수 있다.

```typescript
type UnpackPromise<T> = T extends Promise<infer K>[] ? K : any
const promises = [Promise.resolve('Mark'), Promise.resolve(38)]
```

`UnpackPromise` 는 Generic으로 받은 타입을 `infer` 로 추론하여 T가 Promise의 Array라면 추론한 K로 타이핑하고 아니면 any로 타이핑한다. 위에서 `promisese` 의 타입은 `(Promise<string> | Promise<number> )[]` 이다.

```typescript
type Expected = UnpackPromise<typeof promises>
```

`promises` 를 unpack 하게 되면, `infer`에 의해 `string` 과 `number` 가 추론되고 K는 `string | number` 가 된다.

**함수의 리턴 타입 알아내기 - MyReturnType**

```typescript
function plus(seed: number): number {
  return seed + 1
}
```

이 함수의 Return 타입은 딱 봐도 `number` 지만 어떤 함수든 리턴타입을 알아낼 수 있는 `MyReturnType` 타입을 만들어 보자.

```typescript
type MyReturnType<T extends (...args: any) => any> = T extends (
  ...args: any
) => infer R
  ? R
  : any
```

`MyReturnType` 은 Generic으로 함수만 받겠다고 제약사항을 걸었다. 그리고 함수를 받아서 `infer` 키워드를 통해 리턴값을 추론했다.

### Helper Conditional Type 만들어보기 ( 이미 있는것들임 ㅎㅎ )

**Excluded( diff )**

```typescript
type Excluded<T, U> = T extends U ? never : T
type A = Excluded<string | number, number>
```

Excluded 는 T, U를 받아서 T가 U의 Sub Type 이라면 `never` 로 배제시킨다. (위에 ArrayFilter 만들 때 같은 논리) 따라서 A는 `string` 이다.

추가적으로, 이 코드에서 T는 유니온 타입을 받는다. **타입스크립트 컴파일러는 T가 유니온 타입이면 `extends` 키워드를 연산할 때 유니온 타입 각각에 대해서 연산한다. 즉 `string extends U, number extends U` 를 계산한다!**

실제로 사용할 땐 `Exclude<T, U>` 를 사용하면 된다.

**Extracted( filter )**

```typescript
type Extracted<T, U> = T extends U ? T : never
type B = Extracted<string | number, number> // number
```

Exclude와 반대로 U에 맞지 않으면 타입을 filter 한다. 실제로 사용할 땐 `Extract<T, U>`를 사용하면 된다.

**Pick**

```typescript
type Pick<T extends Object, U extends keyof T> = { [P in U]: T[P] }
```

내가 원하는 Key를 갖는 Object의 타입을 알고 싶을 때 사용한다. 이 역시 이미 있는 헬퍼라서 그냥 `Pick<T, U>` 로 쓰면 된다.

**Omit**

원하지 않는 Key를 뺀 타입을 얻고 싶을 때 사용한다.

```typescript
type Omit<T extends Object, U extends keyof T> = {
  [P in Exclude<keyof T, U>]: T[P]
}
```

`Exclude` 내장 헬퍼를 사용해서 U가 아닌 T의 key들만 P로 받아서 구하는 방법이다. 위에서 만들었던 `Pick` 을 활용하면 좀 더 간단해진다.

```typescript
type Omit<T extends Object, U extends keyof T> = Pick<T, Exclude<keyof T, U>>
```

이 역시 이미 있는 헬퍼라서 그냥 `Omit<T, U>` 로 쓰면 된다.

**NonNullable**

타입에 있는 `null` 이나 `undefined` 를 제거해준다.

```typescript
type NonNullable<T> = T extends null | undefined ? never : T
```

**Parameters**

함수의 파라미터 타입을 튜플 형태로 반환한다.

```typescript
type Parameters<(...args: any) => any> =
		 T extends (...args: infer P) => any ? P : never
```

Generic의 제약사항에 함수만 받게 걸어놓고 `infer` 를 이용해서 파라미터를 추론했다.
