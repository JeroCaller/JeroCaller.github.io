---
title: "[SQL][DB] 데이터 무결성과 제약조건"
category: "SQL"
tag: ["sql", "dbms", "db", "database", "데이터베이스", "무결성", "제약조건", "constraint", "data integrity", "integrity"]
---

# 데이터 무결성 (Data Integrity)

db라고 해서 아무런 데이터나 저장해도 되는 것일까? 예를 들어, 나이를 입력받는 곳에 2000살이라고 하면 적절한 데이터라 할 수 있을까? 또한 테이블 내에 특정 필드에 중복되는 데이터들이 많으면 이것도 적절한 데이터라 할 수 있을까? 이처럼 데이터에도 결함이 있을 수 있다. 

100% 완벽하게 결함있는 데이터들을 방지할 수는 없겠지만, 적어도 그런 노력은 해야할 것이다. 데이터의 결점이 없는 특성을 “데이터 무결성” 이라고 한다. 이는 DB에서 꼭 지켜야하는 성질이라 볼 수 있다. 

데이터의 무결성에는 다음의 3가지로 나뉜다.

- 실체 무결성 (Entity Integrity) : db에서 entity는 보통 테이블을 가리킨다. 테이블의 무결성을 의미하는데, 테이블에 중복되는 데이터의 저장을 방지하는 것을 의미한다. 중복된 데이터가 있으면 삭제, 수정에 문제가 생길 수 있어 실체 무결성을 지켜야 한다.
- 영역 무결성 (Domain Integrity) : 데이터가 삽입될 수 있는 범위를 정해 그 범위를 벗어나는 데이터는 삽입되지 않도록 하는 성질. 앞서 예를 든 것처럼, 사람 나이 입력란에 1300, 2000살을 입력하는 것을 방지하는 것이다.
- 참조 무결성 (Reference Integrity) : 단독 테이블 자체만으로는 어떤 데이터가 잘못된 데이터인지 알아차리기 어려울 수 있다. 이럴 때 다른 테이블이 잘못된 데이터 입력을 방지해주는 역할을 한다. 하나의 테이블이 다른 테이블을 참조하여 잘못된 데이터가 있는지 확인하는데, 다른 테이블을 참조하는 테이블을 “자식 테이블” 또는 “참조 테이블”이라 하고, 잘못된 데이터 입력을 방지하도록 기준을 제공하는 테이블을 “부모 테이블” 또는 “기준 테이블”이라 한다. 보통 부모 테이블에는 기본 키(PK)를, 자식 테이블에는 외래 키(FK)를 설정하고 이 두 필드를 연결하는 방식으로 참조 무결성을 지키기에 PK-FK 관계라고도 불린다. 이러한 특성상 자식과 부모 테이블에는 공통의 필드가 존재하는데, 이 때 부모 테이블에 입력된 데이터들에는 없는 데이터를 자식 데이터에 입력되는 것을 방지하는 방식이다.

이러한 각각의 무결성들을 지키기 위해 DB에서는 여러 제약조건(Constraint)에 해당하는 키워드들을 제공한다. 

- 실체 무결성 : Primary Key(PK, 기본 키), Unique (고유 키)
- 영역 무결성 : Check
- 참조 무결성 : Foreign Key(외래 키)

# 필드의 속성과 각각의 제약조건들

필드와 관련하여 다음의 속성들이 있다.

