---
layout: single
title:  "[Python-basic] 변수, 이름, 객체"
categories: "Python"
tag: ["Python", "basic"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

파이썬에서의 자료형(data type)에는 다음이 있다

- boolean: True or False
- integer: 정수를 표현
- float: 실수를 표현
- string: 문자열

파이썬에서 모든 것은 객체(object)이다. 객체는 데이터를 담는 상자와도 같다.

객체는 자료형을 가진다. 이를 통해 데이터로 무엇을 할지 결정할 수 있다.

객체에 들어있는 데이터 값이 변할 수 있으면 mutable, 변할 수 없으면 immutable이라 한다. 그러나 자료형은 둘 다 바꿀 수 없다.

파이썬은 철저한 자료형 중심(strongly typed)이다. 즉, 객체의 자료형이 변하지 않는다.

변수(variables): 사용자가 정의한 값을 컴퓨터 메모리에서 참조하는 이름. 변수에 값을 할당하기 위해서 = 기호를 사용한다.

변수는 값을 참조하는 것이지 값 그 자체가 아니다. 할당한다는 것은 값을 복사한다는 것이 아니라, 그 데이터가 포함된 객체에 이름을 붙이는 것일 뿐이다.

```python
a = 1
b = a
print(a, b)
```

```python
1 1
```

위 코드에서 변수 b에 변수 a에 들어있는 값을 복사한 것이 아니다. 1이 들어있는 객체에 b라는 또 다른 이름을 붙인 것 뿐이다.

어떤 값에 대한 자료형을 알고 싶다면 type(값)을 이용한다.

```python
a = 1 
print(type(a))
```

```python
<class 'int'>
```

### 변수의 이름 명명 규칙

1. 오직 다음의 문자들만 포함할 수 있다.
    1. 영어 대소문자
    2. 숫자 (0~9)
    3. 밑줄(underscore, _)
2. 이름은 숫자로 시작할 순 없다.
    1. 예) 123basic → X
3. 예약어 (reserved word)를 변수 이름으로 쓸 수 없다.
    1. 예) False, class, is, lambda 등등
    2. 두 개의 언더바 ('\_\_') 로 시작하고 또 두 개의 언더바로 끝나는 이름은 예약어이다. 예) \_\_name\_\_ , \_\_doc\_\_ 등등

---

Reference

[1] Bill Lubannovic, "Introducing Python",  (O'REILLY, 2015)