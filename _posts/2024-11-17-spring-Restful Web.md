---
title: "[Spring] RESTFul Web"
category: "Spring"
tag: ["Spring", "Spring Boot", "Java", "REST API", "HTTP", "RESTFul", "API", "End Point", "Query String", "Path Variable", "Postman", "Swagger"]
---

# 웹을 이용한 데이터 송수신 개요

본래 웹은 서버로부터 HTML, 이미지 등의 여러 컨텐츠들을 다운로드받아 사용자가 이러한 컨텐츠를 소비하는 일종의 서비스 역할을 하였다. 즉 어떻게 보면 서버에서 제공하는 컨텐츠만을 보는 일방적인 형태라 볼 수 있다. 그러나 시간이 지나면서 클라이언트와 서버 간에 양방향으로 데이터를 송수신하는 매개체로 웹을 사용하기 시작하였다. 이 때의 데이터 형태는 기존의 HTML이 아닌 JSON, XML 등 데이터를 송수신하기에 더 적합한 형태로 바뀌었다. 최근에는 여러 형태들 중 데이터를 송수신하기 가장 가볍고 가독성도 좋은 JSON 형태로 데이터를 주고받는 것이 가장 대중적이라고 한다. 

웹, 즉 HTTP를 이용하여 데이터를 주고받는 개념이 생기기 이전에는 클라이언트와 서버 간에 데이터로 통신을 하기 위해서는 HTTP가 아닌 별도의 프로토콜을 가지는 통신 프로그램을 개발해야 했다고 한다. 데이터 송수신 가능한 별도의 통신 프로그램을 개발자가 개발해야하는 구조였기에 보안, 성능, 인증, 안정성과 같은 분야에 문제가 발생할 수 있는 구조였다고 한다. 

이와 같이 클라이언트 - 서버 간의 데이터 통신을 위해 HTTP(웹)을 사용하지 않고 별도의 통신 프로그램을 제작해야할 때 문제점은 다음과 같다. 

- 보안, 성능 등의 여러 문제들을 스스로 해결하여 개발해야 했기에 개발자의 역량에 의존하게 되고, 따라서 자칫 결함 있는 통신 프로그램을 제작할 수도 있다.
- 클라이언트 - 서버 간의 통신 시 요청, 응답 등의 여러 규격들을 정의하는 프로토콜을 자체적으로 정의해야 한다. 이러한 이유로 각 통신 프로그램마다 프로토콜이 서로 달라 호환성이 안 좋아서 범용적으로 사용하기 어렵다.

하지만 데이터 통신의 매개체를 웹으로 선택하여 사용하면 다음의 이점들을 얻을 수 있다. 

- 장기간 검증된 서버 프로그램 사용으로 보안, 안정성 등의 문제에서 자유로우며, 개발자가 일일이 데이터 송수신을 위한 서버 프로그램을 개발하지 않아도 된다.
- HTTP 프로토콜이라는 단일한 프로토콜을 사용하기에 호환성이 좋다. 웹에서도 접근할 수 있고, 모바일과 같은 다른 기기에서도 접근 가능하다.

하지만 초창기에는 이러한 장점에도 불구, 웹을 이용한 데이터 송수신에 대한 인기가 별로 없었다고 한다. 그 이유는 다음과 같다.

- 웹, HTTP는 기본적으로는 HTML 등 응답 “페이지”를 클라이언트에 전송하는 구조이지 데이터 송수신을 위한 구조가 아니다. 따라서 애초에 데이터 통신에 적합하진 않았다.
- 웹을 데이터 통신용으로 사용하기 위해선 별도의 규격을 추가적으로 사용했어야 해서 그 구현 난이도가 높고, 접근성도 떨어졌다고 한다.

하지만 JSON 형식의 등장으로 인해 다시 웹을 이용한 데이터 통신에 관심이 높아졌다고 한다. 기존의 XML 등의 형식과는 달리, JSON은 애초부터 웹 브라우저에서 해석 가능한 자바스크립트의 객체 형태이며, 데이터에 비해 데이터를 감싸는 태그가 불필요하다 느껴질 정도로 많이 존재하는 XML에 비해 그 구조도 상대적으로 매우 가볍고 정말 데이터만 담았기에 HTTP 통신에 부담을 덜 주는 구조였다. 그렇다고 해서 비구조적인 형태도 아니라서 구조화된 데이터 통신에 적합한 형태였다. 

따라서 지금은 클라이언트 - 서버 간 데이터 통신에 웹과 더불어 JSON을 이용하여 데이터를 송수신하는 형태로 많이 사용하고 있다고 한다(물론 아직 XML 형식으로 데이터를 송수신하는 곳도 있다). 거기에 더해, 클라이언트 - 서버 간의 데이터 통신 기능을 구현하는 방법에 대한 아키텍쳐 중 하나인 REST 원칙이 많이 사용된다고 한다. 

이렇게 웹을 이용한 데이터 통신의 개념 등장으로 인해 기존에는 SSR(Server Side Rendering) 방식을 이용하여 서버에서 HTML 형식의 응답 “페이지”를 생성하여 클라이언트에 전송하는 구조였다면 이제는 서버는 JSON, XML 등의 형식을 가지는 응답 “데이터”만 전송하고 프론트엔드에서 이 데이터를 받아 Javascript, React 등의 프론트엔드 구현 도구들을 이용하여 대신 UI를 구성하는 CSR(Client Side Rendering) 방식의 웹 페이지 구현이 가능해졌다. 이로 인해 서버가 요청 처리와 페이지 구성이라는 두 가지 일을 하던 방식에서 페이지 구성을 클라이언트 측에 위임함으로써 서버의 부담을 줄이고 역할도 분담할 수 있다는 장점이 있다. 

# REST와 RESTFul

