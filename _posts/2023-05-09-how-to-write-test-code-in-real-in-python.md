---
layout: single
title:  "[Python] 테스트 코드 작성 실전"
categories: ["Python", "test"]
tag: ["Python", "test", "unit test", "side effects", "SRP", "refactoring", "리팩토링", "통합 테스트", "단위 테스트", "unittest"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

# 테스트 코드 실습 예제

실제로 간단한 테스트 코드 작성 예제를 살펴보겠다. 

실습을 위해, 다음과 같이 패키지를 구성하였다.

```
project_test/
    my_functions/
        __init__.py
```

project_test라는 루트 폴더를 만들고, 그 안에 my_functions라는 서브 폴더를 만들었다. 그리고 그 폴더 안에 \_\_init\_\_.py 라는 파이썬 파일을 만들었다. \_\_init\_\_.py 파일 안에 다음의 코드를 저장하였다.

```python
def add_all(args):
    total = 0
    for num in args:
        total += num
    return total
```

이제 해당 함수를 테스트 하는 코드를 작성하고자 한다. 

# 테스트 코드 파일은 어디에 놓지?

테스트 코드 파일인 test_my_functions.py을 만들고자 한다. 해당 파일은 패키지 안 어디에 두는게 좋을까? 테스트 파일은 실제 앱의 기능들을 import하여 그 기능들을 테스트할 것이므로 이 과정을 간편히 하기 위해 루트 폴더 안에 저장해두는 게 좋을 것이다. 그래서 다음과 같이 구성할 수 있다.

```
project_test/
    my_functions/
        __init__.py
    test_my_functions.py
```

만약 하나의 테스트 파일 내에 모든 테스트 코드를 작성하기 버거울 정도로 프로젝트 규모가 커진다면 일단 여러 개의 테스트 파일로 나눠 코드를 작성한 후, 아예 이름이 test로 시작되는 파이썬 파일들을 따로 모아둘 폴더를 루트 폴더 내에 만들고, 그 폴더 안에 모든 테스트 파일들을 몰아 넣는 식으로 구성할 수도 있다. 다음은 그 예제이다.

```
project_test/
    my_functions/
        __init__.py
    test_folder/
        test_my_functions.py
        test_something.py
        ...
```

프로젝트 규모와 목적에 따라 더 하위의 폴더들을 만들어 거기에 테스트 파일들을 위치하는 경우도 있다고 한다. 

# 테스트 코드 설계

테스트 코드 작성 전에 무엇을 어떻게 테스트 할 것인지 먼저 고려하는 것이 좋다.

- 무엇을 테스트할 것인가?
- 단위 테스트를 할 것인가 아니면 통합 테스트를 할 것인가?

이를 결정한 다음에는 테스트 코드의 구조를 다음과 같이 짠다.

- 테스트 할 함수의 입력값을 따로 만든다.
- 테스트 할 함수에 해당 입력값을 대입하여 실행한 후의 반환값을 변수에 담는다.
- 해당 반환값을 기대값과 비교하는 식으로 테스트한다.

이 예제에서는 add_all() 함수를 테스트해볼 것이다. 테스트로 고려할만 아이디어에는 다음이 있을 것이다.

- list나 tuple, set 내의 정수들에 대해서도 덧셈이 가능할까?
- 실수도 계산해주나?
- 숫자가 아니라 문자열과 같은 다른 자료형의 데이터가 들어간다면?
- 숫자가 음수여도 계산해주나?

이러한 생각들을 모두 마쳤다면 테스트 코드를 작성한다. 다음은 입력값이 모두 음수인 경우에도 잘 작동하는지 테스트하는 코드이다. unittest 모듈을 이용하였다.

```python
import unittest
from my_functions import add_all  #1

class TestAddCalc(unittest.TestCase):
    def test_negative_sum(self):
        """
        음수도 총합을 구하는데 사용할 수 있는지 테스트
        """
        data = [-1, -2, -3]   #2
        result = add_all(data)   #3
        self.assertEqual(result, -6)  #4

if __name__ == '__main__':
    unittest.main()  #5
```

먼저 #1에서 테스트할 코드를 import하였다. 그 다음, unittest.TestCase를 상속하는 테스트를 위한 클래스를 정의하였고 그 안에 테스트 메서드를 정의하였다. 해당 테스트 메서드 안에서는 테스트 할 함수에 대입할 입력값들을 미리 설정한 후(#2), 이를 해당 함수에 대입하여 해당 함수의 반환값을 변수에 담도록 하였다(#3). 그 후, 해당값을 기대값과 비교하여 두 값이 동일한지 assert* 메서드를 통해 그 테스트 코드를 작성하였다(#4). 그 후, 터미널 창에서 python test_my_functions.py를 입력하면 테스트 코드를 실행시켜줄 command-line entry point인 unittest.main()을 #5 자리에 작성하였다. 터미널 창을 이용해 해당파일 실행 시 터미널 창에서 unittest.main()을 호출하여 테스트 코드를 실행시킬 것이다.

# unittest의 assert 메서드

테스트할 실제 함수의 반환값과 기대값을 비교함으로써 테스트 할 함수의 결과값을 입증하는 것을 assertion이라고 한다. unittest 모듈에는 이러한 assert와 관련된 여러 메서드들을 제공한다. 다음은 그 예이다.

| 메서드 | 의미 |
| --- | --- |
| .assertEqual(a, b) | a == b |
| .assertTrue(a) | bool(a) is True |
| .assertFalse(a) | bool(a) is False |
| .assertIs(a, b) | a is b |
| .assertIsNone(a) | a is None |
| .assertIn(a, b) | a in b |
| .assertIsInstance(a, b) | isinstance(a, b) |

# Side effects

어떤 코드를 테스트하기 위해 그 코드를 실행하기만 해도 클래스의 속성이 달라진다든가 패키지 내 파일 구성이 달라지든가 등 주변 환경이 달라질 수 있다. 이러한 영향을 side effects라고 한다. 코드를 테스트하기 위해 해당 코드를 실행하기만 해도 side effects가 일어나기에 테스트에서 중요한 고려요소이다. 테스트 코드 자체에 side effects도 테스트할 항목으로 넣었는지, 아니면 테스트 중에는 side effects가 일어나길 원치 않는지에 대해서도 고려해야한다. 테스트를 여러 번 해야 할 경우, 같은 테스트를 해도 side effects에 의해 그 결과가 달라질 수도 있기 때문이다.

프로그래밍에서의 side effects 개념에 대한 자세한 설명은 refereces [2] 참고.

만약, 어떤 코드를 테스트 했는데 side effects가 너무 많이 발생한다면, 그건 Single Responsibility Principle(SRP)를 어긴 것이 아닌지 고려해봐야한다. 하나의 코드는 하나의 기능만을 해야한다는 원리로, 테스트할 함수가 하나의 함수임에도 여러 기능을 하고 있는 건 아닌지 고려해봐야한다는 것이다(하나의 함수, 하나의 기능, 일대일 대응). 만약 SRP를 어기고 있다면, 해당 함수를 하나의 기능만 수행하도록 refactoring하자. (refactoring: 코드의 가독성을 높이고 유지보수의 편리함 증진을 위해 결과의 변경없이 코드의 구조만 재조정하는 행위를 뜻함)

만약 side effects가 많은 앱을 테스트하고자 한다면 다음의 방법들 중 하나를 택할 수 있다.

- SRP 원칙을 따르도록 코드를 리팩토링하기
- 단위 테스트 대신 통합 테스트하기

# 테스트 실행하기

앞서 작성한 테스트 코드를 이제 실행시켜보자. 더 많은 테스트 코드를 작성하기 전에 기존에 작성한 테스트 코드를 먼저 실행시켜 확인한 후에 다음 코드를 작성하는 것이 좋다. 

터미널 창에서 테스트 코드를 실행시키는 방법에는 여러 가지가 있다. 다음과 같은 명령어들이 유효하다.

```bash
python test_my_functions.py
```

```bash
python -m unittest test_my_functions
```

```bash
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

명령어에 추가할 수 있는 옵션 중 하나로 -v가 있다. 

```bash
python -m unittest -v test_my_functions
test_negative_sum (test_my_functions.TestAddCalc)
음수도 총합을 구하는데 사용할 수 있는지 테스트 ... ok

----------------------------------------------------------------------
Ran 1 test in 0.001s

OK
```

-v 옵션을 추가하면 위 출력결과에서 볼 수 있듯, 테스트 모듈 이름과 테스트 클래스 명, 그리고 테스트 메서드 이름 및 해당 메서드의 docstring도 출력하여 볼 수 있다. 

만약 일일이 테스트 파일 이름을 치는 게 번거롭거나 어렵다면, “discover” 옵션을 추가하여 컴퓨터가 알아서 test 이름으로 시작하는 .py 테스트 파일을 찾아서 실행시키도록 할 수 있다.

```bash
python -m unittest discover
```

만약 테스트 파일들이 여러 개 있고, 이 파일들을 하나의 서브 폴더에 몰아 넣은 상태라면, -s 옵션을 추가한 뒤, 바로 뒤에 해당 폴더 이름을 삽입하면 된다. 다음은 테스트 파일들을 “test_folder”라는 폴더에 넣은 상태를 가정하고 작성한 명령어이다.

```bash
python -m unittest discover -s test_folder
```

그러면 모든 테스트 파일들을 하나씩 실행시켜 테스트할 것이다.

만약 테스트를 해볼 소스 코드의 위치가 루트 폴더에 있는 것이 아니라 서브 디렉토리에 있는 경우, -t 서브폴더명 옵션을 통해 정확히 어떤 소스 코드를 테스트할 것인지 알려줄 수 있다. 소스 코드가 src라는 하위 폴더에 있는 경우 다음의 명령줄을 입력할 수 있다.

```bash
python -m unittest discover -s test_folder -t src
```

현재 디렉토리를 src로 바꾼 후, test_folder 폴더 내의 모든 test*.py 파일들을 실행시켜 테스트를 진행할 것이다.

# 테스트 결과 해석하기

이번에는 테스트 결과를 해석해보자. 그 전에 \_\_init\_\_.py에 다음처럼 테스트할 다른 코드를 추가하였다.

```python
import unittest
from my_functions import add_all

class TestAddCalc(unittest.TestCase):
    def test_negative_sum(self):
        """
        음수도 총합을 구하는데 사용할 수 있는지 테스트
        """
        data = [-1, -2, -3]
        result = add_all(data)
        self.assertEqual(result, -6)

    def test_float_sum(self):
        """
        실수들의 총합 구하기 테스트
        """
        data = [0.3, 0.5, 0.1]
        result = add_all(data)
        self.assertEqual(result, 1.0)  # 일부러 잘못된 값을 기대값으로 설정

if __name__ == '__main__':
    unittest.main()
```

현재 패키지 구조를 다음과 같이 설정했으므로,

```
project_test/
    my_functions/
        __init__.py
    test_folder/
        test_my_functions.py
```

터미널 창에 다음의 명령어를 입력하여 테스트할 코드를 실행시켰다.

```bash
python -m unittest discover -s test_folder
```

결과는 다음과 같았다.

```bash
F.
======================================================================
FAIL: test_float_sum (test_my_functions.TestAddCalc)
실수들의 총합 구하기 테스트
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\project_test\test_folder\test_my_functions.py", line 20, in test_float_sum
    self.assertEqual(result, 1.0)  # 일부러 잘못된 값을 기대값으로 설정
AssertionError: 0.9 != 1.0

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=1)
```

위 출력결과를 자세히 살펴보겠다.

- 맨 처음에 F. 라고 써져 있다. 이는 두 개의 테스트 중 하나는 실패(F, Fail)했다는 것이고, 하나는 통과(.)했다는 뜻이다. 만약 두 테스트 모두 실패했으면 FF, 모두 통과했으면 .. 이라고 표시된다.
- FAIL 구문에는 테스트 메서드명 (테스트모듈명.테스트클래스명), 그리고 해당 테스트 메서드의 docstring도 보여준다.
- Traceback에서는 테스트가 실패한 코드를 집어서 보여준다. 그리고 테스트 함수의 결과값(0.9)도 보여준다.

# 테스트 심화 개념

앞서 테스트 코드를 작성할 때 테스트 함수에 대입할 입력값을 따로 변수에 할당하여 정의하였다. 이러한 테스트 함수에 입력할 입력값을 fixture라고 한다. 보통 fixture를 만들고 테스트를 위해 재활용하는 것이 관례라고 한다.

똑같은 테스트를 반복할 때 각 테스트마다 입력값만 다르게 하고 같은 결과값을 기대한다면 이를 parameterization(매개변수화, 모수화)라고 한다.

## 예상된 에러 처리하기 (Handling expected errors)

만약 테스트 함수에 잘못된 데이터를 입력하여 에러가 날 것이 예상된다면 이 에러를 어떻게 처리해야할까?

예를 들어, \_\_init\_\_.py 파일에 다음과 같은 코드를 작성하였다고 가정하자.

```python
def add_all(args):
    total = 0
    for num in args:
        total += num
    return total

