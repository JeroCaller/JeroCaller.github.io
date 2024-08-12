---
title: "[SQL][DB] Sub Query 심화"
category: "SQL"
tag: ["sql", "dbms", "db", "database", "데이터베이스", "sub query"]
---

# 다중 행 Sub Query

site_users 테이블에서 각 클래스별 최소 마일리지를 보유한 사람들보다 더 많은 마일리지를 보유한 사람들의 정보를 추출하고 싶어 다음의 쿼리를 작성하였다. 

```sql
SELECT * FROM site_users
WHERE mileage > 
(SELECT MIN(mileage) FROM site_users GROUP BY class_number);
```

예제 1-1

그런데 위 쿼리를 실행하면 다음의 에러 메시지가 출력된다.

```sql
ERROR 1242 (21000): Subquery returns more than 1 row
```

예제 1-1 실행결과

왜 이런 에러가 발생했는지 서브 쿼리만 따로 떼어내서 실행해보았다. 

```sql
MariaDB [sql_practice]> SELECT MIN(mileage) FROM site_users GROUP BY class_number;
+--------------+
| MIN(mileage) |
+--------------+
|         1546 |
|         1282 |
|         1396 |
|         2235 |
|         1129 |
+--------------+
5 rows in set (0.000 sec)
```

예제 1-2

서브 쿼리의 결과가 하나의 행이 아닌 여러 개의 행임을 알 수 있다. 애초에 앞선 에러 메시지에서도 대놓고 “서브 쿼리의 결과로 여러 행이 반환되었어요”라고 하고 있다. 

WHERE 절에서는 조건을 확인할 때 한 번에 한 행에 대해서만 조건을 검사한다. 따라서 WHERE 절에는 한 번에 여러 행의 데이터를 조건 검사하도록 해선 안된다. 이렇게 서브 쿼리의 결과가 다중 행을 반환하면 어떻게 조건 검사를 해야할까? 다중 행의 경우 이에 적용할 수 있는 연산자가 따로 있다. 

- IN
- ANY
- ALL
- EXISTS

다중 행 서브쿼리에 적용할 수 있는 위 연산자들을 살펴보자.

먼저 위에서 봤던 예제 1-1에서는 다중 행 연산자를 적용하여 다음과 같이 바꿀 수 있다. 

```sql
SELECT * FROM site_users
WHERE mileage > 
ALL(SELECT MIN(mileage) FROM site_users GROUP BY class_number)
ORDER BY mileage DESC;
```

예제 1-3.

```sql
MariaDB [sql_practice]> SELECT * FROM site_users
    -> WHERE mileage >
    -> ALL(SELECT MIN(mileage) FROM site_users GROUP BY class_number)
    -> ORDER BY mileage DESC;
+-----------+--------------+--------------+---------+--------------+---------------+
| member_id | sign_up_date | class_number | mileage | username     | aver_purchase |
+-----------+--------------+--------------+---------+--------------+---------------+
|        14 | 2024-06-14   |            4 |    4908 | ksimeolid    |          NULL |
|        15 | 2024-05-16   |            1 |    4640 | lscaddinge   |           656 |
|         5 | 2023-12-06   |            3 |    4632 | gfittes4     |           592 |
|         4 | 2024-06-04   |            2 |    4238 | egudgion3    |          NULL |
|         9 | 2024-05-04   |            2 |    4058 | kmardle8     |          NULL |
|        10 | 2023-05-06   |            5 |    4042 | dsummerside9 |           249 |
|        19 | 2022-09-11   |            5 |    3712 | lglandersi   |           100 |
|        13 | 2024-07-09   |            2 |    3492 | dmulgrewc    |           807 |
|        16 | 2023-10-10   |            5 |    3486 | srenonf      |           322 |
|        17 | 2024-03-27   |            4 |    2954 | aledesg      |           850 |
|         2 | 2024-01-09   |            4 |    2712 | eszanto1     |          NULL |
|        18 | 2023-01-06   |            1 |    2632 | hshickleh    |          NULL |
|        20 | 2024-03-13   |            1 |    2596 | dionsj       |           761 |
+-----------+--------------+--------------+---------+--------------+---------------+
13 rows in set (0.001 sec)
```

예제 1-3 실행결과

