---
title: "[지식/이론][python] 디자인 패턴"
category: knowledge
tag: ["python", "knowledge", "디자인 패턴", "design pattern"]
---
# 개요

디자인 패턴(design pattern)은 잘 설계된 구조의 형식적 정의를 소프트웨어 엔지니어링으로 옮긴 것이다. 디자인 패턴에는 다양한 것들이 존재하고, 이들을 이용하여 각기 다른 문제들을 해결할 수 있다.

# 데코레이터 (decorator) 패턴

데코레이터의 사용도 디자인 패턴에 포함되어 있어 이 내용에 포함시켰다. 데코레이터에 대한 설명은 [함수 (Function)](/python/function/) 의 Decorator 장에서 자세히 설명되어 있다. 

# 옵저버 (observer) 패턴

옵저버 패턴은 한 객체에 의존하고 있는 여러 객체(관찰자)들이 존재할 때(일대다 관계), 그 객체의 상태가 변하면 변경 사항을 그 객체에 종속된 모든 객체들에게 알려줘 자동으로 상태를 갱신하는 방법이다. 유튜버와 구독자 관계와 같다. 한 유튜버를 여러 구독자들이 구독하고 있을 때, 해당 유튜버가 새로운 영상을 올리면 그에 대한 알림이 모든 구독자들에게 통지되는 것과 같다. 보통 관찰당하는 객체를 subject(주체), 관찰하는 객체를 observer라 부르기도 한다. 

다음은 옵저버 패턴의 예이다.

```python
class Youtuber():
    def __init__(self, name: str = "") -> None:
        self.name = name
        self.subscribers = []
        self.videos = []

    def register_subscriber(self, subscriber) -> None:
        """
        구독자를 등록한다.
        subscriber: Subscriber
        """
        if subscriber not in self.subscribers:
            self.subscribers.append(subscriber)
        else:
            print("이미 존재하는 구독자입니다.")

    def unregister_subscriber(self, subscriber) -> None:
        """
        기존에 존재하던 구독자가 구독 취소하는 경우,
        구독 목록에서 삭제한다.
        subscriber: Subscriber
        """
        try:
            self.subscribers.remove(subscriber)
        except ValueError:
            print("존재하지 않는 구독자입니다.")

    def notify_all(self, message) -> None:
        """
        새로 변경된 점을 구독자들에게 알린다.
        """
        for subscriber in self.subscribers:
            subscriber.update(message)

    def upload_video(self, new_video: str) -> None:
        """
        유튜버가 새 영상을 자신의 채널에 업로드한다.
        새 영상을 올리면 이를 모든 구독자들에게 알림을 전송한다.
        """
        if new_video not in self.videos:
            self.videos.append(new_video)
            self.notify_all(f"{self.name}님의 '{new_video}' 영상이 방금 업로드 되었습니다. 지금 바로 시청하세요!")
        else:
            print("이미 해당 채널에 존재하는 영상입니다.")

class Subscriber():
    def __init__(self, user_id: str = "") -> None:
        self.user_id = user_id

    def update(self, message: str) -> None:
        """
        구독 중인 유튜버가 새 영상을 올리면 해당 사항을 구독자들에게 공지한다.
        """
        message = f"{self.user_id}님, " + message
        print(message)

if __name__ == '__main__':
    youtuber = Youtuber("코딩관련튜브TV")
    
    sub1 = Subscriber('코딩조아')
    sub2 = Subscriber('지나가던백수')
    sub3 = Subscriber('제로콜라')

    youtuber.register_subscriber(sub1)
    youtuber.register_subscriber(sub2)
    youtuber.register_subscriber(sub3)

    new_video = "코딩은 뭘까? 코딩에 관한 지식 TOP 10"
    youtuber.upload_video(new_video)
```

```python
코딩조아님, 코딩관련튜브TV님의 '코딩은 뭘까? 코딩에 관한 지식 TOP 10' 영상이 방금 업로드 되었습니다. 지금 바로 시청하세요!
지나가던백수님, 코딩관련튜브TV님의 '코딩은 뭘까? 코딩에 관한 지식 TOP 10' 영상이 방금 업로드 되었습니다. 지금 바로 시청하세요!
제로콜라님, 코딩관련튜브TV님의 '코딩은 뭘까? 코딩에 관한 지식 TOP 10' 영상이 방금 업로드 되었습니다. 지금 바로 시청하세요!
```

subject에 해당하는 Youtuber 클래스에는 우선 observer에 해당하는 Subscriber 클래스 객체가 Youtuber 클래스 객체에 종속될 수 있도록 등록해주는 register_subscriber 메서드를 구현한다. 해당 메서드를 통해 observer는 subject에 종속될 수 있고, 향후 subject 객체의 내부에 변경사항이 있으면 이에 대한 정보가 observer의 update() 메서드를 통해 업데이트 될 것이다. 추가로, subject 객체의 종속 관계를 끊고자 하는 observer가 있으면 이를 수행하도록 unregister_subscriber 메서드도 구현하였다. 

Youtuber 클래스의 객체가 Subscriber 객체에 변경사항을 알려주는 실질적인 코드가 notify_all() 메서드에 구현되어 있다. 해당 메서드에는 Subscriber의 update() 메서드를 호출하는 방식을 통해 Youtuber 객체의 변경사항을 Subscriber 객체에 업데이트한다. 