- NN(Not Null) : 필드에 데이터를 강제로 입력하게 하는 속성이다. 필드는 기본적으로 Null 속성을 지녀, 데이터를 입력하지 않아도 된다. 이러한 NN 속성을 지니게끔 하는 제약조건은 Primary Key이다. 기본 키는 한 테이블에 하나의 필드에만 주거나, 여러 필드들을 하나로 조합하여 주는 방식이다. (보통은 하나의 필드에만 주는 것 같다) 이러한 기본 키가 설정된 필드에는 값을 반드시 입력해야 한다. 즉, NN 속성이 적용된다.
제약조건은 아니지만, 테이블 생성 시 특정 필드에 NOT NULL 키워드를 사용해도 NN속성을 부여할 수 있다.
- ND(No Duplicate) : 중복된 데이터가 없게하는 속성이다. 기본적으로는 한 테이블에 중복되는 데이터들도 입력할 수 있게 되어 있다. 이 속성 역시 기본 키라는 제약조건을 부여하면 그 필드에는 중복된 데이터를 입력할 수 없다. unique라는 제약조건도 마찬가지로 ND 속성을 부여한다. Unique는 기본키와는 다르게 여러 필드에 줄 수 있는 제약조건이다.
- NC(No Change) : 필드의 값을 변경할 수 없게 해주는 속성. 원래는 UPDATE 키워드를 통해 데이터를 변경할 수 있다. 이러한 데이터 변경을 막아주는 제약조건이 Foreign Key이다. 참조 무결성을 지키는 두 테이블에 대해, 자식 테이블 내 데이터가 부모 테이블에는 없는 엉뚱한 데이터로 변경되는 것을 막아주는 것이 이 예이다.   

# 제약조건

## Primary Key (기본 키)

기본 키는 NN속성과 ND 속성을 지원한다. 기본적으로는 한 테이블에 하나의 필드에만 적용 가능하며, 여러 필드들을 하나로 묶어 적용할 수도 있다. 이 경우, 단 하나의 필드에는 중복된 데이터가 있을 수 있으나, 다른 필드와 함께 조합하면 중복된 데이터가 나올 수 없는 경우에 사용한다. 예를 들어, 이름은 동명이인이 있을 수 있어 중복될 수 있는 반면, 이름과 전화번호 둘 다 똑같은 사람은 있을 수 없기에 이 둘을 묶어 기본 키를 부여할 수도 있다. 

SQL에서는 PRIMARY KEY라는 키워드를 사용하며, 테이블 생성 시 적용하거나 이미 적용된 테이블에 나중에 적용하는 방법이 있다. 보통 다음과 같이 사용할 수 있다.

```sql
-- 방법1
CREATE TABLE mytbl (
  id int PRIMARY KEY,
  name varchar(10)
);

-- 방법2
-- CONSTRAINT pk고유이름 PRIMARY KEY (적용할 필드명)
CREATE TABLE mytbl2 (
  id int,
  name varchar(10),
  CONSTRAINT pk_id PRIMARY KEY (id)
  -- , CONSTRAINT uk_NAME UNIQUE (name)  -- 제약조건을 여러 개 줄 수도 있다.
);

-- 방법3
-- 이미 기존 테이블이 있고, 그 테이블에 기본키를 적용하는 경우.
ALTER TABLE mytbl 
  ADD CONSTRAINT pk_id PRIMARY KEY (id);
	
-- 참고로, 기존에 적용한 제약조건을 해제하는 방법은 대략 다음과 같다.
ALTER TABLE mytbl
  DROP UNIQUE;
```

기본 키 적용 실습을 위해 다음과 같이 새 테이블을 생성하고 기본 키를 설정해보겠다.

```sql
CREATE TABLE tblperson (
  id int,
  name varchar(10),
  birth date
);

DESC tblperson;
```

예제 1-1

```sql
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | YES  |     | NULL    |       |
| name  | varchar(10) | YES  |     | NULL    |       |
| birth | date        | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.019 sec)
```

예제 1-1 실행결과

위 실행결과를 보면, 새로 생성된 테이블의 그 어떤 필드에도 key가 설정되어 있지 않은 것을 알 수 있다. 이제 ALTER TABLE 키워드를 이용하여 해당 테이블에 기본키를 설정해보겠다.

```sql
ALTER TABLE tblperson
  ADD CONSTRAINT pk_id PRIMARY KEY (id);
```

예제 1-2

```sql
MariaDB [mytestmaria]> desc tblperson;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   | PRI | NULL    |       |
| name  | varchar(10) | YES  |     | NULL    |       |
| birth | date        | YES  |     | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.014 sec)
```

예제 1-2 실행결과

