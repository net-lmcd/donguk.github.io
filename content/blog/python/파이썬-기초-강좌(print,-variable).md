---
title: 파이썬 기초 강좌(print, variable)
date: 2019-11-09 21:11:77
category: python
---

## print

```python
print() # 개행처리
print('hi','my') # hi my
print('hi','my', sep='-') # hi-my, join() 느낌
print('hi',end=' ') 
print('my') # hi my, 개행을 '값'으로 합쳐줌

# d(digit) : 정수, s(string), f(float)
print('%s %s' % ('one', 'two')) # one two
print('{} {}'.format('one','two')) # one two, {}는 뭐가 들어올거라~ 생각하면 된다.
print('{1} {0}'.format('one','two')) # two one, {}가 비어있으면 0이라고 생각 >, index 개념

print('%10s', 'nice') # '      nice', 10자리 string
print('{:>10}'.format('nice')) #'      nice', 왼쪽에 10자리 할당 위랑 같음.
print('{:10}'.format('nice')) #'nice.     ' default는 왼쪽
print('{:10d}'.format(422222222)) # 문자가 아니라면 형 붙여줘야해
print('{:^10}'.format('nice')) #'   nice.  ', 중앙정렬은 ^
...
```



## variables

- <code>type(variable)</code> : variable의 자료형 보여주는 함수
- <code>x=y=z=100</code> : 동시선언 가능

```python
#Object references
print(300) # 300
```

- 위 print구문이 내부적으로 300이라는 값을 출력하는 과정은 300 에 맞는 **Object(<code><class 'int'></code>)를 생성** 하고 **값을 생성하고(<code>int(300)</code>)** 이 값을 출력 한다. 그리고 변수 생성도 이와 같은 절차를 거친다.

- **<code>id(identity)</code>** : 객체에 할당된 고유값 확인

```python
m = 800
n = 600 
print(id(m)) # 4561360016
print(id(n)) # 4561360048
print(id(m) == id(n)) # False

m = 800
n = 800 
print(id(m)) # 4561360016
print(id(n)) # 4561360016
print(id(m) == id(n)) # True
```

두번째 예제의 경우,  **변수에 똑같은 값을 할당 해주면 객체는 하나만 존재하게 된다 즉, 보기에는 2개의 변수를 선언한 것 같지만 사실 하나의 변수만 선언한 것이다.**  (같은 Object를 참조한다는 말. 파이썬 엔진이 내부적으로 처리)

- 다양한 변수 선언
  - Camel Case : 소문자로 시작, 다음 단아에서 대문자 (<code>exampleCase</code>), 주로 Method 선언할 때 사용
  - Pascal Case : 대문자로 시작, 다음 단어에서도 대문자(<code>ExampleCase</code>), 주로 Class 선언할 때 사용
  - Snake Case : 전부 소문자로 시작, 이어지는 단어를 _ 로 붙여줌 ( <code>example_case</code>), 주로 variable 선언할 때 사용( 파이썬에서 많이 사용)
  - 첫글자가 숫자나 특수문자 사용시 변수 선언 불가능. (단, 특수문자에서 _ 와 $ 는 가능)
  - 예약어는 변수로 선언 불가능 ( 예를들어 for문의 for는 변수명으로 선언 불가능 )