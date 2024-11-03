---
title: "[Spring Boot] Spring Boot와 Spring MVC"
category: "Spring"
tag: ["Spring", "Java", "Spring Boot", "MVC", "Spring MVC"]
---

# Spring boot 개요

Spring boot는 기존 Spring framework를 기반으로 하여 더 손쉽게 Spring을 사용하기 위해 등장한 framework이다. Spring boot는 다음의 특징들을 가지고 있다. 

- 환경 설정 및 의존성 관리 간소화 : 기존의 스프링에서는 개발자가 원하는 스프링 제공 라이브러리 또는 외부 라이브러리 추가를 위해 개발자가 일일이 의존성을 pom.xml 파일에 명시해야했으며, 각 모듈 간의 호환성도 고려해야 했다. 만약 여러 모듈 간의 버전이 맞지 않으면 버전 충돌로 인해 실행이 되지 않는 문제가 발생하기 때문이다. 스프링 부트에서는 이미 테스트된 여러 외부 라이브러리(Mybatis, Lombok 등)들 또는 스프링 제공 라이브러리(Spring Data JPA, Spring Web 등)들을 버전에 맞게 자동으로 설정해주는 편리성을 제공한다. (그렇다고 세상 모든 외부 라이브러리들을 버전에 맞게 자동으로 설정해주는 건 아니다. 만약 스프링 부트에서 제공하지 않는 외부 라이브러리에 대해서는 개발자가 따로 그 의존성을 명시해줘야 한다. )
- WAS 내장 : 스프링 부트에서는 톰캣이라는 WAS(Web Application Server)가 내장되어 있어 개발자가 별도로 WAS를 설치할 필요가 없으며, 따로 WAR 파일로 만들어 배포할 필요도 없게 된다. 스프링 부트를 이용하면 모든 의존성 라이브러리들이 JAR 파일로 구성되어 있어 별도의 서버 없이도 단독적으로 웹 애플리케이션을 실행시킬 수 있다.
    - 즉, 스프링 부트에서는 웹 배포 시 JAR 파일을 이용한다. 이로 인해 스프링 부트를 이용하여 SSR(Server Side Rendering) 방식으로 웹 앱 제작 시 JSP는 사용하지 않는다고 한다. JSP를 이용하여 웹 배포를 하려면 무조건 WAR 파일을 사용해야 하기 떄문이다. 대신 스프링 부트에서는 SSR 방식의 웹 앱을 위한 템플릿 엔진(Template Engine) 중 하나인 타임리프(Thymeleaf)를 대신 사용할 수 있다.
- 운영을 위한 도구 제공 : 웹 앱 배포 후에는 웹 사이트를 운영해야 한다. 스프링 부트에서는 모니터링 도구인 Spring boot Actuator를 제공해준다고 한다.

즉, 스프링 부트는 개발을 위한 설정들을 자동화하여 개발자가 오로지 애플리케이션 개발에만 집중할 수 있도록 도와주는 스프링 프레임워크 기반의 도구라 보면 되겠다. 

# Spring boot 사용하기

스프링 부트를 사용하는 방법에는 크게 두 가지가 있겠다. 

