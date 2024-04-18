---
title: "[자료구조][python] 힙 내장 모듈: heapq"
category: "data structure"
tag: ["python", "자료구조", "우선순위 큐", "큐", "data structure", "힙", "heap", "priority queue"]
---
# 개요

파이썬에서는 힙 자료구조를 사용할 수 있도록 heapq라는 내장 모듈을 제공한다. 해당 모듈은 몇 가지 특성이 있다.

1. 해당 모듈의 힙을 리스트로 저장 시 시작 인덱스는 0부터 시작한다. 이는 [우선순위 큐와 힙](/data%20structure/data-structure-priority-queue-heap/) 문서에서 인덱스 1부터 시작했던 점과 다르다. 다만, 이 외에는 리스트에 힙을 저장하는 방식 자체는 해당 문서에서 배웠던 것과 동일하다. 즉, heap[i]의 자식 노드들은 heap[2\*i+1], heap[2\*i+2]이다. 
2. 해당 모듈은 최소 힙(min heap)만을 제공한다. 즉 항상 heap[i] < heap[2\*i+1], heap[2\*i+2]를 만족한다. 해당 모듈로 최대 힙을 구현하려면 다른 방법을 사용해야 하는데, 이에 대한 자세한 내용은 후술할 예정.

# 힙 생성, 삽입, 삭제 및 추출

해당 모듈은 파이썬의 내장 자료형인 리스트를 힙 자료구조처럼 다루게 해준다. 따라서 해당 모듈을 이용하여 힙을 사용하려면 리스트가 필요하다. 

다음은 기존의 리스트를 힙으로 만드는 코드이다.

```python
import heapq

some_list = [4, 2, 1, 5]
heapq.heapify(some_list)
print(some_list)
```

```python
[1, 2, 4, 5]
```

heapq.heapify() 함수에 힙으로 변경시키고자 하는 리스트를 대입하면 해당 리스트를 힙 자료구조처럼 사용할 수 있다. 

또는 빈 리스트에 여러 값들을 차례대로 삽입하는 방식으로 힙을 만들 수도 있다. 

```python
import heapq

some_list = []
add_values = [4, 2, 1, 5]
for v in add_values:
    heapq.heappush(some_list, v)

print(some_list)

while some_list:
    print(heapq.heappop(some_list))
```

```python
[1, 4, 2, 5]
1
2
4
5
```

리스트에 새로운 값들을 힙에 삽입하려면 위 예제 1-2에서처럼 heapq.heappush(heap, item) 함수를 이용하면 된다. 첫 번째 인자에는 힙 자료구조로 사용할 리스트를, 두 번째 인수로 첫 번째 인자의 힙 자료구조 안에 삽입할 아이템을 명시하면 된다. 

생성된 힙 자료구조 내에서 가장 작은 우선순위를 가지는 값을 추출하려면 heapq.heappop(heap) 함수를 이용하면 된다. 해당 함수는 힙 자료구조 내 가장 작은 우선순위를 가지는 아이템을 반환한 뒤, 해당 아이템을 힙 자료구조 내에서 삭제한다. 

# n번째로 작거나 큰 값 추출하기

heapq 모듈로 생성한 힙 자료구조에서 우선순위가 가장 작은 값을 검색하려면, 예를 들어 heap이라는 리스트를 힙 자료구조로 사용하고 있다면 heap[0]으로 검색하면 된다. 이는 heappop()과는 달리 힙 자료구조에서 해당 아이템을 삭제하지 않고 검색만 할 수 있다. 

그런데 해당 힙 내에서 두 번째로 우선순위가 작은 값을 검색하려면 어떻게 해야 할까? heap[1]이라고 하면 될까? 예제 1-2 출력결과의 첫 째 줄을 보면 알겠지만 그건 통하지 않는다는 것을 알 수 있다. 예제 1-2에서 만들어진 힙을 사용하여 some_list[1]를 사용할 경우 두 번째로 작은 수인 2가 아니라 4가 출력될 것이다. 이는 힙 자료구조에서, 부모 노드에는 왼쪽 자식 노드와 오른쪽 자식 노드가 존재하는데, 두 자식 노드 간 대소관계는 그때그때 다르기 때문이다. 