if __name__ == '__main__':
    data = ['hi', 'how are you?']
    result = add_all(data)
    print(data)
```

```python
TypeError: unsupported operand type(s) for +=: 'int' and 'str'
```

데이터의 자료형이 숫자여야 하는데 문자열을 대입해서 TypeError 에러가 발생하였다. 이렇듯, 애초에 잘못된 자료형의 값을 넣는 등의 행위로 인해 에러가 발생할 여지가 있는 경우가 있다. 이를 테스트하는 경우, 에러가 발생하면 테스트도 실패하게 된다. 

이러한 에러를 아예 테스트 코드에서 다루게 하는 방법이 있다. unittest 모듈의 .assertRaises(에러종류) 메서드와 with context-manager를 같이 쓰면 예상되는 에러를 테스트 코드에서 다룰 수 있게 할 수 있다. 다음은 test_my_functions에 추가로 테스트 코드를 작성한 모습이다.

```python
import unittest
from my_functions import add_all

class TestAddCalc(unittest.TestCase):
    def test_negative_sum(self):
        """
        음수도 총합을 구하는데 사용할 수 있는지 테스트
        """
        data = [-1, -2, -3]
        result = add_all(data)
        self.assertEqual(result, -6)

    def test_float_sum(self):
        """
        실수들의 총합 구하기 테스트
        """
        data = [0.3, 0.5, 0.1]
        result = add_all(data)
        self.assertEqual(result, 1.0)  # 일부러 잘못된 값을 기대값으로 설정

    def test_wrong_type(self):
        """
        잘못된 자료형 데이터 입력 시 함수 결과 테스트
        """
        data = ['hi', 'how are you?']
        with self.assertRaises(TypeError):
            # with 구문 실행 시 TypeError가 발생하면 테스트 통과.
            # 테스트 코드 실행 시 예상되는 에러와 실제로 그 에러가 발생하는지
            # 테스트 할 수 있음.
            result = add_all(data)

