---
layout: single
title:  "[Python] unittest 기본 개념 및 API"
categories: ["Python", "test", "unittest"]
tag: ["Python", "test", "unit test", "단위 테스트", "unittest"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 몇몇 개념들

- test fixture : 한 개 이상의 테스트를 수행하기 위한 준비 단계. 데이터베이스 등 테스트 용 데이터를 포함하여 디렉토리, 서버 시작 등등 테스트를 위해 사전 준비해야할 것들을 의미.
- test case: 가장 작은 테스트 단위. 특정 입력값들의 집합에 대한 특정 결과를 체크한다. unittest에서는 기반 클래스인 TestCase를 제공하여 새로운 test case 생성에 사용될 수 있다.
- test suite: test case, test suite의 집합. 함께 실행되어야하는 테스트들을 모아주는 역할을 한다. test suite들은 TestSuite 클래스를 통해 실행된다.
- test runner: 테스트들을 모아서 실행시키고 이 결과를 사용자에게 보여주는 컴포넌트(component). 테스트 러너는 그래픽 인터페이스를 가질 수도, 텍스트 인터페이스를 가질 수도, 또는 테스트 결과을 알려주는 특정 값을 반환하는 형태일 수도 있다.

# 간단한 테스트 코드 작성 예제

```python
import unittest

def factorial(n):
    if n == 1: return n
    return n * factorial(n-1)

class TestSomething(unittest.TestCase):
    def test_factorial(self):
        data = 3
        func_result = factorial(data)
        self.assertEqual(func_result, 6)

if __name__ == '__main__':
    unittest.main()
```

터미널 창에서 위 파일 실행.

```bash
python test_my_unittest.py
.
----------------------------------------------------------------------
Ran 1 test in 0.000s

OK
```

## 테스트 결과 메시지

테스트 결과에서 나올 수 있는 메시지는 다음과 같이 있다.

- OK : 테스트 통과. 테스트 통과한 각각의 Test Case마다 마침표(.)로 표시됨.
- FAIL : 테스트를 통과 못하고 AssertionError 예외를 일으킴. 콘솔에서는 F로 표시됨.
- ERROR: AssertionError 외 다른 에러 발생 시 출력됨. 콘솔에서는 E로 표시됨

## 터미널 창에서 명령어로 테스트 실행하기

command line에서 unittest로 테스트 코드를 작성한 스크립트를 실행하는 명령어에는 다음과 같이 있다.

```bash
python -m unittest testfile
```

unittest 뒤에 테스트 코드가 작성되어있는 파이썬 파일명을 (확장자 없이) 명시하면 해당 파일 내 모든 테스트 코드를 동작시킨다. 

```bash
python -m unittest testfile.test_class
```

또는 해당 테스트 파일 내에 만약 여러 테스트 클래스를 정의해뒀고, 그 중에서 원하는 테스트 클래스에 대해서만 테스트를 실행하고자 한다면 위와 같이 마침표를 이용하여 테스트 파일명과 테스트 클래스명을 언급하면 된다. 위 예제에서는 다음과 같이 표현할 수 있겠다.

```bash
python -m unittest test_my_unittest.TestSomething
```

```bash
python -m unittest testfile.test_class.test_method
```

특정 테스트 클래스 내에서도 특정 테스트 메서드에 대해서만 테스트를 실행하고자 한다면 위와 같이 마침표로 구분하여 테스트 모듈명, 테스트 클래스, 테스트 메서드명을 차례대로 작성하면 된다. 위 예제를 예로 들면 다음과 같이 표현할 수 있다.

```bash
python -m unittest test_my_unittest.TestSomething.test_factorial
```

## 터미널 창에서 테스트 실행 시 옵션 추가

터미널 창에서 명령어를 통해 테스트 실행 시 추가할 수 있는 옵션들이 있다. 보통 - 기호를 먼저 작성하고 그 뒤에 알파벳 문자를 써넣는 식이다. 

```bash
python -m unittest -h
```

옵션에는 다음이 있다.

- -h, —help: 옵션에 관한 도움말 출력
- -v, —verbose: 테스트 메서드의 docstring, 테스트 메서드의 이름 등의 정보를 같이 출력한다.
- -q, —quiet: -v와는 달리 최소한의 결과만을 보여준다.
- -f, —failfast: 첫 테스트 실패 시 그 다음 테스트는 실행하지 않고 멈춘다.
- -c, —catch: 테스트 실행 중 사용자로부터 Ctrl+C 입력 받으면 현재 테스트까지 실행 후 지금까지의 결과를 보여줌.

이 외에도 옵션이 많으니 더 자세한 사항은 터미널 창에 python -m unittest -h를 치거나 Referececs [2] 참조

# API

여기서는 unittest 모듈에서 사용할 수 있는 클래스와 그 메서드들에 대해서 살펴보겠다. 해당 모듈에는 5개의 주요 클래스가 있다고 한다. 

## TestCase class

TestCase 클래스는 아주 작은 단위의 코드를 테스트하고자 할 때 사용하는 클래스이다. 해당 클래스에 정의된 메서드에는 다음이 있다.

| Method | details |
| --- | --- |
| setUp() | test fixture를 준비하기 위한 메서드. 테스트 메서드 호출 전에 자동으로 이 메서드가 호출된다. |
| tearDown() | 테스트 메서드 호출 후 테스트 결과가 기록된 후 호출된다. 테스트 중 예외가 발생해도 호출되며, 테스트 메서드의 테스트 결과와 상관없이 setUp()이 성공적으로 호출되었을 때만 호출된다. |
| setUpClass() | 개별 클래스 내 테스트들이 실행되기 전에 호출되는 클래스 메서드. 클래스 메서드이므로 @classmethod 데코레이터를 받으며, 인자로 클래스 cls를 받는다. |
| tearDownClass() | 개별 클래스 내 테스트들이 실행된 후에 호출되는 클래스 메서드. |
| run(result=None) | 테스트 실행 후 결과를 result 인자로 전달된 TestResult 객체에 수집한다. |
| skipTest(reason) | 테스트 메서드 내 또는 setUp() 메서드 내에서 호출 시 현재 테스트를 건너뜀 |
| debug() | 결과를 수집하지 않고 테스트 수행 |
| shortDescription() | 테스트 설명을 한 줄로 리턴. 테스트 메서드의 docstring의 첫 줄을 반환 |

### Fixture

테스트를 위한 fixture를 설정하려면 setUp()을 오버라이딩하여 작성하면 되고, fixture 설정에 의한 리소스 할당을 해제하려면 tearDown() 메서드를 오버라이딩하면 된다. 

다음은 이차방정식을 푸는 함수에 대한 테스트 코드 작성 예이다. 서로 다른 두 근이 나오도록 하는 테스트와 중근의 경우를 테스트하는 경우로 나누고, 그 경우에 따라 데이터 세팅 (여기선 이차방정식의 3개의 계수들)도 다르게 설정하고 있으며, 이러한 데이터 설정을 setUp() 메서드에 오버라이딩하여 하고 있다. tearDown() 메서드는 각 테스트의 맨 끝부분에서 실행된다.

```python
import unittest
import sympy as sp

def solve_quadratic_eq(coef: list):
    """
    ax^2+bx+c=0을 주어진 a, b, c에 대해서 풀고 그 해를 리턴.
    coef = [a, b, c]
    """
    x = sp.symbols('x')
    a, b, c = sp.symbols('a b c')
    f = a*x**2 + b*x + c
    f = f.subs({a: coef[0], b: coef[1], c: coef[2]})
    set_eq = sp.Eq(f, 0)
    solutions = sp.solve(set_eq)
    return solutions

class TestSomething(unittest.TestCase):
    def setUp(self):
        self.a = 1
        self.b = 2
        self.c = 3
        test_method_desc = self.shortDescription()

        # 테스트 케이스마다 그에 맞는 세트의 데이터를 설정할 수 있다.
        # 물론 테스트용 데이터를 테스트 메서드 안에서 설정할 수도 있다.
        if test_method_desc == '중근의 경우':
            # x^2 - 2x + 1 = 0 -> x = [1]
            self.a = 1
            self.b = -2
            self.c = 1
            print(test_method_desc, self.a, self.b, self.c)
        elif test_method_desc == '서로 다른 두 근의 경우':
            # x^2 -3x + 2 = 0 -> x = [1, 2]
            self.a = 1
            self.b = -3
            self.c = 2
            print(test_method_desc, self.a, self.b, self.c)
        self.coef_set = [self.a, self.b, self.c]
    
    def tearDown(self):
        print("\n{} 테스트 끝.".format(self.shortDescription()))

    def test_diff_roots(self):
        """서로 다른 두 근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1, -2])

    def test_equal_root(self):
        """중근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1])

if __name__ == '__main__':
    unittest.main()
```

```bash
python -m unittest test_my_unittest
서로 다른 두 근의 경우 1 -3 2

서로 다른 두 근의 경우 테스트 끝.
F중근의 경우 1 -2 1

중근의 경우 테스트 끝.
.
======================================================================
FAIL: test_diff_roots (test_my_unittest.TestSomething)
서로 다른 두 근의 경우
----------------------------------------------------------------------
Traceback (most recent call last):
  File "C:\python\python_study\test_my_unittest.py", line 48, in test_diff_roots        
    self.assertEqual(result, [1, -2])
AssertionError: Lists differ: [1, 2] != [1, -2]

First differing element 1:
2
-2

- [1, 2]
+ [1, -2]
?     +

----------------------------------------------------------------------
Ran 2 tests in 0.054s

FAILED (failures=1)
```

### Class fixture

TestCase 클래스에는 setUpClass()와 tearDownClass() 메서드를 가진다. setUpClass()는 TestCase 클래스 내 개개의 테스트들의 실행 전에 실행되고, tearDownClass()는 TestCase 클래스 내 모든 테스트의 실행 후에 실행된다. 이 두 메서드 모두 클래스 메서드이므로 @classmethod로 꾸며줘야 한다. 

다음은 앞선 예제에서 setUpClass()와 tearDownClass() 메서드를 추가한 예제이다.

```python
import unittest
import sympy as sp

def solve_quadratic_eq(coef: list):
    """
    ax^2+bx+c=0을 주어진 a, b, c에 대해서 풀고 그 해를 리턴.
    coef = [a, b, c]
    """
    x = sp.symbols('x')
    a, b, c = sp.symbols('a b c')
    f = a*x**2 + b*x + c
    f = f.subs({a: coef[0], b: coef[1], c: coef[2]})
    set_eq = sp.Eq(f, 0)
    solutions = sp.solve(set_eq)
    return solutions

class TestSomething(unittest.TestCase):

    @classmethod
    def setUpClass(cls):
        print("\nsetUpClass에서 인사 드립니다.")

    @classmethod
    def tearDownClass(cls):
        print("\ntearDownClass에서 작별 인사 드립니다.")
    
    def setUp(self):
        self.a = 1
        self.b = 2
        self.c = 3
        test_method_desc = self.shortDescription()

        # 테스트 케이스마다 그에 맞는 세트의 데이터를 설정할 수 있다.
        # 물론 테스트용 데이터를 테스트 메서드 안에서 설정할 수도 있다.
        if test_method_desc == '중근의 경우':
            # x^2 - 2x + 1 = 0 -> x = [1]
            self.a = 1
            self.b = -2
            self.c = 1
            print("\n", test_method_desc, self.a, self.b, self.c)
        elif test_method_desc == '서로 다른 두 근의 경우':
            # x^2 -3x + 2 = 0 -> x = [1, 2]
            self.a = 1
            self.b = -3
            self.c = 2
            print("\n", test_method_desc, self.a, self.b, self.c)
        self.coef_set = [self.a, self.b, self.c]
    
    def tearDown(self):
        print("\n{} 테스트 끝.".format(self.shortDescription()))

    def test_diff_roots(self):
        """서로 다른 두 근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1, 2])

    def test_equal_root(self):
        """중근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1])

if __name__ == '__main__':
    unittest.main()
```

```bash
python test_my_unittest.py

setUpClass에서 인사 드립니다.

 서로 다른 두 근의 경우 1 -3 2

서로 다른 두 근의 경우 테스트 끝.
.
 중근의 경우 1 -2 1

중근의 경우 테스트 끝.
.
tearDownClass에서 작별 인사 드립니다.

----------------------------------------------------------------------
Ran 2 tests in 0.057s

OK
```

## TestSuite 클래스

unittest 모듈에서는 각각의 test case 인스턴스들을 하나로 모아 실행시킬 수 있게 해주는 기능을 가지고 있는데, TestSuite 클래스를 통해 가능하다. 

test suite를 생성하고 실행시키는 예제를 먼저 보겠다. (solve_quadratic_eq 함수 정의 코드는 다른 모듈에 저장하였다)

```python
import unittest
import sympy as sp
from solveQuadraticEq import solve_quadratic_eq

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
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1, 2])

    def test_equal_root(self):
        """중근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1])

def suite_for_test():
    suite = unittest.TestSuite()
    #suite.addTest(TestSomething("test_diff_roots"))
    #suite.addTest(TestSomething("test_equal_root"))
    suite.addTest(unittest.makeSuite(TestSomething))
    return suite

if __name__ == '__main__':
    runner = unittest.TextTestRunner()
    test_suite = suite_for_test()
    runner.run(test_suite)
```

```bash
python -m unittest test_my_unittest
..
----------------------------------------------------------------------
Ran 2 tests in 0.051s

OK
```

위 예제의 suite_for_test() 함수 정의 코드 이후부터 참고하면 된다. 해당 함수 정의 코드에서 makeSuite() 호출 코드를 주석처리하고, 이미 주석처리된 두 줄의 코드를 주석 해제하고 실행해봐도 된다.

test suite를 생성하고 실행시키는 방법은 다음과 같다.

1. TestSuite 클래스의 인스턴스를 생성한다. 
    
    ```python
    suite = unittest.TestSuite()
    ```
    
2. TestSuite 인스턴스의 메서드인 addTest()에 원하는 TestCase 클래스 또는 해당 클래스를 상속받는 사용자 정의 (테스트) 클래스 이름을 대입한다. 이를 통해 해당 테스트가 suite에 추가되는 것이다.
    
    ```python
    suite.addTest(TestCase 클래스명)
    ```
    
3. 또는 makeSuite() 메서드를 통해서도 테스트를 추가할 수 있다. 
    
    ```python
    suite.addTest(unittest.makeSuite(TestCase 클래스명))
    ```
    
4. 다음과 같이 개별적인 테스트(테스트 메서드)를 suite에 추가할 수도 있다. 이 때 테스트 메서드명은 문자열로 대입한다.
    
    ```python
    suite.addTest(TestCase클래스명("testmethod명"))
    ```
    
5. unittest 모듈의 TextTestRunner 클래스의 인스턴스를 생성한다.
    
    ```python
    runner = unittest.TextTestRunner()
    ```
    
6. 해당 인스턴스의 run() 메서드에 TestSuite 객체를 대입하고 호출하면 suite안의 모든 테스트들을 실행시킬 수 있다.
    
    ```python
    runner.run(test_suite)
    ```
    

이 모든 과정(2번은 없지만)이 위 예제 4-1에서 suite_for_test 함수 정의문부터 구현되어 있다.

- TestSuite 클래스에서 정의된 메서드들

| Methods | Detail |
| --- | --- |
| addTest() | test suite에 테스트 메서드를 추가 |
| addTests() | 여러 개의 TestCase 클래스 내 테스트 메서드들을 한꺼번에 추가 |
| run(result) | suite 내 테스트 실행. 테스트 결과를 test result 객체에 수집함 |
| debug() | suite 내 테스트 실행. 테스트 결과를 수집하지 않음. |
| countTestCases() | TestSuite 테스트 객체에 있는 테스트들의 개수를 반환 |

## TestLoader 클래스

TestLoader 클래스는 테스트 코드가 작성된 여러 개의 클래스나 모듈로부터 테스트를 생성하기 위해 사용되는 클래스이다. 기본적으로는 unittest.main() 호출 시, unittest.defaultTestLoader 인스턴스가 자동적으로 생성된다. 그러나 명시적으로 TestLoader 클래스 인스턴스를 명시하여 사용하면 몇몇 특성들을 개량할 수 있다. 

다음은 TestLoader 객체를 통해 두 테스트 클래스로부터 테스트들을 하나로 수집하는 예제이다.

```python
import unittest
import sympy as sp
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

    def test_diff_roots(self):
        """서로 다른 두 근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1, 2])

    def test_equal_root(self):
        """중근의 경우"""
        result = solve_quadratic_eq(self.coef_set)
        self.assertEqual(result, [1])

class TestSomething2(unittest.TestCase):
    def test_cubic_eq(self):
        """
        3차 방정식을 푸는 함수 테스트.
        """
        coef_set = [1, -6, 11, -6]
        result = solve_cubic_eq(coef_set)
        self.assertEqual(result, [1, 2, 3])

if __name__ == '__main__':
    test_class_list = [TestSomething, TestSomething2]
    test_load = unittest.TestLoader()

    test_list = []
    for test_case in test_class_list:
        test_suite = test_load.loadTestsFromTestCase(test_case)
        test_list.append(test_suite)

    new_suite = unittest.TestSuite(test_list)
    runner = unittest.TextTestRunner()
    runner.run(new_suite)
```

- 참고
    
    참고로, 테스트에 쓰인 함수들이 저장된 solveEq 모듈에는 다음과 같이 코드가 작성되어 있다.
    
    ```python
    import sympy as sp
    
    def solve_quadratic_eq(coef: list):
        """
        ax^2+bx+c=0을 주어진 a, b, c에 대해서 풀고 그 해를 리턴.
        coef = [a, b, c]
        """
        x = sp.symbols('x')
        a, b, c = sp.symbols('a b c')
        f = a*x**2 + b*x + c
        f = f.subs({a: coef[0], b: coef[1], c: coef[2]})
        set_eq = sp.Eq(f, 0)
        solutions = sp.solve(set_eq)
        return solutions
    
    def solve_cubic_eq(coef:list):
        """
        ax^3 + bx^2 + cx + d = 0을 주어진 a, b, c, d에 대해 풀고 그 해를 리턴.
        coef = [a, b, c, d]
        """
        x = sp.symbols('x')
        a, b, c, d = sp.symbols('a b c d')
        coef_set = [a, b, c, d]
        f = a*x**3 + b*x**2 + c*x + d
        f = f.subs({coef_set[i]: coef[i] for i in range(len(coef))})
        set_eq = sp.Eq(f, 0)
        solutions = sp.solve(set_eq)
        return solutions
    ```
    

TestLoader 클래스에 정의되어 있는 메서드에는 다음이 존재한다.

| Methods | Details |
| --- | --- |
| loadTestsFromTestCase() | TestCase 클래스 내 모든 test case들의 suite를 반환 |
| loadTestsFromModule() | 명시된 모듈 내 모든 test case들의 suite 반환 |
| loadTestsFromName() | “testmodule.testclass.testmethod”처럼 주어진 문자열에 대한 모든 test case의 suite 반환 |
| discover() | 지정된 시작 디렉토리로부터 모든 하위 디렉토리를 검색하여 나오는 모든 테스트 모듈을 찾고, 이에 대해 TestSuite 객체로 반환 |

## TestResult 클래스

TestResult 클래스는 여러 테스트들의 결과를 저장한다. TestRunner.run() 메서드가 TestResult의 인스턴스를 반환한다.

TestResult 인스턴스에는 다음의 속성들이 있으며, 해당 속성들을 적절히 활용하면 테스트 결과 출력을 사용자 자의대로 편집할 수 있을 것이다.

- errors: (TestCase_인스턴스, traceback_문자열) 형태의 튜플을 요소로 가지는 list이다. 튜플 속에는 테스트 도중 예기치 못하게 발생한 예외가 생긴 테스트를 담고 있다. (테스트를 통과 못한 게 아니라, 테스트 코드 자체를 실행할 때 발생한 예기치 못한 예외에 대해서 다룬다)
- failures: (TestCase_instance, traceback_문자열) 형태의 튜플을 요소로 가지는 list이다. 각 튜플 속에는 TestCase.assert*() 메서드 호출을 통한 테스트 통과에 실패한 테스트를 포함하고 있다.
- skipped: (TestCase_instance, 테스트_스킵_이유_문자열) 형태의 튜플을 요소로 가지는 list이다.
- wasSuccessful() : 여태까지 실행한 모든 테스트들이 통과했으면 True, 아니면 False를 반환.
- stop() : 실행되고 있는 테스트들을 중단해야할 때 호출하면 된다. (stop()을 호출하여 실행 중인 테스트 중단을 허용하려면 shouldStop()의 인자로 True를 설정해야 하는 것 같다)
- startTestRun() : 모든 테스트가 실행되기 전에 한 번 호출된다.
- stopTestRun() : 모든 테스트가 실행된 후에 한 번 호출된다.
- testsRun: 지금까지 실행된 모든 테스트의 수를 표현.

⇒ 이 외에도 여러 속성들이 있으니 자세한 내용은 References의 [2] 참조. 

다음은 테스트 결과를 사용자 마음대로 출력하도록 한 코드이다. 예제 4-1에서 이어서 쓴다.

```python
def print_test_result(title: str, result_attr):
    """
    TestResult의 속성을 이용하여 원하는 결과 출력하는 함수.
    title: 테스트 결과 출력 제목.
    result_attr: TestResult의 속성
    """
    print_time = 10
    print("=" * print_time + title + "=" * print_time)
    print(result_attr)
    print("=" * print_time * 2 + "=" * len(title))

if __name__ == '__main__':
    runner = unittest.TextTestRunner()
    test_suite = suite_for_test()
    result = runner.run(test_suite)
    print_test_result('에러', result.errors)
    print_test_result('실패', result.failures)
    print_test_result('스킵', result.skipped)
    print_test_result('테스트 성공 횟수', result.wasSuccessful())
    print_test_result('테스트 수', result.testsRun)
```

```bash
python test_my_unittest.py
..
----------------------------------------------------------------------
Ran 2 tests in 0.046s

OK
==========에러==========
[]
======================
==========실패==========
[]
======================
==========스킵==========
[]
======================
==========테스트 성공 횟수==========
True
=============================
==========테스트 수==========
2
=========================
```

---

References

[1] tutorialspoint, “UNITTEST FRAMEWORK”, (2016)

[2] [unittest — 단위 테스트 프레임워크](https://docs.python.org/ko/3/library/unittest.html)