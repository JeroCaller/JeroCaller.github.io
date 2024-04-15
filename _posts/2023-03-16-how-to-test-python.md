---
layout: single
title:  "[Python] 코드 테스트하기"
categories: ["Python"]
tag: ["Python", "test", "pylint", "doctest", "unittest", "time"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

파이썬 프로그램에 오류가 있는지 아니면 잘 작동하는지 확인하는 가장 간단한 방법은 print() 메서드를 적절히 사용하는 것이다. 그러나 코드 자체가 길거나 실제 상품으로 만들 프로그램의 코드라면 테스트 한 후 바로 지워야 하는데, print()가 곳곳에 있을 경우 이는 번거로운 일이 될 것이다. 

그래서 이 장에서는 파이썬 코드를 테스트하는 여러 도구와 방법에 대해서 살펴볼 것이다.

# 파이썬 코드 검사기 : pylint, pyflakes, pep8

해당 모듈들은 터미널 창에서 pip을 통해 다운로드 받을 수 있다.

```python
pip install pylint
pip install pyflakes
pip install pep8
```

해당 프로그램들은 코드 내 에러들을 검사해줄 뿐만 아니라, 좀 더 나은 스타일의 코드를 짤 수 있도록 도와준다. 여기서는 pylint에 대해서만 간단히 알아보자.

먼저 테스트해볼 파이썬 코드를 작성해보자. 다음 코드 파일은 study.py라는 이름으로 저장하였다.

```python
a = 1
b = 2
print(a)
print(b)
print(c)
```

해당 파일을 저장한 후, 터미널 창에서 pylint study.py라고 치면 다음의 결과를 얻을 것이다.

```python
************* Module study
study.py:5:0: C0304: Final newline missing (missing-final-newline)
study.py:1:0: C0114: Missing module docstring (missing-module-docstring)
study.py:1:0: C0103: Constant name "a" doesn't conform to UPPER_CASE naming style (invalid-name)
study.py:2:0: C0103: Constant name "b" doesn't conform to UPPER_CASE naming style (invalid-name)
study.py:5:6: E0602: Undefined variable 'c' (undefined-variable)

-----------------------------------
Your code has been rated at 0.00/10
```

위의 출력 결과에서, E로 시작하는 코드는 에러를 뜻한다. 위 코드에서는 정의되지도 않은 변수 c를 출력하려고 했으므로 에러가 발생하였다. 이를 고치고 다시 실행해보자. 

```python
a = 1
b = 2
c = 3
print(a)
print(b)
print(c)
```

```python
************* Module study
study.py:6:0: C0304: Final newline missing (missing-final-newline)
study.py:1:0: C0114: Missing module docstring (missing-module-docstring)
study.py:1:0: C0103: Constant name "a" doesn't conform to UPPER_CASE naming style (invalid-name)
study.py:2:0: C0103: Constant name "b" doesn't conform to UPPER_CASE naming style (invalid-name)
study.py:3:0: C0103: Constant name "c" doesn't conform to UPPER_CASE naming style (invalid-name)

------------------------------------------------------------------
Your code has been rated at 1.67/10 (previous run: 0.00/10, +1.67)
```

에러는 사라진 것을 알 수 있다. 그 외에 뜨는 문구들은 에러까지는 아니지만 지켜주면 좋은 규칙들에 대해서 설명하는 것 같다. 위 출력 결과에서는 맨 마지막 코드에 개행을 해야 하고, 모듈 자체에 docstring을 쓰는 것을 추천하며, 변수 이름을 설정한 방식을 바꿀 것을 추천하는 것 같다. 이들을 고쳐보자.

```python
"모듈에 대한 설명이 들어가는 곳입니다."

def func():
    "해당 함수에 대한 docstring은 이 곳에..."
    one = 1
    two = 2
    three = 3
    print(one)
    print(two)
    print(three)

func()
```

```python
-------------------------------------------------------------------
Your code has been rated at 10.00/10 (previous run: 1.67/10, +8.33)
```

출력 결과를 보니 스타일 규칙을 모두 지킨 것 같다. 

# unittest로 테스트하기

unittest는 말 그대로 코드 중에서 단위 별로 나눠서 해당 코드가 잘 작동하는지 테스트하기 위한 표준 라이브러리이다. 

먼저 다음처럼 테스트할 코드를 준비했다. cap.py로 저장하였다.

```python
def capitalize_string(text):
    return text.capitalize()
```

그 다음, test_cap.py 파일에 테스트 코드를 작성하였다.

```python
import unittest
import cap

class TestCap(unittest.TestCase):
    def setUp(self):
        '''
        각각의 테스트 메서드들이 호출되기 이전에 먼저 호출됨.
        테스트할 때 필요한 외부 리소스를 할당하기 위해 만듦.
        '''
        pass

    def tearDown(self):
        '''
        각각의 테스트 메서드들이 호출된 이후에 호출됨.
        테스트 후 사용했던 외부 리소스 할당을 풀기 위해 만듦.
        '''
        pass

    def test_one_word(self):
        text = 'hello'
        result = cap.capitalize_string(text)
        self.assertEqual(result, 'Hello')

    def test_multiple_words(self):
        text = 'hello and nice to meet you'
        result = cap.capitalize_string(text)
        self.assertEqual(result, 'Hello And Nice To Meet You')

if __name__ == '__main__':
    unittest.main()
```

setUp(), tearDown() 메서드는 여기서는 외부 데이터를 이용할 일이 없으므로 정의하지 않아도 되나 이런 것도 존재한다는 것을 보이기 위해 의도적으로 넣었다. cap.py 파일 내 코드를 실제로 테스트할 부분은 test_one_word(), test_multiple_words 메서드들이다. 각각 capitalize_string 함수에 각기 다른 문자열 인수들을 대입하여 원하는 결과가 나오는지 확인하고 있다. 참고로, unittest에서는 무언가를 검사할 때 assertEqual()처럼 assert로 시작하는 메서드들을 사용하면 된다. 

이제 test_cap.py 파일을 터미널 창에서 실행시켜보자.

```python
F.
======================================================================
FAIL: test_multiple_words (__main__.TestCap)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python2\python_basic\test_cap.py", line 27, in test_multiple_words
    self.assertEqual(result, 'Hello And Nice To Meet You')
AssertionError: 'Hello and nice to meet you' != 'Hello And Nice To Meet You'
- Hello and nice to meet you
?       ^   ^    ^  ^    ^
+ Hello And Nice To Meet You
?       ^   ^    ^  ^    ^

----------------------------------------------------------------------
Ran 2 tests in 0.001s
```

첫 번째 테스트는 통과했으나, 두 번째 테스트에서 에러를 발견하였다. 어디서 에러가 났는지도 상세히 설명해주고 있다. 

# doctest로 테스트하기

또 다른 표준 라이브러리의 테스트 패키지로 doctest가 있다. 이는 docstring 자체에 테스트 코드를 쓰는 방식이다. 마치 interactive interpreter처럼, 테스트하고자 하는 함수 호출문 앞에 >>> 를 쓰고, 그 다음 라인에 예상되는 결과물을 쓰면 된다. 

앞서 만든 cap.py 파일에 적용해보자.

```python
def capitalize_string(text):
    """
    >>> capitalize_string('hello')
    'Hello'
    """
    return text.capitalize()

if __name__ == '__main__':
    import doctest
    doctest.testmod()
```

터미널 창에서 해당 파일을 실행했을 때 테스트를 모두 통과했다면 아무것도 출력되지 않을 것이다.

```python
$ python cap.py

```

뒤에 -v를 추가로 입력하면 테스트 과정을 볼 수 있다.

```python
$ python cap.py -v
Trying:
    capitalize_string('hello')
Expecting:
    'Hello'
ok
1 items had no tests:
    __main__
1 items passed all tests:
   1 tests in __main__.capitalize_string
1 tests in 2 items.
1 passed and 0 failed.
Test passed.
```

이번에는 테스트를 통과 못하면 어떻게 출력되는지 알기 위해 cap.py에 한 가지 테스트를 더 추가해보았다.

```python
def capitalize_string(text):
    """
    >>> capitalize_string('hello')
    'Hello'
    >>> capitalize_string('nice to meet you')
    'Nice To Meet You'
    """
    return text.capitalize()

if __name__ == '__main__':
    import doctest
    doctest.testmod()
```

터미널 창에서 실행한 결과이다.

```python
**********************************************************************
File "C:\python2\python_basic\cap.py", line 5, in __main__.capitalize_string
Failed example:
    capitalize_string('nice to meet you')
Expected:
    'Nice To Meet You'
Got:
    'Nice to meet you'
**********************************************************************
1 items had failures:
   1 of   2 in __main__.capitalize_string
***Test Failed*** 1 failures.
```

# 코드 실행 시간 재기

어떤 코드가 실행하는데 걸리는 시간을 알고 싶을 때, 가장 간단한 방법은 현재 시간을 먼저 잰 후, 검사하고자 하는 코드를 실행시킨 다음에 지금 시간을 재면 이 시간을 앞서 잰 시간에서 빼면 코드 실행 시간이 나올 것이다.

```python
from time import time

t1 = time()  # 코드 실행 전 시각 기록

# 꽤 큰 범위의 구구단 계산 코드
mul_table = []
for i in range(2, 5000):
    one_table = []
    for j in range(2, 5000):
        one_table.append(i * j)
    mul_table.append(one_table)

print(time() - t1)  # 실행 시간 = 현재 시각 - 처음 잰 시각
```

```python
5.24768590927124
```

이렇게 시간을 재는 것보다 더 간단한 방법이 있다. 바로 표준 모듈인 timeit을 사용하는 것이다. 여기서는 timeit 모듈의 timeit() 과 repeat() 함수 이 두 개에 대해서만 살펴보자.

- timeit.timeit(*stmt='pass'*, *setup='pass'*, *timer<default timer>*,

*number=1000000*, *globals=None*)

stmt : 실행 시간을 측정할 코드 또는 함수. 코드의 경우 반드시 문자열로 대입해야 한다. 함수의 경우 그저 함수명만 명시하면 된다.

setup: stmt를 실행하기 위해 사전에 필요한 코드나 함수 선언. setup 실행 시간은 측정되지 않음

timer: Timer 인스턴스

number: stmt를 반복 실행할 횟수.

```python
from timeit import timeit

# 꽤 큰 범위의 구구단 계산 코드
test_code = '''mul_table = []
for i in range(2, 5000):
    one_table = []
    for j in range(2, 5000):
        one_table.append(i * j)
    mul_table.append(one_table)
'''

print(timeit(test_code, number=1)) # test_code를 한 번만 실행.
```

```python
4.286585800000466
```

- timeit.repeat(*stmt='pass'*, *setup='pass'*, *timer=<default timer>*, *repeat=5*
, *number=1000000*, *globals=None*)

repeat : timeit() 함수 자체를 호출할 반복 횟수. number가 stmt에 들어온 코드 또는 함수를 실행할 횟수라는 점과 다르다. 

같은 코드라도 실행 시간을 여러 번 측정하다보면 시간이 똑같지 않다. 이럴 때 해당 코드의 평균 실행 시간을 도출하기 위해 repeat 함수를 쓰면 유용할 것이다.  repeat 함수는 timeit() 함수를 여러번 실행시킨 후의 결과값인 측정 시간들을 요소로 갖는 list로 반환한다.

```python
from timeit import repeat

# 꽤 큰 범위의 구구단 계산 코드
test_code = '''mul_table = []
for i in range(2, 5000):
    one_table = []
    for j in range(2, 5000):
        one_table.append(i * j)
    mul_table.append(one_table)
'''

print(repeat(test_code, number=1, repeat=3))
```

```python
[4.149668500002008, 4.113044900004752, 4.114543700008653]
```

측정 시간에 쓰이는 것이 함수일 경우 다음과 같이 함수명을 그대로 입력하면 된다. 다음 예제는 list 생성 시 for문을 이용하는 방법과 comprehension문을 이용한 방법의 실행 시간을 비교해보고자 하는 예제이다.

```python
from timeit import timeit

def make_list_for():
    some_list = []
    for element in range(1000):
        some_list.append(element)
    return some_list

def make_list_comp():
    some_list = [element for element in range(1000)]
    return some_list

time_for = timeit(make_list_for, number=1000)
time_comp = timeit(make_list_comp, number=1000)

print('for문을 이용한 list 생성 시간: ', time_for ,'초')
print('comprehension을 이용한 list 생성 시간: ', time_comp, '초')
```

```python
for문을 이용한 list 생성 시간:  0.1371680000447668 초
comprehension을 이용한 list 생성 시간:  0.04923979996237904 초
```

그런데 만약, 테스트할 함수에 매개변수가 존재한다면 어떻게 해야 할까?

```python
from timeit import timeit

def make_list_for(number):
    some_list = []
    for element in range(number):
        some_list.append(element)
    return some_list

def make_list_comp(number):
    some_list = [element for element in range(number)]
    return some_list

num = 1000
time_for = timeit('make_list_for({})'.format(num), number=1000, setup='from __main__ import make_list_for')
time_comp = timeit('make_list_comp({})'.format(num), number=1000, setup='from __main__ import make_list_comp')

print('for문을 이용한 list 생성 시간: ', time_for ,'초')
print('comprehension을 이용한 list 생성 시간: ', time_comp, '초')
```

```python
for문을 이용한 list 생성 시간:  0.1316596000106074 초
comprehension을 이용한 list 생성 시간:  0.049061099998652935 초
```

이 경우 timeit()의 setup 매개변수에 ‘from \_\_main\_\_ import 함수명’을 대입해야 한다. \_\_main\_\_ 은 현재 파이썬 인터프리터가 실행하는 스크립트 파일 자체를 말한다. 그래서 만약 다른 모듈에서 실행한다면 그 모듈의 이름을 직접 명시하는 것이 좋다. make_list_for라는 함수가 포함된 모듈명이 study.py라면 from study import make_list_for 처럼 말이다.) 그리고 stmt 부분에는 문자열 형태로 함수명과 옆에 괄호 ()를 써주고, 괄호 안에 직접 인자를 써넣거나 위 예제처럼 format() 을 이용하여 실제 인자를 넘기면 된다. 

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [timeit — 작은 코드 조각의 실행 시간 측정](https://docs.python.org/ko/3/library/timeit.html)

[3] [[Python] 함수 및 코드 실행시간 측정하기 (timeit 모듈 사용법)](https://brownbears.tistory.com/456)