앞서 말했듯, REST(Representational State Transfer)는 웹 상에서 클라이언트와 서버 사이에서 데이터로 통신하는 기능을 구현하기 위한 여러 아키텍쳐 중 하나이며, 현대에 이르러서는 대부분 이 REST 원칙으로 REST API를 구현한다고 한다. 

이 REST가 무엇인지를 이해하기 쉽게 딱 한마디로 말하기는 어렵다고 생각한다. 그래서 이 REST가 무엇인지를 이해하기 위해선 REST를 구성하는 세 가지 요소를 먼저 소개하는 게 좋다고 생각된다. 

REST 구성 요소에는 다음과 같이 3가지 요소로 구성되어 있다. 

1. 자원 (Resource) : 서버 내에 존재하는 텍스트, DB의 데이터, 이미지, 동영상 등이 모두 자원이며, 고유하게 식별하기 위해 URI(Uniform Resource Identifier)를 각각의 리소스에 부여한다. 이로 인해 클라이언트가 서버 내 특정 리소스에 접근하고자 한다면 `/users/{id}/posts/{no}` 와 같이 서버에서 제공하는 URI로 접근 가능하다. 
2. 행위(Verb) : 서버 내 자원에 접근하였다면 해당 자원으로 무엇을 할 것인지 그 행위를 정해야 한다. 해당 자원을 조회할 것인지, 새로운 자원을 생성하여 서버에 저장하게끔 할 것인지, 수정할 것인지 삭제할 것인지 등을 결정해야 한다. REST 원칙에 기반하여 설계된 데이터 통신 시스템을 RESTFul 시스템이라 하는데, 이러한 RESTFul 시스템에서는 HTTP 요청 method인 GET, POST, PUT, DELETE 등을 이용하여 행위를 지정한다. 이로 인해 클라이언트는 서버 내 리소스에 대해 마치 SQL에서의 Select, Insert, Update, Delete처럼 CRUD(Create, Read, Update, Delete) 작업을 요청할 수 있는 것이다. 참고로 각각의 CRUD와 그에 맞는 HTTP Request Method는 다음의 표로 정리하였다.

| **CRUD** | **HTTP Method** | **예시** |
| --- | --- | --- |
| CREATE | POST | 블로그에 새 게시글을 생성한다. |
| READ | GET | 블로그에 있던 글을 읽어온다. |
| UPDATE | PUT | 기존에 쓴 글을 수정한다. |
| DELETE | DELETE | 특정 블로그 글을 삭제한다. |

1. 표현 (Representation) : 클라이언트가 특정 자원과 그 자원에 대한 행위를 지정하여 이를 서버에 요청했을 때 서버가 클라이언트에게 응답한 자원의 상태(state)를 “표현(Representation)”이라고 부른다. 
    1. 자원은 JSON, XML 등의 여러 가지 형식으로 “표현”될 수 있다. 
    2. 서버는 클라이언트에 응답 시 응답한 표현의 형식을 ‘Content-Type’ 헤더를 통해 명시한다. JSON 형태라면 `Content-Type: application/json` 과 같이 명시할 수 있다. 
    3. 클라이언트는 Accept 헤더 정보를 요청에 포함시킴으로써 원하는 표현 형식을 명시할 수 있다. `Accept: application/json` 이라 한다면 클라이언트가 JSON 형식의 응답을 원함을 명시하는 것이다. 
    4. 요청받은 자원을 반드시 그대로 응답할 필요는 없다. 클라이언트의 요청에 따라 같은 자원이라도 그 요구에 맞게 표현 형식을 변경하여 응답을 전송할 수 있다. 같은 JSON 형식이라 하더라도 그 내부의 구조를 서버에서 가공하여 원하는 형태로 클라이언트에게 제공할 수도 있는 것이다. 비유를 들자면 주방이라는 서버에 음식 재료라는 자원이 있을 때, 이 음식 재료들을 날 것 그대로 클라이언트에게 전송할 것인지 아니면 해당 재료들을 토대로 음식으로 만들어서 전송할 것인지 판별하여 각자 다른 형식으로 응답을 보낼 수 있다. 마치 클라이언트가 요리업체라면 필요한 음식 재료들을 공급하는 공급업체처럼 응답할 수 있고, 클라이언트가 일반 손님이라면 음식 재료들을 가공하여 음식으로 제공하는 쉐프처럼 응답할 수 있는 것과 비슷한 이치이다. 

이렇듯, REST는 클라이언트와 서버 간의 데이터 통신에 대해 자원, 행위, 표현이라는 관점으로 바라본다. 각각의 고유한 URI가 부여된 “자원”들을 클라이언트가 서버에게 어떠한 “행위” (주로 CRUD)를 가하도록 요청을 한다. 서버는 그 요청 처리에 의한 리소스의 상태 정보(사용자라는 리소스를 요청했다면 사용자의 아이디, 이메일 등의 정보들이 이에 해당.), 즉 “표현”으로 응답을 하는 형태의 아키텍쳐라 보면 되겠다. 

이와 더불어 REST의 특징에는 다음이 있다. 

