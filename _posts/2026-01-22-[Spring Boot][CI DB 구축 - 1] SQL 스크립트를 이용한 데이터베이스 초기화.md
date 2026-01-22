---
title: "[Spring Boot][CI DB 구축 - 1] SQL 스크립트를 이용한 데이터베이스 초기화"
category: "Spring"
tag: ["Spring", "Spring Boot", "SQL", "Database", "DB", "CI & CD", "CI"]
---

필자가 직접 제작하고 지금까지도 유지보수하고 있는 스프링부트 기반 라이브러리에서 최근 Github Actions를 이용한 CI 테스트 자동화를 구축하였다. 해당 라이브러리에 작성된 테스트 케이스들 중에서는 DB와 연동하여 데이터를 가져와 테스트를 진행해야하는 경우가 있었다. 필자의 로컬 기기에서는 이미 MariaDB가 설치되어 있는 상태였기에 `application.yml` 파일을 통해 DB와 연동한 후, 인텔리제이에서 테스트를 구동시키면 그 결과를 얻을 수 있었다. 그런데 문득 이러한 과정을 Github Actions를 이용한 CI에서는 어떻게 적용해야할지 궁금해졌다. 사실 나도 모르게 그대로 CI 워크플로우 스크립트를 작성하고 push했었는데, 해당 워크플로우 실행에 실패했었다. runner에서도 똑같이 MariaDB를 미리 구축하고 테스트용 데이터를 주입하는 과정 자체가 필요하단걸 잊고 있었기 때문에 사실 실패는 당연한 것이었다. 그 후 runner에서 DB 인프라를 구축하는 방법에 대해 조사, 실습을 하면서 생각보다 그 과정이 은근 복잡했음을 느꼈다. 워크플로우 스크립트에서만 작업할 게 아니라 라이브러리 쪽에서도 손봐야할게 있었기 때문이다. 기존에 내 로컬 컴퓨터에서 사용하던 방식을 유지한다면 CI 테스트 자동화 과정에서 DB와 연동하여 테스트를 수행하는게 불가능했기에 DB 연동 방식의 변화는 불가피하였다. 

이번 글은 CI 테스트 자동화 구축에 필요한 DB 인프라 구축 및 연동 과정을 다루는 첫 번째 글이며, 스프링부트와 연동할 데이터베이스의 초기화를 SQL 스크립트를 이용하여 진행하는 방법에 대해 다뤄보도록 하겠다. 

# 스프링부트 기반 애플리케이션과 DB 연동 방식

필자는 필자가 제작한 라이브러리의 테스트를 위해 테스트용 가짜 앱을 만들어 테스트를 진행하는 방식을 취했었다. 

<style>
  .cards {
    display: flex;
  }
  .card {
    text-align: center;
    margin: 0.5em;
  }
</style>
<div class="cards">
  <div class="card">
    <img src="/images/2026-01-22/%5BSpring%20Boot%5D%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%201%5D%20SQL%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%B4%88%EA%B8%B0%ED%99%94/1.png" />
    <p>사진 1-1. 필자가 기존에 사용하던 테스트를 위한 앱 - DB 연동 방식</p>
  </div>
  <div class="card">
    <img src="/images/2026-01-22/%5BSpring%20Boot%5D%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%201%5D%20SQL%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%B4%88%EA%B8%B0%ED%99%94/2.png" />
    <p>사진 1-2. 테스트 자동화를 위한 새로운 앱 - DB 연동 방식</p>
  </div>
</div>

위 사진 1-1과 같이 필자는 기존에는 로컬 기기에서만 테스트를 진행했었기에, 이전에 설치된 DB에 테스트용 테이블을 미리 만들어두고, 테스트용 데이터도 미리 삽입해둔 뒤 테스트용 앱과 연동하여 테스트를 진행하는 방식을 취했었다. 

