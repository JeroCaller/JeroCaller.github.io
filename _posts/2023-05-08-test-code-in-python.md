---
layout: single
title:  "[Python] 코드 테스트 개요"
categories: ["Python", "test"]
tag: ["Python", "test", "unit test", "integration test", "단위 테스트", "통합 테스트", "assert", "unittest"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

# Automated VS Manual testing

코드를 테스트하는 방법으로 크게 두 가지로 구분지어볼 수 있을 것이다. 하나는 사람이 직접 테스트하는 수동 테스트(Manual testing), 그리고 테스트 스크립트를 통해 테스트 계획을 세우고 이를 실행시켜 자동으로 코드를 테스트해주는 자동 테스트(Automated testing) 방법이 있겠다. 

수동 테스트의 일환으로 탐색적 테스트(exploratory testing)가 있다. 탐색적 테스트는 일단 직접 만든 앱을 실행시켜 구현한 여러가지 기능들을 직접 실험하면서 오류가 없는지 테스트하는 것이다. 

수동 테스트는 어떠한 도구도 사용하지 않으면서 테스트한다. 즉, 사람이 일일이 테스트함으로 시간이 많이 소비되고 개발자 입장에서도 번거로운 일일 것이다. 설령 개발자가 직접 하는 게 아니라 테스터들을 모집하더라도 자동 테스트에 비해 더 많은 테스터들이 필요할 것이다. 그럼에도 사람이 직접 에러가 있는지를 확인하기에 테스트 정확성이 떨어질 수 있다. 

그러나 자동 테스트는 테스트 자동화 도구를 사용하여 테스트한다. 따라서 사람이 일일이 테스트하는 것에 비해 그 속도가 매우 빠르고, 많은 테스터가 필요없다. 그러면서도 더 정확한 테스트 결과를 얻을 수 있다. 게다가 더 복잡한 테스트를 실행시킬 수 있다. 

파이썬에는 자동화된 테스트를 위한 다양한 도구와 라이브러리들이 있다. 여기서는 그에 대해 알아보겠다. 

# Unit VS Integration test

테스트 방식을 구분하는 또 다른 관점은 단위 테스트(unit test)와 통합 테스트(integration test)이다. 

통합 테스트는 앱을 구성하는 여러 구성요소들이 목표대로 서로 상호작용하여 잘 작동하는지를 테스트하는 것이다. 

단위 테스트는 앱을 구성하는 개개의 구성요소들을 따로 하나씩 분리하여 테스트하는 것이다. 함수, 클래스, 메서드 단위로 테스트한다. 

계산기 앱을 만든다고 해보자. 계산기에는 덧셈, 뺄셈 등 여러 기능들이 있다. 하나 하나의 기능들을 함수 또는 클래스의 메서드로 구현했다고 치자. 덧셈은 add함수로 정의하고, 뺄셈은 subtract 함수로 정의하여 구현했다. 이 때 덧셈 함수만 따로 분리하여 테스트하는 것이 단위 테스트이고, 계산할 때 덧셈과 뺄셈을 섞어서 사용해도 잘 작동되는지 테스트하는게 통합 테스트라고 볼 수 있겠다. 

통합 테스트를 할 때 에러를 발견했다면 간혹 그 에러가 어느 부분에서 나왔는지 확인하기 어려울 때가 있다. 형광등에 불이 안들어오면 불이 안들어오는 이유가 형광등 자체를 갈아끼울 때가 되어서인지, 두꺼비집 전원을 내려 아예 집 전체에 불이 안들어와서인지, 형광등에 연결된 전선에 문제가 생긴건지 알 수 없는 것처럼 말이다. 이 때에는 단위 테스트를 통해 형광등만 따로 테스트, 두꺼비집 테스트, 전선 따로 테스트하여 어디서 에러가 나왔는지 정확히 파악할 수 있다. 반대로, 형광등, 전선, 두꺼비집 다 각각은 잘 작동되나 이들을 연결하여 형광등을 작동시킬 때 불이 안 켜진다면 이 때 통합 테스트를 사용할 수 있을 것이다. 

파이썬에서 단위 테스트를 할 수 있는 가장 간단한 방법은 assert문이다. assert문을 통해 개발자가 직접 구현한 함수가 원하는 값을 반환하는지 확인할 수 있다. 

assert문의 형식은 다음과 같다.

```python
assert 조건식, '오류 시 메시지'
```

조건식이 False면 그 뒤에 작성한 오류 메시지가 출력될 것이다.

다음은 주어진 숫자들을 모두 더하는 함수를 구현하고 이를 테스트하는 예제이다. 테스트 중 오류를 발견했다는 상황 설정을 위해 add_all2 함수에는 일부러 덧셈이 아닌 곱셈을 하고 있다. 

```python
def add_all(*args: int):
    total = 0
    for num in args:
        total += num
    return total

def add_all2(*args: int):
    total = 0
    for num in args:
        total *= num
    return total

def test_add_all():
    assert add_all(1, 2, 3) == 6, "6이어야 하는데..."

def test_add_all2():
    assert add_all2(1, 2, 3) == 6, "6이어야 하는데..."

if __name__ == '__main__':
    test_add_all()
    #test_add_all2()
```

위 예제에서 맨 마지막 줄은 주석처리하고 실행하면 아무런 메시지도 뜨지 않는다. 이는 assert문을 통한 테스트가 통과되었다는 뜻이다. 이번에는 주석처리한 줄을 주석 취소하고 실행시켜보자. 그러면 다음의 메시지가 뜬다.

```python
File "C:\python\python_study\study.py", line 17, in test_add_all2
    assert add_all2(1, 2, 3) == 6, "6이어야 하는데..."
  File "C:\python\python_study\study.py", line 22, in <module>
    test_add_all2()
AssertionError: 6이어야 하는데...
```

assert 문의 조건식에서 add_all2(1, 2, 3)의 결과값과 6을 비교한다. 둘이 동일하면 에러 메시지가 뜨지 않고, 같지 않으면 에러 메시지가 뜨는 것이다.

이를 이용하면 어느 함수에서 에러가 났고, 특정 함수에서 에러가 났다면 기대값을 출력하지 않는 이유에 대해서도 파악할 수 있을 것이다. 

# test runner

한 두 군데의 테스트를 위해선 단순히 assert문으로 테스트해도 될 것이다. 그러나 테스트를 해야할 양이 방대해져서 이를 조금 더 체계적으로 테스트하려면? 이럴 때 테스트 러너를 사용하면 되겠다. 테스트 러너(test runner)는 테스트를 실행하고 테스트 결과를 보여주며, 디버깅을 위한 도구를 제공해주는 목적의 라이브러리라고 보면 되겠다. 

파이썬에서 유명한 테스트 러너에는 다음의 3가지가 있다고 한다. (물론 이거 말고도 더 있겠지)

- unittest
- nose 또는 nose2
- pytest

필요와 개발자의 숙련도에 따라 테스트 러너를 고를 수 있다. 이 셋을 간단하게 살펴보겠다.

## unittest

unittest는 파이썬 표준 라이브러리이다. 해당 모듈에는 테스트 프레임워크와 테스트 러너 둘 다 포함되어 있다고 한다. 

해당 모듈 사용 시 다음의 규칙을 따른다.

- 테스트하고자 하는 코드를 클래스의 메서드 안에 넣어야 한다.
- assert문 대신, unittest.TestCase 클래스 안에 unittest에서 따로 정의한 assertion 메서드들을 사용해야한다.

앞선 예제 1-1을 unittest를 이용하여 테스트 코드를 재구현해보았다.

```python
import unittest  #1 uniitest를 import한다.

def add_all(*args: int):
    total = 0
    for num in args:
        total += num
    return total

def add_all2(*args: int):
    total = 0
    for num in args:
        total *= num
    return total

class TestAddAll(unittest.TestCase):  #2 unittest.TestCase 상속
    def test_add_all(self):
        self.assertEqual(add_all(1, 2, 3), 6, "6이어야 하는데...") #3

    def test_add_all2(self):
        self.assertEqual(add_all2(1, 2, 3), 6, "6이어야 하는데...")

if __name__ == '__main__':
    unittest.main()  #4
```

```python
.F
======================================================================
FAIL: test_add_all2 (__main__.TestAddAll)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 21, in test_add_all2
    self.assertEqual(add_all2(1, 2, 3), 6, "6이어야 하는데...")
AssertionError: 0 != 6 : 6이어야 하는데...

----------------------------------------------------------------------
Ran 2 tests in 0.003s

FAILED (failures=1)
```

출력결과를 보면 unittest는 어디서 에러가 일어났는지 뿐만 아니라 테스트하는 함수의 결과값이 무엇인지까지 알려주고 있다. add_all2() 함수는 0부터 숫자들을 곱해나가므로 사실 결과는 숫자가 어찌됐든 항상 0일 수밖에 없다. 이 0이라는 결과값까지 알려주고 있다. 

unittest 모듈을 사용하려면 우선 해당 모듈을 import 한 뒤, 테스트 코드를 작성할 클래스를 정의하고 이 클래스가 unittest.TestCase 클래스를 상속받게 한다. 그 후, 해당 클래스 안에서 테스트 하고자 하는 코드마다 메서드로 정의하면 된다. 이 때, 메서드 이름이 test로 시작하면 후에 unittest.main() 실행 시 자동으로 해당 메서드들이 실행되어 자동으로 테스트해준다. 그리고, assertEqual() 함수 등 unittest에서 제공하는 assertion 함수들을 사용하여 테스트 코드를 작성하면 된다. 해당 클래스 내에 테스트 코드를 모두 작성 완료했다면 해당 클래스 밖에서 unittest.main() 코드를 작성하고 실행하면 해당 클래스 내에서 이름이 test로 시작되는 메서드들이 자동으로 실행되어 테스트를 실행하게 되는 것이다. 

## nose2

nose2는 unittest로 쓰인 테스트 코드와 호환되어 사용할 수 있는 테스트 러너이다. nose2는 외부 라이브러리이므로 처음 사용한다면 이를 다운로드 받아야한다.

```bash
pip install nose2
```

그리고 현재 디렉토리에 이름이 test로 시작하는 py 파일들과 unittest.TestCase를 상속받는 테스트 클래스를 모두 찾아 이를 실행시킨다. 터미널 창에 다음의 명령줄만 입력하면 말이다.

```bash
python -m nose2
```

앞선 예제 2-1의 파일을 test_sample.py로 이름을 바꾼 후 위 명령줄을 실행해보았다. 다음은 그 결과이다. 

```bash
.F
======================================================================
FAIL: test_add_all2 (test_sample.TestAddAll)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\test_sample.py", line 21, in test_add_all2
    self.assertEqual(add_all2(1, 2, 3), 6, "6이어야 하는데...")
AssertionError: 0 != 6 : 6이어야 하는데...

----------------------------------------------------------------------
Ran 2 tests in 0.000s
```

위 명령줄 실행만으로 현재 디렉토리 내에 존재하는 test_sample.py 파일에 작성한 테스트 코드를 자동으로 실행시켜준 것이다. nose2에서는 이외에도 테스트 코드 중 실행시키고 싶은 부분만 필터를 걸쳐 실행시키게 해주는 여러 명령어들이 있다고 한다. 이에 대한 자세한 내용은 References [5] 참조.

## pytest

pytest도 unittest로 작성한 test case들의 실행을 지원한다. 그러나 unittest와 다른 점 중 하나는 pytest는 test_로 시작하는 이름을 가지는 함수들만 있어도 테스트를 실행할 수 있다는 점이다. TestCase라는 특정 클래스를 상속받는 클래스를 정의해야만 테스트 코드를 작성할 수 있었던 unittest와 다른 점이다. 

pytest도 외부 프레임워크이기 때문에 pip install pytest를 통해 먼저 다운받아야한다.

다음은 test_sample2.py 파일 내 코드이다.

```python
def add_all(*args: int):
    total = 0
    for num in args:
        total += num
    return total

def add_all2(*args: int):
    total = 0
    for num in args:
        total *= num
    return total

def test_add_all():
    assert add_all(1, 2, 3) == 6, "6이어야 하는데..."

def test_add_all2():
    assert add_all2(1, 2, 3) == 6, "6이어야 하는데..."
```

위 파일을 저장한 후, 터미널 창에서 pytest라고만 치면 현재 디렉토리 내 테스트 코드들을 모두 실행시켜준다.

```bash
pytest
================================== test session starts ==================================
platform win32 -- Python 3.10.1, pytest-7.3.1, pluggy-1.0.0
rootdir: C:\python\python_study
plugins: Faker-15.3.4
collected 4 items

test_sample.py .F                                                                  [ 50%]
test_sample2.py .F                                                                 [100%] 

======================================= FAILURES ======================================== 
_______________________________ TestAddAll.test_add_all2 ________________________________ 

self = <test_sample.TestAddAll testMethod=test_add_all2>

    def test_add_all2(self):
>       self.assertEqual(add_all2(1, 2, 3), 6, "6이어야 하는데...")
E       AssertionError: 0 != 6 : 6이어야 하는데...

test_sample.py:21: AssertionError
_____________________________________ test_add_all2 _____________________________________ 

    def test_add_all2():
>       assert add_all2(1, 2, 3) == 6, "6이어야 하는데..."
E       AssertionError: 6이어야 하는데...
E       assert 0 == 6
E        +  where 0 = add_all2(1, 2, 3)

test_sample2.py:17: AssertionError
================================ short test summary info ================================ 
FAILED test_sample.py::TestAddAll::test_add_all2 - AssertionError: 0 != 6 : 6이어야 하는데
...
FAILED test_sample2.py::test_add_all2 - AssertionError: 6이어야 하는데...
============================== 2 failed, 2 passed in 0.35s ==============================
```

test_sample.py 파일까지도 실행되었다. pytest에 대한 더 자세한 사항은 References [6] 참조.

---

References

[1] [Getting Started With Testing in Python – Real Python](https://realpython.com/python-testing/)

[2] tutorialspoint, “UNITTEST FRAMEWORK”, (2016)

[3] [03_가정 설정문(assert)](https://wikidocs.net/21050)

[4] [107 작성한 코드를 테스트하려면? ― unittest](https://wikidocs.net/132725)

[5] [Welcome to nose2 — nose2 0.13.0 documentation](https://docs.nose2.io/en/latest/)

[6] [pytest: helps you write better programs — pytest documentation](https://docs.pytest.org/en/latest/)