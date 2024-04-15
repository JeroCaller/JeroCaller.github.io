---
layout: single
title:  "[Python-basic] Namespace and scope"
categories: "Python"
tag: ["Python", "basic"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# Namespace and scope

# Namespace

x = ‘hello’ 처럼 변수에 객체를 할당할 때, x는 ‘hello’라는 객체를 참조하는 이름이 된다. 

namespace는 각 이름들이 참조하는 객체에 대한 정보와 함께 현재 정의된 이름들의 집합이다. namespace에서는 객체 이름은 key, 객체 자체는 value로 가지는 dictionary에 저장한다. → {객체 이름: 객체} 즉, 이름과 그 이름이 참조하는 객체를 묶어준다.

파이썬에서는 4 종류의 namespace가 존재한다.

1. Built-in
2. Global
3. Enclosing
4. Local

파이썬에서 프로그램 실행 시, 필요한 만큼의 namespace를 생성하고, 더 이상 필요없게되면 삭제한다. 

## Built-in namespace

built-in namespace에는 파이썬에 내장되어 있는 객체들의 이름들을 모두 포함한다. dict, str와 같은 것들이 그 예이다. 사용자가 정의하지 않고 파이썬에 원래부터 내장되어 있는 객체들의 이름들의 공간이라 보면 된다.

이 namespace에 있는 이름들은 파이썬이 실행되는 어느 순간에나 이용가능하다. 파이썬 인터프리터가 실행될 때 built-in namespace가 생성되며, 이 인터프리터가 종료할 때까지 존재한다. 

## Global namespace

global namespace는 메인 프로그램에서 정의된 어떤 이름이든지 포함한다. 메인 프로그램이 실행될 때 global namespace가 생성된다. 그리고 인터프리터가 종료할 때까지 남아있게 된다. 

global namespace는 또한 프로그램이 import 선언문을 통해 로드하는 모듈(module)들에 대해서도 생성된다. 

## Local and enclosing namespace

함수가 실행될 때마다 인터프리터는 새로운 namespace를 만든다. 이렇게 만들어진 namespace는 해당 함수에서 local이며 (즉 해당 함수만 포함한다), 그 함수가 종료될 때까지 존재한다. 

함수에 대해서는 두 종류의 namespace가 있다. 

```python
def f():
    print('f() 시작')

    def g():
        print('g() 시작')
        print('g() 끝')
        return

    
    g()

    print('f() 끝')
    return

f()
```

```python
f() 시작
g() 시작
g() 끝
f() 끝
```

위 예제에서 f() 함수는 g() 함수를 감싸므로 enclosing function (감싸는 함수)이다. 한 편 g() 함수는 enclosed function (감싸인 함수)이다. 

이 때 enclosed function에 대해 만들어지는 namespace를 local namespace, enclosing namespace에 대해 만들어지는 namespace를 enclosing namespace라 한다. 

각각의 namespace는 각각에 해당하는 함수가 종료될 때까지 존재한다. 함수 종료시 해당 namespace에서 객체들을 참조하던 모든 reference들은 무효화된다. 

# 변수의 scope (범위)

똑같은 이름의 여러 다른 instance들이 여러 namespace에 존재할 수도 있다. 이 때 각 instance들이 각자 다른 namespace에 존재한다면 그들은 이름이 똑같더라도 모두 다른 곳에 분류되어 있으며, 따라서 서로에게 간섭하지 않는다. 

그렇다면 예를 들어 이름이 x인 변수를 참조했다면, 그리고 이 x가 여러 namespace에서 존재한다면, 프로그램은 어떤 x를 참조해야할까?

이를 위해 scope라는 개념이 등장한다. 어떤 이름의 scope라 함은 그 이름이 포함된(즉, 유효한) 프로그램 내 영역이라 보면 된다. 인터프리터는 코드 실행 시 이름이 어디에서 정의되었는지, 그리고 코드 어디에서 그 이름이 참조되었는지를 근거로 그 이름의 scope를 결정한다. 

프로그램이 어떤 특정 이름이 어떤 scope에 있는지, 또 어떤 scope 내 이름을 먼저 참조해야할지는 다음의 순서로 결정한다.

1. local
    
    만약 함수 내에서 x를 참조했다면, 그리고 그 함수가 중첩된 함수 중 가장 안쪽에 있는 함수라면 인터프리터는 그 함수의 scope를 먼저 조사한다. 
    
2. enclosing
    
    만약 x가 local scope에 없다면 그 함수를 감싸는 외부 함수에 해당하는 enclosing 영역에서 해당 이름을 찾는다. 
    
3. global
    
    enclosing function에서도 없다면, 즉 함수 자체에 없다면 함수 외부 영역인 global 영역에서 찾는다.
    
4. built-in
    
    만약 x를 그 어디에서도 찾지 못했다면, 인터프리터는 built-in scope에서 찾는다. 즉, str, list 등 파이썬 자체에서 이미 내장된 객체들의 scope를 조사한다. 
    

위 규칙을 LEGB rule이라고도 한다. 즉, L → E → G → B 순으로 scope를 조사한다. 

![[2]](https://files.realpython.com/media/t.fd7bd78bbb47.png)

[2]

만약 위 4개의 scope에서도 해당 이름을 찾지 못한다면, NameError exception을 일으킨다. 

```python
x = 'global'

def outer():

    def inner():
        print(x)  #1

    inner()

outer()
```

```python
global
```

위 예제의 #1줄에 있는 print() 함수는 x를 참조해야 하는데, 참조 가능한 x는 오직 하나 뿐임을 알 수 있다. 즉, global namespace에 정의된 x라는 객체를 출력하게된다. 

```python
x = 'global'

def outer():
    x = 'enclosing'
    
    def inner():
        print(x)

    inner()

outer()
```

```python
enclosing
```

이번에는 x가 두 개라서 프로그램은 print()함수가 참조해야 할 하나의 x를 골라야만 한다. 이 때 LEGB 규칙에 따라 프로그램은 enclosing scope에 있는 x를 우선시하여 이 x를 참조하도록 한다. 

```python
x = 'global'

def outer():
    x = 'enclosing'

    def inner():
        x = 'local'
        print(x)

    inner()

outer()
```

```python
local
```

이제 프로그램은 세 영역을 조사해야 한다. local, enclosing, global이다. 이 때 LEGB 규칙에 따라 print() 함수는 local scope에 있는 x를 참조하게 된다. 

```python
def outer():

    def inner():
        print(x)

    inner()

outer()
```

```python
Exception has occurred: NameError
name 'x' is not defined
```

이번에는 프로그램이 그 어떤 namespace에서도 x를 찾지 못한다. x는 built-in scope에도 없으므로 결국 프로그램은 위 실행결과처럼 NameError 예외를 출력한다. 

# 파이썬 namespace dictionary

앞서 짤막하게 namespace는 key는 객체를 참조하는 이름, value는 그 객체 그 자체인 dictionary로 생각할 수 있다고 언급하였다. global, local namespace에 대해서 파이썬은 실제로 이들을 dictionary로 취급하여 실행시킨다.

(단, built-in namespace는 dictionary가 아닌 module로 실행된다)

파이썬에서는 global과 local namespace dictionary에 사용자가 접근할 수 있도록 내장 함수(built-in function)인 globals()와 locals()를 지원한다.

## globals() 함수

내장 함수인 globals() 함수는 현재 global namespace dictionary에 존재하는 reference들을 반환한다. 이를 통해 global namespace 내에 있는 객체에 접근할 수 있다. 

```python
print(type(globals()))
print(globals())
```

```python
<class 'dict'>
{'__name__': '__main__', '__doc__': None, '__package__': '', '__loader__': None, '__spec__': None, '__file__': 'c:\\python2\\python_basic\\study.py', '__cached__': None, ...}
```

위 예제의 실행결과는 파이썬 버전, 운영 체제 등에 따라 조금씩 다를 수 있다.

```python
x = 'hello'
print(globals())
```

```python
{'__name__': '__main__', '__doc__': None, '__package__': '', '__loader__': None, '__spec__': None, '__file__': 'c:\\python2\\python_basic\\study.py', '__cached__': None, ..., 'x': 'hello'}
```

변수 x를 선언하고 여기에 ‘hello’를 대입하였다. 그 후 globals() 를 실행시켜 출력해보았다. 실행결과 맨 마지막에 x라는 객체 이름과 ‘hello’라는 객체가 key-value 쌍으로 입력되었음을 알 수 있다. 

기존에는 어떤 객체를 참조하고 싶었다면 그 객체를 가리키는 이름을 참조하면 되었다. 예를 들어 x = ‘hello’ 에서 ‘hello’를 원한다면 코드에 x를 써 넣으면 되었다. 그런데 globals() 함수를 통해서 이러한 객체 참조를 간접적인 방법으로 행할 수 있다.

```python
some_var = 'good'
print(some_var)
print(globals()['some_var'])
print(some_var is globals()['some_var'])
```

```python
good
good
True
```

또한 global namespace에 있는 개체들을 새로 생성하거나 수정할 수도 있다.

```python
globals()['my_pi'] = 1000  # my_pi = 1000
print(globals())
globals()['my_pi'] = 3.141592
print(globals())
```

```python
{'__name__': '__main__', '__doc__': None, '__package__': '', '__loader__': None, '__spec__': None, '__file__': 'c:\\python2\\python_basic\\study.py', '__cached__': None, ..., 'my_pi': 1000}
{'__name__': '__main__', '__doc__': None, '__package__': '', '__loader__': None, '__spec__': None, '__file__': 'c:\\python2\\python_basic\\study.py', '__cached__': None, ..., 'my_pi': 3.141592}
```

위 예제들 모두 변수 할당에 따른 globals() 반환값의 변화를 통해 변수 할당 및 수정에 따른 global namespace 내의 변화를 알 수 있었다. 

## locals() 함수

locals() 함수는 globals()와 비슷하나, local namespace에 있는 객체에 접근할 수 있도록 한다. 

```python
def some_func(number, float):
    string = '하이'
    print(locals())

some_func(5, 0.2)
```

```python
{'number': 5, 'float': 0.2, 'string': '하이'}
```

위 예제에서처럼, locals() 함수가 어떤 함수 내에서 호출될 때 locals()는 해당 함수의 local namespace를 나타내는 dictionary를 반환한다. 이 때, local namespace에는 string 변수 뿐만 아니라 매개변수로 들어온 number, float 변수들도 해당 함수의 local namespace에 해당하고, 이를 위 실행결과를 통해서도 확인할 수 있다. 

함수 바깥에서 locals() 함수가 사용되면 이 함수는 globals() 처럼 행동한다.

```python
hello = 'nice to meet you'
print(locals())
```

```python
{'__name__': '__main__', '__doc__': None, '__package__': '', '__loader__': None, '__spec__': None, '__file__': 'c:\\python2\\python_basic\\study.py', '__cached__': None, ... , 'hello': 'nice to meet you'} 
```

### globals()와 locals()의 차이점

globals()는 global namespace를 포함하는 dictionary를 참조하는 reference를 반환한다. 그래서 만일 globals() 함수를 호출하여 그 값을 반환시킨 뒤, 그 후에 새로운 변수들을 정의하면, 새로 정의된 변수들도 해당 dictionary에 저장된다.

```python
global_reference = globals()
print(global_reference)

x = 'wow'
y = 120
print(global_reference)
```

```python
{'__name__': '__main__', '__doc__': None, ... , 'global_reference': {...}}
{'__name__': '__main__', '__doc__': None, ... , 'global_reference': {...}, 'x': 'wow', 'y': 120}
```

그래서 마치 x라는 변수에 처음에는 ‘hello’라고 할당하였다가 나중에 해당 변수에 ‘good’이라고 수정하면 이후 x 참조시 ‘good’이 출력되는 것처럼, globals()는 global namespace의 dictionary를 참조하는 일종의 ‘변수’ 또는 ‘이름’이라고 볼 수 있겠다. 

반면 locals() 함수는 local namespace의 현재 복사본을 dictionary로 반환한다. 즉 반환값이 reference가 아닌 dictionary 그 자체인 것이다. 

그래서 local namespace에 추가적인 객체 이름-객체가 들어오더라도 locals() 함수를 다시 호출하지 않는 이상 이 추가된 내용을 반영하지 못한다. 

또한 globals()와는 달리, locals() 함수를 이용하여 실제 local namespace에 있는 객체들을 수정하지 못한다. 

```python
def my_profile():
    my_nickname = '춤추는 라이언'
    local_variable = locals()
    print(local_variable)  #1

    my_age = 27  #2
    print(local_variable)  #3

    local_variable['my_nickname'] = '부끄러워하는 피치' #4
    print(my_nickname)  #5

    another_local_variable = locals()  #6
    print(another_local_variable) #7

my_profile()
```

```python
{'my_nickname': '춤추는 라이언'}  #1
{'my_nickname': '춤추는 라이언'}  #3
춤추는 라이언  #5
{'my_nickname': '춤추는 라이언', 'local_variable': {...}, 'my_age': 27}  #7
```

위 예제에서, my_profile() 함수 안에 맨 먼저 변수 하나를 정의하여 할당한 후, locals() 함수를 호출하여 그 반환값을 local_variable라는 변수에 할당하였다. 그리고 #2, #4처럼 새 변수를 정의하여 할당하거나, 기존에 있던 변수를 수정하였다. 그러면 locals() 함수를 통해 local namespace에도 변화가 있을 것으로 기대할 수 있지만 실행결과를 보면 locals() 함수는 이 기능을 하지 못하는 것을 알 수 있다. 그렇다고 해서 진짜로 local namespace에 변화가 없는 건 아니다. #6에서 다른 변수에 locals() 함수를 호출하여 반환값을 할당한 뒤 이를 출력했을 땐 변경사항이 반영되었음을 위 실행결과의 마지막 줄을 통해 알 수 있다. 이를 통해 locals() 함수를 다시 호출하지 않는 이상 local namespace의 변경사항을 즉시 확인할 수 없고, 이는 locals() 함수가 reference가 아닌 dictionary 그 자체임을 알 수 있는 부분이다.

다시 정리하자면, globals()는 global namespace의 dictioary를 참조하는 “reference”이고, locals()는 현재 local namespace의 복사본 dictionary이다. 

# Scope 밖에 있는 변수를 수정하는 방법

함수는 외부로부터 들어오는 인자들을 매개변수의 수정을 통해 해당 인자들을 수정할 수도 있고, 또 그러지 못하는 경우도 있다. 

immutable 인자에 대해 함수는 내부에서 해당 인자를 수정할 수 없다.

mutable 인자에 대해 함수는 내부에서 해당 인자를 재정의 (즉 새로운 값으로 다시 할당) 할 수는 없어도 수정은 가능하다. 

```python
num = 2  #1 
def some_func():
    num = 30  #2
    print(num)

some_func()
print(num)
```

```python
30
2
```

#1, #2에서 서로 다른 scope에서 num이라는 같은 이름에 다른 객체들이 할당되었다. 여기서 숫자 2, 30은 integer이고 immutable이므로 함수 내부에서 num에 할당된 값을 수정할 수 없다. 따라서 global scope에는 num = 2가, local scope에는 num = 30이라는 ‘동명이인’이 각자 서로 다른 동네에서 거주(?)하는 것이다. 

```python
some_list = ['국어', '영어', '수학']
def some_func():
    some_list[1] = '일본어'

some_func()
print(some_list)
```

```python
['국어', '일본어', '수학']
```

위 예제의 경우, some_list에는 mutable인 list가 할당되어 있기 때문에, 함수에서도 해당 list 내부에 있는 요소들을 변경시킬 수 있다. 

그러나 함수도 해당 list를 아예 새로운 값으로 할당할 수는 없다.  함수 내에서 같은 이름으로 새 값으로 할당할 경우, 예제 10처럼 동명이인이 서로 다른 동네에 거주하게 되는 셈이 된다.

```python
some_list = ['국어', '영어', '수학']
def some_func():
    some_list = ['사회', '과학']

some_func()
print(some_list)
```

```python
['국어', '영어', '수학']
```

## global 선언

그렇다면 함수 내에서 global scope에 있는 값을 수정하고 싶다면 어떻게 해야 할까? 이는 함수 내에서 global이라는 선언을 통해 가능하다. 

```python
num = 2 
def some_func():
    global num  #1
    num = 30 
    print(num)

some_func()
print(num)
```

```python
30
30
```

위 예제는 예제10에서 #1에 해당하는 코드 한 줄만을 추가했을 뿐이다. 그러나 그 결과는 완전 달라진다. ‘global 변수’ 선언을 통해 함수는 global namespace에 있던 num이라는 변수를 그대로 참조하게 된다. 즉 이제는 ‘동명이인’인 서로 다른 두 사람이 아닌 같은 한 사람을 지칭하게 되는 것이다. 그래서 함수 내에서 새로운 변수가 local namespace에 생성되는 것이 아니다. 대신 global namespace에 있던 기존의 변수를 수정하게 된다. 

global 선언을 이용하면 global 선언이 참조하는 변수가 global namespace에 존재하지 않아도 global 선언 + 변수 할당을 통해 해당 변수를 만들 수도 있게 된다.

```python
def some_func():
    global num
    num = 30 
    print(num)

some_func()
print(num)
```

```python
30
30
```

예제13에서, 분명 global scope에서 num이라는 변수가 정의되지도, 할당되지도 않았다. 그러면 이 num이라는 변수를 호출하면 에러가 생길 것으로 예상할 수 있다. 그러나 some_func 함수 내에서 global 선언을 하고 30을 할당한 후 해당 함수를 호출하고 num을 호출하면 정상적으로 실행됨을 확인할 수 있다. 

global 선언 뒤에는 둘 이상의 변수를 콤마를 통해 동시에 쓸 수 있다.

```python
a, b, c = 1, 2, 3
def some_func():
    global a, b, c
    a, b, c = 4, 5, 6

some_func()
print(a, b, c)
```

```python
4 5 6
```

global 선언 사용 시 주의해야 할 점은, global 선언을 통해 global scope에 있던 변수를 함수 내부로 들여오기도 전에 해당 변수를 함수 내부에서 호출하면 에러를 발생시킨다는 것이다.

```python
a = 10
def some_func():
    print(a)
    global a

some_func()
```

```python
SyntaxError: name 'a' is used prior to global declaration
```

따라서 해당 변수를 호출하고 사용하기 전에 반드시 global 선언을 먼저 하자. (함수 시작 부분부터 global 선언을 해두면 좋을 것 같다)

## nonlocal 선언

중첩 함수에서도 이와 비슷한 상황을 겪을 수 있다. 내부 함수가 외부 함수 내에 있는 객체를 수정하고자 한다면 nonlocal 선언을 이용하면 된다. 사용법은 global 선언과 동일하다. 

```python
def outer_func():
    x = 30

    def inner_func():
        nonlocal x
        x = 40

    inner_func()
    print(x)

outer_func()
```

```python
40
```

nonlocal 선언을 하면 local scope에서 이름만 같은 새로운 변수를 만드는 게 아니라, enclosing scope에 있던 변수를 참조하게 되는 것이다. 

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] Namespaces and Scope in Python

[https://realpython.com/python-namespaces-scope/](https://realpython.com/python-namespaces-scope/)