하지만 테스트 자동화 구축을 위해 이러한 방식에 변화가 필요했다. 그래서 필자는 결국 위 사진 1-2와 같은 방식으로 변경하였다. 해당 방식은, DDL 언어로 작성되는 테이블 등을 정의하는 SQL문이 작성된 `schema.sql` 파일, 그리고 테이블에 새로 삽입할 DML 구문이 작성되는 `data.sql` 파일을 스프링부트 프로젝트 내부에 삽입하는 방식이다. 이 방식을 이용하면 미리 DB에 테스트를 위한 테이블 및 데이터를 삽입하는 사전 작업을 따로 거치지 않아도 스프링부트에서 알아서 이 작업을 대신 수행해주기에 좀 더 편리하게 DB 연동을 할 수 있게 된다. 또한 앱을 다른 기기에서 실행해야할 때에도 그 기기에 DBMS만 설치되어 있다면 손쉽게 실행 환경을 마련할 수 있다는 것도 장점이다. 

# 데이터베이스 초기화에 대한 개념

사용 방법에 앞서, 먼저 “데이터베이스 초기화”란 용어에 대해 정리하고 넘어가고자 한다. 여기서 말하는 “데이터베이스 초기화”라는 건 애플리케이션과의 연동을 위해 데이터베이스의 구조와 데이터를 준비하는 과정을 의미한다. 여기에는 스키마 초기화와 데이터 초기화라는 두 단계를 거치게 된다. 

스키마 초기화는 테이블, 인덱스, 제약조건 등을 생성, 수정, 삭제하는 과정이다. 이 다음에 진행되는 데이터 초기화 단계에서는 앞선 스키마 초기화로 생성된 테이블에 초기 데이터를 삽입하는 과정을 거치게 된다. 

앞서 언급했던 필자가 기존에 사용했던 방식은, 개발자가 직접 손수 DB에 필요한 테이블 및 그 외 스키마들을 생성한 뒤, 필요한 초기 데이터를 직접 채워넣는 방식으로, 일종의 수동 데이터베이스 초기화라 볼 수 있다. 

반면, 개발자가 직접 이렇게 초기화하지 않고 스프링부트에서 자동으로 데이터베이스를 초기화할 수 있다. 여기에는 두 가지 방식이 있는데, 하나는 hibernate(JPA)에 의한 초기화, 또 하나는 이 글에서 다룰 SQL 스크립트에 의한 초기화이다. 

hibernate에 의한 초기화는 `application.yml` 파일 내에서 `spring.jpa.hibernate.ddl-auto` 속성을 이용하여 수행할 수 있다. 예를 들어 `create`, `create-drop` 과 같이 속성값을 설정하면, 자바로 작성한 `@Entity` 가 붙은 엔티티 객체들의 구조 및 상호 관계를 토대로 DB에 똑같이 스키마를 생성해준다. 단, `none`, `validate` 속성값을 `ddl-auto` 에 사용한다면 이는 각각 hibernate에 의한 데이터베이스 초기화를 사용하지 않게 된다. 따라서 이미 수동으로 데이터베이스를 초기화해둔 상태일 때 해당 속성값들이 적절한 선택이 될 것이다. 

SQL 스크립트에 의한 초기화는 앞서 언급한 방식으로, 스키마를 정의한 `schema.sql` 파일과, 초기 데이터 삽입을 위한 `data.sql` 파일을 개발자가 작성하면, 스프링부트에서 이를 토대로 필요한 스키마 생성 및 데이터 주입을 수행하게 되는 방식이다. 

# SQL 스크립트 기반 DB 초기화

앞서 언급한 방식을 사용하는 구체적인 방법은 이러하다. 먼저 `src/main/resources` (또는 별도의 테스트 환경이 필요하다면 `src/test/resources`) 폴더에 각각 `schema.sql` 파일과 `data.sql` 파일을 생성한다. `schema.sql` 에는 테이블 등을 정의하는 DDL 쿼리문을 작성하고, `data.sql` 에는 주로 해당 테이블에 데이터를 삽입하는 DML 쿼리문을 작성한다. 그러면 `schema.sql` 를 통해 DB에 새 테이블이 생성될 것이고, 그 테이블에 `data.sql` 에 명시된 데이터들이 삽입될 것이다. 

필자의 경우 각각 다음과 같은 형식으로 쿼리문을 작성했다.

```sql
CREATE TABLE IF NOT EXISTS `fake_festival` (
  `ID` int(11) NOT NULL,
  `TITLE` varchar(100) NOT NULL,
  `ORIGIN` varchar(100) NOT NULL,
  `CONTENT` varchar(1000) NOT NULL,
  `IMAGE` varchar(50) NOT NULL,
  `STARTDATE` date NOT NULL,
  `ENDDATE` date NOT NULL,
  PRIMARY KEY (`ID`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_general_ci;

```

