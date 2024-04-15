---
layout: single
title:  "[Python] 객체와 클래스 (Object and Class)"
categories: "Python"
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

파이썬을 다루면서 보게되는 모든 숫자, 문자열, 모듈 등등 모든 것이 객체이다. 

객체에는 데이터 (변수, attribute(속성)이라고 불린다. data member 또는 member라고도 불린다)와 코드 (함수, method(메서드)라고 한다. member function이란 단어를 쓰기도 한다) 둘 모두를 내포한다. 객체는 무언가를 지칭하는 ‘명사’에 비유를 든다면, 그 객체들이 서로 상호작용할 수 있도록 돕도록 ‘동사’ 역할을 하는 것이 메서드라 볼 수 있다. 

객체는 사용자가 직접 만들거나 기존에 있는 객체를 수정할 수 있다. 이를 위해서는 class에 대해 알아야 한다. 

클래스는 일종의 객체를 만들어내는 제작틀 같은 거라고 이해하면 된다. 와플기계라는 클래스로부터 와플이라는 객체를 만들어내는 것처럼 말이다. 

# class 키워드로 클래스 정의하기

자신만의 객체를 만들고자 한다면 class 키워드를 통해 클래스를 정의해야한다. 

```python
class 클래스명():
		실행할 코드
```

```python
class Person():
    pass

someone_profile = Person()
print(someone_profile)
print(type(someone_profile))
```

```python
<__main__.Person object at 0x0000018A4FB39AE0>
<class '__main__.Person'>
```

예제1-1에서는 아무런 기능을 수행하지 않는 클래스인 Person을 맨 처음에 정의하였다. 이후 이 클래스로부터 객체를 생성하기 위해 Person()으로 해당 클래스 이름을 호출하고 있다. 호출된 클래스로부터 객체가 형성되고 이 독립적인 객체가 변수(someone_profile)에 할당되었다. 위 예제의 실행결과는 해당 객체를 할당받은 변수 및 그 변수의 자료형을 출력한 모습이다. 

클래스에는 초기화 메서드인 __init__이 있다. 이를 이용한 모습은 다음과 같다.

```python
class Person():
    def __init__(self):
        pass
```

\_\_init\_\_() 은 클래스 정의로부터 객체를 초기화시켜주는 메서드 이름이다. self 매개변수는 객체 자기 자신을 참조하는 매개변수이다. 클래스 정의에서 \_\_init\_\_()이 정의될 때 맨 처음 매개변수는 반드시 self가 들어가야 한다. (하지만 호출 시 self자리에 인자를 넘기지 않아도 된다)

이번에는 위 코드에 살을 붙여보겠다.

```python
class Person():
    def __init__(self, name):
        self.name = name

someone_profile = Person('나이썬')  #1
print('당신의 이름은?', someone_profile.name) #2
```

```python
당신의 이름은? 나이썬
```

위 예제 1-3에서 초기화 메서드에 두번째 매개변수로 name을 추가하였고, 이 매개변수를 self.name라는 속성으로 할당하도록 정의하였다. 이렇게 정의된 클래스는 name 매개변수에 인자를 대입함으로써 Person 클래스부터의 객체를 만들 수 있다. 이를 #1에서 수행하고 있다. #1 코드에선 다음의 일들이 일어난다.

1. 인터프리터는 Person 클래스가 정의된 부분을 찾아본다.
2. 존재한다면 새 객체를 메모리에 인스턴스화(생성한단 뜻)한다. 
3. 해당 객체의 \_\_init\_\_ 메서드를 호출하는데, 이 때 이 객체 자신인 self와 ‘나이썬’이라는 다른 인자를 해당 메서드에 대입한다. 
4. name 값이 객체 내에 self.name 안에 저장된다. 
5. 이렇게 만들어진 새 객체가 반환된다.
6. 이 객체를 someone_profile이란 변수에 할당된다. 

사용자 정의 클래스를 통해 생성된 객체는 다른 객체들과 마찬가지로 행동한다. 리스트, 튜플 등의 요소로 대입할 수 있고, 함수의 인자로 대입하거나 반환값이 될 수도 있다. 

해당 객체에 저장된 속성 (name)은 #2처럼 객체.속성 처럼 사용하여 호출할 수 있다. 

참고로 __init__ 메서드는 클래스를 정의할 때마다 반드시 들어가야 하는 건 아니다. 

# 상속 (inheritance)

코딩을 할 때 기존에 있는 클래스를 내 필요에 맞게 변형시켜 쓸 수도 있을 것이다. 이 때 클래스 안의 코드를 일일이 수정한다면 어쩌면 코드를 잘못 건드려서 잘 작동하던 것이 작동하지 않을 수도 있을 것이다. 그렇다고 이 클래스를 복사하여 붙여넣고 고친다면 일단 거의 똑같은 기능을 수행하는 코드가 두 배로 늘어난 것이라 비효율적이기도 하고, 만약 복사한 클래스에 변경점이 생겼는데 이를 원본 클래스에도 똑같이 업데이트해야 하는 상황이 발생한다면 복잡하고 긴 코드 속에서 일일이 그걸 찾아 업데이트 해야 하는 수고로움이 있을 수도 있다. 즉, 코드 관리 측면에서도 그리 좋은 방법은 아니다.  

이를 해결하기 위해 클래스에선 상속(inheritance)을 지원한다. 기존에 있던 클래스로부터 ‘상속’ 받아 새로운 클래스를 정의하고 내가 원하는 점을 추가 및 수정할 수 있다. 이를 통해 일일이 기존 클래스 코드를 복사하여 쓰지 않아도 기존 클래스의 기능을 온전히 사용할 수 있는 것이다. 

새로운 클래스를 정의하여 기존 클래스로부터 상속받는 이 새 클래스를 자식 클래스 (child class), 하위 클래스(subclass), 또는 파생 클래스(derived class)라고 불리고, 기존 클래스를 부모 클래스(parent class), 상위 클래스 (superclass), 또는 기반 클래스(base class)라고 부른다. 

사용법은 다음과 같다.

```python
class 자식클래스명(상속할 클래스명):
		코드
```

소괄호 안에 상위 클래스 이름을 인자로 넘기기만 하면 된다. 다음 예제는 상속의 간단한 예이다.

```python
class Person():
    def __init__(self, name, job):
        self.name = name
        self.job = job
        

class Police(Person):
    pass

citizen = Person('나이썬', '시민')
police = Police('김경찰', '경찰')

print('제 이름은 {}이고 직업은 {}입니다.'.format(citizen.name, citizen.job))
print('제 이름은 {}이고 직업은 {}입니다.'.format(police.name, police.job))
```

```python
제 이름은 나이썬이고 직업은 시민입니다.
제 이름은 김경찰이고 직업은 경찰입니다.
```

위 예제에서 Police라는 클래스가 Person 클래스를 상속받고 있다. 즉 여기서 Police는 하위 클래스이고 Person 클래스가 상위 클래스가 되는 것이다. 언뜻 보면 Police 클래스 내부에는 pass 키워드밖에 없어서 아무런 기능을 수행하지 않는 것처럼 보이지만, 마지막 줄과 실행결과를 보면 알 수 있듯 Person 클래스로부터 상속받을 때 Person 클래스의 기능들을 그대로 받은 것을 알 수 있다. 

# 메서드 오버라이딩하기 (method overriding)

