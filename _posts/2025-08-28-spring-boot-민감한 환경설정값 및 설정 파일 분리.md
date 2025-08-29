---
title: "[Spring Boot] 민감한 환경설정 값 & 설정 파일 분리"
category: "Spring"
tag: ["Spring", "Spring Boot", "환경설정", "dotenv", "Dotenv", ".env"]
---

스프링부트로 개발 시 외부에 함부로 유출되어선 안되는 민감한 환경 설정값이 있을 경우, 이를 별도의 파일로 분리하여 `.gitignore` 에 등록한 후, 그 값을 외부에서 가져오도록 하는 방식을 취하는 것이 좋을 것이다. 한 편, 설정값들을 개발 환경과 실제 배포 (프로덕션) 환경에서 차이를 둬야 할 수도 있다. 이럴 땐 각각의 환경에 맞는 설정 파일들을 별도로 만들어 관리하는 것이 좋다. 

이번 글에서는 스프링부트에서 민감한 환경 설정 값을 외부에 노출하지 않고 별도의 파일로 분리하는 방법과, 개발 및 프로덕션 이 각각의 환경에 맞는 환경 설정 파일들을 별도로 만들고 이를 사용하는 방법에 대해 살펴보도록 하겠다. 

# `.env` 파일을 이용하여 민감한 환경설정 값 별도로 관리하기

스프링부트 프로젝트에서 원래라면 환경설정 값들을 application.yml (또는 application.properties) 파일에 명시하여 관리했을 것이다. 하지만 open API의 key라든가 JWT의 secret key 등 외부에 노출되어선 안되는 민감한 설정 값들까지 포함시키고 그대로 Github repo에 push할 경우 민감한 값들이 그대로 외부에 노출된다. 따라서 이를 미연에 방지하기 위한 방법 중 하나로 `.env` 파일을 이용하는 것이다. 이 파일에 `.properties` 파일에 값을 적는 방식과 똑같이 민감한 환경 설정값들을 적어 여기서 한꺼번에 관리하는 것이다. 스프링부트 프로젝트에서는 루트 폴더에 `.env` 파일을 생성한다. 

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
  <img width="20%" src="/images/2025-08-28/spring-boot-%EB%AF%BC%EA%B0%90%ED%95%9C%20%ED%99%98%EA%B2%BD%EC%84%A4%EC%A0%95%EA%B0%92%20%EB%B0%8F%20%EC%84%A4%EC%A0%95%20%ED%8C%8C%EC%9D%BC%20%EB%B6%84%EB%A6%AC/1.png" alt="image">
  <p>사진 1-1. 민감한 환경설정 값들을 관리할 .env 파일을 프로젝트 루트 폴더에 생성한 모습</p>
</div>

예를 들어 `.env` 파일에 다음과 같이 민감한 설정값들을 넣을 수 있겠다. 여기서는 DB 연결 관련 설정값들을 넣어보았다. 

```java
DB_USERNAME=...
DB_PASSWORD=...
DB_URL=jdbc:mariadb://127.0.0.1:3308/study_scheduling
```

코드 1-1. `.env` 환경 설정 파일 내용 예시.

이런 민감한 설정값들은 이렇게 `.env` 에서 관리한다. 이 값들을 바로 자바 코드로 가져올 수도 있겠지만 대개는 이를 application.yml 설정 파일로 가져오는 방식을 취한다. 이러한 과정을 자동으로 처리해줄 의존성이 필요하다. 필자는 build.gradle의 dependencies에 다음의 의존성을 넣었다. 

```java
implementation 'io.github.cdimascio:dotenv-java:3.2.0'
```

