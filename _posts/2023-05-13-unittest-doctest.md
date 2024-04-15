---
layout: single
title:  "[Python] doctest"
categories: ["Python", "test"]
tag: ["Python", "test", "doctest"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
파이썬의 표준 모듈 중에는 “Doctest”라는 모듈이 있다. 이는 코드 내에서 python IDLE 사용 시 매 줄마다 나오는 “\>\>\>”가 쓰인 문자열을 찾는다. “\>\>\>”가 쓰인 문자열에는 보통 함수나 클래스, 모듈의 맨 앞에 쓰는 Docstring에 포함되어 있는데, “\>\>\>”가 쓰인 코드 블럭은 주로 해당 함수가 잘 작동하는지 테스트 하는 코드가 담겨 있어 Doctest 모듈이 이러한 코드를 찾아 테스트를 실행시킨다. 

다음은 factorial 함수를 구현한 모듈의 코드이다. 

```python
"""
Docstring 내 쓰인 테스트 코드도 작동하는지 확인하기 위한 모듈.
이 모듈에는 factorial() 함수를 제공함. 해당 함수의 예제는 다음과 같음.

>>> factorial(5)
120
"""

def factorial(n):
    """
    n의 팩토리얼 값 반환. n >= 0의 정수여야함.

    >>> factorial(-1)
    Traceback (most recent call last):
        ...
    ValueError: n은 n >= 0의 정수여야 합니다.
    """
    if n < 0:
        raise ValueError("n은 n >= 0의 정수여야 합니다.")
    
    if n == 0: return 1
    return n * factorial(n-1)

if __name__ == '__main__':
    import doctest
    doctest.testmod()
```

Docstring안에는 “\>\>\>”가 포함된 글이 있다. 해당 부분에는 함수에 인자를 대입하여 호출하는 코드와 그 함수를 호출할 때 나올 것으로 예상되는 코드가 함께 쓰여져 있다. 또한 맨 아래 줄을 보면 doctest 모듈을 import 하여 testmod()를 호출하고 있다. 이 함수의 호출을 통해 docstring에 쓰인 테스트 코드를 판별하여 이를 실행시켜준다. 

터미널 창에서 위 모듈을 실행시켜보겠다.

```bash
python study.py
```

만약 모든 테스트가 통과된다면 아무런 결과도 출력되지 않는다. 만약, 테스트가 모두 통과했어도 그에 대한 결과를 보고 싶다면 위 명령어 끝에 -v를 붙인다.

```bash
python study.py -v
Trying:
    factorial(5)
Expecting:      
    120
ok
Trying:
    factorial(-1)
Expecting:
    Traceback (most recent call last):       
        ...
    ValueError: n은 n >= 0의 정수여야 합니다.
ok
2 items passed all tests:
   1 tests in __main__  -> 실행한 모듈 자체의 docstring 내 테스트
   1 tests in __main__.factorial -> factorial 내 docstring 내 테스트
2 tests in 2 items.
2 passed and 0 failed.
Test passed.
```

만약 실패한 테스트 코드를 작성했다면 어땠을까? 앞선 예제 1-1의 모듈 docstring에서 예상 결과값을 120에서 121로 고쳐 저장하고 다시 실행시켜보았다.

```bash
python study.py -v
Trying:
    factorial(5)
Expecting:
    121
**********************************************************************
File "C:\python\python_study\study.py", line 5, in __main__
Failed example:
    factorial(5)
Expected:
    121
Got:
    120
Trying:
    factorial(-1)
Expecting:
    Traceback (most recent call last):
        ...
    ValueError: n은 n >= 0의 정수여야 합니다.
ok
1 items passed all tests:
   1 tests in __main__.factorial
**********************************************************************
1 items had failures:
   1 of   1 in __main__
2 tests in 2 items.
1 passed and 1 failed.
***Test Failed*** 1 failures.
```

위와 같이 통과하지 못한 테스트의 결과도 나온다. 

참고로 이러한 doctest의 장점은 다음과 같다.

- 실무에서 고객의 추가적인 요구를 들어주기 위해 새 기능을 추가하다보면 알게 모르게 기존의 코드를 변경할 수도 있다. 이러한 경우에 예전에는 없던 버그가 새로 생길 수도 있다. 이러한 버그를 미리 감지하기 위해 사용될 수 있다. 참고로 이러한 상황에서 생긴 버그를 회귀 버그라 하고, 이러한 회귀 버그를 찾는 소프트웨어 테스트 방식을 회귀 테스트 (regression test)라고 한다.
- 개발자가 만든 함수를 처음 접하는 사용자에게 해당 함수를 사용하는 일종의 튜토리얼을 제공하고자 할 때. 보통 해당 함수의 입력값과 출력값을 보여줌으로써 어떻게 사용하는 것인지를 알려줄 수 있다.

---

References

[1] tutorialspoint, “UNITTEST FRAMEWORK”, (2016)

[2] [unittest — 단위 테스트 프레임워크](https://docs.python.org/ko/3/library/unittest.html)

[3] [회귀 테스트](https://ko.wikipedia.org/wiki/회귀_테스트)