ALL은 서브쿼리의 결과로 나온 다중 행, 즉 여러 데이터들 중 모두와 비교했을 때 그 결과값이 true이면 true가 되도록 하는 연산자이다. 위 예제에서, 각 클래스별 최소 마일리지를 보유한 사람 5명의 마일리지 모두와 비교했을 때 그 모두의 값보다 크면 결과값에 포함되는 방식이다. 어떻게 보면 서브쿼리의 여러 데이터에 AND 연산자가 적용된 것처럼 보인다. 

반대로, ANY는 OR 연산자를 적용한 것처럼 행동한다. 즉, 서브 쿼리로 나온 모든 행들과의 조건 비교 중 단 하나의 행이라도 비교에서 true이면 전체 결과가 true가 되는 셈이다. 위 예제의 쿼리문에서 all 대신 any로 교체하면, 20개의 데이터들 중 19개의 데이터가 추출된다. 나머지 한 데이터는 전체 회원들 중에서도 가장 최소의 마일리지 1129 포인트를 보유한 사람이다. ANY를 적용한 경우, 앞선 예제 1-2의 결과로 나온 각 클래스별 최소 마일리지 중 단 하나라도 그 보다 크면 검색 결과에 포함되는 것이다. 1129 포인트를 보유한 사람은 1129를 넘지 못하므로 검색 결과에 포함되지 못한 것이다. 

다음은 “유저 이름에 ‘z’가 들어가는 사람과 같은 클래스 넘버를 가지는 사람들의 정보 조회”를 하는 쿼리문이다.

```sql
SELECT * FROM site_users
  WHERE class_number = ANY(
    SELECT class_number FROM site_users
      WHERE username LIKE '%z%'
  );
```

예제 1-4

```sql
+-----------+--------------+--------------+---------+-----------------+---------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase |
+-----------+--------------+--------------+---------+-----------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 |
|         2 | 2024-01-09   |            4 |    2712 | eszanto1        |          NULL |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 |
|         6 | 2023-09-23   |            4 |    2235 | dbuttrum5       |          NULL |
|         7 | 2024-07-21   |            5 |    1891 | lfree6          |           547 |
|        10 | 2023-05-06   |            5 |    4042 | dsummerside9    |           249 |
|        12 | 2024-02-21   |            1 |    1546 | atailourb       |          NULL |
|        14 | 2024-06-14   |            4 |    4908 | ksimeolid       |          NULL |
|        15 | 2024-05-16   |            1 |    4640 | lscaddinge      |           656 |
|        16 | 2023-10-10   |            5 |    3486 | srenonf         |           322 |
|        17 | 2024-03-27   |            4 |    2954 | aledesg         |           850 |
|        18 | 2023-01-06   |            1 |    2632 | hshickleh       |          NULL |
|        19 | 2022-09-11   |            5 |    3712 | lglandersi      |           100 |
|        20 | 2024-03-13   |            1 |    2596 | dionsj          |           761 |
+-----------+--------------+--------------+---------+-----------------+---------------+
14 rows in set (0.001 sec)
```

예제 1-4 실행결과

이름에 z가 들어가는 사람이 여러 명 있는데, 이 사람들의 클래스 넘버 중 하나라도 해당되면 검색 결과에 포함된다. 이를 위해 `= ANY`를 사용했다. 그런데 이 경우에는 IN으로 대체할 수 있다. 

```sql
SELECT * FROM site_users
  WHERE class_number IN(
    SELECT class_number FROM site_users
      WHERE username LIKE '%z%'
    );
```

예제 1-5

위 쿼리로 실행해도 앞선 결과와 동일하게 나온다. 

EXISTS는 서브쿼리의 결과가 단 하나의 행이라도 나오면 TRUE, 그렇지 않으면 FALSE를 반환한다. 따라서 서브쿼리의 결과가 단 하나라도 나오면 모든 데이터가 출력된다. 

```sql
-- #1
SELECT * FROM site_users
WHERE EXISTS (SELECT aver_purchase 
  FROM site_users
  WHERE aver_purchase > 800);

-- #2
SELECT * FROM site_users
WHERE EXISTS (SELECT aver_purchase 
  FROM site_users
  WHERE aver_purchase > 1000);
```

예제 1-6