코드 1-2. 자바 및 스프링 진영에서 `.env` 파일 내 설정값들을 처리할 의존성. [https://github.com/cdimascio/dotenv-java](https://github.com/cdimascio/dotenv-java) 참고.

사실 이것말고도 다른 dotenv 라이브러리가 있긴 했지만 필자는 위 라이브러리가 잘 작동해서 이걸로 택하였다. 

`.env` 파일 내 설정값들을 application.yml 파일로 가져올려면 `${...}` 문법을 이용하여 `.env` 파일 내 프로퍼티명을 명시하면 된다. 

```yaml
spring:
  application:
    name: QuartzStudy
  config:
    import: optional:file:.env[.properties]
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: ${DB_URL}
    username: ${DB_USERNAME}
    password: ${DB_PASSWORD}
  jpa:
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MariaDBDialect
  quartz:
    # 앱 실행 시 스케줄링 자동 시작 여부 설정.
    # 기본값은 true로, 앱 샐행과 동시에 자동으로 스케줄링이 되길 원한다면
    # 굳이 설정값을 작성하지 않아도 됨.
    auto-startup: true
```

코드 1-3. application.yml 파일에 `.env` 파일 내 설정값들을 가져온다. 

위 코드에서 볼 수 있듯, 먼저 `spring.config.import` 프로퍼티 값에 `optional:file:.env[.properties]` 값을 넣음으로써 `.env` 파일을 가져오도록 한다. 해당 프로퍼티 값의 의미는 `.env` 또는 `.env.properties` 파일이 존재한다면(optional) 이를 가져오고, 없더라도 에러는 발생하지 않도록 한다는 뜻으로 보면 되겠다. `optional` 이 없다면 `.env` 파일이 없을 경우 앱 시작 시 오류가 발생한다고 한다. 

이렇게 하면 민감한 설정값들을 application.yml 파일에 노출하지 않으면서도 설정값들을 프로젝트에 적용할 수 있게 된다. Github repo에 업로드할 땐 반드시 `.env` 파일명 자체를 `.gitignore` 파일에 추가하여 해당 파일이 업로드 되지 않게끔 방지해주자. 

# 개발 환경에 따른 설정 값 분리하기

한 편, 개발 시 필요한 환경 설정 값들에 대해 개발 환경과 프로덕션 환경, 또는 테스트 환경 이렇게 각 환경마다 그 값을 다르게 줘야할 수도 있다. 이 경우, `application-{profile}.yml` 과 같은 방식으로 각 환경에 맞는 환경 설정 파일을 만듦으로써 이를 분리할 수 있다. 

예를 들어 다음과 같은 형태로 설정 파일들을 여러 개 만들어 분리할 수 있다. 

```
project/
├───java
|	   // 생략
│
└───resources
    │   application-dev.yml
    │   application-prod.yml
    │   application-secret.yml
    │   application.yml
```

코드 2-1. application.yml 설정 파일 분리 예시

위 코드는 필자가 예전 팀프로젝트를 리팩토링할 때 실제로 사용한 설정 파일 분리 방법이었다. 각 파일들은 아래와 같은 용도로 사용하였다. 

- application.yml : 개발, 프로덕션 환경 상관없이 모든 환경에서 공통적으로 적용시킬 환경 설정 값들의 모임
- application-dev.yml : 로컬 개발 환경에서만 사용할 환경 설정 값들을 적은 곳.
- application-prod.yml : 프로덕션 환경, 즉 실제 온라인으로 배포할 때 사용할 환경 설정값들을 적어 놓은 파일.
- application-secret.yml : application.yml 파일처럼 모든 환경에서 공통적으로 사용되지만 외부에 노출되어선 안되는 민감한 값들을 모아 정의한 곳.

그리고 application.yml 파일에는 다음과 같이 적어줬다. 

```yaml
spring:
  profiles:
    include: secret
    
    # dev, prod
    active: dev
```

코드 2-2. application.yml 내용 중 일부.

`spring.profiles.include` 속성은 `application-{profile}.yml` 형태의 이름을 가지는 파일들 중 명시된 프로필 파일을 포함시키겠다는 의미이다. 또한 위 코드에서는 `spring.profiles.active` 속성값으로 `dev` 를 지정하였는데, 이는 현재 `application-{profile}.yml` 형태의 이름을 가지는 설정 파일들 중 어떤 설정 파일을 적용시킬 것인지 선택할 때 사용된다. 즉 여기서는 `application-dev.yml` 파일만을 적용하겠다는 것이다. 따라서 위 코드 2-2의 경우, `application-secret.yml` 파일은 무조건 포함시키고, `application-prod.yml` 설정 파일은 제외한 채 `application-dev.yml` 파일만 적용시키겠다는 뜻이다. 이는 로컬 컴퓨터에서 개발할 때 사용할 수 있는 설정이다. 만약 프로덕션 환경이 되면 `spring.profiles.active` 속성값에 `prod` 를 대신 넣으면 된다. 

예전 팀프로젝트 당시에는 개발 환경에서의 DB 관련 설정값과 배포 환경에서의 그 값이 달랐다. 그럼에도 위와 같은 방법이 있었음을 몰라서 다음처럼 하나의 파일에 몰아 넣어 사용했었다. 

```yaml
# mariadb server connect (cloudtype)
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://{배포 url}/private
spring.datasource.username= ...
spring.datasource.password= ...

# mariadb server connect (local)
#spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
#spring.datasource.url=jdbc:mariadb://localhost:3308/forklog_test
#spring.datasource.username=...
```

코드 2-3. 

위와 같이 한 파일에 작성 후, 개발 환경에서는 배포 환경 설정 값들을 주석 처리하고 그 반대 상황에서도 똑같이 주석 처리하는 방식으로 사용했었다. 하지만 앞서 dev, prod 파일로 분리하여 관리하면 위와 같이 번거롭게 수많은 설정값들을 주석 처리할 필요가 없어진다. 

```yaml
spring:
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: jdbc:mariadb://localhost:3308/forklog_dev
    username: ...
```

코드 2-4. application-dev.yml 예시

```yaml
spring:
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: # ...
    username: # ...
    password: # ...
```

코드 2-5. application-prod.yml 예시

위 두 코드처럼 개발 및 배포 환경으로 나눠 설정값들을 따로 관리할 수 있다. 

민감한 값들이 들어 있는 `application-secret.yml` 파일은 `.gitignore` 파일에 등록하여 Github repo에 업로드되지 않도록 한다. 다만 필자의 경우 dev, prod 파일도 `.gitignore` 에 등록하여 업로드를 방지하도록 하였는데, 이 파일들에 대해서는 민감한 값이 하나도 없다면 굳이 비공개로 할 필요는 없을 것으로 보인다. 이에 대해선 개발자가 그때그때 상황에 따라 판단하면 되겠다. 

한 편, `application-secret.yml` 파일을 통해서도 사실상 민감한 환경 설정 값들을 외부에 노출하지 않으면서도 프로젝트 내에서 사용할 수 있는 또 다른 방법임을 알 수 있다. 앞서 소개한 `.env` 방식과 비교하여 어떤 방식을 선택하여 민감한 정보를 숨길지는 이것도 상황에 따라 판단하면 되겠다. 

한 편, 이러한 사실을 이용하면 테스트 환경만 별도로 구성할 수도 있다. `src/test` 폴더에서 테스트 코드를 작성하여 테스트를 하고자 할 때, 개발 및 배포 환경과는 전혀 다른 값들로 테스트하고자 할 때에는 `src/test/resources` 폴더 내에 `application-test.yml` 과 같이 설정 파일을 따로 만들어 관리할 수 있다. 

필자의 경우, 팀 프로젝트 내 일부 중복되는 코드들이 있어 이를 안전하게 리팩토링하기 위해 그 전에 관련 테스트 코드를 작성하여 테스트를 진행해봐야 했었다. 이 때, 테스트 환경에서도 DB 연결이 필요하였는데, 실제 개발할 때 사용하던 DB 내 데이터들을 테스트로 인해 오염시킬 순 없어서 별도의 테스트용 DB를 구성했어야 했다. 이로 인해, dev, prod 설정 파일과는 전혀 다른 테스트용 설정 파일을 만들어서 테스트를 했어야 했다. 이 때 앞서 말한 방식대로 `application-test.yml` 파일을 만들어 별도로 운영했었다. 

```yaml
spring:
  datasource:
    driver-class-name: org.mariadb.jdbc.Driver
    url: jdbc:mariadb://localhost:3308/forklog_test
    username: ...
  jpa:
    properties:
      hibernate:
        show_sql: false
        format_sql: false
        use_sql_comments: false
        dialect: org.hibernate.dialect.MariaDBDialect
    hibernate:
      ddl-auto: create-drop

feign:
  logger:
    # 사용 가능한 값들: NONE, BASIC, HEADERS, FULL
    level: FULL

secret-key: "..."
```

코드 2-6. `src/test/resources` 폴더에 생성한 `application-test.yml` 파일 

이를 이용하면 개발 환경에서의 DB는 전혀 건드리지 않으면서도 별도로 테스트를 수행할 수 있게 된다. 

`src/test/java` 패키지 내부에서 테스트 코드 작성 시 `@SpringBootTest` 를 이용하여  `src/main` 패키지에 작성한 전체 스프링 컨텍스트를 가져올 때에는 먼저 `src/test` 패키지 내부에 있는 설정 파일을 먼저 찾고, 없다면 `src/main` 패키지 내에서 찾는 구조라고 한다. 필자가 마주했던 상황에서는 두 패키지 모두 각각 `src/main/resources/application.yml` 파일과 `src/test/resources/application-test.yml` 파일 모두 존재했던 상황이었다. 따라서 테스트 시에는 후자가 적용되어 실행된다. 그럼에도 필자는 `application-test.yml` 파일 적용을 명시적으로 나타내고자 다음과 같이 테스트 코드에 `@ActiveProfiles` 어노테이션을 적용했었다. 

```java
@SpringBootTest
@AutoConfigureMockMvc
@Slf4j
@ActiveProfiles("test")
@Transactional  // 테스트 후 삽입 된 데이터들에 대해 자동 롤백이 가능하도록 설정함.
class JwtAuthenticationFilterTest { ... }
```

코드 2-7. 테스트 코드에 특정 프로필 적용.

`@ActiveProfiles("test")` 라고 명시하였고, 프로젝트 폴더 전반에 `application-test.yml` 파일이  `src/test/resources` 폴더에만 있으므로 해당 테스트 코드는 실행 시 해당 설정 파일만을 가져와 테스트를 진행하게 된다. 

# 번외) 환경 설정 값들을 자바 클래스에 그대로 매핑하기

한 편, application.yml 파일에 설정한 전체 또는 일부 설정값들을 모두 그대로 자바 클래스로 매핑하여 가져올 수 있다. 원래는 `@Value` 어노테이션을 이용하여 특정 환경 설정값을 자바 클래스 내 특정 필드와 매핑하는 방식이 있는데, 이 대신 자바 클래스 단위로 `@ConfigurationProperties` 어노테이션을 부여하면 해당 자바 클래스에 여러 개의 많은 환경 설정 값들을 그대로 매핑할 수 있다. 

필자가 스프링 부트에서 간단하게 사용할 라이브러리인 [Spoon Suits](https://github.com/JeroCaller/Spoon-Suits) 를 제작할 때, JWT를 이용한 인증 과정에서 JWTAuthenticationFilter와 JWTAuthenticationProvider가 어떤 프로젝트에서 사용하던 그 내용이 거의 같아서 이를 라이브러리화할 때였다. JWT 토큰에 사용될 access, refresh 토큰의 만료 시간, issuer 등의 여러 정보들을 application.yml 파일에 작성할 수 있도록 하여 개발자가 언제든 그 값을 변경할 수 있도록 하는 구조로 설계했었다. 

```yaml
jwt:
  issuer: kimquel@good
  secretKey: this-is-for-jwt-secret-key-which-has-very-long-length
  token:
    access:
      expiry: PT5M
      cookie-name: access-token
    refresh:
      expiry: PT10M
      cookie-name: refresh-token
```

코드 3-1. application.yml 설정 파일에 JWT 관련 설정 값 예시. 

위와 같이 jwt 설정 프로퍼티 구조를 먼저 정했었다. 그리고 이를 그대로 자바 클래스로 매핑하여 가져오도록 하기 위해 다음과 같은 클래스를 작성하였다. 

```java
@Getter
@Setter
@ToString
@Component
@ConfigurationProperties(prefix = "jwt")
public class JwtProperties {

    private String issuer;
    private String secretKey;
    private Token token;

    @Getter
    @Setter
    @ToString
    public static class Token {

        private Access access;
        private Refresh refresh;

        @Getter
        @Setter
        @ToString
        public static class Access {

            private Duration expiry = Duration.ofDays(1);
            private String cookieName = "ACCESS-TOKEN";

        }

        @Getter
        @Setter
        @ToString
        public static class Refresh {

            private Duration expiry = Duration.ofDays(7);
            private String cookieName = "REFRESH-TOKEN";

        }

    }

}
```

코드 3-2. 코드 3-1에서 정의한 설정 프로퍼티 구조를 그대로 자바 클래스로 가져온다. 

위 코드에서 볼 수 있듯, `@ConfigurationProperties(prefix = "jwt")` 어노테이션을 이용하면 설정 파일 내 설정 값들을 그대로 클래스에 매핑할 수 있다. 물론 자바 클래스 내부 구조도 application.yml 파일 내 정의한 구조와 똑같이 정의해야 오류없이 가져올 수 있다. 해당 어노테이션에 `prefix = "jwt"` 와 같은 속성값을 작성하면 application.yml 파일 내에서 `jwt` 로 시작되는 모든 프로퍼티들을 가져올 수 있게 된다. 

`@ConfigurationProperties` 어노테이션은 기본적으로 Java bean의 setter property를 이용하여 환경 설정 값을 가져온다고 한다. 그래서 위 코드 3-2에서도 모두 `@Setter` 를 이용한 것이다. 

위와 같이 `@ConfigurationProperties` 방식을 이용하면 `@Value` 를 사용했을 때보다 환경 설정 값을 좀 더 객체지향적으로 가져와 다룰 수 있게 된다. 

<style>
  details {
    background-color: #434555;
    margin-bottom: 1em;
  }
  details > summary {
    cursor: pointer;
    margin: 0.5em;
  }
  details a {
    text-decoration: none;
    margin: 0 0.5em;
  }
  details > div {
    padding: 0.5em;
  }
</style>
<details id="참고-1">
<summary>
    <strong>
        <i>여담) setter 바인딩 대신 생성자 바인딩으로 전환하려다 실패한 경험.</i>
    </strong>
    <a href="#참고-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
setter 바인딩에 대한 여담을 말하자면, 필자는 `@Setter` 를 사용하면 코드 내에서 필드값을 예기지 않게 바꾸는 실수를 할 수도 있을 것 같아서 setter 대신 생성자로 바인딩하는 방법에 대해 연구했었다. 그래서 처음에는 `@Setter` 를 사용하지 않고 대신 `@RequiredArgsConstructor` 를 사용하고, 필드도 이에 맞게 final로 선언했었다. 그러나 앱 실행 시 다음과 같은 에러가 발생했었다.
    
```
Failed to bind properties under 'jwt' to com.jerocaller.libs.spoonsuits.web.jwt.JwtProperties:
    
    Property: jwt.issuer
    Value: "kimquel@good"
    Origin: class path resource [application.yml] - 6:11
    Reason: java.lang.IllegalStateException: No setter found for property: issuer
```
    
코드 3-3. 생성자 바인딩 방식 사용 시 에러.
    
그래서 이에 대해 조사하다가, 환경 설정 값과의 바인딩 목적으로 사용할 생성자 메서드에 `@ConstructorBinding` 어노테이션을 적용하면 된다는 글을 보고, `@RequiredArgsConstructor` 대신 직접 생성자 메서드를 정의하고 해당 어노테이션을 적용해보기도 했었다. 그럼에도 위와 똑같은 에러가 발생하며 바인딩되지 않았었다. 그 후에도 해결책을 찾지 못해 결국에는 다시 `@Setter` 방식으로 돌아올 수 밖에 없었다. 

2025-08-29 추가 - 최근 이 문제를 해결하여 생성자 바인딩 방식으로 전환할 수 있었다. 이에 대해선 다음을 참고. 

[[Troubleshooting] application.yml 설정 값 및 pojo 생성자 바인딩 문제](https://github.com/JeroCaller/Spoon-Suits/issues/76)

</div>
</details>

# 글을 마치며…

이번 글에서는 `.env` 파일을 이용하여 민감한 환경설정 값들을 별도로 분리하여 관리하는 방법과, 개발, 배포, 테스트 등 여러 환경에서 사용할 환경 설정 값들을 `application-{profile}.yml` 형태로 분리하여 관리하는 방법에 대해 살펴보았다. 또한 `@Value` 대신 `@ConfigurationProperties` 를 이용하여 `application.yml` 파일에 적은 환경 설정 값의 일부 또는 전체를 곧바로 자바 클래스에 바인딩시켜 가져오는 방법에 대해서도 살펴보았다. 이러한 방법들을 알고 있으면 각 환경별로 설정 값들을 유연하게 관리할 수 있고, 민감한 설정값들을 외부에 노출하지 않도록 방지하기가 수월해질 것이다. 

---

References

[1] [Externalized Configuration :: Spring Boot](https://docs.spring.io/spring-boot/reference/features/external-config.html)

[2] [[Spring] 하나의 YAML을 여러 개로 나누어 환경 분리하기](https://velog.io/@devholic/Spring-YAML-%EC%97%AC%EB%9F%AC-%EA%B0%9C-%EC%93%B0%EA%B8%B0)

[3] [스프링 application.yml 파일 dev, prod, secret 환경 설정 분리방법](https://skorea6.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81-applicationyml-dev-prod-secret-%ED%99%98%EA%B2%BD-%EC%84%A4%EC%A0%95-%EB%B6%84%EB%A6%AC%EB%B0%A9%EB%B2%95)

[4] dotenv

[[Spring] 스프링 부트 프로젝트에서 dotenv 환경변수 파일 사용하기](https://gengminy.tistory.com/24)

[5] spring dotenv

[spring .env 에서 yaml 에 주입받기](https://kim-zzaisang.tistory.com/42)