- Uniform 인터페이스(인터페이스 일관성) : 자원에 대한 요청 및 행위를 통일되고 일관된 인터페이스로 수행한다. REST는 HTTP 프로토콜을 따르기에 프로그래밍 언어와 플랫폼 등에 종속되지 않아 항상 일관적인 방식으로 클라이언트에게 데이터를 전송할 수 있다.
- 무상태성 (stateless) : 클라이언트의 요청으로 넘어온 상태 정보를 서버에 저장하지 않는다. 따라서 각각의 클라이언트의 요청들을 독립적으로 처리한다. 상태 정보를 기반으로 진행하지 않기에 클라이언트로 응답 데이터를 전송하기 위한 서버 측의 비즈니스 로직 구현이 자유로워진다.
- 캐시 가능성 (Cacheable) : 클라이언트로부터 똑같은 요청을 받았을 때 이를 똑같이 서버에서 응답하는 것이 아니라 중간 서버를 통해 대신 응답을 할 수 있는 임시 저장 공간을 캐시(cache)라 한다. HTTP에서는 이러한 캐싱 기능을 사용할 수 있어 똑같은 요청에 대해 중간의 캐시 서버가 대신 응답을 하기에 서버에 부담을 주지도 않고 응답 속도도 빠르다는 장점이 있다.
    - tmi)
        1. 캐시는 local cache와 global cache로 나뉘며, local cache는 개인용 컴퓨터처럼 어떤 특정 전자기기 내에서만 저장되어 사용되는 캐시이다. global cache는 일종의 캐시 서버가 있어서 여러 서버에서 해당 캐시 서버에 접근하여 캐시 저장 및 조회용으로 쓰는 것을 말한다.
        2. 캐시라는 저장공간도 그 용량이 한계가 있기 때문에 모든 데이터를 다 담을 수는 없다. 따라서 만약 캐시에 없는 정보를 클라이언트가 요청했다면 서버는 서버에 있는 데이터를 찾아 응답해줘야 한다.
- 계층화된 시스템 (Layered System) : REST 서버는 여러 계층으로 구성할 수 있어 원한다면 여러 기능들을 수행하는 중간 서버들을 확장하여 구성할 수도 있다. 클라이언트에서는 이를 알 수 없고, 그저 요청을 할 수 있는 URI만 알면 된다.
- 클라이언트 - 서버 아키텍쳐 : 서버는 API를 제공하고, 이 API를 통한 요청 시의 비즈니스 로직 구현에만 집중할 수 있으며, 클라이언트는 서버에서 제공한 데이터를 토대로 사용자 인증, 세션 및 로그인 정보 등 사용자 정보를 관리하는 역할을 맡아 할 수 있다. 즉, 서버와 클라이언트의 역할을 서로 분담하여 서로에 대한 의존성을 낮출 수 있다.
- 자가 서술적 메시지 (Self-descriptivenness) : REST API의 메시지만 보고도 이를 쉽게 이해할 수 있도록 구성되어 있다.

이러한 REST 설계 원칙을 준수하는 시스템을 보고 RESTFul하다고 표현하며, 이러한 REST 설계 원칙을 기반으로 하는 API를 REST API라고 한다. 

## API와 Endpoint

API는 Application Programming Interface의 준말로, 프로그램들 간 상호작용을 위한 연결 그 자체를 의미한다. 컴퓨터와 사람 사이를 연결하여 상호작용을 돕는 사용자 인터페이스(UI)와 달리 프로그램과 프로그램 사이를 연결하여 데이터 등의 상호작용을 할 수 있는 “연결 통로”라 볼 수도 있고, “매개체”라 할 수도 있겠다. 

그 중에서도 웹에서의 HTTP 요청, 응답을 통해 서버-클라이언트 간 데이터 통신을 가능케 하는 API를 웹 API이라고 한다. 즉, 이를 이용하면 API 개발자는 클라이언트에게 제공하고자 하는 데이터들을 RESTFul하게 구성하여 특정 자원 및 행위 요청 명세서만 알려준다면 클라이언트에서는 이를 이용하여 원하는 데이터를 얻어올 수 있는 것이다. 이미 존재하는 네이버의 블로그 및 지도 API 등이 그 예라 볼 수 있겠다. 

엔드포인트(Endpoint)란, 커뮤니케이션 채널의 한 쪽 끝을 의미한다. 즉 위에서 설명한 API와 결합하여 설명한다면, 서버 측에서 특정 데이터 제공을 위해 제공하는 URI를 의미한다. REST에서는 자원, 행위, 표현 이 3가지 요소 중 자원의 식별자인 URI를 의미한다고 보면 되겠다. 다음과 같은 REST에서의 URI들을 End point라 할 수 있다. 

```
/api/members
/api/members/{id}
/api/pages/{num}
```

# REST API 설계 원칙

REST API 설계 시 지켜야할 원칙에는 여러 가지가 있다. 여기서는 이들을 정리하여 소개하겠다.

URI 설계 원칙

- URI는 자원(Resource)을 잘 표현해야 한다. 이를 위해 자원의 위치 및 이름을 명확히 나타내야 한다. 그래야 클라이언트 측에서는 해당 URI가 결국에는 어떤 자원을 전송하는지 미리 파악할 수 있기 때문이다.
- URI는 직관적으로 만들어 클라이언트가 단 번에 이해할 수 있도록 설계한다.
- URI에는 동사형 언어보다는 명사형 언어를 작성한다. 즉, 행위를 포함시키지 않는다. 동사의 경우 행위는 HTTP Method를 통해 대신 표현할 수 있기 때문이다.
    
    ```
    /delete-member/{id}  X
    /member/{id}  O
    ```
    

- 대문자보다는 소문자를 사용한다.
    
    ```
    /Member X
    /member O
    ```
    

- `jpg`, `mp4` 와 같이 확장자를 사용하지 않는다.
    
    ```
    /proflie/image.jpg  X
    /profile/image  O
    ```
    

- URI 마지막에 슬래시(/)를 포함시키지 않는다.
    
    ```
    /member/  X
    /member   O
    ```
    

- 단수보다는 복수형 명사를 사용하여 추상적 개념보다는 구체적인 개념을 표현할 수 있도록 한다.
    
    ```
    /member   X
    /members  O
    ```
    

- 두 개의 자원이 서로 관계가 있을 경우 이러한 관계를 표현하는 방법에는 두 가지가 있다.
    - `/users/{name}/posts` 과 같이 사용자 자원인 users와 특정 사용자가 작성한 글이라는 자원인 posts에 대해, `/resource명/해당 리소스 내 특정 자원의 identifier/연관된 다른 리소스명` 형태로 작성하면 된다.
    - `/users/{name}/have/posts` 와 같이 `/리소스명/식별자/관계설명/연관된 다른 리소스명` 형태를 이용하여 서로 간의 관계를 설명할 수 있다. 이 방법은 리소스 간 관계가 복잡할 때 주로 사용된다고 한다.