if __name__ == '__main__':
    unittest.main()
```

```bash
python -m unittest discover -v -s test_folder     
test_float_sum (test_my_functions.TestAddCalc)
실수들의 총합 구하기 테스트 ... FAIL
test_negative_sum (test_my_functions.TestAddCalc)
음수도 총합을 구하는데 사용할 수 있는지 테스트 ... ok
test_wrong_type (test_my_functions.TestAddCalc)
잘못된 자료형 데이터 입력 시 함수 결과 테스트 ... ok

======================================================================
FAIL: test_float_sum (test_my_functions.TestAddCalc)
실수들의 총합 구하기 테스트
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\project_test\test_folder\test_my_functions.py", line 20, in test_float_sum
    self.assertEqual(result, 1.0)  # 일부러 잘못된 값을 기대값으로 설정
AssertionError: 0.9 != 1.0

----------------------------------------------------------------------
Ran 3 tests in 0.002s

FAILED (failures=1)
```

자세히 보면 test_wrong_type 테스트는 통과했음을 알 수 있다. 즉, 테스트할 함수 실행 시 예상되는 에러가 실제 에러와 같으면 테스트를 통과시키는 방식이다. 

# 통합 테스트

지금까지는 단위 테스트에 대해 살펴보았는데 이번에는 통합 테스트에 대해 살펴보겠다. 

통합 테스트(integration testing)는 앱을 구성하는 여러 컴포넌트(component, 구성 요소)들이 서로 상호작용하여 잘 작동하는지 테스트하는 것이다. 통합 테스트를 할 때 마치 실제 앱 사용자가 앱을 사용하듯이 테스트해야할 수도 있다. 이렇게 실제 앱 사용자가 앱을 사용하듯이 테스트하기 위해 앱의 종류(앱인지 웹사이트인지), 목적 등에 따라 다음의 방법들이 있다.

- HTTP REST API를 호출하여 테스트하기
- 파이썬 API를 호출하여 테스트하기
- 웹 서비스를 호출하여 테스트하기
- 명령어(command-line) 기반으로 테스트하기

이러한 통합 테스트는 단위 테스트와 같은 방식으로 데이터 입력값을 설정하고, 그 입력값들을 토대로 테스트할 코드를 assert문과 함께 작성하고 이를 실행시키는 방식으로 진행시킬 수 있다. 그러나 단위 테스트와 같은 방식으로 통합 테스트를 설계하고 실행한다고 해도 단위 테스트만이 가지는 차이점이 있다.

- 앱을 구성하는 여러 가지 구성 요소들을 한꺼번에 테스트하기 때문에 단위 테스트보다 더 많은 side effects를 불러올 수 있다.
- 테스트를 위한 fixture가 더 많이 필요하다. 예) 데이터베이스, 설정 파일 등등

만약 이미 단위 테스트 코드를 작성한 파일이 있다면 이와 분리하여 통합 테스트 파일만이 존재하는 폴더를 따로 만들어 구분시킬 수 있다. 다음은 그 예로, 단위 테스트와 통합 테스트 파일을 각각의 폴더로 분리시켜 담은 가상의 패키지 구성 예이다.

```
my_project/
    main_program/
        __init__.py

    test_folder/

        unit_test/
            __init__.py
            test_one_function.py

        integration_test/
            __init__.py
            test_integration.py
