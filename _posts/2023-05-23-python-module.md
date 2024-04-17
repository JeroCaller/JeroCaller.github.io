---
title: "[지식/이론][python] 모듈(module)"
category: knowledge
tag: ["python", "knowledge", "module"]
---
# 개요

파이썬에서 모듈은 def 키워드를 사용하여 정의한다. def 실행 시 함수의 객체와 참조가 동시에 생성된다. 반환값을 정의하지 않으면 자동으로 None이 반환되게끔 한다. 이렇게 반환값이 없는 함수를 프로시저(procedure)라 한다. 

# 스택(stack)과 활성화 레코드(activation record)

함수가 호출될 때마다 활성화 레코드가 생성된다. 활성화 레코드에는 함수의 정보가 기록되며, 여기에는 반환값, 매개변수, 지역 변수, 반환 주소 등이 기록된다. 그리고 이러한 정보를 스택에 저장한다. 

활성화 레코드의 처리 순서는 다음과 같다.

1. 함수의 실제 매개변수를 스택에 저장(push)한다.
2. 반환 주소를 스택에 저장한다.
3. 스택의 최상위 인덱스를 함수의 지역 변수의 개수만큼 늘린다. 
4. 함수로 건너뛴다(jump).

반대로, 활성화 레코드를 풀어내는(unwinding) 절차는 다음과 같다.

1. 스택의 최상위 인덱스는 지역 변수의 수(함수에 소비된 총 메모리양과 같다)만큼 감소시킨다. 
2. 반환 주소를 스택에서 빼낸다(pop).
3. 스택의 최상위 인덱스는 함수의 실제 매개변수의 수만큼 감소시킨다.

# 모듈의 기본값

앞서 함수 호출 시 활성화 레코드가 생성되어 함수의 정보들을 저장한다고 배웠다. 이로 인해 벌어지는 상황이 있다.

모듈 생성 시 함수 또는 메서드의 매개변수에 가변 객체를 기본값으로 설정하면 안된다. 

매개변수의 기본값을 가변 객체로 설정함으로써 나타나는 현상을 다음 코드에서 보여주고 있다.

```python
import unittest

def only_one_append(element, element_list=[]):
    """
    빈 리스트에 딱 하나의 요소만 삽입하게 해주는 함수.
    """
    element_list.append(element)
    return element_list

class TestMyFunc(unittest.TestCase):
    def test_one(self):
        element = 'a'
        result = only_one_append(element)
        expect_result = ['a']
        self.assertEqual(result, expect_result)

    def test_two(self):
        element = 'b'
        result = only_one_append(element)
        expect_result = ['b']
        self.assertEqual(result, expect_result)

if __name__ == '__main__':
    unittest.main()
```

```python
.F
======================================================================
FAIL: test_two (__main__.TestMyFunc)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 22, in test_two
    self.assertEqual(result, expect_result)
AssertionError: Lists differ: ['a', 'b'] != ['b']

First differing element 0:
'a'
'b'

First list contains 1 additional elements.
First extra element 1:
'b'

- ['a', 'b']
+ ['b']

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=1)
```

해당 함수의 매개변수인 element_list에는 기본값으로 빈 리스트 []가 할당되었다. 이 때 ‘a’를 해당 함수에 대입하여 호출하면 element_list 매개변수에는 [’a’]라고 저장된다. 앞서 설명한 것처럼 함수는 호출될 때마다 해당 매개변수의 정보가 활성화 레코드에 저장된다. 따라서 그 다음에 ‘b’를 대입하여 호출하려고 하면 기존의 element_list = [’a’]에 ‘b’가 추가되어버린다. 원하던 결과인 [’b’]가 나오지 않는 이유이다. 

이를 해결하려면 매개변수의 기본값으로 가변 객체를 설정하는 것이 아니라 가변 객체를 함수 내에서 정의하는 형식으로 사용해야 한다.

```python
import unittest

def only_one_append(element, element_list=None):
    """
    빈 리스트에 딱 하나의 요소만 삽입하게 해주는 함수.
    """
    if element_list is None:
        element_list = []
    element_list.append(element)
    return element_list

class TestMyFunc(unittest.TestCase):
    def test_one(self):
        element = 'a'
        result = only_one_append(element)
        expect_result = ['a']
        self.assertEqual(result, expect_result)

    def test_two(self):
        element = 'b'
        result = only_one_append(element)
        expect_result = ['b']
        self.assertEqual(result, expect_result)

if __name__ == '__main__':
    unittest.main()
```

```python
..
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK
```

# \_\_init\_\_.py 파일

([Module, import 선언문, package](/python/module-import-pkg/) 문서의 \_\_init\_\_.py 부분에서도 확인할 수 있다)

파이썬에서 패키지(package)는 모듈과 \_\_init\_\_.py 파일이 있는 디렉토리로, \_\_init\_\_.py 파일은 파이썬이 해당 디렉토리를 패키지로 인식시키기 위한 요소로서 쓰인다. 

(파이썬 3.3 버전 이상부터는 해당 파일이 없어도 패키지로 인식한다고 한다)

패키지 내에 존재하는 모듈을 import 하려면 다음의 형식대로 코드를 작성하면 된다.

```python
import 디렉토리명.모듈명
```

\_\_init\_\_.py 파일은 그저 패키지 인식 용도로만 쓸 것이라면 그 내용은 비워둬도 된다. 그런데 해당 파일은 그 외에도 여러 기능을 할 수 있다.

- \_\_init\_\_.py 파일에 변수 또는 함수를 정의함으로써 패키지 내에서 공통적으로 해당 변수 및 함수를 사용할 수 있게 해준다.
- \_\_init\_\_.py 파일에 패키지 내 다른 모듈들을 미리 import하여 해당 패키지 내 모듈에 대한 접근을 더 쉽게 해줄 수 있다.
- 패키지를 처음 호출 시 맨 처음에 실행되어야 할 초기화 코드를 작성할 수 있다. 단, \_\_init\_\_.py 파일 내에 작성한 초기화 코드는 처음 한 번만 실행되고, 이후에는 호출해도 실행되지 않는다.
- \_\_init\_\_.py 파일 내에 \_\_all\_\_ = [’파일명1’, …] 과 같이 \_\_all\_\_ 변수를 정의하고 패키지 내 특정 디렉토리의 모듈명을 적어주면 from 디렉토리명 import * 코드를 사용할 수 있다. \_\_all\_\_ 변수를 사용하지 않으면 다른 곳에서 해당 코드로 모듈 import 시 NameError 예외가 발생한다.

# sys 모듈

- sys.path: 인터프리터가 모듈을 검색할 경로를 문자열 형태로 담은 리스트이다. PYTHONPATH 환경변수 또는 내장된 기본값 경로로 초기화한다. 환경변수 수정 시 모듈 경로를 추가하거나 임시적으로 모듈 경로를 추가할 수 있다.
    
    ```python
    import sys
    print(sys.path)
    ```
    
- sys.argv: shell을 통해 입력된 명령 줄에 전달된 인수를 프로그램 내에서 사용할 수 있도록 해준다. 다음은 터미널 창으로부터 명령 줄로 입력받은 인수들을 출력해주는 코드이다.
    
    ```python
    import sys
    
    def print_args():
        for arg in sys.argv[:]:
            print(arg)
    
    if __name__ == '__main__':
        print_args()
    ```
    
    ```bash
    python study.py hi hello
    study.py
    hi
    hello
    ```
    

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2]

[05-3 패키지](https://wikidocs.net/1418#__init__py)