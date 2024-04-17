---
title: "[python] 클래스, 객체 추가 설명"
category: python
tag: ["python", "class", "object", "클래스", "객체", "불변 객체", "해시", "hash", "hashable", "abstract class", "추상 클래스"]
---
# 개요

객체를 생성하고 사용할 때에는 객체에 대해 어떤 데이터를 입력할 때 잘못된 값이 입력되었는지 유효성 검사를 할 필요가 있다. 또한 입력된 데이터들을 원하는 목적에 따라 가공할 수 있어야 한다. 이러한 조건들을 만족해야 우리가 원하는 목적의 객체를 형성시킬 수 있을 것이다. 이를 위해, 객체지향 프로그래밍에서는 객체 생성 시 데이터를 패키지화하고, 메서드를 제한시킴으로서 사용자가 원하는 속성만 가진 객체를 만들어야 한다. 파이썬에서는 이러한 객체를 생성시키기 위해 클래스를 제공한다. 

## 용어

- 바인딩(binding) 또는 에일리어싱(aliasing): 변수가 객체의 메모리 주소를 가리키는 것. 변수가 객체를 참조하는 것.

# 클래스와 객체

클래스(class)는 사전에 정의된 데이터와 메서드의 집합이다. 클래스에 선언된 코드 그대로 생성된 실체를 객체(object)라 부른다. 객체가 소프트웨어에서 실체화될 때, 즉 실제 메모리에 할당되어 사용될 때 이 실체를 인스턴스(instance)라 부른다. 객체는 인스턴스를 포함하는 좀 더 포괄적인 개념이다. 

파이썬에서 클래스를 정의하고 이에 따른 인스턴스를 생성하는 가장 간단한 방법은 다음과 같다.

```python
class MyClass():
    # 여기에 원하는 코드를 적어 클래스를 정의한다.
    pass

some_inst = MyClass()  # 클래스 정의에 따라 인스턴스를 생성한다.
```

클래스의 인스턴스 생성(class instantiation) 방법은 위 예제에서처럼 마치 함수를 호출하는 것과 똑같이 클래스명과 그 뒤에 괄호를 덧붙여 변수에 할당하면 된다. 이 때, MyClass()와 같이 클래스명과 그 뒤에 괄호를 붙인 형태를 생성자(constructor)라고 한다. 해당 코드를 통해 클래스의 인스턴스를 생성했기 때문에 붙여진 이름으로 추정된다. 생성자 호출 시 해당 클래스의 \_\_new\_\_()라는 특수 메서드가 호출되어 해당 클래스의 객체가 할당되고, 그 후 \_\_init\_\_() 메서드가 실행되어 객체를 초기화시킨다. 


# 불변 객체와 해시 가능성의 관계

[hashlib](/knowledge/python-hashlib/) 문서에 해시에 대한 개념이 설명되어있다.

해시 충돌의 가능성을 제외하면, 어떤 값을 해시 함수에 대입하면 그 결과값은 고유하다. 그래서 다른 값의 대입은 항상 서로 다른 값을 발생시키며, 반대로 만약 입력값 A, B의 해싱 결과값이 같다면 A = B이다. 이러한 사실을 이용하면 해시라는 개념을 통해 A와 B가 서로 똑같은지 다른지를 판별할 수 있다. 

딕셔너리는 키를 해시 함수에 대입하여 나온 값을 고유한 인덱스로 사용하여 그에 해당하는 값을 찾을 수 있도록 해주는 해시 테이블로 구현된다. 이러한 특성 덕분에 키만 알면 그에 해당하는 값을 아주 빠른 시간 내에 찾을 수 있다. (이러한 이유로 딕셔너리의 key가 반드시 불변 객체여야 하는 이유이다. 가변 객체라면 중간에 자꾸 데이터를 바꿈으로서 해시 충돌의 위험이 생기기에 딕셔너리의 본 의미인 “사전”의 기능을 상실할 수 있기 때문이다) set 자료형에서 중복되는 값을 허용하지 않는 이유도 해시 값의 고유성을 이용한 것이다. (set은 dictionary를 참조해서 구현했다고 한다) 그래서 딕셔너리의 key나 set의 원소는 숫자, 문자열과 같은 불변 객체를 사용하는 것이다. 만약 가변 객체도 딕셔너리의 key 또는 셋의 원소로 사용 가능하게 만들면 중간에 데이터를 변경할 수 있고, 이로 인해 중간에 해시값이 변경될 수 있어 해시 충돌의 위험이 생긴다. 즉, 서로 다른 입력값이 같은 해시값으로 나오는 오류가 발생할 수 있다. 그래서 가변 객체는 해시가 불가능하다.

