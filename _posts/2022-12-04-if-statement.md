---
layout: single
title:  "[Python-basic] if 조건문"
categories: "Python"
tag: ["Python", "basic"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# if 조건문

```python
i_am_hungry = True
print('배고파?')
if i_am_hungry:
    print("그럼 밥먹어")
else:
    print("그럼 먹지마")
```

```python
배고파?
그럼 밥먹어
```

if 조건문은 조건문이 True 또는 False인지에 따라 해당 코드를 실행한다. 조건문이 True일 경우 if 아래 코드가 실행되고 (예제1의 4번째 줄), False일 경우 else 문이 (예제1 6번째 줄) 실행된다. if 또는 else 다음에는 들여쓰기 (주로 스페이스바 4번)를 하고 코드를 작성한다. 

if문 안에 if문을 제한없이 중첩하여 넣을 수 있다. 

```python
i_am_hungry = True
korean = True
snack = False
print('배고파?')

if i_am_hungry:
    if korean:
        print("한식 먹고 싶어? 그럼 밥먹어")
    else:
        print("그럼 뭐 먹을래?")
else:
    if snack:
        print("입은 심심하다고? 그럼 과자 사먹자")
    else:
        print("그럼 먹지마")
```

```python
배고파?
한식 먹고 싶어? 그럼 밥먹어
```

예제2에서 처음 나오는 if문의 조건문 i_am_hungry가 True인지 판별한다. True이므로 if 아래 코드가 실행된다. 또 다른 if문이 들어있으므로 korean이 True인지를 판별한다. True이므로 8번째 구문이 실행되는 것이다. 참고로 조건에 맞지 않는 코드들은 실행되지 않음을 위 예제를 통해서도 볼 수 있다. 

elif (else if라는 뜻) 구문을 통해 조건이 둘 이상일 경우에 사용할 수 있다.

```python
food = '피자'
print('뭐 먹을래?')

if food == '짜장면':
    print("짜장면!")
elif food == '떡볶이':
    print("떡볶이!")
elif food == '피자':
    print("피자!")
else:
    print("그러게 뭐 먹지?")
```

```python
뭐 먹을래?
피자!
```

예제3과 같이 elif도 조건이 True인 코드만 실행된다.

- 조건문에 들어갈 수 있는 비교 연산자(comparison operator)

| 의미 | 기호 |
| --- | --- |
| 동일 | == |
| 같지 않음 | != |
| 보다 적은 | < |
| 적거나 같은 | <= |
| 보다 많은 | > |
| 많거나 같은 | >= |
| 요소 포함 여부 | in |

```python
numbers = [1, 2, 3, 4, 5]

if numbers[0] == 1:
    print("같음")

if numbers[0] <= 1:
    print("적거나 같음")
    
if numbers[0] < 1:
    print("보다 적음")

if numbers[-1] != numbers[0]:
    print("불일치")

if 3 in numbers:
    print("포함되어 있음")
```

```python
같음
적거나 같음
불일치
포함되어 있음
```

예제4에선 else, elif없이 if 단독으로 쓰임을 알 수 있다. 이 경우 else, elif와는 달리 모든 if문을 거친다. 

참고로 else, elif는 if없이 단독으로 쓰일 순 없다. 

여러 조건문을 동시에 쓰고 싶다면 boolean 연산자인 and, or, not 키워드를 적절히 사용할 수 있다.

```python
x = 6
print(5 < x and x < 10)
print((5 < x) and (x < 10))
print(5 < x < 10)
print(5 < x and not x > 10)
```

```python
True
True
True
True
```

boolean 연산자는 다른 코드들에 비해 비교적 낮은 우선순위를 가지므로, 각각의 조건문이 먼저 연산이 된 다음에 boolean 연산자가 실행된다. 예제5를 보면 5 < x and x < 10에서 5 < x가 먼저 계산되어서 True가 반환되고 그 다음에 x < 10이 먼저 연산되어서 True를 반환한다. 둘 다 True이므로 True and True = True가 연산되어 반환된다. 

이러한 연산 우선순위가 헷갈리면 예제5의 3번째 줄처럼 괄호를 이용하면 된다.

또한 4번째 줄처럼 여러 비교 연산자를 한꺼번에 쓸 수 있다. 

- 비교 연산자

 A 연산자 B

and

| A | B | 결과값 |
| --- | --- | --- |
| True | True | True |
| True | False | False |
| False | True | False |
| False | False | False |

or

| A | B | 결과값 |
| --- | --- | --- |
| True | True | True |
| True | False | True |
| False | True | True |
| False | False | False |

not A

| A | 결과값 |
| --- | --- |
| True | False |
| False | True |

- True, False에 해당하는 것들

False로 인식되는 것에는 다음의 것들이 있다.

| boolean | False |
| --- | --- |
| null | None |
| zero integer | 0 |
| zero float | 0.0 |
| 빈 string | '’ |
| 빈 list | [] |
| 빈 tuple | () |
| 빈 dictionary | {} |
| 빈 set | set() |

그 외의 것들은 True로 간주된다.

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)