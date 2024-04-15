---
layout: single
title:  "[Python] unittest - Assertion"
categories: ["Python", "test"]
tag: ["Python", "test", "unit test", "단위 테스트", "unittest", "assertion"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

unittest에서는 assert로 시작하는 함수들을 제공하여 특정 조건에 대한 테스트를 실행할 수 있도록 한다. 만약 assertion이 실패한 경우, AssertError가 발생하며, unittest에서는 이를 테스트에 실패한 것으로 본다. 이 외의 예외가 발생하면 이를 에러로 취급한다. 

unittest에서는 주로 3종류의 assertion 메서드들이 존재한다. 

- Basic boolean asserts (기본 불린 어서트)
- Comparative asserts (비교 어서트)
- Asserts for collections

# Basic assert methods (기본 assert)

Basic assert method는 연산 결과가 True인지 False인지를 판별한다. 모든 assert method들은 msg 매개변수가 존재하는데, 원한다면 여기에 테스트 실패 시 출력시킬 에러 메시지를 삽입할 수 있다. 

| Methos | Details |
| --- | --- |
| assertEqual(arg1, arg2, msg=None) | arg1과 arg2가 같은지 테스트. 같지 않다면 테스트는 실패. |
| assertNotEqual(arg1, arg2, msg=None) | arg1과 arg2가 다른지 테스트. 같다면 테스트 실패. |
| assertTrue(expr, msg=None) | expr가 True인지 테스트. False면 테스트 실패., |
| assertFalse(expr, msg=None) | expr가 False인지 테스트. True이면 테스트 실패. |
| assertIs(arg1, arg2, msg=None) | arg1과 arg2가 같은 객체인지 테스트. |
| assertIsNot(arg1, arg2, msg=None) | arg1과 arg2가 다른 객체인지 테스트. |
| assertIsNone(expr, msg=None) | expr이 None인지 테스트 |
| assertIsNotNone(expr, msg=None) | expr이 None이 아닌지 테스트 |
| assertIn(arg1, arg2, msg=None) | arg1이 arg2에 포함되는지 테스트. (arg1 in arg2) |
| assertNotIn(arg1, arg2, msg=None) | arg1이 arg2에 포함되지 않는지 테스트. (arg1 not in arg2) |
| assertIsInstance(obj, cls, msg=None) | obj이 cls의 인스턴스인지 확인 |
| assertNotIsInstance(obj, cls, msg=None) | obj이 cls의 인스턴스가 아닌지 확인. |

```python
import unittest

class TestExample(unittest.TestCase):
    def test_equal(self): self.assertEqual(1+1, 2)
    def test_not_equal(self): self.assertNotEqual(2*2, 3)
    def test_true(self): self.assertTrue(1+1==2)
    def test_in(self): self.assertIn('hi', ['hi', 'how are you?'])
    def test_not_in(self): self.assertNotIn(2, range(10))

if __name__ == '__main__':
    unittest.main()
```

```bash
python study.py
...F.
======================================================================
FAIL: test_not_in (__main__.TestExample)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 9, in test_not_in      
    def test_not_in(self): self.assertNotIn(2, range(10))
AssertionError: 2 unexpectedly found in range(0, 10)

----------------------------------------------------------------------
Ran 5 tests in 0.001s

FAILED (failures=1)
```

# comparative assert (비교 assert)

- `assertAlmostEqual(first, second, places=7, msg=None, delta=None)` 또는
`assertNotAlmostEqual(first, second, places=7, msg=None, delta=None)`
first와 second가 근사적으로 같은지 테스트. 이는 둘의 차이값을 주어진 places 자리까지 소수점 반올림한 후, 이를 0과 비교함으로써 진행된다. 만약 places 대신 delta가 주어지면 first, second의 값 차이는 delta보다 작거나 같아야 테스트 통과. delta와 place는 동시에 주어지면 TypeError 발생함.
- `assertGreater(first, second, msg=None)`
first > second인지 테스트.
- `assertGreaterEqual(first, second, msg=None)`
first ≥ second인지 테스트.
- `assertLess(first, second, msg=None)`
first < second인지 테스트.
- `assertLessEqual(first, second, msg=None)`
first ≤ second인지 테스트.
- `assertRegexpMatches(text, regexp, msg=None)`   
`assertNotRegexpMatches(text, regexp, msg=None)`
regexp가 text 내에서 match되는지 아닌지를 테스트함. 여기서 regexp는 정규표현식 객체일 수도 있고, re.search()에 사용할 수 있는 정규식의 문자열 형태일 수도 있다. 테스트 실패 시, 에러 메시지에는 패턴과 텍스트가 포함된다. 
⇒ 직접 실행한 결과, `assertRegex()`, `assertNotRegex()`를 대신 쓰라고 권고하고 있다.

```python
import unittest
import math

class TestExample(unittest.TestCase):
    def test_almost_equal(self): 
        self.assertAlmostEqual(math.pi, 3.14)

    def test_not_almost_equal(self): 
        self.assertNotAlmostEqual(math.sqrt(2), 1.5)

    def test_greater(self):
        self.assertGreater(math.sin(math.pi/6), 1/3)

    def test_regex(self):
        text = 'my id is "hello166". nice to meet you.'
        pattern = '[0-9]+'
        self.assertRegex(text, pattern)

if __name__ == '__main__':
    unittest.main()
```

```bash
python study.py
F...
======================================================================
FAIL: test_almost_equal (__main__.TestExample)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 7, in test_almost_equal
    self.assertAlmostEqual(math.pi, 3.14)
AssertionError: 3.141592653589793 != 3.14 within 7 places (0.0015926535897929917 difference)

----------------------------------------------------------------------
Ran 4 tests in 0.001s

FAILED (failures=1)
```

# Assert for collections

여기서는 list, tuple과 같이 데이터 집합을 가지는 자료형에 대한 assert 함수를 다룬다.

| Methods | Details |
| --- | --- |
| assertListEqual(list1, list2, msg=None) | 두 list가 서로 똑같은지 테스트. 실패 시 두 리스트 간 차이점만을 보여줌. |
| assertTupleEqual(tuple1, tuple2, msg=None) | 두 튜플이 같은지 테스트. 실패 시 두 튜플 간 차이점만을 보여줌. |
| assertSetEqual(set1, set2, msg=None) | 두 set이 같은지 테스트. 실패 시 두 set 간의 차이점만을 보여줌 |
| assertDictEqual(dict1, dict2, msg=None) | 두 딕셔너리가 똑같은지 테스트. 실패 시 두 딕셔너리 간의 차이점만을 보여줌.  |

```python
import unittest

class TestExample(unittest.TestCase):
    def test_list_equal(self):
        self.assertListEqual([1, 2, 3], list(range(4)))

    def test_dict_equal(self):
        test_dict = {'a': 2, 'b': 2, 'c': 3}
        expected_dict = {'a': 1, 'b': 2, 'c': 3}
        self.assertDictEqual(test_dict, expected_dict)

if __name__ == '__main__':
    unittest.main()
```

```bash
python study.py
FF
======================================================================
FAIL: test_dict_equal (__main__.TestExample)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 12, in test_dict_equal 
    self.assertDictEqual(test_dict, expected_dict)
AssertionError: {'a': 2, 'b': 2, 'c': 3} != {'a': 1, 'b': 2, 'c': 3}  
- {'a': 2, 'b': 2, 'c': 3}
?       ^

+ {'a': 1, 'b': 2, 'c': 3}
?       ^

======================================================================
FAIL: test_list_equal (__main__.TestExample)
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 7, in test_list_equal
    self.assertListEqual([1, 2, 3], list(range(4)))
AssertionError: Lists differ: [1, 2, 3] != [0, 1, 2, 3]

First differing element 0:
1
0

Second list contains 1 additional elements.
First extra element 3:
3

- [1, 2, 3]
+ [0, 1, 2, 3]
?  +++

----------------------------------------------------------------------
Ran 2 tests in 0.001s

FAILED (failures=2)
```

---

Reference

[1] tutorialspoint, “UNITTEST FRAMEWORK”, (2016)

[2] [unittest — 단위 테스트 프레임워크](https://docs.python.org/ko/3/library/unittest.html)