---
title: "[SQL][DB] SQL 함수"
category: "SQL"
tag: ["sql", "dbms", "db", "database", "데이터베이스", "Single Row Function", "Aggregation function"]
---

# 개요

SQL에서도 내장 함수들이 존재하여 개발자가 좀 더 복잡한 작업들을 더 편리하게 처리할 수 있게 해준다. SQL에서의 내장 함수는 너무 많아 한꺼번에 다 살펴보긴 어렵겠다. 그러나 기본적으로 사용되는 함수 몇 개만 알아도 복잡한 작업을 더 간단하게 해결할 수 있을 것이다. 이 페이지에서는 몇몇 내장 함수들에 대해 간단히 살펴보겠다. 

SQL 내장 함수에는 크개 두 가지로 나뉜다.

- Single Row Function (단일 행 함수) : 하나의 데이터에 대해서만 작업할 수 있는 함수들.
- Aggregation Function (다중 행 함수, 집합 함수) : 여러 데이터들을 하나로 묶어 사용해야 하는 함수.

이 두 카테고리에 대해 살펴보자.

# Single Row Functions

데이터 자료형에 따라 구분할 수 있다. 

## String

- ASCII(char) : 특정 문자를 ASCII 숫자 코드로 변환. 즉, 숫자를 반환한다.
- CHR(code) : ASCII() 함수와 정반대의 역할로, ASCII 숫자 코드를 입력하면 그에 대응되는 문자를 반환한다.

```sql
SELECT ASCII('a'), ASCII('c'), ASCII('e'); -- 97, 99, 101
SELECT CHR(97), CHR(99), CHR(101); -- a, c, e
```

예제 1-1

- LENGTH(str) : 문자열의 길이 반환

```sql
SELECT LENGTH('site_users'), LENGTH('nice to meet you'); -- 10, 16
```

예제 1-2

- INSTR(str, substr) : 문자열 검색. 전체 문자열 str 내에서 처음으로 오는 찾고자 하는 부분 문자열의 인덱스를 반환. 없으면 0을 반환. SQL에서는 인덱스가 1부터 시작됨.

```sql
-- 9
SELECT INSTR('I wanna wash the dishes in the washington DC', 'wash');

SELECT INSTR('insta', 'str');  -- 0
```

예제 1-3

- LEFT(str, len) : 문자열의 맨 왼쪽에서 len만큼의 부분 문자열 추출.
- RIGHT(str, len) : 문자열의 맨 오른쪽에서 len만큼의 부분 문자열 추출.
- SUBSTR(str, start, len), SUBSTRING(str, start, len) : 문자열에서 start로 지정된 시작 위치로부터 len만큼의 부분 문자열을 읽어 추출. len 생략 시 시작위치부터 맨 끝까지 추출.

```sql
SELECT LEFT('Korea', 2), RIGHT('class A', 1); -- ko, A
SELECT SUBSTR('laptop', 4, 3), SUBSTRING('화이팅!', 1); -- top, 이팅!
SELECT SUBSTR('laptop', -3, 3);  -- top
-- 시작 위치에 음수가 들어오는 경우, 맨 오른쪽에서 그 숫자만큼 떨어진 
-- 곳이 시작 위치가 된다. 
```

예제 1-4

- LOWER(), UPPER() : 영어 대소문자 전환

```sql
-- select something, SELECT ANOTHER
SELECT LOWER('SELECT SOMETHING'), UPPER('select another');
```

예제 1-5

- LTRIM(), RTRIM, TRIM() : 왼쪽, 오른쪽, 양쪽에 존재하는 특정 문자열(주로 공백) 제거

```sql
SELECT LTRIM('      WOW      ');  -- |WOW     |
SELECT RTRIM('      WOW      ');  -- |     WOW|
SELECT TRIM('      WOW      ');  -- |WOW|
```

예제 1-6

## Numeric

- CEIL() : 올림
- FLOOR() : 내림
- ROUND(number, n) : 소수점 n번째에서 반올림. 두 번째 인자 생략 시 소수점 1번째에서 반올림.
- TRUNCATE(number, n) : 소수점 n번째까지만 살리고 그 뒤는 모두 절삭. 두 번째 인자는 생략 불가능.

```sql
-- 2, 1, 3.1416, 2.9
SELECT CEIL(1.25), FLOOR(1.9), ROUND(3.141592, 4), TRUNCATE(2.99, 1);
```

예제 2-1

- MOD(N, M) : N % M, 즉 N을 M으로 나눈 나머지 값 반환.

```sql
SELECT MOD(5, 3), 5 % 3;  -- 2, 2
```

예제 2-2

- RAND() : 0 ≤ x < 1 사이의 실수를 무작위로 반환.

```sql
-- 실행할 때마다 반환되는 값이 다르다.
SELECT RAND();

-- 1부터 10사이의 정수 범위를 가질려면?
SELECT FLOOR((RAND() * 10) + 1);
```

예제 2-3

## Datetime

