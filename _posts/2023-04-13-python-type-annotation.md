---
layout: single
title:  "[Python] Type annotation"
categories: ["Python"]
tag: ["Python", "type annotation", "type alias"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
파이썬은 동적 프로그래밍 언어이다. 즉, 변수의 자료형을 중간에 바꿀 수 있다는 뜻이다. 

```python
a = 3
print(a)

a = 'three'
print(a
```

```python
3
three
```

하지만 정적 프로그래밍 언어에서는 애초에 변수를 선언할 때 변수의 자료형까지 하나로 고정시키고, 그 이후에도 해당 변수의 자료형을 바꿀 수 없다. 

파이썬과 같은 동적 언어는 변수의 자료형을 중간에 바꿀 수 있다는 점이 오히려 단점으로 작용할 때가 있다. 코드가 복잡해질 때 본래 int 자료형이던 변수를 이를 까먹고 str 자료형으로 쓰거나 하는 경우가 있을 수 있다. 이 때 에러가 걸리면 심한 경우 어디서 에러가 났는지 파악하는데에 많은 시간이 소요될 수 있다.

그래서 파이썬 3.5 버전부터 type annotation이라는 것을 지원한다. 이는 어떤 변수의 자료형이 무엇인지 그 힌트를 알려주는 것이다. 이 type annotation을 사용한다고 해서 다른 자료형의 데이터를 변수에 대입해도 에러는 나지 않는다. 

변수에 type annotation을 적용하는 방법은 다음과 같다.

```python
something_to_say : str = 'hello'
```

콜론 (:) 뒤에 str과 같이 자료형을 기입함으로써 해당 변수의 자료형이 str임을 암시해준다. 

이는 함수, 메서드의 매개변수에도 적용시킬 수 있다. 

```python
def some_func(num : int, say : str = 'hi') -> int :
    print(say)
    return num

var = some_func(5)
print(var)
```

```python
hi
5
```

위 예제와 같이 매개변수 뿐만 아니라 함수 또는 메서드의 리턴 타입도 명시할 수 있다. 

annotation type

- 정수 → int
- 문자열 → str
- 리스트 → list
- 튜플 → tuple
- 딕셔너리 → dict
- set → set
- Boolean → Bool
- 사용자 정의 클래스도 가능. 뒤에 소괄호 () 는 붙이지 않고 클래스명만 기입하면 됨.

# 반복형 안에 자료형

```python
def toOneString(lines: list[str]) -> str :
    result = ''
    for line in lines:
        result += line
    return result

some_list : list[str] = [
    'hi, ',
    'hello, ',
    'nice ',
    'to ',
    'meet ',
    'you.'
]

result = toOneString(some_list)
print(result, type(result))
```

```python
hi, hello, nice to meet you. <class 'str'>
```

위와 같이 문자열 요소들을 가지는 list 자료형임을 type annotation을 통해 표시할 수 있다. 이 때 대괄호 ([])를 사용한다. list[str] 은 요소들이 모두 문자열 자료형이고, 이들을 요소로 가지는 list 자료형이라는 뜻이다. 

# Type aliases

타입 힌트에 쓰일 타입명을 자신이 원하는 이름으로 변경하여 사용할 수도 있다.

```python
FilePath = str

def getFilePath(file_path: FilePath) -> str:
    return 'hello ' + file_path
```

명시적으로 정의하고 싶다면, typing 모듈의 TypeAlias를 이용하면 된다.

```python
from typing import TypeAlias

FilePath: TypeAlias =  str

def getFilePath(file_path: FilePath) -> str:
    return 'hello ' + file_path
```

TypeAlias는 3.10 버전부터 사용 가능하다고 한다. 

# typing 모듈

typing 모듈은 타입 힌트에 대한 도구를 제공해주는 표준 라이브러리이다. 

## Callable: 함수가 매개변수에 대입될 경우

함수의 매개변수 또는 반환값으로 함수 그 자체가 입력됨을 알릴려면 Callable을 이용하면 된다. Callable[[인자1, 인자2, …], returntype]으로 구성되며, 대괄호 안의 첫 번째에는 해당 함수에 들어갈 인자들의 타입 힌트를, 두 번째에는 해당 함수의 반환 타입을 명시하면 된다.

```python
from typing import Callable

def some_decor(func: Callable[[int], int])  -> Callable:
    def wrapper(*args, **kwargs):
        print(f"인자 개수: {len(args) + len(kwargs)}")
        result = func(*args, **kwargs)
        return result
    return wrapper

@some_decor
def add_all(*args):
    total = 0
    for num in args:
        total += num
    return total

if __name__ == '__main__':
    print(add_all(1, 2, 3, 5))
```

```python
인자 개수: 4
11
```

## Generics

Sequence, Iterable처럼 추상적인 타입에 대한 힌트도 제공된다.

```python
from typing import Sequence, Iterable

def some_func(a: Iterable[str], b: Sequence[int]):
    pass
```

## literal ellipsis: …

Tuple[int, …]와 같이 타입 힌트의 마지막 인자로 …을 대입하면 이전 인자인 int가 tuple 내에서 반복적으로 나타날 것을 알려준다. 

```python
from typing import Tuple

def some_func(a: Tuple[int, ...]):
    pass

some_func((1, 2, 3))
```

## Union

Union[A, B]는 A | B와 같다. 즉, A 또는 B 타입일 수 있다는 뜻이다. 파이썬 3.10 버전부터는 그냥 A | B라고 써도 된다고 한다.

```python
from typing import Union, Any

def some_func(a, b) -> Union[Any, None]:
    pass
```

## Optional

Optional[X] = X | None = Union[X, None]이다. 

```python
from typing import Optional

def some_func(a: Optional[str]): # str | None
    pass
```

## Final

Final은 해당 변수가 더 이상 재할당되거나 다른 값으로 오버라이딩되지 않는 상수임을 명시할 때 쓰인다.

```python
from typing import Final

constant: Final[bool] = True
constant = False # 실행 시 예외는 발생하지 않으나 경고가 뜸.
```

---

Reference

[1] [07-4 파이썬 타입 어노테이션](https://wikidocs.net/184212)

[2] [PEP 484 – Type Hints \| peps.python.org](https://peps.python.org/pep-0484/)

[3] [PEP 526 – Syntax for Variable Annotations \| peps.python.org](https://peps.python.org/pep-0526/)

[4] [[파이썬] typing 모듈로 타입 표시하기](https://www.daleseo.com/python-typing/)

[5] [typing — Support for type hints](https://docs.python.org/3/library/typing.html)