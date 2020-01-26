---
title: Queue
date: 2019-12-24 20:12:05
category: cs
---

> # 큐



## 1. 설명

- 입력과 출력을 한쪽 끝으로 제한한 자료구조. 먼저 들어온 원소가 먼저 나가기 때문에 FIFO(First In First Out) 이다.

- 큐의 가장 첫번째 원소를 **front**, 가장 끝원소를 **rear** 라고 한다. 큐는 **들어올 때 rear로 들어오지만, 나갈때는 front부터 나가는 특성을 갖는다.**  ( 놀이공원 대기줄 생각!! )
- 접근 방법은 front와 rear로만 가능하다.
- stack 과 queue는 search가 없다.
- **queue overflow** : 큐가 꽉 차서 더 이상 자료를 넣을 수 없는 경우
- **queue underflow** 큐가 비어 있어 자료를 꺼낼 수 없는 경우



## 2. 언제 사용할까?

버퍼, 프린트, 마구 입력된 것을 처리하고 있지 못하는 상황, BFS



## 3. 인터페이스

- front( head ) : 데이터를 큐에서 dequeue 할 수 있는 위치
- rear( tail ) : 데이터를 큐에 enqueue 할 수 있는 위치
- 메소드
  - enqueue : 큐에 데이터를 넣는다. ( insert )
  - dequeue : 큐를 데이터에서 꺼낸다. ( delete )
  - is_empty() : 큐가 비었는지 확인 
  - peek : 맨 앞의 값 반환만 하고 삭제는 하지 않는다.

![image](https://user-images.githubusercontent.com/39187116/71406881-ce781d80-267c-11ea-8f05-3345dd06c37b.png)

[이미지 출처](https://wayhome25.github.io/cs/2017/04/18/cs-21/)



## 4. 구현

- ### 방법1 ( 리스트 활용 )

```python
class Queue:
  def __init__(self):
    self.items = []
  def enqueue(self, item):
    self.items.insert(0, item)
  def dequeue(self):
    self.items.pop()
  def is_empty(self):
    return self.items == []
  def print_queue(self):
    print(self.items)
  def peek(self):
    return self.items[0]
  def size(self):
    return len(self.items)
```

- ### (중요) 방법2 ( 노드를 활용, **head 와 tail 이용** )

```python
class Node:
  def __init__(self, item):
    self.data = item
    self.next = None
class Queue:
  def __init__(self):
    self.head = None
    self.tail = None
    
  def is_empty(self):
  	return not self.head
  
  def enqueue(self, item):
    node_data = Node(item)
    if self.is_empty():
      self.head = node_data
      self.tail = node_data
    self.tail.next = node_data
    self.tail = node_data
  
  def dequeue(self):
    if self.is_empty():
      return
    out_data = self.head.data
    self.head = self.head.next
    return out_data
  
  def peek(self):
    return self.head.data
```

