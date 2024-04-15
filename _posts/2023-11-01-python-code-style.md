---
layout: single
title:  "[Python] Code style"
categories: ["Python"]
tag: ["Python", "code style", "PEP-8", "style guide", "스타일 가이드", "가독성"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 개요

이 문서에서는 코드의 가독성을 향상시키기 위해 관련 내용들을 작성하고자 만든 문서이다. 

# PEP-8에 따른 코드 스타일

파이썬 공식 문서 중 PEP-8에서는 파이썬으로 코드 작성 시 어떤 스타일로 코드를 작성할 지 그 기준을 제시하고 있다. 물론 이 코드 스타일 기준을 무조건 따르라고 강요하지는 않는다. 코드 스타일 가이드를 제시한 것은 일관성있는 코드 스타일을 유지하여 코드의 가독성을 높이기 위함이라고 명시하고 있다. 심지어 이 코드 스타일 가이드 중 일부 내용을 따라했을 때 오히려 가독성을 헤친다면 그 기준을 무시해도 좋다고까지 말하고 있다. 그럼에도, 코드 가독성 유지에 좋을 것으로 판단되는 내용들이 있어 일부 내용들을 기록하고자 한다. 

(다음에 나올 내용들은 변수명, 함수명, 클래스명 등 모든 이름들을 영어로 짓는 것을 가정한다)

(파이썬 표준 라이브러리가 지켜야 할 조건들도 명시되어 있지만 파이썬 표준 라이브러리를 개인이 제작할 일이 왠만해선 없다고 판단하여 해당 내용은 제외하였다)

(PEP-8에 대한 전체 내용은 References [2] 참고)

## 들여쓰기(Indentation)

- 괄호 안 내용 들여쓰기

괄호 안 내용이 길어서 여러 줄에 걸쳐서 쓰고자 할 때에는 두 가지 방법 중 하나를 선택할 수 있다. 

1. 괄호 안의 내용들이 모두 수직으로 정렬되도록 한다. 
2. hanging indentation을 사용한다. 여기서 hanging indentation은 원래는 단락의 첫 줄을 제외한 나머지 모든 줄을 들여쓰기 하는 방식으로, 파이썬에서는 여는 괄호 ‘(’를 첫 줄의 마지막으로 하고, 다음 줄부터는 닫는 괄호 ‘)’가 나올 때까지 들여쓰는 방식이다. 

```python
# Correct:

# Aligned with opening delimiter.
foo = long_function_name(var_one, var_two,
                         var_three, var_four)

# Add 4 spaces (an extra level of indentation) to distinguish arguments from the rest.
def long_function_name(
        var_one, var_two, var_three,
        var_four):
    print(var_one)

# Hanging indents should add a level.
foo = long_function_name(
    var_one, var_two,
    var_three, var_four)
```

## 한 줄 길이 제한

코드의 한 줄의 최대 길이는 79자로 제한한다. 독스트링이나 주석과 같은 텍스트로만 이루어진 텍스트 블록의 경우에는 한 줄 당 72자로 제한한다. 이러한 결정은 보통 둘 이상의 코드 파일들을 화면의 양 옆에 띄우고 볼 때 한 줄의 코드가 한 눈에 보이게 하기 위함이라고 한다. 원할 경우 코드의 길이를 99자로 그 제한 길이를 늘일 수 있으나 이 때에도 텍스트 블록의 한 줄 길이는 72자로 제한한다. 만약 한 줄 최대 길이를 초과하는 코드가 있으면 백슬래쉬(\)를 이용하여 여러 줄로 작성하도록 한다. 

## 이항 연산자가 포함된 줄의 줄바꿈 방법

이항 연산자가 포함된 줄이 너무 길어 줄바꿈하고자 한다면, 두 번째 줄부터 각 줄의 시작 문자가 이항 연산자가 되도록 한다. 즉 다음과 같이 한다.

```python
# Correct:
# easy to match operators with operands
income = (gross_wages
          + taxable_interest
          + (dividends - qualified_dividends)
          - ira_deduction
          - student_loan_interest)
```

만약 이항연산자가 각 줄의 맨 뒤에 위치하게끔 줄바꿈을 하면 피연산자 변수명의 길이가 제각기 다를 경우 해당 연산자가 어디에 적용되는 것인지 그 가독성이 좋지 않다. 

## 빈 줄

최상위 함수(top-level)와 클래스 정의부의 위아래로 2줄의 빈 줄을 삽입한다. 반면 메서드 정의부는 한 줄만 띄어쓴다. 

여러 개의 함수들이 정의되어 있을 때, 서로 관련 있는 함수들만 그룹으로 모아 그 그룹의 위아래로 빈 한 줄을 더 추가해도 된다고 한다. → 하지만 난 개인적으로 빈 줄보다는 #====== 처럼 주석을 이용해서 구분하는 게 더 명시적이라서 이 방법을 사용하고 있다. 그리고 서로 밀접한 연관이 있는 함수들이라면 각각 개별의 함수들로 만들기보다는 가능하다면 하나의 클래스의 메서드로 넣음으로서 그룹으로 묶어서 해당 함수들의 상호 연관성 또는 의존성 및 다른 그룹의 함수들과의 구분성을 유지하는 방법을 택하고 있다. 

함수 내부에서도 논리적 섹션에 따라 빈 한 줄을 넣어 구분해도 된다. 

## Import

- import 키워드로 모듈을 임포트할 때에는 각자 개별 라인에서 정의한다.

```python
# Correct:
import os
import sys

# Wrong:
import sys, os
```

- import 문은 모듈 수준의 독스트링과 주석 부분의 아래, 그리고 모듈의 전역변수 및 상수 정의부의 위쪽 부분에 위치시킨다. 또한 import 부분 내부에서도 표준 라이브러리가 최상위에, 관련 third party 모듈이 그 다음에 로컬 라이브러리 import 문을 작성한다. 각 부분의 경계 부분에 한 줄을 띄어서 서로 구분되게끔 해준다.
- 되도록 from … import *처럼 * 와일드카드를 사용하지 않도록 한다. 해당 모듈 내의 모든 것을 임포트하는 기능을 가지지만, 해당 모듈 제작자가 외부에서 사용하지 못하도록 막은 코드들도 있기에 결과적으로 무엇을 임포트하고자 하는지 이해하는데에 혼동이 올 수 있기 때문이다.

## 여백(Whitespace)

- 소괄호’()’, 중괄호({}), 대괄호([])와 그 안에 들어가는 표현식 사이에는 공백을 넣지 않는다.

```python
# Correct:
spam(ham[1], {eggs: 2})

# Wrong:
spam( ham[ 1 ], { eggs: 2 } )
```

- 요소가 단 하나뿐인 튜플에 대해서, 콤마와 그 뒤의 닫는 괄호 사이에 공백을 넣지 않는다.

```python
# Correct:
foo = (0,)

# Wrong:
bar = (0, )
```

- 줄 맨 뒤에 공백을 삽입하지 않는다. 어차피 공백은 사람 눈에는 보이지 않아 결국 불필요하기 때문이다.
- 서로 우선순위 다른 연산자들이 사용될 때, 더 낮은 우선순위를 가지는 연산자의 주변에 공백 한 칸 넣는 것을 고려해볼 수 있다. (그러나 이는 선택사항)

```python
result = 2 + 2*2
```

- 함수 어노테이션(annotation)의 경우, 변수명 뒤의 콜론(:)과 그 뒤로 나오는 변수 타입 사이에 공백을 하나 넣는다.

```python
def func(num: int): ...
```

- 함수의 키워드 인자나 매개변수의 기본값 설정 시 등호(=) 근처에 공백을 삽입하지 않는다.

```python
def func(num=1):
    return other_func(mode=True)   # O

def func(num = 1):
    return other_func(mode = True) # X
```

- 단, 어노테이션과 기본값 설정이 동시에 이뤄지는 경우, 등호 근처에 공백을 삽입해도 된다.

```python
def func(num: int = 1): ... # O
```

## Trailing comma

리스트, 딕셔너리와 같은 자료구조에서 요소를 대입할 때 맨 마지막에 추가된 콤마를 trailing comma라 한다. 

```python
a_list = [
    'wow',
    'how',
]
some_func(input1,
          input2,
          )
```

프로그램이 실행될 때 리스트와 같은 자료구조에 요소들이 더 추가될 수 있기에 마지막에 콤마를 부여한다. 하지만 이러한 trailing comma는 위 예시처럼 여러 줄에 걸쳐서 작성할 때 쓸 수 있고, 한 줄에는 쓸 수 없다. 

```python
# Wrong:
FILES = ['setup.cfg', 'tox.ini',]
initialize(FILES, error=True,)
```

## Docstring

모든 public한 모듈, 함수, 클래스, 메서드에 대해서는 독스트링을 작성한다. 그러나 public이 아닌 것에 대해서는 독스트링을 반드시 작성할 필요는 없다. 대신 def 라인 바로 아래 독스트링 대신 # 주석을 작성할 수 있다. 

독스트링을 여러 줄에 걸쳐 작성할 경우, 맨 마지막 줄은 닫는 따옴표로 마무리 짓는다. 

```python
"""독스트링 첫 줄
두 번째 줄
"""
```

## Naming style

- 변수명, 함수명 앞에 하나의 언더바(underscore, _)가 붙으면 해당 변수 또는 함수는 public이 아니라 내부에서만 사용할 목적이라는 뜻이다. from … import *와 같이 임포트할 때 앞에 언더바 하나로 시작하는 객체들은 임포트되지 않도록 방지할 수 있다. (이 경우, \_\_all\_\_ 변수에 외부에 공개할 객체들의 목록만을 작성함으로써 가능케 할 수 있다)
- 반면 이름의 맨 뒤에 언더바 하나를 덧붙이는 경우는 파이썬에 이미 존재하는 예약어와 겹칠 경우이다. 예를 들면 from_, class_ 같이 사용할 수 있다.
- 이름 맨 앞에 언더바가 두 번 붙는 경우, 이것이 클래스 내부에 쓰일 경우 외부에서 해당 속성을 접근하지 못하도록 막을 수 있다.
- 이름 앞 뒤로 언더바가 두 개가 붙는 이름들은 보통 special methods, attributes 같은 것들이다. \_\_init\_\_, \_\_import\_\_같은 것들인데, 이들은 모두 파이썬에서 이미 약속해둔 이름들과 같은 것이라서, 이런 형태의 이름을 사용자가 만들지 않도록 한다.
- 모듈, 패키지 이름은 되도록 짧게 하며, 필요한 경우 언더바(_)를 넣어 단어들을 구분할 수 있다. (물론 최대한 언더바를 사용하지 않는게 가독성 측면에서 더 좋긴 하다)
- 클래스 이름은 SomeClass와 같이 각 단어의 앞글자들이 무조건 대문자로 쓰여야 한다.
- 함수 및 변수 이름은 모두 소문자로 하고, 띄어쓰기는 언더바로 대체한다. 이는 메서드와 인스턴스 변수에도 적용된다.
- 상수는 모든 글자를 대문자로 사용한다. ex) CONSTANT=3.14

