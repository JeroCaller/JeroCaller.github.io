---
title: "[자료구조] 연결 리스트 (linked list)"
category: data structure
tag: ["python", "자료구조", "data structure", "list", "linked list", "연결 리스트"]
---
# 개요

연결 리스트는 데이터와 포인터가 포함된 노드들로 구성된 선형 리스트이다. 여기서 포인터는 다음 노드(또는 이전 노드) 값을 가리킴으로써 노드와 노드 사이가 연결될 수 있다. 마지막 노드는 그 뒤에 포인터가 가리킬 노드가 없으므로 해당 노드는 Null값을 가지게 된다. 파이썬에서는 None을 가진다. 기차 칸과 기차 칸이 연결된 일종의 기차와도 같다. 

연결 리스트의 맨 앞 노드가 어떤 노드인지를 표시하기 위해 head라는 포인터를 사용할 수도 있다. 

# 연결 리스트의 종류

연결 리스트에는 여러 종류가 있다.

- 단일 연결 리스트: 각 노드에는 데이터가 들어갈 공간 하나와 한 개의 포인터가 들어갈 공간으로 이루어져있고, 각 노드의 포인터는 다음 노드를 가리키는 구조이다.

![단일 연결 리스트 구조. [3] 참조.](https://upload.wikimedia.org/wikipedia/commons/thumb/9/9c/Single_linked_list.png/600px-Single_linked_list.png)

단일 연결 리스트 구조. [3] 참조.

- 이중 연결 리스트: 단일 연결 리스트와 거의 같으나, 차이점은 하나의 노드에 두 개의 포인터가 포함되어 있어 그 중 한 노드는 이전 노드를, 다른 노드는 다음 노드를 가리키는 구조이다. 단일 연결 리스트는 노드 A, B가 있고, A가 B를 참조하면 B는 A를 참조하지 않는 일방적인 구조를 가지나 이중 연결 리스트 구조에서는 두 노드가 서로를 참조하는 구조를 띤다.

![이중 연결 리스트 구조. [3] 참조.](https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/Doubly_linked_list.png/600px-Doubly_linked_list.png)

이중 연결 리스트 구조. [3] 참조.

- 원형 연결 리스트: 단일 연결 리스트에서 처음 노드와 마지막 노드를 연결하여 원형을 이루게 만든 구조이다.

![원형 연결 리스트 구조. [3] 참조.](https://upload.wikimedia.org/wikipedia/commons/thumb/9/98/Circurlar_linked_list.png/600px-Circurlar_linked_list.png)

원형 연결 리스트 구조. [3] 참조.

# 노드 삽입

단일 연결 리스트 구조로 가정하고 설명하겠다.

- 맨 앞에 노드를 삽입하는 경우

연결 리스트가 비어 있는 경우, 즉 그 어떤 노드도 존재하지 않는 경우에는 새로 삽입할 노드를 생성한 후, head 포인터가 해당 노드를 가리키게 하면 된다.

![노드의 하얀색 배경이 데이터를 담는 공간, 파란색 배경이 다른 노드를 가리키기 위한 포인터를 담는 공간이다.](/images/2023-05-30/2023-05-30-data-structure-linked-list-1.png)

노드의 하얀색 배경이 데이터를 담는 공간, 파란색 배경이 다른 노드를 가리키기 위한 포인터를 담는 공간이다.

연결 리스트가 비어있는지 확인하려면 head가 null 값을 가지는지 확인한다. null값을 가지면 연결 리스트가 비어있다는 뜻이다.

이미 기존 노드가 존재하여 연결 리스트가 비어있지 않는 경우, 일단 새 노드를 생성한 후, 새로 삽입할 노드의 포인터가 맨 앞에 있는 노드를 가리키게 하고 head 노드는 새 노드를 가리키게 하면 된다. 

![여기서는 이해를 쉽게 하기 위해 포인터가 가리키는 노드의 데이터 값을 포인터 공간에 담았다. 그러나 실제로는 포인터 공간에는 포인터가 가리키는 노드의 메모리 주소값을 담는다고 한다.](/images/2023-05-30/2023-05-30-data-structure-linked-list-2.png)

여기서는 이해를 쉽게 하기 위해 포인터가 가리키는 노드의 데이터 값을 포인터 공간에 담았다. 그러나 실제로는 포인터 공간에는 포인터가 가리키는 노드의 메모리 주소값을 담는다고 한다.

- 맨 뒤에 새 노드 삽입

연결 리스트의 맨 뒤에 새 노드를 삽입하고자 한다면, 기존의 마지막 노드의 포인터가 새 노드를 가리키게 하면 된다. 

![연결리스트 삽입1.png](/images/2023-05-30/2023-05-30-data-structure-linked-list-3.png)

이렇게 맨 앞이나 뒤에 새 노드를 삽입하는 경우의 시간복잡도는 O(1)이다. (그저 맨 앞 또는 맨 뒤 노드를 찾아 삽입하기만 되기에) (O(1)의 시간복잡도로 맨 마지막에 노드 삽입을 위해 head 말고도 tail이란 포인터를 사용한다. 해당 포인터는 맨 마지막 노드를 가리키는 포인터로 쓰인다)

- 중간에 노드 삽입

연결 리스트 중간의 특정 위치에 노드를 삽입하고자 한다. 기존에 노드 A, C가 있고 A → C로 연결되어 있을 때, 새 노드 B를 A와 C 사이에 삽입하고자 한다. 이 경우, B의 포인터는 A의 포인터가 가리키는 노드를 가리키도록 설정한다. 그 후, A 노드의 포인터는 새로 삽입된 B 노드를 가리키게 하면 된다. 

![연결리스트 삽입1.png](/images/2023-05-30/2023-05-30-data-structure-linked-list-4.png)

특정 위치에 노드를 삽입하고자 한다면 우선 그 특정 위치를 찾아야 한다. n개의 데이터가 있을 때 해당 위치를 찾을 수 있는 최악의 경우의 수는 n이다. 따라서 중간에 노드를 삽입하는 동작의 시간복잡도는 O(n)이다. 

# 노드의 삭제 및 검색

![연결리스트 삭제.png](/images/2023-05-30/2023-05-30-data-structure-linked-list-5.png)

위와 같은 연결 리스트가 존재하고, 여기서 값이 ‘c’인 노드를 삭제하고자 한다. 이를 위해서 우선 prev와 cur 포인터를 준비시켰다. 이 두 포인터는 처음에는 head 포인터가 가리키는 첫 번째 노드를 가리키게 한다. 그 다음, cur 포인터가 가리키는 노드의 값이 ‘c’인지를 조사한다. 아니라면 cur 포인터는 다음 노드를 가리키게 한다. prev 포인터는 cur가 가리키는 노드의 바로 이전 노드를 가리키는 용도이므로 cur 포인터가 가리키는 노드의 바로 이전 노드를 가리키게 하면 된다. 이렇게 계속 반복했을 때 cur 포인터가 가리키는 노드가 마침내 ‘c’ 값을 가지는 경우, 우선 prev 포인터가 가리키는 노드의 포인터를 삭제하고자 하는 노드의 포인터가 가리키는 다음 노드를 가리키도록 한다. 즉, prev가 가리키는 노드의 포인터는 다음 다음 노드를 가리키게 된다. 그 후, 삭제하고자하는 노드 (cur 포인터가 가리키고 있는 노드)의 포인터를 null로 바꾸면 해당 노드를 삭제하게 되는 것이다. 

특정 노드를 검색할 때도 이와 비슷하게 cur 포인터를 이용하면 된다. 검색 시에는 노드를 삽입하거나 삭제하는 것이 아니기에 기존 노드의 포인터 값을 변경시킬 필요는 없다. 

# 연결 리스트 구현 예제

다음은 단일 연결 리스트를 구현하는 예제이다.

- 단일 값만을 담는 노드에 대한 연결리스트
    
    ```python
    class Node():
        def __init__(self, value=None, pointer=None):
            """
            하나의 노드를 구현하는 클래스.
            value: 노드의 값
            pointer: 현재 노드의 다음 노드를 가리키는 포인터.
            포인터에는 다음 노드의 메모리 주소값을 저장하므로
            해당 매개변수는 다음 Node 객체를 대입받아야 한다.
            """
            self.value = value
            self.pointer = pointer
    
    class LinkedList():
        def __init__(self):
            """
            self.head_pointer: 맨 앞 노드를 가리키는 포인터. Node 객체를 받는다.
            self.tail_pointer: 맨 뒤 노드를 가리키는 포인터. Node 객체를 받는다.
            self.length: 연결 리스트 내 총 노드의 개수.
            """
            self.head_pointer: Node | None = None
            self.tail_pointer: Node | None = None
            self.length = 0
    
        def getLength(self):
            """
            현재 연결 리스트 내 총 노드 수 반환.
            """
            return self.length
    
        def __repr__(self):
            """
            현재 연결 리스트를 출력.
            연결 리스트가 비어있으면 빈 문자열 출력.
            """
            linked_list = []
            current_node: Node | None = self.head_pointer
            while current_node is not None:
                value_ = current_node.value
                if type(value_) != str:
                    value_ = str(value_)
                linked_list.append(value_)
                current_node = current_node.pointer
            return '->'.join(linked_list) if linked_list else "빈 연결리스트."
    
        def addNodeFront(self, new_node: Node):
            """
            연결 리스트의 맨 앞에 새 노드를 삽입.
            """
            if self.head_pointer is None:
                # 연결 리스트에 아무런 노드도 없는 경우.
                self.head_pointer = new_node
                self.tail_pointer = new_node
            else:
                # 연결 리스트에 기존 노드들이 존재하는 경우.
                new_node.pointer = self.head_pointer
                self.head_pointer = new_node
            self.length += 1
    
        def addNodeBack(self, new_node: Node):
            """
            연결 리스트의 맨 뒤에 새 노드 삽입.
            """
            if self.tail_pointer is None:
                # 연결 리스트에 아무 노드도 없는 경우.
                self.tail_pointer = new_node
                self.head_pointer = new_node
            else:
                # 연결 리스트에 기존 노드들이 존재하는 경우.
                self.tail_pointer.pointer = new_node
                self.tail_pointer = new_node
            self.length += 1
    
        def findNodeByIndex(self, index: int) -> tuple[Node, int, Node | None]:
            """
            인덱스로 찾고자 하는 노드 반환.
            인덱스의 시작 번호는 0이다.
            현재 연결 리스트의 노드 개수보다 더 큰 인덱스 값 대입 시,
            또는 인덱스에 음수 입력 시
            IndexError 예외 발생.
            """
            if self.length < index + 1 or index < 0:
                raise IndexError("연결 리스트의 길이에서 벗어나는 인덱스를 입력하였습니다.")
            cur_i = 0
            target_node = self.head_pointer
            prev_pointer = self.head_pointer
            while cur_i != index:
                prev_pointer = target_node
                target_node = target_node.pointer
                cur_i += 1
            if index == 0:
                prev_pointer = None
            return (target_node, index, prev_pointer)
        
        def findNodeByValue(self, target_value) -> tuple[Node, int, Node | None] | None:
            """
            찾고자 하는 값이 특정 노드의 값과 같은 경우 그 노드와 그 노드의 인덱스 반환.
            찾고자 하는 노드가 없다면 None을 반환.
            """
            current_node = self.head_pointer
            
            # 현재 선택된 노드의 이전 노드를 가리키는 포인터
            prev_pointer = self.head_pointer
    
            target_node = None
            target_index = 0
            while True:
                if current_node is None:
                    break
                if current_node.value == target_value:
                    target_node = current_node
                    if target_index == 0:
                        prev_pointer = None
                    break
                prev_pointer = current_node
                current_node = current_node.pointer
                target_index += 1
            if target_node is None:
                return None
            else:
                return (target_node, target_index, prev_pointer)
            
        def insertNode(self, index: int, new_node: Node):
            """
            연결 리스트에서 지정된 인덱스 위치에 새 노드 삽입.
            """
            old_node, old_index, prev_pointer = self.findNodeByIndex(index)
            new_node.pointer = old_node
            if prev_pointer is None:
                # 삽입하고자 하는 인덱스 위치가 0임.
                self.head_pointer = new_node
            else:
                prev_pointer.pointer = new_node
            self.length += 1
            
        def deleteNodeByIndex(self, index: int):
            """
            인덱스에 해당하는 노드 삭제.
            """
            target_node, target_index, prev_pointer = self.findNodeByIndex(index)
            if prev_pointer is None:
                # 삭제하고자 하는 인덱스 위치가 0임.
                self.head_pointer = target_node.pointer
            else:
                prev_pointer.pointer = target_node.pointer
            target_node.pointer = None
            self.length -= 1
    
        def deleteNodeByValue(self, target_value):
            """
            삭제하고자 하는 값과 일치하는 노드를 삭제.
            """
            try:
                search_result = self.findNodeByValue(target_value)
            except TypeError:
                print("TypeError: 찾고자 하는 값이 없습니다.")
            else:
                target_node, target_index, prev_pointer = search_result
            
            if target_index == 0:
                self.head_pointer = target_node.pointer
            else:
                prev_pointer.pointer = target_node.pointer
            target_node.pointer = None
            self.length -= 1
    ```
    
    다음은 위 파일을 테스트하는 파일과 그 결과이다.
    
    ```python
    """
    my_linked_list의 LinkedList 테스트.
    LinkedList는 연결 리스트를 구현한 클래스이다.
    """
    
    import unittest
    from my_linked_list import Node, LinkedList
    
    class TestLinkedList(unittest.TestCase):
        def setUp(self) -> None:
            self.linked_list = LinkedList()
            self.desc = self.shortDescription()
    
            # 테스트에 따른 fixture 설정.
            if self.desc == "test_add_node_front":
                self.linked_list.addNodeFront(Node('a'))
    
            elif self.desc == "test_add_node_back":
                test_nodes = [Node('a'), Node('b')]
                for one_node in test_nodes:
                    self.linked_list.addNodeBack(one_node)
    
            elif self.desc in (
                    'test_find_node_by_index',
                    'test_find_node_by_value',
                    'test_insert_node',
                    'test_delete_node_by_index',
                    'test_delete_node_by_value',
                ):
                test_values = ['a', 'b', 'c', 'd']
                test_nodes = [Node(value) for value in test_values]
                for one_node in test_nodes:
                    self.linked_list.addNodeBack(one_node)
    
        def test_empty(self):
            """
            test_empty\n
            비어있는 연결 리스트 출력 테스트
            """
            self.assertEqual(self.linked_list.__repr__(), "빈 연결리스트.")
            self.assertEqual(self.linked_list.getLength(), 0)
    
        def test_add_node_front(self):
            """
            test_add_node_front\n
            노드 하나를 맨 앞에 삽입하는 데에 성공하는지 테스트.
            """
            expected_result = '->'.join(['a'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 1)
    
        def test_add_node_back(self):
            """
            test_add_node_back\n
            노드 하나를 맨 뒤에 삽입하는 테스트.
            """
            expected_result = '->'.join(['a', 'b'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
        def test_find_node_by_index(self):
            """
            test_find_node_by_index\n
            주어진 인덱스의 노드를 찾을 수 있는지 테스트.
            """
            # test1
            test_index = 2
            actual_result = self.linked_list.findNodeByIndex(test_index)
            actual_result = (
                actual_result[0].value, 
                actual_result[1],
                actual_result[2].value,
                )
            expected_result = ('c', 2, 'b')
            self.assertEqual(actual_result, expected_result)
    
            # test2
            test_index = -1
            with self.assertRaises(IndexError):
                self.linked_list.findNodeByIndex(test_index)
    
            # test3. IndexError 테스트.
            test_index = 4
            with self.assertRaises(IndexError):
                self.linked_list.findNodeByIndex(test_index)
    
            # test4
            test_index = 0
            actual_result = self.linked_list.findNodeByIndex(test_index)
            actual_result = (
                actual_result[0].value, 
                actual_result[1],
                actual_result[2],
                )
            expected_result = ('a', 0, None)
            self.assertEqual(actual_result, expected_result)
    
        def test_find_node_by_value(self):
            """
            test_find_node_by_value\n
            주어진 값에 해당하는 노드를 찾을 수 있는지 테스트.
            """
            # test1
            test_value = 'b'
            actual_result = self.linked_list.findNodeByValue(test_value)
            actual_result = (
                actual_result[0].value, 
                actual_result[1],
                actual_result[2].value
                )
            expected_result = ('b', 1, 'a')
            self.assertEqual(actual_result, expected_result)
    
            # test2
            test_value = 'e'
            actual_result = self.linked_list.findNodeByValue(test_value)
            expected_result = None
            self.assertEqual(actual_result, expected_result)
    
            # test3
            test_value = 'a'
            actual_result = self.linked_list.findNodeByValue(test_value)
            actual_result = (
                actual_result[0].value, 
                actual_result[1],
                actual_result[2],
                )
            expected_result = ('a', 0, None)
            self.assertEqual(actual_result, expected_result)
            
        def test_insert_node(self):
            """
            test_insert_node\n
            특정 위치에 노드가 삽입되는지 테스트.
            """
            # test1
            target_index = 2
            new_node = Node('가')
            self.linked_list.insertNode(target_index, new_node)
            expected_result = '->'.join(['a', 'b', '가', 'c', 'd'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 5)
            
            # test2
            target_index = 0
            new_node = Node('나')
            self.linked_list.insertNode(target_index, new_node)
            expected_result = '->'.join(['나', 'a', 'b', '가', 'c', 'd'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 6)
            
        def test_delete_node_by_index(self):
            """
            test_delete_node_by_index\n
            지정된 인덱스 위치의 노드를 삭제할 수 있는지 테스트.
            """
            # test1
            target_index = 2
            self.linked_list.deleteNodeByIndex(target_index)
            expected_result = '->'.join(['a', 'b', 'd'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 3)
    
            # test2
            target_index = 0
            self.linked_list.deleteNodeByIndex(target_index)
            expected_result = '->'.join(['b', 'd'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
            # test3
            target_index = 5
            with self.assertRaises(IndexError):
                self.linked_list.deleteNodeByIndex(target_index)
    
        def test_delete_node_by_value(self):
            """
            test_delete_node_by_value\n
            주어진 값과 일치하는 값을 가지는 노드를 삭제하는지 테스트.
            """
            # test1
            target_value = 'c'
            self.linked_list.deleteNodeByValue(target_value)
            expected_result = '->'.join(['a', 'b', 'd'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 3)
    
            # test2
            target_value = 'a'
            self.linked_list.deleteNodeByValue(target_value)
            expected_result = '->'.join(['b', 'd'])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
            # test3
            target_value = 'a'
            with self.assertRaises(TypeError):
                self.linked_list.deleteNodeByValue(target_value)
    
    if __name__ == '__main__':
        unittest.main()
    ```
    
    ```python
    ........
    ----------------------------------------------------------------------
    Ran 8 tests in 0.001s
    
    OK
    ```
    
- 키-값 쌍 데이터를 담는 노드에 대한 연결리스트
    
    ```python
    """
    키-값 형태의 데이터를 담을 수 있는 연결 리스트 구현.
    """
    from abc import *
    
    class _Node(metaclass=ABCMeta):
        @abstractmethod
        def __init__(self, key=None, value=None, pointer=None):
            pass
    
    class Node(_Node):
        def __init__(
                self, 
                key=None, 
                value=None, 
                pointer: _Node | None = None
            ) -> None:
            """
            하나의 노드를 구현하는 클래스.\n
            key: key-value의 key
            value: key-value의 value
            pointer: 현재 노드의 다음 노드를 가리키는 포인터.
            포인터에는 다음 노드의 메모리 주소값을 저장하므로
            해당 매개변수는 다음 Node 객체를 대입받아야 한다.
            """
            self.key = key
            self.value = value
            self.pointer = pointer
    
        def getKeyValueData(self):
            return (self.key, self.value)
    
    class LinkedList():
        def __init__(self) -> None:
            """
            self.head_pointer: 맨 앞 노드를 가리키는 포인터. Node 객체를 받는다.\n
            self.tail_pointer: 맨 뒤 노드를 가리키는 포인터. Node 객체를 받는다.\n
            self.length: 연결 리스트 내 총 노드의 개수.
            """
            self.head_pointer: Node | None = None
            self.tail_pointer: Node | None = None
            self.length = 0
    
        def clear(self):
            """
            연결 리스트를 비운다.
            즉, 모든 노드를 삭제한다.
            """
            self.head_pointer: Node | None = None
            self.tail_pointer: Node | None = None
            self.length = 0
    
        def getLength(self):
            """
            현재 연결 리스트 내 총 노드 수 반환.
            """
            return self.length
        
        def __repr__(self):
            """
            현재 연결 리스트를 출력.
            연결 리스트가 비어있으면 <Empty> 출력.
            """
            linked_list = []
            current_node: Node | None = self.head_pointer
            while current_node is not None:
                key_ = current_node.key
                value_ = current_node.value
                if type(key_) != str:
                    key_ = str(key_)
                if type(value_) != str:
                    value_ = str(value_)
                linked_list.append(str((key_, value_)))
                current_node = current_node.pointer
            return ' -> '.join(linked_list) if linked_list else "<Empty>"
        
        def addNodeFront(self, new_node: Node):
            """
            연결 리스트의 맨 앞에 새 노드를 삽입.\n
            만약 기존 노드의 키와 같은 키를 입력하는 경우, 
            기존 키의 값을 새 노드의 값으로 바꾼다. 
            """
            try:
                old_node = self.findNodeByKey(new_node.key)[0]
            except TypeError:
                # findNodeByKey() 의 반환값이 애초에 None인 경우.
                pass
            else:
                if old_node.key == new_node.key:
                    old_node.value = new_node.value
                    return
            
            if self.head_pointer is None:
                # 연결 리스트에 아무런 노드도 없는 경우.
                self.head_pointer = new_node
                self.tail_pointer = new_node
            else:
                # 연결 리스트에 기존 노드들이 존재하는 경우.
                new_node.pointer = self.head_pointer
                self.head_pointer = new_node
            self.length += 1
    
        def addNodeBack(self, new_node: Node):
            """
            연결 리스트의 맨 뒤에 새 노드 삽입.\n
            만약 기존 노드의 키와 같은 키를 입력하는 경우, 
            기존 키의 값을 새 노드의 값으로 바꾼다.
            """
            try:
                old_node = self.findNodeByKey(new_node.key)[0]
            except TypeError:
                # findNodeByKey() 의 반환값이 애초에 None인 경우.
                pass
            else:
                if old_node.key == new_node.key:
                    old_node.value = new_node.value
                    return
    
            if self.tail_pointer is None:
                # 연결 리스트에 아무 노드도 없는 경우.
                self.tail_pointer = new_node
                self.head_pointer = new_node
            else:
                # 연결 리스트에 기존 노드들이 존재하는 경우.
                self.tail_pointer.pointer = new_node
                self.tail_pointer = new_node
            self.length += 1
    
        def findNodeByIndex(self, index: int) -> tuple[Node, int, Node | None]:
            """
            인덱스로 찾고자 하는 노드 반환.
            인덱스의 시작 번호는 0이다.
            현재 연결 리스트의 노드 개수보다 더 큰 인덱스 값 대입 시,
            또는 인덱스에 음수 입력 시
            IndexError 예외 발생.
            """
            if self.length < index + 1 or index < 0:
                raise IndexError("연결 리스트의 길이에서 벗어나는 인덱스를 입력하였습니다.")
            cur_i = 0
            target_node = self.head_pointer
            prev_pointer = self.head_pointer
            while cur_i != index:
                prev_pointer = target_node
                target_node = target_node.pointer
                cur_i += 1
            if index == 0:
                prev_pointer = None
            return (target_node, index, prev_pointer)
        
        def findNodeByKey(self, target_key) -> tuple[Node, int, Node | None] | None:
            """
            찾고자 하는 값이 특정 노드의 값과 같은 경우 그 노드와 그 노드의 인덱스 반환.
            찾고자 하는 노드가 없다면 None을 반환.
            """
            current_node = self.head_pointer
            
            # 현재 선택된 노드의 이전 노드를 가리키는 포인터
            prev_pointer = self.head_pointer
    
            target_node = None
            target_index = 0
            while True:
                if current_node is None:
                    break
                if current_node.key == target_key:
                    target_node = current_node
                    if target_index == 0:
                        prev_pointer = None
                    break
                prev_pointer = current_node
                current_node = current_node.pointer
                target_index += 1
            if target_node is None:
                return None
            else:
                return (target_node, target_index, prev_pointer)
            
        def insertNode(self, index: int, new_node: Node):
            """
            연결 리스트에서 지정된 인덱스 위치에 새 노드 삽입.\n
            만약 기존 노드의 키와 같은 키를 입력하는 경우, 
            기존 키의 값을 새 노드의 값으로 바꾼다. 위치는 바꾸지 않는다.
            """
            try:
                old_node = self.findNodeByKey(new_node.key)[0]
            except TypeError:
                pass
            else:
                if old_node.key == new_node.key:
                    old_node.value = new_node.value
                return
    
            old_node, old_index, prev_pointer = self.findNodeByIndex(index)
            new_node.pointer = old_node
            if prev_pointer is None:
                # 삽입하고자 하는 인덱스 위치가 0임.
                self.head_pointer = new_node
            else:
                prev_pointer.pointer = new_node
            self.length += 1
    
        def deleteNodeByIndex(self, index: int):
            """
            인덱스에 해당하는 노드 삭제.
            """
            target_node, target_index, prev_pointer = self.findNodeByIndex(index)
            if prev_pointer is None:
                # 삭제하고자 하는 인덱스 위치가 0임.
                self.head_pointer = target_node.pointer
            else:
                prev_pointer.pointer = target_node.pointer
            target_node.pointer = None
            self.length -= 1
    
        def deleteNodeByKey(self, target_key):
            """
            삭제하고자 하는 값과 일치하는 노드를 삭제.
            """
            try:
                search_result = self.findNodeByKey(target_key)
            except TypeError:
                print("TypeError: 찾고자 하는 값이 없습니다.")
            else:
                target_node, target_index, prev_pointer = search_result
            
            if target_index == 0:
                self.head_pointer = target_node.pointer
            else:
                prev_pointer.pointer = target_node.pointer
            target_node.pointer = None
            self.length -= 1
    
        def getValue(self, key):
            """
            key가 주어지면 그에 해당하는 value를 반환.
            해당하는 value가 없으면 None 반환.
            """
            try:
                target_data = self.findNodeByKey(key)[0].value
            except TypeError:
                return None
            return target_data
        
        def getAllData(self) -> list[tuple]:
            """
            연결 리스트 내 모든 데이터를 반환.\n
            반환값 형태: [(key1, value1), (key2, value2), ...]
            """
            current_node = self.head_pointer
            all_data = []
            while current_node is not None:
                all_data.append((current_node.key, current_node.value))
                current_node = current_node.pointer
            return all_data
    
    if __name__ == '__main__':
        linked_list = LinkedList()
        print(linked_list)
    
        linked_list.addNodeFront(Node('사과', 'apple'))
        linked_list.addNodeFront(Node('바나나', 'banana'))
        print(linked_list)
        print(linked_list.getLength())
    ```
    
    다음은 위 파일을 테스트하는 파일이다.
    
    ```python
    import unittest
    from linked_list_kv import Node, LinkedList
    
    def insertAllNodes(
            ll: LinkedList, 
            kv_data: list[tuple],
            insert_mode: str
        ):
        """
        여러 데이터들을 연결 리스트에 한 번에 넣는다.\n
        insert_mode)
        'f': addNodeFront() 메서드 사용.
        'b': addNodeBack() 메서드 사용.
        """
        for one_data in kv_data:
            if insert_mode == 'f':
                ll.addNodeFront(Node(one_data[0], one_data[1]))
            else:
                ll.addNodeBack(Node(one_data[0], one_data[1]))
    
    class TestMyLinkedList(unittest.TestCase):
        def setUp(self) -> None:
            self.linked_list = LinkedList()
            self.desc = self.shortDescription()
    
            # 테스트에 따른 fixture 설정.
            if self.desc in (
                'test_find_node_by_index', 'test_find_node_by_key',
                'test_get_value', 'test_insert_node',
                'test_delete_node_by_index', 'test_delete_node_by_key',
                'test_get_all_data',
                ):
                self.linked_list.clear()
                dataset = [
                    ('사과', 'apple'),
                    ('바나나', 'banana'),
                    ('자전거', 'cycle'),
                    ('지연', 'delay'),
                ]
                insertAllNodes(self.linked_list, dataset, 'b')
    
        def test_empty(self):
            """
            test_empty\n
            비어있는 연결 리스트 출력 테스트
            """
            self.assertEqual(self.linked_list.__repr__(), "<Empty>")
            self.assertEqual(self.linked_list.getLength(), 0)
    
        def test_clear(self):
            """
            test_clear\n
            연결 리스트를 비울 수 있는지 테스트.
            """
            dataset = [
                ('사과', 'apple'),
                ('바나나', 'banana'),
                ('자전거', 'cycle'),
            ]
            insertAllNodes(self.linked_list, dataset, 'f')
            expected_result = "('자전거', 'cycle') -> ('바나나', 'banana') -> ('사과', 'apple')"
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), len(dataset))
    
            self.linked_list.clear()
            self.assertEqual(self.linked_list.__repr__(), "<Empty>")
            self.assertEqual(self.linked_list.getLength(), 0)
    
        def test_add_node_front(self):
            """
            test_add_node_front\n
            노드 하나를 맨 앞에 삽입하는 데에 성공하는지 테스트.
            """
            # test1
            data1 = ('사과', 'apple')
            data_list = [data1]
            self.linked_list.addNodeFront(Node(data1[0], data1[1]))
            expected_result = ' -> '.join([str(('사과', 'apple'))])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 1)
    
            # test2
            data2 = ('바나나', 'banana')
            data_list.insert(0, data2)
            self.linked_list.addNodeFront(Node(data2[0], data2[1]))
            expected_result = ' -> '.join([str(data) for data in data_list])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
            # test3
            # 기존 노드의 키와 같은 키 입력 시 새로운 노드의 value로 바꾸는지 
            # 테스트.
            data3 = ('바나나', 'BANANA')
            data_list.remove(data2)
            data_list.insert(0, data3)
            self.linked_list.addNodeFront(Node(data3[0], data3[1]))
            expected_result = ' -> '.join([str(data) for data in data_list])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
        def test_add_node_back(self):
            """
            test_add_node_back\n
            노드 하나를 맨 뒤에 삽입하는 테스트.
            """
            # test1
            data1 = ('사과', 'apple')
            data_list = [data1]
            self.linked_list.addNodeBack(Node(data1[0], data1[1]))
            expected_result = ' -> '.join([str(('사과', 'apple'))])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 1)
    
            # test2
            data2 = ('바나나', 'banana')
            data_list.append(data2)
            self.linked_list.addNodeBack(Node(data2[0], data2[1]))
            expected_result = ' -> '.join([str(data) for data in data_list])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
            # test3
            # 기존 노드의 키와 같은 키 입력 시 새로운 노드의 value로 바꾸는지 
            # 테스트.
            data3 = ('바나나', 'BANANA')
            data_list.remove(data2)
            data_list.append(data3)
            self.linked_list.addNodeBack(Node(data3[0], data3[1]))
            expected_result = ' -> '.join([str(data) for data in data_list])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
        def test_find_node_by_index(self):
            """
            test_find_node_by_index\n
            주어진 인덱스의 노드를 찾을 수 있는지 테스트.
            """
            # test1
            index = 2
            actual_result = self.linked_list.findNodeByIndex(index)
            actual_result = (
                (actual_result[0].key, actual_result[0].value),
                actual_result[1],
                (actual_result[2].key, actual_result[2].value),
            )
            expected_result = (('자전거', 'cycle'), 2, ('바나나', 'banana'))
            self.assertEqual(actual_result, expected_result)
    
            # test2
            index = 0
            actual_result = self.linked_list.findNodeByIndex(index)
            actual_result = (
                (actual_result[0].key, actual_result[0].value),
                actual_result[1],
                actual_result[2],
            )
            expected_result = (('사과', 'apple'), 0, None)
            self.assertEqual(actual_result, expected_result)
    
            # test3
            index = -1
            with self.assertRaises(IndexError):
                self.linked_list.findNodeByIndex(index)
    
            # test4
            index = 4
            with self.assertRaises(IndexError):
                self.linked_list.findNodeByIndex(index)
    
        def test_find_node_by_key(self):
            """
            test_find_node_by_key\n
            주어진 키에 해당하는 노드를 찾을 수 있는지 테스트.
            """
            # test1
            test_key = '바나나'
            actual_result = self.linked_list.findNodeByKey(test_key)
            actual_result = (
                (actual_result[0].key, actual_result[0].value),
                actual_result[1],
                (actual_result[2].key, actual_result[2].value),
            )
            expected_result = (('바나나', 'banana'), 1, ('사과', 'apple'),)
            self.assertEqual(actual_result, expected_result)
    
            # test2
            test_key = '사과'
            actual_result = self.linked_list.findNodeByKey(test_key)
            actual_result = (
                (actual_result[0].key, actual_result[0].value),
                actual_result[1],
                actual_result[2],
            )
            expected_result = (('사과', 'apple'), 0, None,)
            self.assertEqual(actual_result, expected_result)
    
            # test3
            test_key = '전자'
            actual_result = self.linked_list.findNodeByKey(test_key)
            expected_result = None
            self.assertEqual(actual_result, expected_result)
    
        def test_insert_node(self):
            """
            test_insert_node\n
            특정 위치에 노드가 삽입되는지 테스트.
            """
            # test1
            test_index = 2
            new_node = Node('전자', 'electron')
            self.linked_list.insertNode(test_index, new_node)
            expected_result = ' -> '.join([
                "('사과', 'apple')",
                "('바나나', 'banana')",
                "('전자', 'electron')",
                "('자전거', 'cycle')",
                "('지연', 'delay')"
            ])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 5)
    
            # test2
            test_index = 0
            new_node = Node('라면', 'ramen')
            self.linked_list.insertNode(test_index, new_node)
            expected_result = ' -> '.join([
                "('라면', 'ramen')",
                "('사과', 'apple')",
                "('바나나', 'banana')",
                "('전자', 'electron')",
                "('자전거', 'cycle')",
                "('지연', 'delay')"
            ])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 6)
    
            # test3
            test_index = 10
            new_node = Node()
            with self.assertRaises(IndexError):
                self.linked_list.insertNode(test_index, new_node)
    
            # test4
            # 기존 노드의 키가 새로 삽입할 노드의 키가 같은 경우,
            # 기존 노드의 value를 새로 삽입할 노드의 value로 바꾸는 지 테스트.
            test_index = 4
            new_node = Node('라면', 'RAMEN')
            self.linked_list.insertNode(test_index, new_node)
            expected_result = ' -> '.join([
                "('라면', 'RAMEN')",
                "('사과', 'apple')",
                "('바나나', 'banana')",
                "('전자', 'electron')",
                "('자전거', 'cycle')",
                "('지연', 'delay')"
            ])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 6)
            
    
        def test_delete_node_by_index(self):
            """
            test_delete_node_by_index\n
            지정된 인덱스 위치의 노드를 삭제할 수 있는지 테스트.
            """
            # test1
            target_index = 2
            self.linked_list.deleteNodeByIndex(target_index)
            expected_result = ' -> '.join([
                "('사과', 'apple')",
                "('바나나', 'banana')",
                "('지연', 'delay')",
            ])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 3)
    
            # test2
            target_index = 0
            self.linked_list.deleteNodeByIndex(target_index)
            expected_result = ' -> '.join([
                "('바나나', 'banana')",
                "('지연', 'delay')",
            ])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
            # test3
            target_index = 5
            with self.assertRaises(IndexError):
                self.linked_list.deleteNodeByIndex(target_index)
    
        def test_delete_node_by_key(self):
            """
            test_delete_node_by_key\n
            주어진 key와 일치하는 데이터를 가지는 노드를 삭제하는지 테스트.
            """
            # test1
            target_key = '바나나'
            self.linked_list.deleteNodeByKey(target_key)
            expected_result = ' -> '.join([
                "('사과', 'apple')",
                "('자전거', 'cycle')",
                "('지연', 'delay')",
            ])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 3)
    
            # test2
            target_key = '사과'
            self.linked_list.deleteNodeByKey(target_key)
            expected_result = ' -> '.join([
                "('자전거', 'cycle')",
                "('지연', 'delay')",
            ])
            self.assertEqual(self.linked_list.__repr__(), expected_result)
            self.assertEqual(self.linked_list.getLength(), 2)
    
            # test3
            target_key = '사과'
            with self.assertRaises(TypeError):
                self.linked_list.deleteNodeByKey(target_key)
    
        def test_get_value(self):
            """
            test_get_value\n
            key가 주어지면 그에 해당하는 value를 반환.
            """
            # test1
            test_key = '사과'
            actual_result = self.linked_list.getValue(test_key)
            expected_result = 'apple'
            self.assertEqual(actual_result, expected_result)
    
            # test2
            test_key = '전자'
            actual_result = self.linked_list.getValue(test_key)
            expected_result = None
            self.assertEqual(actual_result, expected_result)
    
        def test_get_all_data(self):
            """
            test_get_all_data\n
            연결 리스트 내 모든 데이터 출력 테스트.
            """
            actual_result = self.linked_list.getAllData()
            expected_result = [
                ('사과', 'apple'),
                ('바나나', 'banana'),
                ('자전거', 'cycle'),
                ('지연', 'delay'),
            ]
            self.assertEqual(actual_result, expected_result)
    
    if __name__ == '__main__':
        unittest.main()
    ```
    
    ```python
    ...........
    ----------------------------------------------------------------------
    Ran 11 tests in 0.002s
    
    OK
    ```
    

# 보충 설명

연결 리스트의 크기는 동적일 수 있어서, 실행 중에 저장할 데이터들의 수를 알 수 없을 때 사용할 수 있다. 

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2] [[자료구조] 연결리스트(Linked List)의 개념, 이해 \| 단순연결리스트(Singly linked list) C언어 구현, 소스코드](https://code-lab1.tistory.com/2)

[3] [연결 리스트](https://ko.wikipedia.org/wiki/연결_리스트)