코드 1-1. `schema.sql` - 필자가 입력했던 쿼리문

```sql
INSERT INTO `fake_festival` (`ID`, `TITLE`, `ORIGIN`, `CONTENT`, `IMAGE`, `STARTDATE`, `ENDDATE`) VALUES
	(1, 'Move Over, Darling', ...), // 생략 ...
```

코드 1-2. `data.sql` - 필자가 작성했던 쿼리문 일부. 

그 후에는 `application.yml`에 다음과 같이 몇 가지 속성들을 추가해줘야 한다. 

- `spring.sql.init.mode`
- `spring.jpa.defer-datasource-initialization`
    - 해당 속성은 `ddl-auto` 와 함께 사용할때 이용한다. 스크립트 기반 초기화 방식만을 사용한다면 사용하지 않는다.

```yaml
spring:
  # 생략 ...
  jpa:
    hibernate:
      ddl-auto: create-drop
    # 생략...
    defer-datasource-initialization: true
  sql:
    init:
      mode: always
```

코드 1-3. `application.yml`

SQL 스크립트 기반의 데이터베이스 초기화는 기본적으로 embedded in-memory DB인 경우에만 자동으로 진행된다고 한다. 그 외 DB들에 대해서는 초기화가 자동으로 이뤄지지 않는다. 만약 다른 종류의 DB더라도 항상 초기화되도록 하고자 한다면 위와 같이 `spring.sql.init.mode` 속성값에 `always`를 명시한다. 만약 반대로 SQL 스크립트에 의한 데이터베이스 초기화를 막고자 한다면 해당 속성에 `never` 라는 값을 주면 된다. 

한 편, 이러한 스크립트 기반의 데이터베이스 초기화 과정은 기본적으로 JPA의 `EntityManagerFactory` bean이 생성되기 전에 수행된다. 쉽게 말해, 스크립트 기반 데이터베이스 초기화가 hibernate 기반 초기화보다 먼저 수행된다는 것이다. 그런데 만약 이 순서를 거꾸로 하고자 한다면, `spring.jpa.defer-datasource-initialization` 속성값에 `true`를 주면 된다. 그러면 `EntityManagerFactory` bean이 먼저 생성된 후에 스크립트가 실행된다. 

<style>
  .single-image {
    text-align: center;
    margin: 1em;
  }
  .single-image > p {
    margin-top: 0.5em;
  }
</style>
<div class="single-image">
  <img width="70%" src="/images/2026-01-22/%5BSpring%20Boot%5D%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%201%5D%20SQL%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%B4%88%EA%B8%B0%ED%99%94/3.png" alt="image">
  <p>사진 2-1. <code>spring.jpa... = false</code> 로 했을 때 <code>ddl-auto</code> 와 <code>data.sql</code> 간의 실행 순서</p>
</div>
<div class="single-image">
  <img width="70%" src="/images/2026-01-22/%5BSpring%20Boot%5D%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%201%5D%20SQL%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%B4%88%EA%B8%B0%ED%99%94/4.png" alt="image">
  <p>사진 2-2. <code>spring.jpa... = true</code> 로 했을 때의 <code>ddl-auto</code> 와 <code>data.sql</code> 간의 실행 순서</p>
</div>

이 원리에 따르면, 만약 `ddl-auto: create` 이고, `schema.sql` 없이 오로지 `data.sql`만 존재하는 상황이라면, 앞서 말한 속성을 설정하지 않는 경우 스크립트가 먼저 실행되기에 `data.sql` 이 먼저 실행되는데, 이 단계에서는 `ddl-auto` 가 아직 실행되지 않아 아무런 테이블도 없는 상황이고, 없는 테이블에 데이터를 삽입하게 되니 에러가 발생한다. 반면 `spring.jpa.defer-...` 속성을 `true` 로 설정한 경우, `ddl-auto` 가 먼저 적용되어, 미리 정의된 엔티티 객체에 의해 스키마가 생성된다. 그 후에 `data.sql` 이 실행되어 데이터가 무사히 삽입된다. 

