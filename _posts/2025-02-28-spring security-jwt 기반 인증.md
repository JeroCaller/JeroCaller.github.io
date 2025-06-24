---
title: "[Spring Security] JWT 기반 인증"
category: "Spring"
tag: ["Spring", "Spring boot", "Spring Security", "Filter", "Servlet", "인증", "인가", "보안", "Authentication", "Authorization", "Auth", "JWT", "Token", "Access Token", "Refresh Token"]
---

이 글에서는 이전 글인 [“[Spring Boot] 보안과 Spring Security”](/spring/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC-spring-security/)을 기반으로 하니 참고바랍니다. 

# 토큰 기반 인증

이전 글인 [“[Spring Boot] 보안과 Spring Security”](/spring/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC-spring-security/)에서는 세션 기반 인증에 대해 살펴보았다. 그런데 인증 방식에는 다양하게 존재한다고 하는데, 그 중 하나로 토큰 기반 인증이라는 것이 있다. 이는 말 그대로 토큰을 이용하여 인증을 수행하는 방법을 의미한다. 토큰은 클라이언트를 고유하게 식별하기 위한 값이며, 보통 인간이 해석할 수 없는 문자열 형태로 나열되어 표시된다(어쩌면 당연한 것이, 인증이라는 보안적 측면을 고려해야 하니, 사용자 정보가 담긴 토큰을 인간의 눈으로 바로 해석가능하게 하면 안될 것이다). 

그러면 토큰 기반 인증을 위해 클라이언트와 서버 사이에서 어떻게 토큰으로 통신하는지 아래 그림과 함께 살펴보겠다. 

![그림 1-1. 토큰 기반 인증 방식 사용 시 인증을 위해 클라이언트와 서버 간 토큰 전송 방식.](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/1.png)

그림 1-1. 토큰 기반 인증 방식 사용 시 인증을 위해 클라이언트와 서버 간 토큰 전송 방식.

1. 클라이언트가 서버에 아이디 및 비밀번호와 같은 사용자 정보를 HTTP 요청에 실어 서버에 전송하여 인증을 요청한다. 
2. 인증 요청을 받은 서버에서는 먼저 클라이언트가 전송한 사용자 정보와 DB 내 저장되어 있는 정보와 일치하는지를 확인하여 인증 절차를 거친다. 인증에 성공하면 해당 사용자의 정보를 토대로 고유한 토큰을 생성하여 이를 클라이언트에 전송한다. 
3. 이 후, 인증에 성공하여 서버로부터 토큰을 받은 클라이언트는 이를 저장해두었다가 이후 인증이 필요한 리소스 요청 시 해당 토큰을 함께 요청에 실어 서버에 전송한다. 
4. 서버에서는 요청으로 넘어온 토큰이 유효한지를 검증하고, 검증 완료 시 클라이언트의 요청을 처리, 응답한다. 이 때, 이미 요청으로 넘어온 토큰 자체에 사용자의 인증 정보가 있으므로, 이를 추출하여 사용자의 인증 정보를 확인하면 되기에 또 다시 DB(또는 세션)로부터 사용자 정보를 얻어오는 작업이 필요하지 않다. 

이러한 토큰 기반 인증에는 세션 기반 인증과 비교했을 때 다음의 주요한 특징들을 가지고 있다.

1. 무상태성 (Stateless) - 세션 기반 인증에서는 사용자의 정보를 서버 메모리에 저장하여 기억하는 방식으로, stateful한 방식이다. 이로 인해 사용자가 로그아웃하거나 세션이 만료되지 않는 한 계속 사용자의 정보를 서버 메모리에 저장하고 있어야 하기에 서버의 자원을 계속 소모하게 되며, 이는 서버의 부담으로 이어진다. (AWS와 같은 클라우드 서비스 제공업체를 이용하여 서버를 구축하는 경우라면 그만큼 금전적 비용으로 이어질 것이다) 그러나 토큰 기반 인증은 사용자의 정보를 서버가 아닌 클라이언트(브라우저)에 저장하는 방식이므로 서버 입장에서는 사용자의 정보를 서버에 기억하지 않아도 된다는 stateless 성격을 띈다. 이로 인해 서버의 부담을 줄일 수 있게 된다.
2. 확장성 - 이러한 무상태성은 서버의 확장성을 높여주는 기능도 하게 된다. 서버가 더 이상 인증을 위한 사용자의 정보를 서버 내에 저장해도 되지 않기에 서버를 확장해도 인증에 아무런 문제가 없게 된다. 최근에는 MSA(Microservice Architecture) 방식을 택하여 원래는 하나의 서버에 포함시킬 여러 가지 서비스들을 여러 개의 독립적인 서버들로 분산시키는 방식을 택하고 있다고 한다. 이러한 MSA 방식은 하나의 서버에 문제가 생겨도 다른 서버에서 대신 기능을 처리할 수 있어 고객에게 제공하는 웹 사이트 전체가 다운될 가능성이 작아지며, 기존 서버가 수용하기 힘들 정도로 사용자들이 많아지면 서버만 추가해도 기존 기능에 문제가 없는 등의 여러 이점이 있다고 한다. 그런데 세션 기반 인증 방식을 사용하면 다른 서버의 서비스를 사용하기 위해선 일관성을 위해 결국 모든 서버가 해당 사용자의 정보를 기억하게끔 설계하거나, 아예 중앙 서버를 하나 둬서 관리하든가 해야 하는데, 이 모두 비효율적이거나 자원 소모, 비용이 드는 단점이 존재한다. 그러나 stateless한 토큰 기반 인증 방식을 사용하면 서버에서 사용자의 정보를 기억하지 않아도 되기에 분산 서버 구축 및 서버 확장을 문제 없이 수행할 수 있게 된다. 게다가 토큰 기반 인증에서는 하나의 토큰만으로도 각자 분산된 서버들에서 각각 제공하는 서비스들을 쉽게 이용할 수 있다는 장점도 있다. 여기서 더 나아가 구글 등의 사이트에서는 토큰 기반 인증을 사용한다고 하는데, 이를 통해 하나의 같은 토큰만으로도 다른 사이트에서도 접근 가능하여 여러 서비스들을 쉽게 사용할 수 있다고 한다. → 고객의 입장에서는 서비스 이용의 확장성도 확보한다고 말할 수 있겠다. 
3. 무결성 - 토큰은 한 번 발급되면 그 정보를 변경할 수 없다. 만약 변경하더라도 서버에서 바로 유효하지 않은 토큰으로 판단하기에 토큰의 무결성을 보장하게 되고, 이는 사용자의 보안에 직결된다. 

# JWT(JSON Web Token)

토큰 기반 인증에서 자주 사용되는 토큰은 JWT(JSON Web Token) 라고 한다. JWT는 그 이름에도 포함되어 있듯, 클라이언트와 서버 간 인증 정보를 교환하기 위한 JSON 형태의 토큰이다. 복호화되어 있는 경우에는 JSON 형태로 사용자 정보가 담겨져 있으며, 암호화된 경우 다음과 같은 구조의 문자열로 구성된다. 

```
XXXXXX.YYYYYY.ZZZZZZ

헤더(Header).내용(Payload).서명(Signature)
```

## JWT의 구조

### 헤더(Header)

헤더에는 토큰의 타입과 해싱 알고리즘(Hasing Algorithm)의 타입을 지정한다. 다음은 그 예이다.

```
{
  "alg": "HS256",
  "typ": "JWT"
}
```

alg에는 해싱 알고리즘을 지정하며, 위 예시에서는 HS256이라는 알고리즘을 사용하여 암호화하겠다는 뜻이다. 또한 typ에는 토큰의 타입을 지정하며, 여기선 JWT에 대해 다루기에 JWT라는 값이 쓰여져 있다. 

### 내용(Payload)

내용(Payload)에는 토큰에 담을 정보들을 포함시키는 곳이다. 이곳에 포함되는 속성들은 각자 클레임(Claim)이라고 불리며, 다음의 세 가지로 분류된다.

- 등록된 클레임 (Registered Claim) : 토큰의 정보를 담는 데 사용되며, 속성의 key 이름이 이미 정해져 있는 클레임을 의미한다. 등록된 클레임에는 다음과 같이 정의되어 있다.

| 이름 | 설명 |
| --- | --- |
| iss | Issuer, 즉 토큰의 발급자. 주로 문자열 또는 URI를 포함하는 대소문자를 구분하는 문자열로 구성된다.  |
| sub | Subject. 토큰의 제목. 사용자를 고유하게 구분할 수 있는 ID와 같은 값을 넣기도 한다.  |
| aud | audience. 토큰의 수신자를 의미한다.  |
| exp | expiration. 토큰의 만료 시간을 의미. 시간은 NumericDate 형식으로 지정해야 하며, 현재 시간 이후로 설정해야 한다.  |
| nbf | Not BeFore. 토큰의 활성 날짜를 의미. NumericDate 형식으로 지정해야 한다. 지정된 날짜를 지나기 전까지 토큰이 처리되지 않는다. |
| iat | Issued At. 토큰 발급 시각을 의미.  |
| jti | JWT ID. 토큰의 고유 식별자로, 주로 일회용 토큰에 사용하며, 중복 처리를 위해 사용된다. |