추가로, Youtuber 클래스의 upload_video() 메서드처럼, 어떤 작업을 수행하는 메서드를 작성하고, 해당 메서드가 호출되어 어떤 작업을 수행 후 변경된 사항들을 Subscirber들에게 알려주고 싶으면 해당 메서드의 마지막 부분에 notify_all() 메서드 호출을 하여  Subscriber들에게 알려주도록 코드를 구성할 수도 있다. 

Subscriber 클래스에는 Youtuber 클래스 객체의 변경 사항을 받을 update() 메서드가 구현되어 있다. 

이러한 작업을 통해 Youtuber 객체에 변경 사항이 추가되면 (영상이 추가) Subscriber 객체들에게 해당 사항을 통지할 수 있게 된다. 

옵저버 패턴을 활용하는 대표적 예는 GUI 프로그래밍에서의 이벤트 및 이벤트 핸들러이다. 어떤 위젯에서 이벤트가 발생하면 이를 관찰하고 있던 이벤트 헨들러가 해당 사항을 감지하고 그에 따른 작업을 수행한다.

# 싱글턴 (singleton) 패턴

싱글턴 패턴은 어떤 클래스의 객체를 단 한 번만 생성하고 그 이상은 생성하지 못하게 막는 방법이다. 이는 클래스의 \_\_new\_\_() 메서드를 오버라이딩하여 구현할 수 있다. \_\_new\_\_() 메서드는 사용자 정의 클래스에 정의되지 않으면 파이썬 최상위 클래스인 object에서 알아서 해당 메서드를 호출하여 해당 클래스의 객체를 생성해준다. 사용자가 해당 메서드를 구현하려면 object 클래스의 \_\_new\_\_() 메서드를 호출하여 사용자 정의 클래스의 인스턴스를 생성하고 이를 반환시키는 코드가 필수로 구현되어야 한다. (그래야 해당 클래스의 객체를 생성할 수 있으니깐)

다음은 \_\_new\_\_() 메서드를 오버라이딩하여 싱글턴 패턴을 구현하는 예이다. 

```python
class ChosenOne():
    _is_instantiated = None

    def __new__(cls):
        if ChosenOne._is_instantiated is None:
            print("__new__ method is called.")
            ChosenOne._is_instantiated = super().__new__(cls)  # ChosenOne 객체 생성
        return ChosenOne._is_instantiated  # ChosenOne 객체 반환
    
    def __init__(self):
        print("__init__ method is called.")
    

if __name__ == '__main__':
    single_inst1 = ChosenOne()
    single_inst2 = ChosenOne()
    print(single_inst1)
    print(single_inst2)
    print(single_inst1 == single_inst2)
```

```python
__new__ method is called.
__init__ method is called.
__init__ method is called.
<__main__.ChosenOne object at 0x0000022301F33550>
<__main__.ChosenOne object at 0x0000022301F33550>
True
```

출력 결과를 보면, 해당 클래스의 인스턴스를 2번 생성하려고 시도했음에도  “\_\_new\_\_ method is called.”라는 문구가 단 한 번만 출력되었다. 그리고, 두 인스턴스의 주소가 동일하다. 이는 single_inst1과 single_inst2가 가리키는 객체는 단 하나의 ChosenOne() 객체인 것이고, 이는 해당 객체가 단 한 번만 생성되었음을 뜻한다. 

그러나 위 예제의 경우, 해당 객체를 여러 번 생성하려고 시도하면 객체는 하나만 생성되나, 해당 객체의 속성이 여러 번의 인스턴스화에 의해 제각기 달라질 수 있다. 위 출력 결과에서 “\_\_init\_\_ method is called.”이라는 문구가 2번 뜬 것을 통해 확인할 수 있다. 즉, 객체는 하나인데 객체의 속성이 서로 달라질 수 있다. 이러면 단 하나의 객체만 생성하도록 만든 의미가 사라지게 된다. 이를 방지하기 위해서는  \_\_init\_\_() 메서드도 \_\_new\_\_() 메서드와 마찬가지로 단 한 번만 호출되도록 구현해주면 된다.

```python
class ChosenOne():
    _is_instantiated = None
    _has_init = False  # 추가

    def __new__(cls):
        if ChosenOne._is_instantiated is None:
            print("__new__ method is called.")
            ChosenOne._is_instantiated = super().__new__(cls)  # ChosenOne 객체 생성
        return ChosenOne._is_instantiated  # ChosenOne 객체 반환
    
    def __init__(self):
        if ChosenOne._has_init is False:
            print("__init__ method is called.")
            ChosenOne._has_init = True
    

if __name__ == '__main__':
    single_inst1 = ChosenOne()
    single_inst2 = ChosenOne()
    print(single_inst1)
    print(single_inst2)
    print(single_inst1 == single_inst2)
```

```python
__new__ method is called.
__init__ method is called.
<__main__.ChosenOne object at 0x000002390AC93580>
<__main__.ChosenOne object at 0x000002390AC93580>
True
```

변경된 코드를 통해 “\_\_init\_\_ method is called.” 문구도 단 한 번만 출력되는 것을 확인할 수 있다.

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2]

[[Design Pattern] 옵저버 패턴 (Observer Pattern)](https://brownbears.tistory.com/555)

[3]

[[디자인 패턴 12편] 행동 패턴, 옵저버(Observer)](https://dailyheumsi.tistory.com/211)

[4]

[파이썬으로 옵저버 패턴을 구현해보자](https://weejw.tistory.com/108)

[5]

[[Design Pattern] 옵저버 패턴 - Python](https://deep-learning-challenge.tistory.com/102)

[6]

[01) 싱글턴 패턴](https://wikidocs.net/69361)