<div class="single-image">
  <img width="80%" src="/images/2026-01-22/%5BSpring%20Boot%5D%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%201%5D%20SQL%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%B4%88%EA%B8%B0%ED%99%94/5.png" alt="image">
  <p>사진 2-3. <code>spring.jpa... = false</code> 일 때의 <code>schema.sql</code> , <code>data.sql</code> , <code>ddl-auto</code> 간 실행 순서 및 전개 과정</p>
</div>
<div class="single-image">
  <img width="80%" src="/images/2026-01-22/%5BSpring%20Boot%5D%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%201%5D%20SQL%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4%20%EC%B4%88%EA%B8%B0%ED%99%94/6.png" alt="image">
  <p>사진 2-4. <code>spring.jpa... = true</code> 일 떄의 <code>schema.sql</code> , <code>data.sql</code> , <code>ddl-auto</code> 간 실행 순서 및 전개 과정</p>
</div>

여기서 `schema.sql` 이 추가되더라도 `spring.jpa.defer-datasource-initialization` 속성값과 `ddl-auto` 값에 따라 결과가 달라질 수 있으니 주의해야한다. 예를 들어, `ddl-auto` 는 `create-drop` , 그리고`spring.jpa.defer...` 속성이 없거나 그 값이 `false`인 경우, 처음에 `schema.sql` 에 의해 스키마가 생성되고, 그 후 `data.sql` 에 의해 생성된 테이블에 데이터가 삽입된다. 그러나 그 후 `ddl-auto: create-drop` 에서는 기존에 테이블이 있었다면 이를 모두 삭제하고 새로 생성하게 된다. 따라서 앞서 `data.sql` 로 삽입된 데이터들이 모두 날라가게 된다. 텅 빈 테이블만 남게 되는 것이다. 따라서 이 경우에는 반드시 `spring.jpa.defer-datasource-initialization` 속성값을 `true` 로 해줘야 원하는 의도대로 초기화된다. 

한 편, 스프링 공식 문서에 따르면, hibernate에 의한 초기화 방법과 스크립트 기반 초기화 방법을 이렇게 모두 사용하는 것은 권장하지 않는다고 한다. 그 이유에 대해선 명확히 언급하진 않았지만, 아마 앞서 말한 두 방식의 충돌로 인한 예기치 않은 데이터 유실, 그리고 자바 엔티티 객체 구성으로 생성되는 스키마와 `schema.sql` 에서 정의한 스키마 간 불일치가 발생할 수 있다는 점 등이 그 이유인 것 같다. 

아무튼, 정리하자면, `src/main/resources` 폴더에 `schema.sql` , `data.sql` 파일을 각각 생성하고 각 파일안에 스키마 및 데이터 삽입 쿼리문을 작성한 뒤, `application.yml` 파일에서는 각각 `spring.sql.init.mode: always` , `spring.jpa.defer-datasource-initialization: true` 속성값을 설정하면 된다. 그러면 스프링부트에 의해 자동으로 데이터베이스 초기화가 수행된다. 이는 특히 필자의 상황처럼 테스트 환경에서 유용하다. 필자는 Github Actions의 runner에서의 데이터베이스 초기화를 쉽게 하기 위해 `ddl-auto` 속성값을 `create-drop` 으로 하기도 하였다. 

## 그 외 스프링부트에서의 데이터베이스 초기화 관련 정보

- DBMS마다 각자 고유의 SQL 문법이 있어 서로 호환이 안될 때도 있을 것이다. 이를 대비해, 각 DBMS별로 `schema-{platform].sql` , `data-{platform}.sql` 파일들을 만들 수 있다. `{platform}` 자리에 원하는 DBMS 이름을 넣으면 된다. 예를 들면 `oracle`, `h2` , `mysql` 등이 있겠다. 이 때 `application.yml` 파일에도 `spring.sql.init.platform` 에 같은 이름을 써야 한다. 이 기능을 이용하면 상황마다 DBMS를 바꿔서 운영해야할 때 편리할 것이다.

# 상황별 데이터베이스 초기화 방식

로컬에서 테스트를 진행할 때, CI에서 runner와 같은 별도의 가상 머신에서 테스트 자동화를 진행해야할 때, 실제 운영 상황일 때 이렇게 3가지 경우가 있을 수 있다. 이 각각의 경우마다 데이터베이스 초기화 방식이 달라질 수 있다. 