- NOW(), SYSDATE() - 현재 날짜와 시각 표시. (NOW()는 표준 SQL이 아님)

```sql
SELECT NOW(), SYSDATE();  -- 2024-08-06 14:23:53, 2024-08-06 14:23:53
```

예제 3-1

- CURDATE() : 현재 날짜 표시
- CURTIME() : 현재 시각 표시

```sql
SELECT CURDATE(), CURTIME(); -- 2024-08-06, 14:27:42
```

예제 3-2

- YEAR(date) : 날짜의 연도만 반환.
- MONTH(date) : 날짜의 월만 반환.
- DAYOFMONTH(date) : 날짜의 일을 반환.
- DAYOFWEEK(date) : 날짜의 요일에 해당하는 숫자를 반환. 1-일요일, 2-월요일, … 순으로 매핑된다.
- WEEKDAY(date) : 날짜의 요일에 해당하는 숫자를 반환. 0-월요일, 1-화요일 순으로 매핑되며 DAYOFWEEK() 함수와는 2 차이가 난다.

```sql
SELECT DAYOFWEEK('2024-08-06'), WEEKDAY('2024-08-06'); -- 3, 1 (화)
```

예제 3-3

- DAYOFYEAR(date) : 1년 기준 (1월 1일로부터)으로부터 현재까지의 경과 일수를 반환.

```sql
SELECT DAYOFYEAR('2024-08-06'); -- 219
```

예제 3-4

- DATE_ADD(date, INTERVAL num unit) : 특정 날짜에서 기간을 더했을 때의 날짜를 반환.
- DATE_SUB(date, INTERVAL num unit) : 특정 날짜에서 기간을 뺐을 때의 날짜를 반환.
- unit : DAY, YEAR, SECOND, MONTH 등…

```sql
SELECT 
  DATE_ADD(NOW(), INTERVAL 100 DAY), 
  DATE_SUB(NOW(), INTERVAL 100 DAY),
  DATE_ADD(NOW(), INTERVAL 3 YEAR)
;
-- 2024-11-14 17:16:29 | 2024-04-28 17:16:29 | 2027-08-06 17:16:29
```

예제 3-5

- DATEDIFF(date1, date2) : 두 날짜의 차이를 일수로 반환.
- TIMESTAMPDIFF(unit, date1, date2) : 두 날짜의 차이를 unit으로 지정한 단위로 반환

```sql
SELECT
  DATEDIFF(NOW(), '1996.06.17'),
  ABS(TIMESTAMPDIFF(YEAR, NOW(), '1996.06.17'))
;
-- 10,277 | 28
-- ABS() : 음수를 양수로 변환.
```

예제 3-6

- LAST_DAY(date) : 인자로 주어진 날짜에서, 그 달의 마지막 일을 날짜 형태로 반환.

```sql
SELECT LAST_DAY(NOW());  -- 2024-08-31
```

예제 3-7

# Aggregation functions

- GROUP BY와 함께 사용할 수 있다.
- 함수들
    - AVG(): 평균
    - SUM() : 총합
    - MAX() : 최대
    - MIN() : 최소
    - COUNT() : 개수. NULL값은 제외하고 계산
- WHERE 조건문 내에서 Aggregation function들을 사용할 수 없다. 대신 GROUP BY 뒤에 올 HAVING 조건문에서 사용해야 한다. GROUP BY, HAVING은 다른 페이지에서 자세히 소개할 예정.

```sql
SELECT 
  AVG(mileage), 
  SUM(mileage), 
  MAX(mileage), 
  MIN(mileage), 
  COUNT(mileage)
FROM site_users;
```

예제 4-1

```sql
+--------------+--------------+--------------+--------------+----------------+
| AVG(mileage) | SUM(mileage) | MAX(mileage) | MIN(mileage) | COUNT(mileage) |
+--------------+--------------+--------------+--------------+----------------+
|    2956.5000 |        59130 |         4908 |         1129 |             20 |
+--------------+--------------+--------------+--------------+----------------+
```

예제 4-1 실행결과

# 그 외 함수들

- CONVERT(data, datetype), CAST() : 데이터의 자료형을 변환해주는 함수

```sql
SELECT CONVERT('20240806', DATE);  
-- 2024-08-06 | 문자열을 날짜형으로 변환. 
```

예제 5-1

- CASE : 다중 조건문으로 사용

```sql
-- CASE 구조
CASE
  WHEN 조건식1 THEN
    결과1
  WHEN 조건식2 THEN
    결과2
  [WHEN ... THEN ...]  -- 원하는 만큼 추가 가능.
  [ELSE 결과]  -- 생략 가능.
END
```

예제 5-2

```sql
-- 클래스 넘버를 보기 좋게 등급명으로 변환하여 보여준다. 
SELECT username, mileage, 
  CASE
    WHEN class_number = 1 THEN
      'VVIP'
    WHEN class_number = 2 THEN
      'VIP'
    WHEN class_number = 3 THEN
      'GOLD'
    WHEN class_number = 4 THEN
      'SILVER'
    ELSE
      'BRONZE'
  END '등급'
FROM site_users
ORDER BY class_number;
```