```sql
-- #1 결과
+-----------+--------------+--------------+---------+-----------------+---------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase |
+-----------+--------------+--------------+---------+-----------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 |
|         2 | 2024-01-09   |            4 |    2712 | eszanto1        |          NULL |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 |
|         4 | 2024-06-04   |            2 |    4238 | egudgion3       |          NULL |
|         5 | 2023-12-06   |            3 |    4632 | gfittes4        |           592 |
|         6 | 2023-09-23   |            4 |    2235 | dbuttrum5       |          NULL |
|         7 | 2024-07-21   |            5 |    1891 | lfree6          |           547 |
|         8 | 2023-07-03   |            3 |    1396 | bleggis7        |          NULL |
|         9 | 2024-05-04   |            2 |    4058 | kmardle8        |          NULL |
|        10 | 2023-05-06   |            5 |    4042 | dsummerside9    |           249 |
|        11 | 2023-01-08   |            2 |    1282 | fwillimonta     |          NULL |
|        12 | 2024-02-21   |            1 |    1546 | atailourb       |          NULL |
|        13 | 2024-07-09   |            2 |    3492 | dmulgrewc       |           807 |
|        14 | 2024-06-14   |            4 |    4908 | ksimeolid       |          NULL |
|        15 | 2024-05-16   |            1 |    4640 | lscaddinge      |           656 |
|        16 | 2023-10-10   |            5 |    3486 | srenonf         |           322 |
|        17 | 2024-03-27   |            4 |    2954 | aledesg         |           850 |
|        18 | 2023-01-06   |            1 |    2632 | hshickleh       |          NULL |
|        19 | 2022-09-11   |            5 |    3712 | lglandersi      |           100 |
|        20 | 2024-03-13   |            1 |    2596 | dionsj          |           761 |
+-----------+--------------+--------------+---------+-----------------+---------------+

-- #2 결과
Empty set (0.000 sec)
```

예제 1-6 실행결과

`NOT EXISTS` 와 같이 사용할 수도 있음을 참고.

# 다중 열 비교

이번에는 먼저 다음의 데이터를 삽입하겠다.

```sql
INSERT INTO site_users VALUES (21, now(), 1, 3000, 'good123', 855);
```

예제 2-1

```sql
MariaDB [sql_practice]> select * from site_users;
+-----------+--------------+--------------+---------+-----------------+---------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase |
+-----------+--------------+--------------+---------+-----------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 |
|         2 | 2024-01-09   |            4 |    2712 | eszanto1        |          NULL |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 |
|         4 | 2024-06-04   |            2 |    4238 | egudgion3       |          NULL |
|         5 | 2023-12-06   |            3 |    4632 | gfittes4        |           592 |
|         6 | 2023-09-23   |            4 |    2235 | dbuttrum5       |          NULL |
|         7 | 2024-07-21   |            5 |    1891 | lfree6          |           547 |
|         8 | 2023-07-03   |            3 |    1396 | bleggis7        |          NULL |
|         9 | 2024-05-04   |            2 |    4058 | kmardle8        |          NULL |
|        10 | 2023-05-06   |            5 |    4042 | dsummerside9    |           249 |
|        11 | 2023-01-08   |            2 |    1282 | fwillimonta     |          NULL |
|        12 | 2024-02-21   |            1 |    1546 | atailourb       |          NULL |
|        13 | 2024-07-09   |            2 |    3492 | dmulgrewc       |           807 |
|        14 | 2024-06-14   |            4 |    4908 | ksimeolid       |          NULL |
|        15 | 2024-05-16   |            1 |    4640 | lscaddinge      |           656 |
|        16 | 2023-10-10   |            5 |    3486 | srenonf         |           322 |
|        17 | 2024-03-27   |            4 |    2954 | aledesg         |           850 |
|        18 | 2023-01-06   |            1 |    2632 | hshickleh       |          NULL |
|        19 | 2022-09-11   |            5 |    3712 | lglandersi      |           100 |
|        20 | 2024-03-13   |            1 |    2596 | dionsj          |           761 |
|        21 | 2024-08-07   |            1 |    3000 | good123         |           855 |
+-----------+--------------+--------------+---------+-----------------+---------------+
21 rows in set (0.000 sec)
```

예제 2-1 실행결과

이번에는 유저 이름에 'z'가 들어가는 사람의 클래스 넘버와 평균 구매액이 똑같은 사람을 출력하고자 한다. 예를 들어 

| username | class_number | aver_purchase |
| --- | --- | --- |
| zerocola | 1 | 400 |

이란 데이터가 있을 때, 이 사람의 정보와 똑같이 클래스 넘버가 1이면서 동시에 평균구매액이 400인 사람들을 조회하고자 한다. 이를 위해 다음의 쿼리를 작성하였다.