- 만약 URI 명이 너무 길어져서 띄어쓰기가 필요할 경우, 띄어쓰기 또는 언더바(_) 대신 하이픈(-)을 사용한다.
    
    ```jsx
    /members id   X
    /members_id   X
    /members-id   O
    ```
    
- 슬래시(/) 사용 시 하나의 path 내에 있는 자원명들은 서로 계층적 관계를 가지도록 구성한다.
    - `/admins/managers/staffs/` : 어드민이 매니저를 관리하고, 매니저가 직원을 관리하는 수직적, 계층적일 경우 이를 위와 같이 표현.

## Query String VS Path Variable

URI 경로에 값을 파라미터로 주고자 할 때 query string으로 줄 수도 있고, path variable로 줄 수도 있다. 그렇다면 이 둘은 각각 어떨 때 사용하는 것이 좋을까?

path variable의 경우, 특정 자원의 세부 데이터를 식별자(identifier)로 접근하고자 할 때 사용된다.

- 예) `/members/3` : 3번 id를 가지는 멤버 자원에 접근

즉, 요청 URI에 넘길 요청 정보가 식별자로서의 역할을 할 경우 path variable을 사용한다. 

반면, query string에서는 특정 세부 자원의 식별자로서의 역할이 아닌, 페이지 번호, 정렬 기준, 페이지네이션, 검색 등 부가 정보를 추가하고자 할 때 사용된다. 

- 예1) `/posts?offset=5&limit=10` : 전체 글 데이터들 중 5 페이지부터 시작(offset)하여 최대 10개의 데이터들(limit)만을 읽어오고자 할 경우. (페이징) 참고로 offset ~ limit 표기법은 facebook에서 사용하는 스타일이라고 한다.
- 예2) `/members?ordering=points` : 멤버들을 보유한 포인트 순으로 정렬하여 가져오고자 할 때 사용.
- 예3) `/members?search=김큐엘` : 멤버들 중 “김큐엘”이란 아이디를 가지는 멤버를 찾고자 할 경우

# Spring에서의 REST API 제작

이번 챕터에서는 Spring에서 CRUD가 가능한 REST API를 제작하는 방법과 그 과정에서 사용하는 어노테이션들에 대해서 소개하겠다. 여기서는 로컬 DB와 연동하여 클라이언트로부터의 CRUD 요청이 실행되도록 할 것이며, 이를 위해 Spring Data JPA를 사용해보겠다. 

연동을 위한 DB 테이블 구조는 다음과 같다.

![사진 1-1. REST API 연습을 위한 로컬 테이블 구조](/images/2024-11-17/spring-Restful%20Web/1.png)

사진 1-1. REST API 연습을 위한 로컬 테이블 구조

![사진 1-2. REST API 연습을 위한 로컬 테이블 내 데이터들. 이전에 필자가 연습을 위해 넣어둔 데이터들이 보인다. ](/images/2024-11-17/spring-Restful%20Web/2.png)

사진 1-2. REST API 연습을 위한 로컬 테이블 내 데이터들. 이전에 필자가 연습을 위해 넣어둔 데이터들이 보인다. 

Eclipse에서 Spring Boot를 이용하여 새 프로젝트 폴더를 생성한 후, 다음과 같이 설정 파일들의 내용을 설정하였다.

```
spring.application.name=RestfulApi

# Mariadb server connect
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://127.0.0.1:3308/mvc
spring.datasource.username=root

# jpa
spring.jpa.properties.hibernate.dialect=org.hibernate.dialect.MariaDBDialect
```

코드 1-1. application.properties

```
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.4.1'
	id 'io.spring.dependency-management' version '1.1.7'
}

group = 'com.jerocaller'
version = '0.0.1-SNAPSHOT'

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(21)
	}
}

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	runtimeOnly 'org.mariadb.jdbc:mariadb-java-client'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
	
	// Swagger
	// http://localhost:8080/swagger-ui/index.html
  implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.7.0'
}

tasks.named('test') {
	useJUnitPlatform()
}

```

코드 1-2. build.gradle

그리고 Spring Data JPA를 이용하여 DB의 테이블과 매핑하기 위한 Entity 및 Repository, 그리고 요청, 응답용 DTO를 생성하였다. 

```java
package com.jerocaller.entity;

import com.jerocaller.dto.ProductsDto;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@Table
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Products {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(nullable = false, length = 11)
	private Integer id;
	
	@Column(nullable = false, length = 20)
	private String name;
	
	@Column(nullable = false, length = 11)
	private Integer price;
	
	@Column(nullable = false, length = 20)
	private String category;
	
	public static Products toInsertEntity(ProductsDto dto) {
		
		return Products.builder()
			.name(dto.getName())
			.price(dto.getPrice())
			.category(dto.getCategory())
			.build();
		
	}
	
	public static Products toUpdateEntity(int no, ProductsDto dto) {
		
		return Products.builder()
			.id(no)
			.name(dto.getName())
			.price(dto.getPrice())
			.category(dto.getCategory())
			.build();
		
	}
	
}

```

코드 1-3. Products.java

```java
package com.jerocaller.repository;

import java.util.List;

import org.springframework.data.jpa.repository.JpaRepository;

import com.jerocaller.entity.Products;

public interface ProductRepository 
	extends JpaRepository<Products, Integer> {
	
	List<Products> findByCategory(String category);
	
}

```

코드 1-4. ProductRepository.java

```java
package com.jerocaller.dto;

import com.jerocaller.entity.Products;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ProductsDto {
	
	private String name;
	private Integer price;
	private String category;
	
	public static ProductsDto toDto(Products entity) {
		
		return ProductsDto.builder()
			.name(entity.getName())
			.price(entity.getPrice())
			.category(entity.getCategory())
			.build();
		
	}
	
}

```