예제 5-3

```sql
+-----------------+---------+--------+
| username        | mileage | 등급   |
+-----------------+---------+--------+
| pcopozio0       |    1549 | VVIP   |
| hshickleh       |    2632 | VVIP   |
| lscaddinge      |    4640 | VVIP   |
| atailourb       |    1546 | VVIP   |
| dionsj          |    2596 | VVIP   |
| fwillimonta     |    1282 | VIP    |
| dmulgrewc       |    3492 | VIP    |
| egudgion3       |    4238 | VIP    |
| kmardle8        |    4058 | VIP    |
| gfittes4        |    4632 | GOLD   |
| bleggis7        |    1396 | GOLD   |
| dbuttrum5       |    2235 | SILVER |
| ksimeolid       |    4908 | SILVER |
| eszanto1        |    2712 | SILVER |
| aledesg         |    2954 | SILVER |
| lglandersi      |    3712 | BRONZE |
| srenonf         |    3486 | BRONZE |
| jmatuszkiewicz2 |    1129 | BRONZE |
| lfree6          |    1891 | BRONZE |
| dsummerside9    |    4042 | BRONZE |
+-----------------+---------+--------+
```

예제 5-3 실행결과

- COALESCE(값, 대체값) : 첫 번째 인자로 주어진 값이 NULL일 경우 이를 대체할 값 지정하여 반환.

```sql
-- NULL값은 숫자와 사칙연산을 할 수 없다. 
-- 따라서 aver_purchase가 NULL이면 
-- 연산 결과도 NULL이 나온다. 
SELECT 
  username, 
  aver_purchase, 
  aver_purchase + 1000
FROM site_users
ORDER BY aver_purchase;
```

예제 5-4

```sql
+-----------------+---------------+----------------------+
| username        | aver_purchase | aver_purchase + 1000 |
+-----------------+---------------+----------------------+
| fwillimonta     |          NULL |                 NULL |
| atailourb       |          NULL |                 NULL |
| hshickleh       |          NULL |                 NULL |
| kmardle8        |          NULL |                 NULL |
| bleggis7        |          NULL |                 NULL |
| dbuttrum5       |          NULL |                 NULL |
| egudgion3       |          NULL |                 NULL |
| lglandersi      |          NULL |                 NULL |
| eszanto1        |          NULL |                 NULL |
| ksimeolid       |          NULL |                 NULL |
| dsummerside9    |           249 |                 1249 |
| srenonf         |           322 |                 1322 |
| lfree6          |           547 |                 1547 |
| gfittes4        |           592 |                 1592 |
| pcopozio0       |           616 |                 1616 |
| lscaddinge      |           656 |                 1656 |
| dionsj          |           761 |                 1761 |
| dmulgrewc       |           807 |                 1807 |
| aledesg         |           850 |                 1850 |
| jmatuszkiewicz2 |           855 |                 1855 |
+-----------------+---------------+----------------------+
```

예제 5-4 실행결과

```sql
SELECT 
  username, 
  aver_purchase, 
  COALESCE(aver_purchase, 0) + 1000  -- NULL값은 0으로 대체.
FROM site_users
ORDER BY aver_purchase;
```

예제 5-5

```sql
+-----------------+---------------+-----------------------------------+
| username        | aver_purchase | COALESCE(aver_purchase, 0) + 1000 |
+-----------------+---------------+-----------------------------------+
| fwillimonta     |          NULL |                              1000 |
| atailourb       |          NULL |                              1000 |
| hshickleh       |          NULL |                              1000 |
| kmardle8        |          NULL |                              1000 |
| bleggis7        |          NULL |                              1000 |
| dbuttrum5       |          NULL |                              1000 |
| egudgion3       |          NULL |                              1000 |
| lglandersi      |          NULL |                              1000 |
| eszanto1        |          NULL |                              1000 |
| ksimeolid       |          NULL |                              1000 |
| dsummerside9    |           249 |                              1249 |
| srenonf         |           322 |                              1322 |
| lfree6          |           547 |                              1547 |
| gfittes4        |           592 |                              1592 |
| pcopozio0       |           616 |                              1616 |
| lscaddinge      |           656 |                              1656 |
| dionsj          |           761 |                              1761 |
| dmulgrewc       |           807 |                              1807 |
| aledesg         |           850 |                              1850 |
| jmatuszkiewicz2 |           855 |                              1855 |
+-----------------+---------------+-----------------------------------+
```

예제 5-5 실행결과

---

References

[1] 에이콘아카데미(강남) 강의

[2] MySQL - 레퍼런스 메뉴얼. 여러 함수, 연산자, 문법들을 모두 볼 수 있는 곳.

[MySQL :: MySQL 9.0 Reference Manual](https://dev.mysql.com/doc/refman/9.0/en/)