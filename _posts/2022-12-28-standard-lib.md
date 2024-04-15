---
layout: single
title:  "[Python][Library] 파이썬 표준 라이브러리 (1)"
categories: "Python"
tag: ["Python", "Library"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

파이썬에는 파이썬 설치 시 포함되는 표준 라이브러리(standard library)가 존재한다. 이 표준 라이브러리는 파이썬 언어가 너무 방대해지는 것을 막기 위해 따로 라이브러리로 분류되어 있다. 표준 라이브러리에는 유용한 것들이 많기 때문에 스스로 코드를 짤 때 구현하고자 하는 것이 있다면 그걸 구현하기 전에 이미 표준 라이브러리에 있는지 확인하면 그 코드를 갖다 쓰기만 하면 되므로 시간적으로 비용을 줄일 수 있을 것이다. 표준 라이브러리에는 웹, 시스템, 데이터베이스 등 여러 종류들을 다루는 것들이 아주 많이 있다. 이 장에서는 그러한 표준 라이브러리들 중 몇 개를 소개하고자 한다.

# setdefault(), defaultdict()으로 존재하지 않는 key 다루기

dictionary에서 없는 key를 참조하면 KeyError라는 예외가 발생한다. 이 에러를 방지하기 위해 get()함수를 썼었다.

```python
periodic_table = {'Hydrogen': 1, 'Helium': 2}
print(periodic_table.get('silicon', 14))
print(periodic_table)
```

```python
14
{'Hydrogen': 1, 'Helium': 2}
```

이와 거의 똑같은 역할을 해주는 함수로 setdefault()가 존재한다.

```python
periodic_table = {'Hydrogen': 1, 'Helium': 2}
print(periodic_table.setdefault('silicon', 14))
print(periodic_table)
```

```python
14
{'Hydrogen': 1, 'Helium': 2, 'silicon': 14}
```

차이점은, get()함수는 해당 키가 없다면 None을 반환하거나 get() 함수의 두 번째 인자를 반환하지만 기존의 dictionary를 건들진 않는다. 그러나 setdefault()의 경우 해당 key가 없다면 그 key와 그에 해당하는 value를 그 dictionary에 추가한다. 

만약 dictionary안에 존재하는 key라면 setdefault() 함수의 두 번째 인자로 전해지는 객체로 다시 저장하지 않고 기존의 value를 그대로 반환한다.

```python
periodic_table = {'Hydrogen': 1, 'Helium': 2}
print(periodic_table.setdefault('Hydrogen', 12))
print(periodic_table)
```

```python
1
{'Hydrogen': 1, 'Helium': 2}
```

이와 비슷한 기능을 하는 또 다른 함수로 defaultdict() 함수가 있다. 이 함수는 dictionary안에 없는 key를 참조하려는 경우, 애초에 그 key에 해당하는 value로 디폴트 값을 미리 설정해주는 함수이다. 사용법과 예제는 다음에서 살펴볼 수 있다. 

```python
from collections import defaultdict
periodic_table = defaultdict(int)
periodic_table['Hydrogen'] = 1
print(periodic_table['Helium'])
print(periodic_table['silicon'])
```

```python
0
0
```

defaultdict 함수를 쓰려면 collections라는 모듈을 import해야 함을 알 수 있다. 위 예제의 마지막 두 줄에서는 dictionary안에 정의되지 않은 key들을 참조하고 있다. 원래라면 오류가 떠야하지만 두 번째 줄의 periodic_table = defaultdict(int)라는 선언문 덕분에 정의되지 않은 key에도 디폴트 값으로 0이 value에 저장되기에 오류가 나타나지 않고 0이 반환되는 것이다. 

defaultdict() 함수의 인자는 함수이다. 그래서 다음처럼 쓸 수도 있다. 

```python
from collections import defaultdict

def default_value_in_dict():
    return '해당 key에는 할당된 value가 없습니다.'

periodic_table = defaultdict(default_value_in_dict)
periodic_table['Hydrogen'] = 1
print(periodic_table['Hydrogen'])
print(periodic_table['Helium'])
print(periodic_table['silicon'])
```

```python
1
해당 key에는 할당된 value가 없습니다.
해당 key에는 할당된 value가 없습니다.
```

참고로 defaultdict() 함수에 건넬 인자로 int(), list(), dict() 등이 가능하며, int()는 0을, list()는 빈 list인 []을, dict()는 빈 dictionary인 {}을 반환한다. 만약 인자를 생략한다면 반환값은 None이다. 

# Counter()로 아이템 개수 세기

list와 같은 iterator 안에 저장된 요소들의 개수를 셀 때 편리하게 쓸 수 있는 Counter() 함수가 존재한다. 이 함수도 defaultdict()가 포함되어 있는 똑같은 모듈인 collectoins를 import 함으로써 사용할 수 있다. 

사용 예제는 다음과 같다.

```python
from collections import Counter

list_a = ['당근', '당근', '감자', '당근', '양파', '감자']
list_a_counter = Counter(list_a)
print(list_a_counter)
print(type(list_a_counter))
print(dict(list_a_counter))
```

```python
Counter({'당근': 3, '감자': 2, '양파': 1})
<class 'collections.Counter'>
{'당근': 3, '감자': 2, '양파': 1}
```

해당 list 내에 중복되는 요소가 몇 개인지를 파악하여 이를 요소-개수 짝으로 dictionary 형태로 묶어 반환한다. 위 예제의 실행결과를 보면 알겠지만 Counter()의 반환값 형태는 Counter 라는 객체로 되어 있다. 

Counter()에서 쓸 수 있는 함수 중 하나인 most_common() 함수는 자연수 인자만큼의 데이터를 내림차순으로 반환한다. 

```python
from collections import Counter

list_a = ['당근', '당근', '감자', '당근', '양파', '감자']
list_a_counter = Counter(list_a)
print(list_a_counter.most_common())
print(list_a_counter.most_common(1))
print(list_a_counter.most_common(2))
print(list_a_counter.most_common(3))
```

```python
[('당근', 3), ('감자', 2), ('양파', 1)]
[('당근', 3)]
[('당근', 3), ('감자', 2)]
[('당근', 3), ('감자', 2), ('양파', 1)]
```

most_common() 함수에 아무런 인자도 없이 빈 형태로 남기면 위 예제 실행결과의 첫 째 줄처럼 전체 데이터를 반환한다.

또한 Counter로 마치 set처럼 두 Counter끼리 더하거나 빼거나, 교집합 및 합집합을 구할 수 있다. 

먼저 다음의 예제를 설정한다. 

```python
from collections import Counter

list_a = ['당근', '당근', '당근', '양파']
list_a_counter = Counter(list_a)

list_b = ['양파', '양파', '감자']
list_b_counter = Counter(list_b)
```

그 뒤를 이어 다음의 코드를 쓴다.

```python
print(list_a_counter + list_b_counter)
```

```python
Counter({'당근': 3, '양파': 3, '감자': 1})
```

덧셈기호(+)를 쓴 경우, 두 Counter중 하나에만 존재하는 요소는 그대로 포함되고, 두 Counter에 공통적으로 존재하는 (위 예제에서 ‘양파’) 요소는 서로 더해 나온 합을 포함시킨다. 

마찬가지로 뺄셈기호(-)도 쓸 수 있다.

```python
print(list_a_counter - list_b_counter)
print(list_b_counter - list_a_counter)
```

```python
Counter({'당근': 3})
Counter({'양파': 1, '감자': 1})
```

위 예제의 첫 째 줄의 경우, list_a에는 양파가 1개이고, list_b에는 양파가 2개이므로 list_a - list_b에서 양파는 -1이라는 음수가 나온다. 이 음수는 결과값에 포함되지 않는다. 마찬가지로 list_a에는 감자가 0개이고, list_b에는 감자가 1개이므로 이를 빼면 음수라서 포함되지 않음을 위 실행결과를 통해서 알 수 있다. 반대로 list_b에는 당근이 0개이고 list_a에는 당근이 3개이므로 list_b - list_a에서 -3으로 음수가 되므로 결과값에는 당근 자체가 없음을 알 수 있다. 

다음으로 교집합 기호 &가 있다.

```python
print(list_a_counter & list_b_counter)
```

```python
Counter({'양파': 1})
```

두 list에는 양파가 공통적으로 존재하는데, list_a에는 2개, list_b에는 1개가 존재한다. 이 경우 가장 낮은 값이 반환된다. 

또한 합집합 기호 | 도 존재한다. 

 

```python
print(list_a_counter | list_b_counter)
```

```python
Counter({'당근': 3, '양파': 2, '감자': 1})
```

앞서 덧셈기호 사용 때와는 달리, 기호 |를 쓸 때는 두 Counter 에 공통으로 존재하는 요소에 대해서는 서로 개수를 더하지 않고 그 중 가장 큰 개수를 반환한다. 

# deque로 stack과 queue 다루기

deque는 양쪽에서 쓸 수 있는 queue이며, stack과 queue를 모두 포함한다. 시퀀스의 양쪽 끝 중 어느 한 부분에서 요소를 더하거나 삭제하고자 할 때 유용하다. 

다음의 예시는 주어진 영문이 회문인지를 판별하는 코드이다.

```python
def palindrome(word):
    from collections import deque
    dq = deque(word)
    while len(dq) > 1:
        if dq.popleft() != dq.pop():
            return False
    return True

print(palindrome('racecar'))
print(palindrome('apple'))
```

```python
True
False
```

popleft() 함수는 deque에서 가장 왼쪽에 있는 아이템을 꺼내 반환한 뒤 deque로부터 그 아이템을 삭제한다. pop()은 가장 오른쪽에서 실행한다. 이를 이용해 위 예제에서는 주어진 영문 시퀀스를 popleft()와 pop()을 이용해 하나씩 가장 왼쪽에 있는 글자와 오른쪽에 있는 글자를 가져와 이 둘이 서로 똑같은지를 판별한다. racecar를 예로 들면 처음에는 가장 오른쪽과 왼쪽에 있는 글자인 ‘r’을 꺼내고 둘이 서로 같은지 비교한다. 같으면 그 다음으로 ‘a’라는 글자에 대해서도 같은 기능을 수행한다. 이로써 끝까지 같으면 주어진 영문이 회문임을 판별해주는 원리이다. 

# itertools로 iterate하기

itertools 모듈은 특정 목적의 반복자 함수를 보유하고 있다. 

## itertools.chain()

chain()는 인자로 들어오는 여러 개의 iterable (반복 가능한) 객체들을 마치 하나의 iterable인 것 마냥 실행시킨다.

```python
import itertools

for item in itertools.chain([1, 2], ['a', 'b'], [3, 4]):
    print(item)
```

```python
1
2
a
b
3
4
```

## itertools.cycle()

cycle()은 무한 반복자이다. 반복자를 하나의 인자로 넣으면 그것을 무한 반복시켜준다.

```python
import itertools

for item in itertools.cycle([1, 2, 3]):
    print(item)
```

```python
1
2
3
1
2
3
.
.
.
(생략)
```

## itertools.accumulate()

accumulate()는 인자로 들어온 반복자 내 요소들에 대해 누적된 값을 계산한다. 디폴트로 덧셈을 실행한다.

```python
import itertools

for item in itertools.accumulate([1, 2, 3, 4]):
    print(item)
```

```python
1
3
6
10
```

accumulate()의 두 번째 인자로 함수를 대입할수도 있다. 그러면 덧셈 대신 함수를 작동시킨다. 이 때 함수는 2개의 인자만을 취해야 하며 반환값은 하나의 값이어야 한다. 다음 예제는 곱셈 연산을 하는 함수를 정의하여 이를 accumulate()에 넘겨 실행하는 모습이다.

```python
import itertools

def multiply(a, b):
    return a * b

for item in itertools.accumulate([1, 2, 3, 4], multiply):
    print(item)
```

```python
1
2
6
24
```

## itertools.zip_longest()

```python
itertools.zip_longest(*iterables, fillvalue=None)
```

해당 함수는 두 반복자의 요소들을 서로 묶는 zip()과 같은 기능을 수행한다. 하지만 차이점은 zip_longest()는 두 반복자의 길이가 다를 경우, 남는 반복자의 요소들에게 fillvalue에 설정한 값으로 대체하여 넣을 수 있다는 것이다. 

기존의 zip()을 활용하면 다음과 같았다.

```python
rpg_characters = ['힐러', '딜러', '탱커', '궁수', '검사']
initial_money = [1000, 2000, 3000]

status = zip(rpg_characters, initial_money)
print(dict(status))
```

```python
{'힐러': 1000, '딜러': 2000, '탱커': 3000}
```

itertools.zip_longest()를 사용하면 다음과 같다.

```python
import itertools

rpg_characters = ['힐러', '딜러', '탱커', '궁수', '검사']
initial_money = [1000, 2000, 3000]

status = itertools.zip_longest(rpg_characters, initial_money, fillvalue=2000)
print(dict(status))
```

```python
{'힐러': 1000, '딜러': 2000, '탱커': 3000, '궁수': 2000, '검사': 2000}
```

더 긴 반복자의 요소 중 짝 짓지 못하고 남은 요소들은 자동으로 fillvalue에 저장된 값을 받는다는 것을 알 수 있다. 

## itertools.permutations()

```python
itertools.permutations(iterable, r)
```

해당 함수는 반복 가능 객체 내 전체 요소 중 r개를 선택한 순열을 반복자로 반환하는 함수다. 

예를 들어, 1에서 6까지 적힌 카드 6장 중 순서를 고려하여 2장을 뽑아 만들 수 있는 2자리 수를 모두 구하고자 한다. 그러면 다음의 예제처럼 해당 함수를 활용하여 이를 구현할 수 있을 것이다.

```python
import itertools

all_cases = list(itertools.permutations([1, 2, 3, 4, 5, 6], 2))
print(all_cases)
print(len(all_cases))
```

```python
[(1, 2), (1, 3), (1, 4), (1, 5), (1, 6), (2, 1), (2, 3), (2, 4), (2, 5), (2, 6), (3, 1), (3, 2), (3, 4), (3, 5), (3, 6), (4, 1), (4, 2), (4, 3), (4, 5), (4, 6), (5, 1), (5, 2), (5, 3), (5, 4), (5, 6), (6, 1), (6, 2), (6, 3), (6, 4), (6, 5)]
30
```

반면, 순서 상관없이 2장을 고르는 조합은 itertools.combinations() 함수를 이용하면 된다.

## itertools.combinations()

```python
itertools.combinations(iterable, r)
```

해당 함수는 반복 가능 객체 중에서 r개를 선택한 조합을 반복자로 반환하는 함수다. 

앞서 본 예제 4-6을 순열이 아닌 조합 문제로 바꿔 풀면 다음과 같다.

```python
import itertools

all_cases = list(itertools.combinations([1, 2, 3, 4, 5, 6], 2))
print(all_cases)
print(len(all_cases))
```

```python
[(1, 2), (1, 3), (1, 4), (1, 5), (1, 6), (2, 3), (2, 4), (2, 5), (2, 6), (3, 4), (3, 5), (3, 6), (4, 5), (4, 6), (5, 6)]
15
```

만약 숫자를 중복하여 뽑는 것을 허용한다면 itertools.combinations_with_replacement()를 사용하면 된다. 

```python
import itertools

all_cases = list(itertools.combinations_with_replacement([1, 2, 3, 4, 5, 6], 2))
print(all_cases)
print(len(all_cases))
```

```python
[(1, 1), (1, 2), (1, 3), (1, 4), (1, 5), (1, 6), (2, 2), (2, 3), (2, 4), (2, 5), (2, 6), (3, 3), (3, 4), (3, 5), (3, 6), (4, 4), (4, 5), (4, 6), (5, 5), (5, 6), (6, 6)]        
21
```

# pprint()로 깔끔하게 출력하기

여태까지 무언가를 출력할 때 print() 함수를 사용하였는데, 때로는 이쁘게 출력되지 않을 때도 있다. 이럴 때 pprint()를 쓰면 좋다. 

```python
from pprint import pprint

kor_eng_dict = {
    'feasibility': '실행 가능성',
    'entitle': '제목을 짓다',
    'lease': '임대차 계약',
    'contract': '계약'
}

print(kor_eng_dict)
print('--------------')
pprint(kor_eng_dict)
```

```python
{'feasibility': '실행 가능성', 'entitle': '제목을 짓다', 'lease': '임대차 계약', 'contract': '계약'}
--------------
{'contract': '계약',
 'entitle': '제목을 짓다',
 'feasibility': '실행 가능성',
 'lease': '임대차 계약'}
```

# OrderedDict()로 key 순으로 정렬하기

dictionary는 호출 시 key 순서가 무작위로 정렬되어 호출된다. 따라서 내가 원하는 순서대로 정렬하고자 한다면 OrderedDict() 함수를 이용하면 되겠다.

먼저 평소 dictionary 사용 시 다음과 같다. 

```python
from pprint import pprint

kor_eng_dict = {
    'feasibility': '실행 가능성',
    'entitle': '제목을 짓다',
    'lease': '임대차 계약',
    'contract': '계약'
}

pprint(kor_eng_dict)
```

```python
{'contract': '계약',
 'entitle': '제목을 짓다',
 'feasibility': '실행 가능성',
 'lease': '임대차 계약'}
```

이제 OrderedDict()를 이용하여 원하는 순서대로 key를 정렬하도록 하겠다. 이 때 OrdereedDict()의 인수로 넘길 때 (key, value)의 시퀀스 형태로 넘겨야만 한다. dictionary 형태 그대로 넘기지 말아야 할 것을 잊지마라.

```python
from pprint import pprint
from collections import OrderedDict

ordered_kor_eng_dict = OrderedDict([
    ('feasibility', '실행가능성'),
    ('entitle', '제목을 짓다'),
    ('lease', '임대차 계약'),
    ('contract', '계약')
])

pprint(ordered_kor_eng_dict)
pprint(type(ordered_kor_eng_dict))
pprint(dict(ordered_kor_eng_dict))
```

```python
OrderedDict([('feasibility', '실행가능성'),
             ('entitle', '제목을 짓다'),
             ('lease', '임대차 계약'),
             ('contract', '계약')])
<class 'collections.OrderedDict'>
{'contract': '계약',
 'entitle': '제목을 짓다',
 'feasibility': '실행가능성',
 'lease': '임대차 계약'}
```

위 예제의 실행결과를 보면 OrderedDict 객체에 dict()를 실행시키면 다시 순서가 무작위로 바뀌는 것 같다. 

# datetime.date

datetime.date는 년, 월, 일로 날짜를 표현할 때 쓰이는 함수이다. 

```python
import datetime

my_birth = datetime.date(1996, 6, 17)
current_day = datetime.date(2022, 12, 29)
print(my_birth)
print(type(my_birth))
```

```python
1996-06-17
<class 'datetime.date'>
```

두 날짜 간 차이는 - 마이너스 기호를 쓰면 된다. 날짜 간 차이를 구한 값을 대입한 변수에 .days를 하면 그 값을 일 수로 표현해준다.

```python
import datetime

my_birth = datetime.date(1996, 6, 17)
current_day = datetime.date(2022, 12, 29)
diff = current_day - my_birth
print(diff.days)
print(type(diff))
print(type(diff.days))
```

```python
9691
<class 'datetime.timedelta'>
<class 'int'>
```

활용

```python
import datetime

my_birth = datetime.date(1996, 6, 17)
current_day = datetime.date(2022, 12, 29)
diff = current_day - my_birth
print(divmod(diff.days, 365))
```

```python
(26, 201)
```

해당 날짜의 요일을 알고자 한다면 datetime.date 객체의 weekday() 또는 isoweekday()를 사용하면 된다. 이 때 각 함수의 반환값과 그에 대한 의미는 다음과 같다.

- weekday()

- isoweekday()

| 가능한 반환값 | 의미 |
| --- | --- |
| 0 | 월 |
| 1 | 화 |
| 2 | 수 |
| 3 | 목 |
| 4 | 금 |
| 5 | 토 |
| 6 | 일 |

| 가능한 반환값 | 의미 |
| --- | --- |
| 1 | 월 |
| 2 | 화 |
| 3 | 수 |
| 4 | 목 |
| 5 | 금 |
| 6 | 토 |
| 7 | 일 |

```python
import datetime

def get_week_name(date_obj):
    week_dict = {
        0: '월',
        1: '화',
        2: '수',
        3: '목',
        4: '금',
        5: '토',
        6: '일'
    }
    return week_dict[date_obj.weekday()]

current_date = datetime.date(2022, 12, 29)
print(get_week_name(current_date))
```

```python
목
```

# time

time 시간과 관련된 모듈이다. 

## time.time

time.time()은 UTC(Universal Time Coordinated 협정 세계 표준시)를 사용하여 현재 시간을 실수 형태로 리턴하는 함수이다. 1970년 1월 1일 0시 0분 0초를 기준으로 지난 시간을 초 단위로 돌려준다.

```python
import time
print(time.time())
```

```python
1672279844.3941853
```

## time.localtime

time.localtime()에 time.time()을 인자로 넣으면 time.time() 반환값을 토대로 연도, 월, 일, 시, 분, 초 등의 여러 형태로 바꿔주는 함수이다.

```python
import time

current_time = time.localtime(time.time())
print(current_time)
print(type(current_time))
```

```python
time.struct_time(tm_year=2022, tm_mon=12, tm_mday=29, tm_hour=11, tm_min=15, tm_sec=28, 
tm_wday=3, tm_yday=363, tm_isdst=0)
<class 'time.struct_time'>
```

## time.asctime

time.localtime()에 의해 반환된 튜플 형태의 값을 인자로 받아 해당 날짜 정보를 더 알아보기 쉬운 형태로 반환하는 함수이다.

```python
import time

current_time = time.localtime(time.time())
more_convenient_current_time = time.asctime(current_time)
print(more_convenient_current_time)
```

```python
Thu Dec 29 11:17:58 2022
```

## time.ctime

time.ctime()은 time.asctime()을 사용해 긴 문장을 쓸 필요없이 보기 쉽게 날짜 정보를 반환하는 함수이다. 다만 ctime은 현재 시간만을 반환한다. 

```python
import time

print(time.ctime())
```

```python
Thu Dec 29 11:20:43 2022
```

## time.strftime

strftime() 함수는 시간 정보를 사용자가 원하는 형태로 반환하도록 하는 함수이다.

```python
time.strftime('출력할 형식 포맷 코드', time.localtime(time.time())
```

이와 관련된 포맷 코드는 다음과 같다.

- 포맷코드

| 포맷코드 | 설명 | 예 |
| --- | --- | --- |
| %a | 요일 줄임말 | Mon |
| %A | 요일 | Monday |
| %b | 달 줄임말 | Jan |
| %B | 달 | January |
| %c | 날짜, 시간을 출력 | Thu Dec 29 11:25:58 2022 |
| %d | 일 (day) | 01부터 31 사이값 출력 |
| %H | 시간, 24시간 출력 형태 | [00, 23] 사이값 출력 |
| %I (대문자 i임) | 시간, 12시간 출력 형태 | [01, 12] 사이값 출력 |
| %j | 1년 중 누적 날짜 | [001,366] |
| %m | 월 (숫자로 표현) | [01,12] |
| %M | 분 | [01,59] |
| %p | 현 시간의 AM 또는 PM 출력 | AM 또는 PM을 출력 |
| %S | 초 | [00,59] |
| %U | 1년 중 누적 주-일요일을 시작으로 | [00,53] |
| %w | 숫자로 된 요일 | [0(일요일),6] |
| %W | 1년 중 누적 주-월요일을 시작으로 | [00,53] |
| %x | 현재 설정된 로케일에 기반한 날짜 출력 | 12/29/22 (월/일/년도) |
| %X | 현재 설정된 로케일에 기반한 시간 출력 | 11:32:16 (시:분:초) |
| %Y | 년도 출력 | 2022 |
| %Z | 시간대 출력 (현재 설정된 국가의 시간으로 추정됨) | 대한민국 표준시 |
| %% | % 문자 자체 출력 | % |
| %y | 년도 중 앞의 두 자리를 제외한 나머지 뒤 두 자리만 출력 | 2022 → 22 |

```python
import time

current_time = time.localtime(time.time())
strformat = '현재시간: %Y년 %m월 %d일 %p %I시 %M분 %S초'
more_convenient_time_format = time.strftime(strformat, current_time)
print(more_convenient_time_format)
```

```python
현재시간: 2022년 12월 29일 AM 11시 36분 42초
```

## time.sleep

time.sleep() 함수는 이 함수에 인자로 대입된 정수 또는 실수만큼 실행시간을 지연시키는 역할을 한다. 보통 루프 안에서 많이 사용된다고 한다. 루프 안에서 실행 시 일정한 시간 간격을 두고 루프를 실행할 수 있는 것이다.

 

```python
import time

for i in range(10):
    print(i)
    time.sleep(5)  # 5초 지연
```

위 예제를 실행시켜보면 숫자 하나하나 출력할 때까지 5초가 걸린다. 

# math

## math.gcd

math.gcd() 함수에 여러 숫자를 넣으면 그 숫자들의 최대공약수를 반환한다.

```python
import math
print(math.gcd(20, 35, 100))
```

```python
5
```

## math.lcm

math.lcm()은 최소공배수를 반환하는 함수이다.

```python
import math
print(math.lcm(3, 7))
```

```python
21
```

# random

random은 난수 즉, 규칙이 없는 임의의 수를 발생시키는 모듈이다. 

## random.random()

random.random()은 0.0 ~ 1.0 사이의 실수 중 난수 값을 돌려준다.

```python
import random

one = random.random()
two = random.random()
print(one, two)
```

```python
0.22342297394546773 0.5800910663220454
```

## random.randint()

random.randint(시작 숫자, 끝 숫자) 함수는 두 인자로 넘겨진 두 숫자 사이의 정수 중 난수 값을 반환한다.

```python
import random

start = 1
end = 45
random_integer = random.randint(start, end)
print(random_integer)
```

```python
19
```

## random.choice()

반복자를 인자로 넘기면, 반복자 중 하나의 요소를 무작위로 반환한다.

```python
import random

lotto_list = [num for num in range(1, 45+1)]
for i in range(6):
    print(random.choice(lotto_list))
```

```python
44
44
31
15
30
30
```

## random.sample()

random.sample(반복자, 추출할 요소의 수)는 반복자 내 요소를 무작위로 섞어주고, 이 결과값을 두 번째 인자로 넘긴 숫자만큼만 반환하는 함수이다.

```python
import random

number_list = [1, 2, 3, 4, 5]
shuffled = random.sample(number_list, len(number_list))
print(shuffled)
```

```python
[5, 4, 2, 3, 1]
```

# functools.reduce()

```python
import functools

functools.reduce(function, iterable)
```

해당 함수는 반복 가능한 객체의 요소들을 차례대로 왼쪽부터 오른쪽으로 누적 적용하여 하나의 값으로 줄여 반환시키는 함수이다.

다음 예제는 주어진 list 내 모든 요소를 더하여 반환시키는 예제이다.

```python
import functools

numbers = [1, 2, 3, 4, 5]
add_result = functools.reduce(lambda x, y: x + y, numbers)
print(add_result)
```

```python
15
```

먼저 처음 두 요소인 1, 2를 lambda 함수의 두 인자로 받아들인다. 그리고 이 함수 내의 코드에 따라 덧셈 연산되어 그 결과값인 3이 나온다. 그 다음으로 이 결과값과 세 번째 요소인 3이 두 인자로 대입되고 똑같이 덧셈 연산이 된다. 그러면 6이 반환될 것이다. 그 다음으로 이 누적값 6과 다음 요소인 4가 함수의 두 인자로 대입되고 연산되는 과정을 반복하게 되는 것이다. 즉, ((((1+2)+3)+4)+5)와 같은 방식이다. 

이 함수를 이용하면 리스트 내 최대, 최소값을 구할 수도 있다.

```python
import random
import functools

number_list = [random.randint(1, 100) for i in range(5)]
max_num = functools.reduce(lambda x, y: x if x > y else y, number_list)
min_num = functools.reduce(lambda x, y: x if x < y else y, number_list)
print('무작위로 뽑힌 숫자목록: ', number_list)
print('최대값:', max_num)
print('최소값:', min_num)
```

```python
무작위로 뽑힌 숫자목록:  [50, 15, 64, 16, 46]
최대값: 64
최소값: 15
```

먼저 max_num이 포함된 줄의 코드를 보자. 먼저 1번째와 2번째 요소가 lambda의 x, y로 인자로 전달된다. 그 후 두 숫자를 비교해 왼쪽에 있던 숫자가 더 크면 그 숫자를, 아니면 오른쪽 숫자를 반환한다. 위 예제에서는 50과 15가 먼저 비교되는 것이고, 50이 제일 크므로 50이 반환될 것이다. 그 다음으로 이 50이란 값과 3번째 요소인 64가 함수의 인자로 대입되어 비교 연산이 된다. 그 결과 64가 반환된다. 이 과정을 리스트의 끝까지 진행되는 것이다. min_num도 같은 원리로 진행되는 것이다. 

# operator.itemgetter()

operator.itemgetter()는 주로 sorted와 같은 함수의 key 매개변수에 적용되어 다양한 기준으로 정렬되도록 도와주는 함수이다. 

먼저 데이터가 튜플을 요소로 갖는 리스트 형식인 경우를 보자.

```python
from operator import itemgetter
from pprint import pprint

label = ['이름', '학번', '전공과목등급', '교양과목등급', '토익점수']
students = [
    ('이영일', 21, 'B', 'B', 201),
    ('나토익', 20, 'C', 'C', 970),
    ('정전공', 19, 'A', 'F', 723),
]

sort_by_id = sorted(students, key=itemgetter(1))
pprint(sort_by_id)
```

```python
[('정전공', 19, 'A', 'F', 723),
 ('나토익', 20, 'C', 'C', 970),
 ('이영일', 21, 'A', 'B', 201)]
```

itemgetter(1)은 students 요소들인 튜플의 2번째 요소를 기준으로 정렬하겠다는 뜻이다. 

이번에는 데이터가 dictionary인 경우이다.

```python
from operator import itemgetter
from pprint import pprint
from collections import OrderedDict

label = ['이름', '학번', '전공과목등급', '교양과목등급', '토익점수']
students = [
    ('이영일', 21, 'B', 'B', 201),
    ('나토익', 20, 'C', 'C', 970),
    ('정전공', 19, 'A', 'F', 723),
]
students_dict = [OrderedDict(zip(label, students[i])) for i in range(len(students))]

sorted_by = sorted(students_dict, key=itemgetter('학번'))
pprint(sorted_by)
```

```python
[OrderedDict([('이름', '정전공'),
              ('학번', 19),
              ('전공과목등급', 'A'),
              ('교양과목등급', 'F'),
              ('토익점수', 723)]),
 OrderedDict([('이름', '나토익'),
              ('학번', 20),
              ('전공과목등급', 'C'),
              ('교양과목등급', 'C'),
              ('토익점수', 970)]),
 OrderedDict([('이름', '이영일'),
              ('학번', 21),
              ('전공과목등급', 'B'),
              ('교양과목등급', 'B'),
              ('토익점수', 201)])]
```

위 예제에서는 기존의 데이터를 dictionary로 바꾸는 작업을 한 후, ‘학번’을 기준으로 정렬을 시킨 코드이다. 실행결과를 자세히보면 학번순으로 정렬되었음을 알 수 있다. 

## operator.attrgetter()

리스트 요소가 튜플이 아닌 클래스의 객체라면 attrgetter()를 적용하여 정렬해야 한다.

```python
from operator import attrgetter

class Student:
    def __init__(self, name, student_id, major_grade, liberal_arts_grade, toeic_score):
        self.name = name
        self.student_id = student_id
        self.major_grade = major_grade
        self.liberal_arts_grade = liberal_arts_grade
        self.toeic_score = toeic_score

students_list = [
    Student('이영일', 21, 'B', 'B', 201).get_data(),
    Student('나토익', 20, 'C', 'C', 970).get_data(),
    Student('정전공', 19, 'A', 'F', 723).get_data(),
]

sorted_result = sorted(students_list, key=attrgetter('student_id'))
```

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 점프 투 파이썬

[점프 투 파이썬](https://wikidocs.net/33)