## Public and Internal interface

파이썬에서는 public이란 용어는 사용해도 private란 용어는 사용하지 않는다. 대신 internal이란 단어를 쓴다. public은 모듈이나 클래스, 메서드, 인스턴스 속성들을 외부에서 사용할 수 있는 상태이고, internal은 특정 모듈이나 클래스 내부에서만 사용하고 외부에서 꺼내서 사용하는 용도가 아닌 것들을 말한다. private란 단어가 파이썬에서 안 쓰이는 이유는 아마 파이썬에서는 강제로 외부에서 특정 객체를 못 쓰도록 방지하는 기능이 없기 때문인 것으로 추측된다. (설령 self.__private와 같이 앞에 두 개의 언더바를 쓰면 name mangling 즉, 일반적으로는 외부에서 접근할 수 없게 되지만 이마저도 obj._ClassName__private와 같이 쓰면 접근이 가능해진다)

위의 독스트링 부분에서도 언급했듯, 어떤 인터페이스에 독스트링을 통해 문서화가 되어있다면 해당 독스트링에 달리 명시되지 않는 한 해당 인터페이스는 public으로 취급한다. 반대로 독스트링이 없는 인터페이스는 internal로 취급된다. 

이를 명확히 하기 위해 public API에 \_\_all\_\_ 속성에 리스트로 외부에 공개하고자 하는 객체명들을 입력하면 해당 객체들을 외부에서 사용할 수 있도록 해준다. 반대로 말하면, \_\_all\_\_ = []이라고 하는 경우, 외부에서 사용할 public API가 없다는 뜻이기도 하다. 