코드 1-5. ProductsDto.java

그리고 이 Entity를 기반으로 CRUD할 Service 계층 코드를 다음과 같이 작성하였다.

```java
package com.jerocaller.service;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

import org.springframework.stereotype.Service;

import com.jerocaller.dto.ProductsDto;
import com.jerocaller.entity.Products;
import com.jerocaller.repository.ProductRepository;

import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class ProductService {
	
	private final ProductRepository productRepository;
	
	public List<ProductsDto> getAll() {
		
		List<ProductsDto> products = productRepository.findAll()
			.stream()
			.map(ProductsDto :: toDto)
			.collect(Collectors.toList());
		
		return products;
		
	}
	
	public ProductsDto getOne(int no) throws EntityNotFoundException {
		
		Products product = productRepository.findById(no)
			.orElseThrow(() -> new EntityNotFoundException("찾고자 하는 정보가 없습니다."));
		
		return ProductsDto.toDto(product);
		
	}
	
	public List<ProductsDto> getByCategory(String category) {
		
		List<ProductsDto> products = productRepository
				.findByCategory(category)
				.stream()
				.map(ProductsDto :: toDto)
				.collect(Collectors.toList());
		
		return products;
		
	}
	
	public ProductsDto insert(ProductsDto dto) {
		
		Products product = Products.toInsertEntity(dto);
		Products savedProduct = productRepository.save(product);
		
		return ProductsDto.toDto(savedProduct);
		
	}
	
	public ProductsDto update(int no, ProductsDto dto) {
		
		Products product = Products.toUpdateEntity(no, dto);
		Products updatedProduct = productRepository.save(product);
		
		return ProductsDto.toDto(updatedProduct);
		
	}
	
	public boolean deleteOne(int no) {
		
		try {
			productRepository.deleteById(no);
		} catch (IllegalArgumentException e) {
			return false;
		}
		
		return true;
		
	}
	
}

```

코드 1-6. ProductService.java

그리고 실질적으로 클라이언트의 요청을 받고 응답을 전송할 Controller 계층 코드를 다음과 같이 구성하였다. 

```java
package com.jerocaller.controller;

import java.util.List;

import org.hibernate.dialect.lock.OptimisticEntityLockException;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.jerocaller.dto.ProductsDto;
import com.jerocaller.service.ProductService;

import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;

@RestController
@RequiredArgsConstructor
@RequestMapping("/products")
public class ProductController {

	private final ProductService productService;
	
	@GetMapping
	public ResponseEntity<List<ProductsDto>> getAll() {
		
		List<ProductsDto> products = productService.getAll();
		
		return ResponseEntity.ok(products);
		
	}
	
	@GetMapping("/{id}")
	public ResponseEntity<ProductsDto> getOne(
		@PathVariable("id") int id
	) {
		
		ProductsDto result = null;
		
		try {
			result = productService.getOne(id);
		} catch (EntityNotFoundException e) {
			return ResponseEntity.notFound().build();
		}
		
		return ResponseEntity.ok(result);
		
	}
	
	@GetMapping("/filter")
	public ResponseEntity<List<ProductsDto>> getByCategory(
		@RequestParam(name = "category") String category
	) {
		
		List<ProductsDto> products = productService.getByCategory(category);
		
		return ResponseEntity.ok(products);
		
	}
	
	@PostMapping
	public ResponseEntity<ProductsDto> insert(
		@RequestBody ProductsDto request
	) {
		
		ProductsDto result = null;
		
		try {
			result = productService.insert(request);
		} catch (IllegalArgumentException e) {
			return ResponseEntity.badRequest().build();
		} catch (OptimisticEntityLockException e) {
			return ResponseEntity.internalServerError().build();
		}
		
		return ResponseEntity.ok(result);
		
	}
	
	@PutMapping("/{id}")
	public ResponseEntity<ProductsDto> update(
		@PathVariable("id") int no, 
		@RequestBody ProductsDto request
	) {
		
		ProductsDto result = null;
		
		try {
			result = productService.update(no, request);
		} catch (IllegalArgumentException e) {
			return ResponseEntity.badRequest().build();
		} catch (OptimisticEntityLockException e) {
			return ResponseEntity.internalServerError().build();
		}
		
		return ResponseEntity.ok(result);
		
	}
	
	@DeleteMapping("/{id}")
	public ResponseEntity<Boolean> deleteOne(
		@PathVariable("id") int no
	) {
		
		boolean result = productService.deleteOne(no);
		
		return ResponseEntity.ok(result);
		
	}
	
}

```

코드 1-7. ProductController.java

또한, 샘플용 데이터를 응답하기 위한 Controller, Service 코드는 각각 다음과 같다.

```java
package com.jerocaller.service;

import java.util.HashMap;
import java.util.Map;

import org.springframework.stereotype.Service;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class SampleService {

	/**
	 * Map 자료구조로 응답 데이터 전송을 위한 샘플 데이터 생성
	 * 
	 * @return
	 */
	public Map<String, Object> getSampleData() {
		
		Map<String, Object> sample = new HashMap<String, Object>();
		
		sample.put("name", "샘플 데이터");
		sample.put("category", "Sample Test");
		
		return sample;
		
	}
	
}

```

코드 1-8. SampleService.java

```java
package com.jerocaller.controller;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;

import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

import com.jerocaller.service.SampleService;

import lombok.RequiredArgsConstructor;

@RestController
@RequestMapping("/samples")
@RequiredArgsConstructor
public class SampleController {
	
	private final SampleService sampleService;
	
	@GetMapping
	public ResponseEntity<List<Map<String, Object>>> getSampleData(
		@RequestParam Map<String, Object> params
	) {
		List<Map<String, Object>> responseResult = new ArrayList<Map<String,Object>>();
		Map<String, Object> result = sampleService.getSampleData();
		
		responseResult.add(result);
		responseResult.add(params);
		
		return ResponseEntity.ok(responseResult);
		
	}
	
}

```