> 딕셔너리를 구현하는 해시 테이블은 그 자체로 해시 충돌의 가능성이 있는데, 이러한 해시 충돌을 막는 알고리즘을 구현하여 해시 충돌의 위험을 최소화한다고 한다.
> 

어떤 객체가 해시 가능하다는 것은 그 객체는 불변형이라는 뜻이다. 

파이썬에서의 객체의 관점에서 설명하면, 객체의 수명 주기 동안(즉, 메모리 상에 존재하는 동안) \_\_hash\_\_() 메서드를 통해 해시값을 반환할 수 있고, \_\_eq\_\_() 메서드를 통해 다른 객체와 비교할 수 있다면 그 객체를 해시 가능(hashable)하다고 한다. 당연한 말인 것이, 앞서 입력값 A, B를 예로 든 것처럼 해시 충돌의 위험이 없는 해시값은 고유성을 띄기에 객체가 해시 가능해지면 서로 다른 객체와 비교하여 두 객체가 같은지 다른지 비교가 가능해진다. (마치 객체가 “주민등록번호”를 가진 것과 같다) 

사용자 정의 클래스로 생성된 객체는 기본적으로 해시 가능하며, 즉 불변 객체이다. 사용자 정의 클래스에서 \_\_hash\_\_() 메서드를 명시하여 내부에서 코드를 원하는대로 구현하여 사용할 수 있다. 

리스트와 같은 가변 객체는 해시 불가능하다. 파이썬에서 hash() 함수에 가변 객체를 대입하면 다음의 에러가 뜬다.

```python
a_tuple = ('a', 'b', 'c')
print(hash(a_tuple))

a_list = ['a', 'b', 'c']
print(hash(a_list))  # TypeError 예외 발생
```

```python
-7746738329679704537
TypeError: unhashable type: 'list'
```

# 추상 클래스 (abstract class)

추상 클래스는 일종의 “클래스 작성 지침서”에 비유할 수 있다. 추상 클래스는 이 클래스를 상속받는 클래스가 작성해야 할 메서드 목록들을 명시하고, 이 추상 클래스를 상속받는 클래스는 추상 클래스에 명시된 메서드들을 반드시 구현해야 한다. 

파이썬에서 추상 클래스를 만드는 방법은 다음과 같다. 

```python
from abc import *

class ABCClass(metaclass=ABCMeta):
    @abstractmethod
    def methodYouShouldWrite1(self):
        pass
```

1. 먼저 abc 모듈을 import 한다. (abc = Abstract Base Class)
2. 추상 클래스 정의를 작성하고, 괄호 안에 metaclass=ABCMeta라고 적는다.
3. 추상 클래스 안에 메서드 작성 시 이름 위에 @abstractmethod 데코레이터를 붙인다.

다음은 추상 클래스를 작성하고 이를 다른 클래스에서 상속하는 예이다.

