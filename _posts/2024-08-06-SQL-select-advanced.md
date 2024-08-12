---
title: "[SQL][DB] SELECT문 심화"
category: "SQL"
tag: ["sql", "dbms", "db", "database", "데이터베이스", "select", "limit", "group by", "having"]
---

# LIMIT: 결과 데이터 수 제한

LIMIT은 출력될 데이터의 수를 제한시킨다. 

```sql
SELECT 필드명 FROM 테이블명 LIMIT 개수
```

문법 구조

```sql
SELECT * FROM site_users LIMIT 3;
```

예제 1-1

```sql
+-----------+--------------+--------------+---------+-----------------+---------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase |
+-----------+--------------+--------------+---------+-----------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 |
|         2 | 2024-01-09   |            4 |    2712 | eszanto1        |          NULL |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 |
+-----------+--------------+--------------+---------+-----------------+---------------+
```

예제 1-1 실행결과

다음과 같은 형태로도 사용할 수 있다.

```sql
LIMIT 시작위치, 개수
LIMIT 개수 OFFSET 시작위치
-- 위 두 구문은 서로 똑같다. 
```

위 구문은 시작 위치로부터 몇 개만 추출하여 보여주고자 할 때 사용한다. 참고로 여기서의 시작위치는 0번부터 시작한다. 따라서 만약 member_id가 1~3인 정보만 보려고 한다면 다음과 같이 작성해야 한다.

```sql
SELECT * FROM site_users LIMIT 0, 3;
SELECT * FROM site_users LIMIT 3 OFFSET 0;
```

예제 1-2

```sql
+-----------+--------------+--------------+---------+-----------------+---------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase |
+-----------+--------------+--------------+---------+-----------------+---------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 |
|         2 | 2024-01-09   |            4 |    2712 | eszanto1        |          NULL |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 |
+-----------+--------------+--------------+---------+-----------------+---------------+
```

예제 1-2 실행결과

LIMIT 뒤에 n이라는 숫자 하나만 적으면 이는 `LIMIT 0, n` 을 뜻하게 된다. 

# GROUP BY : 데이터를 그룹으로 묶는다

GROUP BY 절은 여러 데이터들을 하나의 그룹으로 묶는 역할을 한다. 주로 그룹별 합산, 평균 등을 내기 위해 Aggregation function과 함께 사용할 때 쓰인다. 

다음은 클래스별 마일리지에 대한 합계 및 평균을 보려는 쿼리문이다. 먼저 GROUP BY를 사용하지 않았을 때이다.

```sql
SELECT
  class_number,
  SUM(mileage),
  AVG(mileage)
FROM site_users;
```

예제 2-1

```sql
+--------------+--------------+--------------+
| class_number | SUM(mileage) | AVG(mileage) |
+--------------+--------------+--------------+
|            1 |        59130 |    2956.5000 |
+--------------+--------------+--------------+
```

예제 2-1 실행결과

클래스별 각각의 마일리지 합계 및 평균을 보고 싶었으나 위 결과에서는 결과가 단 하나만 나왔다. 이는 합계 및 평균 연산이 모든 데이터들에 행해졌다는 뜻이다. 즉, 클래스별로 그룹화하지 않고 사용했기에 이런 문제가 나타난 것이다. 

이제 원래 의도를 위해 GROUP BY 절을 사용해보겠다.

```sql
SELECT
  class_number,
  SUM(mileage),
  AVG(mileage)
FROM site_users
GROUP BY class_number;
```

예제 2-2

```sql
+--------------+--------------+--------------+
| class_number | SUM(mileage) | AVG(mileage) |
+--------------+--------------+--------------+
|            1 |        12963 |    2592.6000 |
|            2 |        13070 |    3267.5000 |
|            3 |         6028 |    3014.0000 |
|            4 |        12809 |    3202.2500 |
|            5 |        14260 |    2852.0000 |
+--------------+--------------+--------------+
```

예제 2-2 실행결과

원래 의도되었던 대로 클래스별 각각의 마일리지 합계 및 평균을 구하였다. 

ORDER BY와 마찬가지로, GROUP BY 뒤에는 그룹을 지정한 기준을 여러 개 지정할 수 있다. 이 역시 1차 기준, 2차 기준, … 순으로 그룹화가 되는 것이다. 

```sql
GROUP BY 필드1, 필드2, 필드3, ...
```

# HAVING 조건문 : 그룹화된 데이터에 대한 조건

```sql
SELECT
  class_number,
  SUM(mileage),
  AVG(mileage)
FROM site_users
GROUP BY class_number
HAVING class_number <= 3;
```

예제 3-1

```sql
+--------------+--------------+--------------+
| class_number | SUM(mileage) | AVG(mileage) |
+--------------+--------------+--------------+
|            1 |        12963 |    2592.6000 |
|            2 |        13070 |    3267.5000 |
|            3 |         6028 |    3014.0000 |
+--------------+--------------+--------------+
```

