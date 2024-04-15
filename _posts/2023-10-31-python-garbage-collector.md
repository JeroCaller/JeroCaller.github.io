---
layout: single
title:  "[Python] 메모리 관리와 garbage collector"
categories: ["Python"]
tag: ["Python", "메모리", "메모리 관리", "garbage collector", "gc", "연결리스트", "linked list", "memory leak", "메모리 누수", "dangling pointer", "reference counting", "generational garbage collection", "container object"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 개요 (내가 메모리 관리와 garbage collector에 대해 공부하게 된 계기)

자료구조를 공부하던 중, 연결리스트에 대해 배울 때 직접 손으로 연결리스트라는 자료구조를 구현했던 적이 있다. 연결리스트를 구성하는 노드를 따로 클래스로 만든 후, 연결리스트 클래스를 따로 정의하여 그 안에서 노드 객체를 생성하여 연결하는 방식으로 구현했었다. 대략적인 코드는 다음과 같다. 

```python
# For type alias.
Node_ = object
DPNode_ = object
Value = object

class Node():
    node_counter = 0

    def __init__(
            self, 
            value: Value = None, 
            pointer: Node_ = None, 
        ):
        """
        하나의 노드를 구현하는 클래스. 
        매개변수
        -------
        value: 노드의 값
        pointer: 현재 노드의 다음 노드를 가리키는 포인터.
        포인터에는 다음 노드의 메모리 주소값을 저장하므로
        해당 매개변수는 다음 Node 객체를 대입받아야 한다.  
        """
        self.value: Value = value
        self.pointer: Node | None = pointer
        Node.node_counter += 1

# (중간 생략)
class LinkedList():
    def __init__(self):
        """
        self.head_pointer: 맨 앞 노드를 가리키는 포인터. Node 객체를 받는다.\n
        self.tail_pointer: 맨 뒤 노드를 가리키는 포인터. Node 객체를 받는다.\n
        self.length: 연결 리스트 내 총 노드의 개수.
        """
        self.head_pointer: Node | None = None
        self.tail_pointer: Node | None = None
        self.length = 0
        self.link_char = ' -> '

	def addNodeFront(self, new_value: Value) -> (None):
	    """
	    연결 리스트의 맨 앞에 새 노드를 삽입.
	    """
	    self._addNodeFront(Node(new_value))

    def _addNodeFront(self, new_node: Node) -> (None):
        if self.head_pointer is None:
            # 연결 리스트에 아무런 노드도 없는 경우.
            self.head_pointer = new_node
            self.tail_pointer = new_node
        else:
            # 연결 리스트에 기존 노드들이 존재하는 경우.
            new_node.pointer = self.head_pointer
            self.head_pointer = new_node
        self.length += 1

# (생략)
	def clear(self) -> (None):
	    """
	    연결 리스트를 모두 비운다. 
	    즉, 연결 리스트 내 모든 노드들을 삭제한다.
	    """
	    node = self.head_pointer
	    while node:
	        next_node = node.pointer
	        node.pointer = None
	        del node
	        node = next_node
	
	    self.head_pointer: Node | None = None
	    self.tail_pointer: Node | None = None
	    self.length = 0
# (생략)
```

연결리스트를 구성하는 노드는 Node라는 클래스로 따로 정의한 후, 다음 노드를 가리키기 위해 해당 클래스에 pointer라는 인스턴스 속성을 따로 선언하였다. 그래서 prev_node.pointer = next_node 이런 식으로 다음 노드에 해당하는 Node 객체를 이전 Node 객체의 pointer 변수에 할당하는 방식을 이용해서 연결리스트를 구현했었다. 

처음 연결리스트를 LinkedList라는 클래스로 구현했을 때, 위 예제 1-1의 clear() 메서드 내 코드와는 달리 그 때는 단순히 clear() 메서드 안에 self.head_pointer = None 이라고만 작성했던 것 같다(내 기억이 맞다면). 그 때 당시엔 메모리 관리에 대해 잘 몰랐었고, clear() 메서드를 구현한 이유는 단순히 나중에 연결리스트 자료구조를 사용할 때 기존에 연결리스트 내에 있던 내용들을 모두 지우고 다시 시작하고자 할 때를 위해 구현했던 것이다. 즉 메모리 관리적인 측면은 전혀 생각하지 못했었다. 

그러다 문득 이런 생각이 들었다. “이렇게 연결리스트의 시작 부분에 해당하는 노드로의 참조만 끊으면 개발자 입장에서는, 즉 코드적인 측면에서는 해당 노드 객체를 다시 참조할 순 없겠지만, 메모리상에는 계속 남아있는 거 아닌가?” 라는 의문이 떠올랐었다. 만약 그렇다면 내가 다른 프로그램을 실행할 때 해당 메모리 영역에 데이터가 계속 남아있어서 메모리 용량 부족으로 인해 실행이 안되는 상황도 발생하는 거 아닌가라는 생각도 들었었다. 

그래서 이 생각 이후 clear() 메서드 내부 코드를 위 예제 1-1처럼 바꾸었다. 단순히 시작 노드의 참조만 해제하는 것이 아니라, 시작 노드부터 끝 부분의 노드까지 하나씩 전진(?)하면서 del 을 이용해 노드 객체를 하나씩 지우는 방식으로 재작성했다. 즉, 이전에는 기존의 노드들을 지우고 새 노드들로 채우려고만 했던 방식에서 메모리 관리 측면까지 고려한 것이었다. 

그런데 여기서 또 의문이 들었다. 정말 이 코드로 완전히 메모리 상에서 해당 노드 객체들의 데이터가 사라진 것일까? 그러고보니 파이썬에서는 내가 직접 메모리에 할당된 객체 데이터를 할당 해제하는 작업을 하지 않아도 메모리 관련 문제가 일어났던 적은 없었다. 그렇다고 해서 그것만 믿고 메모리 생각 안하고 코드를 막짜도 괜찮은 것인가, 라는 의문도 들었다. 그래서 이참에 조사를 해보았고 결국에는 메모리 관리에 대한 내용을 대략적으로 파악하게 되었고, 파이썬의 garbage collector라는 개념에 대해서도 접하게 되었다. 

# 메모리 관리 (memory management)

프로그래밍 언어로 짠 프로그램을 실행시킬 때, 객체의 값들은 메모리에 저장되어 프로그램이 필요할 때마다 메모리에 접근하여 해당 값들을 사용한다. 그리고, 파이썬을 비롯한 대부분의 프로그래밍 언어에서 변수(variable)란 메모리에 존재하는 객체의 주소를 참조하는 일종의 포인터이다. 쉽게 말하면 변수는 “달을 가리키는 손가락”인 셈이다(달이 객체이고, 그 달이 밤하늘이라는 넓은 메모리 안에 존재하는 것이다). 그래서 프로그램이 어떤 변수를 사용한다는 것은, 프로그램의 프로세스가 그 변수가 가리키는 값을 메모리로부터 읽어들여온다는 것이다. 그 후에는 해당 값을 토대로 연산을 수행할 것이다. 

저수준(low-level) 언어에서는 어떤 객체를 생성하기 전에 개발자가 직접 그 변수에 대한 메모리를 할당(allocate)해주고, 그 변수에 대한 작업을 모두 마친 후에는 개발자가 직접 할당 해제(deallocate, 반환)해야 했다. 할당 해제라는 것은 메모리 상에 저장된 객체를 메모리 공간으로부터 제거하여 빈 공간 상태를 만드는 것이다(”방 빼세요”라고 말하는 것처럼). 할당 해제를 해야 다른 프로그램 사용 시 그 프로그램이 동작할 수 있도록 다른 데이터가 메모리 공간에 저장될 수 있기 때문에 할당 해제도 필수이다. 

어쨌거나 저수준 언어에서는 개발자가 직접 메모리를 할당 및 반환해야하는데, 이럴 때 문제점이 존재한다. 

- 개발자가 메모리 반환해야하는 것을 깜빡한 경우: 이러한 경우 반환되지 않은 데이터가 메모리 상에 계속 존재하게 되는데, 이를 메모리 누수(memory leak)라 한다. 메모리 누수로 인해 프로그램 작동 시 시간이 경과함에 따라 점점 더 많은 메모리를 잡아먹게 되는 문제점이 발생한다. 이로 인해 오랫동안 동작되는 앱의 경우 메모리 누수는 심각한 문제가 된다. (파이썬에서는 장기간 실행되는 프로세스가 시간이 지남에 따라 더 많은 메모리를 차지하는 데에는 단순히 메모리 누수만이 원인이 되지 않는다. 파이썬에서는 특정 메모리가 나중에 쓰일 것을 염두해 미리 저장하는 특성이 있는데, 이로 인해 해당 증상이 일어날 수 있다고 한다)
- 너무 일찍 메모리 반환한 경우: 아직 프로그램에서 메모리에 저장된 특정 데이터를 사용할 일이 남았는데 먼저 해당 데이터에 대해 메모리 할당 해제한 경우, 메모리에 존재하지 않는 데이터에 접근하게 되는 상황이 발생한다. 이 경우 데이터 손상이 발생할 수 있다고 한다. 이미 할당 해제된 메모리를 참조하는 변수를 dangling pointer(댕글링 포인터, 허상 포인터)라고 부른다.

이로 인해, 고수준(high-level) 언어에서는 사람이 일일이 메모리를 할당하고 반환하는 대신 자동으로 이러한 작업들을 해주는 자동 메모리 관리 시스템(automatic memory management)이 내장된다. 그리고 이러한 자동 메모리 관리 시스템을 가능하게 해주는 메모리 관리 기법 중 하나가 garbage collection (쓰레기 수집)이다. 

자동 메모리 관리 시스템이 있으면 우선 개발자는 메모리 관리에 대해 신경쓰지 않아도 되서 코드 작성을 더 빨리 해낼 수 있게 된다. 또한 앞서 언급했던 문제들인 메모리 누수, dangling pointer 문제점도 해결된다. 

이러한 자동 메모리 관리 시스템은 단점도 존재한다. 자동 메모리 관리 시스템을 위한 추가적인 메모리가 필요하며, 또한 모든 참조들을 추적하기 위한 추가 연산도 필요해진다. 또한 자동 메모리 관리 시스템을 가진 대부분의 언어에서는 가비지 콜렉터(garbage collector, 쓰레기 수집기)가 삭제되어야 할 객체들을 수집하는 동안 프로세스가 일시 중단된다. 이는 프로그램의 전체적인 실행 속도가 느려질 수 있음을 시사한다.

하지만 이러한 단점들은 시간이 지날수록 기술의 발전으로 인해 가성비 좋은 더 큰 용량을 가진 메모리들이 등장함으로써 희석된다. 

파이썬도 가비지 콜렉션을 통해 자동으로 메모리를 관리해주는 기능을 가지고 있다. 

# 파이썬에서 사용하는 가비지 콜렉션 기법

파이썬은 보통 C언어로 구현된 컴파일러(CPython)를 통해 실행된다. (사실 Java 기반의 Jython과 C# 기반의 IronPython으로도 파이썬이 실행될 수 있다.)  이러한 CPython에서는 자동 메모리 관리를 위해 두 가지 가비지 콜렉션 기법을 사용한다. 

- Reference counting (참조 횟수 카운팅)
- Generational garbage collection (세대 가비지 컬렉션)

## Reference counting

Reference counting은 메모리 상에 존재하는 어떤 객체(데이터)가 변수 또는 다른 무언가에 의해 참조되는 횟수를 계산하고, 만약 해당 객체가 참조되는 횟수가 0일 때 해당 객체에 대한 메모리 자원을 반환시키는 기법이다. 

파이썬에서는 어떤 객체에 대한 참조 횟수를 세는 기준은 다음과 같다.

- 변수에 객체를 할당했을 때.
- 객체를 자료구조(data structure)에 추가했을 때. 즉, list의 append() 메서드를 통해 또는 클래스 인스턴스의 속성(변수)에 할당되었을 때 등을 포함한다.
- 함수의 인자로써 객체가 전달되었을 때.

참고로 파이썬에서 작동되는 Reference counting 기능은 개발자가 끌 수 없다. (반면 다음에 설명할 Generational garbage collection은 개발자가 원할 때 그 기능을 끌 수 있다) 

해당 기법은 어떤 객체의 참조 횟수가 0이 됨과 동시에 해당 객체에 대한 메모리 자원을 반환하기에 빠르고 효율적으로 메모리를 관리할 수 있다. 

파이썬에서 특정 객체의 참조 횟수를 확인하는 방법은 sys 모듈의 getrefcount() 함수를 사용하는 것이다. 다음은 그 예이다.

```python
import sys

some_var = 'hi there'
print(sys.getrefcount(some_var))
```

```python
3
```

위 예제 2-1을 살펴보면 ‘hi there’라는 객체는 some_var라는 변수로 할당됨에 따라 해당 객체의 참조 횟수가 1 늘어났다. 그 다음, 해당 객체는 some_var라는 변수가 getrefcount라는 함수의 인자로 대입되었기에 여기서도 참조 횟수가 하나 늘어나서 총 2회가 된다. 위 예제는 vscode 에디터에서 실행하였는데, print 함수를 사용하지 않아도 되는 python IDLE 프로그램에서 위 코드를 실행시키면 2라는 숫자가 출력될 것이다. print 함수를 통해 sys.getrefcount() 반환값을 직접 출력할 경우 해당 과정에서 (이유는 알 수 없는) 참조 횟수가 1 증가한다. 

(이건 확실하지는 않은데, print() 함수의 인자로 해당 객체가 직접적으로 대입된 게 아님에도 이것도 참조횟수로 카운팅하는 이유는 sys.getrefcount() 함수의 특성 때문인 것 같다. sys.getrefcount() 함수의 반환값은 해당 함수 인자로 들어온 객체 그 자체가 아니라 그 객체의 참조 횟수이다. 그래서 해당 함수의 반환값을 인자로 받아들이는 print() 함수 입장에서는 객체를 인자로 받아드리는 게 아니라 그 객체의 참조 횟수를 받는 것이다. 그래서 이론상으로는 print() 함수에서는 해당 객체에 대한 참조 횟수가 늘어날 이유가 없다. 그런데 sys.getrefcount() 함수의 특성 상 print() 함수 실행 시 해당 객체에 대한 임시 참조가 생성되어 참조 횟수가 하나 증가하는 것 같다. sys.getrefcount() 함수가 아니였다면 해당 객체에 대한 참조 횟수는 2였을 것이다.)

```python
import sys

some_var = 'hi there'
some_other_var = some_var
some_list = [some_var]
some_dict = {'hi there'}

def some_func(some_arg):
    return some_arg

some_result = some_func(some_var)
print(sys.getrefcount('hi there'))
```

```python
7
```

위 예제 2-2에서는 마지막 줄에서의 참조 횟수 2번을 제외하면, some_var 변수 선언에 의한 참조, some_other_var라는 다른 변수에 의한 참조, 리스트와 딕셔너리 같은 자료구조에서의 참조, 함수 인자에서의 참조 이렇게 5번의 참조가 발생함을 알 수 있다. 

이렇게 파이썬에서 특정 객체의 참조 횟수를 확인하는 방법을 살펴보았다. 이러한 객체의 참조 횟수가 0이 되면 파이썬에서는 자동으로 해당 객체를 메모리에서 삭제한다. 

## Generational garbage collection

그런데, 위에서 소개한 reference counting 방법에는 한계가 존재한다. 바로 참조 순환(reference cycle)이다. 이것은 하나의 객체가 자기 자신을 가리키거나 둘 이상의 객체가 서로를 참조해서 순환 사이클이 생기는 경우이다. 다음은 참조 순환의 예이다.

```python
# ex1)
some_list = []
some_list.append(some_list)

# ex2)
class SomeClass:
    def __init__(self, name):
        self.pointer = None
        self.name = name

    def setObj(self, obj):
        self.pointer = obj

a = SomeClass('a')
b = SomeClass('b')
a.setObj(b)
b.setObj(a)

c = SomeClass('c')
d = SomeClass('d')
e = SomeClass('e')
c.setObj(d)
d.setObj(e)

res_list = []
def retrieve_all_nodes(init_node: SomeClass, result_list: list):
    if init_node is None:
        return result_list
    result_list.append(init_node.name)
    return retrieve_all_nodes(init_node.pointer, result_list)

res_list = retrieve_all_nodes(a, res_list)  # RecursionError 발생. 순환 참조하고 있다는 증거. 
#res_list = retrieve_all_nodes(c, []) # -> ['c', 'd', 'e']
print(res_list)
```

위 예제 3-1에서 리스트를 담는 변수가 해당 리스트의 요소로 추가하여 자기가 자신을 참조하는 참조 순환이 일어날 수 있고, 해당 클래스의 객체를 담을 수 있는 속성을 추가한 클래스를 정의할 경우 변수 a, b와 같이 서로 다른 변수가 서로를 참조하는 참조 순환이 발생할 수 있다. 

참조 순환이 발생하는 경우는 위 예제에서 볼 수 있듯, 다른 객체를 포함할 수 있는 컨테이너 객체(container object)에서 발생할 수 있다. 파이썬의 내장 자료형에서 컨테이너 객체에는 리스트, 딕셔너리, 사용자 정의 클래스의 인스턴스 등이 있다. 

이렇게 참조 순환이 발생한 경우, 외부에서 해당 객체의 참조를 해제하더라도 순환 참조에 의해 참조 횟수가 0이 되지 않고 1로 유지되는 현상이 발생한다. 이 경우 reference counting 기법으로는 해당 객체를 메모리 해제할 수 없다. 그래서 파이썬에서는 Generational garbage collection이라는 다른 방법도 추가하여 사용한다. 

Generational garbage collection는 이러한 순환 참조 문제를 해결하는 garbage collector이다. 해당 기법은 총 3개의 세대(generation)가 존재한다. 새로 생긴 객체들은 무조건 garbage collector의 1세대에 포함된다. 각 세대는 특정 조건을 만족하면 각 세대에 포함된 객체들에 대해 garbage collection 프로세스를 진행한다. 즉, 순환 참조인 객체를 조사하고, 존재하면 해당 객체에 대한 메모리 자원을 반환시키는 작업을 진행한다. 만약 이 수집 과정에서 살아남은 객체들이 있다면 해당 객체들은 더 높은(더 오래된 세대라고도 한다) 세대로 올라간다. 

특정 조건마다 수집 과정을 진행시키기 위해 그 특정 조건을 임계값(threshold)라는 값으로 기준을 삼는다. 가장 젋은(가장 아래) 세대는 현재 존재하는 모든 객체들의 수를 센다. 새로운 객체가 생성될 때마다 모든 객체의 수를 계산한다. 이 객체들의 수가 정해진 임계값을 넘는 순간 garbage collection 프로세스가 진행되는 것이다. 2, 3세대는 현재 각 세대에 존재하는 객체들의 수에서 마지막으로 garbage collection의 수행으로 할당 해제된 객체들의 수를 뺀 값을 기준으로(각 세대 존재하는 객체들의 수 - 마지막 garbage collection으로 할당 해제된 객체들의 수) 그 수가 임계값이 넘으면 해당 수집 프로세스가 진행되는 것이다. 

파이썬에서 generational garbage collector를 확인할 수 있는 모듈로 gc 모듈이 존재한다. 

```python
import gc

print(gc.get_threshold())
print(gc.get_count())
print(gc.collect())
print(gc.get_count())
```

```python
(700, 10, 10)
(387, 2, 8)
21
(5, 0, 0)
```

gc.get_threshold() 함수는 현재 파이썬의 세대별 임계값을 반환하는 함수이다. 위 출력결과의 맨 첫 째줄을 보면 현재 1, 2, 3세대의 임계값이 각각 700, 10, 10임을 알 수 있다. gc.get_count() 함수는 현재 각 세대에 존재하는 객체들의 수를 의미한다. gc.collect()를 실행하면 개발자가 수동으로 garbage collection 프로세스를 진행시킬 수 있다. gc.collect() 함수를 호출하고 그 후에 get_count() 함수를 통해 각 세대별 객체 수를 살펴보면 확연히 줄어든 것을 확인할 수 있다. 

# 개발자가 파이썬의 garbage collector를 대하는 법

파이썬이 두 가지의 garbage collector를 이용하여 자동으로 메모리를 관리해준다는 것을 알았는데, 이 사실을 통해 개발자는 어떤 일을 할 수 있을까? 

사실 방금 말했듯, 메모리 관리는 파이썬이 알아서 다해주기에 왠만해서는 개발자가 garbage collector의 행동을 제어하거나 직접 메모리 관리를 해줄 필요가 없다. 만약 파이썬으로 작성한 프로그램의 성능이 garbage collector 때문에 느리다고 판단될 경우, gc 모듈을 통해서 모든 세대의 임계값을 0으로 한다든지 하여 해당 기능을 수동으로 끌 수는 있다. 한 예로, 인스타그램은 Django라는 파이썬 웹 프레임워크를 사용하는데, 파이썬의 gc를 수동으로 끔으로써 성능을 10% 향상시켰다고 한다. 하지만 이 경우 인스타그램은 무수히 많은 유저들이 사용하는 웹이라서 조금이라도 성능을 향상시키기 위해 이와 같은 일들을 한 것이고, 일반적으로는 파이썬의 gc만으로도 별 문제가 없다고 한다. 따라서 우선은 성능에 문제가 생긴다면 먼저 왜 성능에 문제가 생긴 것인지 면밀히 검사한 후에 해당 문제의 원인과 이를 어떻게 해결해야할지를 잘 이해하는 것이 중요하다. 

즉, 결론적으로, 파이썬의 gc가 메모리 관리를 알아서 잘 해주기에 메모리 할당 및 할당 해제에 관해서는 개발자가 별로 신경 쓸 것이 없다. 그저 코드 짤 때 메모리를 최소한만 사용하도록 짜서 메모리를 쓸데없이 낭비하는 문제만 방지하면 될 듯 싶다. 

# 연결리스트 clear 결론

글 맨 앞 개요 부분으로 돌아가서, 해당 연결리스트 클래스의 clear 메서드에는 예전 코드처럼 self.head_pointer = None이라고 해도 메모리 관리에 문제는 없을 것으로 예상된다. 예제 1-1의 clear() 메서드 코드처럼 굳이 저렇게 짤 필요까지는 없는 듯 싶다. 예를 들어 연결 리스트의 노드들이 A → B → C 처럼 연결되어있다고 할 때, 노드 객체 A를 유일하게 가리키는 self.head_pointer의 값을 None으로 하여 노드 객체 A에 대한 참조를 해제하면 노드 객체 A를 가리키는 참조는 더 이상 존재하지 않는다. 그러면 reference counting이라는 gargabe collection 프로세스가 작동하여 해당 노드 객체 A는 메모리 상에서 삭제될 것이다. 그러면 자동적으로 노드 객체 B의 참조 횟수도 0이 되어 해당 객체도 사라지고 그 뒤에 있는 모든 노드 객체들이 도미노처럼 메모리 반환이 일어날 것이다. 따라서 메모리 누수와 같은 문제는 걱정하지 않아도 될 것 같다. 

이러한 문제는 Node 객체를 이용하여 이진 트리(binary tree) 클래스를 구성할 때에도 마찬가지이다. 이진 트리 내에서 특정 노드 객체를 참조하는 객체는 오로지 부모 노드 객체 하나 뿐이다. 그래서 트리의 루트 노드를 참조하는 self.root에 None과 같은 다른 값을 대입하여 루트 노드 객체의 참조 횟수를 0으로 만들면 자연스레 루트 노드 객체를 포함한 모든 하위 트리의 노드 객체들도 reference counting에 의해 자동적으로 메모리 자원이 반환될 것이다. 

설령 원형 연결리스트처럼 순환 참조하는 경우에도 파이썬의 Generational garbage collector가 자동으로 해당 메모리를 관리해줄 것이라서 따로 내가 걱정하지 않아도 되겠다. 

---

Reference

[1] [Python Garbage Collection: What It Is and How It Works](https://stackify.com/python-garbage-collection/)

[2] [CPython](https://en.wikipedia.org/wiki/CPython)

[3] [Garbage Collection in Python](https://medium.com/dmsfordsm/garbage-collection-in-python-777916fd3189)

[4] [파이썬 메모리에서 객체 지우기](https://chickencat-jjanga.tistory.com/65)

[5] [Garbage collection in Python: things you need to know \| Artem Golubin](https://rushter.com/blog/python-garbage-collector/)

[6] 파이썬 객체 참조 관계를 시각화해주는 외부 라이브러리

[Python Object Graphs — objgraph 3.6.0 documentation](https://mg.pov.lt/objgraph/)

[7] wrtn - 생성형 챗 AI에게도 물어봄. 

[8] [Dismissing Python Garbage Collection at Instagram](https://instagram-engineering.com/dismissing-python-garbage-collection-at-instagram-4dca40b29172)