```sql
SELECT * FROM site_users
  WHERE class_number IN(
    SELECT class_number FROM site_users
      WHERE username LIKE '%z%'
  ) AND
  aver_purchase IN (
    SELECT aver_purchase FROM site_users
      WHERE username LIKE '%z%');
```

예제 2-2

```sql
+-----------+--------------+--------------+---------+-----------------+---------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase |
+-----------+--------------+--------------+---------+-----------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 |
|        21 | 2024-08-07   |            1 |    3000 | good123         |           855 |
+-----------+--------------+--------------+---------+-----------------+---------------+
3 rows in set (0.001 sec)
```

예제 2-2 실행결과

이름에 z가 들어가는 데이터는 이 조건을 만족한다. 조건이 결국에 자기 자신을 가리키는 것이기 때문이다. 그런데 위 실행결과를 자세히 보면 이상한 데이터도 추가되어 있다. 그것은 맨 마지막 데이터로, 우리가 아까 추가한 데이터이다. 해당 데이터는 클래스 넘버가 1, 평균구매액이 855로, 이름에 ‘z’가 포함된 나머지 두 데이터와 비교했을 때 클래스 넘버와 평균 구매액 이 둘이 동시에 같지 않다. 따라서 원래는 해당 데이터는 검색 결과에 나와선 안되었다. 그런데 왜 나온걸까?

자세히 보면 알겠지만, 나와선 안되었던 데이터를 자세히 보면, 클래스 넘버는 유저 이름이 ‘pcopozio0’ 인 사람과 동일하고, 평균 구매액은 ‘jmatuszkiewicz2’인 사람의 것과 동일한 것을 알 수 있다. 이를 통해, 방금 작성한 쿼리문은 AND 조건을 사용했음에도 불구하고 두 필드값을 동시에 만족하는 데이터를 찾는 것이 아닌, 두 필드값 중 하나라도 일치하는 데이터까지 찾아버리는 것이다. 

이렇게 둘 이상의 필드값이 동시에 일치하도록 하는 조건을 원한다면 메인 쿼리의 WHERE절에 명시된 그 필드들을 괄호로 묶어 다중 열로 만들어야 한다. 그리고 서브 쿼리에 있는 해당 필드들을 SELECT 문 뒤에 나열하여 작성해야 한다. 다음과 같이 말이다. 

```sql
SELECT * FROM site_users
  WHERE (class_number, aver_purchase) IN (  -- 다중 열 사용
    SELECT class_number, aver_purchase FROM site_users
      WHERE username LIKE '%z%'
    );
```

예제 2-3

```sql
+-----------+--------------+--------------+---------+-----------------+---------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase |
+-----------+--------------+--------------+---------+-----------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 |
+-----------+--------------+--------------+---------+-----------------+---------------+
```

예제 2-3 실행결과

이렇게 필드들을 하나의 괄호로 묶어야 해당 필드들의 조건을 동시에 만족하는 데이터만을 추출할 수 있게 되는 것이다. 

# 상관 서브 쿼리(Relative Sub Query)

```sql
/*
클래스 넘버가 멤버 아이디보다 큰 사람들 중 
한 명이라도 그 마일리지보다 더 많이 보유한 사람들 조회.
*/
SELECT * FROM site_users AS su1
WHERE mileage > ALL(SELECT mileage FROM site_users AS su2
  WHERE su1.class_number > su2.member_id);
```

예제 3-1

```sql
+-----------+--------------+--------------+---------+------------+---------------+
| member_id | sign_up_date | class_number | mileage | username   | aver_purchase |
+-----------+--------------+--------------+---------+------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0  |           616 |
|         4 | 2024-06-04   |            2 |    4238 | egudgion3  |          NULL |
|         5 | 2023-12-06   |            3 |    4632 | gfittes4   |           592 |
|         9 | 2024-05-04   |            2 |    4058 | kmardle8   |          NULL |
|        12 | 2024-02-21   |            1 |    1546 | atailourb  |          NULL |
|        13 | 2024-07-09   |            2 |    3492 | dmulgrewc  |           807 |
|        14 | 2024-06-14   |            4 |    4908 | ksimeolid  |          NULL |
|        15 | 2024-05-16   |            1 |    4640 | lscaddinge |           656 |
|        17 | 2024-03-27   |            4 |    2954 | aledesg    |           850 |
|        18 | 2023-01-06   |            1 |    2632 | hshickleh  |          NULL |
|        20 | 2024-03-13   |            1 |    2596 | dionsj     |           761 |
|        21 | 2024-08-07   |            1 |    3000 | good123    |           855 |
+-----------+--------------+--------------+---------+------------+---------------+
12 rows in set (0.000 sec)
```