- [https://start.spring.io/](https://start.spring.io/)  사이트에서 프로젝트 폴더 생성하고 이를 이클립스와 같은 IDE에서 import 하여 사용하기
- 이클립스 IDE 내에서 스프링 부트 사용하기

개인적으로 두 번째 방법이 더 편하다고 생각하기에 여기서는 두 번째 방법을 살펴보겠다. 

1. 이클립스에서 상단 메뉴의 [Help] → [Eclipse Marketplace…]를 클릭한다. 
    
    ![20241027_18042232.png](/images/2024-10-24/Spring-boot-Spring-Mvc/1.png)
    

2. 그러면 다음의 창이 뜬다. 검색창에 “spring” 입력하면 다음과 같이 “Spring Tool 4 (aka Spring Tool Suite 4)” 라는 검색 결과가 뜨는데, 거기에 있는 “Install” 버튼을 눌러 설치를 시작한다. 
    
    ![20241027_18044931.png](/images/2024-10-24/Spring-boot-Spring-Mvc/2.png)
    

3. 다음의 창에서 “I accept …” 체크 박스에 체크한 후 [Finish] 클릭 

![20241027_18060288.png](/images/2024-10-24/Spring-boot-Spring-Mvc/3.png)

4. 그러면 설치 과정이 시작된다. 이클립스 우하단에 다음과 같이 설치 중이란 메시지가 뜬다. 
    
    ![20241027_18061336.png](/images/2024-10-24/Spring-boot-Spring-Mvc/4.png)
    

5. 중간에 다음의 창들이 뜰텐데, [Select All] 클릭 후 [Trust Selected] 클릭. 
    
    ![20241027_18083300.png](/images/2024-10-24/Spring-boot-Spring-Mvc/5.png)
    
    ![20241027_18104807.png](/images/2024-10-24/Spring-boot-Spring-Mvc/6.png)
    

6. 다음의 창이 뜨면 [Restart Now]를 클릭한다. 그러면 이클립스가 재가동될 것이다. 그러면 설치가 완료된다. 
    
    ![20241027_18110160.png](/images/2024-10-24/Spring-boot-Spring-Mvc/7.png)
    

이제 이클립스 내에서 스프링 부트 프로젝트를 생성하는 방법은 다음과 같다. 

1. [File] → [New] → [Other] 클릭
    
    ![20241027_18113614.png](/images/2024-10-24/Spring-boot-Spring-Mvc/8.png)
    

2. 다음의 창 내 목록에서 [Spring Boot] → [Spring Starter Project]를 클릭한다. 그러면 스프링 부트를 이용한 프로젝트 폴더를 생성할 수 있다. 
    
    ![20241027_18115865.png](/images/2024-10-24/Spring-boot-Spring-Mvc/9.png)
    

3. 다음과 같은 창이 뜨는데, 여기서 원하는 설정을 하면 된다. 필자는 Maven 대신 Gradle을 사용해보고자 하기에 [Gradle - Groovy]를 선택하였다. 그 외에도 Java Version, Group, Name 등을 설정하였다. 다 되었으면 맨 아래에 [Next] 버튼을 클릭한다. 
    
    ![1.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/10.png)
    

4. 그 다음에는 아래와 같이 여러 라이브러리(의존성)들을 추가할 수 있다. 웹 앱을 만들고자 하는 경우 아래 사진처럼 Spring Web을 추가한다. Spring Boot DevTools는 개발 시 유용한 기능들을 제공해주기에 넣는 것이 좋겠다. 여기서 Mybatis, MariaDB Driver, Spring Data JPA 등 여러 라이브러리들을 추가할 수 있다. 다 되었으면 [Finish]를 클릭한다. 그 후 이클립스에서 스프링 부트 프로젝트 폴더가 생성된다. 시간이 조금 걸릴 수 있음에 유의한다. 
    
    ![2.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/11.png)
    

다음은 스프링 부트로 생성한 프로젝트 폴더의 기본 구조이다. 

![1.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/12.png)

- src/main/java : 자바 파일을 생성, 관리하는 곳이다.
    - (프로젝트명)Application.java : 앱 실행을 위한 중요한 자바 파일이다.
- src/main/resouces : HTML, CSS, Javascript 또는 이미지, 영상 파일 등의 정적 리소스들을 넣어 관리하는 곳이다.
    - static : HTML, CSS, Javascript 또는 이미지 및 사운드, 영상 파일 등을 저장하는 공간.
    - template : SSR 방식의 웹 구현 시 사용자의 요청에 따른 응답 페이지(MVC에서의 View)를 서버에서 생성할 때 사용되는 것이 JSP, Thymeleaf와 같은 템플릿 엔진이다. 이러한 템플릿 파일들을 저장하는 곳이다. Thymeleaf 사용 시 파일 확장자는 HTML이며, 이러한 템플릿 파일을 이곳에 저장하여 관리하면 된다. 참고로 template 폴더는 서버 외부에서 접근할 수 없는 폴더이다. 즉, 기존의 WEB-INF 폴더와 동일하다고 보면 되곘다.
    - application.properties : 프로젝트 환경 설정을 이곳에 한다. 서버 포트 번호, DB 연동 정보 등의 여러 설정 정보들을 여기에 담을 수 있다. 애플리케이션 실행 시 스프링에서 해당 파일 내 정보를 읽어와 설정을 해준다.
- src/test/java : src/main/java 내 자바 파일들을 테스트하기 위한 공간.
- Project and External Dependencies : 해당 폴더 내에서 현재 해당 프로젝트 폴더에 설정된 스프링 관련 라이브러리 또는 외부 라이브러리들을 살펴볼 수 있다.
- build.gradle : Maven에서의 pom.xml과 같은 역할을 하는 환경 설정 파일이다. 해당 파일 내에서 필요한 라이브러리 및 플러그인 의존성을 여기에 명시하면 이를 자동으로 등록해준다(Project and External Dependencies 폴더 내에서 추가된 실제 라이브러리 JAR 파일 확인 가능).

static 폴더 내에 index.html을 만들었다. 

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>First Spring Boot</title>
</head>
<body>
	<h1>만나서 반갑습니다.</h1>
</body>
</html>
```

index.html

해당 프로젝트 폴더 우클릭 → [Run As] → [Spring Boot App]을 클릭하면 애플리케이션이 실행된다. 

![20241027_20493964.png](/images/2024-10-24/Spring-boot-Spring-Mvc/13.png)

실행하면 콘솔창에는 다음과 같은 메시지들이 뜬다. 

![2.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/14.png)

필자는 포트 번호에 대해 아무런 설정도 하지 않았다. 이 경우 `localhost:8080/index.html` 을 브라우저 URL 입력창에 입력하면 다음과 같은 페이지가 보일 것이다. 

![1.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/15.png)

스프링 부트 기반 웹 사이트가 로컬 서버에서 잘 동작하고 있다는 뜻이다. 

# 스프링 부트 기반 앱 개발 시 주의사항들

## 포트 번호 관련 에러

필자는 처음 스프링 부트를 접했을 때 그 이전에는 서블릿 기반의 웹 제작을 경험했기에 코드 수정 후 항상 이클립스에서 Ctrl + F11을 눌러 재실행하곤 했다. 이 버릇 때문에 스프링 부트에서도 코드 수정 후 모르고 Ctrl + F11을 누르곤 했다. 하지만 스프링 부트에서는 이럴 필요가 없다. 스프링 부트 기반 프로젝트 폴더 생성을 위한 초반 설정에서 추가한 [Spring Boot DevTools] 라는 도구가 이미 자동으로 코드 변경을 감지하여 서버 재구동을 해주기 때문이다. 오히려 개발자가 이미 구동중인 스프링 부트에서 Ctrl + F11을 눌러 재시작하려고 하면 다음과 같은 에러가 발생한다. 

![1.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/16.png)

위 사진의 콘솔창 내 에러 메시지를 살펴보면 실행하려는 특정 포트가 이미 사용 중이라 웹 서버를 구동하지 못했다고 나와있다. 이 말인 즉, 개발자가 Ctrl + F11을 눌러 서버를 재실행하려고 할 때, 사실은 “재실행”되는 것이 아니다. 재실행이라는 것은 기존 서버의 동작을 중단시키고 다시 키는 것인데, 스프링 부트에서는 기존 서버의 동작이 자동으로 중단되지 않는다. 따라서 위와 같은 에러가 발생하여 웹 서버를 다시 구동시킬 수 없는 것이다. 

위의 문제점을 해결할 수 있는 방법에는 크게 두 가지가 있다. 

첫 번째는 CMD 명령창을 통해 사용 중인 상태에 놓인 포트 번호를 다시 원래 상태로 되돌려 놓는 방법이다. 명령 프롬프트 창에서 `netstat -ano | findstr 포트번호]` 를 입력한다. 

![2.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/17.png)

그냥 `netstat -ano` 만 입력하면 모든 포트 번호 정보가 나오기에 찾고자 하는 포트 번호 정보를 찾기가 어려워진다. 따라서 이미 이클립스 콘솔창에 어느 포트 번호에서 에러가 나는지를 알려주므로 이를 이용하여 특정 포트 번호 정보만 검색하는 것이다. 

참고로 맨 오른쪽 숫자가 PID라는 정보이다. 이것이 우리가 필요한 정보이다. 해당 PID를 이용하여 `taskkill /f /pid (pid번호)` 명령어를 입력하면 해당 포트 번호의 사용이 중단된다. 

![3.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/18.png)

이제 이클립스에서 해당 프로젝트 폴더를 다시 실행해보면 잘 실행된다. 

두 번째 방법은 첫 번째 방법보다 훨씬 더 간단하다. 이클립스의 [Console] 창에서 마우스 우클릭 → [Terminate/Disconnect All]을 눌러 기존 서버의 구동을 중단시킨다. 

![20241027_21152557.png](/images/2024-10-24/Spring-boot-Spring-Mvc/19.png)

그러면 다음과 같은 메시지가 콘솔창에 뜰 것이다. 

![4.PNG](/images/2024-10-24/Spring-boot-Spring-Mvc/20.png)

앱 실행 중단 요청이 전달되었다는 메시지와 함께 상단 아이콘의 색이 빨간색이 아닌 회색이 된 것을 볼 수 있다. 기존 서버의 구동이 중단되었다는 것이다. 이 상태에서 다시 한번 Ctrl + F11을 눌러 실행하면 정상적으로 실행된다. 

## application.properties 주의 사항

application.properties 파일에서는 환경 설정을 위해 key:value 형태로 설정 정보들을 기입하는 방식을 취한다. 즉 다음의 형태를 띤다. 

```html
spring.application.name=FirstSpringBoot

