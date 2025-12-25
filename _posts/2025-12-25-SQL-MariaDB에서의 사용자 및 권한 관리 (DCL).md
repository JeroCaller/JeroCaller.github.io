---
title: "[SQL][MariaDB] MariaDB에서의 사용자 및 권한 관리 (DCL)"
category: "SQL"
tag: ["SQL", "DB", "DBMS", "MariaDB", "SQLD", "DCL", "Data Control Language", "Spring Boot"]
---

# MariaDB에서의 사용자 및 권한 관리 (DCL)

<p class="notice--info">
💡

MariaDB 11.4.2 버전을 기준으로 작성한 글입니다.

</p>

MariaDB의 계정(USER, “사용자”라 표현하기도 하지만 이 글에서는 편의상 “계정”이란 표현과 함께 혼합하여 사용할 예정) 및 권한과 관련된 쿼리문 작업을 하던 중에 에러가 나서 실행이 안되는 부분들이 너무 많았다. 사실 계정 및 권한을 다루는 DCL(Data Control Language) 구문에 대해, 필자가 개인적으로 SQLD 자격증 공부를 한 적이 있고, 그 내용 중 DCL을 다루는 부분도 존재해서 그 때 기록해둔대로 따라했다. SQLD에서는 보통 오라클 및 MSSQL을 기준으로 한다. 그래서 그런지 계정 및 권한 관련 문법의 큰 틀은 MariaDB와 거의 동일했지만, 세부적인 부분에서는 다른 점들이 너무 많았고, 이 때문에 여러 에러가 났던 것이었다. 그래서 이번 기회에 MariaDB에서의 계정 및 권한 관련 작업과 관련된 문법들을 이렇게 글로 기록하고자 한다. 

# USER 식별

MariaDB에서는 특정 USER를 명시하고자 할 때, `'username'@'host'` 와 같은 형식으로 username뿐만 아니라 host 이름도 작성해야 한다. 

```sql
ALTER USER 'root'@'localhost' IDENTIFIED BY '새 비밀번호';
```

코드 1-1. root 유저에게 다른 비밀번호를 부여하는 예시 쿼리문. 위 쿼리문에서는 “localhost로 접근하는 root 사용자”를 대상으로 작업하고 있다. 

한 가지 사실은, host 자리에 `'%'` 문자만을 사용할 수도 있는데, 이 경우, 이 계정으로 접속하는 모든 호스트들을 의미한다. 

```sql
ALTER USER 'root'@'%' IDENTIFIED BY '새 비밀번호';
```

코드 1-2. 

위 쿼리문의 경우, “어떤 호스트로 접속하든, root 계정에 대한 비밀번호를 새 비밀번호로 바꾼다”라는 의미이다. 

```sql
GRANT PROJECT_ADMIN TO 'some-user'@'%';
```

코드 1-3. `PROJECT_ADMIN` 이라는 role을 특정 유저에게 부여하는 쿼리문. 

# GRANT 권한 부여

