---
layout: single
title:  "[Python] Traceback"
categories: ["Python"]
tag: ["Python", "Traceback", "call stack", "호출 스택"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 개요

파이썬으로 코딩을 할 때마다 우린 종종 에러를 마주치게 된다. 그리고 에러를 마주칠 때마다 파이썬에서는 그 에러에 대해 어떠한 긴 글의 정보를 우리에게 보여준다. 다음이 그 예이다.

```python
def mul(a, b):
    result = a * b
    return result

def divide(a, b):
    result = a / b
    return result

def calculator(a, b):
    mul_result = mul(a, b)
    divide_result = divide(a, b)
    print(f"{a} * {b} = {mul_result}")
    print(f"{a} / {b} = {divide_result}")

number1 = 2
number2 = 0
calculator(number1, number2)
```

```python
Traceback (most recent call last):
  File "C:\python_projects\text_poker\study.py", line 17, in <module>  
    calculator(number1, number2)
  File "C:\python_projects\text_poker\study.py", line 11, in calculator
    divide_result = divide(a, b)
  File "C:\python_projects\text_poker\study.py", line 6, in divide     
    result = a / b
ZeroDivisionError: division by zero
```

Traceback이라는 단어로 시작되는 글이 보이는데, 이것은 코드 실행 시 어떤 종류의 에러가 발생했으며, 그 에러가 발생될 때 관련된 코드 내 모든 정보들을 보여준다. 이를 통해 개발자는 코드의 어느 부분이 문제인지 파악할 수 있고 이를 통해 틀린 부분을 고쳐 문제를 해결할 수 있다. 

이 문서에서는 traceback에 대해 살펴보고, 파이썬 내장 모듈인 traceback에 대해서도 살펴보겠다.

# 호출 스택 (call stack)

traceback이란 단어는 “역추적”이란 뜻이다. 즉, 에러가 난 원인을 거꾸로 추적하여 이를 우리에게 보여주는 것이다. 무언가를 추적하려면 단서라는 게 있어야 한다. 이 때, 호출 스택이란 것을 단서로 삼아 에러 원인을 역추적한다. 호출 스택은 코드 실행 시 코드 내 정의된 함수 또는 변수가 저장되는 메모리로, 스택 구조로 저장되어 먼저 호출 스택내에 들어온 정보가 나중에 나가게 되는 선입후출(FILO) 형태를 띈다. 

코드 실행 시 함수, 변수 등의 정보가 어떻게 호출 스택에 저장되는지 살펴보겠다. 다음은 예제 1-1을 통해 그 과정을 살펴보겠다. 

코드가 실행되면 먼저 호출 스택에는 전역 프레임(global frame)이라는 곳에 전역 변수 및 함수가 저장된다. 여기서 프레임(또는 스택 프레임)은 호출 스택이라는 메모리 내에서 함수 및 그 함수에 속한 변수가 저장되는 독립적 공간이다. 예제 1-1에서 전역 프레임에 들어가는 데이터는 number1, number2의 전역 변수와 mul, divide, calculator 함수이다. 이 때, 전역 프레임 안에 저장된 함수들은 아직 호출은 되지 않은 상태이다. 지금까지 묘사한 것을 아래와 같이 임의로 시각화해보았다.  

```python
호출 스택
--------
{
	전역 프레임
	---------
	mul
	divide
	calculator
	number1: 2
	number2: 0
}
```

예제 1-1을 보면 맨 먼저 전역 공간에서 calculator() 함수를 호출한다. 해당 함수 호출 시 호출 스택에 해당 함수의 프레임이 형성되어 저장된다. 이를 표현하면 다음과 같다.

```python
호출 스택
--------
{
	전역 프레임
	---------
	mul
	divide
	calculator
	number1: 2
	number2: 0
}
{
	calculator 프레임
  ----------
  a: 2
  b: 0
  mul_result: ?
  divide_result: ???
}
```

호출 스택 내에서 caculator 함수에 대한 프레임이 형성되고(여기선 간단하게 calculator 프레임이라고 부르겠다), 해당 프레임에선 해당 함수의 매개변수와 지역변수의 정보가 저장된다. 

calculator 함수 정의 내에서 먼저 마주치게 되는 함수는 mul()이라는 함수의 호출부이다. 해당 함수가 호출되므로 호출 스택에도 mul 프레임이 추가된다. 

```python
호출 스택
--------
{
	전역 프레임
	---------
	mul
	divide
	calculator
	number1: 2
	number2: 0
}
{
	calculator 프레임
  ----------
  a: 2
  b: 0
  mul_result: ?
  divide_result: ???
}
{
	mul 프레임
  ---------
  a: 2
  b: 0
  result: 0
}
```

mul() 함수에서 작업이 끝나고 값을 반환하면 해당 함수는 종료된다. 이렇게 작업이 끝나게 된 함수는 호출 스택 내에서 사라지게 된다. 즉 다음과 같이 변한다. 

```python
호출 스택
--------
{
	전역 프레임
	---------
	mul
	divide
	calculator
	number1: 2
	number2: 0
}
{
	calculator 프레임
  ----------
  a: 2
  b: 0
  mul_result: 0
  divide_result: ???
}
```

calculator 함수 내에서 이번에는 divide 함수의 호출부를 마주치게 된다. mul 함수에서 설명했던 것과 같은 원리로 이번에는 divide 함수 프레임이 호출 스택에 추가된다. 

```python
호출 스택
--------
{
	전역 프레임
	---------
	mul
	divide
	calculator
	number1: 2
	number2: 0
}
{
	calculator 프레임
  ----------
  a: 2
  b: 0
  mul_result: 0
  divide_result: ???
}
{
	divide 프레임
  -----------
  a: 2
  b: 0
  result: ???
}
```

(2를 0으로 나눈 값은 존재하지 않는데, 실제로 호출 스택에서는 이 결과를 어떻게 저장하는지를 몰라 위에서는 그저 “???”으로 표시하였다)

여기서는 2를 0으로 나누는 작업이 진행되는데, 실제로도 2/0이란 것은 존재할 수 없다. 하지만 여기서는 설명을 위해 2/0이란 작업을 한 어떠한 결과값이 나왔다고 가정하고 설명하겠다. 어쨌든 divide 함수에서도 작업이 끝나면 호출 스택에서 해당 프레임은 사라지게 된다. 

```python
호출 스택
--------
{
	전역 프레임
	---------
	mul
	divide
	calculator
	number1: 2
	number2: 0
}
{
	calculator 프레임
  ----------
  a: 2
  b: 0
  mul_result: 0
  divide_result: ???
}
```

이제 calculator 함수에서는 다른 함수 mul(), divide()의 반환값을 토대로 그 결과를 출력하는 코드를 실행하고 그 후에 작업을 마친다. 그러면 호출 스택에서도 calculator 프레임이 사라진다. 

위 과정을 보면 알 수 있듯, 스크립트 실행 시 가장 먼저 호출되는 함수의 정보가 먼저 호출 스택에 저장되고, 나중에 호출된 함수 정보가 나중에 호출 스택에 저장되며, 실행은 가장 나중에 호출 스택에 들어간 함수가 실행되는 점을 보면, 함수 정보가 스택처럼 호출 스택에 저장되고 삭제되는 것을 확인할 수 있다. 그래서 이름도 “호출 스택”인 것이다. 

참고로, 전역 프레임은 스크립트 실행이 모두 끝나면 그 때 사라진다. 

> 재귀 함수 호출을 이용할 때에도 호출 스택이 사용된다. 그래프 알고리즘인 DFS를 구현할 때 재귀 함수를 사용할 수 있는 이유이기도 한다. 프로그래머가 스택 자료구조를 따로 정의하고 while 반복문을 이용하여 DFS를 구현할 수 있는데, 이 대신 호출 스택을 이용하여 재귀 호출을 통해 DFS를 구현할 수 있는 것이다. DFS에 관한 내용은 Depth First Search, DFS (깊이 우선 탐색) (그리고 backtracking) 문서 참조(해당 문서는 준비 중).
참고로 파이썬에서는 연속 재귀 호출 횟수가 1000번으로 제한되어 있다.
> 

다시 예제 1-1의 실행 후 traceback 에러문을 살펴보자. 자세히보면 위에서부터 코드 실행 시 먼저 호출되는 calculator 함수 호출 코드가 먼저 작성되어 있고, 그 뒤로 점점 나중에 호출되는 divide 함수와 해당 함수 내 지역 변수의 정보가 출력되어 있는 것을 확인할 수 있다. 파이썬에서 이렇게 역추적 방식으로 에러 발생에 관여되어 있는 모든 코드들을 보여줄 수 있는 것도 호출 스택의 존재덕분이다. 실제로, traceback과 비슷한 용어로 스택 추적(stack trace, stack backtrace, stack traceback)이란 용어가 있는데, 프로그램 실행 중 특정 시점에서의 호출 스택에 대한 보고를 의미한다. 

# 파이썬 내장 모듈 traceback

프로그램 실행 도중 에러가 발생해도 그 에러로 인해 멈추지 않고 계속 진행하도록 강제하고자 할 때 try-except 구문을 사용한다. 다음은 예제 1-1에 try-except구문을 적용한 예이다.

```python
def mul(a, b):
    result = a * b
    return result

def divide(a, b):
    result = a / b
    return result

def calculator(a, b):
    mul_result = mul(a, b)
    try:
        divide_result = divide(a, b)
    except ZeroDivisionError as e:
        print(f"에러 발생: {e}")
    else:
        print(f"{a} / {b} = {divide_result}")
    finally:
        print(f"{a} * {b} = {mul_result}")

number1 = 2
number2 = 0
calculator(number1, number2)
```

```python
에러 발생: division by zero
2 * 0 = 0
```

calculator 함수 내부를 보면, ZeroDivisionError 에러가 예상되는 부분에 try-except 구문을 통해 예외 처리를 하고 있음을 확인할 수 있다. 이 때, 해당 에러에 대한 메시지를 출력하도록 설정하였다. 그리하여 출력결과에 division by zero 라는 문구가 출력되었다. 그런데 여기서 원한 것은 앞서 예제 1-1의 traceback처럼 에러 원인에 대한 자세한 정보가 출력되기를 원한 것이었는데 여기선 단 한줄의 문구만 뜨고 더 자세한 원인에 대한 정보가 제공되지 않았다. 

try-except 구문 내에서도 traceback 정보를 출력하고자 한다면 파이썬 내장 모듈인 traceback을 import하여 사용하면 된다. 해당 모듈의 format_exc() 함수가 traceback 내용을 문자열로 반환하는 함수이다. 이를 이용하여 예제 2-1을 다음과 같이 수정하였다.

```python
import traceback

def mul(a, b):
    result = a * b
    return result

def divide(a, b):
    result = a / b
    return result

def calculator(a, b):
    mul_result = mul(a, b)
    try:
        divide_result = divide(a, b)
    except ZeroDivisionError as e:
        print(f"에러 발생: {e}")
        print(traceback.format_exc())  # 추가
    else:
        print(f"{a} / {b} = {divide_result}")
    finally:
        print(f"{a} * {b} = {mul_result}")

number1 = 2
number2 = 0
calculator(number1, number2)
```

```python
에러 발생: division by zero
Traceback (most recent call last):
  File "C:\python_projects\text_poker\study.py", line 14, in calculator
    divide_result = divide(a, b)
  File "C:\python_projects\text_poker\study.py", line 8, in divide
    result = a / b
ZeroDivisionError: division by zero

2 * 0 = 0
```

이제 traceback 내용이 출력되는 것을 확인할 수 있다. 이렇게 파이썬 내장 모듈인 traceback 모듈을 이용하면 try-except 구문을 사용하여 프로그램 실행 도중 에러가 발생해도 실행을 중단하지 않도록 하면서도 그 에러의 자세한 원인까지 출력하여 파악할 수 있다는 장점이 있다. 

---

References

[1] [파이썬 코딩 도장: 29.5 함수의 호출 과정 알아보기](https://dojang.io/mod/page/view.php?id=2341)

[2] [스택 추적](https://ko.wikipedia.org/wiki/스택_추적)

[3] [콜 스택](https://ko.wikipedia.org/wiki/콜_스택)

[4] [Understanding the Python Traceback – Real Python](https://realpython.com/python-traceback/)

[5] [traceback — 스택 트레이스백 인쇄와 조회](https://docs.python.org/ko/3/library/traceback.html)