코드 1-9. SampleController.java

REST API의 Endpoint는 Controller 계층의 `@ ~ Mapping` 어노테이션을 통해 정의, 생성된다. 어떻게 보면 사실상 REST API는 거의 Controller 계층에서 만들어진다고 봐도 될 것이다. REST API 제작을 위해 Controller 계층에서 사용된 위 코드 1-7, 1-9에서 보인 어노테이션들을 정리하면 다음과 같다.

- `@RestController` : 해당 Controller 클래스가 REST API 용도임을 선언. `@Controller` + `@ResponseBody` 와 동일
- `@RequestMapping` : 특정 URI와 매핑하기 위한 어노테이션. 메서드 수준과 클래스 수준에서 사용할 수 있는데, 메서드 수준에서는 `@GetMapping` , `@PostMapping` 등의 파생 어노테이션을 대신 사용한다. 클래스 수준에서 사용 시 해당 URI은 해당 클래스 내 모든 메서드들과 매핑되는 서브 URI의 공통 URI가 된다. 위 코드 1-7에서의 모든 메서드들의 URI는 `/products` 로 시작된다. 예를 들어 위 코드 1-7의 ProductController의 `getByCategory()` 메서드에는 `/products/filter?category=food` 와 같은 URI가 매핑되게 된다. 클라이언트 측에서 이 URI를 호출하면 `getByCategory()` 메서드가 호출되며, 이 메서드의 반환값이 곧 클라이언트에게 응답 데이터로 전송되어 보이는 원리이다.
    - `@GetMapping` - Http Method를 Get으로 하는 URI 매핑 어노테이션. 리소스 조회 시 사용된다. CRUD의 R(Read)에 해당.
    - `@PostMapping` - Http Method를 Post로 하는 URI 매핑 어노테이션. 리소스 삽입 시 사용된다. CRUD의 C(Create)에 해당.
    - `@PutMapping` - Http Method를 Put으로 하는 URI 매핑 어노테이션. 리소스의 모든 요소들을 변경할 때 사용된다. CRUD의 U(Update)에 해당.
        - `@PatchMapping` 은 Put과 달리 리소스의 일부만 변경하고자 할 때 사용한다.
    - `@DeleteMapping` - Http Method를 Delete로 하는 URI 매팡 어노테이션. 리소스 삭제 시 사용된다. CRUD의 D(Delete)에 해당.
- `@PathVariable` - URI의 변수, 즉 경로 변수(Path Variable)를 받기 위한 어노테이션. 메서드의 인자에 사용한다. 어노테이션의 인자로 `@ ~ Mapping` 에 매핑된 URI 내 `{...}` 형태의 경로 변수들 중 받고자 하는 Path Variable의 이름을 명시한다.
- `@RequestParam` - Query String을 받기 위한 어노테이션. 메서드의 인자에 사용한다. 이 Query String을 Map 자료구조로 받을 수도 있고, 개별 변수로 받을 수도 있는데, 후자의 경우 해당 어노테이션의 인자로 Query String을 구성하는 key 이름을 명시하여 받을 수 있다.
- `@RequestBody` - HTTP 요청의 Body로 넘어오는 데이터를 받기 위한 어노테이션으로, 메서드의 인자에 사용한다. 주로 Body를 통해 데이터를 전송하는 POST, PUT 등의 HTTP Method에 사용된다.

여기에 더해, 위 코드 1-7, 1-9에서는 ResponseEntity라는 클래스가 Controller 내 메서드의 반환값으로 사용되고 있는데, ResponseEntity는 개발자가 상황에 따라 HTTP 응답 상태를 결정할 때 사용되는 클래스이다. 응답 상태가 양호하다고 판단될 때는 `ResponseEntity.ok(...)` 메서드를 사용하여 클라이언트에 HTTP 상태 코드 200, OK의 응답을 전송할 수 있고, 클라이언트가 잘못된 URI로 요청을 했다고 판단될 때에는 `ResponseEntity.badRequest()` 메서드를 사용함으로써 클라이언트의 요청 상태에 따라 각기 다른 응답 상태를 구성할 수 있다는 장점이 있다. 

## JSON 데이터와 매핑될 수 있는 자바 객체

사실 JSON 구조와 자바는 서로 구조 자체가 다르기 때문에 이를 매핑할 무언가가 필요한데, 스프링 부트에서는 자동으로 Jackson이라는 라이브러리가 설치되어 있기에 이 라이브러리가 자동으로 매핑해준다. JSON과 자바 간 매핑 관계는 다음과 같다.

| JSON | Java |
| --- | --- |
| Object ( {…} ) | Map, DTO |
| List ( […]) | List<T> |

사실 위 코드 1-7, 1-9에서 이미 Map, DTO, List를 이용하여 요청 및 응답 데이터를 구성하는 코드가 포함되어 있으니 참고. 

JSON의 Object 타입을 구성할 때 쓰이는 자바 객체로 Map 자료구조와 DTO 클래스가 있는데, 이 둘은 결과는 똑같지만 다음의 차이점을 가지고 있다.

- Map
    - 요청, 응답 데이터를 동적으로 구성할 수 있다. 이로 인해 어떠한 정보가 들어올지 모를 때 사용하면 유용하다.
- DTO
    - 요청, 응답 데이터 구조를 고정시킨다. 이러한 점을 활용하면 개발자가 실수 또는 고의적으로 불필요한 데이터를 추가하는 것을 방지할 수 있다.

