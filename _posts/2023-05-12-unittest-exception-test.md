---
layout: single
title:  "[Python] 예외 테스트"
categories: ["Python", "test"]
tag: ["Python", "test", "unit test", "단위 테스트", "unittest"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
unittest에서는 테스트하려는 코드에서 예외가 일어나는지 체크하는 assertion 메서드를 제공한다. 

- `assertRaises(exception, callable, *args, **kwards)`
위치 인자와 키워드 인자가 대입되어 함수(callable)가 호출될 때, exception 인자로 주어진 Exception 종류에 맞는 예외가 뜨는지 테스트한다. 만약 예상된 예외가 일어나는 경우에는 테스트를 통과하고, 그 외에 다른 종류의 예외가 발생하는 경우에는 에러로 처리, 아무런 예외도 일어나지 않으면 테스트 실패로 처리한다. 둘 이상의 예외들에 대해 테스트하려면, exception 인자에 튜플 형태로 여러 예외 클래스명을 대입하면 된다.

```python
import unittest

def into_int(data: str):
    """
    문자열로 쓰여진 숫자를 int형으로 변환하여 반환.
    ex) into_int('1') -> 1
    """
    return int(data)

class TestExample(unittest.TestCase):
    def test_raise(self):
        self.assertRaises(ValueError, into_int, 'hi')

    def test_raise2(self):
        with self.assertRaises(ValueError):
            into_int('2')

if __name__ == '__main__':
    unittest.main()
```

```python
.F
======================================================================
FAIL: test_raise2 (__main__.TestExample)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 16, in test_raise2
    self.assertRaises(ValueError, into_int, '2')
AssertionError: ValueError not raised by into_int

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=1)
```

위 예제의 test_raise2() 메서드 내부의 코드처럼 with context-manager를 이용하여 작성할 수도 있다. 

---

References

[1] tutorialspoint, “UNITTEST FRAMEWORK”, (2016)

[2] [unittest — 단위 테스트 프레임워크](https://docs.python.org/ko/3/library/unittest.html)