id 키에 기본키가 설정되어 있어 “key” 필드에 “PRI”라고 적혀 있는 것을 확인할 수 있다. 또한, 기본 키는 NN 속성도 있기에 “Null” 필드에 “NO”로 바뀐 것을 볼 수 있다. 

이제 기본키가 정말로 NN속성과 ND 속성을 가지는지 테스트하기위해 몇몇 데이터를 삽입해보겠다.

```sql
-- NN 속성 테스트.
INSERT INTO tblperson (name, birth) VALUES ('자바스', '2000-01-01');
-- ERROR 1364 (HY000): Field 'id' doesn't have a default value

-- ND 속성 테스트.
INSERT INTO tblperson VALUES (1, '자바스', '2000-01-01');
INSERT INTO tblperson VALUES (1, '나이썬', '1999-09-09');  -- 여기서 에러!
-- ERROR 1062 (23000): Duplicate entry '1' for key 'PRIMARY'
```

예제 1-3

## Unique

고유 키(unique)는 ND 속성을 가지게 할 수 있는 제약조건이다. 즉, 중복된 데이터 삽입 방지만 할 수 있다. 기본 키와 달리 NN속성을 부여할 수는 없다. 또한 이 제약조건은 테이블에 여러 필드에 개별적으로 부여할 수 있다. 

이번에는 unique 제약조건을 테이블에 부여해보겠다. 기존 테이블에 하겠다. 

```sql
ALTER TABLE tblperson
  ADD CONSTRAINT uk_name UNIQUE (name),
  ADD CONSTRAINT uk_birth UNIQUE (birth);
```

예제 1-4

```sql
MariaDB [mytestmaria]> desc tblperson;
+-------+-------------+------+-----+---------+-------+
| Field | Type        | Null | Key | Default | Extra |
+-------+-------------+------+-----+---------+-------+
| id    | int(11)     | NO   | PRI | NULL    |       |
| name  | varchar(10) | YES  | UNI | NULL    |       |
| birth | date        | YES  | UNI | NULL    |       |
+-------+-------------+------+-----+---------+-------+
3 rows in set (0.017 sec)
```

예제 1-4 실행결과

위와 같이 둘 이상에 필드에 개별적으로 고유 키를 부여할 수 있다. 이제 ND 속성을 체크해보자. 

```sql
-- ND 속성 테스트.
INSERT INTO tblperson VALUES (2, '자바스', '1998-01-01');
-- ERROR 1062 (23000): Duplicate entry '자바스' for key 'uk_name'
INSERT INTO tblperson VALUES (2, '나이썬', '2000-01-01');
-- ERROR 1062 (23000): Duplicate entry '2000-01-01' for key 'uk_birth'
```

예제 1-5

## Check

Check 키워드는 영역 무결성을 위한 키워드로, 입력된 데이터가 정해진 범위 내에 있는지 확인하는 키워드이다. 

```sql
-- #1 테이블 생성부터 제약조건 부여
CREATE TABLE tblperson (
  id int,
  name varchar(10),
  age int CHECK(age BETWEEN 20 AND 60)
);

-- #2 이미 생성된 테이블에 제약조건 부여.
CREATE TABLE tblperson (
  id int,
  name varchar(10),
  age int
);

ALTER TABLE tblperson
  ADD CONSTRAINT CHECK(age BETWEEN 20 AND 60);
	
-- 영역 무결성 테스트
INSERT INTO tblperson VALUES (1, '김큐엘', 10);
-- ERROR 4025 (23000): CONSTRAINT `CONSTRAINT_1` failed for `sql_practice`.`tblperson`
```

예제 2-1

20살부터 60살 사이의 나이를 입력하지 않으면 데이터 삽입이 안되는 것을 볼 수 있다. 

## Foreign Key

이전에 만들었던 site_users와 classes 테이블을 각각 살펴보겠다.