internal한 패키지, 모듈, 클래스, 함수, 속성 등은 모두 이름 앞에 언더바 하나를 추가하여 해당 객체가 internal임을 명시한다. 

## 그 외

- 문자열끼리 합치고자 하는 경우(concatenation), a + b 처럼 덧셈 연산자를 사용하기 보다는 ‘’.join([a, b])처럼 join() 함수를 사용한다. 해당 함수는 입력값들의 개수에 상관없이 선형 시간 안에 문자열들을 합쳐주기 때문이다.
- None과 같은 singleton의 비교는 ‘==’ 대신 is, is not을 사용한다. (if x is not None)
- 함수 내에 조건에 따라 반환값이 달라질 때, 그 중 하나로 None을 반환할 수 있다면, return 또는 return문 생략 대신 return None이라고 명확하게 명시한다.

```python
# Correct:
def foo(x):
    if x >= 0:
        return math.sqrt(x)
    else:
        return None

def bar(x):
    if x < 0:
        return None
    return math.sqrt(x)

# Wrong:
def foo(x):
    if x >= 0:
        return math.sqrt(x)

def bar(x):
    if x < 0:
        return
    return math.sqrt(x)
```

- 문자의 앞 또는 뒤글자와의 매치 확인을 위해 문자열을 슬라이싱하기보다는 ‘’.startswith(), ‘’.endswith() 함수를 사용하도록 한다.
- 두 객체의 타입이 같은지 비교할 때에는 isinstance()을 사용한다.
- boolean 값과의 비교는 되도록 하지 않는다.

```python
# Correct:
if greeting:

# Wrong:
if greeting == True:

# Worse
if greeting is True:
```

---

References

[1] [When Good Comments Go Bad](https://blog.codinghorror.com/when-good-comments-go-bad/)

[2] [PEP 8 – Style Guide for Python Code \| peps.python.org](https://peps.python.org/pep-0008/)