# 포트 번호 설정
server.port=80

```

이 때 주의할 점은, value 뒤에 그 어떤 공백도 있어선 안된다는 것이다. 공백 자체도 value로 인식할 수 있기 때문이다. 또한 `key = value` 와 같이 등호 양 옆에 공백을 주지 않는 것이 좋다. 즉 `key=value` 와 같이 사용하는 것이 혹시 모를 에러 방지에 좋다. 

# Spring Web MVC

앞서 이클립스에서 스프링 부트 기반 프로젝트 폴더 생성을 위한 설정 시 디펜던시 설정에서 [Spring Web]을 선택했었다. 이 후 이클립스에서 해당 폴더 내 [Project and External Dependencies] 라는 폴더 내부를 보면 “spring-boot-starter-web-x.x.x.jar” 파일이 등록되어 있는 것을 볼 수 있다. 해당 라이브러리는 톰캣 기반의 스프링 MVC 구조 기반의 웹앱을 만들 수 있게 한다. 

스프링 웹 MVC의 구조와 동작 원리는 다음의 그림과 같이 알려져 있다. 

![그림 1-1. Spring MVC 구조](/images/2024-10-24/Spring-boot-Spring-Mvc/spring_mvc.drawio.png)

그림 1-1. Spring MVC 구조

1. (1) 클라이언트의 요청은 DispatcherServlet이라는 컨트롤러로 전달된다. 이 때, DispatcherServlet은 Front Controller 패턴으로 구현되어 있어 웹 사이트 전반에서 들어오는 모든 종류의 요청을 다 받는다. 
2. (2) DispatcherServlet은 받은 요청을 어떤 Controller가 처리할 수 있을지에 대한 판단을 Handler Mapping이라는 인터페이스에 위임시킨다. (3) 그러면 HandlerMapping에서 특정 요청의 URI와 매핑된  Controller를 탐색하여 해당 Controller 정보를 반환한다. 
3. (4) HandlerMapping으로부터 특정 요청을 처리할 수 있는 특정 Controller 정보를 얻은 DispatcherServlet은 이를 Handler Adapter에게 전달한다. 
4. (5) Handler Adapter는 DispatcherServlet으로부터 전달받은 특정 Controller 정보를 토대로 해당 Controller를 실행시킨다. 
5. 해당 컨트롤러는 특정 요청을 처리할 수 있는 Service 클래스 및 Dao 클래스를 선택하여 데이터 처리를 요청한다. (6), (7) 그 후 Model로부터 온 데이터를 다시 DispatcherServlet에게 전달한다. 
6. (8) model 결과를 얻은 DispatcherServlet은 해당 데이터를 토대로 어떤 View에서 이를 출력할지에 대한 판단을 View Resolver에게 위임한다. 
7. View Resolver는 어떤 View가 응답 페이지로 클라이언트에게 보여줄지 결정하여 해당 View 객체를 다시 DispatcherSerlvet에 전달한다. 
8. View 객체를 받은 DispatcherServlet은 응답 페이지 역할의 View를 클라이언트에 전송함으로써, 사용자 요청 처리에 대한 응답 페이지를 클라이언트에게 보여준다. 

위 과정은 SSR(Server Side Rendering) 방식으로 서버에서 응답 페이지를 생성하는 경우에 대한 그림이었다. 만약 응답 데이터만 서버에서 생성하여 클라이언트에 넘기고, 이를 프론트엔드 단에서 받아 화면을 구현하는 CSR(Cliend Side Rendering) 방식이라면 View Resolver와 View는 존재하지 않고 대신, HTTP Request, Response Body의 값을 변환시켜주는 역할의 MessageConverter가 존재한다. 이 개체가 JSON 형태의 응답 데이터를 DispatcherServlet을 통해 클라이언트에게 전송한다. 

한 편 위 그림에서 볼 수 있듯, Front Controller 및 어떤 Controller 또는 어떤 View를 선택할지에 대한 구현은 이미 Spring MVC에서 구현하고 있으므로, 개발자는 Controller, Model(Service, DAO) 및 View (SSR 방식인 경우) 만 구현하면 된다. 이조차도 Spring에서는 POJO(Plain Old Java Object)을 지향하기에 개발자는 외부 라이브러리를 직접 가져와 사용하지 않아도 구현할 수 있다는 장점을 가진다. 

## Spring Web MVC 맛보기

앞서 설치한 스프링 부트를 기반으로 하여 Spring Web MVC에 대해 살펴보겠다. 이를 위해 간단한 예제를 만들어보았다. 

![스프링 부트 프로젝트 의존성 설정](/images/2024-10-24/Spring-boot-Spring-Mvc/21.png)

스프링 부트 프로젝트 의존성 설정

![프로젝트 폴더 내 구조](/images/2024-10-24/Spring-boot-Spring-Mvc/22.png)

프로젝트 폴더 내 구조

```html
spring.application.name=FirstSpringMVC