필자는 개인적으로 DTO 사용을 선호한다. 개발자의 불필요한 데이터 추가를 방지하기에도 좋고, Map 자료구조 사용 시 특정 프로퍼티의 key를 언급하려면 무조건 문자열 형태로 호출해야 하는데, 이 경우 실수로 오타를 낼 수도 있고 Eclipse와 같은 IDE의 자동 완성 기능을 사용할 수 없다. 반면 DTO 사용 시 프로퍼티 키를 클래스의 필드로 사용하기에 IDE에서 제공하는 자동 완성 기능을 사용할 수 있어 조금 더 안정적인 코드를 작성할 수 있다는 장점 때문이다. 

자, 이제 위 코드를 동작시켜서 앞서 만든 REST API가 제대로 동작하는지 확인해볼 차례이다. 그런데 사실 이대로는 REST API 작동을 테스트해볼 수 없다. 이를 도와주는 도구가 대표적으로 2가지가 있는데, Postman과 Swagger가 있다. 이번에는 이 두 가지 도구를 소개하며, 이들을 통해 REST API를 확인하는 과정을 살펴보겠다. 

## Postman

Postman은 크롬 브라우저에서 확장 프로그램으로 설치하여 브라우저 상에서 REST API를 테스트해볼 수 있는 도구이다. 설치 과정은 다음과 같다. 

1. 크롬 브라우저를 킨 상태에서 구글에서 “chrome extension”이라 검색한다. 그러면 구글에서 제공하는 “chrome 웹 스토어” 사이트에 들어갈 수 있다. 