```

예를 들어, 위 패키지 구성에서 통합 테스트만 따로 실행하고자 한다면 터미널 창에 다음의 명령어를 이용할 수 있다.

```bash
python -m unittest discover -s test_folder/integration_test
```

위 명령어를 입력하면 test_folder/integration_test 내의 모든 테스트 코드들을 실행시킬 것이다.

## 데이터 중심의 앱 테스트하기

대부분의 통합 테스트는 테스트를 위해 데이터가 필요할 것이다. 몇몇의 통합 테스트는 테스트가 반복 가능하고 예측가능해야 하기에 서로 다른 여러 테스트 fixture가 필요할 수 있다. 

이렇게 테스트 데이터가 필요한 통합 테스트의 경우, 아예 테스트 데이터 자체를 패키지 안의 통합 테스트 폴더 안에 데이터 폴더로 넣고 테스트를 실행할 수 있다. 다음은 데이터가 들어있는 JSON 파일들을 패키지 안에 포함시킨 패키지 구성 예이다.

```
my_project/
    main_program/
        __init__.py

    test_folder/
        unit_test/
            __init__.py
            test_one_function.py

        integration_test/
                fixtures/
                    test_simple_data.json
                    test_big_data.json
            __init__.py
            test_integration.py
```

다음은 통합 테스트 폴더 내에 테스트 데이터 폴더를 따로 마련하여 넣은 후, 해당 데이터를 토대로 테스트를 하기 위한 테스트 코드 작성 과정이다. 먼저 패키지 구성은 다음과 같이 하였다.

```
project_test/
    my_functions/
        __init__.py
        handle_json_data.py

    test_folder/
        test_my_functions.py

        integration_test/
            fixtures/
                test_big_fake_data.json
                test_small_fake_data.json
            test_integration.py