```sql
MariaDB [sql_practice]> select * from classes;
+--------------+------------+-------+
| class_number | class_name | bonus |
+--------------+------------+-------+
|            1 | vvip       |  5000 |
|            2 | vip        |  4000 |
|            3 | gold       |  3000 |
|            4 | silver     |  2000 |
|            5 | bronze     |  1000 |
+--------------+------------+-------+
5 rows in set (0.001 sec)

MariaDB [sql_practice]> select * from site_users;
+-----------+--------------+--------------+---------+-----------------+---------------+-----------------+
| member_id | sign_up_date | class_number | mileage | username        | aver_purchase | recomm_by       |
+-----------+--------------+--------------+---------+-----------------+---------------+-----------------+
|         1 | 2023-10-23   |            1 |    1549 | pcopozio0       |           616 | NULL            |
|         2 | 2024-01-09   |            4 |    2712 | eszanto1        |          NULL | NULL            |
|         3 | 2023-08-15   |            5 |    1129 | jmatuszkiewicz2 |           855 | lglandersi      |
|         4 | 2024-06-04   |            2 |    4238 | egudgion3       |          NULL | dmulgrewc       |
|         5 | 2023-12-06   |            3 |    4632 | gfittes4        |           592 | jmatuszkiewicz2 |
|         6 | 2023-09-23   |            4 |    2235 | dbuttrum5       |          NULL | egudgion3       |
|         7 | 2024-07-21   |            5 |    1891 | lfree6          |           547 | NULL            |
|         8 | 2023-07-03   |            3 |    1396 | bleggis7        |          NULL | NULL            |
|         9 | 2024-05-04   |            2 |    4058 | kmardle8        |          NULL | eszanto1        |
|        10 | 2023-05-06   |            5 |    4042 | dsummerside9    |           249 | NULL            |
|        11 | 2023-01-08   |            2 |    1282 | fwillimonta     |          NULL | lfree6          |
|        12 | 2024-02-21   |            1 |    1546 | atailourb       |          NULL | eszanto1        |
|        13 | 2024-07-09   |            2 |    3492 | dmulgrewc       |           807 | bleggis7        |
|        14 | 2024-06-14   |            4 |    4908 | ksimeolid       |          NULL | lfree6          |
|        15 | 2024-05-16   |            1 |    4640 | lscaddinge      |           656 | NULL            |
|        16 | 2023-10-10   |            5 |    3486 | srenonf         |           322 | jmatuszkiewicz2 |
|        17 | 2024-03-27   |            4 |    2954 | aledesg         |           850 | NULL            |
|        18 | 2023-01-06   |            1 |    2632 | hshickleh       |          NULL | NULL            |
|        19 | 2022-09-11   |            5 |    3712 | lglandersi      |           100 | good123         |
|        20 | 2024-03-13   |            1 |    2596 | dionsj          |           761 | NULL            |
|        21 | 2024-08-07   |            1 |    3000 | good123         |           855 | dionsj          |
+-----------+--------------+--------------+---------+-----------------+---------------+-----------------+
```

예제 3-1

해당 사이트에는 클래스 넘버가 1~5까지만 있다. 여기서 site_users 테이블에 새 데이터를 삽입할 때 클래스 넘버가 1부터 5사이의 값을 가지지 않는 전혀 다른 값을 가진다면 안된다고 가정해보자. site_users 테이블 자체로는 클래스 넘버에 잘못된 값이 입력되었는지 확인할 길이 없다. 이 때, classes 테이블이 잘못된 데이터의 기준을 세워주는 테이블이 된다. site_users 테이블이 classes 테이블의 내용을 참조하여 잘못된 데이터가 입력되는지 확인하도록 해보자. 이 경우, classes를 참조하는 site_users 테이블이 자식 테이블, classes 테이블이 부모 테이블이 된다. 

부모 테이블에 먼저 기본 키 또는 고유 키 제약조건을 설정해줘야하고, 그 다음에 자식 테이블에서 외래 키를 설정해 이를 부모 테이블의 기본 키(또는 고유 키, 여기선 기본 키를 중점으로 설명)가 설정된 필드와 연결한다. 그러면 두 테이블은 참조 무결성을 지킬 수 있게 된다. 

이제 이를 실행해보자.

