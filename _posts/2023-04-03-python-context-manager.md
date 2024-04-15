---
layout: single
title:  "[Python] Context manager - 리소스 관리하기"
categories: ["Python"]
tag: ["Python", "context manager"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 개요

어떠한 리소스가 앱 또는 스레드에 의해 할당되었을 때, 그 리소스가 다 사용되고 나서는 반드시 할당을 해제하여(영어로는 free, clean up이라고 하는데 적절한 번역이 안 떠올라 “할당을 해제”한단 표현을 썼다) 다른 앱이나 다른 스레드가 사용 가능한 상태로 만들어야 한다. 그런데 모종의 이유로 실행중인 앱이 갑자기 에러가 나서 종료되거나 다른 이유로 인해 정상적으로 앱을 종료할 수 없는 경우, 갑자기 앱이 꺼지면 할당된 리소스는 계속 특정 앱 또는 스레드에 할당된 (종속된) 상태로 유지하게 된다. 할당을 해제하라는 코드 구문까지 도달하지 못한 채로 강제 종료되었기 때문이다. context manager는 이러한 불상사를 막기 위해 할당된 자원이 어떤 상황에서도 반드시 할당 해제되도록 하는 역할을 한다. 

파이썬에서는 with 키워드를 이용하여 context manager를 사용할 수 있다. 예를 들면 다음과 같이 쓸 수 있겠다.

```python
with method_about_thread() as variable_name:
    수행할 코드
(with 문 밖으로 나가면 코드 수행을 위해 할당된 리소스들이 자동 해제된다)
```

파이썬 인터프리터가 with 구문을 만나면 context라는 것을 새로 만든다. 해당 context는 선택적으로 객체를 반환할 수 있다. 이 반환된 context manager 객체를 as 키워드 뒤의 변수 이름에 저장할 수 있다. as 뒤의 변수명은 일종의 “별명”이라고 볼 수 있겠다. (마치 import pandas as pd라고 하는 것처럼) 

이 “별명”은 with 구문 이후에도 접근할 수 있다. 

```python
with open('test.txt', 'w') as file_obj:
    pass

print(file_obj.closed)
```

```python
True
```

“별명”이 with 구문 이후에도 쓸 수 있다고 해서 할당된 리소스가 할당 해제되지 않는 건 아니다. 

이러한 context manager는 다중 스레드를 사용할 때 유용할 것이다. 만약 어떤 에러에 의해 lock이 풀리지 않으면 deadlock(교착 상태) 상태에 빠질 수 있다. 교착 상태에 빠지면 다른 스레드들은 해당 리소스가 잠금 해제될 때까지 작업도 못한 채 하염없이 기다리게 되는 문제점이 발생할 수 있다. 그러나 context manager는 with 키워드를 통해 그 안의 코드들을 모두 수행했을 때 자동으로 lock 상태를 해제하므로 이러한 문제점을 방지할 수 있다. 

## with 구문 안에서 에러가 나도 리소스를 자동 할당하는 원리

```python
with some_class() as aka
    # 수행할 코드
```

위 코드에서 some_class()를 호출하면 context manager 객체를 반환한다. 이 객체는 context management protocol를 실행한다. 이 프로토콜은 두 개의 special method로 구성된다. 

- \_\_enter\_\_() : runtime context에 들어가기 위해 with 구문을 통해 호출됨
- \_\_exit\_\_() : with 코드 블럭을 빠져나갈 때 호출됨.

만약 위와 같이 with 구문 뒤에 클래스를 놓으면, 파이썬에서는 암묵적으로 해당 클래스의 객체로부터 context manager라는 객체를 반환하도록 하고, 자동으로 context manager의  \_\_enter\_\_() 메서드를 호출한다. \_\_enter\_\_() 는 context를 세팅하고 경우에 따라 객체를 반환한다. 위 코드의 경우, \_\_enter\_\_()에서 객체를 반환한다면 이를 aka라는 변수에 저장한다. 여기서 헷갈리지 말아야 할 점은, some_class()의 객체를 저장하는 것이 아니고 \_\_enter\_\_()에서 반환된 객체를 저장한다는 것이다. 

with 코드 바깥이나 안에서 에러가 나면, 파이썬 인터프리터는 \_\_enter\_\_() 에서 반환된 객체 (aka에 저장된 객체)의 \_\_exit\_\_() 메서드를 호출하여 종료시킨다. 해당 메서드는 객체를 “종료”시키는 역할이다. 달리 말하면 할당된 리소스를 해제해준다. 즉, with 구문 안에서 에러가 나더라도 자동으로 \_\_exit\_\_() 메서드를 호출하여 할당된 리소스를 자동으로 해제한다는 것이다. 그래서 교착 상태의 걱정 없이 사용할 수 있는 것이다. 

- context manager : \_\_enter\_\_(), \_\_exit\_\_()라는 special method를 가지는 객체를 의미. with 구문은 그 아래 들여쓰기 된 코드를 설정해주는 \_\_enter\_\_() 메서드와 그 코드 구문에서 빠져나가기 위해 호출되는 \_\_exit\_\_() 메서드가 호출되도록 보장한다. 이 때 \_\_exit\_\_() 메서드는 with 구문 안에 쓰인 코드 중간에 예외가 발생해도 호출되어 할당된 리소스가 안전하게 할당 해제되도록 해준다.
- runtime context :  \_\_enter\_\_()의 호출로 세팅되고 \_\_exit\_\_()의 호출로 코드가 끝나게 해주는 환경을 말함. with 구문으로 생성된다.

---

Reference

[1] B.M. Harwani, “Qt5 Python GUI Programming Cookbook: Building responsive and powerful cross-platform applications with PyQt”, (Packt, 2018)

[2] [Python Context Managers](https://www.pythontutorial.net/advanced-python/python-context-managers/)

[3] [교착 상태](https://ko.wikipedia.org/wiki/교착_상태)

[4] https://www.phind.com/search?cache=82ad02ad-9648-47cd-82b9-40be5a2273dd

[5] [Context Managers and Python's with Statement – Real Python](https://realpython.com/python-with-statement/)

[6] [What is a "runtime context"?](https://stackoverflow.com/questions/19652662/what-is-a-runtime-context)