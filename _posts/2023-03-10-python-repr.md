---
layout: single
title:  "[Python] __repr__"
categories: ["Python"]
tag: ["Python", "special methods"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
\_\_repr\_\_ 은 클래스에 쓸 수 있는 special method 중 하나이다. 기능은 객체를 사람이 이해할 수 있는 평문으로 표현해준다. 

클래스를 정의하고, 그 인스턴스를 print()로 출력하면 그 객체의 메모리 주소만 출력된다.

```python
class A:
    pass

a = A()
print(a)
```

```python
<__main__.A object at 0x000001C5741EE500>
```

이 출력결과는 분명 인간이 보기에는 이해할 수 없는 구문일 것이다. 이를 \_\_repr\_\_를 통해서 인간이 이해할 수 있는 평문이 출력되도록 해주는 것이다. 

```python
class A:
    pass

class B:
    def __repr__(self):
        return "This is from B class"

a = A()
print(a)

b = B()
print(b)
```

```python
<__main__.A object at 0x0000015E557FE320>
This is from B class
```

즉, 해당 클래스의 인스턴스가 print() 함수의 인자로 넘겨질 경우, 해당 인스턴스를 설명해주는 문자열을 출력해주는 기능을 \_\_repr\_\_가 제공해주는 것이다. 

\_\_str\_\_ 도 객체의 문자열 표현을 반환한다. print() 함수의 인자로 넘겨질 때 그렇게 해준다.

```python
class A:
    def __str__(self):
        return "hello, this is from __str__"
    

a = A()
print(a)
```

```python
hello, this is from __str__
```

그러나 \_\_str\_\_ 와 \_\_repr\_\_ 의 차이점은 \_\_str\_\_은 인간이 이해할 수 있는 문자열의 표현이 아닌 “문자열화” 그 자체에 있다. 즉, 다른 데이터와 호환되도록 하거나 다른 객체끼리 정보를 주고 받는 등의 목적을 위해 존재하는 것이고, \_\_repr\_\_은 “표현” 그 자체에 초점을 맞춰 객체를 인간이 이해하기 쉬운 문장으로 설명해주는 역할이다. 즉, “문자열화”라는 기능 자체는 같으나 “목적” 자체가 다르다는 게 차이점이다. 

---

Reference

[1] [[Python] \_\_str\_\_와 \_\_repr\_\_의 차이 살펴보기](https://shoark7.github.io/programming/python/difference-between-__repr__-vs-__str__)

[2] [[Python] 내장 함수 \_\_str\_\_, \_\_repr\_\_](https://recordnb.tistory.com/47)