```sql
-- 부모 테이블에 기본 키 설정
ALTER TABLE classes
  ADD CONSTRAINT pk_class_number PRIMARY KEY (class_number);
	
-- 자식 테이블에 외래 키 설정
-- 외래 키 이름은 관례적으로 fk로 시작하며, 
-- 참조 관계를 명확히 알리기 위해 부모테이블명_자식테이블명_필드명으로 지었다.
-- FOREIGN KEY (외래키 설정할 필드명) REFERENCES 부모테이블(키본키 설정된 필드명)
ALTER TABLE site_users
  ADD CONSTRAINT fk_classes_site_users_class_number 
    FOREIGN KEY (class_number) REFERENCES classes(class_number);
```

예제 3-2

```sql
MariaDB [sql_practice]> desc classes;
+--------------+-------------+------+-----+---------+-------+
| Field        | Type        | Null | Key | Default | Extra |
+--------------+-------------+------+-----+---------+-------+
| class_number | int(11)     | NO   | PRI | NULL    |       |
| class_name   | varchar(20) | YES  |     | NULL    |       |
| bonus        | int(11)     | YES  |     | NULL    |       |
+--------------+-------------+------+-----+---------+-------+

MariaDB [sql_practice]> desc site_users;
+---------------+-------------+------+-----+---------+-------+
| Field         | Type        | Null | Key | Default | Extra |
+---------------+-------------+------+-----+---------+-------+
| member_id     | int(11)     | NO   | PRI | NULL    |       |
| sign_up_date  | date        | YES  |     | NULL    |       |
| class_number  | int(11)     | YES  | MUL | NULL    |       |
| mileage       | int(11)     | YES  |     | NULL    |       |
| username      | varchar(50) | YES  |     | NULL    |       |
| aver_purchase | int(11)     | YES  |     | NULL    |       |
| recomm_by     | varchar(50) | YES  |     | NULL    |       |
+---------------+-------------+------+-----+---------+-------+
7 rows in set (0.028 sec)
```

예제 3-2 실행결과

이제 NC 속성 테스트를 해보자. 

```sql
INSERT INTO site_users VALUES (22, NOW(), 6, 2000, 'ricepaper111', NULL, NULL);
/* 
Cannot add or update a child row: a foreign key constraint fails (`sql_practice`.`site_users`, CONSTRAINT `fk_classes_site_users_class_number` FOREIGN KEY (`class_number`) REFERENCES `classes` (`class_number`))
class_number에 6을 넣었다. 이는 classes.class_number 데이터에는 없는 데이터이므로 
잘못된 데이터 입력으로 인식하여 입력되지 않는 것이다. 
*/

UPDATE site_users
SET class_number = -1
WHERE member_id = 1;
/* 
ERROR 1452 (23000): Cannot add or update a child row: a foreign key constraint fails (`sql_practice`.`site_users`, CONSTRAINT `fk_classes_site_users_class_number` FOREIGN KEY (`class_number`) REFERENCES `classes` (`class_number`))

기존에 존재하는 자식 테이블 내 외래키가 설정된 필드의 데이터를 
부모 테이블에 연결된 기본 키 필드 내 데이터들과 다른 데이터로 변경할 수 없다. 
*/

DELETE FROM site_users WHERE member_id = 21;
/*
Query OK, 1 row affected (0.002 sec)

자식 테이블의 데이터를 삭제하는 것은 가능하다. 
요점은 자식 테이블에 부모 테이블에는 없는 데이터를 입력하거나 
부모 테이블에 없는 데이터로 변경하려고 할 때 이를 방지한다는 것이다.
*/

-- 부모 테이블에 새 데이터 입력은 OK
INSERT INTO classes VALUES (6, 'newbie', 500);

DELETE FROM classes WHERE class_number = 6;
-- Query OK, 1 row affected (0.012 sec)
-- 자식 테이블로부터 아직 참조되지 않는 부모 테이블 내 데이터는 삭제 및 변경 가능.

UPDATE classes
SET class_number = 0
WHERE class_number = 1;
/*
ERROR 1451 (23000): Cannot delete or update a parent row: a foreign key constraint fails (`sql_practice`.`site_users`, CONSTRAINT `fk_classes_site_users_class_number` FOREIGN KEY (`class_number`) REFERENCES `classes` (`class_number`))

그러나, 자식 테이블로부터 단 하나의 데이터라도 부모 테이블의 특정 데이터를 참조하고 있다면, 
부모 테이블에 있는 그 데이터는 변경 혹은 삭제를 할 수 없다. 
*/
```

