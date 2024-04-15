---
layout: single
title:  "[Python] 동시성(concurrency)"
categories: ["Python"]
tag: ["Python", "queue", "data structure", "process", "concurrency", "thread", "synchronous", "asynchronous", "I/O bound", "CPU bound"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

여태까지 배운 내용들에서의 대부분의 프로그램들은 한 기계에서만 작동했으며, 한 번에 한 줄씩 실행하였다. 그러나 한 번에 여러 가지 일들을 동시에 수행할 수 있다. (동시성) 

동시성과 관련된 용어가 있다.

- 동기 (synchronous) : 어떠한 작업이 동시에 일어나는 것. 그리고 어떠한 작업을 하고 나서야 다른 작업을 시작할 수 있다.
- 비동기 (asynchronous) : 어떠한 작업이 동시에 일어나지 않는 것. 각 작업들이 서로 독립적으로 진행된다. 이러한 독립적인 특성 덕분에 굳이 이전의 작업이 끝나기를 기다려야만 다음 작업을 시작할 수 있는 건 아니다.

⇒ 동기는 설계가 간단하나, 이전의 작업의 결과물이 나오지 않는다면 그 이후의 작업을 진행할 수 없다는 단점이 존재한다. 비동기는 설계가 복잡하지만 이전 작업의 결과가 나오지 않더라도 그 동안에 다른 작업들을 병렬적으로 수행할 수 있다는 장점이 있다. 

동시성은 어떤 프로그램이 빠르게 동작하기 위해서 알아야 하는 과정이다. 만약 어떤 웹사이트를 방문했는데 로딩이 너무 길어진다면? 이용자는 불편해할 것이다. 

그러나 동시성을 가지는 프로그램 설계는 복잡하고 어려운 일이다. 특히, 서로 독립적인 작업을 수행하는 것들을 하나로 모아 서로 소통하며 작동되게 하는 것이 어렵다. 

이번 장에서는 이러한 동시성을 가지는 프로그램 설계를 조금이나마 쉽게 해주고, 여러 작업들을 관리해줄 수 있는 여러 메서드들을 살펴볼 것이다. 

# Queue

큐는 마치 리스트와 같은 구조를 띠고 있다. 무언가가 한 쪽 끝으로부터 추가되었다면 반대쪽에서 그것을 꺼낼 수 있는 구조이다. 이러한 특성을 FIFO (first in, fisrt out)이라고 하며, 일종의 양쪽 끝이 뚫려 있는 파이프 같은 것이다. 

일반적으로 큐는 그 어떤 종류의 정보도 될 수 있는 일종의 메세지를 전송한다. 이 중 분산 작업 관리 (distributed task management)와 관련된 큐를 work queues, job queues, task queues 라고 부른다. 

예를 들어, 설거지를 한다고 치자. 설거지를 하는 과정으로 크게 접시를 물에 씻기는 과정, 말리는 과정, 원래 자리에 놓는 과정 이렇게 3가지로 구분한다고 치자. 설거지를 하는 방법은 여러 가지가 있을 것이다. 한 사람이 이 세 과정을 한 꺼번에 할 수도 있고, 또는 (정말로 설거지할 거리가 너무 많다면) 접시를 물에 씻기는 사람, 말리는 사람, 원래 자리에 놓는 사람 이렇게 여러 명이서 분업해서 하는 방식이 있을 것이다. 후자의 경우, 만약 접시를 씻기는 사람의 작업 속도가 말리는 사람의 작업 속도보다 빨라서 말리는 사람이 작업을 다 끝낼 때까지 기다린다면 이것은 동기라고 볼 수 있을 것이다. 왜냐하면 다음 사람의 작업이 끝날 때까지 나는 아무것도 할 수도 없이 그저 기다려야만 하니깐. 그러나 다음 사람의 작업 속도와 나의 작업 속도 간 상대적 차이에 관계없이 내 할일 끝나면 다 된 접시를 나와 다음 작업자 사이의 빈 공간에 쌓아놓는다면 이것은 비동기적 특성을 띤다고 볼 수 있다. 다음 작업자가 자신의 일을 끝내건 말건 나는 내 할일을 계속 독립적으로 할 수 있으니깐. 이렇게 나와 다음 작업자 사이의 공간에 접시를 어떻게 쌓고 다음 작업자가 어떤 방식으로 접시를 가져가서 작업할 것인가에 대한 접근 방식 중 하나가 큐라고 볼 수 있겠다. 만약 “먼저 들어온 접시를 먼저 말린다”라는 방식을 취하면 그건 큐, “맨 마지막에 온 접시를 먼저 말리겠다”라는 방식을 취하면 그건 스택(stack)인 것이다. 

이렇듯, 큐는 여러 작업들 사이에서 데이터와 같은 어떠한 정보를 어떻게 교환할 것인가에 대한 여러 방식 중 하나라고 보면 되겠다. 

# 프로세스

큐를 실행하는 방법에는 여러가지가 있다. 그 중에는 앞서 다른 장에서 살펴보았던 multiprocessing 모듈에 있는 Queue 함수를 사용하는 것이다. 

다음 예제에서는 접시를 씻기는 사람과 접시를 말리는 사람이 협력하는 과정을 묘사한다. 

```python
import multiprocessing as mp

def washer(dishes, output):
    for dish in dishes:
        print('{} 접시를 씻겼습니다.'.format(dish))
        output.put(dish)

def dryer(input_):
    while True:
        dish = input_.get()
        print('{} 접시를 말렸습니다.'.format(dish))
        input_.task_done()

if __name__ == '__main__':
    dish_queue = mp.JoinableQueue()
    dryer_proc = mp.Process(target=dryer, args=(dish_queue,))
    dryer_proc.daemon = True
    dryer_proc.start()

    dishes = ['샐러드', '밥', '반찬', '과일']
    washer(dishes, dish_queue)
    dish_queue.join()  # 이전 프로세스에게 다음 프로세스에서의 일이 모두 끝났음을 알려주는 역할인 듯.
```

위 파일을 dishes.py로 저장한 뒤 터미널 창에서 python dishes.py라고 치고 실행시키면 다음의 결과를 얻는다.

```python
샐러드 접시를 씻겼습니다.
밥 접시를 씻겼습니다.
반찬 접시를 씻겼습니다.
과일 접시를 씻겼습니다.
샐러드 접시를 말렸습니다.
밥 접시를 말렸습니다.
반찬 접시를 말렸습니다.
과일 접시를 말렸습니다.
```

결과만 봐서는 하나의 iterator를 for문을 통해 출력시킨 것만 같다. 그러나 사실은 서로 큐라는 방식을 통해 소통할 수 있는 서로 분리된 두 프로세스를 실행시킨 결과이다. 

(이것 말고도 다른 큐 방식들도 존재한다고 한다)

# 쓰레드 (Thread)

쓰레드는 프로세스 안에서 작동되는데, 프로세스 내에 어떠한 것에도 접근할 수 있다. 쓰레드는 프로세스 내에서 실행되는 흐름의 단위이다. 멀티프로세스와 멀티 스레드는 모두 여러 흐름이 동시에 진행된다는 공통점을 가지고 있지만 프로세스들은 서로 독립적이고 별개의 메모리를 차지하고 있는 반면, 멀티 스레드는 하나의 프로세스 안에 존재하는 메모리를 공유해서 사용할 수 있다. 

이전에 [multiprocessing](/python/program-process-(2)/) 모듈을 다룰 때 나왔던 예제를 다시 꺼내보자.

```python
import multiprocessing
import os

def sub_func(thing):
    main_func(thing)

def main_func(thing):
    print("Process {} says: {}".format(os.getpid(), thing))

if __name__ == '__main__':
    main_func("메인 프로그램")
    for i in range(4):
        p = multiprocessing.Process(target=sub_func, args=("서브 프로그램 {}".format(i),))
        p.start()
```

위 예제에서는 서로 독립적인 4개의 프로세스를 형성한 예제이다. 이것을 하나의 프로세스 안의 여러 개의 쓰레드를 사용하는 형식으로 바꿔보겠다.

```python
import threading

def sub_func(thing):
    main_func(thing)

def main_func(thing):
    print("Process {} says: {}".format(threading.current_thread(), thing))

if __name__ == '__main__':
    main_func("메인 프로그램")
    for i in range(4):
        th = threading.Thread(target=sub_func, args=("서브 프로그램 {}".format(i),))
        th.start()
```

```python
Process <_MainThread(MainThread, started 9780)> says: 메인 프로그램
Process <Thread(Thread-1 (sub_func), started 9660)> says: 서브 프로그램 0
Process <Thread(Thread-2 (sub_func), started 7508)> says: 서브 프로그램 1
Process <Thread(Thread-3 (sub_func), started 12344)> says: 서브 프로그램 2
Process <Thread(Thread-4 (sub_func), started 5600)> says: 서브 프로그램 3
```

앞서 살펴보았던 설거지 예제는 쓰레드를 이용하여 다음과 같이 바꿀 수 있을 것이다.

```python
import threading, queue

def washer(dishes, output):
    for dish in dishes:
        print('{} 접시를 씻겼습니다.'.format(dish))
        output.put(dish)

def dryer(input_):
    while True:
        dish = input_.get()
        print('{} 접시를 말렸습니다.'.format(dish))
        input_.task_done()

if __name__ == '__main__':
    dish_queue = queue.Queue()
    for n in range(2):
        dryer_thread = threading.Thread(target=dryer, args=(dish_queue,))
        dryer_thread.start()

    dishes = ['샐러드', '밥', '반찬', '과일']
    washer(dishes, dish_queue)
    dish_queue.join()  # 이전 프로세스에게 다음 프로세스에서의 일이 모두 끝났음을 알려주는 역할인 듯.
```

(그런데 프로세스와는 달리 위 예제는 실행하면 멈추질 않는다)

multiprocessing과는 달리 threading에는 terminate()라는 함수가 없다. 이는 쓰레드는 잘못 사용하면 어떠한 종류이든 문제를 일으킬 소지가 있기 때문이다. 쓰레드나 C, C++처럼 메모리 관리를 수동으로 직접 하는 언어의 경우, 찾기 어려운 버그를 일으킬 가능성이 있다. 따라서 쓰레드를 사용할 때에는 코드가 쓰레드 안전(thread-safe)해야 한다. 여기서 쓰레드 안전이란, 코드 내 어떤 함수나 객체, 변수 등을 여러 쓰레드가 가져다 쓰더라도 오류없이 올바른 결과를 내는 것을 말한다. 쓰레드 안전을 지키기 위해서, 그 어떤 쓰레드도 코드 내의 변수나, 함수 등의 값을 읽기만 해야 할 뿐 그것을 변경해서는 안된다. 이러한 관점에서 전역 변수가 가장 위험하다. 바로 위의 예제의 경우, 전역 변수를 여러 쓰레드가 공유하는 경우가 없었기 때문에 어떠한 오류 없이 무사히 독립적으로 실행될 수 있었던 것이다. 그렇지만, 여러 개의 쓰레드를 사용하는 목적 중 하나는 큰 작업을 여러 개로 쪼개서 그에 해당하는 데이터들을 다루고 서로 공유해야하기 때문에 쓰레드에 의한 데이터 값의 변형은 어느 정도는 어쩔 수 없이 일어난다. 

I/O bound는 프로세스가 진행 시 CPU 사용 기간보다 I/O 작업 시간이 더 많은 경우를 의미한다. I/O의 예로는 디스크 작업, 파일 쓰기, 네트워크 통신, 크롤링, DB 데이터 송수신 등이 있다. 다른 시스템과 통신할 때 나타나는 병목현상이 어느정도로 나느냐에 따라 작업 속도가 결정된다. 

반면 CPU bound는 프로세스가 진행 시 CPU 사용 기간이 I/O 작업에 의해 걸리는 시간보다 더 많은 경우를 의미한다. CPU는 주로 연산 작업을 하므로, 머신러닝, 과학적, 그래픽 작업 등을 할 때 발생한다. CPU bound에 의한 작업 속도는 CPU 성능에 의해 결정된다.

쓰레드는 I/O 작업이 완전히 수행되는데에 걸리는 시간을 절약하고자 할 때 유용하다. I/O의 경우 각각의 데이터들이 서로 분리된 변수로 존재하므로 쓰레드를 사용하기에 적합하다. 

쓰레드간에 데이터를 안전하게 공유하는 일반적인 방법은 한 쓰레드 안의 변수를 바꾸기 전에 소프트웨어 락(lock)을 거는 것이다. 즉, 쓰레드를 방에 비유하면, 여러 개의 방 중 특정 변수가 들어 있는 특정 방의 문만 걸어잠그는 것이다. 이를 통해 그 안에서 변수가 변경되더라도 다른 쓰레드가 접근하는 것을 막을 수 있다. 이 방법을 사용할 때 한 가지 유념해야할 점은, 작업이 다 끝났으면 언락(unlock)해야 하는 것이다. 그래야 다른 쓰레드들과 데이터 통신을 할 수 있으니깐. 그러나 락 자체가 전통적인 방법이기는 하지만 사용하기에 매우 복잡한 편이다. 

그래서 결론적으로, 쓰레드와 프로세스를 어떨 때 써야하는지는 다음과 같다.

- I/O 관련 작업에는 쓰레드를 사용한다.
- event나 네트워킹 같은 CPU 관련 작업에는 프로세스를 사용한다.

> 파이썬에서는 쓰레드가 CPU bound 작업에 있어서 속도를 내주는 역할을 할 수 없다. 그것은 Global Interpreter Lock(GIL)이라고 하는 표준 파이썬 시스템에 내재된 lock 때문이다. 이 개념은 파이썬 인터프리터에서의 쓰레드 문제를 피하기 위해 만들어졌으며, 때문에 싱글 쓰레드보다 작업 속도가 더 느릴 수도 있다.
> 

# Green threads and gevent

앞서 보았듯, 프로그램 실행에 있어서 느린 부분을 피하기 위해 쓰레드나 프로세스를 이용하였다. Apache 웹 서버가 이를 이용하는 대표적 예이다.

그런데, 해당 방법들 말고도 프로그램 실행을 빠르게 해주는 다른 방법이 있다. 바로 이벤트 기반 프로그래밍(event-based programming)이다. 이벤트 기반 프로그램은 event loop를 실행하여 적은 양의 임무를 주고, 해당 루프를 반복하는 방식이다. nginx 웹 서버가 대표적 예이며, 전반적으로 Apache보다 빠르다.

gevent 라이브러리가 이벤트 기반 프로그래밍을 해주는 도구이다. 그저 일반적으로 코드를 짜도 해당 라이브러리는 이를 동시 실행(coroutine)해준다. 해당 라이브러리는 파이썬 코드에서만 작동하며, C언어 같은 다른 언어로 쓰인 것에는 적용되지 않는다. 

해당 라이브러리는 파이썬 표준 라이브러리가 아니기에 pip install gevent로 다운받자. 

socket이라는 모듈에는 gethostbyname()이라는 함수가 있다. 해당 함수는 서버 이름을 쫓아 해당 서버의 주소를 찾아주는 함수이다. 해당 함수는 동기적이라, 여러 사이트를 찾고자 할 때에는 한 번에 한 사이트만 찾을 수 있다. 그러나 gevent에서는 한 번에 독립적으로 여러 개의 사이트를 찾을 수 있게 한다. 다음이 그 예이다.

```python
import gevent
from gevent import socket

hosts = ['www.crappytaxidermy.com', 'www.walterpottertaxidermy.com',
         'www.antique-taxidermy.com']
jobs = [gevent.spawn(gevent.socket.gethostbyname, host) for host in hosts]
gevent.joinall(jobs, timeout=5)
for job in jobs:
    print(job.value)
```

해당 파일을 터미널에서 실행하면 다음과 같은 결과를 얻는다.

```python
66.6.44.4
144.217.51.126
None
```

위 예제를 살펴보면, 각각의 hostname이 gethostbyname()으로 들어가는데, 이는 gevent 버전이므로 비동기적으로 실행된다. 

gevent.spawn() 함수는 greenlet (green thread, micro-thread라고도 불림) 이라고 하는 것을 생성하여 각각의 gevent.socket.gethostbyname(url)을 실행시킨다. 

일반적인 쓰레드와 다른 점은 쓰레드를 블록(block)하지 않는다는 것이다. 여기서 block이란, 어떤 함수를 호출했을 때 그 작업을 모두 완수할 때까지 기다렸다가 결과값이 return되는 것을 의미한다. 논블록(non-block)은 어떤 함수를 호출했을 때 어떤 작업들을 요청했을 때 바로 return되는 것을 말한다. 즉, greenlet은 만약 어떤 일반 쓰레드가 블록되었다면, gevent가 다른 greenlet으로 작업을 이전하기 때문에 결과적으로 블로킹 당하는 일이 없게 된다.

gevent.joinall() 메서드는 모든 spawn된 작업들이 다 끝날 때까지 기다린다. 

gevent의 socket을 쓰는 대신 monkey-patch 함수라는 것을 쓸 수 있다. 이것은 gevent 버전으로 쓰인 파이썬 표준 라이브러리를 사용하는 대신에 표준 모듈들을 모두 greenlet으로 바꿔주는 역할을 한다. 해당 함수는 gevent가 모든 코드에 적용되기를 원할 때 유용하게 쓰일 수 있다. 

사용 방법은 다음과 같다. 먼저 일반 socket이 호출될 때마다 gevent 버전의 socket으로 호출되기 원한다면 코드 맨 위에 다음처럼 작성할 수 있다.

```python
from gevent import monkey
monkey.patch_socket()
```

위 코드는 현재 코드 파일 뿐만 아니라 import되는 표준 라이브러리에서 조차도 적용된다. 

좀 더 다양한 표준 모듈에도 적용하고 싶다면 다음처럼 쓰면 된다.

```python
from gevent import monkey
monkey.patch_all()
```

gevent로 프로그램 실행을 최대한 빨리 하고자 한다면 위 코드를 쓰면 되겠다. 

monkey-patch를 예제 2-1에 적용하면 다음처럼 된다. 실행 결과는 똑같으므로 생략하겠다.

```python
import gevent
from gevent import monkey; monkey.patch_all()
import socket

hosts = [
    'www.crappytaxidermy.com',
    'www.walterpottertaxidermy.com',
    'www.antique-taxidermy.com'
]
jobs = [gevent.spawn(socket.gethostbyname, host) for host in hosts]
gevent.joinall(jobs, timeout=5)
for job in jobs:
    print(job.value)
```

gevent 사용할 때도 고려해야할 점들이 있다. 모든 이벤트 기반 시스템이 그러하듯, 실행되는 각 코드들도 실행 속도가 빨라야 한다. 논블로킹이라는 특징을 가진다고 하더라도 수많은 작업을 실행하는 코드들은 여전히 느리다. 

> 참고로, 이벤트 기반 프레임워크가 몇 가지 있는데, 그 중 tornado와 gunicorn이 있다. 해당 프레임워크들은 low-level 이벤트 처리 기법과 빠른 웹 서버를 제공한다. Apache와 같은 전통적인 웹 서버를 다루지 않고도 빠른 웹사이트를 만들기 원한다면 해당 프레임워크를 고려해보는 것도 좋다.
> 

- 데이터를 받는다든가, 연결을 종료시킨다든가 하는 이벤트에 함수들을 연결시킨 후, 해당 이벤트가 발생하면 그에 연결된 함수들이 실행된다. 이를 callback 디자인이라고 한다.

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [동기 ( synchronous ) vs 비동기 ( asynchronous )](https://velog.io/@hyeonyohwan/동기-synchronous-vs-비동기-asynchronous)

[3] [동기와 비동기의 개념과 차이](https://private.tistory.com/24)

[4] [[OS] Thread Safe란?](https://gompangs.tistory.com/entry/OS-Thread-Safe란)

[5] [[분산 시스템] 2-3. CPU Bound & I/O Bound](https://velog.io/@carrykim/분산-시스템-2-3.-CPU-Bound-IO-Bound)

[6] [[OS] CPU Bound vs I/O Bound](https://kkongchii.tistory.com/entry/OS-CPU-Bound-vs-IO-Bound)