예제 3-1 실행결과

위 예제를 보면, 서브쿼리 내부에서 메인 쿼리의 FROM 뒤에 오는 테이블의 별칭을 가져다가 쓰고 있는 것을 알 수 있다. 이렇게 메인 쿼리에 있는 것을 가져와 사용하는 서브쿼리를 상관 서브쿼리라 한다. 

어떤 서브쿼리가 상관 서브쿼리인지 아니면 단독 서브쿼리인지는 그 서브쿼리는 따로 떼어내서 실행해보면 알 수 있다.

```sql
MariaDB [sql_practice]> SELECT mileage FROM site_users AS su2
    -> WHERE su1.class_number > su2.member_id
    -> ;
ERROR 1054 (42S22): Unknown column 'su1.class_number' in 'where clause'
```

예제 3-2

위와 같이 에러가 났는데, 그 이유가 su1을 인식하지 못해서이다. su1은 메인쿼리에 있던 테이블의 별칭인데, 그 구문이 없으므로 인식하지 못하는 것이다. 이러한 에러가 나면 해당 쿼리는 상관 서브쿼리라고 할 수 있다. 

상관 서브쿼리는 먼저 외부에 있는 메인 쿼리가 실행된 이후, 메인 쿼리의 값을 서브 쿼리에서 가져온 후, 처리된 값을 다시 메인 쿼리에 반환하고, 메인 쿼리의 처리가 끝나는 과정을 거친다. 이러한 복잡한 과정 때문에, 단독 서브쿼리보다 성능이 좋지 않다고 한다. 

# 그 외 서브쿼리

다음의 서브쿼리들은 서브쿼리로 보지 않는 관점도 있으니 참고.

- 인라인 뷰(inline view)

```sql
SELECT * FROM (SELECT * FROM site_users WHERE class_number = 1) as t;
```

예제 4-1

```sql
+-----------+--------------+--------------+---------+------------+---------------+
| member_id | sign_up_date | class_number | mileage | username   | aver_purchase |
+-----------+--------------+--------------+---------+------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0  |           616 |
|        12 | 2024-02-21   |            1 |    1546 | atailourb  |          NULL |
|        15 | 2024-05-16   |            1 |    4640 | lscaddinge |           656 |
|        18 | 2023-01-06   |            1 |    2632 | hshickleh  |          NULL |
|        20 | 2024-03-13   |            1 |    2596 | dionsj     |           761 |
|        21 | 2024-08-07   |            1 |    3000 | good123    |           855 |
+-----------+--------------+--------------+---------+------------+---------------+
6 rows in set (0.001 sec)
```

예제 4-1 실행결과

위 예제에서, 직접 테이블명을 사용하면 해당 테이블로부터 모든 데이터를 가져온다. 그러나 위와 같이 테이블명을 직접 언급하는 대신, 해당 테이블로부터 특정 조건을 만족하는 데이터들만 가져오도록 하고 있다. 이를 인라인 뷰라 한다. 이렇게 하면 미리 필터링된 데이터들만을 가져오므로 성능 향상을 꾀할 수 있다. 

주의할 점은, 반드시 별칭을 붙여줘야 한다는 것이다. 위 예제에서 t라고 별칭을 준 것처럼 말이다. 그렇지 않으면 에러가 나므로 주의해야 한다. 

- INSERT ~ SELECT문

[DML 살펴보기](/sql/SQL-DML-살펴보기/) 페이지에서 살펴본 INSERT ~ SELECT문도 서브쿼리로 보는 관점이 있다. 

```sql
INSERT INTO tbl SELECT * FROM site_users;
```

예제 4-2

---

References

[1] 에이콘아카데미(강남) 강의

[2] [https://bill1224.tistory.com/350#다중행 서브쿼리-1](https://bill1224.tistory.com/350#%EB%8B%A4%EC%A4%91%ED%96%89%20%EC%84%9C%EB%B8%8C%EC%BF%BC%EB%A6%AC-1)

[3] exists

[MySQL :: MySQL 9.0 Reference Manual :: 15.2.15.6 Subqueries with EXISTS or NOT EXISTS](https://dev.mysql.com/doc/refman/9.0/en/exists-and-not-exists-subqueries.html)