앞서 상속에 대해 살펴보았고, 그에 대한 예제도 보았다. 그런데 기존 클래스로부터 상속받고 코드를 수정하거나 추가하지 않고 그대로 두고 쓴다면 사실상 상속의 의미가 없을 것이다. 

이번에는 상속받은 클래스 코드를 수정하는 예제를 보겠다. 참고로 상위 클래스에 있던 메서드를 메서드 이름은 그대로 둔 채 내부 코드를 다시 짜는 것을 메서드 오버라이딩 (overriding, 덮어쓰기)이라고 한다. 이를 이용하면 같은 메서드 이름이라도 상위 클래스의 메서드가 아닌 하위 클래스의 메서드가 쓰이게 된다. 

```python
class Person():
    def __init__(self, name, job):
        self.name = name
        self.job = job

    def identify_yourself(self):
        print("=" * 10)
        print('제 이름은 {}이고 직업은 {}입니다.'.format(self.name, self.job))
        print("제 역할은 마피아를 찾아 투표로 추방하는 것입니다.")
        print('=' * 10)
        

class Police(Person):
    def identify_yourself(self):
        print("=" * 10)
        print('제 이름은 {}이고 직업은 {}입니다.'.format(self.name, self.job))
        print("제 역할은 마피아로 의심되는 사람을 조사하고 밝혀내는 것입니다.")
        print('=' * 10)

citizen = Person('나이썬', '시민')
police = Police('김경찰', '경찰')

citizen.identify_yourself()
police.identify_yourself()
```

```python
==========
제 이름은 나이썬이고 직업은 시민입니다.
제 역할은 마피아를 찾아 투표로 추방하는 것입니다.
==========
==========
제 이름은 김경찰이고 직업은 경찰입니다.
제 역할은 마피아로 의심되는 사람을 조사하고 밝혀내는 것입니다.
==========
```

위 예제에서는 Police 클래스의 identify_yourself 메서드 내부의 코드 일부를 바꿈으로써 메서드를 오버라이드 하였다. 실행결과에서도 Person 클래스 객체인 citizen의 출력 결과와 Police 클래스 객체인 police 출력 결과가 일부 다름을 알 수 있다. 

# 메서드 추가하기

또한 하위 클래스에서는 상위 클래스에는 없던 메서드를 추가하여 사용할 수도 있다. 그저 새로운 메서드를 정의하고 원하는 코드를 쓰면 된다. 

다음 예제에서는 하위 클래스인 Police 클래스에 새로운 메서드를 추가하였다.

```python
class Person():
    def __init__(self, name, job):
        self.name = name
        self.job = job

    def identify_yourself(self):
        print("=" * 10)
        print('제 이름은 {}이고 직업은 {}입니다.'.format(self.name, self.job))
        print("제 역할은 마피아를 찾아 투표로 추방하는 것입니다.")
        print('=' * 10)
        

class Police(Person):
    def identify_yourself(self):
        print("=" * 10)
        print('제 이름은 {}이고 직업은 {}입니다.'.format(self.name, self.job))
        print("제 역할은 마피아로 의심되는 사람을 조사하고 밝혀내는 것입니다.")
        print('=' * 10)

    def use_skill(self):
        print("{}은 자신의 직업인 {}을 이용하여 {} 스킬을 시전하였습니다.".format(self.name, self.job, '조사하기'))

citizen = Person('나이썬', '시민')
police = Police('김경찰', '경찰')

police.use_skill()
```

```python
김경찰은 자신의 직업인 경찰을 이용하여 조사하기 스킬을 시전하였습니다.
```

위 예제에서 만약 상위 클래스인 Person 클래스의 객체인 citizen이 use_skill 메서드를 사용하려고 한다면 어떨까? 위 예제에서 다음의 코드를 추가하여 실행해보았다.

```python
citizen.use_skill()
```

```python
Exception has occurred: AttributeError
'Person' object has no attribute 'use_skill'
```

추가된 메서드는 하위 클래스에만 존재하기에 상위 클래스에선 해당 메서드를 사용하지 못하는 것을 알 수 있다. 

# super()로 상위 클래스의 메서드 호출하기

하위 클래스 내부에서 상위 클래스를 호출하고자 한다면 super() 를 이용하면 된다. 

다음은 예제 4-1를 조금 수정한 모습이다.

```python
class Person():
    def __init__(self, name, job):
        self.name = name
        self.job = job

    def identify_yourself(self):
        print("=" * 10)
        print('제 이름은 {}이고 직업은 {}입니다.'.format(self.name, self.job))
        print("제 역할은 마피아를 찾아 투표로 추방하는 것입니다.")
        print('=' * 10)
        

class Police(Person):
    def __init__(self, name, job, skill):
        super().__init__(name, job)    #1
        self.skill = skill    #2

    def identify_yourself(self):
        print("=" * 10)
        print('제 이름은 {}이고 직업은 {}입니다.'.format(self.name, self.job))
        print("제 역할은 마피아로 의심되는 사람을 조사하고 밝혀내는 것입니다.")
        print('=' * 10)

    def use_skill(self):
        print("{}은 자신의 직업인 {}을 이용하여 {} 스킬을 시전하였습니다.".format(self.name, self.job, self.skill))

citizen = Person('나이썬', '시민')
police = Police('김경찰', '경찰', '조사하기')

police.identify_yourself()  #3
police.use_skill()  #4
```

```python
==========
제 이름은 김경찰이고 직업은 경찰입니다.
제 역할은 마피아로 의심되는 사람을 조사하고 밝혀내는 것입니다.
==========
김경찰은 자신의 직업인 경찰을 이용하여 조사하기 스킬을 시전하였습니다.
```

예제5-1의 바뀐 코드는 #1 ~ #4로 표시해두었다. #1에서 super()를 이용하고 있는데, super().\_\_init\_\_(name, job) 라는 코드를 통해 Person 클래스로부터의 \_\_init\_\_() 메서드를 호출한 것이다. 이 코드에서 일어나는 세부적인 사항은 다음과 같다.

1. super() 는 부모 클래스인 Person으로부터 그 정의를 읽어온다.
2. \_\_init\_\_() 메서드는 Person.\_\_init\_\_() 메서드를 호출한다. self 인자는 자동으로 넘긴 것이라 간주하기에 다른 추가적인 인자만 넣으면 된다. 위 예제에서는 name과 job을 인자로 넘기고 있다. 
3. self.skill = skil 라는 새 코드를 넣음으로써 Police와 Person이 서로 구분되는 점이 있음을 암시한다. 

#1 코드로도 잘 실행됨을 위 실행결과를 통해 알 수 있다. 

위 예제에서도 알 수 있듯, super() 는 같은 코드를 일일이 치지 않고도 부모 클래스의 코드를 그대로 사용할 수 있다는 점에서 장점이 있다. 또한, 부모 클래스에 무언가 수정되거나 추가된다면 이 변경사항이 하위 클래스에도 자동으로 업데이트 되기에 부모 클래스의 변경 사항을 일일이 자식 클래스에 타이핑하여 쓰지 않아도 된다는 장점도 있다. 

자식이 자기가 할 일은 하지만, 여전히 부모의 손길이 필요할 때 super()를 사용하자. 

# self에 대해…

```python
police = Police('김경찰', '경찰', '조사하기')
police.identify_yourself()
```

