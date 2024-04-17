---
title: "[자료구조] 스택(stack)"
category: "data structure"
tag: ["python", "자료구조", "stack", "스택", "data structure"]
---
스택은 배열의 한 쪽 끝에서만 데이터에 접근할 수 있는 선형 자료구조이다. (스택은 파이썬의 list 자료형과 달리, 인덱싱을 통해 중간에 있는 요소를 조회, 삽입할 수 없다) 스택은 한 쪽 끝이 막힌 긴 원통형 멘토스 포장 용기라고 볼 수 있겠다. 긴 원기둥 구조에 한 쪽 끝은 막혀 있고 한 쪽은 뚫려 있어 그 쪽을 통해 멘토스(데이터)가 삽입되거나 바깥으로 뺄 수 있다. 이러한 선천적 구조로 인해 마지막으로 들어간 데이터가 맨 처음으로 빠져나갈 수 있는 후입선출(last in, first out, LIFO) 구조이다. 

스택이 가질 수 있는 동작에는 다음이 존재하며, 해당 동작들의 시간복잡도는 모두 O(1)이다.

- push : 스택 맨 끝(맨 위)에 요소를 삽입한다. (멘토스 한 알 추가)
- pop : 스택 맨 끝 요소를 반환하면서 동시에 스택에서 해당 항목을 제거한다. (맨 위에 있는 멘토스 한 알을 꺼낸다)
- top/peek : 스택 맨 끝 요소를 조회한다. (맨 끝에 어떤 색깔의 멘토스가 있는지 확인한다)
- empty : 스택이 비어있는지를 확인한다. (멘토스 다 먹었나?)
- size: 스택 크기를 확인한다. (멘토스 얼마나 있나?)

파이썬에서는 리스트의 append(), pop() 메서드를 이용하여 스택을 구현할 수 있다. 다음은 앞서 언급한 스택의 모든 동작들을 구현하는 예제이다.

```python
from typing import Any

class StaticStack():
    """
    스택의 크기를 고정시킨 스택.
    """
    def __init__(self, size_: int):
        """
        size_ : 스택 최대 크기 설정.
        """
        self.stack: list = []
        self.size_: int = size_

    def is_empty(self) -> bool:
        return self.stack == []

    def push(self, one_element) -> bool:
        """
        맨 끝에 요소 삽입. 
        정해진 size_만큼만 삽입할 수 있다.\n
        return type)\n
        True: 삽입 성공.\n
        False: 삽입 실패.\n
        """
        if self.getLength() == self.size_:
            print("스택의 최대 크기에 도달하여 요소를 추가할 수 없습니다.")
            return False
        self.stack.append(one_element)
        return True

    def pop(self) -> Any | None:
        """
        맨 끝 요소 추출 후 스택에서 제거.
        """
        try:
            return self.stack.pop()
        except IndexError:
            print("스택이 비어있어 반환할 아이템이 없습니다.")
            return
        
    def peek(self):
        """
        맨 끝 요소 조회. 
        """
        return self.stack[-1]
    
    def getLength(self) -> int:
        """
        현재 스택에 들어있는 요소들의 수 반환.
        """
        return len(self.stack)
    
    def setNewSize(self, new_size: int):
        """
        스택의 최대 크기 재설정. 
        현재 스택에 들어있는 요소들의 수보다 더 작게 설정하지 못하도록 함.
        """
        if new_size < self.getLength():
            print("스택 내 항목들의 수보다 더 작게 설정할 수 없습니다.")
            print(f"현재 스택 내 항목들의 수: {self.getLength()}")
            return
        self.size_ = new_size

    def clear(self):
        """
        스택을 모두 비운다.
        """
        del self.stack
        self.stack = []
    
    def __repr__(self):
        return repr(self.stack)
    

class DynamicStack(StaticStack):
    """
    크기에 제한을 두지 않고 무한정 크기를 동적으로 늘릴 수 있는 스택.
    """
    def __init__(self):
        self.stack: list = []

    def push(self, one_element):
        """
        맨 끝에 요소 삽입. 
        """
        self.stack.append(one_element)

    def setNewSize(self):
        """
        DynamicStack에서는 사용하지 않는 메서드. 
        """
        pass

def test_static_stack():
    stack = StaticStack(10)
    data = ["초록색", "분홍색", "노란색", '하늘색', '흰색']
    print("멘토스를 준비합니다.")
    for item in data:
        stack.push(item)
    print(f"멘토스 준비 완료. 상태: {stack}")
    print(f"현재 멘토스 양: {stack.getLength()}")
    print(f"맨 마지막에 있는 멘토스의 색은? {stack.peek()}")
    one_item = stack.pop()
    print(f"맨 마지막에 있는 {one_item} 멘토스를 꺼내 먹었다.")
    print(f"맨 마지막에 있는 멘토스의 색은? {stack.peek()}")
    print(f"멘토스 다 먹었나? {stack.is_empty()}")
    print(stack)
    
    
if __name__ == '__main__':
    test_static_stack()
```

