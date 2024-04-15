---
layout: single
title:  "[Python] 프로그램과 프로세스 (2)"
categories: ["Python"]
tag: ["Python", "program", "process", "system"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# multiprocessing 모듈

multiprocessing 모듈을 이용하면 여러 개의 서로 독립적인 프로세스들을 하나의 프로그램 내에서 실행시킬 수 있다. 

다음은 여러 개의 프로세스들을 생성하여 실행시키는 예제이다. 해당 예제를 터미널 창에서 실행시켜보자.

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

터미널 창에 다음을 입력한다

```python
python study.py
```

그러면 실행결과는 다음과 같다.

```python
Process 16520 says: 메인 프로그램
Process 10308 says: 서브 프로그램 0
Process 16452 says: 서브 프로그램 1
Process 2788 says: 서브 프로그램 2
Process 12948 says: 서브 프로그램 3
```

Process() 함수가 새로운 프로세스를 생성하여 sub_func() 함수를 실행시킨 것이다. 여기서는 for 루프문 안에 4번의 새로운 프로세스를 생성시켰으므로, sub_func() 함수를 실행하고 종료되는 프로세스를 4개 만든 것이다. 

해당 모듈은 실행 시간을 줄이기 위해 특정 일들을 여러 프로세스들에게 맡길 때 쓰인다. 해당 모듈에는 일들을 queue로 만드는 방법과, 프로세스들 간에 소통하는 방법, 그리고 모든 프로세스들이 끝날 때까지 기다리는 방법 등 여러 가지를 포함하고 있다. 

## terminate()로 프로세스 종료시키기

여러 개의 프로세스를 생성시키고 실행시키다가 어떠한 이유로 종료시키고자 할 때 terminate()를 쓰면 된다. 

다음 예제는 큰 숫자까지 1부터 차례대로 카운트하는 함수를 프로세스에서 실행시키다가 5초만에 해당 프로세스를 종료시키는 예제이다.

```python
import multiprocessing
import os
import time

def sub_func(thing):
    main_func(thing)
    start_num = 1
    stop = 100000
    for num in range(start_num, stop):
        print("\tNumber {} of {}.".format(num, stop))
        time.sleep(1)

def main_func(thing):
    print("Process {} says: {}".format(os.getpid(), thing))

if __name__ == '__main__':
    main_func("메인 프로그램")
    p = multiprocessing.Process(target=sub_func, args=("loop",))
    p.start()
    time.sleep(5)
    p.terminate()
```

```python
Process 10540 says: 메인 프로그램
Process 16692 says: loop
        Number 1 of 100000.
        Number 2 of 100000.
        Number 3 of 100000.
        Number 4 of 100000.
        Number 5 of 100000.
```

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)