spring.thymeleaf.cache=false

# DataSource Setting
spring.datasource.driver-class-name=org.mariadb.jdbc.Driver
spring.datasource.url=jdbc:mariadb://localhost:3308/mvc
spring.datasource.username=root
spring.datasource.password=1111
```

application.properties

Model 계층의 코드는 다음과 같다.

```java
package com.jerocaller.model;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class UserDto {
	private int user_no;
	private String id, password;
}

```

UserDto.java

```java
package com.jerocaller.model;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class ProductDto {
	private int id, price;
	private String name, category;
}

```

ProductDto.java

```java
package com.jerocaller.model;

import java.util.List;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.SelectProvider;

@Mapper
public interface SqlMappterInterface {
	@SelectProvider(
			type = QueryBuilder.class, 
			method = "getQuerySelectOneUser")
	UserDto getOneUser(
			@Param("id") String id, 
			@Param("password") String password
	);
	
	@SelectProvider(
			type = QueryBuilder.class, 
			method = "getQuerySelectAllProducts")
	List<ProductDto> getAllProducts();
}

```

SqlMapperInterface.java

```java
package com.jerocaller.model;

import org.apache.ibatis.jdbc.SQL;

public class QueryBuilder {
	private final String USER_TABLE = "users";
	private final String PRODUCT_TABLE = "products";
	