```python
멘토스를 준비합니다.
멘토스 준비 완료. 상태: ['초록색', '분홍색', '노란색', '하늘색', '흰색']
현재 멘토스 양: 5
맨 마지막에 있는 멘토스의 색은? 흰색
맨 마지막에 있는 흰색 멘토스를 꺼내 먹었다.
맨 마지막에 있는 멘토스의 색은? 하늘색
멘토스 다 먹었나? False
['초록색', '분홍색', '노란색', '하늘색']
```

- 테스트 코드
    
    ```python
    import unittest
    from my_stack import StaticStack
    
    class TestStack(unittest.TestCase):
        def setUp(self) -> None:
            self.st = StaticStack(10)
            self.desc = self.shortDescription()
            self.dataset = ['사과', '바나나', '당근', '참외']
    
            if self.desc == "need_dataset":
                for data in self.dataset:
                    self.st.push(data)
    
        def test_is_empty(self):
            """
            빈 스택 테스트.
            """
            self.assertEqual(self.st.__repr__(), '[]')
            self.assertEqual(self.st.is_empty(), True)
            self.assertEqual(self.st.getLength(), 0)
    
        def test_push(self):
            """
            스택에 항목 삽입 테스트.
            """
            # test1
            data = '오렌지'
            self.st.push(data)
            self.assertEqual(self.st.__repr__(), "['오렌지']")
            self.assertEqual(self.st.getLength(), 1)
            self.assertEqual(self.st.is_empty(), False)
    
            # test2
            for data in self.dataset:
                self.st.push(data)
            expected_result = "['오렌지', '사과', '바나나', '당근', '참외']"
            self.assertEqual(self.st.__repr__(), expected_result)
            self.assertEqual(self.st.getLength(), 5)
            self.assertEqual(self.st.is_empty(), False)
    
            # test3
            st2 = StaticStack(0)
            actual_result = st2.push('코끼리')
            self.assertEqual(actual_result, False)
            self.assertEqual(st2.is_empty(), True)
    
        def test_pop(self):
            """
            need_dataset\n
            pop 테스트.
            """
            # test1
            st2 = StaticStack(5)
            self.assertEqual(st2.pop(), None)
    
            # test2
            self.assertEqual(self.st.pop(), '참외')
            self.assertEqual(self.st.getLength(), 3)
    
        def test_peek(self):
            """
            need_dataset\n
            스택 맨 끝 항목 조회 기능 테스트.
            """
            # test1
            st2 = StaticStack(0)
            with self.assertRaises(IndexError):
                st2.peek()
    
            # test2
            self.assertEqual(self.st.peek(), '참외')
            self.assertEqual(self.st.getLength(), 4)
    
        def test_set_new_size(self):
            """
            need_dataset\n
            setNewSize 메서드 테스트.
            """
            # test1
            self.st.setNewSize(20)
            self.assertEqual(self.st.size_, 20)
    
            # test2
            self.st.setNewSize(5)
            self.assertEqual(self.st.size_, 5)
    
            # test3
            self.st.setNewSize(3)
            self.assertEqual(self.st.size_, 5)
    
        def test_clear(self):
            """
            need_dataset\n
            스택을 모두 비우는 테스트.
            """
            # test1
            self.st.clear()
            self.assertEqual(self.st.is_empty(), True)
            self.assertEqual(self.st.getLength(), 0)
            self.assertEqual(getattr(self.st, 'stack'), [])
            self.assertEqual(self.st.__repr__(), '[]')
    
    if __name__ == '__main__':
        unittest.main()
    ```
    
    ```python
    ...스택이 비어있어 반환할 아이템이 없습니다.
    .스택의 최대 크기에 도달하여 요소를 추가할 수 없습니다.
    .스택 내 항목들의 수보다 더 작게 설정할 수 없습니다.
    현재 스택 내 항목들의 수: 4
    .
    ----------------------------------------------------------------------
    Ran 6 tests in 0.001s
    
    OK
    ```
    

스택은 깊이 우선 탐색 (DFS)에서 유용하게 사용된다고 한다. 깊이 우선 탐색은 나중에 다른 페이지에서 다룰 예정.

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)