---
title: "[지식/이론][python] return VS yield"
category: knowledge
tag: ["python", "knowledge", "return", "yield"]
---

# 제너레이터 (generator)

제너레이터는 이터레이터(iterator)를 생성해주는 함수이다. 이터레이터는 next() 함수를 통해 데이터에 순차적으로 접근이 가능한 객체를 말한다. 이터레이터를 구현하려면 클래스에 이터레이터 객체 그 자체를 반환하는 \_\_iter\_\_() 메서드와 반복 가능한(iterable) 객체 내의 데이터를 하나씩 차례대로 반환하는 \_\_next\_\_() 메서드를 먼저 구현해야한다. 이터레이터는 next() 함수를 통해 다음 데이터를 계속 반환할 수 있는데, 모든 값을 다 반환하면 StopIteration 예외가 발생한다. 이는 더 이상 반복할 값이 없음을 알리기 위함이다.

그런데 함수 안에 return 대신 yield 키워드를 사용하면 해당 함수는 제너레이터가 된다. 즉 yield 키워드 하나만으로도 이터레이터를 생성할 수 있다. 

return 키워드를 사용하면 함수 (또는 메서드) 호출 시 반환값을 반환하고 함수를 종료시킨다. 즉, 함수 내에서 return 키워드를 한 번이라도 만나면 값을 반환하고 함수를 종료한다. 그러나 yield 키워드를 사용하면 값을 발생시키고 이를 반환하는데, 이 때 함수를 종료시키지 않고 대기 상태로 놓고 함수 밖의 코드가 먼저 실행되도록 양보한다. 함수 밖의 코드를 수행한 후에는 다시 함수의 yield 키워드로 발생된 다음 값을 반환시킨 뒤 해당 함수는 다시 대기 상태로 돌아가는 순환을 반복한다. 즉, 함수 내에서 yield를 만난다고 값 하나만 반환하고 종료되는 것이 아니라, 현재 값을 반환하고 함수를 대기 상태에 놓아 함수 밖의 코드가 실행되게 하고, 그 후 다음 값을 반환하고 또 대기 상태에 놓는 식이다. 

```python
def some_generator(some_list: list):
    while some_list:
        yield some_list.pop(0)

a = [1, 2, 3]
gener = some_generator(a)

for value in gener:
    print(value)
```

```python
1
2
3
```

위 예제에서 for문을 보면 처음에는 yield로 인해 맨 처음 값인 1이 반환된다. 그 후 some_generater() 함수는 잠시 대기 상태로 놓이고, 해당 함수의 바깥 영역인 for문에서 값 1에 대한 처리를 한다. 여기서는 해당 값을 출력하고 있다. 

그 후, for문의 작업이 끝났으면 해당 함수에서 yield를 통해 2를 반환한다(이 때 제너레이터는 함수의 첫 부분부터 다시 시작되는 게 아니라 yield 키워드 이후 코드부터 실행한다). 그리고 해당 함수는 잠시 대기 상태에 또 놓이게 되고, for문은 값 2를 출력하는 작업을 실행하게 된다. 이렇게 계속 반복한다. 

## for문에서의 제너레이터

for문에 제너레이터를 사용할 때, for문은 먼저 제너레이터의 \_\_next\_\_() 메서드를 호출하여 해당 제너레이터의 다음 값을 가져온다. 즉, yield에서 발생시킨 값을 가져오는 것이다. 가져온 값에 대해서 for문 이하의 코드를 수행하고, 그 후에 다시 제너레이터에서 다음 값을 받아오는 식으로 반복한다. 이러한 과정은 더 이상 반복할 값이 없어 StopIteration 예외가 발생할 때까지 반복된다. 

# 제너레이터의 사용 이유

언뜻 보면 그냥 리스트 등의 시퀀스 자료형을 for문에 직접 사용해도 되는데 왜 제너레이터라는 것이 존재하는지 이해가 가지 않을 것이다. 하지만 제너레이터의 유용한 점이 있다.

- 메모리를 효율적으로 사용할 수 있다. 리스트로 여러 값들을 생성하면 모든 요소들을 저장하기 위해 그 만큼의 메모리가 할당되어 사이즈가 커질 수록 그만큼 차지하는 메모리양도 늘어난다. 그러나 제너레이터는 next() 메서드를 통해 차례로 하나씩 값에 접근할 때에만 그 값만을 메모리에 할당하기에 상대적으로 메모리 공간을 덜 차지하게 된다. 
다음은 각각 list와 generator로 똑같은 양의 값을 생성 시 각각의 자료형들이 차지하는 메모리 양을 보여주는 예제이다.
    
    ```python
    import sys
    
    print(sys.getsizeof([num for num in range(100)]))  # list comprehension
    print(sys.getsizeof((num for num in range(100))))  # generator comprehension
    ```
    
    ```python
    920
    104
    ```
    
    위 예제를 보면 list 자료형이 차지하는 메모리 공간이 제너레이터에 비해 거의 9배에 달하는 것을 확인할 수 있다. 
    
    > 두루마리 휴지 한 칸을 각각의 데이터라고 한다면, 일반적인 시퀀스는 두루마리 휴지를 길게 뽑아서 바닥에 길게 늘여놓은 것에 비유할 수 있고, 제너레이터는 한 번에 한 장씩만 뽑아쓰는 티슈와도 같다. 여러 칸의 두루마리 휴지를 바닥에 길게 늘여놓으면 그만큼의 공간을 차지하게 된다. 그러나 티슈는 한 번에 한 장씩만 뽑으므로 공간 절약이 된다.
    > 
    
- 연산 결과 값이 필요할 때까지 연산을 늦출 수 있다. (이를 Lazy evaluation, 느긋한 계산법이라 한다) 이러한 계산법은 불필요한 계산을 하지 않아 실행을 더 빨리 해준다는 장점이 있다. for문에 제너레이터가 사용되면 모든 값을 호출하는 것이 아니라 한 번에 하나씩만 호출하므로 이를 통해 lazy evaluation 기법을 사용할 수 있다고 한다.

# iter(), next()

```python
a_list = [1, 2, 3]

a_iter = iter(a_list)
print(a_iter)
print(type(a_iter))
```

```python
<list_iterator object at 0x000002560A353520>
<class 'list_iterator'>
```

iter() 함수는 시퀀스를 iterator로 바꿔주는 함수이다. 

한 편, 이터레이터는 next() 함수를 통해 값을 하나씩 차례대로 가져올 수 있다. 

```python
def my_iter(seq):
    for value in seq:
        yield value

my_g = my_iter(['a', 'b', 'c'])

a = next(my_g)
print(a)

b = next(my_g)
print(b)

c = next(my_g)
print(c)
```

```python
a
b
c
```

위 상태에서 next() 함수를 또 사용한다면 더 이상 반환시킬 값이 없으므로 StopIteration 예외가 발생한다.

```python
d = next(my_g)
print(d)
```

```python
예외가 발생했습니다. StopIteration
exception: no description
  File "C:\python\python_study\study.py", line 17, in <module>
    d = next(my_g)
StopIteration:
```

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2]

[파이썬 코딩 도장: 40.1 제너레이터와 yield 알아보기](https://dojang.io/mod/page/view.php?id=2412)

[3]

[python generator(제너레이터) 란 무엇인가](https://bluese05.tistory.com/56)

[4]

[python iterable과 iterator의 의미](https://bluese05.tistory.com/55)

[5]

[07-3 이터레이터와 제너레이터](https://wikidocs.net/184211)

[6]

[느긋한 계산법](https://ko.wikipedia.org/wiki/느긋한_계산법)