```

여기서는 handle_json_data.py 모듈을 테스트할 것이다. 이 모듈을 테스트하는 파일은 test_integration.py이다. 그리고 테스트를 위한 데이터는 fixtures 폴더 안에 담겨져 있다. 

handle_json_data.py는 다음의 코드가 작성되어있다.

```python
"""테스트용 앱"""

import json
import os
from pprint import pprint

class DisplayData():
    """테스트용 가짜 db가 든 json 파일로부터 모든 데이터를 읽고 이를 표시함."""
    def __init__(
            self,
            json_file_path: str = ''
            ) -> None:
        self.json_file_path = json_file_path
        self.db: list = []
        if os.path.splitext(self.json_file_path)[1] == '.json':
            is_error = self.extractAllData()
            if is_error is not None:
                print(is_error)

    def setJsonPath(self, new_json_path: str):
        """
        데이터가 포함된 json 파일 경로 설정
        """
        self.json_file_path = new_json_path

    def extractAllData(self) -> None | str:
        """
        json 파일로부터 모든 데이터를 추출함.
        return type)
        None: 오류 없이 잘 수행됨
        str: 오류 발생 메시지 반환.
        """
        try:
            with open(self.json_file_path, 'r') as f:
                self.db = json.load(f)
        except FileNotFoundError:
            error_message = """json 파일이 존재하지 않습니다. 
            json 파일 경로가 잘못되었거나 setJsonPath() 메서드를 통해 json 파일 경로 설정을 하지 않은 듯 합니다."""
            return error_message
        except Exception as e:
            error_message = """extractAllData() 메서드 내 에러 발생. 자세한 내용은 다음을 참고.\n"""
            error_message += "=" * 50 + '\n'
            error_message += str(e)
            return error_message
        return None
    
    def getLen(self):
        """
        총 데이터의 수를 반환.
        """
        return len(self.db)
    
    def getOneRecord(self, id: int = 0) -> dict | tuple:
        """
        db로부터 특정 행 데이터 하나를 추출.
        id: 추출할 데이터의 번호.
        """
        one_row_data = self.db[id]
        return one_row_data
    
    def getAllDB(self):
        """
        모든 데이터를 반환
        """
        return self.db
    