예제 3-1 실행결과

HAVING 조건문은 GROUP BY를 통해 얻은 결과에 대해 조건을 지정하여 해당 조건을 만족하는 데이터만 추출할 수 있게 해주는 키워드이다. HAVING 절은 꼭 쓰지 않아도 되나, 만약 사용한다면 반드시 앞에 GROUP BY 키워드가 사용되어야 한다. 

또한 HAVING 조건문은 Aggregation function과 함께 사용할 수 있다. 

Aggregation function은 WHERE절과 함께 사용할 수 없다. 다음은 클래스별 마일리지 합계와 평균 결과에서 평균이 3000이상인 클래스만 추출하려고 WHERE절을 사용한 쿼리이다.

```sql
SELECT
  class_number,
  SUM(mileage),
  AVG(mileage)
FROM site_users
WHERE AVG(mileage) >= 3000
GROUP BY class_number;
```

예제 3-2

```sql
ERROR 1111 (HY000): Invalid use of group function
```

예제 3-2 실행결과

Aggregation function인 AVG() 함수를 WHERE절에 사용하니 에러가 뜬 모습이다. WHERE절은 한 번에 한 레코드에 대해서만 검사하는데, 집합 함수는 여러 데이터들을 묶어 계산해야 하기 때문에 수가 맞지 않아 실행되지 않는 것이다. 

위 예제를 HAVING절로 바꿔 작성해보자.

```sql
SELECT
  class_number,
  SUM(mileage),
  AVG(mileage)
FROM site_users
GROUP BY class_number
HAVING AVG(mileage) >= 3000;
```

예제 3-3

```sql
+--------------+--------------+--------------+
| class_number | SUM(mileage) | AVG(mileage) |
+--------------+--------------+--------------+
|            2 |        13070 |    3267.5000 |
|            3 |         6028 |    3014.0000 |
|            4 |        12809 |    3202.2500 |
+--------------+--------------+--------------+
```

예제 3-3 실행결과

잘 작동하는 것을 볼 수 있다. 

다음은 아까와 같이 각 클래스별 마일리지의 합계 및 평균을 구하는데, 이번에는 클래스 넘버가 1인 데이터는 제외하는 쿼리문이다. 

```sql
SELECT
  class_number,
  SUM(mileage),
  AVG(mileage)
FROM site_users
WHERE class_number != 1
GROUP BY class_number;

SELECT
  class_number,
  SUM(mileage),
  AVG(mileage)
FROM site_users
GROUP BY class_number
HAVING class_number != 1;
```

예제 3-4

```sql
+--------------+--------------+--------------+
| class_number | SUM(mileage) | AVG(mileage) |
+--------------+--------------+--------------+
|            2 |        13070 |    3267.5000 |
|            3 |         6028 |    3014.0000 |
|            4 |        12809 |    3202.2500 |
|            5 |        14260 |    2852.0000 |
+--------------+--------------+--------------+
```

예제 3-4 실행결과

위 두 쿼리문 모두 똑같은 결과를 보여준다. 그런데 차이점은 하나는 WHERE절을, 다른 하나는 HAVING 절을 사용했다는 것이다. GROUP BY와 HAVING 절을 같이 사용하는 경우, GROUP BY를 통해 테이블 내 전체 데이터를 그룹화한다. 그룹화된 데이터 묶음에 대해 HAVING 조건문이 실행된다. GROUP BY를 통해 그룹화될 때 필요없는 클래스 넘버 1의 데이터도 같이 그룹화되고 HAVING 절에서야 결과에서 제외되는 방식이다. 반면, WHERE절을 사용할 경우, GROUP BY 보다 WHERE 절이 먼저 실행되어 불필요한 데이터가 먼저 제거된다. 불필요한 데이터가 제거된 후 남은 나머지 데이터들에 대해서만 그룹화가 진행되는 방식이다. 따라서 이론상으로는 GROUP BY 사용 시 WHERE절과 같이 사용하는 것이 HAVING절보다 더 빠르다고 볼 수 있다. 

물론 집합 함수를 조건문에 사용한다면 HAVING 절만 사용할 수 있다는 걸 잊지말자. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 우재남, “혼자 공부하는 SQL”, (한빛미디어, 2021)

[3] [[SQL 첫걸음] 11. 결과 행 제한하기 - LIMIT](https://lxxjn0-dev.netlify.app/first-step-sql-lec-11)

[4] WHERE vs HAVING 성능 차이

[오라클공부 317. HAVING 절과 WHERE 절의 성능 차이](https://oraclejavastudy.tistory.com/entry/오라클공부-317-HAVING-절과-WHERE-절의-성능-차이)

[5] WHERE vs HAVING 성능 차이 (2)   

[SQL 기초 및 PLSQL 실무 강좌 자료 HAVING 절과 WHERE 절의 성능 차이](https://blog.naver.com/zeusmale1/220878218911)