이전 예제를 보면 분명 police 클래스의 identify_yourself() 메서드에는 self라는 매개변수가 존재했다. 그러나 위 예제 6-1에서는 아무런 인자를 전달하지 않는 것처럼 보인다. 그러나 사실 위 예제의 코드 실행 시 다음의 일들이 일어나고 있다.

1. 인터프리터는 police라는 객체의 클래스 Police를 찾는다.
2. 객체 police 자체를 Police 클래스의 identify_yourself() 메서드에 인자로 넘긴다. (self라는 키워드 자체가 해당 클래스로 인스턴스화된 객체 그 자체를 참조하는 키워드임을 기억하자)

그래서 사실 다음의 코드도 가능하긴 하다.

```python
police = Police('김경찰', '경찰', '조사하기')
#police.identify_yourself()
Police.identify_yourself(police)
```

위 예제의 마지막 줄이 police 변수가 아닌 Police 클래스로 시작함을 헷갈리지 말자. 

위 코드도 가능하지만 보통은 예제 6-1처럼 클래스로부터 생성된 객체를 변수에 할당하고 그 변수를 직접 이용하여 속성 또는 메서드를 호출하는 게 일반적이다. 

# get, set 속성과 property

객체지향 언어 (object-oriented language)에는 외부로부터의 직접 접근을 막기 위해 private 객체 속성을 지원한다. 그래서 private 속성의 값을 읽거나 쓰기 위해선 getter와 setter 메서드를 써야 한다. 

파이썬에서는 모든 메서드와 속성이 public이라서 기본적으로는 getter와 setter가 필요치 않다. 그러나 속성으로의 직접 접근을 원치 않는다면 해당 메서드들을 쓸 수 있다. 이 때 property()를 이용할 수 있다. 

예를 들어 홈페이지에 가입된 사람들의 명단을 위해 Person이라는 객체를 만들고 그 안에 사람의 이름을 담을 속성을 정의했다고 가정해보자. 그러면 보안을 위해 이 이름이라는 속성을 외부에서 직접 접근하지 못하도록 해야 할 것이다. 이를 위해 다음과 같이 코드를 짠다.

```python
class Person():
    def __init__(self, user_name):
        self.private_name = user_name
    
    def get_user_name(self):  #1
        print('getter 호출됨')
        return self.private_name

    def set_user_name(self, new_name):  #2
        print('setter 호출됨')
        self.private_name = new_name
    
    name = property(get_user_name, set_user_name)  #3

user_1 = Person('David')
user_1.name = 'john'  #4
print(user_1.name)  #5
```

```python
setter 호출됨
getter 호출됨
john
```

위 예제의 #1, #2 부분이 각각 getter와 setter이다. #1에서는 외부에서 객체의 이름을 원한다면 이 메서드를 통해 해당 속성을 반환하는 기능을 한다. #2에서는 외부에서 해당 객체의 이름 속성을 바꾸고자 할 때 이 메서드에 새 이름을 대입하여 바꾸도록 하고 있다. 이를 통해 해당 속성에 직접 접근하지 못하도록 막고 메서드를 통해 간접적으로 실행시키고 있는 것이다. 

그 후, #3에서, self.private_name을 대체할 name 변수를 정의하고 여기에 property() 함수를 적용하였다. property() 의 첫 인자로는 getter를, 두 번째 인자로 setter 메서드를 대입하면 된다. 그러면 self.private_name이라는 속성을 대체하여 name 변수를 호출하여 해당 속성의 값을 받고 (#5) 또는 name 변수에 새 이름을 할당 (#4)할 수 있는 것이다. #4, #5를 보면 알겠지만 마치 name이 Person 클래스의 진짜 속성인 것처럼 값을 반환할 수 있고 동시에 새 값도 할당할 수 있는 것이다. 

이 상태에서도 직접 해당 속성을, 또는 getter, setter 메서드를 호출할 수는 있다. 

property를 이용하는 또 다른 방법은 데코레이터를 이용하는 것이다. 이번에는 name을 메서드명으로 한다. 이 메서드에 다음의 데코레이터를 적용하는 것이다.

- @property : getter 메서드 바로 앞에 쓴다.
- @name.setter : setter 메서드 앞에 쓴다.

```python
class NewPerson():
    def __init__(self, user_name):
        self.private_name = user_name

    @property
    def name(self):
        print('getter 호출됨')
        return self.private_name

    @name.setter
    def name(self, new_name):
        print('setter 호출됨')
        self.private_name = new_name

user_1 = NewPerson('siera')
print(user_1.name)
```

```python
getter 호출됨
siera
```

name에 새로운 값 할당도 해보자.

```python
user_1.name = 'tom'
print(user_1.name)
```

```python
setter 호출됨
getter 호출됨
tom
```

# 클래스 내 속성에 대한 외부로부터의 직접적 접근 방지

사실 이전 예제에서는 getter, setter 개념을 적용하였지만 그럼에도 외부에서 직접 속성에 접근이 가능했다. 이번에는 이를 방지해보자. 

방법은 간단하다. 직접 접근을 방지하고자 하는 속성 이름 앞에 언더바 2개 (__)를 추가하면 된다. 이를 이전 예제에 적용하면 다음과 같다.

```python
class NewPerson():
    def __init__(self, user_name):
        self.__private_name = user_name

    @property
    def name(self):
        print('getter 호출됨')
        return self.__private_name

    @name.setter
    def name(self, new_name):
        print('setter 호출됨')
        self.__private_name = new_name

user_1 = NewPerson('siera')
print(user_1.name)

user_1.name = 'tom'
print(user_1.name)
```

```python
getter 호출됨
siera
setter 호출됨
getter 호출됨
tom
```

이전 예제와 똑같으나, 바뀐 점은 클래스 내 속성인 private_name 앞에 언더바 2개가 붙었다는 것이다. 이제 외부에서 이 속성을 호출하려고 해보자.

```python
print(user_1.__private_name)
```

```python
Exception has occurred: AttributeError
'NewPerson' object has no attribute '__private_name'
```

보다싶이 해당 속성이 존재하지 않는다며 에러를 일으킨 것을 볼 수 있다. 해당 속성이 외부로부터 직접 접근할 수 없음을 보여준다.

그런데 사실 외부로부터의 직접 접근이 아예 불가능한 건 아니다. 

객체._클래스명__속성  ⇒ 이렇게 하면 해당 속성에 접근할 수 있다.

```python
print(user_1._NewPerson__private_name)
```

```python
siera
```

그래서 비록 해당 방법은 완전히 외부 접근을 차단시킬 순 없지만, 실수로 또는 누군가가 고의적으로 해당 속성에 접근하는 것을 어느 정도는 막을 수 있다. 

# 클래스로 데코레이터 만들기

이전에 [함수](/python/function/) 장에서 함수로 데코레이터를 만들고 다른 함수에 적용하는 방법에 대해 언급했었다. 그런데 데코레이터는 함수 뿐만 아니라 클래스로도 만들 수 있다. 

## 입출력 없는 함수에 클래스 데코레이터 적용하기

인자를 받지도, 그리고 return 키워드를 통해 아무것도 반환하지도 않는 함수에 대해 클래스 데코레이터를 적용하는 예제를 보자.

```python
class DecorClass():
    def __init__(self, func):   #1
        self.func = func         #2

    def __call__(self):        #3
        print(self.func.__name__, '함수 시작')  
        self.func()           #4
        print(self.func.__name__, '함수 끝') 

    
@DecorClass   #5
def print_something():  #6
    print('안녕하세요')

print_something()  #7
```

