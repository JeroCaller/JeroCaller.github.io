---
title: "[SQL][DB] 집합 연산"
category: "SQL"
tag: ["sql", "dbms", "db", "database", "데이터베이스"]
---

UNION 키워드를 이용하면 앞뒤에 존재하는 두 SELECT문의 검색 결과를 하나의 표로 합쳐 보여준다. 

```sql
SELECT class_number, mileage, username FROM site_users
GROUP BY class_number
HAVING mileage > AVG(mileage)

UNION

SELECT class_number, mileage, username FROM site_users
WHERE class_number = 1 AND member_id = 1;
```

예제 1-1

```sql
+--------------+---------+-----------+
| class_number | mileage | username  |
+--------------+---------+-----------+
|            2 |    4238 | egudgion3 |
|            3 |    4632 | gfittes4  |
|            1 |    1549 | pcopozio0 |
+--------------+---------+-----------+
```

예제 1-1 실행결과

이 외에도 집합 연산자들이 다음과 같이 존재한다.

- UNION ALL : UNION은 기본적으로 DISTINCT처럼 중복된 데이터들은 걸러서 결과에 보여준다. 반면 그 뒤에 ALL을 쓰면 중복되는 데이터들도 같이 보여준다.
- INTERSECT : 교집합. 즉, 두 SELECT 문 내에서 공통된 데이터들만을 보여준다.
- EXCEPT : 차집합. 두 SELECT문 중 첫 번째 SELECT문의 검색결과에만 있는 데이터를 보여준다.

다음은 site_users 테이블과 classes 테이블에 공통으로 존재하는 class_number 필드값을 출력한 모습이다.

```sql
MariaDB [mytestmaria]> select class_number from site_users;
+--------------+
| class_number |
+--------------+
|            1 |
|            4 |
|            5 |
|            2 |
|            3 |
|            4 |
|            5 |
|            3 |
|            2 |
|            5 |
|            2 |
|            1 |
|            2 |
|            4 |
|            1 |
|            5 |
|            4 |
|            1 |
|            5 |
|            1 |
+--------------+
20 rows in set (0.001 sec)

MariaDB [mytestmaria]> select class_number from classes;
+--------------+
| class_number |
+--------------+
|            1 |
|            2 |
|            3 |
|            4 |
|            5 |
+--------------+
5 rows in set (0.001 sec)
```

예제 1-2

이제 앞서 살펴본 집합연산자들을 각각 적용하여 그 결과를 살펴보겠다. 

```sql
SELECT class_number FROM site_users
UNION
SELECT class_number FROM classes;
```

예제 1-3

```sql
+--------------+
| class_number |
+--------------+
|            1 |
|            4 |
|            5 |
|            2 |
|            3 |
+--------------+
5 rows in set (0.001 sec)
```

예제 1-3 실행결과

```sql
SELECT class_number FROM site_users
UNION ALL
SELECT class_number FROM classes;
```

예제 1-4

```sql
+--------------+
| class_number |
+--------------+
|            1 |
|            4 |
|            5 |
|            2 |
|            3 |
|            4 |
|            5 |
|            3 |
|            2 |
|            5 |
|            2 |
|            1 |
|            2 |
|            4 |
|            1 |
|            5 |
|            4 |
|            1 |
|            5 |
|            1 |
|            1 |
|            2 |
|            3 |
|            4 |
|            5 |
+--------------+
25 rows in set (0.001 sec)
```

예제 1-4 실행결과

```sql
SELECT class_number FROM site_users
INTERSECT
SELECT class_number FROM classes;
```

예제 1-5

```sql
+--------------+
| class_number |
+--------------+
|            1 |
|            4 |
|            5 |
|            2 |
|            3 |
+--------------+
5 rows in set (0.000 sec)
```

예제 1-5 실행결과

```sql
SELECT class_number FROM site_users
EXCEPT
SELECT class_number FROM classes;
```

예제 1-6

```sql
Empty set (0.000 sec)
```

예제 1-6 실행결과

---

References

[1] 에이콘아카데미(강남) 강의

[2] [W3Schools.com](https://www.w3schools.com/sql/sql_union.asp)