위에 나열된 모든 클레임들을 사용해야하는 것은 아니다. 

- 공개 클레임(Public Claim) - 정보가 공개되어도 되는 클레임을 의미하며, 개발자가 속성의 이름을 마음대로 지을 수 있으나 충돌이 발생하지 않도록 지어야 한다. 보통 클레임의 이름을 URI로 짓는다고 한다.
- 비공개 클레임(Private Claim) - 정보가 공개되어선 안되는 클레임. 클라이언트와 서버 간 상호 합의 하에 통신할 때 쓰임. 등록된 클레임과 공개 클레임이 아닌 클레임을 의미하기도 한다. 등록된 클레임도 아니고, URI의 형태를 가지는 이름도 아니라면 그 클레임은 비공개 클레임으로 보면 된다.

> -
> 
> 
> [RFC 7519: JSON Web Token (JWT)](https://datatracker.ietf.org/doc/html/rfc7519#section-2)
> 
> 위 사이트에 따르면 NumericDate는 UTC 기준으로 `1970-01-01T00:00:00` 로부터 현재 시각까지 흐른 시간의 초(Seconds)로 표현된다. (참고로 저 시각으로부터 지금까지 얼마나 시간이 흘렀는지를 초로 표현한 것을 epoch라 한다) 
> 아래에 코드와 함께 설명할 때 보이겠지만 자바에서는 `java.util.Date(long date)` 와, epoch 값을 나타내는 `System.currentTimeMillis()` 를 함께 이용하면 된다. 
> 

다음은 payload의 예이다.

```
{
  "sub": "1234567890",  // 등록된 클레임
  "name": "John Doe", // 비공개 클레임
  "admin": true,  // 비공개 클레임
  "https://example.com/username": "john_doe",  // 공개 클레임
  "iat": 1516239022  // 등록된 클레임 
}
```

### 서명(Signature)

서명(Signature)은 토큰의 조작, 변경 여부를 확인하는 용도로 사용되며, 인코딩된 헤더, payload, 그리고 여기에 더해 비밀키(secret key, 서버에서 지정하며, 보안상의 이유로 서버 내에서만 보유, 사용된다)와 함께 헤더에 명시한 해싱 알고리즘을 통해 생성된다. 

## jwt.io에서 확인하기

[jwt.io](http://jwt.io) 사이트에서는 JWT가 인코딩된 값과 디코딩된 값을 확인해볼 수 있다. 다음은 필자가 직접 JWT 기반 인증을 구현하여 얻은 JWT를 해당 사이트에서 디코딩한 결과이다. (필자가 구현한 JWT 기반 인증 관련 코드는 아래에 후술할 예정)

![사진 1-1. 필자가 구현한 JWT 기반 인증을 통해 얻은 JWT 토큰을 해당 사이트에서 디코딩한 결과](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/2.png)

사진 1-1. 필자가 구현한 JWT 기반 인증을 통해 얻은 JWT 토큰을 해당 사이트에서 디코딩한 결과

해당 사이트에서는 주어진 JWT를 디코딩하는 기능 뿐만 아니라 반대로 인코딩 기능도 있으므로 JWT에 대해 더 살펴보고자 하거나 디버깅해야 할 일이 있을 때 활용하면 유익할 것이다. 

# Access Token과 Refresh Token

앞에서 토큰 인증 방식과 대표적으로 사용되는 토큰인 JWT에 대해 살펴보았다. 그런데 이러한 토큰을 가지고 인증을 구현할 때 하나의 토큰만 사용하는 방식과 두 개의 토큰을 사용하는 방식이 있다. 하나의 토큰만 사용하는 방식에서는 토큰이 인증 용도로 사용되기에 Access Token이라 불린다. 그리고 두 개의 토큰을 사용하는 방식에서 하나는 인증 용도의 Access Token이고, 또 하나는 Access Token이 만료되었을 때 새로운 Access Token을 발급하기 위한 용도의 Refresh Token이다. 이 Refresh Token은 Access Token과는 별개로 취급되며, Access Token은 인증 용도이기에 서버에 저장되지 않는 반면, Refresh Token은 만료된 Access Token의 재발급을 위한 용도이므로 이 값은 DB에 저장되어 사용된다. 

Refresh Token이 DB에 저장되어 사용된다는 점에서 토큰 기반 인증의 장점인 stateless를 잃는 것처럼 보인다. 토큰은 기본적으로 서버의 메모리는 물론 DB에도 저장되지 않기 때문이다. 하지만 이러한 Refresh Token이 등장한 이유가 있다. 

만약 Access Token 하나만 사용한다고 가정해보자. 그러면 여기서 떠올릴 수 있는 의문은, 이 토큰의 만료 시간을 어느 정도로 해야 적절한가, 이다. 너무 짧게 잡으면 토큰이 탈취되더라도 금세 만료되기에 탈취자는 해당 사용자의 계정을 악용할 기회가 줄어들거나 악용하더라도 오랜 기간 악용할 수는 없기에 보안성 측면에서는 좋겠다. 그러나 사용자 입장에서는 너무 빠른 시간에 만료되기에 매번 재로그인을 해야한다는 단점이 있다. 반대로 너무 길게 잡으면 사용자 입장에서는 매번 재로그인을 해야한다는 느낌을 받지 않게 할 수 있겠지만 해당 토큰이 탈취되었을 때 그 긴 기간동안 공격자가 마음대로 사용자의 계정을 사용할 수 있다는 보안적인 단점이 있다. 이러한 편의성과 보안성 두 마리 토끼를 잡기 위해 Refresh Token이란 개념이 도입된 것이다. Access Token은 30분에서 1, 2시간 정도로 짧게 잡고 Refresh Token은 보통 1주일에서 길게는 1개월 정도로 잡는다고 한다. 그러면 Access Token의 만료 시간을 짧게 잡아도 Refresh Token을 통해 재발급받으면 되기에 편의성도 챙길 수 있으며, Access Token이 탈취되더라도 그 만료 시간이 짧기에 보안성도 챙길 수 있게 된다. 

Access Token 하나만 사용하는 경우 클라이언트와 서버 간의 통신 과정은 앞서 그림 1-1을 통해 살펴보았다. 그러면 Refresh Token까지 같이 사용하는 경우 클라이언트와 서버 간의 인증을 위한 통신 과정은 어떻게 될까? 

![그림 2-1. Access Token과 Refresh Token 사용 시 인증을 위한 클라이언트 - 서버 간 통신 과정](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/3.png)

그림 2-1. Access Token과 Refresh Token 사용 시 인증을 위한 클라이언트 - 서버 간 통신 과정

위 그림에 따르면 다음의 과정을 거친다.

1. 먼저 인증을 위해 클라이언트에서 사용자의 정보와 함께 인증 요청을 서버에 전송한다. 
2. 인증 요청을 받은 서버에서는 Access Token과 Refresh Token을 발급한다. 이 때 Refresh Token은 DB에 저장한다. 
3. 두 토큰을 생성한 서버는 이 두 토큰을 클라이언트에 응답으로 실어 전송한다. 
4. 두 토큰을 받은 클라이언트는 이후 Access Token만을 이용하여 인증이 필요한 요청에 해당 토큰을 실어 서버에 전송한다. 
5. 그러면 서버에서는 해당 토큰을 검증하고 이상이 없으면 요청을 처리, 응답한다. 
6. 만약 클라이언트가 만료된 Access Token을 가지고 요청을 전송하면, 서버에서는 해당 토큰 검증 후 해당 토큰이 만료되었다는 메시지와 함께 클라이언트에 전송한다. 
7. 그러면 클라이언트에서는 새로운 Access Token을 발급 받기 위해 Refresh Token과 함께 서버에 새 Access Token 발급 요청을 한다. 
8. 해당 요청을 받은 서버는 전송받은 Refresh Token이 DB 내에 있는지 확인하고, 해당 토큰의 유효성을 검사한다. DB 내에 있음이 확인되고 유효한 토큰임이 확인되면 서버에서는 새로운 Access Token을 생성하여 클라이언트에 응답한다. 
    1. 만약 Refresh Token도 만료된 경우, 클라이언트에게 재로그인을 하라는 응답을 보낸다. 그리고 DB 상에서는 해당 Refresh Token을 삭제하든가 아니면 무효 상태임을 표시한다. 

참고로, 사용자가 로그아웃을 요청하면 두 토큰 모두 무효화시킨다. 클라이언트에 Access Token, Refresh Token을 쿠키로 전송했다면 이 쿠키의 만료 시간을 0으로 설정하여 응답하면 해당 쿠키를 만료시킬 수 있다. 그리고 마찬가지로 DB 상에서는 해당 Refresh Token을 삭제하든가 아니면 무효 상태임을 표시한다. 

한 편, 이러한 통신 과정을 API의 관점에서 다루면 다음과 같이 표현할 수 있겠다.

![그림 2-2. Access Token과 Refresh Token에 대한 클라이언트 - 서버 간 통신 과정을 REST API 관점에서 재구성해보았다. ](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/4.png)

그림 2-2. Access Token과 Refresh Token에 대한 클라이언트 - 서버 간 통신 과정을 REST API 관점에서 재구성해보았다. 

즉, 백엔드 측에서는 Access Token을 재발급해주는 별도의 API를 제작하고, 프론트엔드 측에서는 Access Token의 만료 응답을 다른 API로부터 받으면 Access Token을 재발급 해주는 별도의 API에 요청하여 재발급을 받는 형식인 것이다. 

## Authorization - Bearer

인증을 위해 Access Token을 요청에 실어 서버에 전송할 때 보통 해당 토큰값을 요청 헤더의 Authorization 헤더의 값으로 싣는다고 한다. 이 때 토큰값 앞에는 Bearer를 붙여야 한다고 한다. 즉 해당 요청 헤더의 형태는 다음과 같다.

```
Authorization: Bearer <Token>
```

이는 토큰을 이용한 인증 메커니즘의 표준이라고 한다. 여기서 Bearer는 토큰의 소유자를 의미한다. 

위 형식을 지키지 않아도 토큰을 주고받는데에 기능상의 문제는 없지만 위와 같은 형식이 표준이기에 다른 시스템과의 호환성을 고려한다면 위 표준을 지키는 것이 좋다고 한다. 

# 코드로 살펴보기

이제 Spring Boot, Spring Security와 함께 JWT 기반 인증을 구현하는 과정을 코드를 통해 살펴보겠다. 여기서는 앞서 소개한 Access Token과 Refresh Token 두 개 모두를 활용하는 방식에 대해 다루겠다. Access Token만 사용하는 인증 방식의 예시 코드도 필자가 작성하였으니 이에 대해선 다음의 사이트에서 전체 소스 코드를 참고하면 되겠다.

[spring-study/JWTAuthStudy at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/JWTAuthStudy)

여기서는 Access Token & Refresh Token을 이용한 JWT 기반 인증을 핵심적으로 볼 예정이라 많은 부분들이 생략될 것이기에 코드의 흐름을 전체적으로 파악하길 원한다면 다음의 사이트를 참고하면 되겠다. 

[spring-study/JWTAuthWithRefreshTokenStudy at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/JWTAuthWithRefreshTokenStudy)

다음의 기술들을 이용하여 코드를 작성하였다. 

- Spring Boot
- Spring Data JPA
- Maria DB
- REST API
- Spring Security
- API 테스트: Swagger

DB의 테이블은 다음과 같이 구성하였다.

![그림 3-1. JWT 기반 인증 예시 코드를 위한 ERD](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/5.png)

그림 3-1. JWT 기반 인증 예시 코드를 위한 ERD

한 명의 사용자가 여러 개의 댓글을 작성할 수 있으며, 또한 한 명의 사용자는 한 개의 고유한 Refresh Token만 가지도록 하였다. tb_refresh_token 테이블의 before_token은 나중에 설명할 RTR(Refresh Token Rotation)과 Token Reuse Detection을 구현하기 위해 가장 최근에 만료된 Refresh Token을 기록하기 위한 컬럼으로, 현재 이 챕터에서는 필요하지 않은 컬럼이니 무시해도 된다. 한 편, Refresh Token이 만료되거나 사용자가 로그아웃을 한 경우 DB 상에서 삭제하는 대신 is_invalid 컬럼을 통해 현재 해당 토큰이 사용 가능한지 여부를 표시하도록 하였다. 물론 만료된 토큰은 삭제해도 되겠지만, 테스트, 디버깅 또는 보안을 위해 현재 토큰 상황을 감시하고자 할 때에는 이렇게 상태를 표시하는 방식으로 대체해도 되겠다. 

JWT 기반 인증을 사용하기 위해 먼저 build.gradle 파일 내에 다음의 의존성을 기입한다. 

```
// JWT
implementation 'io.jsonwebtoken:jjwt-api:0.12.6'
runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.6'
runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.6'
```

주의할 점은 위 세 개 중 하나라도 기입하지 않으면 JWT 관련 기능이 제대로 동작하지 않기에 위 세 개의 의존성 모두 기입해야 한다. 

그리고 각각의 엔티티 클래스를 정의한다. 여기선 Refresh Token과 JWT 기반 인증에 대해 집중적으로 살펴보기 위해 댓글(comment) 관련 코드는 생략하겠다. 

```java
@Entity
@Table(name = "tb_user")
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Member implements UserDetails {
    
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  @Column(length = 11)
  private int id;
    
  @Column(nullable = false, length = 20, unique = true)
  private String username;
    
  @Column(nullable = false, length = 100)
  @JsonProperty(access = Access.WRITE_ONLY)
  private String password;
    
  @Column(length = 10, name = "role_name")
  private String role;

  @Override
  public Collection<? extends GrantedAuthority> getAuthorities() {
        
    GrantedAuthority auth = new SimpleGrantedAuthority(
      RoleNames.ROLE_PREFIX + this.role
    );
                
    return Arrays.asList(auth);
  }
    
  public static Member toEntityForInserting(MemberRequest dto) {
    return Member.builder()
      .username(dto.getUsername())
      .password(dto.getPassword())
      .role(dto.getRole())
      .build();
  }
    
}

```

코드 1-1. Member.java

```java
@Entity
@Table(name = "tb_refresh_token")
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@ToString(callSuper = true)
public class RefreshToken extends BaseEntity {
    
  @Id
  @GeneratedValue(strategy = GenerationType.IDENTITY)
  private int id;
    
  @Column(length = 1000, unique = true)
  private String refreshToken;
    
  @Column(length = 1000, unique = true)
  private String beforeToken;
    
  /**
   * 참고 사이트)
   * https://velog.io/@yarogono/JPA-MariaDB-BOOLEAN%EC%BB%AC%EB%9F%BC%EA%B3%BC-Enum
   * 
   * 자바 Enum에 명시된 데이터들을 DB에 저장하도록 함. 
   */
  @Enumerated(EnumType.STRING)
  @Column(name = "is_invalid", length = 20, nullable = false)
  @Builder.Default
  private RefreshTokenStatus tokenStatus = RefreshTokenStatus.VALID;
    
  @OneToOne
  @JoinColumn(name = "user_id", nullable = false)
  private Member member;
    
}

```

코드 1-2. RefreshToken.java

## JWT를 위한 AuthenticationProvider, AuthenticationFilter 구현하기

이제 본격적으로 JWT 기반 인증 구현을 할텐데, 그 전에 먼저 흐름을 파악하기 위해 이전 글에서 필자가 보인 그림을 다시 한 번 살펴보자. 

![그림 3-2. Spring Security에서의 인증 처리 과정](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/2.png)

그림 3-2. Spring Security에서의 인증 처리 과정

Spring Security에서 인증을 처리하기 위한 과정에 여러 가지 객체들이 사용되는데, 그 중 JWT 기반 인증을 위한 AuthenticationFilter와 실질적으로 인증을 수행할 AuthenticationProvider가 필요하다. 이 두 개는 개발자가 구현한다. 

다음은 JWT 기반 인증을 실질적으로 수행할 JwtTokenProvider를 정의한 코드이다.

```java
package com.jerocaller.jwt;

import java.nio.charset.StandardCharsets;
import java.security.Key;
import java.util.ArrayList;
import java.util.Date;
import java.util.List;

import javax.crypto.SecretKey;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.stereotype.Component;

import com.jerocaller.common.CookieNames;
import com.jerocaller.common.RequestHeaderNames;
import com.jerocaller.common.RoleNames;
import com.jerocaller.data.entity.Member;
import com.jerocaller.exception.classes.jwt.JwtIllegalArgumentException;

import io.jsonwebtoken.Claims;
import io.jsonwebtoken.JwtException;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.UnsupportedJwtException;
import io.jsonwebtoken.security.Keys;
import jakarta.servlet.http.HttpServletRequest;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Component
@RequiredArgsConstructor
@Slf4j
public class JwtTokenProvider {
    
  // 로그인 후라면 이미 사용자 정보가 JWT에 담겨져 있으므로 
  // JWT에 있는 사용자 정보를 활용하는 것이 DB 부담을 줄일 수 있다. 
  //private final UserDetailsService userDetailsService;
  private final JwtCookieUtil jwtCookieUtil;
    
  /**
   * <p>
   * io.jsonwebtoken.security.WeakKeyException: 
   * The specified key byte array is 80 bits which 
   * is not secure enough for any JWT HMAC-SHA algorithm. 
   * </p>
   * 
   * secret key가 너무 짧으면 위와 같은 예외가 발생하니 
   * secret key를 충분히 길게 작성해야 한다. 
   */
  @Value("${jwt.secret}")
  private String secretKey = "defaultKey";
    
  private Key getSigningKey() {
      return Keys.hmacShaKeyFor(secretKey.getBytes(StandardCharsets.UTF_8));
  }
    
  /**
   * Access Token, Refresh Token 겸용 토큰 생성 메서드.
   * 
   * @param member
   * @param expirationInMilliSeconds
   * @return
   */
  public String createToken(
    Member member, 
    long expirationInMilliSeconds
  ) {
        
    Date expiration = new Date(
        System.currentTimeMillis() + expirationInMilliSeconds
    );
        
    String token = Jwts.builder()
        .header()
        .add("typ", CookieNames.JWT)
        .and()
        .subject(member.getUsername())
        .claim("role", member.getRole())
        .issuedAt(new Date())
        .expiration(expiration)
        .signWith(getSigningKey())
        .compact();

    return token;
  }
    
  public Authentication getAuthentication(String token) {
        
    String username = extractUsernameFromToken(token);
        
    String role = (String) extractClaims(token).get("role");
    log.info("=== [getAuthentication] JWT로부터 추출한 사용자의 역할: {}", role);
        
    List<GrantedAuthority> authorities = new ArrayList<GrantedAuthority>();
    authorities.add(new SimpleGrantedAuthority(RoleNames.ROLE_PREFIX + role));
        
    // 두 번째 인자에는 credential(자격 증명)를 넣는데, 보통은 패스워드를 넣는다. 
    // 그런데 패스워드를 얻으려면 DB로부터 조회해야 하는데, 이는 DB 접근을 최소화하는 장점을 
    // 가진 JWT의 그 장점을 활용하지 못하는 것이다. 
    // 따라서 아무 값도 넣지 않거나, 아니면 JWT 토큰 자체를 넣을 수도 있다. 
    return new UsernamePasswordAuthenticationToken(username, null, authorities);
  }
    
  public Claims extractClaims(String token) {
    return Jwts.parser()
      .verifyWith((SecretKey) getSigningKey())
      .build()
      .parseSignedClaims(token)
      .getPayload();
  }
    
  public String extractUsernameFromToken(String token) {
    return extractClaims(token).getSubject();
  }
    
  /**
   * HTTP Request header로부터 Token 값 추출.
   * 
   * Authorization: Bearer <JWT Token>
   * 형식 관련 설명 참조)
   * https://cafe.daum.net/flowlife/HqLp/36
   * 
   * @param request
   * @return
   */
  public String resolveToken(HttpServletRequest request) {
        
    String token = null;
    String tokenHeader = request.getHeader(RequestHeaderNames.AUTH_TOKEN);
        
    // 먼저 요청 헤더로부터 jwt값이 있는지 확인 후 있으면 추출하는 작업을 진행한다. 
    if (tokenHeader != null && 
      tokenHeader.startsWith(RequestHeaderNames.BEARER)
    ) {
      token = tokenHeader.substring(RequestHeaderNames.BEARER.length())
        .trim();
      log.info("Request Header로부터 JWT 추출 성공.");
    } else {
      // 요청 헤더에 없다면 요청으로 넘어온 쿠키에 jwt 값이 있는지 확인 후 추출 시도.
      token = jwtCookieUtil.extractJwtFromCookies(
        request, 
        CookieNames.ACCESS_TOKEN
      );
      log.info("Request에 포함된 Cookie로부터 JWT 추출 성공");
    }
    return token;
  }
    
  /**
   * 주어진 토큰이 유효한지 체크. 만료 시간에 대해서만 boolean값으로 반환되며, 
   * 그 외 다른 경우로 유효하지 않음이 판별되면 런타임 예외 발생.
   * 
   * @param token
   * @return - 아직 만료되지 않았다면 true, 만료되었거나 토큰에 문제가 있는 경우 false
   */
  public boolean validateToken(String token) {
        
    try {
      Claims claims = extractClaims(token);
            
      // 해당 토큰의 만료 시각 정보를 가져온 후, 만료 시각이 현재 시간 이전이면 true, 
      // 이후면 false가 된다. 
      // 만료 시각이 현재 시간 이전이라는 것은 이미 만료되었단 뜻이므로 
      // 더 이상 유효하지 않기에 true 값을 반전시킨 false를 반환시켜 유효하지 않음을 표시한다. 
      return !claims.getExpiration().before(new Date());
    } catch (UnsupportedJwtException unsupportedJwtException) {
      throw new UnsupportedJwtException("지원되는 형식의 JWT가 아닙니다.");
    } catch (JwtException jwtException) {
      throw new JwtException("주어진 JWT를 parsing할 수 없거나, 유효하지 않은 형식의 JWT입니다.");
    } catch (IllegalArgumentException illegalArgumentException) {
      throw new JwtIllegalArgumentException();
    } catch (Exception e) {
      // 토큰 서명에 문제가 있거나 JWT 구조가 올바르지 않은 등의 이유로 예외 발생 가능.
      log.error("=== [validateToken] 토큰 유효 검사 예외 발생 ===");
      return false;
    }
        
  }
    
}

```

코드 1-3. JwtTokenProvider.java

위 코드를 부분별로 살펴보겠다. 

```java
@Value("${jwt.secret}")
private String secretKey = "defaultKey";
```

코드 1-4. 코드 1-3의 일부.

위 코드는 application.properties 파일 내 지정한 secret key를 가져와 `secretKey` 에 주입하는 코드이다. 필자는 다음과 같이 해당 파일에 secret key를 지정하였다. 

```java
# jwt secret key
jwt.secret=jwt_test_this_key_length_must_be_so_long_enough_to_avoid_weak_key_exception
```

코드 1-5. application.properties

이 secret key는 JWT의 서명 부분을 채우기 위해 암호화 및 복호화할 때 사용할 중요한 키이므로 외부에 노출되지 않도록 하며, 유추하기 쉬운 문자열은 피하도록 하자. 한 편 secret key의 길이는 최소 256 bit 이상이어야 하며, 그렇지 않으면 `io.jsonwebtoken.security.WeakKeyException` 예외가 발생한다. 

```java
private Key getSigningKey() {
  return Keys.hmacShaKeyFor(secretKey.getBytes(StandardCharsets.UTF_8));
}
```

코드 1-6. 코드 1-3의 일부. 

위 코드는 앞서 가져온 secret key를 토대로 HMAC-SHA 알고리즘으로 암호화하여 반환하는 메서드이다. JWT의 서명에 쓰일 용도이다. 

```java
public String createToken(
  Member member, 
  long expirationInMilliSeconds
) {
  // #1
  Date expiration = new Date(
    System.currentTimeMillis() + expirationInMilliSeconds
  );
    
  // #2
  String token = Jwts.builder()
    .header()
    .add("typ", CookieNames.JWT)
    .and()
    .subject(member.getUsername())
    .claim("role", member.getRole())
    .issuedAt(new Date())
    .expiration(expiration)
    .signWith(getSigningKey())
    .compact();

  return token;
}
```

코드 1-7. 코드 1-3의 일부.

위 메서드는 JWT 토큰을 생성하기 위한 메서드로, 회원 정보와 만료 시간을 인자로 받는다. #1에서는 만료 시간을 설정하기 위해 epoch값을 이용하여 Date 객체로 생성하고 있다. 그리고 #2에서 본격적으로 사용자의 정보와 앞서 만든 secretKey, 그리고 만료 시간을 토대로 JWT의 헤더, payload, signature를 생성하고 있다. 이렇게 생성된 JWT를 반환하고 있다. 

```java
public Claims extractClaims(String token) {
    return Jwts.parser()
        .verifyWith((SecretKey) getSigningKey())
        .build()
        .parseSignedClaims(token)
        .getPayload();
}
    
public String extractUsernameFromToken(String token) {
    return extractClaims(token).getSubject();
}
```

코드 1-8. 코드 1-3의 일부.

위 코드에서는 주어진 JWT 토큰으로부터 이를 파싱하여 각각 payload에 저장된 Claim과 username을 추출하는 메서드이다. 

> jjwt 라이브러리의 0.12.6 버전 이후로 JWT 관련 메서드명이 크게 바뀌었다. 이전에는 `setSigningKey()` 였는데 지금은 `verifyWith()` 로 바뀌었다든지, `setClaims()` → `claims()` 등으로 바뀌었고, 이전 메서드들은 모두 deprecated되었다. 그렇기에 이전 버전을 소개하는 책이나 다른 글에서 사용된 메서드명과 많이 다르니 참고바란다.
> 

```java
public Authentication getAuthentication(String token) {
        
    String username = extractUsernameFromToken(token);
        
    String role = (String) extractClaims(token).get("role");
    log.info("=== [getAuthentication] JWT로부터 추출한 사용자의 역할: {}", role);
        
    List<GrantedAuthority> authorities = new ArrayList<GrantedAuthority>();
    authorities.add(new SimpleGrantedAuthority(RoleNames.ROLE_PREFIX + role));
        
    // 두 번째 인자에는 credential(자격 증명)를 넣는데, 보통은 패스워드를 넣는다. 
    // 그런데 패스워드를 얻으려면 DB로부터 조회해야 하는데, 이는 DB 접근을 최소화하는 장점을 
    // 가진 JWT의 그 장점을 활용하지 못하는 것이다. 
    // 따라서 아무 값도 넣지 않거나, 아니면 JWT 토큰 자체를 넣을 수도 있다. 
    return new UsernamePasswordAuthenticationToken(username, null, authorities);
}
```

코드 1-9. 코드 1-3의 일부.

위 코드는 클라이언트의 요청에 담겨진 JWT 토큰의 유효성 검증 후, 인증된 토큰을 SecurityContextHolder 내에 Authentication 객체 형태로 저장하기 위한 메서드이다. 

```java
public String resolveToken(HttpServletRequest request) {
        
    String token = null;
    String tokenHeader = request.getHeader(RequestHeaderNames.AUTH_TOKEN);
        
    // 먼저 요청 헤더로부터 jwt값이 있는지 확인 후 있으면 추출하는 작업을 진행한다. 
    if (tokenHeader != null && 
        tokenHeader.startsWith(RequestHeaderNames.BEARER)
    ) {
        token = tokenHeader.substring(RequestHeaderNames.BEARER.length())
            .trim();
        log.info("Request Header로부터 JWT 추출 성공.");
    } else {
        // 요청 헤더에 없다면 요청으로 넘어온 쿠키에 jwt 값이 있는지 확인 후 추출 시도.
        token = jwtCookieUtil.extractJwtFromCookies(
            request, 
            CookieNames.ACCESS_TOKEN
        );
        log.info("Request에 포함된 Cookie로부터 JWT 추출 성공");
    }
    return token;
}
```

코드 1-10. 코드 1-3의 일부.

위 메서드는 클라이언트의 요청이 담긴 HttpServletRequest 객체로부터 JWT 토큰을 추출하여 이를 반환하는 메서드이다. 여기서는 요청 객체로부터 JWT 토큰을 추출하는 방법이 2가지가 사용되었다. 하나는 요청 헤더에 `Authorization: Bearer <JWT>` 형태의 헤더가 있는지 확인하여 JWT를 추출하는 방식이고, 또 하나는 요청과 같이 넘어온 쿠키들 중 Access Token을 담은 쿠키가 있는지 확인하여 이로부터 추출하는 방식이 쓰였다. 이 두 방식은 상황에 따라 적절히 사용하면 되겠다. 한 편, 쿠키로부터 JWT 토큰을 얻기 위한 JwtCookieUtil의 코드는 다음과 같다. 

```java
@Component
public class JwtCookieUtil {
    
    public String extractJwtFromCookies(
        HttpServletRequest request, 
        String jwtCookieName
    ) {
        
        String token = null;
        Cookie[] cookies = request.getCookies();
        
        if (cookies != null) {
            for (Cookie cookie : cookies) {
                if (cookie.getName().equals(jwtCookieName)) {
                    token = cookie.getValue();
                    break;
                }
            }
        }
        
        return token;
    }
    
    public void addCookieWithJwt(
        HttpServletResponse httpServletResponse,
        JwtCookieRequest jwtCookieRequest
    ) {
        
        Cookie jwtCookie = new Cookie(
            jwtCookieRequest.getCookieName(), 
            jwtCookieRequest.getToken()
        );
        
        // 클라이언트의 조작에 의한 변형 방지. XSS 공격 방지
        jwtCookie.setHttpOnly(true);
        
        // HTTPS에서만 사용할 경우 true로 설정. HTTP에서도 사용할 경우 false.
        jwtCookie.setSecure(false);
        
        // 전체 경로에서 사용 가능.
        jwtCookie.setPath("/");
        
        jwtCookie.setMaxAge(jwtCookieRequest.getMaxAge());
        httpServletResponse.addCookie(jwtCookie);
        
    }
    
    public void deleteCookies(
        HttpServletResponse httpResponse, 
        String... cookieNames
    ) {
        
        List<Cookie> cookies = new ArrayList<Cookie>();
        for (String cookieName : cookieNames) {
            Cookie cookie = new Cookie(cookieName, null);
            cookie.setPath("/");
            cookie.setHttpOnly(true);
            cookie.setMaxAge(0);
            cookies.add(cookie);
        }
        
        cookies.forEach(cookie -> {
           httpResponse.addCookie(cookie);
        });
        
    }
    
}
```

코드 1-11. JwtCookieUtil.java

```java
public boolean validateToken(String token) {
        
    try {
        Claims claims = extractClaims(token);
            
        // 해당 토큰의 만료 시각 정보를 가져온 후, 만료 시각이 현재 시간 이전이면 true, 
        // 이후면 false가 된다. 
        // 만료 시각이 현재 시간 이전이라는 것은 이미 만료되었단 뜻이므로 
        // 더 이상 유효하지 않기에 true 값을 반전시킨 false를 반환시켜 유효하지 않음을 표시한다. 
        return !claims.getExpiration().before(new Date());
    } catch (UnsupportedJwtException unsupportedJwtException) {
        throw new UnsupportedJwtException("지원되는 형식의 JWT가 아닙니다.");
    } catch (JwtException jwtException) {
        throw new JwtException("주어진 JWT를 parsing할 수 없거나, 유효하지 않은 형식의 JWT입니다.");
    } catch (IllegalArgumentException illegalArgumentException) {
        throw new JwtIllegalArgumentException();
    } catch (Exception e) {
        // 토큰 서명에 문제가 있거나 JWT 구조가 올바르지 않은 등의 이유로 예외 발생 가능.
        log.error("=== [validateToken] 토큰 유효 검사 예외 발생 ===");
        return false;
    }
        
}
```

코드 1-12. 코드 1-3의 일부.

위 메서드는 주어진 토큰의 유효성을 검증하는 메서드로, 만료 시간이 지나면 false를, 그 외 다른 이유로 유효성 검사에 실패한 경우에는 각각의 경우에 맞는 예외를 던지도록 하였고, 문제 없으면 true를 반환하도록 하였다. 

이번에는 클라이언트의 모든 요청에 대해 인증을 수행할 수 있도록 하는 Filter를 다음과 같이 만들었다.

```java
package com.jerocaller.jwt;

import java.io.IOException;

import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.stereotype.Component;
import org.springframework.web.filter.OncePerRequestFilter;

import com.jerocaller.common.RoleNames;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Component
@RequiredArgsConstructor
@Slf4j
public class JwtAuthenticationFilter extends OncePerRequestFilter {
    
    private final JwtTokenProvider jwtTokenProvider;

    @Override
    protected void doFilterInternal(
        HttpServletRequest request, 
        HttpServletResponse response, 
        FilterChain filterChain
    ) throws ServletException, IOException {
        
        String accessToken = jwtTokenProvider.resolveToken(request);
        
        if (accessToken != null && jwtTokenProvider.validateToken(accessToken)) {
            Authentication auth = jwtTokenProvider.getAuthentication(accessToken);
            SecurityContextHolder.getContext().setAuthentication(auth);
        }
        
        filterChain.doFilter(request, response);
    }

}

```

코드 1-13. JwtAuthenticatioFilter.java

위 코드에서는 앞서 제작한 JwtTokenProvider 객체를 이용하여 클라이언트의 요청에 담긴 JWT를 추출, 유효성 검사를 진행하고 인증된 사용자라면 이 인증된 사용자 정보를 SecurityContextHolder 내부에 저장하도록 하고 있다. 

## Security 설정 클래스 구현하고 필터 등록하기

이제 Spring Security 관련 설정을 할 것이다. 다음의 코드에서 이를 수행하고 있다.

```java
package com.jerocaller.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.access.hierarchicalroles.RoleHierarchy;
import org.springframework.security.access.hierarchicalroles.RoleHierarchyImpl;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler;

import com.jerocaller.common.RoleNames;
import com.jerocaller.jwt.JwtAuthenticationFilter;

import lombok.RequiredArgsConstructor;

@Configuration
@RequiredArgsConstructor
@EnableWebSecurity
public class SecurityConfig {
    
    private final AccessDeniedHandler accessDeniedHandler;
    private final AuthenticationEntryPoint authenticationEntryPoint;
    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    private final String[] permitAllRequestUris = {
        "/swagger-ui/**", 
        "/v3/api-docs",
        "/v3/api-docs/**", 
        "/swagger-ui.html",
        "/swagger-ui/index.html",
        "/auth/**",
        "/members/registration",
        "/jwt/tokens/**"
    };
    
    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) 
        throws Exception 
    {
        
        httpSecurity
            .csrf(csrf -> csrf.disable())
            .sessionManagement(manager -> manager
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)
            )
            .authorizeHttpRequests(auth -> auth
                .requestMatchers(permitAllRequestUris).permitAll()
                .requestMatchers("/comments/others/users").hasRole(RoleNames.USER)
                .requestMatchers("/comments/others/staffs").hasRole(RoleNames.STAFF)
                .requestMatchers("/comments/others/admins").hasRole(RoleNames.ADMIN)
                .anyRequest().authenticated()
            )
            .addFilterBefore(
                jwtAuthenticationFilter, 
                UsernamePasswordAuthenticationFilter.class
            )
            .exceptionHandling(handle -> handle
                .accessDeniedHandler(accessDeniedHandler)
                .authenticationEntryPoint(authenticationEntryPoint)
            );
        
        return httpSecurity.build();
    }
    
    @Bean
    public PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
    
    /**
     * Role 계층 구성. 
     * 
     * @return
     */
    @Bean
    public RoleHierarchy roleHierarchy() {
        return RoleHierarchyImpl.withDefaultRolePrefix()
            .role(RoleNames.ADMIN).implies(RoleNames.STAFF)
            .role(RoleNames.STAFF).implies(RoleNames.USER)
            .build();
    }
    
    @Bean
    public SecurityContextLogoutHandler securityContextLogoutHandler() {
        return new SecurityContextLogoutHandler();
    }
    
}

```

코드 2-1. SecurityConfig.java

위 코드의 대부분은 이전 글인 [https://jerocaller.github.io/spring/spring-boot-보안과-spring-security/](/spring/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC-spring-security/) 에서 설명했기에 여기서는 설명을 생략하겠다. 그러나 위 코드에서는 몇 가지 주목할만한 점이 있다. 그 중 하나는 앞서 만든 필터를 등록하는 코드이다.

```java
.addFilterBefore(
    jwtAuthenticationFilter, 
    UsernamePasswordAuthenticationFilter.class
)
```

코드 2-2. 코드 2-1 일부.

Spring Security에서 기본으로 제공하는 인증용 필터 앞에 앞서 만든 필터를 위치시킴으로써 JWT 기반 인증을 먼저 수행하도록 하였다. JwtAuthenticationFilter 내에서 인증에 성공하면 UsernamePasswordAuthenticationFilter는 자동으로 통과된다고 한다. 

## 인증과 인가 예외 처리

코드 2-1에서 주목할만한 또 다른 부분은 인증과 인가를 처리하는 코드이다.

```java
.exceptionHandling(handle -> handle
    .accessDeniedHandler(accessDeniedHandler)
    .authenticationEntryPoint(authenticationEntryPoint)
);
```

코드 3-1. 코드 2-1 일부

참고로 인증과 인가에서의 예외 처리는 다음의 과정을 거친다고 한다.

![참고 사진 1-1. Spring Security에서 인증, 인가에서 예외 발생 시 처리 과정. [https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-exceptiontranslationfilter](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-exceptiontranslationfilter) 참고함.](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/exceptiontranslationfilter.png)

참고 사진 1-1. Spring Security에서 인증, 인가에서 예외 발생 시 처리 과정. [https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-exceptiontranslationfilter](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-exceptiontranslationfilter) 참고함.

만일 요청을 한 현재 사용자가 인증되지 않았거나 AuthenticationException 예외가 발생한 경우에는 위 사진의 2번처럼 인증을 하도록 유도되는데, 먼저 SecurityContextHolder 내부가 모두 지워진다. 그 후 AuthenticationEntryPoint로 넘어가지며, 여기서 클라이언트에게 재로그인할 수 있도록 리다이렉트한다든지와 같은 인증 관련 처리를 할 수 있다. 여기서는 이미 JwtAuthenticationFilter를 앞에 배치하여 JWT 기반 인증을 수행하도록 처리하였는데 AuthenticationEntryPoint에 왔다는 것은 인증에 실패했다는 뜻이다. 따라서 여기서의 AuthenticationEntryPoint는 인증 실패와 같은 인증 관련 예외 발생 시 이를 처리할 용도가 될 것이다. 이 AuthenticationEntryPoint라는 인터페이스의 구현체를 만들어 인증 관련 처리 로직을 작성하면 된다. 

한 편 인증은 성공했으나 인가가 실패하여 특정 리소스에 접근하지 못하면 AccessDeniedException 예외가 발생하며, 이 예외를 처리하기 위한 AccessDeniedHandler로 넘어와 여기서 인가 관련 예외를 처리한다. 이 역시 인터페이스로, 이를 구현하는 클래스를 만들어 인가 관련 예외를 처리할 수 있다. 

즉, 앞서 본 코드 3-1은 각각 인증과 인가 실패 시 발생하는 예외 핸들러를 등록하는 코드인 것이다. 다음은 각각 인증과 인가 실패 시 발생하는 예외를 처리하기 위해 필자가 구현한 구현체들이다. 

```java
package com.jerocaller.exception.security;

import java.io.IOException;

import org.springframework.http.HttpStatus;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.stereotype.Component;

import com.fasterxml.jackson.databind.ObjectMapper;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;

@Component
@Slf4j
public class CustomAuthenticationEntryPoint implements AuthenticationEntryPoint {

    @Override
    public void commence(
        HttpServletRequest request, 
        HttpServletResponse response,
        AuthenticationException authException
    ) throws IOException, ServletException {
        
        log.info("=== 인증 실패 ===");
        
        ObjectMapper objectMapper = new ObjectMapper();
        
        String message = "인증되지 않았습니다. 로그인 하거나 액세스 토큰을 재발급하세요.";
        SecurityExceptionResponse securityExceptionResponse
            = SecurityExceptionResponse.builder()
                .status(HttpStatus.UNAUTHORIZED)
                .messasge(message)
                .build();
        
        response.setStatus(securityExceptionResponse.getStatus().value());
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        response.getWriter().write(
            objectMapper.writeValueAsString(securityExceptionResponse)
        );
        
    }

}
```

코드 3-2. CustomAuthenticationEntryPoint.java

```java
package com.jerocaller.exception.security;

import java.io.IOException;

import org.springframework.http.HttpStatus;
import org.springframework.security.access.AccessDeniedException;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.stereotype.Component;

import com.fasterxml.jackson.databind.ObjectMapper;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

/**
 * 인가(Authorization) 과정에서 예외 발생 시 이를 처리할 핸들러.
 */
@Component
public class CustomAccessDeniedHandler implements AccessDeniedHandler {

    @Override
    public void handle(
        HttpServletRequest request, 
        HttpServletResponse response,
        AccessDeniedException accessDeniedException
    ) throws IOException, ServletException {
        
        ObjectMapper objectMapper = new ObjectMapper();
        
        SecurityExceptionResponse securityExceptionResponse
            = SecurityExceptionResponse.builder()
                .status(HttpStatus.UNAUTHORIZED)
                .messasge("접근 권한 없음.")
                .build();
        
        response.setStatus(securityExceptionResponse.getStatus().value());
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");
        response.getWriter().write(
            objectMapper.writeValueAsString(securityExceptionResponse)
        );
        
    }
    
}
```

코드 3-3. CustomAccessDeniedHandler.java

위 두 코드 모두 클라이언트에게 JSON 형태로 각각 인증과 인가가 실패했다는 응답을 보내도록 처리하였다. 

## Token Service 만들기

이번에는 토큰을 다루는 서비스 코드를 제작할 차례이다. 먼저 Refresh Token을 생성, DB 저장, 만료시키는 기능을 담은 RefreshTokenService를 다음과 같이 만들었다.

```java
@Service
@RequiredArgsConstructor
public class RefreshTokenService {
    
    private final RefreshTokenRepository refreshTokenRepository;
    private final JwtTokenProvider jwtTokenProvider;
    private final JwtCookieUtil jwtCookieUtil;
    
    public RefreshToken findRefreshToken(String refreshToken) {
        return refreshTokenRepository.findByRefreshToken(refreshToken)
            .orElseThrow(() -> new EntityNotFoundException("조회된 refresh token 없음."));
    }
    
    @Transactional
    public RefreshToken createAndSaveRefreshToken(Member member) {
        
        String refreshToken = jwtTokenProvider.createToken(
            member, 
            ExpirationTime.REFRESH_TOKEN_IN_MILLI_SECONDS
        );
        
        // 리프레시 DB 테이블에 이미 해당 회원 레코드가 존재할 경우, 
        // 새로 생성하지 않고 해당 레코드에 업데이트.
        Optional<RefreshToken> refreshTokenOpt = refreshTokenRepository
            .findByMember(member);
        
        RefreshToken newRefreshToken = null;
        
        if (refreshTokenOpt.isPresent()) {
            RefreshToken existRefreshToken = refreshTokenOpt.get();
            newRefreshToken = RefreshToken.builder()
                .id(existRefreshToken.getId())
                .refreshToken(refreshToken)
                .beforeToken(existRefreshToken.getRefreshToken())
                .tokenStatus(RefreshTokenStatus.VALID)
                .member(existRefreshToken.getMember())
                .build();
        } else {
            newRefreshToken = RefreshToken.builder()
                .refreshToken(refreshToken)
                .tokenStatus(RefreshTokenStatus.VALID)
                .member(member)
                .build();
        }

        return refreshTokenRepository.save(newRefreshToken);
    }
    
    /**
     * 로그아웃 시 DB에 저장된 리프레시 토큰의 상태를 무효로 설정.
     * 
     * @param httpServletRequest
     */
    @Transactional
    public void invalidateRefreshToken(HttpServletRequest httpServletRequest) {
        
        String token = jwtCookieUtil
            .extractJwtFromCookies(
                httpServletRequest, 
                CookieNames.REFRESH_TOKEN
            );
        
        RefreshToken targetRefreshToken = refreshTokenRepository
            .findByRefreshToken(token)
            .orElse(null);
        if (targetRefreshToken == null) return;
        
        RefreshToken updateRefreshToken = RefreshToken.builder()
            .id(targetRefreshToken.getId())
            .tokenStatus(RefreshTokenStatus.INVALID)
            .member(targetRefreshToken.getMember())
            .build();
        refreshTokenRepository.save(updateRefreshToken);
        
    }
    
}
```

코드 4-1. RefreshTokenService.java

`findRefreshToken` 메서드를 통해 클라이언트가 AccessToken 갱신 요청을 위해 넘긴 Refresh Token을 DB에서 조회하도록 하였다. 

`createAndSaveRefreshToken` 메서드에서는 Refresh Token을 생성, 이를 DB에 저장한다. 

`invalidateRefreshToken` 메서드에서는 로그아웃 시 DB 상에 기록된 해당 RefreshToken이 로그아웃에 의해 무효화되었음을 기록하도록 하였다. 이 대신 DB에서 해당 Refresh Token 데이터를 삭제하는 로직으로 바꿔도 된다. 

이번에는 Access Token과 Refresh Token 이 둘 모두 다룰 TokenService를 정의하였다. 방금 만든 RefreshTokenService를 의존성 주입 받아 활용한다. 

```java
@Service
@RequiredArgsConstructor
public class TokenService {
    
    private final RefreshTokenService refreshTokenService;
    private final JwtTokenProvider jwtTokenProvider;
    
    public TokenResponse getNewAccessToken(
        TokenRequest refreshTokenRequest
    ) {
        
        if (!jwtTokenProvider.validateToken(
            refreshTokenRequest.getRefreshToken()
        )) {
            throw new RefreshTokenExpiredException();
        }
        
        RefreshToken refreshToken = refreshTokenService
            .findRefreshToken(refreshTokenRequest.getRefreshToken());
        
        Member member = refreshToken.getMember();
        String newAccessToken = jwtTokenProvider.createToken(
            member, 
            ExpirationTime.ACCESS_TOKEN_IN_MILLI_SECONDS
        );
        
        return TokenResponse.toDto(newAccessToken);
    }
    
    /**
     * 액세스 토큰과 리프레시 토큰 모두 새로 발급한다. 
     * 로그인 시 발급할 용도.
     * 
     * @return
     */
    public TokenResponse getNewAllTokens(Member member) {
        
        String accessToken = jwtTokenProvider.createToken(
            member, 
            ExpirationTime.ACCESS_TOKEN_IN_MILLI_SECONDS
        );
        
        RefreshToken newRefreshToken = refreshTokenService
            .createAndSaveRefreshToken(member);
        
        return TokenResponse.toDto(
            accessToken, 
            newRefreshToken.getRefreshToken()
        );
    }
    
}

```

코드 4-2. TokenService.java 

`getNewAccessToken` 메서드에서는 클라이언트가 새 AccessToken 요청 시 이를 처리하기 위해 새 Access Token을 생성하여 반환하는 메서드이다. 

`getNewAllTokens` 메서드에서는 로그인 시 두 토큰 모두 생성하여 반환하고, Refresh Token은 앞서 정의한 RefreshTokenService 객체를 이용하여 DB에 저장하는 로직을 작성하였다. 

한 편, 로그인, 로그아웃을 위한 서비스 코드는 다음의 별도의 클래스에 정의하였다. 앞서 만든 TokenService, RefreshTokenService를 의존성 주입받아 활용한다. 

```java
@Service
@RequiredArgsConstructor
public class AuthService {
    
    private final UserDetailsService userDetailsService;
    private final PasswordEncoder passwordEncoder;
    private final TokenService tokenService;
    private final RefreshTokenService refreshTokenService;
    private final SecurityContextLogoutHandler logoutHandler;
    private final AuthUtil authUtil;
    
    public MemberResponse login(MemberRequest memberRequest) {
        
        // DB 내 해당 유저 아이디가 있는지 확인
        Member member = (Member) userDetailsService.loadUserByUsername(
            memberRequest.getUsername()
        );
        
        // 패스워드 일치 여부 검사
        if (!passwordEncoder.matches(
            memberRequest.getPassword(), 
            member.getPassword()
        )) {
            throw new PasswordNotMatchedException();
        }
        
        // 액세스 및 리프레시 토큰 발급.
        TokenResponse tokenResponse = tokenService.getNewAllTokens(member);
        
        return MemberResponse.toDtoWithToken(member, tokenResponse);
    }
    
    public void logout(
        HttpServletRequest httpRequest, 
        HttpServletResponse httpResponse
    ) {
        
        // 로그아웃 전 DB에 저장한 리프레시 토큰 데이터 변경.
        // 삭제할 수도, 사용 가능 여부 상태를 바꿀 수도 있다. 
        // 여기서는 후자를 택함. 
        refreshTokenService.invalidateRefreshToken(httpRequest);
        
        logoutHandler.logout(httpRequest, httpResponse, authUtil.getAuth());
        
    }
    
}
```

코드 4-3. AuthService.java

## 인증, 로그아웃 및 새 Access Token 발급을 위한 REST API 구성

앞서 만든 인증 및 새 토큰 발급을 위한 서비스 코드들을 활용하여 각각의 REST API를 만들어보았다. 

먼저, 다음은 로그인 및 로그아웃을 위한 컨트롤러 계층의 코드이다. 

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/auth")
public class AuthController {
    
    private final AuthService authService;
    private final JwtCookieUtil jwtCookieUtil;
    private final HttpServletRequest httpServletRequest;
    private final HttpServletResponse httpServletResponse;
    
    @PostMapping("/login")
    public ResponseEntity<RestResponse> login(
        @Valid @RequestBody MemberRequest memberRequest
    ) {
        
        MemberResponse memberResponse = authService.login(memberRequest);
        
        // 로그인 성공 시 클라이언트에 JWT 토큰이 담긴 쿠키 전송.
        jwtCookieUtil.addCookieWithJwt(
            httpServletResponse,
            JwtCookieRequest.builder()
                .cookieName(CookieNames.ACCESS_TOKEN)
                .token(memberResponse.getTokens().getAccessToken())
                .maxAge(ExpirationTime.ACCESS_TOKEN_IN_SECONDS)
                .build()
        );
        jwtCookieUtil.addCookieWithJwt(
            httpServletResponse, 
            JwtCookieRequest.builder()
                .cookieName(CookieNames.REFRESH_TOKEN)
                .token(memberResponse.getTokens().getRefreshToken())
                .maxAge(ExpirationTime.REFRESH_TOKEN_IN_SECONDS)
                .build()
        );
        
        return RestResponse.builder()
            .httpStatus(HttpStatus.OK)
            .message("로그인 성공")
            .data(memberResponse)
            .build()
            .toResponse();
    }
    
    @PostMapping("/logout")
    public ResponseEntity<RestResponse> logout() {
        
        authService.logout(httpServletRequest, httpServletResponse);
        
        jwtCookieUtil.deleteCookies(
            httpServletResponse, 
            CookieNames.ACCESS_TOKEN, 
            CookieNames.REFRESH_TOKEN
        );
        
        return RestResponse.builder()
            .httpStatus(HttpStatus.OK)
            .message("로그아웃 성공")
            .build()
            .toResponse();
    }
    
}

```

코드 5-1. AuthController.java

“/login” URI 요청 시 생성된 두 토큰을 `jwtCookieUtil.addCookieWithJwt` 메서드를 이용하여 각각의 쿠키에 담아 Http Response에 실어 전송하도록 구현하였다. 쿠키는 HTTP Response에 담는데, HTTP Response를 직접 다루는 계층은 컨트롤러 계층이 더 적합하다 생각하여 서비스 계층 대신 해당 계층에 위치시켰다. 

“/logout” URI를 통해 로그아웃을 요청할 때에도 클라이언트의 브라우저에 저장된 두 토큰의 쿠키들을 삭제하여 무효화하도록 하였다. 혹시라도 로그아웃 후에도 해당 사용자의 쿠키를 탈취해 악용할 가능성을 대비해 로그아웃 시 두 쿠키를 삭제하도록 한 것이다. 

다음은 Access Token 만료 시 이를 재발급해주기 위한 컨트롤러 코드이다. 

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/jwt/tokens")
public class TokenController {
    
    private final TokenService tokenService;
    private final HttpServletResponse httpServletResponse;
    private final JwtCookieUtil jwtCookieUtil;
    
    /**
     * 액세스 토큰 갱신
     * 
     * @return
     */
    @PostMapping("/new/access")
    public ResponseEntity<RestResponse> getNewAccessToken(
       @RequestBody TokenRequest tokenRequest
    ) {
        
        TokenResponse tokenResponse = tokenService
            .getNewAccessToken(tokenRequest);
        
        jwtCookieUtil.addCookieWithJwt(
            httpServletResponse, 
            JwtCookieRequest.builder()
                .token(tokenResponse.getAccessToken())
                .cookieName(CookieNames.ACCESS_TOKEN)
                .maxAge(ExpirationTime.ACCESS_TOKEN_IN_SECONDS)
                .build()
        );
        
        return RestResponse.builder()
            .httpStatus(HttpStatus.OK)
            .message("액세스 토큰 발급 성공")
            .data(tokenResponse)
            .build()
            .toResponse();
    }

}

```

코드 5-2. TokenController.java

위 코드에서도 마찬가지로 새 Access Token 발급 시 이를 쿠키에 담아 응답으로 전송하도록 하였다. 

## 결과 확인

Swagger에서 JWT 기반 인증이 잘 작동되는지 살펴보겠다. 

먼저 로그인을 해보았다. 

![사진 2-1. 로그인 성공 후 응답 결과. 브라우저에 두 토큰을 담은 각각의 두 쿠키들이 생성된 것을 볼 수 있다. ](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/6.png)

사진 2-1. 로그인 성공 후 응답 결과. 브라우저에 두 토큰을 담은 각각의 두 쿠키들이 생성된 것을 볼 수 있다. 

DB 상에서 리프레시 토큰 데이터는 다음과 같이 생성된다.

![사진 2-2. 리프레시 발급 후의 DB 상의 결과. 필자가 이전에 해당 계정으로 테스트한 적이 있어 이미 해당 레코드가 생성되어 있다. 다만 INVALID에서 VALID로 상태가 바뀌었으며, refresh_token에는 아무 값이 없다가 새로운 토큰 값이 입력되었다.](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/7.png)

사진 2-2. 리프레시 발급 후의 DB 상의 결과. 필자가 이전에 해당 계정으로 테스트한 적이 있어 이미 해당 레코드가 생성되어 있다. 다만 INVALID에서 VALID로 상태가 바뀌었으며, refresh_token에는 아무 값이 없다가 새로운 토큰 값이 입력되었다.

그 후 인증이 필요한 URI 요청을 하면 다음과 같이 정상적으로 응답 받는다.

![사진 2-3. JWT 기반 인증 후 인증이 필요한 URI 요청 후의 응답 결과.](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/8.png)

사진 2-3. JWT 기반 인증 후 인증이 필요한 URI 요청 후의 응답 결과.

로그아웃을 요청하면 다음의 결과를 얻는다.

![사진 2-4. 로그아웃 요청 후의 결과. 두 토큰을 담은 쿠키들이 삭제된 것을 볼 수 있다. ](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/9.png)

사진 2-4. 로그아웃 요청 후의 결과. 두 토큰을 담은 쿠키들이 삭제된 것을 볼 수 있다. 

![사진 2-5. 로그아웃 이후 DB에서의 리프레시 토큰 상태. 해당 토큰값이 삭제되었으며, INVALID 상태로 전환되었다. ](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/10.png)

사진 2-5. 로그아웃 이후 DB에서의 리프레시 토큰 상태. 해당 토큰값이 삭제되었으며, INVALID 상태로 전환되었다. 

이번에는 Access Token의 만료 시간을 매우 짧게 잡아 로그인 이후 가만히 두었다가 만료 시간 이후에 인증이 필요한 URI 요청 시의 결과이다. 

![사진 2-6. Access Token 만료 시간을 짧게 잡은 후, 만료 시간이 지날 때까지 기다린 후 요청을 한 결과. 응답 메시지에서는 인증되지 않았으니 재로그인 또는 액세스 토큰을 발급하라는 메시지가 뜨고, 브라우저에는 액세스 토큰 쿠키가 사라진 것을 볼 수 있다. ](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/11.png)

사진 2-6. Access Token 만료 시간을 짧게 잡은 후, 만료 시간이 지날 때까지 기다린 후 요청을 한 결과. 응답 메시지에서는 인증되지 않았으니 재로그인 또는 액세스 토큰을 발급하라는 메시지가 뜨고, 브라우저에는 액세스 토큰 쿠키가 사라진 것을 볼 수 있다. 

이제 Refresh Token을 가지고 새로운 Access Token이 발급되는지 확인해보겠다. 

![사진 2-7. 새 Access Token 발급을 위해 쿠키로 보관하고 있던 Refresh Token 값을 Request Body에 실어 요청한 후의 결과. 위 사진의 오른쪽을 보면 Access Token 쿠키가 새로 생성된 것을 볼 수 있다. ](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/12.png)

사진 2-7. 새 Access Token 발급을 위해 쿠키로 보관하고 있던 Refresh Token 값을 Request Body에 실어 요청한 후의 결과. 위 사진의 오른쪽을 보면 Access Token 쿠키가 새로 생성된 것을 볼 수 있다. 

이 후 다시 인증이 필요한 URI 요청을 하면…

![사진 2-8. 새 Access Token 발급 후 인증을 요구하는 URI 요청 후의 응답 결과. 정상적으로 응답 결과를 받아 볼 수 있다. ](/images/2025-02-28/spring%20security-jwt%20%EA%B8%B0%EB%B0%98%20%EC%9D%B8%EC%A6%9D/13.png)

사진 2-8. 새 Access Token 발급 후 인증을 요구하는 URI 요청 후의 응답 결과. 정상적으로 응답 결과를 받아 볼 수 있다. 

이로써 Access Token과 Refresh Token을 이용한 JWT 기반 인증 구현에 성공하였다. 

# 보안적 측면에서의 고려사항들

그런데 JWT 기반 인증의 단점은 토큰 탈취의 우려가 있다는 점이다. Access Token의 만료 시간을 짧게 잡으면 해커가 이를 탈취하더라도 이미 만료되어 쓰지 못하거나 오랫동안 사용하지 못하는 점은 있어도, 이 자체로는 탈취 자체를 막을 수 없다. 이것은 Refresh Token도 마찬가지이다. 

클라이언트, 즉 브라우저 상에서는 서버로부터 넘어오는 토큰을 저장할 여러 후보 공간들이 있다. 

- 쿠키 (Cookie)
- 로컬 스토리지 (Local Storage)
- 세션 스토리지 (Session Storage)
- 지역 변수 (Local Variable)

필자가 조사해본 결과, 대부분의 사람들이 말하길, 액세스 토큰은 지역 변수(자바스크립트)에 담고, 리프레시 토큰은 쿠키에 담는 것이 가장 안전하다고 한다. 스토리지의 경우 XSS(Cross Site Scripting, 자바스크립트를 이용한 보안 공격), CSRF 등의 보안 공격에 취약하다고 한다. 애초에 스토리지는 자바스크립트를 통해 접근, 조작할 수 있기에 이러한 보안 공격에 취약한 것이다. 

반면 쿠키와 지역 변수는 스토리지에 비해 상대적으로 안전하다고 한다. 지역 변수의 경우, 보통 프론트엔드 측에서 사이트를 구성할 때 리액트와 같이 자바스크립트 기반의 UI 라이브러리 또는 프레임워크를 이용하여 구현할 텐데, 이 자바스크립트의 지역 변수에 액세스 토큰을 담아 저장하면 이 지역 변수는 함수 scope 내에서만 접근 가능하기에 상대적으로 안전하다고 한다. 

쿠키의 경우, 자바스크립트로 접근 불가능하도록 HttpOnly 속성값을 설정하고, HTTPS 프로토콜에서만 전송하도록 설정, sameSite (CORS)로 설정하여 다른 출처(origin)로부터의 무분별한 요청을 막도록 하면 상대적으로 안전하게 쿠키값을 보관할 수 있다고 한다. 그래서 앞서 필자는 JwtCookieUtil 클래스의 `addCookieWithJwt` 메서드에서 새로 생성된 쿠키에 다음의 설정들을 한 것이다. 

```java
Cookie jwtCookie = new Cookie(
    jwtCookieRequest.getCookieName(), 
    jwtCookieRequest.getToken()
);
        
// 클라이언트의 조작에 의한 변형 방지. XSS 공격 방지
jwtCookie.setHttpOnly(true);
        
// HTTPS에서만 사용할 경우 true로 설정. HTTP에서도 사용할 경우 false.
jwtCookie.setSecure(false);
        
// 전체 경로에서 사용 가능.
jwtCookie.setPath("/");
        
jwtCookie.setMaxAge(jwtCookieRequest.getMaxAge());
httpServletResponse.addCookie(jwtCookie);
```

코드 6-1. 쿠키 보안 관련 설정. 

물론 이러한 조치들을 취해도 모든 보안 공격을 완벽히 막을 수는 없다고 한다. 그래서 쿠키에 저장된 리프레시 토큰도 탈취의 위험성이 아예 없는 것은 아니다. 이러한 리프레시 토큰의 보안적 취약점을 보완하기 위한 기술로 RTR(Refresh Token Rotation)이 있다고 하는데, 이에 대해서는 다음 글에서 살펴보도록 하겠다. 

---

References

[1] JWT 안전하게 저장하는 방법

[JWT를 안전하게 저장하는 방법](https://bbogle2.tistory.com/entry/JWT-%EC%96%B4%EB%94%94%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%B4%EC%95%BC-%ED%95%A0%EA%B9%8C)

[2] JWT에 대한 내용 및 토큰 저장 위치 관련 글

[[인증인가] Access Token, Refresh Token의 저장 위치에 대한 고찰](https://olrlobt.tistory.com/98)

[3] 참고 자료) JWT 기반 인증 예시

[Spring Security + JWT + Redis를 이용한 인증 실습](https://blog.naver.com/nebi25/222905745654)

[4] 참고 자료) Refresh Token, RTR(Refresh Token Rotation), Redis

[[인증/인가] RefreshToken은 왜 Redis를 사용해 관리할까? (with. RTR 방식)](https://pgmjun.tistory.com/125)

[5] 신선영, “스프링 부트 3 백엔드 개발자 되기 - 자바 편, 2판”, (골든래빗)

[6] [jwt refreshToken을 db에서 유지하는 이유가 궁금합니다. \| OKKY Q&A](https://okky.kr/questions/1409503)

[7] 참고 자료) MSA

[[MSA] MSA란 무엇인가? 개념 이해하기](https://wooaoe.tistory.com/57)

[8] authorization bearer

[Daum 카페](https://cafe.daum.net/flowlife/HqLp/36)

[9] JWT 공식 사이트. 여기서 JWT의 인코딩, 디코딩 과정을 체험해볼 수 있다. 

[JWT.IO](https://jwt.io/)

[10] 스프링에서 JWT 기반 인증 구현 시 사용되는 jjwt 의존성의 공식 문서

[https://github.com/jwtk/jjwt](https://github.com/jwtk/jjwt)

[11] 인증, 인가 예외 처리 과정

[Architecture :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-exceptiontranslationfilter)