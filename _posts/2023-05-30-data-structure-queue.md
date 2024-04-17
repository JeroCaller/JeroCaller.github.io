---
title: "[자료구조] 큐(queue)"
category: "data structure"
tag: ["python", "자료구조", "queue", "큐", "data structure"]
---
큐는 스택과 달리 양쪽 끝이 뚫려 있는 파이프 구조를 이룬다. 그래서 항목이 들어온 순서대로 접근도 가능한 선입선출(first in, first out, FIFO) 구조를 띈다. 큐는 큐의 뒤 쪽에서만 데이터를 삽입할 수 있고, 큐의 앞 쪽에서만 데이터를 꺼낼 수 있기에 중간 요소들에는 직접 접근할 수 없다. 보통 양쪽 끝을 각각 front(head)와 rear(tail)이라고 하는데, 보통 front는 데이터를 얻을 수 있는 곳, rear는 데이터를 삽입하는 곳으로 표현한다. 

큐는 데이터가 입력된 시간 순대로 처리해야할 때 쓰일 수 있다. 

큐가 꽉 차서 더 이상 데이터를 삽입할 수 없는 경우를 오버플로우(overflow), 큐가 비어 있어 그 어떤 데이터도 꺼낼 수 없는 경우를 언더플로우(underflow)라고 한다.

큐의 동작에는 다음이 존재하며, 모두 시간복잡도가 O(1)이다.

- enqueue: 큐 뒤쪽에 항목을 삽입한다. put(insert)이라고도 한다.
- dequeue: 큐 앞쪽의 항목을 반환하고 제거한다. get이라고도 한다.
- peek/front : 큐 앞쪽의 항목을 조회한다.
- empty: 큐가 비었는지 확인.
- size: 큐의 크기 확인.

파이썬에서 큐를 구현해보자. 먼저 첫 번째 방법으로, 리스트 하나에 insert() 메서드를 이용하여 데이터를 삽입하고, 먼저 삽입된 데이터를 pop() 메서드를 통해 꺼내는 방식으로 구현해보자.

```python
class MyQueue():
    def __init__(self, limit_size: int = 10):
        self.queue = []
        self.limit_size = limit_size  # queue가 담을 수 있는 총 아이템 수 지정.

    def isEmpty(self):
        return self.queue == []
    
    def getCurrentSize(self):
        return len(self.queue)
    
    def enqueue(self, item):
        if len(self.queue) != self.limit_size:
            self.queue.insert(0, item)
        else:
            print("큐에 데이터가 꽉 차서 더 이상 삽입할 수 없습니다.")

    def dequeue(self):
        try:
            return self.queue.pop()
        except IndexError:
            print("큐가 비어서 출력할 데이터가 없습니다.")
            return
        
    def seek_peek(self):
        return self.queue[-1]
    

if __name__ == '__main__':
    q = MyQueue()
    data = ['a', 'b', 'c', 'd']
    print("현재 큐는 비어있나요? ", q.isEmpty())
    print("큐에 데이터 삽입.")
    for item in data:
        q.enqueue(item)
    print("현재 큐는 비어있나요? ", q.isEmpty())
    print("최신 데이터: ", q.dequeue())
    print("현재 큐에서 제일 먼저 출력될 아이템: ", q.seek_peek())
    print("현재 큐에 존재하는 총 아이템 개수: ", q.getCurrentSize())
```

```python
현재 큐는 비어있나요?  True 
큐에 데이터 삽입.
현재 큐는 비어있나요?  False
최신 데이터:  a
현재 큐에서 제일 먼저 출력될 아이템:  b
현재 큐에 존재하는 총 아이템 개수:  3
```

그러나 insert() 메서드를 사용하여 맨 처음 위치에 요소를 삽입하는 경우, 오른쪽에 있는 모든 요소들이 메모리상에서 오른쪽으로 한 칸씩 이동해야 한다. 이로 인해 시간복잡도가 O(n)이고, 이는 비효율적이다. 이러한 문제점 해결을 위해, 시간복잡도 O(1)인 두 개의 스택으로 좀 더 효율적인 큐를 구현할 수 있다. 

![두 개의 스택으로 큐를 구현하는 방법](/images/2023-05-30/2023-05-30-queue_from_two_stacks.png)

두 개의 스택으로 큐를 구현하는 방법

