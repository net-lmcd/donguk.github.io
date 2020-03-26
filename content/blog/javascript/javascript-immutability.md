---
title: javascript immutability
date: 2020-03-26 14:03:95
category: javascript
---

_Egoing Lee 님의 inflearn - Javascript Immutability 강의를 듣고 정리_

<br/>

### 1 . Immutability

데이터의 원본을 훼손 하고싶지 않음!!!!!!!!!!!!!!!!!!!!!!!!!

<br/>

### 2.  이름에 대한 불변함 `var` vs `const`

```javascript
var v = 1;
v = 2;
console.log('v: ', v);

const c = 1;
c = 2 // TypeError Assignment to constant to variable
```

<br/>

### 3.  JS data type

| Primitive |  Object  |
| :-------: | :------: |
|  Number   |  Object  |
|  String   |  Array   |
|  Boolean  | Function |
|   Null    |          |
| Undefined |          |
|  Symbol   |          |

`Primitive` : 원자 데이터 타입 -> 더 이상 쪼갤 수 없음.

`Object` : 서로 연관된 정보를 정리정돈(?) 하는데 주로 사용.

<br/>

### 4. 초기 값의 비교

![image](https://user-images.githubusercontent.com/39187116/77556927-73b0c500-6efc-11ea-95fb-bf078be111a2.png)

- 동등연산자 > 참 > **같은 값을 가리킨다.** 라는 뜻

<br/>

### 5. 객체의 가변성

![image](https://user-images.githubusercontent.com/39187116/77557613-4d3f5980-6efd-11ea-9a94-27184c6a6b21.png)

- 원자 데이터 타입 -> 값이 같을 때는 같은 곳을 가리키지만 달라지면 다른 곳을 가리킨다.
- Object 타입 -> 값을 수정하면 원본도 수정 -> 해결 방법은..?

#### Object.assign()

- 객체의 property를 복제한다. 
- 배열에는 사용하지 말 ( 배열이 갖고 있는 특수한 기능들이 사라짐 )
- 배열에는 concat / slice / Array.from

```javascript
var o2 = Obejct = Object.assign({}, o1)
```

o2 는 새로운 메모리를 참조한다. (원본 데이터의 불변함을 유지 할 수 있다.)

만약 객체의 property에 배열이 있다면, 배열의 주소가 복사된다...따라서 오류가 발생하지 않게 추가적으로 concat 같은 메소드를 사용해줘야 한다.

1.

![image](https://user-images.githubusercontent.com/39187116/77559996-32baaf80-6f00-11ea-8a92-270ef2ff4e01.png)

2.

![image](https://user-images.githubusercontent.com/39187116/77559949-233b6680-6f00-11ea-8b1a-a4f4f4d221f9.png)



#### 함수에서 사용하기..

```javascript
function fn(person){
  Object.assign({}, person);
  person.name = 'lee';
  return person
}
var o1 = {name: 'kim'}
var o2 = fn(o1); // 여기에서 fn(Object.assign({}, person)) 가능
console.log(o1, o2) // {name: 'kim'}, {name: 'lee'}
```

<br/>

### 6. 가변과 불변 API 비교

```javascript
var score = [1,2,3];
score.push(4);
console.log(score); // [1,2,3,4]

var score2 = [1,2,3];
var score3 = score2.concat(4); 
console.log(score2) // [1,2,3]
```

`concat` : 원본을 복제하여 리턴 > 원본을 immutable 하게 유지

<br/>

### 7. Object.freeze

객체를 불변하게 만들어주는 함수 ( 누구도 수정 불가능,, )

```javascript
var o1 = {name: 'kim', score: [1,2]}
Object.freeze(o1);
o1.name = 'lee'
console.log(o1) // {name: 'kim', score: [1,2]}
```

o1은 완벽하게 immutable 하게 됨.

하지만 nested object에서 문제가 생긴다..

```javascript
o1.score.push(3)
console.log(o1.score) // [1, 2, 3]
```

이것을 방지하기 위해서는 property의 객체에도 Object.freeze를 해줘야 한다.

```javascript
Object.freeze(o1.score)
o1.score.push(3) // Error!!
```



### 8. const vs Object.freeze()

![image](https://user-images.githubusercontent.com/39187116/77611752-857c8180-6f69-11ea-834c-0fdf22f86137.png)

`const`는 이름이 가리키는 값 대상을 못 바꾸게 한다. => **이름을 규제한다**

`Object.freeze` 는 값 자체를 못 바꾸게 한다 ( Property를 못 바꾸게 한다.) => **값을 규제한다.**
