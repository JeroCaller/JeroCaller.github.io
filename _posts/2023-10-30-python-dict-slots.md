---
layout: single
title:  "[Python] __dict__, __slots__"
categories: ["Python"]
tag: ["Python", "special methods"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# \_\_dict\_\_

\_\_dict\_\_ 은 모듈 또는 클래스 객체의 namespace에서의 변수명과 그 변수가 참조하는 값을 dictionary 형태로 담고 있는 special attribute이다. 모듈 또는 클래스 내 속성을 참조한다는 것은 곧 이 \_\_dict\_\_라는 dictionary 내에서 해당 속성을 key로 참조하는 것과 같다. 모듈을 예로 들면, study.py 라는 모듈에서 var라는 전역변수를 선언했다면, 다른 모듈에서 해당 모듈을 import 하여 해당 변수를 참조할 때 study.var라고 표현할 수 있는데, 이는 study.\_\_dict\_\_[’var’]와 동일하다. 코드 예제로 살펴보면 다음과 같다. 

```python
var_in_another_module = 'hi from study.py'
```

```python
import study

print(study.__dict__['var_in_another_module']) #1
print(study.var_in_another_module) #2

study.__dict__['another_var'] = 'hello' #3
print(study.__dict__['another_var']) #4
print(study.another_var) #5
```

```python
hi from study.py
hi from study.py
hello
hello
```

\_\_dict\_\_의 한 가지 특징은, 위 예제에서 study.py에 직접 변수를 추가하여 선언하지 않고, import 하고 있는 study2.py 모듈에서 `study.__dict__['another_var'] = 'hello'` 와 같이 새로 변수를 추가할 수 있다는 것이다. 즉, 특정 모듈을 import하고 있는 외부 모듈에서도 \_\_dict\_\_ 를 통해 변수를 추가 정의할 수 있다. 

클래스에서의 \_\_dict\_\_은 어떻게 보면 두 종류로 나눠서 볼 수도 있는데, 하나는 \_\_init\_\_ 이라는 special method 내에 정의된 인스턴스 속성들이 dictionary 형태로 저장된 special attrubite이고, 또 하나는 클래스 속성들이 저장된 dictionary이다. 

```python
class User:
    class_attribute = True

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def createOtherVar(self):
        self.my_id = 1

if __name__ == '__main__':
    me = User('hi', 20)
    me.nickname = 'hello'
    print(me.__dict__)
    print(User.__dict__)
```

```python
{'name': 'hi', 'age': 20, 'nickname': 'hello'}
{'__module__': '__main__', 'class_attribute': True, '__init__': <function User.__init__ at 0x000002C20A29ADD0>, 'createOtherVar': <function User.createOtherVar at 
0x000002C20A29AF80>, '__dict__': <attribute '__dict__' of 'User' objects>, '__weakref__': <attribute '__weakref__' of 'User' objects>, '__doc__': None}
```

위 예제 1-1과 그 출력결과를 보면 알 수 있듯, 클래스의 객체에서 \_\_dict\_\_를 호출하면 (예제 1-1에서 `me.__dict__` 처럼) 해당 객체의 \_\_init\_\_ 에 정의된 인스턴스 속성값들이 dictionary 형태의 값으로 반환된다는 것을 알 수 있다. 또한 앞서 모듈에서도 보았듯, 클래스에서의 \_\_dict\_\_ 에도 클래스 외부에서 변수를 추가할 수 있다(`me.nickname = 'hello'`). 참고로 해당 객체의 \_\_init\_\_에 정의되지 않은 인스턴스 속성은 \_\_dict\_\_에 반영되지 않음을 알 수 있다. (위 예제 1-1의 createOtherVar() 메서드 내 self.my_id는 출력결과에 나오지 않았다) 

위 예제의  `User.__dict__` 처럼 클래스명.\_\_dict\_\_ 을 호출하면 `me.__dict__` 와는 전혀 다른 속성들이 출력되는 것을 확인할 수 있다. 이 중에는 해당 클래스에 정의된 인스턴스 메서드명이 보이는 것으로 보아 인스턴스 변수보다는 인스턴스 메서드명과 그 메서드들의 메모리상에서의 위치를 주로 저장하는 것으로 보인다. 

```python
class User:
    class_attribute = True

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def createOtherVar(self):
        self.my_id = 1

if __name__ == '__main__':
    me = User('hi', 20)
    you = User('hello', 30)

    print(me.__dict__)
    print(you.__dict__)
```

```python
{'name': 'hi', 'age': 20}
{'name': 'hello', 'age': 30}
```

위 예제 1-2를 보면 같은 User라는 클래스에서 인스턴스화된 두 변수 me, you에 대해 각각 \_\_dict\_\_을 호출하니 변수명은 같으나 해당 변수들이 참조하는 값들이 다름을 알 수 있다. 위 예제 1-2를 통해 알 수 있는 것은, 어떤 클래스를 인스턴스화할 때마다 각 객체마다 별도의 인스턴스 namespace가 형성되고, 또 그만큼의 메모리를 차지한다는 것이다. 여기서 앞서 말했던 \_\_dict\_\_의 특징인 클래스 외부에서도 변수를 추가할 수 있다는 점은 어떻게 보면 그만큼 차지하는 메모리량도 증가한다는 뜻으로 볼 수 있다. 만약 클래스의 외부에서 해당 클래스에 변수를 추가 정의하는 것을 막고자 한다면 \_\_slots\_\_ 이라는 special attribute를 사용하면 된다.

# \_\_slots\_\_

해당 attribute는 클래스 내 변수들을 명시적으로 정의함과 동시에 \_\_dict\_\_의 생성을 막고, 클래스 외부에서 새로운 변수를 해당 클래스에 추가하는 것을 막는 역할을 한다. 

```python
class User:
    class_attribute = True

    __slots__ = ['name', 'age']

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def createOtherVar(self):
        self.my_id = 1

if __name__ == '__main__':
    me = User('hi', 20)
    you = User('hello', 30)

    me.nickname = 'good'
```

```python
AttributeError: 'User' object has no attribute 'nickname'
```

위 예제 2-1의 마지막 줄에 User 클래스의 인스턴스가 담긴 변수 me에 새로운 변수 nickname과 그 값을 추가하려고 시도하였으나 에러가 발생한 것을 볼 수 있다. 이렇게 \_\_slots\_\_를 사용하면 외부에서의 새 변수 추가를 방지할 수 있다. (setattr() 함수를 통해서도 새 변수를 추가할 수 없다) 

그리고 다음의 예제도 예외가 발생한다.

```python
class User:
    class_attribute = True

    __slots__ = ['name', 'age']

    def __init__(self, name, age):
        self.name = name
        self.age = age

    def createOtherVar(self):
        self.my_id = 1

if __name__ == '__main__':
    me = User('hi', 20)
    you = User('hello', 30)

    print(me.__dict__)  # 예외가 발생하는 지점.
```

```python
AttributeError: 'User' object has no attribute '__dict__'
```

즉, \_\_slots\_\_는 해당 객체의 \_\_dict\_\_ 라는 special attribute를 생성하지 못하게 막는 역할도 한다. 위에서 언급했던 여러 사실들과 종합해보면, \_\_slots\_\_는 \_\_dict\_\_의 생성을 막기 때문에 해당 객체에 새로운 변수를 추가하지 못한다고 볼 수 있겠다(사실 이 정보는 파이썬의 공식 문서 (References [3])에서 명확히 밝히고 있다. 즉, \_\_dict\_\_ 생성을 막음으로써 \_\_slots\_\_에 등록되지 않은 변수를 새로 할당할 수 없다고 나와있다). 

---

Reference

[1] [3. Data model](https://docs.python.org/3/reference/datamodel.html#modules)

[2] [3. Data model](https://docs.python.org/3/reference/datamodel.html#custom-classes)

[3] [3. Data model](https://docs.python.org/3/reference/datamodel.html#slots)