if __name__ == '__main__':
    json_path = 'test_folder\\intergration\\fixtures\\test_big_fake_data.json'
    inst = DisplayData(json_path)
    db_len = inst.getLen()
    entire_db = inst.getAllDB()
    get_one_record = inst.getOneRecord(3)
    print(db_len)
    pprint(entire_db)
    print("=" * 20)
    pprint(get_one_record)
    print(get_one_record['아이디'])
```

그리고 test_intergration.py 코드는 다음과 같이 작성되어있다.

```python
import unittest
from my_functions import handle_json_data as hjd

class TestSmallDB(unittest.TestCase):
    def setUp(self):
        """테스트 데이터 로드"""
        db_path = 'test_folder\\intergration\\fixtures\\test_small_fake_data.json'
        self.display_inst = hjd.DisplayData(db_path)

    def test_db_len(self):
        """총 데이터 개수 확인"""
        data_len = self.display_inst.getLen()
        self.assertEqual(data_len, 5)

    def test_one_data(self):
        """데이터와 일치하는지 여부 확인"""
        one_data = self.display_inst.getOneRecord(3)
        self.assertEqual(one_data['나이'], 31)
        self.assertEqual(one_data['성별'], '남')
        self.assertEqual(one_data['하고싶은말'], '수익에 중점을 둔 선구적 환경')

class TestBigDB(unittest.TestCase):
    def setUp(self):
        """테스트 데이터 로드"""
        db_path = 'test_folder\\intergration\\fixtures\\test_big_fake_data.json'
        self.display_inst = hjd.DisplayData(db_path)

    def test_one_data(self):
        """데이터와 일치하는지 여부 확인"""
        one_data = self.display_inst.getOneRecord(3)
        self.assertEqual(one_data['나이'], 60)
        self.assertEqual(one_data['아이디'], 'siui')

if __name__ == '__main__':
    unittest.main()
```

하나의 테스트 모듈에 둘 이상의 테스트 클래스를 만들어 여러 개의 테스트를 하나의 모듈 내에서 실행시키도록 할 수 있다. unittest.TestCase의 setUp() 메서드에서 테스트 전에 사전 준비할 것들을 미리 세팅할 수 있다. 여기서는 테스트 데이터를 불러오고있다. 

터미널 창에 다음을 입력하고 실행하였다.

```bash
python -m unittest discover -s test_folder\intergration
...
----------------------------------------------------------------------
Ran 3 tests in 0.002s