```python
from typing import Any

class StaticQueue():
    """큐의 왼쪽에서 데이터 삽입, 오른쪽에서 데이터 추출 가능한 구조."""
    def __init__(self, limit_size: int = 10):
        self.en_stack = []
        self.de_stack = []
        self.limit_size = limit_size  # queue가 담을 수 있는 총 아이템 수 지정.

    def _transfer(self):
        while self.en_stack:
            self.de_stack.append(self.en_stack.pop())

    def isEmpty(self) -> bool:
        return self.en_stack == [] and self.de_stack == []
    
    def getCurrentSize(self) -> int:
        return len(self.en_stack) + len(self.de_stack)
    
    def enqueue(self, item):
        cur_size = self.getCurrentSize()
        if cur_size != self.limit_size:
            self.en_stack.append(item)
        else:
            print("큐에 데이터가 꽉 차서 더 이상 삽입할 수 없습니다.")

    def dequeue(self) -> Any | None:
        if not self.de_stack:
            self._transfer()
        try:
            return self.de_stack.pop()
        except IndexError:
            print("큐가 비어서 출력할 데이터가 없습니다.")
            return
        
    def seek_peek(self) -> Any | None:
        if not self.de_stack:
            if self.en_stack:
                return self.en_stack[0]
            else:
                return
        return self.de_stack[-1]
    
    def clear(self) -> None:
        """
        큐를 비운다.
        """
        self.en_stack = []
        self.de_stack = []
    
    def __repr__(self):
        if self.en_stack == [] and self.de_stack == []:
            return repr([])
        
        self.total_queue = []  # self.en_queue + self.de_queue
        temp_stack = []
        if self.en_stack:
            temp_stack = self.en_stack.copy()
            while temp_stack:
                self.total_queue.append(temp_stack.pop())
        if self.de_stack:
            self.total_queue.extend(self.de_stack)
        return repr(self.total_queue)
    

class DynamicQueue(StaticQueue):
    """
    크기 제한없이 동적으로 데이터를 enqueue및 dequeue할 수 있는 동적 큐.
    """
    def __init__(self) -> None:
        self.en_stack = []
        self.de_stack = []

    def enqueue(self, item: any) -> None:
        self.en_stack.append(item)

def test_static_queue():
    q = StaticQueue()
    data = ['a', 'b', 'c', 'd']
    print("현재 큐는 비어있나요? ", q.isEmpty())
    print("큐에 데이터 삽입.")
    for item in data:
        q.enqueue(item)
    print("현재 큐는 비어있나요? ", q.isEmpty())
    print("최신 데이터: ", q.dequeue())
    print("현재 큐에서 제일 먼저 출력될 아이템: ", q.seek_peek())
    print("현재 큐에 존재하는 총 아이템 개수: ", q.getCurrentSize())
    print(q)
        

if __name__ == '__main__':
    test_static_queue()
```

```python
현재 큐는 비어있나요?  True
큐에 데이터 삽입.
현재 큐는 비어있나요?  False
최신 데이터:  a
현재 큐에서 제일 먼저 출력될 아이템:  b
현재 큐에 존재하는 총 아이템 개수:  3
['d', 'c', 'b']
```

- 테스트 코드
    
    ```python
    import unittest
    from my_queue import StaticQueue
    
    class TestQueue(unittest.TestCase):
        def setUp(self) -> None:
            self.q = StaticQueue(5)
            self.desc = self.shortDescription()
            self.dataset = ['빨강', '주황', '노랑', '초록', '파랑']
    
            if self.desc == 'need_dataset':
                for data in self.dataset:
                    self.q.enqueue(data)
    
        def test_empty(self):
            """
            빈 큐 테스트.
            """
            self.assertEqual(self.q.__repr__(), '[]')
            self.assertEqual(self.q.isEmpty(), True)
            self.assertEqual(self.q.getCurrentSize(), 0)
            self.assertEqual(self.q.dequeue(), None)
            self.assertEqual(self.q.seek_peek(), None)
    
        def test_enqueue_dequeue(self):
            """
            큐 항목 삽입 및 추출 테스트.
            """
            # test1
            self.q.enqueue('빨강')
            self.assertEqual(self.q.seek_peek(), '빨강')
            self.assertEqual(self.q.__repr__(), "['빨강']")
            self.assertEqual(self.q.getCurrentSize(), 1)
    
            # test2
            self.q.enqueue('주황')
            self.assertEqual(self.q.seek_peek(), '빨강')
            self.assertEqual(self.q.__repr__(), "['주황', '빨강']")
            self.assertEqual(self.q.getCurrentSize(), 2)
    
            # test3
            get_item = self.q.dequeue()
            self.assertEqual(get_item, '빨강')
            self.assertEqual(self.q.seek_peek(), '주황')
            self.assertEqual(self.q.__repr__(), "['주황']")
            self.assertEqual(self.q.getCurrentSize(), 1)
    
            # test4
            self.q.enqueue('빨강')
            self.assertEqual(self.q.seek_peek(), '주황')
            self.assertEqual(self.q.__repr__(), "['빨강', '주황']")
            self.assertEqual(self.q.getCurrentSize(), 2)
    
            # test5
            get_item = self.q.dequeue()
            self.assertEqual(get_item, '주황')
            self.assertEqual(self.q.seek_peek(), '빨강')
            self.assertEqual(self.q.__repr__(), "['빨강']")
            self.assertEqual(self.q.getCurrentSize(), 1)
    
        def test_clear(self):
            """
            need_dataset\n
            큐 내 모든 데이터 삭제 테스트.
            """
            # test1
            self.assertEqual(self.q.getCurrentSize(), 5)
            self.q.clear()
            self.assertEqual(self.q.getCurrentSize(), 0)
            self.assertEqual(self.q.__repr__(), '[]')
            self.assertEqual(self.q.isEmpty(), True)
            self.assertEqual(self.q.dequeue(), None)
            self.assertEqual(self.q.seek_peek(), None)
    
            # test2
            self.q.clear()
            self.assertEqual(self.q.getCurrentSize(), 0)
            self.assertEqual(self.q.__repr__(), '[]')
            self.assertEqual(self.q.isEmpty(), True)
            self.assertEqual(self.q.dequeue(), None)
            self.assertEqual(self.q.seek_peek(), None)
    
    if __name__ == '__main__':
        unittest.main()
    ```
    
    ```python
    큐가 비어서 출력할 데이터가 없습니다.
    큐가 비어서 출력할 데이터가 없습니다.
    .큐가 비어서 출력할 데이터가 없습니다.
    ..
    ----------------------------------------------------------------------
    Ran 3 tests in 0.001s
    
    OK
    ```
    

한 편, 큐는 너비 우선 탐색(BFS)에 사용되며, 해당 개념은 나중에 다룰 예정.

> 파이썬 표준 모듈 queue에서 큐 자료구조를 제공한다. 뿐만 아니라 LIFO 형태의 큐, 우선순위 큐 등도 제공한다.
> 

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2]

[큐 (자료 구조)](https://ko.wikipedia.org/wiki/큐_(자료_구조))