[Chrome Web Store - Extensions](https://chromewebstore.google.com/category/extensions)

1. 해당 사이트에서 “postman”이라 검색하면 여러 가지 확장프로그램들이 나온다. 필자는 “Tabbed Postman - REST Client”이라는 확장 프로그램을 선택하여 설치하였다.
    
    ![사진 2-1. ](/images/2024-11-17/spring-Restful%20Web/3.png)
    
    사진 2-1. 
    
    ![사진 2-2.](/images/2024-11-17/spring-Restful%20Web/4.png)
    
    사진 2-2.
    

1. 설치를 완료하면 크롬 브라우저 상단 우측에서 다음 사진과 같이 방금 설치한 postman을 볼 수 있는데, 이를 클릭하면 화면 상에서 postman 화면이 뜬다. 
    
    ![사진 2-3.](/images/2024-11-17/spring-Restful%20Web/5.png)
    
    사진 2-3.
    

![사진 2-4. Postman 화면. 필자가 이미 이런저런 테스트를 한 모습도 보인다.](/images/2024-11-17/spring-Restful%20Web/6.png)

사진 2-4. Postman 화면. 필자가 이미 이런저런 테스트를 한 모습도 보인다.

이제 이 화면에서 REST API를 테스트할 수 있게 된다. 실제로 백엔드로 REST API 개발 시, 이 API를 호출하여 화면에 표시할 프론트엔드 구축까지 할 필요없이 바로 테스트를 해볼 수 있다는 장점이 있다. 

이제 이 화면에서 앞서 만든 REST API를 테스트해보겠다. 스프링 부트 서버는 킨 상태여야 한다.

![사진 2-5. ProductController.getAll() 메서드와 매핑된 엔드포인트 호출 결과](/images/2024-11-17/spring-Restful%20Web/7.png)

사진 2-5. ProductController.getAll() 메서드와 매핑된 엔드포인트 호출 결과

위 사진에서는 ProductController.getAll() 메서드와 매핑된 엔드포인트를 호출한 결과이다. 화면 상단에 요청 URL을 입력하고, 바로 우측에서 HTTP Method를 선택하는 곳에서 “GET”을 선택하였다. 그 후 아래의 “Send” 파란색 버튼을 클릭하면 바로 아래에 응답 데이터가 뜨는 것을 볼 수 있다. 

다음은 GET Method로 호출하는 나머지 엔드포인트들의 호출 결과다. 앞서 작성한 코드 1-7의 Controller 내 메서드와 매핑된 URI과 비교해 보면 스프링 부트를 이용하여 어떻게 REST API를 구축해야할 지 알 수 있을 것이다. 

![사진 2-6. ProductController.getOne() 메서드와 매핑된 엔드포인트 호출 결과](/images/2024-11-17/spring-Restful%20Web/8.png)

사진 2-6. ProductController.getOne() 메서드와 매핑된 엔드포인트 호출 결과

![사진 2-7. ProductController.getByCategory() 메서드와 매핑된 엔드포인트 호출 결과](/images/2024-11-17/spring-Restful%20Web/9.png)

사진 2-7. ProductController.getByCategory() 메서드와 매핑된 엔드포인트 호출 결과

이번에는 HTTP Method가 POST인 엔드포인트를 호출해보자. 아까와는 조금 다르게 설정해야 한다. 

![사진 2-8. ProductController.insert() 메서드와 매핑된 URI 호출을 위한 설정 및 응답 데이터.](/images/2024-11-17/spring-Restful%20Web/10.png)

사진 2-8. ProductController.insert() 메서드와 매핑된 URI 호출을 위한 설정 및 응답 데이터.

위 사진 2-8과 같은 화면에서, 우측의 “Headers” 버튼을 클릭하면 URI 입력란 바로 아래에 요청 헤더에 정보를 담을 수 있는 입력란이 추가된다. POST의 경우 요청 데이터를 Header가 아닌 Body에 실어 전송하기에 이를 위해 헤더에 `Content-Type: application/json;charset=utf-8` 이란 정보를 실어야 한다. 해당 입력란에 이를 입력하면 된다. 이를 입력하지 않으면 에러가 발생하여 원하는 작업이 실행되지 않는다. 그 후, 요청 body에 전송하고자 하는 데이터를 JSON 형식으로 작성하면 된다. 여기서는 새로운 데이터를 생성하는 엔드포인트를 호출하려고 하고 있으므로 새로 삽입하고자 하는 데이터를 구성한다. 그리고 “Send” 버튼을 누르면 성공적으로 해당 데이터가 DB에 삽입된다. 

![사진 2-9. 사진 2-8에서의 실행 결과 DB에 새로운 데이터가 추가되었다.](/images/2024-11-17/spring-Restful%20Web/11.png)

사진 2-9. 사진 2-8에서의 실행 결과 DB에 새로운 데이터가 추가되었다.

사실 위에서 언급한대로 REST API의 End-Point, HTTP Method, HTTP 요청 헤더 및 body 설정만 하면 REST API를 통해 CRUD를 실행해볼 수 있게 된다. 앞서 만든 Put, Delete HTTP Method 방식의 URI도 같은 방식으로 테스트할 수 있다. 

## Swagger

REST API를 테스트해볼 수 있는 다른 도구로 Swagger가 존재하는데, 이를 사용하려면 build.gradle에 다음의 의존성을 추가해야 한다.

```java
// Swagger
// http://localhost:8080/swagger-ui/index.html
implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.7.0'
```

위 코드를 추가한 build.gradle의 전체 모습은 다음과 같다.

```java
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.4.1'
	id 'io.spring.dependency-management' version '1.1.7'
}

group = 'com.jerocaller'
version = '0.0.1-SNAPSHOT'

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(21)
	}
}

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	runtimeOnly 'org.mariadb.jdbc:mariadb-java-client'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
	
	// Swagger
	// http://localhost:8080/swagger-ui/index.html
  implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.7.0'
}

tasks.named('test') {
	useJUnitPlatform()
}

```

코드 2-1. build.gradle

그 후, 브라우저 주소 입력란에 [`http://localhost:8080/swagger-ui/index.html`](http://localhost:8080/swagger-ui/index.html) 를 입력하면 다음의 화면을 볼 수 있게 된다. 

![사진 3-1. Swagger 첫 화면](/images/2024-11-17/spring-Restful%20Web/12.png)

사진 3-1. Swagger 첫 화면

Swagger를 이용하면 위 사진에서 볼 수 있듯, 여태껏 작성한 모든 REST API의 URI들을 볼 수 있다. 각각의 URI의 HTTP Method 및 요청과 응답 예시까지 볼 수 있다는 장점이 있다. Swagger는 개발자가 작성한 모든 REST API의 정보들을 자동으로 보여주는 API 명세서 구성 툴이라고도 볼 수 있다. 위 사진에서 보이는 여러 URI들 중 하나를 클릭하여 실제로 테스트해볼 수도 있다. 

![사진 3-2. Swagger 화면에 보이는 URI 하나를 테스트하는 모습. ](/images/2024-11-17/spring-Restful%20Web/13.png)

사진 3-2. Swagger 화면에 보이는 URI 하나를 테스트하는 모습. 

![사진 3-3. 사진 3-2에서 테스트한 결과물을 볼 수 있다.](/images/2024-11-17/spring-Restful%20Web/14.png)

사진 3-3. 사진 3-2에서 테스트한 결과물을 볼 수 있다.

필자는 개인적으로 Swagger의 이러한 점들이 더 장점으로 다가오기에 Postmam보다는 Swagger를 자주 사용하는 편이다. 

지금까지 살펴본 예제 코드들은 다음의 github Repository에서 전체 소스코드로 볼 수 있다. 

[spring-study/RestfulApi at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/RestfulApi)

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, Ch. 4-2, Ch. 12,  (위키북스, 2024)

[4] 황희정, “짧고 굵게 배우는 JSP 웹 프로그래밍과 스프링 프레임워크”, (한빛아카데미, 2024)

[5] 내 예전 글

[[Python][Web] 웹 서비스와 자동화](https://jerocaller.github.io/python/web/web-service-and-automation/)

[6] 내 예전 글

[[Python][Web] 웹 클라이언트](https://jerocaller.github.io/python/web/web-client-and-python/)

[7] API란

[API란 무엇일까? API 쉽게 이해하기](https://brunch.co.kr/@operator/65)

[8] API란?

[API란? 비개발자가 알기 쉽게 설명해드립니다! - wishket](https://blog.wishket.com/api%EB%9E%80-%EC%89%BD%EA%B2%8C-%EC%84%A4%EB%AA%85-%EA%B7%B8%EB%A6%B0%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8/)

[9] API란

[봐도봐도 모르겠는 API 개념 설명 (Application Programming Interface)](https://dev-dain.tistory.com/50)

[10] API VS Endpoint

[API 와 Endpoint ? (둘 다 정확히 알고 있다면 안 봐도 되는 글)](https://blog.naver.com/ghdalswl77/222401162545)

[11] API VS Endpoint

[[Web] API 그리고 EndPoint](https://velog.io/@kho5420/Web-API-%EA%B7%B8%EB%A6%AC%EA%B3%A0-EndPoint)

[12] Endpoint

[API 엔드포인트란 무엇인가요? \| IBM](https://www.ibm.com/kr-ko/topics/api-endpoint)

[13] API 위키백과

[API](https://ko.wikipedia.org/wiki/API)

[14] end point

[What is an Endpoint?](https://stackoverflow.com/questions/2122604/what-is-an-endpoint)

[15] URI 설계 원칙

[REST API 디자인 가이드](https://bcho.tistory.com/914)

[16] URI 설계 원칙

[7 Rules for REST API URI Design](https://blog.restcase.com/7-rules-for-rest-api-uri-design/)

[17] Query String VS Path Variable

[Query parameter, Query String vs Path Variable](https://i-ten.tistory.com/243)

[18] Query String VS Path Variable

[[API] Path parameter VS Query Parameter (기록용)](https://velog.io/@juno97/API-Path-parameter-VS-Query-Parameter-%EA%B8%B0%EB%A1%9D%EC%9A%A9)

[19] HTTP Method - PUT VS PATCH

[HTTP 메소드 PUT , PATCH 차이 - 삽질중인 개발자](https://programmer93.tistory.com/39)