로컬 환경에서는 사실 개발자가 직접 수동으로 미리 데이터베이스를 초기화하여 사용할 수도 있고, 아니면 앞서 언급했듯이 스프링부트에서 hibernate에 의한 초기화 또는 SQL 스크립트를 이용한 초기화 방식을 사용해도 문제는 없을 것이다. 

CI를 위해 Github Actions의 runner와 같이 별도의 가상 머신에서 테스트를 자동으로 구동시켜야 할 경우, 아주 쉬운 데이터베이스 초기화를 위해 hibernate에 의한 초기화 방식과 SQL 스크립트를 이용한 초기화 방식 모두 혼용하여 사용한다. `spring.jpa.hibernate.ddl-auto` 속성값을 `create-drop` 으로 설정함으로써, 매번 테스트를 할 때마다 테이블을 새로 생성하고 새로운 데이터를 주입하도록 하여 독립적이고 정확도 높은 테스트 환경을 꾸릴 수 있겠다. 또한 테스트를 모두 마치면 자동적으로 모든 데이터 및 테이블들이 삭제되기에 이 또한 테스트 상황에 적합하다. 이와 더불어, Github Actions에서 제공하는 runner를 이용하는 경우 매번 테스트할 때마다 새로운 가상 머신을 부여받기에 `create-drop` 속성값이 적합하다고 볼 수 있다. 

한 편, 실제 운영하는 상황이라면 절대 `ddl-auto` 값을 사용하면 안될 것이다. 이 앱을 사용하는 실제 유저들이 존재하는 단계이기 때문이다. `ddl-auto` 를 `create-drop` 으로 했다간, 실수로 앱을 재시작하는 경우 모든 유저들의 데이터가 날라가기에 절대 사용해선 안될 것이다. 그리고 실제 운영하는 상황이라면 따로 테스트 데이터가 필요한 것도 아닐 것이라서 스크립트 기반 초기화도 필요없을 수도 있다. 

이전에 필자가 작성한 글인 "[[Spring Boot] 민감한 환경설정 값 & 설정 파일 분리](/spring/spring-boot-%EB%AF%BC%EA%B0%90%ED%95%9C-%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95%EA%B0%92-%EB%B0%8F-%EC%84%A4%EC%A0%95-%ED%8C%8C%EC%9D%BC-%EB%B6%84%EB%A6%AC/)” 글에서도 소개했지만, 각 상황별로 필요한 환경설정 값들이 다를 경우 `application-local.yml` , `application-ci.yml` , `application-prod.yml` 과 같이 각 상황별로 환경설정 파일을 분리하여 운용하면 더 편리할 것이다. 로컬 또는 CI 환경에서는 `spring.sql.init.mode` 속성값을 `always` 로 하여 스크립트 기반 초기화를 활성화하고, 프로덕션 환경에서는 해당 속성값을 `never` 로 두고 `ddl-auto` 를 설정하지 않도록 하여 각 상황에 알맞은 설정값들을 달리 할 수 있겠다. 

# 글을 마치며

이 글에서는 CI 테스트 자동화 구축을 쉽게 하기 위해 연동될 데이터베이스 초기화 방식을 스프링부트 내에서 자동으로 수행하게끔 SQL 스크립트 방식으로 구성하는 방법에 대해 살펴보았다. SQL 스크립트 방식을 이용하면 CI 환경에서 쉽게 DB와 연동하고 초기화하여 테스트를 원활히 진행할 수 있을 것이다. 

다음에는 Github Actions 기반 CI 테스트 자동화 구축을 위해 `application-secret.yml` 과 같이 민감한 설정값들이 있을 때 이를 유출 문제 없이 주입하는 방법에 관해 다룰 것이다. 그 후에는 본격적으로 Github Actions에서 테스트용 DB 인프라를 마련하는 방법에 대해 살펴보고자 한다. 

---
References

[1] [Database Initialization :: Spring Boot](https://docs.spring.io/spring-boot/how-to/data-initialization.html)

[2] [Spring Boot 초기 데이터 설정 방법 정리(data.sql, schema.sql)](https://wildeveloperetrain.tistory.com/228)