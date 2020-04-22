---
title: Array.prototype.sort
date: 2020-04-17 11:04:46
category: javascript
---

<br/>

### 1. 정의

 `sort()` 메소드는 배열의 요소를 정렬 할 때 사용. 기본 정렬 순서는 **유니코드 코드 포인트** 를 기준으로 한다.

```javascript
const alpha = ['B', 'D', 'A', 'C' ]
alpha.sort()
console.log(alpha) // (4) ["A", "B", "C", "D"]
```

유니코드를 기준으로 정렬하기 때문에 숫자 배열에서 주의해야 한다.

```javascript
const nums = [1, 10, 9, 3, 8]
nums.sort()
console.log(nums) // (4) [1, 10, 3, 8, 9]
```

정렬되는 과정에서 숫자는 문자열로 변환되기 때문에 10은 3보다 유니코드 기준으로 앞에 있다. 따라서 배열의 앞쪽 원소에 배치된다.

**참고로 `sort()` 함수는 원본배열을 수정한다. 복사본이 `return`  되는게 아니다!!**

<br/>

`sort()` 메소드는 인자로 함수의 리턴값에 따라서 정렬을 다르게 한다.

만약, 인자로 받는 함수 `sortFunction(a, b)` 리턴값이 z 라고 한다면

1. z 가 0보다 작다면 a를 b보다 낮은 색인으로 정렬한다.
2. z가 0보다 크다면 b를 a보다 낮은 색인으로 정렬한다.
3. z가 0이면 a,b를 서로에 대해 변경하지 않고 다른 모든 요소에 대해 정렬한다.

이 결과를 토대로 오름차순, 내림차순 구현이 가능하다.

### 2. 활용

#### 오름차순

```javascript
const sortFunction = (a,b) => a - b 
const english = [ "Google", "Stackoverflow", "Yahoo!", "Ask", "Bing"]
english.sort(sortfunction)
console.log(english) 
// (5) ["Ask", "Bing", "Google", "Stackoverflow", "Yahoo!"]
```

#### 내림차순

```javascript
const sortFunction = (a,b) => b - a 
const nums = [1, 10, 9, 3, 8]
nums.sort()
console.log(nums)
// (5) [10, 9, 8, 3, 1]
```

#### Object Array 정렬

key A를 오름차순으로 정렬 하는데 만약 같다면 key B를 기준으로 내림차순으로 정렬 해보자.

```javascript
const objArray = [{A: 10, B: 5}, {A: 8, B: 9}, {A: 3, B: 4}, {A: 3, B:3}, {A: 8, B: 10}]
const sortFunction = (a,b) => {
     if (a.A === b.A) return b.B - a.B
     else return a.A - b.A
}
objArray.sort(sortFunction)
```

결과는

```javascript
0: {A: 3, B: 4}
1: {A: 3, B: 3}
2: {A: 8, B: 10}
3: {A: 8, B: 9}
4: {A: 10, B: 5}
```

잘 정렬 됐다.. ㅎㅎ

</br>

### 3. Reference

- https://tonks.tistory.com/124
- https://developer.mozilla.org/ko/docs/Web/JavaScript/Reference/Global_Objects/Array/sort