```python
from abc import *

class ABCTodayPlan(metaclass=ABCMeta):
    @abstractmethod
    def wash(self):
        """아침에 일어나 씻는다."""
        pass

    @abstractmethod
    def haveMeal(self, what_food: str):
        """
        밥을 먹는다.
        what_food: 먹은 음식
        """
        pass

    @abstractmethod
    def study(self, subject: str, study_time: list[int]):
        """
        공부한다.
        subject: 공부 과목
        study_time: 공부 시간. [시, 분]
        """
        pass

class MyToday(ABCTodayPlan):
    def __init__(self):
        self.what_food = ''
        self.study_subject = ''
        self.study_time = []

    def wash(self):
        print("몸을 씻었습니다.")

    def haveMeal(self, what_food: str):
        self.what_food = what_food
        print(f"{self.what_food}을(를) 먹었습니다.")

    """
    def study(self, subject: str, study_time: list[int]):
        self.study_subject = subject
        self.study_time = study_time
        print(f"{self.study_subject} 과목을 {self.study_time[0]}시간 {self.study_time[1]}분 동안 공부했습니다.")
    """

    def get_all_info(self):
        return (self.what_food, self.study_subject, self.study_time)
    

if __name__ == '__main__':
    my_plan = MyToday()
    my_plan.wash()
    my_plan.haveMeal('밥과 반찬')
    #my_plan.study("파이썬", [1, 30])
    print(my_plan.get_all_info())
```

```python
TypeError: Can't instantiate abstract class MyToday with abstract method study
```

위 예제에서, 추상 클래스를 상속 받는 하위 클래스 MyToday() 내부에 추상 클래스에 작성된 메서드인 study() 메서드를 구현하지 않았다. 이로 인해 위와 같은 TypeError 예외가 발생한다. study() 메서드 주변의 따옴표를 지우고(my_plan.study() 부분도 주석 해제하자) 다시 실행시켜보자. 그러면 정상적으로 실행된다.

```python
몸을 씻었습니다.
밥과 반찬을(를) 먹었습니다.
파이썬 과목을 1시간 30분 동안 공부했습니다.
('밥과 반찬', '파이썬', [1, 30])
```

추상 클래스 자체는 인스턴스화할 수 없다. 예제 4-2의 if \_\_name\_\_ … 아래에 다음의 코드로 치환하고 실행시켜보자.

```python
if __name__ == '__main__':
    my_abc_inst = ABCTodayPlan()
```

```python
TypeError: Can't instantiate abstract class ABCTodayPlan with abstract methods haveMeal, study, wash
```

추상 클래스를 인스턴스화하려고 하면 위와 같이 TypeError 예외가 발생하여 인스턴스를 생성할 수 없다. 

추상 클래스는 이렇게 객체로 만들지 않고 오로지 하위 클래스가 작성해야할 메서드만을 알려주기에 추상 클래스 내부의 메서드 내용까지 구현할 필요가 없어 pass로 비워두는 것이다. (추상 클래스의 메서드 내용까지 구현하면 거기서부터 “추상” 클래스가 아니게 되는 셈이니…)

어떤 클래스를 구현할 때 꼭 필요한 기능을 메서드로 구현하는 걸 깜빡하는 걸 방지하고자 할 때 추상 클래스를 이용하면 유용할 것으로 보인다. 

---

References

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2] 

[파이썬 set의 데이터구조부터 hashtable까지(갑분 C 등장)](https://velog.io/@jewelrykim/파이썬-set의-데이터구조부터-hashtable까지갑분-C-등장)

[3]

[[자료구조] 해시테이블(HashTable)이란?](https://mangkyu.tistory.com/102)

[4]

[Python에서 Hashable이란](https://qqplot.github.io/python/2022/02/12/python_hashable.html)

[5]

[What does "hashable" mean in Python?](https://stackoverflow.com/questions/14535730/what-does-hashable-mean-in-python)

[6]

[해시가능하다(hashable) 의 정의](https://checkwhoiam.tistory.com/149)

[7]

[Hashable 이란? 쉬운 설명: python](https://analytics4everything.tistory.com/138)

[8]

[[파이썬 Python] 파이썬에서 해시가능(Hashable)의 의미와 불변객체와의 관계](https://knockcha.tistory.com/44)

[9]

[45. class 정리 - 추상클래스(abstract class)](https://wikidocs.net/16075)

[10]

[파이썬 코딩 도장: 36.6 추상 클래스 사용하기](https://dojang.io/mod/page/view.php?id=2389)

[11] 바인딩 용어 설명

[1) 리스트](https://wikidocs.net/2846)