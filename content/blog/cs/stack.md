---
title: Stack
date: 2019-12-25 17:12:93
category: cs
---

> # Stack



## 1. 설명

- 입력과 출력이 한 방향으로 제한된 자료구조
- **가장 나중에 들어온 것이 가장 나중에 나옴 ( LIFO )**

- stack과 queue에는 search가 없다.
- <code>stack overflow</code> 는 스택이 꽉차서 더이상 데이터를 받지 못하는 상태
- <code>stack underflow</code>는 스택이 비어서 꺼낼 데이터가 없는 상태

## 2. 언제 사용할까?

- 브라우저의 back button
- 함수의 콜 스택
- 문자열 역순 출력
- 연산자 후위 표기법 등등..



## 3. 인터페이스

- push - 데이터를 맨위에 넣음

- pop - 맨위 값을 리턴하고 동시에 삭제

- is_empty - 스택이 비었는지 확인

- peek - 맨위 값을 리턴하고 삭제는 하지 않음



## 4. 구현



### 1) 리스트를 이용한 방법

```python
class Stack:
  def __init__(self):
    self.items = []
    self.max = 5
    
  def push(self, item):
    if self.items.len < 5:
      self.items.append(item)
    else:
      print('abort push in order to prevent stack overflow')
      
  def pop(self):
    if not self.items:
      self.items.pop()
    else:
      print('stack is empty. abort pop to prevent stack underflow')
      
  def is_empty(self):
    return not self.items
  
  def peek(self):
    return self.items[len(self.items)-1]
```



### 2. 노드를 이용한 방법

```python
class Node:
  def __init__(self, item):
    self.data = item
    self.next = None

class Stack:
  def __init__(self):
    self.head = None
    
  def is_empty(self):
    return not self.head
  
  def push(self, item):
    node_data = Node(item)
    node_data.next = self.head
    self.head = node_data
 
  def pop(self):
    if self.is_empty():
      return 
    ret_data = self.head.data
    self.head = self.head.next
    return ret_data
 
  def peek(self):
    if self.is_empty():
      return
    return self.head.data
  
```