[MariaDB GRANT](https://mariadb.com/docs/server/reference/sql-statements/account-management-sql-statements/grant) 사이트에는 MariaDB에서 사용 가능한 권한(privileges)의 이름, 종류들을 볼 수 있다. 여기서 필자가 발견한 SQLD에서 공부한 내용과 다른 점들은 다음과 같았다. 

- SQLD에서는 `CREATE SESSION` 권한이 있으나, MariaDB에서는 존재하지 않았다.
    - MariaDB에서는 특정 사용자에 이미 최소 하나의 데이터베이스에 어떤 권한이라도 부여하였다면 자동으로 세션이 생성되어 DB에 접속이 가능해진다고 한다.
- `GRANT` 구문을 이용하여 특정 권한들을 사용자 또는 role에 부여하는 경우,  `ON` 절을 추가해야한다. 여기서 ON절 뒤에는 `dbname.tblname` 형태로 부여할 수 있으며, 어느 database에 포함된 어느 table에 해당 권한을 부여할 것인지를 지정할 때 사용된다. 필자가 알기로는 SQLD에서는 컬럼 권한을 제외한 테이블, 데이터베이스 등의 권한 부여 시 별도의 ON 절을 부여하지 않고 바로 특정 사용자 또는 role에 부여할 수 있는 것으로 알고 있었다. 그러나 적어도 MariaDB에서는 ON 절은 필수이다.
    - `*.*` 처럼 지정할 수 있는데, 이는 “모든 데이터베이스와 그 데이터베이스들의 모든 테이블”에 대해 특정 권한을 부여하겠다는 뜻이다.
    - `*` 또는 `table_name` 과 같이 테이블명만 입력하여 특정 테이블에 대해서만 권한을 부여할 수도 있다.

```sql
GRANT 
	CREATE,
	ALTER,
	DROP,
	CREATE VIEW,
	INSERT, 
	UPDATE, 
	DELETE, 
	SELECT
ON A.*
TO PROJECT_ADMIN;
```

코드 2-1. GRANT 구문을 이용하여 특정 role에 특정 권한들을 부여하는 쿼리문. ON절에 `A` 라는 데이터베이스 내 모든 테이블에 대한 특정 권한들을 부여하도록 하였다. 

필자는 이 ON절을 필수로 사용해야한다는 사실을 몰라 ON 절을 빼고 진행하려 했었다. 일단 role에 특정 권한을 부여하고, 이 role을 특정 사용자에게 부여한 뒤, 이 사용자가 접근할 수 있는 DB를 만들어주려고 했던 것이었다. 그런데 알고보니 GRANT 단계에서 ON 절을 이용하여 특정 데이터베이스를 미리 지정해줘야 했던 것이었다. 

- 권한명이 종종 다르다.
    - 예를 들어, SQLD 공부할 때에는 `CREATE TABLE` 키워드를 이용하여 테이블 생성 권한을 부여할 수 있는데, MariaDB에서는 해당 키워드는 없고 대신 `CREATE` 라고만 명시해야 한다. 이 차이를 몰라 계속 쿼리문에서 에러가 발생했었다.
    - MariaDB에서 사용하는 권한 키워드들은 앞서 언급한 MariaDB 공식 사이트를 참고.

## GRANT 권한 부여와 SET ROLE의 차이

한 편, MariaDB에서는 앞서 보았던 `GRANT ... TO {ROLE}` 구문처럼 특정 role에 권한들을 부여한 뒤, 이를 다시 `GRANT {ROLE} TO {user}` 형식으로 특정 사용자에게 부여헀다고 해서 다가 아니다. 이와는 별도로, 해당 사용자가 DB에 접속할 때에는 그 때마다 해당 role을 부여해줘야 한다. 이게 무슨 말인지 이해하기 위해 비유를 들자면, 맨 처음 `GRANT ... TO {ROLE}` , `GRANT {ROLE} TO {user}` 구문을 실행하는 것은 마치 “DB”라는 건물에 새로운 출입자가 생겨서 그 출입자에게 출입을 위한 카드키를 발급해주는 것과 같다. 그런데 이와는 별개로, 해당 출입자가 실제로 건물에 들어가려고 할 때 깜빡하고 그 카드키를 가져오지 않았다면 그 건물에 출입할 수 없을 것이다. 즉, 새로운 출입자에게 카드키를 발급한 것과, 출입자가 그 카드키를 가지고 실제로 건물에 들어가는 것과는 별개라는 것이다. 

DB 접속 시에는 기본적으로 `mysql.user` 테이블의 `default_role` 컬럼값을 따라 해당 접속 사용자에게 그 role을 부여한다. 이를 통해 접속자는 부여받은 role에 포함된 권한들을 통해 DB 조회 및 관련 작업을 할 수 있는 것이다. 만약 `default_role` 즉, 기본으로 설정된 role이 없다면 해당 사용자는 매번 DB에 접속할 때마다 부여받는 role이 전혀 존재하지 않아 아무런 권한도 취득할 수 없어 DB 접속이 안될 수도 있다. 

이를 해결하기 위해선 애초에 `GRANT` 구문을 통해 권한 부여를 한 뒤에는, 해당 사용자가 나중에 DB에 접속할 때마다 자동적으로 그 권한이 부여된 role을 부여해주도록 하는 것이 좋다(사용자가 “카드키”를 가지고 오는 걸 깜빡하는 걸 방지하기 위해 아예 그 사용자의 몸에 카드키에 해당하는 “칩”을 박는 것이라 보면 되겠다). 이는 `SET DEFAULT ROLE` 구문을 통해 수행할 수 있다. 

```sql
SET DEFAULT ROLE {role_name} FOR 'username';
```

코드 3-1. `SET DEFAULT ROLE` 구문 기본 구조

```sql
SET DEFAULT ROLE PROJECT_ADMIN FOR 'some-user'@'%';
```

코드 3-2. `SET DEFAULT ROLE` 구문의 활용 예시. 위 쿼리문을 실행하게 되면 ‘some-user’라는 사용자가 다음에 DB 접속할 때마다 자동적으로 `PROJECT_ADMIN` 이라는 역할이 자동적으로 부여된다. 

위와 같은 쿼리문을 실행하고 나면 `mysql.user` 테이블의 `default_role` 컬럼값이 지정되는 것을 볼 수 있다. 

만약 해당 기본 role을 지우고자 한다면 다음과 같은 형태의 쿼리문을 실행하면 된다. 

```sql
SET DEFAULT ROLE NONE FOR 'username';
```

코드 3-3. 특정 사용자가 DB 접속할 때마다 기본적으로 부여할 role을 삭제하는 쿼리문 구조.

만약 기본으로 설정된 role이 없다면, 해당 사용자는 매번 DB에 접속할 때마다 `SET ROLE {role_name}` 쿼리문을 매번 실행하여, 자기 자신에게 해당 role을 부여해줘야 한다. 

지금까지 언급한 사항들은 다음의 상황들에선 예외적으로 적용되지 않는다.

- 사람이 직접 HeidiSQL과 같은 DBMS 클라이언트 GUI 프로그램 등을 이용하여 DB에 접속할 때.
- root 계정으로 DB에 접속할 때.

위 상황들에 대해서는 기본 role이 설정되지 않았더라도 별도의 `SET ROLE` 쿼리문을 사용하지 않아도 문제 없이 DB에 접속할 수 있다. 

다만 `SET DEFAULT ROLE` 구문을 통해 미리 특정 사용자에게 기본 role을 부여하지 않은 상황이라면 문제가 발생할 때가 있다. 그것은 바로 외부 애플리케이션이 DB 서버에 접속할 때이다. 예를 들어 Spring Boot와 같은 프레임워크로 제작한 백엔드 애플리케이션에서 DB 작업이 필요해 DB 서버에 접속하고자 할 때, 만약 DB에 미리 `SET DEFAULT ROLE` 구문이 적용되지 않은 상태라면 아무리 username, password, url을 올바르게 입력했더라도 접근 거부 에러가 뜬다. 

```java
Caused by: java.sql.SQLSyntaxErrorException: (conn=47) Access denied for user ...
```

코드 3-4. Spring Boot 애플리케이션 실행 시 연결하고자 하는 DB에 현재 사용자에 대한 기본 role이 적용되지 않은 경우 위와 같이 접근 거부 에러가 뜬다. 

따라서 GRANT 키워드를 이용하여 특정 사용자에게 권한을 부여했다면 그 이후에 바로 `SET DEFAULT ROLE` 구문을 이용하여 기본 role을 해당 사용자에게 적용해주는 것이 여러모로 편리하다. 

# USER 및 ROLE 조회

현재 등록된 모든 사용자들의 정보를 조회하고자 한다면 다음의 쿼리문을 사용하면 된다. 

```sql
SELECT * FROM mysql.`user`;
```

코드 4-1. 

![사진 1-1. ](/images/2025-12-25/SQL-MariaDB%EC%97%90%EC%84%9C%EC%9D%98%20%EC%82%AC%EC%9A%A9%EC%9E%90%20%EB%B0%8F%20%EA%B6%8C%ED%95%9C%20%EA%B4%80%EB%A6%AC%20(DCL)/1.png)

사진 1-1. 

![사진 1-2.](/images/2025-12-25/SQL-MariaDB%EC%97%90%EC%84%9C%EC%9D%98%20%EC%82%AC%EC%9A%A9%EC%9E%90%20%EB%B0%8F%20%EA%B6%8C%ED%95%9C%20%EA%B4%80%EB%A6%AC%20(DCL)/2.png)

사진 1-2.

참고로 위 쿼리문의 결과에는 사용자 이름뿐만 아니라 여태까지 등록된 role 정보도 포함되어 있다. 이 role 이름도 “User”라는 컬럼값으로 등록되기 때문에, 특정 행의 “User” 컬럼값이 사용자인지 role인지 판단하려면 같은 테이블의 “is_role” 컬럼을 참고하면 된다. 

한 편, 어떤 사용자에게 어떤 role이 부여되어 있는지는 다음의 쿼리문을 통해 확인 가능하다.

```sql
SELECT * FROM mysql.roles_mapping;
```

코드 4-2.

![사진 1-3.](/images/2025-12-25/SQL-MariaDB%EC%97%90%EC%84%9C%EC%9D%98%20%EC%82%AC%EC%9A%A9%EC%9E%90%20%EB%B0%8F%20%EA%B6%8C%ED%95%9C%20%EA%B4%80%EB%A6%AC%20(DCL)/3.png)

사진 1-3.

참고로, 위 사진에서는 `'root'@'localhost'` 계정에도 `PROJECT_ADMIN` 이라는 role이 부여되어 있는데, 사실 root가 아닌 다른 하위 계정에 해당 role을 부여한 것임에도 위와 같이 “Role” 값이 변경되었다. 이렇게 자동으로 변경된 이유에 대해서는 파악하지 못했으나, 확인 결과 root 계정에 부여된 모든 권한이 사라진 것은 아닌 것 같다. 여전히 다른 모든 데이터베이스 조회 등이 가능함을 확인하였다. 

어떤 사용자에게, 또는 어떤 role에게 어떤 권한들이 부여되었는지 확인하고자 한다면 다음의 쿼리문을 사용할 수 있다.

```sql
SHOW GRANTS FOR 'some-user';  -- 특정 사용자에게 부여된 모든 권한 조회
SHOW GRANTS; -- 'root'@'localhost' 사용자에 부여된 모든 권한 조회
SHOW GRANTS FOR 'PROJECT_ADMIN';  -- 특정 role에 부여된 모든 권한 조회.
```

코드 4-3.

위 쿼리문 실행 시, 각 계정 또는 role에 부여된 권한들이 `GRANT ... ON ... TO ...` 쿼리문 형식 그대로 보여진다. 

# 번외) 그 외 참고사항

사용자 및 권한, role에 관련된 사항은 아니지만 MariaDB에 대해 자꾸 까먹는 정보들을 여기에 정리하고자 한다.

## MariaDB 버전 확인

현재 설치되어 있는 MariaDB의 버전을 확인하고자 할 때, MariaDB 서버에 접속한 상태라면 다음의 쿼리문을 통해 확인 가능하다.

```sql
SELECT VERSION();
SHOW VARIABLES LIKE '%VERSION%';
```

코드 5-1.

첫 번째 줄의 쿼리문의 경우 단순히 MariaDB의 버전만 나오며, 두 번째 줄의 경우에는 좀 더 상세한 버전 정보들이 출력된다. 

한 편, 터미널에서도 다음과 같은 명령어를 통해 확인 가능하다.

```sql
mysql -V
```

코드 5-2. 

---

References

[1] MariaDB에서의 부여 가능한 권한 종류들 목록

[GRANT \| Server \| MariaDB Documentation](https://mariadb.com/docs/server/reference/sql-statements/account-management-sql-statements/grant)

[2] [MariaDB 데이터베이스, 사용자, 롤 관리를 위한 쿼리](https://calgary.tistory.com/43)

[3] [[MariaDB] 그룹(ROLE) 기반 권한 부여](https://opensrc.tistory.com/236)

[4] [SET DEFAULT ROLE \| Server \| MariaDB Documentation](https://mariadb.com/docs/server/reference/sql-statements/account-management-sql-statements/set-default-role)

[5] [MariaDB: SET DEFAULT ROLE 완벽 가이드 - 역할 설정부터 오류 해결까지](https://runebook.dev/ko/docs/mariadb/set-default-role/index)

[6] DB 접근 거부 오류 해결책

[[mysql] Access denied for user ''@'localhost' 오류](https://yoonhj97.tistory.com/73)