	public String getQuerySelectOneUser() {
		return new SQL()
			.SELECT("user_no, id, password")
			.FROM(USER_TABLE)
			.WHERE(" id = #{ id} AND password = #{password}")
			.toString();
	}
	
	public String getQuerySelectAllProducts() {
		return new SQL()
			.SELECT("id, name, price, category")
			.FROM(PRODUCT_TABLE)
			.toString();
	}
}

```

QueryBuilder.java

```java
package com.jerocaller.model;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Repository;

@Repository
public class WebDao {
	
	@Autowired
	private SqlMappterInterface mappterInterface;
	
	public UserDto getOneUser(String id, String password) {
		return mappterInterface.getOneUser(id, password);
	}
	
	public List<ProductDto> getAllProducts() {
		return mappterInterface.getAllProducts();
	}
}

```

WebDao.java

View 계층의 HTML 파일들은 다음과 같은 내용으로 구성되어 있다. thymeleaf를 사용하였다. thymeleaf에 대해선 추후에 별도의 글에서 정리하겠다. 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>환영!</title>
</head>
<body>
	<p>로그인하기</p>
	<form th:action="@{/login}" method="post">
		<p>아이디 : <input type="text" name="id"/> </p>
		<p>비밀번호 : <input type="text" name="password"/> </p>
		<input type="submit" value="로그인" />
	</form>
</body>
</html>
```