이를 위해서는 `heapq.nsmallest(n, iterable, key=None)`과 `heapq.nlargest(n, iterable, key=None)`을 사용하면 된다. 다음은 이를 활용한 예제이다.

```python
import heapq

some_list = []
add_values = [4, 2, 1, 5]
for v in add_values:
    heapq.heappush(some_list, v)

print(some_list)
print(heapq.nsmallest(3, some_list))
print(heapq.nlargest(3, some_list))
```

```python
[1, 4, 2, 5]
[1, 2, 4]
[5, 4, 2]
```

위 예제에서는 각각 힙에서 3개의 가장 작은 요소들과 가장 큰 요소들을 출력하였다. 3번째로 작거나 큰 요소만을 추출하고자 한다면 `heapq.nsmallest(3, some_list)[-1]`과 같이 사용하면 되겠다. 

# (우선순위, 값)으로 값과 함께 사용하기

앞서 살펴본 예제들에서는 우선순위 수치값만을 사용하였는데, 값과 쌍을 이루는 item을 힙에 삽입할 수도 있다. 

```python
import heapq

some_list = [(1, 'a'), (24, 'b'), (5, 'c'), (17, 'd')]
heapq.heapify(some_list)
print(some_list)
heapq.heappush(some_list, (3, 'e'))
print(some_list)  # 힙 내 구조 변화

while some_list:
    print(heapq.heappop(some_list))
```

```python
[(1, 'a'), (17, 'd'), (5, 'c'), (24, 'b')]
[(1, 'a'), (3, 'e'), (5, 'c'), (24, 'b'), (17, 'd')]
(1, 'a')
(3, 'e')
(5, 'c')
(17, 'd')
(24, 'b')
```

위와 같이 (우선순위, 값) 튜플을 이용하여 힙에 삽입하여 사용하면 된다. 

이 때 주의해야할 점은, (우선순위, 값) 아이템에서 우선순위 수치값은 항상 튜플의 맨 앞에 와야 한다는 것이다. 만약 거꾸로 (값, 우선순위) 아이템을 힙에 삽입하여 사용하면 어떻게 될까?

```python
import heapq

#some_list = [(1, 'a'), (24, 'b'), (5, 'c'), (17, 'd')]
some_list = [('a', 1), ('d', 17), ('c', 5), ('b', 24)]
heapq.heapify(some_list)
print(some_list)
heapq.heappush(some_list, ('e', 3))
print(some_list)  # 힙 내 구조 변화

while some_list:
    print(heapq.heappop(some_list))
```

```python
[('a', 1), ('b', 24), ('c', 5), ('d', 17)]
[('a', 1), ('b', 24), ('c', 5), ('d', 17), ('e', 3)]
('a', 1)
('b', 24)
('c', 5)
('d', 17)
('e', 3)
```

알파벳이 우선순위로 사용된 것을 확인할 수 있다. 

# 최대 힙 사용하기

내장 모듈 heapq는 최소 힙만을 제공한다. 이를 최대 힙으로 사용하려면 우선순위 수치값 앞에 마이너스(-) 부호를 붙여야 한다.

```python
import heapq

some_list = [(1, 'a'), (24, 'b'), (5, 'c'), (17, 'd')]
heap = []

for p, v in some_list:
    heapq.heappush(heap, (-p, v))

print(heap, heap[0])

while heap:
    print(heapq.heappop(heap))
```

```python
[(-24, 'b'), (-17, 'd'), (-5, 'c'), (-1, 'a')] (-24, 'b')
(-24, 'b')
(-17, 'd')
(-5, 'c')
(-1, 'a')
```

---

Reference

[1] [heapq — 힙 큐 알고리즘](https://docs.python.org/ko/3/library/heapq.html)
