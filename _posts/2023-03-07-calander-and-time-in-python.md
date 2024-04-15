---
layout: single
title:  "[Python] 캘린더와 시간"
categories: ["Python"]
tag: ["Python", "calander", "time"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# datetime 모듈

해당 모듈에는 4개의 주요 객체들이 존재한다.

- date : 년, 월, 일 모두 다룸
- time : 시, 분, 초를 다룸
- datetime : 날짜와 시간 모두 다룸
- timedelta : 날짜와 시간 간격을 다룸

date 객체의 경우, 년, 월, 일을 인자로 넘김으로써 해당 객체를 만들 수 있다. 그 다음, 년, 월, 일 각각의 값들은 해당 객체의 속성으로써 사용할 수 있다.

```python
from datetime import date

my_birth_day = date(1996, 6, 17)
print(my_birth_day)
print(type(my_birth_day))
print(my_birth_day.year)
print(my_birth_day.month)
print(my_birth_day.day)
print(my_birth_day.isoformat())
```

```python
1996-06-17
<class 'datetime.date'>
1996
6
17
1996-06-17
```

위 예제에서 맨 마지막 줄의 isoformat()은 날짜와 시간을 나타내는 국제 표준인 ISO 8601을 뜻하며, 항상 “xxxx(년도)-xx(월)-xx(일)”의 형태로 출력한다. 

date 객체는 오늘 날짜를 알려주는 today() 메서드도 제공한다.

```python
from datetime import date

now = date.today()
print(now)
print(type(now))
```

```python
2023-03-07
<class 'datetime.date'>
```

오늘로부터 며칠 뒤, 또는 며칠 전의 날짜를 출력하기 위해 timedelta를 쓸 수 있다.

```python
from datetime import date, timedelta

today = date.today()
print('today is {}'.format(today))

week = timedelta(days=7)
after_week = today + week
print(after_week)

another_day = timedelta(days=-4)
ago = today + another_day
print(ago)
```

```python
today is 2023-03-07
2023-03-14
2023-03-03
```

참고로 date에서 다룰 수 있는 날짜의 범위는 date.min(year=1, month=1, day=1) 에서부터 date.max(year=9999, month=12, day=31) 까지이다. 

이번에는 time 객체를 통해 시간을 간단하게 다뤄보자.

```python
from datetime import time

some_time = time(9, 12, 30)
print(some_time)
print(type(some_time))

print(some_time.hour)
print(some_time.minute)
print(some_time.second)
print(some_time.microsecond)
```

```python
09:12:30
<class 'datetime.time'>
9
12
30
0
```

time 객체에서도 시, 분, 초, 마이크로 초를 각각 속성으로 접근할 수 있다. 시, 분, 초, 마이크로 초 모두 디폴트는 0이다. 여기서 마이크로초는 하드웨어와 운영체제와 같은 여러 요소에 따라 그 정확성이 달라질 수 있다. 즉, 해당 마이크로초를 출력한다고 해서 그 출력 결과가 정확할 거란 보장이 없다는 뜻이다. 

datetime 객체는 날짜와 시간 모두를 담을 수 있다. 날짜와 시간 모두를 다음 예제와 같이 일일이 특정할 수도 있다.

```python
from datetime import datetime

some_day = datetime(2023, 3, 7, 9, 17)
print(some_day)
print(type(some_day))
print(some_day.isoformat())
print(some_day.isocalendar())
print(some_day.isoweekday())  # 일주일 중 오늘이 며칠인지를 숫자로 반환
# 1: 월, 7: 일
```

```python
2023-03-07 09:17:00
<class 'datetime.datetime'>
2023-03-07T09:17:00
datetime.IsoCalendarDate(year=2023, week=10, weekday=2)
2
```

또한 해당 객체는 now()라는 메서드를 제공하며, 해당 메서드는 현재 날짜와 시간 모두를 반환한다.

```python
from datetime import datetime

now = datetime.now()
print(now)
print(type(now))
print(now.year)
print(now.month)
print(now.day)
print(now.hour)
print(now.minute)
print(now.second)
print(now.microsecond)
```

```python
2023-03-07 09:21:53.286516
<class 'datetime.datetime'>
2023
3
7
9
21
53
286516
```

datetime 객체의 combine() 메서드를 이용하면 time 객체와 date 객체를 datetime 객체로 합칠 수 있다. 

```python
from datetime import datetime, time, date

noon = time(12)
today = date.today()
noon_today = datetime.combine(today, noon)
print(noon_today)
print(type(noon_today))
print(type(noon))
print(type(today))
```

```python
2023-03-07 12:00:00
<class 'datetime.datetime'>
<class 'datetime.time'>
<class 'datetime.date'>
```

반대로 날짜와 시간이 합쳐진 datetime 객체로부터 각각 날짜와 시간을 분리하여 추출하려면 datetime 객체에서 date(), time() 메서드를 이용하면 된다.

```python
from datetime import datetime, time, date

noon = time(12)
today = date.today()
noon_today = datetime.combine(today, noon)
print(noon_today)
print(noon_today.date())
print(noon_today.time())
```

```python
2023-03-07 12:00:00
2023-03-07
12:00:00
```

# time 모듈

앞서 소개한 datetime의 time 객체와 헷갈리겠지만, 파이썬에서는 독립적인 time 모듈을 따로 제공한다. 

유닉스 시간 (Unix time)은 1970년 1월 1일 자정으로부터 지금까지 몇 초가 지났는지를 표현한다. 이 값을 epoch라고도 부른다. 해당 개념은 시스템 간에 시간과 날짜를 교환하는 가장 간단한 방법으로 쓰인다고 한다. 

time 모듈의 time() 함수가 바로 현재 시간을 epoch 값으로 표현한다.

 

```python
import time

now = time.time()
print(now)
print(type(now))
```

```python
1678149059.4657943
<class 'float'>
```

epoch 값을 문자열로 바꿔주는 함수가 ctime()이다.

```python
import time

now = time.time()
print(time.ctime(now))
```

```python
import time

now = time.time()
print(time.ctime(now))
```

더 자세한 기능들은 “[파이썬 표준 라이브러리 (1)](/python/standard-lib/)”의 time 참고.

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)