---
title: LinkedList
date: 2019-12-26 03:12:98
category: cs
---

> # Linked List



## 1. 설명

linked list는 노드들이 링크된 data structure 따라서 기본적으로 노드들이 정의된 data structure를 정의 해줘야 한다.

```python
class Node:
  def __init__(self, item):
    self.data = item
    self.next = None
```

Node 클래스는 data, next 2개의 멤버를 갖는다. data 멤버는 받은 item을 저장하고 next는 다음 노드를 가리키는 역할을 갖는다.



## 2. 구현

- ### add

```python
def add(self, item):
  cur = self.head
  while cur.next is not None:
    cur = cur.next
  cur.next = Node(item)
```

cur.next가 None 일 때 까지 head부터 시작하여 모든 노드를 순회 한다. cur.next가 None이면 새로운 노드 객체를 만들어서 연결 시킨다.

- ### print

```python
def print_node(self):
  cur = self.head
  while cur is not None :
    print(cur.data)
    cur = cur.next
```

- ### remove  

Head를 지우는 Case, 중간에 있는 Node를 지우는 Case로 나뉜다.

```python
def remove(self, item):
  if self.head.data == item: # Head 지우는 Case
    self.head  = self.head.next 
  else: # 중간에 있는 Node를 지우는 Case
    cur = self.head
    while cur.next is not None:
      if cur.next.data == item:
        nextNode = cur.next.next
        cur.next = nextNode
        return
      cur = cur.next
    print('Item does not exist in linked list!')
```



- ### reverse

```python
def reverse(self):
  prev = None
  cur = self.head
  while cur is not None:
    next = cur.next
    cur.next = prev
    prev = cur
    cur = next
  self.head = prev
```



- ### 전체 코드

```python
class Node:
  def __init__(self, item):
    self.data = item
    self.next = None
    
class LinkedList:
  def __init__(self, item):
    self.head = Node(item)
    
  def add(item):
    cur = self.head
    while cur.next is not None:
      cur = cur.next
    cur.next = Node(item)
  
  def remove(self, item):
    if self.head.data == item:
      self.head = self.head.next
    else:
      cur = self.head
      while cur.next is not None:
        if cur.next.data == item:
          nextNode = cur.next.next
          cur.next = nextNode
          return
        cur = cur.next
    print('Item does not exist in linked list!')
  
  def reverse(self):
    prev = None
    cur = self.head
    while cur is not None:
      next = cur.next
      cur.next = prev
      prev = cur
      cur = next
    self.head = prev
      
```

