---
layout: single
title:  "[Python] unittest - skip test. 테스트 건너뛰기"
categories: ["Python", "test"]
tag: ["Python", "test", "unit test", "단위 테스트", "unittest"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
테스트 시 특정 테스트 메서드나 TestCase 클래스의 테스트를 조건부로 또는 무조건적으로 건너뛸 수 있다. 그러면 unittest에서는 해당 테스트를 ‘expected failure’라고 표시한다. 스킵된 테스트는 실패로 간주되나 TestResult에서는 실패한 테스트라고 간주되지 않는다. 

특정 테스트 메서드를 스킵하고자 한다면, @unittest.skip() 데코레이터를 사용한다. 인자로는 스킵하는 이유를 문자열로 적으면 된다.

```python
import unittest
from solveEq import solve_quadratic_eq
from solveEq import solve_cubic_eq

class TestSomething(unittest.TestCase):
    def setUp(self):
        self.coef_set = [1, 2, 3]
        test_method_desc = self.shortDescription()

        # 테스트 케이스마다 그에 맞는 세트의 데이터를 설정할 수 있다.
        # 물론 테스트용 데이터를 테스트 메서드 안에서 설정할 수도 있다.
        if test_method_desc == '서로 다른 두 근의 경우':
            # x^2 -3x + 2 = 0 -> x = [1, 2]
            self.coef_set = [1, -3, 2]
        elif test_method_desc == '중근의 경우':
            # x^2 - 2x + 1 = 0 -> x = [1]
            self.coef_set = [1, -2, 1]

    @unittest.skip("스킵이 되는지 테스트.")
    def test_diff_roots(self):
        """서로 다른 두 근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1, 2])

    def test_equal_root(self):
        """중근의 경우"""
        self.skipTest("또 다른 스킵 방법")  # 또 다른 스킵 방법
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1])

if __name__ == '__main__':
    unittest.main()
```

```bash
python test_my_unittest.py
ss
----------------------------------------------------------------------
Ran 2 tests in 0.000s

OK (skipped=2)
```

스킵된 테스트는 출력결과에서 ‘s’로 표시된다.

또 다른 방법으로, skipTest() 메서드를 사용해도 된다. 위 예제의 test_equal_root() 메서드 안에 해당 메서드를 작성하여 해당 테스트 메서드도 스킵되었다. 

테스트 스킵과 관련된 데코레이터로는 다음이 존재한다.

- `unittest.skip(reason)`
무조건 테스트를 스킵한다. reason 인자에는 해당 테스트를 스킵하는 이유를 문자열로 대입하면 된다.
- `unittest.skipIf(condition, reason)`: condition이 True인 경우 해당 테스트를 스킵한다.
- `unittest.skipUnless(condition, reason)`: condition이 True가 아니면 테스트를 스킵한다.
- `unittest.expectedFailure`: 해당 테스트를 expected failure (예상된 실패)로 표시한다. 테스트 실행 시 해당 테스트가 실패한다면 해당 테스트를 실패한 테스트로 세지 않는다.

```python
import unittest

class TestExample(unittest.TestCase):
    a = -10
    b = 0

    @unittest.skipIf(a<b, "a-b < 0인 경우는 스킵.")
    def test_sub(self):
        """뺄셈"""
        result = TestExample.a - TestExample.b
        self.assertEqual(result, -10)

    @unittest.skipUnless(b != 0, "0으로 나눌 수 없으니까.")
    def test_div(self):
        """나눗셈"""
        result = TestExample.a / TestExample.b
        self.assertEqual(result, 0.5)

    @unittest.expectedFailure
    def test_mul(self):
        """곱셈"""
        result = TestExample.a * TestExample.b
        self.assertEqual(result, 200)

if __name__ == '__main__':
    unittest.main()
```

```bash
python study.py
sxs
----------------------------------------------------------------------
Ran 3 tests in 0.001s

OK (skipped=2, expected failures=1)
```

expectedFailure 데코레이터로 지정된 테스트 메서드를 실행하면 출력 결과에서는 “x”로 표시된다. 

- 참고

만약 데이터를 클래스의 속성이 아닌, setUp() 또는 테스트 메서드 내에 쓰는 등 인스턴스 속성으로 작성하면 스킵 데코레이터의 인자로 대입할 수 없다. 다음의 예를 보자.

```python
import unittest
from solveEq import solve_quadratic_eq

def discriminant(coef: list) -> int | bool:
    """
    판별식 b^2-4ac가 0을 기준으로 양수, 음수, 0인지를 판별
    return type)
    True: 양수
    False: 음수
    0: 정확히 0
    """
    a, b, c = coef
    d = b**2 - 4*a*c
    if d > 0: return True
    elif d < 0: return False
    else: return 0

class TestSomething(unittest.TestCase):
    def setUp(self):
        self.coef_set = [1, 2, 3]
        test_method_desc = self.shortDescription()

        # 테스트 케이스마다 그에 맞는 세트의 데이터를 설정할 수 있다.
        # 물론 테스트용 데이터를 테스트 메서드 안에서 설정할 수도 있다.
        if test_method_desc == '서로 다른 두 근의 경우':
            # x^2 -3x + 2 = 0 -> x = [1, 2]
            self.coef_set = [1, -3, 2]
        elif test_method_desc == '중근의 경우':
            # x^2 - 2x + 1 = 0 -> x = [1]
            self.coef_set = [1, -2, 1]

    # 데코레이터 에러
    @unittest.skipIf(discriminant(self.coef_set) is False, "허근 비허용")
    def test_diff_roots(self):
        """서로 다른 두 근의 경우"""
        self.coef_set = [1, -3, 2]
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1, 2])
```

위 예제의 skipIf 데코레이터 줄을 보면 self.coef_set을 대입하려고 하나, self가 정의되지 않았다면서 에러가 뜬다. 이렇게 데이터를 인스턴스 속성으로 작성한 경우, 데코레이터보다는 if문과 self.skipTest()를 테스트 메서드 안에 섞어 쓰는 게 좋을 것 같다.

```python
import unittest
from solveEq import solve_quadratic_eq

def discriminant(coef: list) -> int | bool:
    """
    판별식 b^2-4ac가 0을 기준으로 양수, 음수, 0인지를 판별
    return type)
    True: 양수
    False: 음수
    0: 정확히 0
    """
    a, b, c = coef
    d = b**2 - 4*a*c
    if d > 0: return True
    elif d < 0: return False
    else: return 0

class TestSomething(unittest.TestCase):
    def setUp(self):
        self.coef_set = [1, 2, 3]
        test_method_desc = self.shortDescription()

        # 테스트 케이스마다 그에 맞는 세트의 데이터를 설정할 수 있다.
        # 물론 테스트용 데이터를 테스트 메서드 안에서 설정할 수도 있다.
        if test_method_desc == '서로 다른 두 근의 경우':
            # x^2 -3x + 2 = 0 -> x = [1, 2]
            self.coef_set = [1, -3, 2]
        elif test_method_desc == '중근의 경우':
            # x^2 - 2x + 1 = 0 -> x = [1]
            self.coef_set = [1, -2, 1]

    def test_diff_roots(self):
        """서로 다른 두 근의 경우"""
        if discriminant(self.coef_set) is False:
            self.skipTest("허근 비허용")
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1, 2])
```

---

Reference

[1] tutorialspoint, “UNITTEST FRAMEWORK”, (2016)

[2] [unittest — 단위 테스트 프레임워크](https://docs.python.org/ko/3/library/unittest.html)