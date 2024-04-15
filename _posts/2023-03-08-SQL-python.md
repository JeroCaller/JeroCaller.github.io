---
layout: single
title:  "[Python][RDBMS][SQL] SQL과 파이썬 관련 모듈들."
categories: ["Python"]
tag: ["Python", "관계형 데이터베이스", "데이터베이스", "RDBMS", 'DB', 'Database', "SQL"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# SQL

SQL은 모든 관계형 데이터베이스에 쓰이는 보편적인 선언형 언어(declarative language)이다. 즉, 어떤 작업을 할 것인지만 명령을 내릴 뿐 그 작업을 어떻게 하는지는 언급하지 않는다(알아서 다 해준다). SQL 쿼리(query)들은 모두 텍스트 문자열로 구성되어 있다. 

SQL 선언문에는 두 가지 종류가 있다.

- DDL (data definition language) : 테이블, 데이터베이스에 대한 생성, 삭제, 제약, 허가 등을 다룬다.
- DML (data manipulation language) : 데이터 삽입, 선택, 업데이트, 삭제를 다룬다. 주로 CRUD로 알려져 있다.
    - Create : SQL INSERT 문을 사용하여 생성
    - Read : SELECT 문을 이용
    - Update: UPDATE 문 사용
    - Delete: DELETE 문 사용

기초적인 SQL DDL 명령어들

| 기능 | SQL 패턴 | SQL 예 |
| --- | --- | --- |
| 데이터베이스를 생성 | CREATE DATABASE dbname | CREATE DATABASE my_db |
| 현재 데이터베이스를 선택 | USE dbname | USE my_db |
| 데이터베이스 및 그 안에 포함된 테이블들 모두 삭제 | DROP DATABASE dbname | DROP DATABASE my_db |
| 테이블 생성 | CREATE TABLE tbname (coldefs) | CREATE TABLE my_tb (id INT, count INT) |
| 테이블 삭제 | DROP TABLE tbname | DROP TABLE my_tb |
| 테이블에서 모든 행 삭제 | TRUNCATE TABLE tbname | TRUNCATE TABLE my_tb |

기초적인 SQL DML 명령어들

| 기능 | SQL 패턴 | SQL 예 |
| --- | --- | --- |
| 행 추가 | INSERT INTO tbname VALUES(…) | INSERT INTO my_tb VALUES(1, 12) |
| 모든 행과 열 선택 | SELECT * FROM tbname | SELECT * FROM my_tb |
| 모든 행과 몇몇 열 선택 | SELECT cols FROM tbname | SELECT id, count FROM my_tb |
| 특정 행과 열 선택 | SELECT cols FROM tbname WHERE condition | SELECT id, count FROM my_tb WHERE count > 5 AND id = 9 |
| 한 열에 있는 몇몇 행 변경 | UPDATE tbname SET col = value WHERE condition | UPDATE my_tb SET count=3 WHERE id=5 |
| 특정 행 삭제 | DELETE FROM tbname WHERE condition | DELETE FROM my_tb WHERE count <= 10 OR id = 16 |

# DB-API

application programming interface(API)은 특정 서비스에 접근하기 위해 호출하는 함수들의 집합이다. DB-API는 관계형 데이터베이스에 접근하기 위한 파이썬 표준 API이다. 이를 통해 하나의 프로그램에서도 여러 개의 관계형 데이터베이스를 다룰 수 있다. 

주요 함수들은 다음과 같다.

- connect() : 데이터베이스와 연결한다. 사용자이름, 패스워드, 서버 주소 등의 인자들이 포함될 수 있다.
- cursor() : 쿼리를 관리하기 위한 cursor 객체를 형성한다.
- execute(), executemany() : 한 개 또는 그 이상의 SQL 명령문을 데이터베이스에 수행한다.
- fetchone(), fetchmany(), fetchall() : execute() 로 실행한 결과물을 얻는다.

파이썬 데이터베이스 모듈들은 대부분 위 형식의 DB-API를 따른다. 물론 몇몇 세부적인 사항들은 다를 수 있다. 

# SQLite

SQLite는 가벼운 오픈 소스 관계형 데이터베이스이다. 파이썬 표준 라이브러리로 존재하며, 데이터베이스를 일반 파일 형식으로 저장한다. SQLite로 형성된 데이터베이스를 파일로 저장하면 기기 간 또는 운영체제 사이에서 해당 파일을 주고 받으면서 통신하기 좋다. 그래서 SQLite는 간단한 데이터베이스 앱을 만들고자 할 때 적합하다. MySQL보다는 기능들이 상대적으로 적지만, 어쨌거나 SQL을 지원하고, 여러 명의 동시 사용자들을 관리할 수 있다. 웹 브라우저, 폰이나 다른 앱에서 SQLite를 embedded database로 활용하곤 한다. 

먼저 로컬 SQLite 데이터베이스 파일을 만들거나 사용하기 위해서 파이썬 코드의 맨 앞에 connect()를 사용해야 한다. 해당 메서드 안에 ‘:memory:’를 넣으면 데이터베이스가 오로지 메모리상에서만 생성된다. 이는 테스트용에 적합하며, 프로그램이 종료되면 데이터는 자동으로 삭제된다. 

해당 모듈을 이용하여 간단한 예제를 만들어보겠다. 다음 예제는 서점에 있는 책의 이름과 책 재고, 가격을 데이터로 저장하는 예제이다.

```python
import sqlite3

conn = sqlite3.connect('bookstore.db')  #1
curs = conn.cursor()  #2

curs.execute('''CREATE TABLE book_list 
(title VARCHAR(40) PRIMARY KEY, inventory INT, price INT)''')  #3

curs.execute('INSERT INTO book_list VALUES("밥 잘 먹고 다니는 법", 45, 17000)') #4
curs.execute('INSERT INTO book_list VALUES("알잘딱한 삶을 사는 법", 12, 23000)')
insert_sql = 'INSERT INTO book_list (title, inventory, price) VALUES(?, ?, ?)'  #5
curs.execute(insert_sql, ('오늘 우리는 첫 이별을 한다.', 3, 10000))  #6

conn.commit()  #7
curs.close()  #8
conn.close()  #9
```

먼저 sqlite3 모듈을 import한다. 표준 라이브러리라 따로 설치할 필요는 없다. 

#1에서 먼저 데이터베이스를 새로 만들고 있다. 참고로 데이터베이스 파일 확장명은 보다시피 .db이다. 그 다음 #2에서 커서 객체를 형성하였다. 앞으로 해당 데이터베이스 안에서 테이블이나 테이블 안 데이터를 명령어로 조작 시 커서 객체를 주로 쓸 것이다. (위 예제를 보더라도 curs 객체가 자주 쓰이는 것을 알 수 있다)

#3에서는 먼저 book_list라는 이름의 테이블을 만들고 각각의 필드명들과 그 필드명들의 자료형을 정의하고 있다. 책 제목이 담길 title, 남은 책 재고량을 표시하는 inventory, 책 한 권 당 가격인 price로 생성해보았다. title은 문자열을 받을 것이므로 40글자 제한의 VARCHAR를 통해 그 자료형을 정의하고 있다. 마찬가지로 inventory, price는 정수형 숫자를 써도 되므로 각각 INT로 그 자료형을 정의하였다. 

#4부터는 본격적으로 데이터를 입력하기 시작하였다. 그런데 #5처럼 아예 명령어 자체를 placeholder로 앞으로 빼내어 따로 작성한 뒤, #6처럼 해당 변수를 execute에 대입하고, 실질적인 데이터를 두 번째 인자로 넣는 것도 가능하다. 해당 방법을 사용하면 외부로부터 악의적인 목적의 SQL 명령어를 삽입하여 해킹을 시도하는 SQL injection으로부터 보호할 수 있다. #5에서 ? 마크는 해당 물음표 자리에 데이터가 들어갈 자리라는 것을 암시하는 장치이다. 

INSERT 문으로 데이터를 새로 생성했을 때 이를 저장하려면 #7에서처럼 코드 마지막에 반드시 connect 객체의 commit() 메서드를 넣어야 한다. 이것을 넣지 않고 해당 코드를 실행시키고 종료시키면 나중에 데이터베이스 파일을 볼 때 해당 데이터들이 저장되지 않은 모습을 볼 수 있다. 

만약 커밋으로 저장한 데이터 이전에 이미 입력하고 있던 데이터를 취소하려면 connect 객체의 rollback() 메서드를 호출하면 된다. 해당 메서드를 호출하면 이전 상태로 되돌아간다. 즉, 커밋 이전의 데이터 변경 사항이 다 취소된다. 그러나 이미 commit()으로 저장된 데이터에 대해서는 rollback() 메서드를 호출하더라도 취소되지 않는다. (뭐 이런 경우는 DELETE 명령어로 일일이 삭제해야겠지) 참고로 commit(), rollback()은 INSERT 문 뿐만 아니라 DELETE, UPDATE 등 데이터를 생성, 변경, 삭제시키는 구문에서도 적용된다. 

데이터베이스 작업을 모두 마치고 종료하려면 맨 마지막 코드에 #8, #9처럼 close() 메서드를 써줘야 한다. 주의할 점은 close() 메서드만 쓴다고 해서 데이터가 모두 저장되는 건 아니므로 데이터를 저장하려면 앞서 언급했듯 commit() 메서드를 이용해야 한다. 

위 예제는 데이터베이스를 새로 만들고, 그 안에 새로운 테이블을 만들었고, 그 안에 새로운 데이터들을 삽입하는 작업들을 수행하였다. 이제 해당 데이터베이스가 잘 저장되었는지, 그리고 특정 데이터만 추출하는 연습을 위해 이번에는 앞서 만든 데이터베이스 내에서 특정 조건을 만족하는 데이터만 추출하는 예제를 만들어보겠다.

```python
import sqlite3

conn = sqlite3.connect('bookstore.db')
curs = conn.cursor()

# book_list 테이블 내 모든 데이터 보기
curs.execute('SELECT * FROM book_list')
rows = curs.fetchall()
print(rows)

# inventory를 오름차순으로 해당 테이블 내 모든 데이터 보기
curs.execute('SELECT * FROM book_list ORDER BY inventory')
print(curs.fetchall())

# inventory 내림차순 기준으로 모든 데이터 보기
curs.execute('SELECT * FROM book_list ORDER BY price DESC')
print(curs.fetchall())

# 해당 테이블 내 가장 높은 가격을 가진 책 데이터 보기
curs.execute('SELECT * FROM book_list WHERE price=(SELECT MAX(price) FROM book_list)')
print(curs.fetchall())

curs.close()
conn.close()
```

```python
[('밥 잘 먹고 다니는 법', 45, 17000), ('알잘딱한 삶을 사는 법', 12, 23000), ('오늘 우리는 첫 이별을 한다.', 3, 10000)]
[('오늘 우리는 첫 이별을 한다.', 3, 10000), ('알잘딱한 삶을 사는 법', 12, 23000), ('밥 잘 먹고 다니는 법', 45, 17000)]
[('알잘딱한 삶을 사는 법', 12, 23000), ('밥 잘 먹고 다니는 법', 45, 17000), ('오늘 우리는 첫 이별을 한다.', 3, 10000)]
[('알잘딱한 삶을 사는 법', 12, 23000)]
```

# SQLAlchemy

관계형 데이터베이스 프로그램에는 SQLite, MySQL 등 다양하다. 따라서 각 관계형 데이터베이스 프로그램들마다 쓰는 SQL이 조금 다를 수 있다. 각 데이터베이스 프로그램들은 자신의 기능과 철학을 반영하는 특정 dialect(사투리, 방언)를 사용한다. 그러다보니 이렇게 차이나는 프로그램들을 하나로 이어줄려는 라이브러리들도 등장하게 되었는데, 그 중 파이썬에서 가장 유명한 라이브러리는 SQLAlchemy가 있다. 

표준 라이브러리는 아니기에 pip install sqlalchemy 명령어를 터미널창에 쳐서 다운 받도록 하자. 

해당 라이브러리에서는 여러 레벨의 사용법을 제공한다.

- 가장 낮은 레벨: 데이터베이스 연결을 직접 다룬다. SQL 명령어를 직접적으로 이용하고 그에 대한 결과를 반환한다. DB-API와 가장 가까운 형태이다.
- 중간 레벨: SQL Expression Language (표현 언어). SQL를 좀 더 파이썬스럽게 실행시키도록 도와준다.
- 가장 높은 레벨: ORM(Object Relational Model, Object Relational Mapper). SQL Expression language를 사용하고, 관계형 데이터 구조와 앱 코드를 결합시켜준다.

높은 레벨일수록 사용자가 더 쉽게 관계형 데이터베이스를 다룰 수 있다. SQL 구문의 직접적 사용 대신 사용자가 좀 더 이해하기 쉬운 문법을 쓰도록 유도하기 때문이다. 하지만 높은 레벨의 것을 쓴다고 무조건 좋은 것은 아니다. 만약 어떠한 이유로 명령대로 작업되지 않거나 좀 더 복잡한 작업을 하길 원한다면 낮은 레벨의 사용도 고려해야한다. 

앞서 말했듯, 관계형 데이터베이스를 다루는 dialect도 sqlite, MySQL등 다양하다. 그리고 하나의 dialect 안에도 여러 가지의 드라이버(driver)라는 것이 존재한다. SQLAlchemy를 처음 사용할 때에는 어떤 dialect의 driver를 사용할 것인지를 문자열 형태로 명시해야 한다. 다음의 예제에서 나오겠지만, sqlalchemy 라이브러리의 create_engine() 함수 안에 인자로 넘기면 된다. 어떤 데이터베이스 dialect와 driver를 쓸 지 명시하는 구체적인 문법은 다음과 같다.

```python
dialect + driver :// user : password@host:port/dbname
```

- dialect : 데이터베이스 종류. sqlite를 쓸 건지 MySQL를 쓸 건지 등을 결정한다.
- driver : 해당 dialect 데이터베이스 중 사용하고자 하는 특정 driver명을 입력한다.
- user, password : 만일 데이터베이스에 접속하기 위해 인증 절차가 따로 필요하다면 유저명과 패스워드를 입력하면 된다.
- host, port : 데이터베이스 서버의 위치를 명시하면 된다. 이 때 : port 부분은 해당 서버에서 표준으로 쓰이는 port가 아닐 때에만 필요하다
- dbname : 데이터베이스 서버에서 처음으로 연결할 데이터베이스 이름을 명시한다.

SQLAlchemy에 연결할 수 있는 dialect 및 driver에는 대표적으로 다음이 있다고 한다.

| dialect | driver |
| --- | --- |
| sqlite | pysqlite (또는 생략 가능) |
| mysql | mysqlconnector |
| mysql | pymysql |
| mysql | oursql |
| postgresql | psycopg2 |
| postgresql | pypostgresql |

지금부터는 앞서 언급한 세 가지 레벨에 대해 예제를 들면서 자세히 살펴보겠다.

## 엔진 층 (engine layer)

SQLAlchemy 중 가장 낮은 레벨을 살펴보자. 여기서는 SQLite를 사용해보자. SQLite에 대한 연결 문자열은 host, port, user, password를 생략한다. dbname은 SQLite에게 데이터베이스에 저장하기 위해 어떤 데이터베이스 파일을 쓸 것인지 알려주는 역할을 한다. 만약 dbname을 생략하면, SQLite는 데이터베이스를 메모리상에 형성한다. (:memory: 와 같아서, 프로그램을 종료하면 해당 데이터베이스는 사라질 것이다) dbname이 슬래쉬 (/)로 시작한다면 사용자 컴퓨터에서의 절대 경로로 데이터베이스 파일을 지정한다. 

다음 예제는 메모리 상에 “책 리스트”를 위한 데이터베이스를 메모리상에 형성하고, 그 안에 책의 이름과 가격, 재고를 입력한 뒤, 데이터가 잘 입력되었는지 확인하는 예제이다. 

```python
import sqlalchemy as sa
from sqlalchemy import text

conn_engine = sa.create_engine('sqlite://')
conn = conn_engine.connect()

create_table_query = '''CREATE TABLE book_list
(title VARCHAR(40) PRIMARY KEY, 
inventory INT, 
price INT)'''
conn.execute(text(create_table_query))

insert_string = text('INSERT INTO book_list (title, inventory, price) VALUES (:title, :inventory, :price)')
conn.execute(insert_string, {"title": '엑셀 다루는 법', "inventory": 3, "price": 12000})
conn.execute(insert_string, {"title": '사회에서 살아남기 위한 알잘딱 10선', "inventory": 10, "price": 20000})
conn.execute(insert_string, {"title": '자취생들을 위한 요리 레시피 45종', "inventory": 5, "price": 18000})

rows = conn.execute(text('SELECT * FROM book_list'))
print(rows)
for row in rows:
    print(row)
```

```python
<sqlalchemy.engine.cursor.CursorResult object at 0x000002747BA40880>
('엑셀 다루는 법', 3, 12000)
('사회에서 살아남기 위한 알잘딱 10선', 10, 20000)
('자취생들을 위한 요리 레시피 45종', 5, 18000)
```

위 예제를 부분별로 나눠 하나하나 살펴보겠다.

```python
import sqlalchemy as sa
from sqlalchemy import text
```

맨 처음에는 위와 같이 import 해준다. 여기서 text() 함수에는 SQL 명령어를 문자열로 넘긴다. 이렇게 해야 나중에 execute() 안에 넣을 때 오류 없이 실행시킬 수 있다. 

```python
conn_engine = sa.create_engine('sqlite://')
conn = conn_engine.connect()
```

sa의 create_engine() 메서드에 어떤 dialect를 사용할 지 명시한다. 여기서는 sqlite를 사용할 것이며, 테스트 용이기에 저장할 필요가 없어서 메모리상에 데이터베이스를 생성하기로 하였다. 그러면 conn_engine 변수에는 

<class 'sqlalchemy.engine.base.Engine'> 이라는 일종의 Engine 객체가 할당되는 것이다. 해당 Engine 객체의 connect()를 호출하여 데이터베이스에 연결한 뒤, 이를 conn 변수에 할당한다. (connect() 메서드에는 아무런 인자도 넣지 않는다)여기서 conn 변수의 자료형은 

<class 'sqlalchemy.engine.base.Connection'>라고 하는 일종의 Connection 객체이다. 

```python
create_table_query = '''CREATE TABLE book_list
(title VARCHAR(40) PRIMARY KEY, 
inventory INT, 
price INT)'''
conn.execute(text(create_table_query))
```

데이터베이스에 연결하였으면 이제 해당 데이터베이스에 새로운 테이블을 만들 차례이다. 그래서 위 코드를 삽입하였다. 이 때, execute() 메서드에 해당 SQL 쿼리문을 입력할 때 반드시 text() 함수 안에 쿼리문을 넣어야 오류없이 실행된다.

```python
insert_string = text('INSERT INTO book_list (title, inventory, price) VALUES (:title, :inventory, :price)')
conn.execute(insert_string, {"title": '엑셀 다루는 법', "inventory": 3, "price": 12000})
conn.execute(insert_string, {"title": '사회에서 살아남기 위한 알잘딱 10선', "inventory": 10, "price": 20000})
conn.execute(insert_string, {"title": '자취생들을 위한 요리 레시피 45종', "inventory": 5, "price": 18000})
```

이제 새로 형성된 테이블에 데이터를 삽입하는 과정이다. 직접 쿼리문에 데이터를 입력하는 대신, 매개변수화하여 데이터는 따로 대입해주는 방식으로 진행되었음을 알 수 있다. 앞서 SQLite에서는 데이터가 들어갈 매개변수를 물음표 (?)로 지정했다면, SQLAlchemy에서는 “:title” 처럼 필드명 앞에 콜론(:)을 붙인다. 그러면 SQLite에서 실행 시 자동으로 ? 마크로 변경되어 SQLite가 알아들을 수 있는 문법으로 바뀐다고 한다. 앞서 언급했듯이, SQLAlchemy는 SQLite, MySQL을 비롯한 여러 관계형 데이터베이스들을 포괄적으로 다루는 곳이기에 특정 dialect에서만 쓰이는 문법보다는 이렇게 일반적인 문법을 쓴다고 한다. 그러면 쿼리문이 해당 dialect로 넘어갈 때 dialect 고유의 문법으로 변환되기에 여러 dialect들을 서로 연결시켜 사용할 때 유용한 방법일 것이다. (마치 한국인, 일본인, 프랑스인 등 여러 나라 국적의 사람들이 모이면 언어가 안 통하니 “영어”라는 세계 공통어를 쓰는 것과 같은 이치이다. 일단 영어를 알면, 영어로 상대방의 말을 들은 다음에, 이를 자기 나라 말로 통역하면 그만이다)

매개변수로 데이터를 넘길 때는 위 코드처럼 {”필드명”: 데이터} 형식으로 dictionary 형태로 써서 execute의 두 번째 인자로 넘기면 된다. 

위 코드에서는 하나의 데이터마다 execute() 메서드에 집어넣어서 데이터를 테이블에 삽입하고 있다. 그러나 사실 매개변수에 한꺼번에 여러 데이터를 써서 집어넣어서 execute() 메서드를 한 번만 쓰게 할 수도 있다. 위 코드를 다음처럼 바꿔 실행해도 결과는 똑같다.

```python
parameters = [
    {"title": '엑셀 다루는 법', "inventory": 3, "price": 12000},
    {"title": '사회에서 살아남기 위한 알잘딱 10선', "inventory": 10, "price": 20000},
    {"title": '자취생들을 위한 요리 레시피 45종', "inventory": 5, "price": 18000}
]
conn.execute(insert_string, parameters)
```

이 때, 위 코드처럼 여러 개의 매개변수 사용 시, dictionary들을 요소로 하는 list 형식으로 만들어 execute() 메서드에 넘기면 된다. 

```python
rows = conn.execute(text('SELECT * FROM book_list'))
print(rows) #1
for row in rows:
    print(row)
```

위에서 데이터들을 삽입하였으니 이제 데이터들이 무사히 삽입되었는지 확인하는 절차이다. 해당 테이블 내의 모든 데이터들을 선택하여 보고자 한다. #1에서처럼 바로 print() 하면 

<sqlalchemy.engine.cursor.CursorResult object at 0x000002747BA40880>

처럼 출력되기 때문에 만약 그 내용물을 보고 싶다면 위 예제처럼 for문을 사용하면 된다. 

위 예제는 SQLite 장에서 DB-API 형식으로 예제를 다뤘던 것과 거의 동일한 형태이다. 이러한 형식의 장점은 일일이 데이터베이스 driver를 코드에 명시할 필요가 없다는 점이다. 대신 create_engine()에 어떤 데이터베이스 driver를 쓸 것인지만 명시하면 SQLAlchemy가 알아서 변환해준다. 

## SQL Expression Language

그 다음 레벨이자 중간 레벨인 SQLAlchemy의 SQL Expression Language에 대해 살펴보자. (SQL EL, 또는 EL이라고 줄여서 말하겠다)해당 개념은 앞서 살펴본 engine layer에 비해 SQL 쿼리문을 사용자가 좀 더 쉽게 적용하도록 할 수 있게 해준다. 이를 위해 EL에서는 SQL를 위한 여러 함수들을 제공한다. 그리고 앞서 살펴본 낮은 레벨의 engine layer보다 더 다양한 dialect들 간 차이점들을 다룰 수 있게 해준다. 

이제 앞서 낮은 레벨에서처럼, SQL Expression Language을 이용한 예제를 살펴보겠다. 다음 예제의 목적 자체는 앞선 예제처럼 데이터베이스 생성 및 연결 → “책 리스트”라는 테이블 생성 → 데이터 입력 → 데이터 출력을 차례대로 실행시키는 것으로 동일하다. 

```python
import sqlalchemy as sa

conn_engine = sa.create_engine('sqlite://')
conn = conn_engine.connect()

# 새 테이블 생성
meta = sa.MetaData()
book_list_table = sa.Table(
    'book_list',
    meta,
    sa.Column('title', sa.String, primary_key=True),
    sa.Column('inventory', sa.Integer),
    sa.Column('price', sa.Integer)
)
meta.create_all(conn)

# 테이블에 데이터 삽입
conn.execute(book_list_table.insert().values(title='엑셀 다루는 법', inventory=3, price=12000))
conn.execute(book_list_table.insert().values({"title": '사회에서 살아남기 위한 알잘딱 10선', "inventory": 10, "price": 20000}))
conn.execute(book_list_table.insert().values(('자취생들을 위한 요리 레시피 45종', 5, 18000)))

# 테이블로부터 모든 데이터 추출
result = conn.execute(book_list_table.select())
rows = result.fetchall()
print(rows)
```

```python
[('엑셀 다루는 법', 3, 12000), ('사회에서 살아남기 위한 알잘딱 10선', 10, 20000), ('자취생들을 위한 요리 레시피 45종', 5, 18000)]
```

위 예제 중 새로운 부분에 대해 부분별로 자세히 살펴보자.

```python
meta = sa.MetaData()
book_list_table = sa.Table(
    'book_list',
    meta,
    sa.Column('title', sa.String, primary_key=True),
    sa.Column('inventory', sa.Integer),
    sa.Column('price', sa.Integer)
)
meta.create_all(conn)
```

예제 2-1에서 새 테이블을 만드는 과정이 위 코드로 바뀌었다. 개인적으로 봤을 땐 SQL 쿼리문을 직접 입력하는 것보다 조금 더 쉽고 구조화되어서 테이블이 어떤 구조로 생성되는지를 더 잘 알 수 있는 것 같다고 느껴진다. 우선 sa 모듈의 MetaData() 메서드를 통해 해당 객체를 형성하고 이를 sa모듈의 Table() 클래스의 두 번째 인자로 넣는다. Table()의 첫째 인자는 테이블의 이름을, 세번째 인자부터는 필드명과 그 필드명의 속성을 정해주면 된다. 그리고 마지막에 Connect 객체를 담은 conn 변수를 meta.create_all() 메서드 안에 넣으면 된다. 

```python
# 테이블에 데이터 삽입
conn.execute(book_list_table.insert().values(title='엑셀 다루는 법', inventory=3, price=12000))
conn.execute(book_list_table.insert().values({"title": '사회에서 살아남기 위한 알잘딱 10선', "inventory": 10, "price": 20000}))
conn.execute(book_list_table.insert().values(('자취생들을 위한 요리 레시피 45종', 5, 18000)))
```

이제 테이블에 데이터를 삽입하는 과정이다. insert, select와 같은 SQL 명령어는 모두 앞서 만든 Table() 객체인 book_list_table의 메서드 (insert(), select() 등등)로 호출하면 된다. 위 코드에서도 마찬가지로 데이터를 삽입하기 위해 insert() 메서드의 values() 메서드에 데이터들을 집어 넣으면 된다. values() 안에 데이터를 넣는 방식은 위 코드에서처럼 3가지 방식으로 행할 수 있다. 위 코드에서 execute가 세 번이나 등장하는 게 싫다면 다음처럼 바꿀 수도 있다.

```python
records = [
    {"title": "엑셀 다루는 법", "inventory": 3, "price": 12000},
    {"title": "사회에서 살아남기 위한 알잘딱 10선", "inventory": 10, "price": 20000},
    {"title": "자취생들을 위한 요리 레시피 45종", "inventory": 5, "price": 10000}
]
conn.execute(book_list_table.insert().values(records))
```

참고로 update(), select() 등을 사용 시 특정 조건을 만족하는 행에만 데이터를 편집, 선택하고자 한다면 예를 들어 update().where(title==”책”).values(inventory=10) 과 같은 형식으로 특정 조건을 삽입할 수 있다.  

```python
result = conn.execute(book_list_table.select())
rows = result.fetchall()
print(rows)
```

이제 해당 테이블의 모든 데이터를 보기 위해 위와 같은 코드를 입력했다. 위의 select() 메서드가 바로 “SELECT * FROM book_list”의 역할을 하는 것이다. select()를 통해 모든 데이터를 얻어온 result에서 데이터를 추출하려면 fetchall()를 사용하면 된다. 

이렇듯, SQL EL은 SQL과 Python을 이어주는 일종의 매개체로 생각할 수 있다. 

## The Object-Relational Mapper (ORM, 객체-관계 매핑)

이제 SQLAlchemy에서의 가장 높은 데이터베이스 연결 레벨인 Object-Relational Mapper 방식을 살펴보자. ORM은 EL을 사용하기는 하지만, EL과의 차이점은 바로 ORM은 실제 데이터베이스가 동작하는 과정들을 안보이게끔 해준다는 것이다. 이를 위해 클래스를 정의한 후 이로부터 ORM이 알아서 데이터베이스로 데이터를 삽입하거나 추출하도록 해준다. 즉 파이썬의 객체를 통해서 데이터베이스와 상호작용하는 것이다. 이를 이용하면 SQL이라는 다른 언어를 직접 사용하지 않고도 오로지 파이썬 문법으로만 데이터베이스 관련 코드를 짜는 것 같은 경험을 줄 것이다. 

이번 예제도 이전 예제와 목적은 똑같다. 다만 이번에는 book_list.db라는 실제 데이터베이스 파일을 만들어 거기에 데이터를 저장하여 ORM이 실제로 동작하는지를 확인할 것이다. 

```python
import sqlalchemy as sa
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

conn_engine = sa.create_engine('sqlite:///book_list.db')
conn = conn_engine.connect()

# 새 테이블 형성
Base = declarative_base()
class BookList(Base):
    __tablename__ = 'book_list'
    title = sa.Column('title', sa.String, primary_key=True)
    inventory = sa.Column('inventory', sa.Integer)
    price = sa.Column('price', sa.Integer)

    def __init__(self, title, inventory, price):
        self.title = title
        self.inventory = inventory
        self.price = price

    def __repr__(self):
        return "<BookList({}, {}, {})>".format(self.title, self.inventory, self.price)
    

Base.metadata.create_all(conn)

# 새 테이블에 입력하기 위한 데이터 삽입을 파이썬 객체를 통해 실행.
first = BookList("엑셀 다루는 법", 3, 12000)
second = BookList("사회에서 살아남기 위한 알잘딱 10선", 10, 20000)
third = BookList("자취생들을 위한 요리 레시피 45종", 5, 10000)

# ORM과 SQL를 서로 연결
Session = sessionmaker(bind=conn)  # 데이터베이스와 상호작용 가능하게 하기 위함
session = Session()

# 테이블에 입력하기 위한 데이터를 본격적으로 데이터베이스에 추가한다.
session.add(first)
session.add_all([second, third])

session.commit()  # 데이터 저장을 위해 필수
conn.commit()  # 이것까지 해야 데이터가 저장됨

# 테이블 내 모든 데이터 추출 및 출력
results = session.query(BookList).all()
for record in results:
    print(record)
```

```python
<BookList(엑셀 다루는 법, 3, 12000)>
<BookList(사회에서 살아남기 위한 알잘딱 10선, 10, 20000)>
<BookList(자취생들을 위한 요리 레시피 45종, 5, 10000)>
```

위 예제를 실행한 후, book_list.db를 찾아가보면 데이터들이 저장되어 있는 것을 확인할 수 있다. (db 파일을 직접 확인하려면 SQLiteStudio라는 프로그램을 다운로드 받아서 그 프로그램을 통해 파일을 열면 된다)

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [047 블로그 데이터를 저장하려면? ― sqlite3](https://wikidocs.net/110975)

[3] [ObjectNotExecutableError when executing any SQL query using AsyncEngine](https://stackoverflow.com/questions/69490450/objectnotexecutableerror-when-executing-any-sql-query-using-asyncengine)

[4] [Working with Transactions and the DBAPI
 —
    SQLAlchemy 1.4 Documentation](https://docs.sqlalchemy.org/en/14/tutorial/dbapi_transactions.html#bundling-parameters-with-a-statement)

[5] [Insert, Updates, Deletes
 —
    SQLAlchemy 2.0 Documentation](https://docs.sqlalchemy.org/en/20/core/dml.html)

[6] [SQLAlchemy, ORM](https://velog.io/@sugenius77/SQLAlchemy-ORM)