예제 3-3

부모-자식 테이블을 모두 삭제하고자 한다면, 자식 테이블부터 삭제해야 한다.

```sql

CREATE TABLE tblparent (
  id INT,
  CONSTRAINT pk_id PRIMARY KEY (id)
);

CREATE TABLE tblchild (
  id INT,
  CONSTRAINT fk_id FOREIGN KEY (id)
    REFERENCES tblparent(id)
);

DROP TABLE tblparent;
/*
ERROR 1451 (23000): Cannot delete or update a parent row: a 
foreign key constraint fails

참조 관계에 있는 두 테이블 삭제 시 부모 테이블부터 삭제하면 에러가 난다.
이 경우, 자식 테이블부터 삭제해야 한다.
*/

-- 다음 쿼리문 순서는 OK
DROP TABLE tblchild;
DROP TABLE tblparent;
```

예제 3-4

# 제약조건 외 무결성 지원 명령어들

Default 키워드는 사용자가 특정 필드에 데이터를 입력하지 않은 경우, 미리 설정된 기본값으로 설정해주는 키워드로, 이를 통해 NN 속성을 지원해준다. 

```sql
DROP TABLE tblperson;  -- 기존 테이블 제거

CREATE TABLE tblperson (
  id int DEFAULT 1,
  name varchar(10)
);

INSERT INTO tblperson (name) VALUES ('김큐엘');
SELECT * FROM tblperson;
/*
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | 김큐엘    |
+------+-----------+
*/

-- 기본값을 명시적으로 사용하고자 한다면 DEFAULT 키워드를 사용한다. 
INSERT INTO tblperson (id, name) VALUES (DEFAULT, '나이썬');
SELECT * FROM tblperson;
/*
+------+-----------+
| id   | name      |
+------+-----------+
|    1 | 김큐엘    |
|    1 | 나이썬    |
+------+-----------+
*/
```

예제 3-5

AUTO_INCREMENT는 정수형 데이터를 받는 필드에 데이터 입력 시 자동으로 1씩 증가하는 자연수를 부여해주는 키워드이다. 첫 번째 데이터가 입력되면 1, 두 번째 데이터가 입력되면 2가 자동으로 입력되는 형식이다. 이러한 특성 때문에 해당 필드의 모든 데이터들의 값은 중복되지 않는다. ND 속성을 부여한다. 

주의할 점은, AUTO_INCREMENT를 부여하려면 먼저 해당 필드에 기본 키 또는 고유 키를 먼저 부여해야 한다. AUTO_INCREMENT 속성을 부여한 필드에는 사용자가 명시적으로 숫자를 입력할 수 있으나, 데이터가 입력됨에 따라 자동으로 증가된 숫자가 부여되길 원한다면 해당 필드에 데이터를 입력하지 않는 게 좋다.

```sql
DROP TABLE tblperson;  -- 기존 테이블 제거

CREATE TABLE tblperson (
  id int AUTO_INCREMENT,
  name varchar(10), 
  CONSTRAINT pk_id PRIMARY KEY (id)
);

INSERT INTO tblperson VALUE (NULL, '김큐엘');
INSERT INTO tblperson VALUE (NULL, '나이썬');
/*
MariaDB [sql_practice]> SELECT * FROM tblperson;
+----+-----------+
| id | name      |
+----+-----------+
|  1 | 김큐엘    |
|  2 | 나이썬    |
+----+-----------+
2 rows in set (0.000 sec)
*/
```

예제 3-6

---

References

[1] 에이콘아카데미(강남) 강의

[2] 우재남, “혼자 공부하는 SQL”, (한빛미디어, 2021)