OK
```

만약 테스트 데이터를 외부 사이트 등 외부에서 얻어오는 경우라면, 인터넷 연결 이슈나 해당 API가 오프라인 상태가 되어버리는 등의 이슈로 인해 테스트를 제대로 실행시키지 못할 수도 있다. 이러한 경우를 대비해서, 데이터는 미리 외부로부터 얻어오고 이 데이터를 파일로 저장하여 위 예제에서처럼 패키지 안에 위치시킨 후에 테스트를 실행하는 방식으로 사용하는 것이 좋다. 

# 다중 환경에서 테스트하기 (내용 생략)

지금까지는 특정 파이썬 버전에서 테스트를 하였는데, 여러 버전의 파이썬에서 테스트하거나, 또는 여러 버전의 패키지에 대해 테스트를 하고자 할 때도 있을 것이다. 이러한 경우, 다중 환경에서 자동화된 테스트를 지원하는 Tox라는 라이브러리를 이용하면 된다. 

Tox는 PyPl에서 패키지로써 사용가능하다. 터미널 창에서 다음을 통해 다운로드 받을 수 있다.

```bash
pip install tox
```

이후의 tox에 대한 자세한 사항은 References[1] 참조.

- 첨언
    
    다중 환경 테스트가 나중에 실무에서 필요하다고 느낄 경우 해당 내용을 작성할 예정.
    

# GitHub에서 테스트 실행 자동화하기

지금까지는 테스트를 하는 것 자체는 여러 도구의 도움을 받아 자동화할 수 있었지만, 테스트를 실행하는 것은 일일이 명령어를 치면서 실행시켜야했다. 그런데, 만약 앱의 코드나 이를 테스트하는 테스트 코드에 변경 내용이 있어 이를 GitHub과 같은 Git repository에 커밋했다면, 커밋할 때마다 자동으로 테스트를 실행시켜주는 도구들이 존재한다. 즉, 새로운 버전의 앱 코드 또는 테스트 코드를 업데이트할 때마다 알아서 자동으로 테스트를 실행시켜주는 도구가 있다는 것이다. 

테스트 자동화 도구를 CI/CD (Continuous integration/Continuous Deployment)라고 부른다. 이러한 도구는 어떤 앱이든 테스트를 실행시켜주고, 그 앱을 컴파일 및 퍼블리시까지 해주며, 심지어는 제품으로 deploy 시켜주기도 한다.

Travis CI는 여러 CI 중 하나이다. Travis CI는 GitHub 또는 GitLab에서 오픈 소스 프로젝트라면 무료로 사용 가능하고, 개인 프로젝트에는 유료로 사용 가능하다.

사용하기 위해선 우선 GitHub과 같은 사이트에 로그인한 후, .travis.yml 파일 생성 후 다음의 코드를 입력한다. 여기서 세부 내용은 사용자에 따라 변경하여 쓸 수 있다.

```yaml
language: python
python:
  - "2.7"
  - "3.7"
install:
  - pip install -r requirements.txt
script:
  - python -m unittest discover
```

위 코드는, 일단 파이썬 2.7과 3.7 버전에 대해서 테스트를 하라고 써져 있다. 또한, install 부분에서 requirements.txt에 적힌 모든 패키지를 설치하라고 써져 있다. (만약 어떠한 dependency도 없다면 해당 섹션은 삭제한다) 그리고 script 부분을 통해 테스트를 실행하라고 써져 있다. 

위 파일을 한 번 commit, push하면 Travis CI는 사용자가 Git repository에 코드를 push할 때마다 자동으로 .travis.yml 파일 내 명령어들을 실행시켜서 자동으로 테스트를 진행한다. 

---

References

[1] [Getting Started With Testing in Python – Real Python](https://realpython.com/python-testing/)

[2] [프로그래밍에서 SIDE EFFECT 란?](https://hyeonk-lab.tistory.com/43)

[3] [Single-responsibility principle](https://en.wikipedia.org/wiki/Single-responsibility_principle)

[4] [리팩터링](https://ko.wikipedia.org/wiki/리팩터링)