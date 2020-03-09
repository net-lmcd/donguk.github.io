---
title: heap
date: 2020-03-09 22:03:71
category: cs
---

# 힙( Heap )



우선 순위 큐를 위하여 만들어진 자료구조.

### 우선순위 큐?

1. 우선순위의 개념을 큐에 도입한 자료 구조

| 자료구조                    | 삭제되는 요소               |
| --------------------------- | :-------------------------- |
| 스택(Stack)                 | 가장 최근에 들어온 요소     |
| 큐(Queue)                   | 가장 먼저 들어온 요소       |
| 우선순위 큐(Priority Queue) | 가장 우선순위가 높은 데이터 |

2. 우선순위 큐의 이용 사례

   a. 네트워크 트레픽 제어

   b. 시뮬레이션 시스템

   c. 운영 체제에서의 작업 스케줄링

   d. 수치 해석적인 계산

3. 우선순위 큐는 배열, 연결리스트, **힙** 으로 구현이 가능하다. 이 중에서 힙으로 구현하는 것이 가장 효율적이다.( 삽입,삭제 nlogn )



### 힙 이란?

1. 우선순위 큐를 위하여 만들어진 자료구조
2. 최댓값이나 최솟값을 빠르게 찾아내도록 만들어진 자료구조
3. 이진 탐색 트리에서는 중복된 값을 허용하지 않지만 힙 트리에서는 중복을 허용한다.
4. 부모 노드의 키 값이 자식 노드의 키 값보다 항상 큰 이진트리



### 힙의 종류

1. 최대 힙(max heap)
   - 부모 노드의 키 값이 자식 노드의 키 값보다 크거나 같은 완전 이진 트리
2. 최소 힙(min heap)
   - 부모 노드의 키값이 자식 노드의 키 값보다 작거나 같은 완전 이진 트리



### 힙의 구현

- 힙을 저장하는 표준적인 자료구조는 리스트이다.
- 구현을 쉽게 하기 위하여 배열의 첫 번째 인덱스인 0은 사용되지 않는다.
- 힙에서의 부모 노드와 자식 노드의 관계

```
왼쪽 자식의 인덱스 = 부모의 인덱스 * 2
오른쪽 자식의 인덱스 = 부모의 인덱스 * 2 + 1
부모의 인덱스 = 자식의 인덱스 / 2
```

- 최대 힙

다음 세 가지 규칙을 이용해서 구현

1. 루트 노드가 항상 최댓값을 가진다.
2. 반드시 완전 이진 트리이어야 한다.
3. 최대 힙 내의 임의의 노드를 루트로 하는 서브트리 또한 최대 힙이다.

```python
class MaxHeap:
    def __init__(self):
        self.data = [None]

    def insert(self, item):
        self.data.append(item)
        i = len(self.data) - 1
        while i > 1:
            if self.data[i] > self.data[(i // 2)]:
                self.data[i], self.data[(i // 2)] = self.data[(i // 2)], self.data[i]
                i = i // 2
            else:
                break

    def remove(self):
        if len(self.data) > 1:
            self.data[1], self.data[-1] = self.data[-1], self.data[1]
            data = self.data.pop(-1)
            self.maxHeapify(1)
        else:
            data = None
        return data

    def maxHeapify(self, i):
        left = 2*i
        right = 2*i+1
        smallest = i
        if left < len(self.data) and self.data[i] < self.data[left]:
            smallest = left
        if right < len(self.data) and self.data[i] < self.data[right]:
            smallest = right
        if smallest != i:
            self.data[i], self.data[smallest] = self.data[smallest], self.data[i]
            self.maxHeapify(smallest)


```

- Insert : 리스트의 끝에 원소를 넣고 리스트의 길이가 2이상이면 부모 노드와의 크기를 비교하면서 적절한 위치로 이동시킨다.
- remove: 리스트의 루트노드 ( Max 값 노드 ) 와 마지막 노드의 위치를 변경 시키고 pop을 하여 최대값을 리턴한다. 루트 노드와 변경된 노드가 적절한 위치로 이동하기 위해 maxHeapify 메소드를 이용한다.
- maxHeapify: 파라미터로 받은 노드를 적절한 위치로 이동시키는 역할을 한다.