index.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h3>로그인 실패</h3>
	<a th:href="@{/}">메인으로 돌아가기</a>
</body>
</html>
```

loginFailed.html

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<p th:text="|${user.id}님 환영합니다|"></p>
	
	<p>상품 목록</p>
	<table border="1">
		<tr>
			<th>코드</th>
			<th>상품명</th>
			<th>가격</th>
			<th>카테고리</th>
		</tr>
		<th:block th:if="${products.size > 0}">
			<tr th:each="prod:${products}">
				<td th:text="${prod.id}"></td>
				<td th:text="${prod.name}"></td>
				<td th:text="${prod.price}"></td>
				<td th:text="${prod.category}"></td>
			</tr>
		</th:block>
		<tr>
			<td colspan="4" th:text="|총 물품의 수: ${products.size}개|"></td>
		</tr>
	</table>
	<a th:href="@{/}">메인으로 돌아가기</a>
</body>
</html>
```

products.html

그리고 Controller 계층의 클래스는 다음과 같이 단 하나만 만들었다. 

```java
package com.jerocaller.controller;

import java.util.List;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

import com.jerocaller.model.ProductDto;
import com.jerocaller.model.UserDto;
import com.jerocaller.model.WebDao;

@Controller
public class WebController {
	
	@Autowired
	private WebDao dao;
	
	@RequestMapping(value = "/", method = RequestMethod.GET)
	/**
	 * RequestMapping(value = "이동할 페이지의 URI", method = 처리할 HTTP 메서드)
	 * 
	 * Controller 클래스에 @Controller 어노테이션을 붙이면 SSR 방식으로 응답 "페이지"를 
	 * 사용자에게 보여준다. 이 때 컨트롤러 내 메서드에서는 String 반환값을 가지며, 
	 * 반환값은 이동할 페이지의 URI을 입력한다. 
	 * 
	 * @return
	 */
	public String goToIndex() {
		// 포워딩 방식으로 index.html로 이동.
		// "forward:/index"의 축약형
		// 만약 redirect 방식으로 이동하고자 한다면 
		// "redirect:/index"와 같이 명시해야 한다. 
		return "index";
	}
	
	@PostMapping("/login")
	public String login(
			@RequestParam("id") String id, 
			@RequestParam("password") String password,
			Model model
		) 
	{
		UserDto selectedUser = dao.getOneUser(id, password);
		if (selectedUser == null) {
			return "loginFailed";
		}
		
		// 유저 정보를 싣고 View로 전달. 
		// Servlet에서의 request.setAttribute()와 동일.
		model.addAttribute("user", selectedUser);
		
		List<ProductDto> products = dao.getAllProducts();
		model.addAttribute("products", products);
		
		return "products";
	}
}

```

WebController.java

