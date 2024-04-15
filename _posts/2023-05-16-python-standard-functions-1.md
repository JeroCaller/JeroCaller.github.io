---
layout: single
title:  "[Python] 내장 함수 모음 (1)"
categories: ["Python"]
tag: ["Python", "내장 함수"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
use_math: true  # 수식이 없는 포스트 글에는 해당 속성 자체를 삽입하지 않으면 됨.
---
# abs

abs(x)는 숫자를 입력받으면 그 수의 절대값을 리턴하는 함수이다.

```python
print(abs(10))
print(abs(-19))
print(abs(-3.4))
```

```python
10
19
3.4
```

# all

all(x)는 반복 가능한(iterable) 데이터를 입력받으면 해당 데이터 내 모든 요소가 True이면 True를, False가 하나라도 있으면 False를 반환한다.

```python
print(all([1, 2, 3]))
print(all([1, 2, 0]))
print(all(['1', '2', '']))
print(all([]))  # 요소가 비어있는 경우에는 True를 반환.
```

```python
True
False
False
True
```

# any

any(x)는 반복 가능한 데이터를 입력값으로 받으며, all과 반대로 데이터의 요소 중 하나라도 True가 있으면 True를 반환, 요소가 모두 False면 False를 반환한다. 

```python
print(any([1, 2, 0]))
print(any([0, '']))
print(any([]))  # 비어있는 경우, False를 리턴.
```

```python
True
False
False
```

# chr

chr(x)는 유니코드 숫자값을 입력받아 그에 해당하는 문자를 반환하는 함수이다. 

유니코드 검색 사이트 - 
[SYMBL (◕‿◕) 기호, 이모지, 히에로글리프, 스크립트, 알파벳 및 모든 유니코드](https://symbl.cc/kr/)

```python
print(chr(100))
print(chr(44033))
```

```python
d
각
```

# ord

ord(문자)는 문자의 유니코드 숫자값을 반환하는 함수이다. chr()의 반대라고 보면 되겠다.

```python
print(ord('d'))
print(ord('각'))
```

```python
100
44033
```

# dir

dir()는 객체가 지닌 변수 및 함수를 보여주는 함수이다. 

```python
print(dir([1, 2, 3]))  # list의 함수나 메서드, 변수 등을 보여줌
print(dir({'a': 1}))  # dict의 함수나 메서드, 변수 등을 보여줌
```

```python
['__add__', '__class__', '__class_getitem__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__iadd__', '__imul__', '__init__', '__init_subclass__', '__iter__', '__le__', '__len__', '__lt__', '__mul__', '__ne__', '__new__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__rmul__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'append', 'clear', 'copy', 'count', 'extend', 'index', 'insert', 'pop', 'remove', 'reverse', 'sort']
['__class__', '__class_getitem__', '__contains__', '__delattr__', '__delitem__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__getattribute__', '__getitem__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__ior__', '__iter__', '__le__', '__len__', '__lt__', '__ne__', '__new__', '__or__', '__reduce__', '__reduce_ex__', '__repr__', '__reversed__', '__ror__', '__setattr__', '__setitem__', '__sizeof__', '__str__', '__subclasshook__', 'clear', 'copy', 'fromkeys', 'get', 'items', 'keys', 'pop', 'popitem', 'setdefault', 'update', 'values']
```

# divmod

divmod(a, b)는 두 숫자를 입력 받아 a / b의 몫과 나머지를 튜플로 반환하는 함수이다.

```python
print(divmod(5, 3))  # (몫, 나머지)
print(5 // 3)  # 몫 
print(5 % 3)   # 나머지
```

```python
(1, 2)
1
2
```

# eval

eval(expression)은 문자열로 표현된 표현식을 실행한 결과값을 반환하는 함수이다.

```python
print(eval('1+1'))
print(eval("'nice' + 'to' + 'meet' + 'you'"))
print(eval('chr(40)'))
```

```python
2
nicetomeetyou
(
```

# filter

filter(func, iterable) 함수는 첫 번째 인수로 함수명을, 두 번째 인수로 해당 함수에 대입될 인수들을 차례대로 나열한 반복 가능한 데이터를 받는다. 그리고 두 번 째 인자의 반복 가능한 데이터 내 요소들의 순서대로 함수를 호출할 때 반환 값이 True인 것만 다시 iterable 데이터로 묶어 반환한다. 

다음은 3의 배수인 숫자에 대해서만 True를 반환하는 함수에 대해 filter 함수를 실행하여 3의 배수만 걸러내는 코드이다.

```python
def multipleOfThree(x): return x % 3 == 0
print(list(filter(multipleOfThree, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])))
```

```python
[3, 6, 9]
```

# hex

hex(x)는 정수를 입력받아 이를 16진수의 문자열로 반환하는 함수이다.

```python
print(hex(123))
print(hex(456))
print(type(hex(123)))
```

```python
0x7b
0x1c8
<class 'str'>
```

# id

id(object)는 인자로 입력받은 객체의 고유 주소값(레퍼런스)을 반환하는 함수이다.

```python
class myClass: pass

a = 1
b = a
some_inst = myClass()
print(id(1))
print(id(a))
print(id(b))
print(id(2))
print(id(some_inst))
print(id(myClass))
```

```python
1948086894832
1948086894832
1948086894832
1948086894864
1948109845024
1948100698784
```

위 예제에서, 1, a, b의 고유 주소값이 모두 동일하다. 즉, 이 셋 모두 같은 객체를 가리키고 있다는 뜻이다. 반면 그 외 2, some_inst와는 주소값이 다르므로 서로 다른 객체를 가리킨다고 볼 수 있다. 

# int

int(x)는 실수나 문자열 형태의 숫자를 정수로 반환하는 함수이다. 정수를 입력받으면 정수 그대로 반환한다.

```python
print(int('1'))
print(int(3.141592))  # 소수점은 모두 버리고 정수 부분만 반환함.
```

```python
1
3
```

그런데 문자열 형태의 실수는 정수로 반환되지 않고 에러를 발생한다.

```python
print(int('3.141592'))
```

```python
ValueError: invalid literal for int() with base 10: '3.141592'
```

이 경우, 문자열 형태의 실수를 우선 float() 함수를 통해 실수로 변환시킨 다음에 이 실수를 int() 함수를 통해 정수로 변환해야한다.

```python
print(int(float('3.141592')))
```

```python
3
```

int(x, radix)는 radix 진수로 표현된 문자열 x를 10진수로 변환하여 반환한다. 

```python
print(int('111', 2))  # 2진법으로 표현된 111을 10진수로 변환
print(int('1a', 16))  # 16진법으로 표현된 1a를 10진수로 변환
```

```python
7
26
```

# isinstance

isinstance(object, class) 함수는 각각 객체와 클래스를 인자로 받고, 해당 객체가 해당 클래스의 인스턴스인지를 판별해주는 함수로, 맞으면 True, 아니면 False를 반환.

```python
class MyClass: pass

a = MyClass()
b = 1
print(isinstance(a, MyClass))
print(isinstance(b, MyClass))
```

```python
True
False
```

# map

map(func, iterable)은 함수와 반복 가능한 데이터를 인자로 받고, 데이터의 각 요소들을 함수에 대입했을 때의 결과를 반복 가능한 데이터로 반환하는 함수이다.

다음은 팩토리얼 함수를 구현하고, 해당 함수에 여러 숫자들을 대입했을 때 각각의 결과값을 얻는 예제이다.

```python
def factorial(n):
    if n == 1: return 1
    return n * factorial(n-1)

input_data = [1, 2, 3, 4]
result_list = list(map(factorial, input_data))
print(result_list)
```

```python
[1, 2, 6, 24]
```

# max

max(iterable)은 반복 가능한 데이터를 입력받아 데이터 내 요소들 중 최대값을 반환하는 함수이다.

```python
print(max([1, 2, 3]))
print(max('python'))
print(max('파이썬'))
```

```python
3
y
파
```

# min

min(iterable)은 max 함수와 반대로 최소값을 반환하는 함수이다.

```python
print(min([1, 2, 3]))
print(min('python'))
print(min('파이썬'))
```

```python
1
h
썬
```

# pow

pow(x, y) 는 \\(x^y\\) 의 값을 반환하는 함수이다.

```python
print(pow(2, 3))
```

```python
8
```

# round

round(number[, ndigits]) 함수는 입력받은 실수를 반올림하여 반환하는 함수이다. 두 번째 인수는 반올림하여 표시하고자 하는 소수점의 자리수를 의미하며, 생략할 수 있다.

```python
print(round(1.2))
print(round(1.5))
print(round(1.1782, 2))
```

```python
1
2
1.18
```

# sum

sum(iterable) 함수는 입력된 데이터 내 모든 요소의 합을 구하는 함수이다.

```python
print(sum([1, 2, 3]))
```

```python
6
```

---

Reference

[1] [05-5 내장 함수](https://wikidocs.net/32)