```python
print_something 함수 시작
안녕하세요
print_something 함수 끝
```

위 예제에서는 DecorClass 클래스가 클래스 데코레이터가 된다. 이러한 클래스 데코레이터를 만들기 위해서 먼저 #1에서처럼 초기화시 호출할 함수의 이름을 func이라는 매개변수로 받은 후 이를 #2에서처럼 속성으로 저장한다. 

그 후, 인스턴스를 함수처럼 호출하게 해주는 \_\_call\_\_ 메서드를 #3에서 구현해야 한다. 이를 통해 해당 클래스를 호출하면 자동으로 \_\_init\_\_ 메서드가 실행되듯이, 다른 함수에 해당 클래스를 데코레이터로 적용시키면 자동으로 \_\_call\_\_ 메서드가 실행되는 것이다. 위 예제에서는 단순히 해당 함수의 이름 및 시작과 끝부분을 알려주는 역할의 코드를 썼다. 

그러면 이제 클래스 데코레이터가 만들어진 것이다. 이 데코레이터를 적용시킬 함수 (#6) 바로 위에 @클래스_데코레이터명 을 써주면 해당 함수에 데코레이터가 적용된다. 이를 위의 실행결과에서도 확인할 수 있다. 

## 입출력이 존재하는 함수에 클래스 데코레이터 적용하기

이번에는 매개변수로 들어오는 인자 및 반환값을 처리하는 클래스 데코레이터에 대해 살펴보자. 

```python
class DecorClass():
    def __init__(self, func):   #1 호출할 함수를 인스턴스의 초기값으로 지정
        self.func = func  #2 호출할 함수명을 속성 func에 저장.

    def __call__(self, *args, **kwargs):  #3 *args, **kwargs를 통해 호출될 함수 func의 인자를 받음
        result = self.func(*args, **kwargs)
        print("===============")
        print('사용된 함수명: {}'.format(self.func.__name__))
        print('입력된 args: {}'.format(args))
        print('입력된 kwargs: {}'.format(kwargs))
        print('함수 실행 결과: {}'.format(result))
        print("===============")
        return result  #4 self.func의 반환값 반환

    
@DecorClass
def add(a, b):
    return a + b

print(add(2, 3))
print(add(a=30, b=2))
```

```python
===============
사용된 함수명: add
입력된 args: (2, 3)
입력된 kwargs: {}
함수 실행 결과: 5
===============
5
===============
사용된 함수명: add
입력된 args: ()
입력된 kwargs: {'a': 30, 'b': 2}
함수 실행 결과: 32
===============
32
```

위 예제에서 #1의 \_\_init_\_() 메서드로 데코레이터를 지정할 함수 이름을 func라는 매개변수로 받는다. 그 후 이 매개변수를 #2에서처럼 self.func라는 속성에 저장한다. 여기까지는 이전과 동일하다. 이제 \_\_call\_\_ 메서드에서는 데코레이터가 적용될 함수의 매개변수를 지정하고, 이 매개변수를 self.func의 매개변수로 지정한다. 그러면 아래의 add(a, b)함수에서 a, b가 문제없이 함수에 인자를 전달할 수 있다. 
#4에서는 데코레이터가 지정될 함수의 반환값을 반환해주고 있다. 그 이후로는 이전과 똑같이 데코레이터로 지정될 함수 정의 바로 윗부분에 @ 데코레이터를 지정해준다. 

## 매개변수를 받는 클래스 데코레이터 만들기

클래스 데코레이터 자체에 인자를 받을 수 있도록 할 수도 있다. 

다음 예제는 덧셈 결과를 반환하는 add 함수의 결과값과 데코레이터의 매개변수로 넘겨지는 숫자 간 최대 공약수를 구하는 클래스 데코레이터 예제이다.

```python
from math import gcd

class GCDChecker():
    def __init__(self, x):  #1 데코레이터의 인자를 받을 매개변수 지정
        self.x = x  #2

    def __call__(self, func):  #3 호출할 함수를 매개변수로 지정
        def wrapper(a, b):  #4 호출할 함수의 매개변수와 똑같이 지정
            result = func(a, b)
            gcd_result = gcd(result, self.x)
            print('결과값 {}와 {}의 최대공약수는 {}입니다.'.format(result, self.x ,gcd_result))
            return result
        return wrapper

@GCDChecker(50)  #5
def add(a, b):
    return a + b

print(add(13, 7))
```

```python
결과값 20와 50의 최대공약수는 10입니다.
20
```

이전의 클래스 데코레이터 예제와 달리 이번 예제에서는 #5에서 데코레이터에 인자를 넘기는 것을 알 수 있다. 이렇게 클래스 데코레이터 자체가 어떤 인자를 받는 경우의 클래스 데코레이터 내부 코드 구조는 약간 달라진다. 

먼저 #1에서 데코레이터의 인자를 받을 매개변수를 지정한다. #5에서 50이란 인자를 받는 매개변수이다. 이전에는 데코레이터로 지정될 함수 이름을 넘겨받았다는 것과 차이가 있다. 그 후 #2에서처럼 해당 인자를 속성으로 저장한다. 

#3에서 \_\_call\_\_ 메서드를 정의하는 것은 똑같으나, 이번에는 매개변수로 데코레이터로 지정될 함수를 받는다는 것을 알 수 있다. 그리고 내부 코드에는 #4처럼  wrapper라는 내부 함수를 정의하고, 데코레이터로 지정될 함수의 매개변수와 똑같은 수의 매개변수를 지정한다. 그 후, result = func(a, b)를 통해 해당 함수가 실행되고 그 결과값을 받는다. 이 결과값과 self.x 이 두 수의 최대공약수를 출력하는 코드를 적었다. 그 후, 실행될 함수 (add)와 똑같이 return 키워드를 통해 덧셈 결과값을 반환하면서 wrapper 함수의 정의는 끝난다. 그 다음 이 wrapper 함수 자체를 반환한다. 코드 구조가 클로져와 똑같음을 알 수 있다. 

이 후 #5에서처럼 임의의 숫자 인자를 데코레이터의 인자로 넘기는 코드를 함수 add 정의부분의 바로 앞에 지정하면 해당 함수에 데코레이터가 지정된다. 실행결과를 보면 덧셈 결과와 함께, 임의로 대입한 숫자 50과의 최대공약수까지 구해주는 것을 확인할 수 있다. 

# 메서드 종류

사실 클래스 내에 정의되는 메서드에도 종류가 있다. 세 가지 종류가 있다.

1. 인스턴스 메서드 (instance method). self를 맨 처음 인자로 받는 메서드로, 이전까지의 모든 예제에서 다뤘던 클래스 내의 메서드들은 전부 인스턴스 메서드라고 보면 된다. self는 해당 클래스로 만들어진 인스턴스(객체) 자신을 지칭한다. 인스턴스 메서드는 해당 인스턴스에만 영향을 준다. 인스턴스 메서드 안에서 정의된 속성을 인스턴스 속성(instance variable이라고도 함)이라 한다.
2. 클래스 메서드 (class method). 인스턴스 메서드와는 달리 클래스 메서드는 해당 클래스 전체에 영향을 끼친다. 이 클래스 메서드는 해당 클래스로 생성된 모든 인스턴스에 똑같은 영향을 끼친다. 클래스 메서드를 정의하려면 우선 해당 메서드 정의부분 바로 앞에 @classmethod 데코레이터를 지정한다. 그리고 클래스 메서드의 첫 매개변수는 self가 아닌 cls 키워드를 쓴다. 클래스 정의 밖에서 해당 클래스 메서드를 쓰고자 한다면 클래스명.클래스메서드명 이라고 치면 된다. 인스턴스 메서드가 아닌 클래스 영역에서 정의된 속성을 클래스 속성 (또는 클래스 변수, class variable)이라 한다. 
3. 정적 메서드 (static method). 클래스 내에 정의되기는 하나 해당 클래스나 객체에 영향을 끼치지 않는다. 메서드의 실행으로 인스턴스에 영향을 주지 않는 메서드를 만들고자 할 때, 객체와 독립적이지만 클래스 내에 포함되어야 하는 메서드를 작성할 때 주로 쓰인다. 인스턴스와 독립적이므로 인스턴스 속성 및 메서드에 접근할 수 없다. 정적 메서드를 정의하기 위해, 메서드 정의 부분 바로 위에 @staticmethod 를 붙인다. 인스턴스 메서드와 클래스 메서드와는 달리 self, cls처럼 필수적으로 지정해야 하는 매개변수는 없다. 

## 인스턴스 메서드

```python
class User():
    def __init__(self, name, user_name):
        self.name = name
        self.user_name = user_name

    def get_profile(self):
        return self.name, self.user_name

user_1 = User('나이썬', 'meython')
user_2 = User('이일영', 'two_one_zero')

print('user_1')
print(user_1.get_profile())
print('user_2')
print(user_2.get_profile())
```

```python
user_1
('나이썬', 'meython')
user_2
('이일영', 'two_one_zero')
```

위 예제에서는 같은 클래스로 user_1, user_2 두 개의 객체를 생성하는 것을 볼 수 있다. 실행결과를 보면 두 객체에서 같은 인스턴스 메서드를 호출하여 출력하고 있지만 결과는 다른 것을 알 수 있다. 같은 클래스에서 나왔음에도, 그래서 같은 이름의 메서드와 같은 이름의 속성을 가짐에도 두 객체는 서로 독립적인 객체이다. 

## 클래스 메서드

다음 예제는 어느 홈페이지에 가입한 사람들의 이름 및 유저 이름에 대한 데이터를 클래스로 구현한 예제이다. 예제에서는 홈페이지에 가입된 총 유저 수를 알기 위해 클래스 속성 및 클래스 메서드를 활용하였다. 

```python
class User():
    number_of_user = 0      #1 클래스 속성
    def __init__(self, name, user_name):
        self.name = name
        self.user_name = user_name
        User.number_of_user += 1   #2 인스턴스 메서드 안에서 클래스명.클래스속성 으로 쓰면 된다.

    def get_profile(self):
        return self.name, self.user_name

    @classmethod   #3  클래스 메서드 정의를 위한 데코레이터 지정
    def show_number_of_users(cls):   #4 클래스 자체를 의미하는 cls
        print('현재 가입된 인원 수는 {}명 입니다.'.format(cls.number_of_user))

user_1 = User('나이썬', 'meython')
user_2 = User('이일영', 'two_one_zero')
User.show_number_of_users()    #5 클래스 정의 밖에서 클래스명.클래스메서드() 로 호출하면 된다. 
```

```python
현재 가입된 인원 수는 2명 입니다.
```

#1 은 인스턴스 메서드 바깥 영역임을 알 수 있고, 이 영역에서 정의된 속성은 클래스 속성이 된다. #2에서처럼 해당 클래스 속성을 인스턴스 메서드 안에서 호출하거나 수정하고자 할 때 해당 클래스명.클래스속성 이라고 치면 된다. #3에서는 클래스 메서드로 만들고자 하는 메서드명 바로 위에 classmethod라는 데코레이터를 지정하였다. 그 후 #4에서 메서드를 정의하고 있다. 클래스 메서드의 첫 매개변수로 cls를 지정하고 있으며 이는 해당 클래스 자체를 의미한다. 그러면 해당 메서드 안에서 클래스 자체를 참조하고자 한다면 cls 키워드를 계속 이용하면 된다. (마치 인스턴스 메서드에서 인스턴스 자신을 참조하기 위해 self 키워드를 쓰는 것과 같은 원리이다) 참고로 클래스 메서드에 cls 매개변수를 지정하지 않으면 에러가 난다.

user_1과 user_2라는 객체를 형성하였다. 그리고 해당 클래스 메서드를 호출하였을 때 실행결과는 2명이라고 나왔다. 즉, 클래스 속성 및 클래스 메서드는 클래스 자체에 영향을 미쳐서 해당 클래스로 형성된 객체 모두에게 영향을 끼친다. 그렇지 않았더라면 위 예제에서 애초에 홈페이지에 가입된 유저의 수를 해당 방법으로 구할 순 없었을 것이다. 

## 정적 메서드

다음 예제에서는 홈페이지 운영자가 배너 광고를 넣고 싶어서 코드를 추가한 모습이다. 홈페이지에 가입된 유저의 정보가 추가되건 바뀌던 삭제(회원탈퇴)되건 상관없이 배너 광고를 독립적으로 화면에 노출시키고 싶었던 운영자는 정적 메서드를 사용하였다.

```python
class User():
    def __init__(self, name, user_name):
        self.name = name
        self.user_name = user_name

    def get_profile(self):
        return self.name, self.user_name

    @staticmethod  #1  정적 메서드 정의를 위해 해당 데코레이터 지정
    def banner_ad():   #2 굳이 아무런 매개변수를 지정하지 않아도 실행됨.
        print('코딩을 제대로 배우고 싶다면? xx코딩학원!')

user_1 = User('나이썬', 'meython')
user_2 = User('이일영', 'two_one_zero')

User.banner_ad()  #3  클래스명.정적메서드명()
```

```python
코딩을 제대로 배우고 싶다면? xx코딩학원!
```

유저들의 데이터나 User 클래스에 영향을 끼치지 않고 독립적으로 배너 광고를 실행하고자 하였기에 정적 메서드를 선택하였다. 

참고로 user_1.banner_ad()처럼 객체.정적메서드명() 이라고 쳐도 해당 정적 메서드가 실행됨을 확인하였다. 

# 다형성 (polymorphism)

파이썬에서의 다형성은 서로 다른 클래스 내에서 같은 이름의 메서드를 사용하더라도 메서드 내부에 구현된 코드가 서로 달라 서로 다르게 작동하는 것을 의미한다. 앞서 다뤘던 오버라이딩이 다형성을 구현하는 예이다. 

```python
class Person():
    def __init__(self, name, job):
        self.name = name
        self.job = job

    def show_profile(self):
        print('{}은 일반 시민입니다.'.format(self.name))

class Police(Person):
    def show_profile(self):
        print('{}은 경찰입니다.'.format(self.name))

class Doctor(Person):
    def show_profile(self):
        print('{}은 의사입니다.'.format(self.name))

person_list = [Person('김백수', '시민'), Police('정경찰', '경찰'), Doctor('박의사', '의사')]
for obj in person_list:
    obj.show_profile()
```

```python
김백수은 일반 시민입니다.
정경찰은 경찰입니다.
박의사은 의사입니다.
```

위 예제에서는 Police, Doctor 클래스가 Person 클래스를 상속받고 있고, 하위 클래스들은 상위 클래스에 있던 show_profile()이라는 메서드 내부 코드에 변형을 주었다. 이렇게 show_profile()이라는 같은 메서드 이름이지만 내부 코드를 다르게 하였다. 그럼에도 마지막 줄의 obj.show_profile()이라는 코드를 통해, 메서드명이 show_profile()인 메서드가 존재하기만 한다면 실행된다. 즉, 다형성은 겉으로 보기엔 같은 형태의 메서드가 서로 다른 기능을 수행하는 의미로 볼 수 있을 것 같다. 

# 덕 타이핑 (Duck typing)

“만약 오리처럼 걷고, 오리처럼 운다면, 그건 오리다.”

덕 타이핑은 어떤 클래스냐에 상관없이 구현된 메서드로만 판단하는 방식이다. 서로 다른 클래스라도 객체의 적합성은 객체가 어떤 유형이냐가 아닌 특정 메서드와 속성이 존재하느냐로 결정되는 것이다. 

```python
class Dog():
    def who_are_you(self):
        return '개'

    def speak(self):
        print('월월!')

class Person():
    def who_are_you(self):
        return '술에 만취한 사람'

    def speak(self):
        print("#!@##)@$으아어")

def dog_typing(obj):
    print('당신은 무슨 소리를 내죠?')
    obj.speak()
    print('그렇다면 당신은 누구인가요?')
    print(obj.who_are_you())
		# obj로 넘어오는 인자가 객체이고, speak(), who_are_you() 메서드
    # 만 있으면 실행 가능. 그렇지 않으면 오류 발생

dog_typing(Dog())
print("====")
dog_typing(Person())
print('====')
print("둘은 똑같군요")
```

```python
당신은 무슨 소리를 내죠?
월월!
그렇다면 당신은 누구인가요?
개
====
당신은 무슨 소리를 내죠?
#!@##)@$으아어
그렇다면 당신은 누구인가요?
술에 만취한 사람
====
둘은 똑같군요
```

위 예제에서 Dog와 Person클래스는 서로 상속관계도 아닌 서로 다른 클래스이다. 다만 내부에는 같은 이름의 메서드들이 구현되었다. 물론 메서드 내부 코드들은 다르다. 

서로 다른 독립된 클래스임에도, 같은 이름의 메서드가 존재하기 때문에 dog_typing 함수 내의 obj.speak(), obj.who_are_you()라는 코드에서도 두 클래스는 문제없이 실행된다. 이것이 덕 타이핑이며, “서로 다른 클래스라도 객체의 적합성은 객체가 어떤 유형이냐가 아닌 특정 메서드와 속성이 존재하느냐로 결정되는 것이다. ” 의 의미인 것이다. 

- tmi
    
    마치 전래동화 “해와 달이 된 오누이”에서 호랑이를 피해 초가집으로 도망간 오누이에게 호랑이가 엄마 옷을 입고 문풍지로 자신의 손을 넣어 엄마 손이라고 하는 것 같다. 여기선 사람 손처럼 생겼으면 사람인가보다. ~~타이거 타이핑~~
    

# special method

result = 1 + 2 라는 코드가 있다고 해보자. 그런데 가만히 생각해보면 궁금점이 하나 생긴다. 1과 2라는 integer 객체는 어떻게 +라는 기호만 가지고 이 기호가 ‘덧셈’ 기능을 하도록 하는 것일까? 그리고 애초에 = 기호만으로 어떻게 result에 할당이 되는 걸까? 

이러한 기능들은 파이썬에서 여러 가지 special method (또는 magic method라고도 한다)들을 제공하기 때문이다. 보통 이 스페셜 메서드의 이름은 시작과 끝에 두 개의 언더바(\_\_)가 들어간다. 여태껏 클래스 내에서 봤던 \_\_init\_\_ 메서드도 스페셜 메서드인 것이다. 덕분에 우린 별다른 추가적인 노력 없이도 클래스에 인자를 넘기고 이를 변수에 할당하여 객체를 형성하는 것이 가능한 것이었다. 이 메서드 외의 다른 스페셜 메서드들에 대해서도 알아보자.

다음 예제에서는 EnglishWord라는 클래스를 정의하여 영단어를 받아 속성에 저장하고, 다른 영단어와 비교하여 해당 영단어와 같은지 판별해주는 메서드를 구현하였다. 아래 예제에서 equals()라는 메서드가 그 역할을 하도록 구현하였다. 이 때 대문자인 경우 모두 소문자로 만들고 비교하도록 만들었다.

```python
class EnglishWord():
    def __init__(self, word):
        self.word = word

    def equals(self, other_word):  # other_word는 EnglishWord 클래스로 만들어진 인스턴스 인자
        return self.word.lower() == other_word.word.lower()

greeting = EnglishWord('hi')
cap_hi = EnglishWord('HI')
other_hi = EnglishWord('hello')

print(greeting.equals(cap_hi))  
print(greeting.equals(other_hi))
```

```python
True
False
```

해당 클래스 정의 후, 3개의 영단어들을 각각 하나의 객체에 대입하였다. 그 후 greeting.equals(EnglishWord 클래스 인스턴스) 식으로 다른 객체의 영단어와 해당 객체의 영단어가 서로 같은지 비교하고 있다. 

greeting.equals(cap_hi) 같은 방식보다는 greeting == cap_hi가 더 직관적이고 파이썬스러울 것이다. 이를 가능케 하는 special method로 \_\_eq\_\_()가 있다. 이번에는 equals() 메서드 대신 \_\_eq\_\_() 메서드를 이용해보자.

```python
class EnglishWord():
    def __init__(self, word):
        self.word = word

    def __eq__(self, other_word):  # other_word는 EnglishWord 클래스로 만들어진 인스턴스 인자
        return self.word.lower() == other_word.word.lower()

greeting = EnglishWord('hi')
cap_hi = EnglishWord('HI')
other_hi = EnglishWord('hello')

print(greeting == cap_hi)  #1
print(greeting == other_hi)  #2
#print(greeting.__eq__(cap_hi))  #3 
#print(greeting.__eq__(other_hi))  #4
```

```python
True
False
```

앞선 예제에서 메서드 이름만 바꿨고, 메서드 내부 코드는 전혀 바꾸지 않았다. 이제 #1, #2처럼 greeting == cap_hi 와 같은 문법이 가능해진다. 물론 #3, #4처럼 해도 똑같이 동작한다. 확인하고자 한다면 #1, #2는 주석처리하고 #3, #4 앞에 #을 지우고 다시 실행해보면 된다. 

그렇다면 이외에 또 다른 special method들에는 무엇이 있을까? 이를 표로 정리하였다.

- 비교 연산을 위한 special method

| method | 사용법 |
| --- | --- |
| \_\_eq\_\_(self, other) | self == other |
| \_\_ne\_\_(self, other) | self != other |
| \_\_lt\_\_(self, other) (앞에 소문자 L) | self < other |
| \_\_gt\_\_(self, other) | self > other |
| \_\_le\_\_(self, other) | self <= other |
| \_\_ge\_\_(self, other) | self >= other |

- 수학 관련 special method

| method | 사용법 |
| --- | --- |
| \_\_add\_\_(self, other) | self + other |
| \_\_sub\_\_(self, other) | self - other |
| \_\_mul\_\_(self, other) | self * other |
| \_\_floordiv\_\_(self, other) | self // other |
| \_\_truediv\_\_(self, other) | self / other |
| \_\_mod\_\_(self, other) | self % other |
| \_\_pow\_\_(self, other) | self ** other |

> \_\_add\_\_()와 \_\_mul\_\_() 메서드는 꼭 숫자에만 적용되는 것은 아니다. 문자열에도 적용가능하며, 각각 두 문자열을 잇는 역할과 해당 문자열을 여러 번 반복하는 역할을 가진다.
> 

- 그 외 special method

| method | 사용법 |
| --- | --- |
| \_\_str\_\_(self) | str(self) |
| \_\_repr\_\_(self) | repr(self) |
| \_\_len\_\_(self) | len(self) |

이 외에도 더 많은 special method가 있다고 한다. 

- 추측
    
    위 표에서 other는 객체만 가능한 듯 싶다. self 자체가 객체이므로 이 객체와 연산이 가능하려면 other도 객체여야 하지 않을까?
    

이제 앞선 예제에 더 많은 speical method들을 적용시켜보자.

```python
class EnglishWord():
    def __init__(self, word):
        self.word = word

    def __eq__(self, other_word):  # other_word는 EnglishWord 클래스로 만들어진 인스턴스 인자
        return self.word.lower() == other_word.word.lower()

    def __len__(self):
        return len(self.word)

    def __add__(self, other_word):
        '''other_word는 EnglishWord 클래스 인스턴스'''
        return self.word + other_word.word

    def __mul__(self, number):
        return self.word * number

greeting = EnglishWord('hi')
cap_hi = EnglishWord('HI')
other_hi = EnglishWord('hello')

print(cap_hi == other_hi)
print(len(greeting))
print(cap_hi + other_hi)
print(greeting * 3)
```

```python
False
2
HIhello
hihihi
```

# composition

composition 또는 aggregation이라고 하는 이 개념은 어떤 클래스 안에 다른 클래스의 객체나 속성, 메서드 등을 명시하여 다른 클래스의 기능을 쓰는 것을 의미한다. 상속과 다른 점은, 상속 받은 하위 클래스 내부에선 상위 클래스를 직접적으로 언급하지 않는다. 즉 다른 클래스를 해당 클래스 내부에서 암시적 선언을 한다. 그러나 composition의 경우, 다른 클래스명을 해당 클래스 내부에서 명시적으로 선언한다. composition은 상속을 사용 시 클래스 간 관계 또는 코드 자체가 복잡해질 때 대신해서 쓰인다고 한다. 즉 상속이나 컴포지션이나 다른 클래스의 기능을 이용하는 건 똑같으나, 상속을 쓰지 않는게 좋은 상황일 경우, 또는 일부 기능만 사용할 거라 전체 기능은 필요없다면 컴포지션을 쓰면 되는 것이다. 

다음 예제에서는 나만의 계산기를 클래스로 구현하는 모습이다. 이 때 MyTools라는 다른 클래스로부터 일부 기능을 빌려와 계산기 클래스를 구현하는 composition을 이용하고 있다.

```python
import math

class MyTools():
    def add_total(self, iterable=[]):
        total = 0
        for num in iterable:
            total += num
        return total

    def square(self, num):
        return num ** 2

class MyCalculator():
    def __init__(self, num_list):
        self.num_list = num_list
        self.tools = MyTools()   #1 composition

    def get_average(self):
        return self.tools.add_total(self.num_list) / len(self.num_list)

    def pythagoras_end(self):
        '''
        num_list의 첫번째 요소 숫자와 마지막 요소 숫자에 
        피타고라스 법칙 적용
        '''
        first_squared = self.tools.square(self.num_list[0])
        second_squared = self.tools.square(self.num_list[-1])
        return math.sqrt(first_squared + second_squared)

calc = MyCalculator([3, 2, 1, 5, 4])
print('평균값:', calc.get_average())
print('피타고라스 적용값:', calc.pythagoras_end())
```

```python
평균값: 3.0
피타고라스 적용값: 5.0
```

#1 에서 다른 클래스인 MyTools 클래스 객체를 self.tools 속성에 저장한 후, get_average나 pythagoras_end 메서드에서 해당 클래스의 기능을 이용하고 있다. 이처럼 클래스명 옆에 소괄호에 상위 클래스를 넘기고, 그 후 하위 클래스에서는 상위 클래스를 직접적으로 명시하지 않는 (하더라도 super() 키워드를 이용할 것이다) 상속과는 다르게, MyCalculator 클래스는 안에 다른 클래스인 MyTools를 #1에서  “나 이 클래스 쓸거임!”이라고 명시적으로 선언하고 있다. 

# class vs module

코드를 짤 때 클래스로 짤 지 모듈로 짤 지에 대해 판단하는데에 도움이 될만한 팁들

1. 수많은 인스턴스들이 필요하고 그 인스턴스들의 행동(메서드)들이 비슷하나 내부 상태(속성)는 다르기를 원하면 객체가 유용하다.
2. 클래스는 상속을 지원하나 모듈에는 그런 거 없다.
3. 무언가가 하나만 필요하다면, 모듈이 좋을 지도 모른다. 프로그램에서 얼마나 자주 모듈이 참조되건 오직 하나의 복사본만 로드되기 때문이다.
4. 여러 개의 값을 포함하는 여러 개의 변수들이 있고 이 변수들이 여러 개의 함수의 인자로 넘겨질 수 있다면 이 변수와 함수들을 하나로 모아 클래스로 정의하는 것이 낫다. 예를 들어, 컬러 이미지를 표현하기 위해 size와 color를 키로 갖는 dictionary를 사용한다고 해보자. 그러면 이미지가 여러 개인 경우 각 이미지마다 또 다른 dictionary들을 생성해야 한다. 그 후 이들을 scale()이라든지 transform()과 같은 함수의 인자로 넘길 것이다. 이는 키와 함수들을 추가시킬수록 너무 복잡해진다. 따라서 이 경우 Image라는 클래스를 만들어 size와 color를 속성으로, scale()과 transform()을 메서드로 정의하여 이 모든 걸 한 장소에 몰아서 정의하는 것이 낫다. 그러면 이미지가 여러 개더라도 그에 맞는 객체들을 만들어주기만 하면 훨씬 간편하고 코드도 간단해질 것이다. 
5. 문제를 풀 때 가장 간단한 해결책을 써라. dictionary, list 같은 자료형들로 문제를 풀 수 있으면 그걸로 풀자. 모듈보다 빠르고 클래스로 직접 만들어 푸는 것보다 더 간단하기 때문이다. 

# named tuple

named tuple은 tuple의 하위 클래스로, [offset]처럼 위치뿐만 아니라 이름으로도 값에 접근할 수 있다. 

named tuple은 파이썬 내장 함수는 아니라서 모듈을 import해야 한다. 다음 예제의 첫번 째 줄에서 그 역할을 하고 있다. 

```python
from collections import namedtuple  #1

User = namedtuple('UserInfo', 'name age')  #2 namedtuple 정의
user_inst = User('김지신', 24)  #3  앞서 정의된 namedtuple 객체 생성
print(user_inst)  #4
print(user_inst.name, user_inst.age)  #5
```

```python
UserInfo(name='김지신', age=24)
김지신 24
```

#1 에서 namedtuple이 포함된 collections라는 모듈을 먼저 import 해주고 있다. 그 다음, namedtuple에 여러 인자들을 대입하여 이를 User에 할당하고 있다. 이 때 첫 번째 인자 (UserInfo)는 해당 namedtuple에 붙일 이름이다. 그리고 두 번째 인자로 필드 이름(매개변수)을 만들어준다. 이 때 name과 age라는 필드 이름을 만들 것이므로 두 이름 사이에 띄어쓰기를 해준다. 안 그러면 nameage라는 하나의 이름으로 인식되므로 이에 주의한다. 

그 후, 생성된 User라는 namedtuple을 user_inst에 할당하는데 이 때 앞서 정의한 필드 이름 name, age에 각각 해당하는 데이터를 대입해주면 된다. 그러면 #5에서처럼 해당 named tuple 내에서 name에 할당된 값만 얻고프다면 user_inst.name 이런 식으로 쓰면 된다. 

#4로 named tuple 전체를 출력하면 named tuple의 이름인 UserInfo부터 시작하여 출력됨을 알 수 있다. 

즉, 튜플은 튜플인데 튜플을 이루는 요소들이 필드 이름을 갖게 되는 것이다. 그리고 튜플 자체도 고유의 이름을 갖게 된다 (UserInfo).

그런데 위 예제는 꼭 클래스와 같은 행동을 하는 것만 같다. 위 예제를 클래스로만 구현하면 다음과 같다.

```python
class User():
    def __init__(self, name, age):
        self.name = name
        self.age = age
    

user_inst = User('김지신', 24)  #1
print(user_inst.name, user_inst.age)
```

```python
김지신 24
```

예제 15-2를 예제 15-1의 named tuple과 비교해보자. namedtuple() 함수에 두 번째 인자로 대입했던 name과 age는 예제 15-2에서 클래스의 속성과 같다. 그 후 이 클래스 인스턴스를 형성하고 이 인스턴스의 속성을 호출하는 것까지 서로 똑같음을 알 수 있다. 예제 15-1의 #2는 예제15-2의 클래스 정의 부분과 속성 이름 정의 부분과 대응된다. 예제15-1의 #3는 마치 정의된 클래스로 객체를 형성하는 것과 같다. 

한 편, 예제 15-1의 #3에서 실제 데이터를 대입하여 namedtuple을 형성한다. 이 때 실제 데이터를 대입하는 다른 방법이 있다. 다음 예제는 예제 15-1에서 이어서 쓴 것이다.

```python
from collections import namedtuple

User = namedtuple('UserInfo', 'name age')
user_inst = User('김지신', 24)

dict_data = {'name': '박온고', 'age': 32}  #1
user_2_inst = User(**dict_data)  #2
#user_2_inst = User(name='박온고', age=32)  #3
print(user_2_inst)
```

```python
UserInfo(name='박온고', age=32)
```

위 예제에서 #1처럼 앞서 정의했던 이름들 name과 age를 key로 하고 실제 데이터 값인 ‘김지신’과 32라는 숫자를 각각 value로 취하는 dictionary를 정의하였다. 그리고 이를 User() 함수에 키워드 인자로 대입하고 있다. 그러면 실행 시 dict_data라는 dictionary로부터 key와 value를 추출하고 key는 매개변수, value는 매개변수로 넘기는 인자로 인식되는 것이다. 즉 #2 코드는 #3 코드와 동일한 기능을 수행한다. 

named tuple은 불변량 (immutable)이다. 하지만 새 named tuple를 만들 때 기존에 있던 named tuple 요소를 바꿔서 이를 반환하는 식으로 할 수는 있다.

```python
from collections import namedtuple

User = namedtuple('UserInfo', 'name age')
user_inst = User('김지신', 24)

dict_data = {'name': '박온고', 'age': 32}
user_2_inst = User(**dict_data)
#user_2_inst = User(name='박온고', age=32)

# 새 코드 추가 =================================
user_3_inst = user_2_inst._replace(name='나이썬', age=25)  #1
print(user_3_inst)
print(user_3_inst[0])
```

```python
UserInfo(name='나이썬', age=25)
나이썬
```

위 예제의 #1 부분을 보면 user_3_inst를 통해 새로운 UserInfo라는 이름의 named tuple을 만들고자 한다. 이 때 기존에 있던 user_2_inst라는 named tuple을 요소값만 바꾼 후 이 named tuple을 user_3_inst에 할당하고 있는 것이다. 

앞서 말했듯 named tuple은 immutable이기에 _replace() 함수를 써도 기존의 named tuple (user_2_inst) 내용은 바뀌지 않는다. ( #1 코드를 추가해도 user_2_inst에는 여전히 ‘박온고’ , 32라는 데이터가 그대로 존재한다) 

따라서 이미 만들어진 named tuple에 새 속성과 새 값을 대입하려고 하면 에러를 일으킨다. 

named tuple의 장점

- immutable 객체처럼 행동한다.
- 객체보다 시공간(?)적으로 효율적이다. 속성만 필요하면 굳이 클래스라는 긴 코드를 쓰지 않고 named tuple을 이용하면 코드도 짧아지고 더 긴 코드를 치느라 시간을 낭비하지도 않아도 되니깐.
- dictioary[key] 처럼 조금은 비직관적인 방법 대신 .attribute와 같은 방식으로 닷(.)을 쓰기만 하면 해당 속성값에 접근할 수 있어 좀 더 직관적이다.
- 튜플임에도 속성을 dictionary의 key처럼 이용할 수 있다.

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 점프 투 파이썬

[점프 투 파이썬](https://wikidocs.net/28#_9)

[3] 코딩 도장, 클래스로 데코레이터 만들기, 클래스로 매개변수와 반환값을 처리하는 데코레이터 만들기

[파이썬 코딩 도장](https://dojang.io/mod/page/view.php?id=2430)

[4] 예제로 배우는 파이썬 프로그래밍

[예제로 배우는 파이썬 프로그래밍 - 클래스](http://pythonstudy.xyz/python/article/19-%ED%81%B4%EB%9E%98%EC%8A%A4)

[5] 코딩도장, 정적 메서드 사용하기

[파이썬 코딩 도장](https://dojang.io/mod/page/view.php?id=2379)

[6] 

[(Cs with python)-클래스(메서드 오버라이딩, 다형성, 추상클래스)](https://kwonsoonwoo.github.io/%EC%BB%B4%ED%93%A8%ED%84%B0%20%EA%B3%B5%ED%95%99/2018/10/20/cs-with-python-%ED%81%B4%EB%9E%98%EC%8A%A4(%EB%A9%94%EC%84%9C%EB%93%9C-%EC%98%A4%EB%B2%84%EB%9D%BC%EC%9D%B4%EB%94%A9,-%EB%8B%A4%ED%98%95%EC%84%B1).html)

[7]

[파이썬의 다형성(Polymorphism)](https://snepbnt.tistory.com/123)

[8] 덕 타이핑이란 무엇인가요?

[파이썬 코딩 도장](https://dojang.io/mod/page/view.php?id=2397)

[9] 덕 타이핑(Duck Typing 이란?)

[점프 투 파이썬](https://wikidocs.net/16076)

[10] Composition

[잔재미코딩 온라인 강의 사이트입니다](https://www.fun-coding.org/PL&OOP1-11.html)