여기서 중점적으로 보고자 하는 것은 WebController.java 파일이다. 서블릿에서는 HttpServlet 등 EE 스펙에서 제공하는 외부 라이브러리를 사용해야 했는데, 위 코드에서는 외부 라이브러리(spring 제외)를 전혀 사용하고 있지 않음에도 컨트롤러로서의 역할을 하고 있음을 알 수 있다. 

우선 위 코드에서 사용된 어노테이션에 대해서만 간단히 정리하자면 다음과 같다. 

- `@Controller` : 해당 자바 클래스가 컨트롤러임을 선언. SSR 방식으로 View가 존재할 때 사용된다. 만약 CSR 방식을 위해 JSON 응답 데이터만 클라이언트에 전송하고자 한다면 대신 `@RestController` 를 사용하거나 아니면 `@Controller`와 더불어 `@ResponseBody` 어노테이션을 같이 사용하면 된다. 참고로 `@ResponseBody` 어노테이션은 클래스 수준에서 사용해도 되고, 메서드 수준에서 사용할 수도 있다.
- `@RequestMapping()` : 어떤 URI 요청, 어떤 HTTP Method를 받을지 결정. 메서드 수준에서 사용한다. 사실 `@GetMapping`, `@PostMapping` 등의 어노테이션이 이 어노테이션을 대체하여 주로 사용된다고 한다.
    - value : 처리할 요청 URI
    - method : 처리할 HTTP 요청 메서드 방식. RequestMethod의 GET, POST와 같은 상수를 이용한다.
- `@PostMapping()` : Post 방식의 특정 URI 요청을 처리할 때 사용. 인자로는 요청 URI를 입력한다. 이 외에도 요청 Method 방식에 따라 `@GetMapping()` , `@DeleteMapping()` 등의 어노테이션들도 존재한다.
- `@RequestParam()` : 클라이언트로부터 전달받은 쿼리스트링(query string) 방식의 요청 정보를 전달받을 때 사용하는 어노테이션으로, 매개변수에 적용한다. 요청 데이터의 key 값이 컨트롤러 내 메서드의 매개변수명과 일치한다면 굳이 `@RequestParam()` 어노테이션에 key값을 명시하지 않아도 된다. 다만 쿼리스트링에 명시된 key값과 매개변수명을 다르게 해야한다면 해당 어노테이션에 요청으로 넘어오는 데이터의 key 이름과 동일하게 작성해줘야 한다.

Spring MVC 관련 어노테이션에는 이것 말고도 다양하게 많은데, 이는 별도의 페이지에서 따로 다뤄보겠다. 

어찌됐건, 위 예제를 통해 Spring MVC를 이용하여 웹을 구현하는 법에 대해 간단히 살펴보았다. 위 예제에서 알 수 있듯, 개발자는 DB 연동과 데이터를 통한 비즈니스 로직, 컨트롤러와 view에만 신경쓰면 되고 그 외에는 Spring boot가 알아서 해주기에 좀 더 편하게 애플리케이션을 개발할 수 있다. 

> 위 글에서 소개한 소스 코드는 다음의 사이트에서 모두 살펴볼 수 있다.
> 
> 
> [spring-study/FirstSpringMVC at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/FirstSpringMVC)
> 

> 위 예제 코드에서는 MyBatis를 이용하였는데, SQL Mapper 역할의 인터페이스에 `@Mapper` 어노테이션을 적용하였다. 그러면 DB 연동 정보를 application.properties에 등록만 하면 별도의 XML 파일 없이도 MyBatis를 이용하여 DB 연동 작업을 할 수 있다.
> 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[4] 스프링 부트 내부 구조

[[SpringBoot] 개념, 구조, 동작 방식](https://nyyang.tistory.com/118)

[5] 이클립스 스프링 부트에 의존성 추가 방법

[이클립스에 의존성 추가](https://velog.io/@eun0eun0/%EC%9D%B4%ED%81%B4%EB%A6%BD%EC%8A%A4%EC%97%90-%EC%9D%98%EC%A1%B4%EC%84%B1-%EC%B6%94%EA%B0%80)