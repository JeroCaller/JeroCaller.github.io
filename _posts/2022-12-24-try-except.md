---
layout: single
title:  "[Python-basic] try-except문으로 예외 처리"
categories: "Python"
tag: ["Python", "basic"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 예외 처리

파이썬에서는 에러가 발생할 시 그와 관련된 코드를 실행하는 ‘예외 (exception)’이란 것이 존재한다. 에러가 날 수도 있는 코드를 실행 시 적절하게 예외 처리 (exception handling)를 하여 사전에 방지할 필요가 있다. 에러를 방지하지 못한다면 적어도 어디서 에러가 나는지라도 알 수 있는 게 좋다. 

예외 처리자(exception handler)를 사용하지 않으면 파이썬에서 에러 메세지와 함께 에러가 난 곳을 알려주고 프로그램을 종료한다. 이런 방식이 아닌 다른 방식으로 에러를 처리하고자 한다면 try-except문을 사용하면 된다.

```python
sample_list = ['a', 'b', 'c']
index = 10
print(sample_list[index])
```

```python
Exception has occurred: IndexError
list index out of range
  File "C:\python2\python_basic\study.py", line 3, in <module>
    print(sample_list[index])
```

위 예제에서 list 크기보다 더 큰 인덱스를 대입하여 IndexError에 대한 오류 메시지가 뜬 상태이다. 

```python
sample_list = ['a', 'b', 'c']
index = 10
try:
    sample_list[index]
except:
    print('인덱스 범위는 0부터 {} 사이여야 하지만 현재 {}이(가) 입력되어 오류가 발생하였습니다.'.format(len(sample_list)-1, index))
```

```python
인덱스 범위는 0부터 2 사이여야 하지만 현재 10이(가) 입력되어 오류가 발생하였습니다.
```

try-except문을 이용하면 예제1-2와 같이 원하는 방식대로 오류 처리를 할 수 있음을 알 수 있다. 

try-except문 사용법은 다음과 같다.

```python
try:
    오류가 날 수도 있을 거라 의심되는 코드
except:
    오류 발생 시 실행할 코드
```

```python
try:
    오류가 날 수도 있을 거라 의심되는 코드
except 예외종류 as 오류변수:
    오류 발생 시 실행할 코드
```

```python
try:
    오류가 날 수도 있을 거라 의심되는 코드
except 예외종류:
    오류 발생 시 실행할 코드
```

위 사용법을 보면, 실행할 코드를 try 다음에 들여쓰기하여 쓴다. 그리고 except문에는 try 코드에서 오류 발생 시 실행할 코드를 입력한다. 코드 실행 시 try 안의 코드가 실행되고 오류가 나면 except문이 실행되고, 오류가 없다면 except문은 실행되지 않고 넘어간다. 

사용법2에서는 오류에도 다양한 종류의 오류가 있기 때문에 특정 오류를 처리하기 위해 오류의 종류와 이 오류를 저장할 이름을 따로 지정하고 있다. 이해를 돕기 위해 다음의 예제를 보면 된다.

```python
sample_list = [1, 2, 3, 4, 5]
for i in range(2):
    try:
        if i == 0:
            print(sample_list[10])
        else:
            some_num = sample_list[2]
            print(some_num / 0)
    except IndexError as index_err:
        print('인덱스 오류: ', index_err)
    except ZeroDivisionError as zero_err:
        print('0으로 나누는 에러: ', zero_err)
    except Exception as other_err:
        print('다른 종류의 에러 발생: ', other_err)
```

```python
인덱스 오류:  list index out of range
0으로 나누는 에러:  division by zero
```

예제 2를 보면 except 문에서 IndexError, ZeroDivisionError과 같은 다양한 종류의 에러 메시지를 각각 index_err, zero_err 변수에 저장하여 이를 출력하는 모습이다. 한 편, 예제 2의 except문을 보면 알 수 있듯, except문을 여러 번 쓸 수 있다. 

한 편 마지막 except문의 Exception은 모든 종류의 오류를 포함할 수 있다. 

## finally, else와 같이 쓰기

try문에는 finally, else 키워드도 함께 쓸 수 있다. 

finally문은 try문 실행 시 오류 발생 여부에 상관없이 반드시 실행된다. 

```python
try:
    실행할 코드
finally:
    오류 발생 여부 상관없이 실행할 코드
```

```python
sample_list = [1, 2, 3, 4, 5]
try:
    print(sample_list[10])
except IndexError as index_err:
    print(index_err)
finally:
    print('해당 리스트 내 모든 숫자의 합:', sum(sample_list))

print('--------')
try:
    print(sample_list[0])
finally:
    print('해당 리스트 마지막 요소를 2로 나누면:', sample_list[-1] / 2)
```

```python
list index out of range
해당 리스트 내 모든 숫자의 합: 15
--------
1
해당 리스트 마지막 요소를 2로 나누면: 2.5
```

else문을 쓰면 try문 실행 중 오류 발생 시 except문이 실행되고, 오류가 발생하지 않으면 else문이 실행된다. 단, else문은 앞에 반드시 except문이 있어야 한다. 

```python
sample_list = [1, 2, 3, 4, 5]
try:
    print(sample_list[10])
except IndexError as index_err:
    print(index_err)
else:
    print('해당 리스트 내 모든 숫자의 합:', sum(sample_list))

print('--------')
try:
    print(sample_list[0])
except Exception as err:
    print(err)
else:
    print('해당 리스트 마지막 요소를 2로 나누면:', sample_list[-1] / 2)
```

```python
list index out of range
--------
1
해당 리스트 마지막 요소를 2로 나누면: 2.5
```

# 예외 직접 만들기

앞서 살펴본 IndexError와 같은 예외들은 파이썬에 이미 정의된 예외들이다. 하지만 사용자가 직접 예외를 정의하여 자신이 원하는 목적에 맞게 쓸 수 있다. 이 때 class로 새로운 객체 종류를 정의해야한다. 

```python
class UppercaseException(Exception):
    pass

eng_words = ['entity', 'failure', 'study', 'MORE']
for word in eng_words:
    if word.isupper():
        raise UppercaseException(word)
```

```python
Exception has occurred: UppercaseException
MORE
  File "C:\python2\python_basic\study.py", line 8, in <module>
    raise UppercaseException(word)
```

예제5에서, 첫 째 코드에서 UppercaseException이라는 사용자 정의 클래스를 정의하였다. 그리고 Exception 클래스를 상속하였다. 예제5의 마지막 줄에서 raise 키워드와 그 뒤에 앞서 정의했던 클래스를 호출하고 있다. 이를 통해 if 조건문 충족시 오류를 일으키도록 하고 있다. 

```python
class MyError(Exception):
    pass

def input_str(string=''):
    if type(string) is not type(str()):
        raise MyError()
    print("--- " + string + " ---")

try:
    input_str('hello')
    input_str(12)
except MyError as err:
    print(err)
```

```python
--- hello ---
```

예제6-1에서, MyError 클래스를 정의하고, raise 키워드를 이용해 특정 조건에서 MyError 예외가 일어나도록 설정하였다. 그리고 except문에서 해당 예외 발생 시 해당 예외 메세지를 err 변수를 통해 출력하도록 설정하였다. 그런데 실행결과에서는 예외 메세지가 출력되지 않고 있다. 이를 해결하려면 오류 클래스에서 __str__ 메서드를 구현해야 한다. 다음은 이를 반영하여 위 예제를 수정한 예제이다. 

```python
class MyError(Exception):
    def __str__(self):
        return 'input_str 함수에는 문자열 인자만 들어갈 수 있습니다.'

def input_str(string=''):
    if type(string) is not type(str()):
        raise MyError()
    print("--- " + string + " ---")

try:
    input_str('hello')
    input_str(12)
except MyError as err:
    print(err)
```

```python
--- hello ---
input_str 함수에는 문자열 인자만 들어갈 수 있습니다.
```

또는, MyError의 __init__() 메서드 안에서 상위 클래스인 Exception의 __init__() 메서드 인자로 에러 메시지를 대입해도 된다.

```python
class MyError(Exception):
    def __init__(self):
        super().__init__("input_str 함수에는 문자열 인자만 들어갈 수 있습니다.")

def input_str(string=''):
    if type(string) is not type(str()):
        raise MyError()
    print("--- " + string + " ---")

try:
    input_str('hello')
    input_str(12)
except MyError as err:
    print(err)
```

```python
-- hello ---
input_str 함수에는 문자열 인자만 들어갈 수 있습니다.
```

# raise 키워드

문법: raise 예외(’에러메시지’)

여기서 예외는 파이썬에서 기본 내장된 에러를 써도 되고, 사용자가 직접 정의한 예외를 써도 된다.

에러 메시지는 생략 가능하다.

## raise 처리 과정

```python
def printOnlyEvenNumber():
    x = int(input("짝수를 입력하세요: "))
    if x / 2 != 0:
        raise Exception("입력 에러: 짝수만 입력 가능.")

try:
    printOnlyEvenNumber()
except Exception as e:
    print(e)
```

```python
짝수를 입력하세요: 1
입력 에러: 짝수만 입력 가능.
```

raise로 예외를 발생시킬 때 현재 코드 블록에서 이 예외를 처리해 줄 except문이 없으면 파이썬 인터프리터는 상위 코드로 올라가 except를 찾는다. 위 예제에서는 함수 안에 except문이 없으므로 함수 바깥 부분의 except를 찾고, 찾았으면 except 구문에서의 코드대로 실행하게 된다. 위 예제에서는 에러를 그대로 출력한다. 만약 최상위 코드에서도 except문을 발견하지 못하면 그 즉시 실행을 중단시키고 해당 에러를 사용자에게 보여준다. 그래서 만약 에러가 발생해도 코드 실행 자체는 그대로 계속 하고 싶다면 try-except문을 써야 하는 것이다. 

## 예외 다시 발생시키기 (re-raise)

except 문 안에 raise를 또 사용하면 현재 예외를 다시 발생시킨다.

```python
def printOnlyEvenNumber():
    x = int(input("짝수를 입력하세요: "))
    try:
        if x / 2 != 0:
            raise Exception("홀수 에러: 해당 숫자는 짝수가 아닙니다.")
    except Exception as e:
        print('printOnlyEvenNumber 함수 내부 에러 발생! ', e)
        raise

try:
    printOnlyEvenNumber()
except Exception as e:
    print('printOnlyEvenNumber 함수 호출 도중 에러 발생! ', e)
```

```python
짝수를 입력하세요: 1
printOnlyEvenNumber 함수 내부 에러 발생!  홀수 에러: 해당 숫자는 짝수가 아닙니다.
printOnlyEvenNumber 함수 호출 도중 에러 발생!  홀수 에러: 해당 숫자는 짝수가 아닙니다.
```

위 예제에서 사용자가 홀수를 입력한 경우, try 구문 안의 raise로 인해 나타난 예외가 바로 아래에 있는 except에서 처리된다. 그런데 except 구문 안에 또 raise 구문이 있어서 예외가 발생하고, 이 예외는 이 예외를 처리해줄 except를 찾아 상위 코드를 탐색한다. 그 결과 위 코드의 맨 마지막 줄에서 except문을 찾아 이 예외를 처리하는 것이다. 

# assert

assert도 raise와 비슷한 키워드로, 조건식이 거짓일 때 AssertionError 예외를 발생시키고, 조건식이 참이면 넘어간다. 문법은 다음과 같다.

사용법 1) assert 조건식

사용법 2) assert 조건식, 에러메시지

```python
def printOnlyEvenNumber():
    x = int(input("짝수를 입력하세요: "))
    assert x / 2 == 0, "홀수 에러: 짝수를 입력해야 합니다."
    print(x)

try:
    printOnlyEvenNumber()
except Exception as e:
    print(e)
```

```python
짝수를 입력하세요: 1
홀수 에러: 짝수를 입력해야 합니다.
```

asssert는 디버깅 모드에서만 실행되는데, 파이썬은 기본적으로 디버깅 모드이다. 따라서 터미널에서 파이썬 파일을 실행할 때 assert가 실행되지 않도록 하려면 -O (영문 대문자 O)를 삽입해서 실행시키면 된다. 

```python
python -O study2.py
```

```python
짝수를 입력하세요: 1
1
```

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 예외 처리 - 점프 투 파이썬

[점프 투 파이썬](https://wikidocs.net/30)

[3]

[파이썬 코딩 도장: 38.3 예외 발생시키기](https://dojang.io/mod/page/view.php?id=2400)

[4]

[파이썬 코딩 도장: 38.4 예외 만들기](https://dojang.io/mod/page/view.php?id=2401)