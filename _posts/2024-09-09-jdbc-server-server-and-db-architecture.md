---
title: "[JDBC][Server] Server and DB architecture"
category: "Servlet & JSP"
tag: ["JDBC", "Server", "DB", "Architecture", "JNDI", "DBCP", "Directory Service", "WAS", "Web Server"]
---

# Web server VS WAS(Web Application Server)

Web server는 클라이언트로부터 HTTP 요청을 받으면 이에 대한 응답으로 HTML, CSS, image 등의 정적(static) 컨텐츠를 제공하는 용도의 프로그램이다. 

WAS(Web Application Server)는 사용자 요청에 따라 동적 컨텐츠를 제공하는 서버로, 사용자의 동적 컨텐츠 요청이 먼저 Web server에 도착하면 정적 컨텐츠만 응답할 수 있는 특성상 Web server는 이 요청을 WAS에 전달한다. 그러면 WAS는 동적 컨텐츠 응답을 위해 DB 작업 등의 여러 로직들을 처리한 후, 그 응답을 다시 Web server에 건네주고, web server에서 동적 컨텐츠를 다시 클라이언트에 전송하는 방식이다. 이렇게 WAS는 사실상 웹 서버와 클라이언트, 그리고 필요에 따라 DB도 연동하여 그 사이에서 요청을 처리하고 그 응답을 간접적으로 전달하는 방식이므로 미들웨어(middle ware)라고도 불린다. 이러한 WAS에는 대표적으로 지난 페이지에서 쭉 다뤄왔던 tomcat이 그 예이다. 

현재의 Tomcat은 정적 컨텐츠를 다루는 웹 서버와 동적 컨텐츠를 다루는 WAS가 모두 포함되어 있다. 그럼에도 WAS와 웹 서버는 같이 사용하는 것이 좋다. 만약 WAS에게 정적 컨텐츠 응답까지 담당하게 하면 웹 서버로 분리하는 방식에 비해 부담이 가기에 서버 부하가 발생할 수 있기 때문이다. 

![그림 1. 동적 요청 처리 과정을 그림으로 표현해봤다.](/images/2024-09-09/jdbc-server-server-and-db-architecture/1.png)

그림 1. 동적 요청 처리 과정을 그림으로 표현해봤다.

여태까지 서블릿, jsp를 통해 사용자의 요청을 동적으로 처리했던 것도 모두 WAS 내부에서 벌어진 일이다. 

# 기존 JDBC 방식의 문제점

[[JDBC] DB 구축과 JDBC](/servlet%20&%20jsp/jdbc-db-구축과-jdbc/) 페이지에서 JDBC를 이용하여 자바 프로그램 내에서 DB를 연동하여 데이터를 다뤄보았다. DAO(Data Access Object)와 DTO(Data transfer Object) 디자인 패턴을 이용하여 너무 복잡하고 긴 DB 작업 코드를 최소화시켰다. 그럼에도 아직 실제 서버에 배포하여 운영하기에는 부족한 점들이 많다. 

다음은 해당 페이지에서 구현한 JDBC 사용 방식의 문제점이다. 

- DB 접속에 필요한 jdbc url, DB 계정, DB 비밀번호 및 드라이버 객체 등록 코드가 자바 소스 코드, 즉 자바 프로그램에 내장되어 있다. 이게 문제인 이유는, 로직을 구현하는 개발자와 서버를 운영하는 운영자가 서로 다를 수 있는데, 자바 소스 코드를 모르는 운영자 입장에서는 DBMS 프로그램 변경이나 DB 계정 변경 등의 작업을 사실상 할 수가 없게 된다. 개발용 서버, 운영용 서버 등 상황과 목적에 따라 매번 서버를 바꿔야할 수도 있기에 DB 관련 환경 설정을 자바 소스 코드를 모르는 실제 서버 운영자가 할 수 있도록 해줘야 한다. ⇒ 해결책 : JNDI
- 기존의 JDBC 방식은 SQL문을 한 번 사용할 때마다 DB를 연동하고 해제하는 순서를 밟는다. SQL문 하나를 실행하고 DB 연결을 해제하는 것은 꽤 비효율적이다. 이 방식이라면 사용자가 많을 경우 매번 DB 연동 및 연동 해제를 반복하기에 DB에 부하를 준다. ⇒ 해결책 : DBCP

이러한 기존 JDBC 방식의 문제점을 해결해주는 방법으로 각각 JNDI과 DBCP가 있다. 

# DBCP (DataBase Connection Pool)

스레드 객체가 너무 많아지거나 한 번의 작업만 수행하고 삭제되는 방식은 메모리 자원에 부담이 된다. 따라서 아예 한정된 개수의 스레드 객체들을 생성하고 이를 재활용하는 방식인 thread pool이 존재한다. 그러면 스레드 객체를 필요 이상으로 생성하는 것을 방지하고, 기존의 스레드를 계속 재활용하기에 메모리 자원을 비교적 덜 소모한다. 

DBCP도 같은 방식을 사용한다. SQL문 하나를 실행할 때마다 DB를 연동하고 실행 완료되면 DB를 끄는 방식은 거시적으로 보면 마치 불 스위치를 계속 껐다 켰다하는 것처럼 DB 작업을 위해 수시로 DB를 연동하고 연결을 해제하기에 매우 비효율적이다. 

만약 사용자의 요청이 있을 때마다 DB 작업을 수행해야하는 방식이라면 서버 접속자가 많아질수록 서버에 과부하가 걸릴 수밖에 없다. DB 사용을 위해 Connection 객체를 수시로 생성 또는 삭제하기에 서버에 큰 부담이다. 

따라서 사용자의 요청에 상관없이, 또는 DB 작업 빈도수에 상관없이 아예 DB 연결을 위한 Connection 객체를 일정 개수 만들어 미리 DB와 연결해놓고 이를 하나의 pool에 모아놓는다. 이러한 Connection들을 하나로 모아 관리하는 것을 DBCP(DataBase Connection Pool)이라 한다. DBCP는 DB 작업 요청이 들어오면 DB와 연결된 Connection 객체를 빌려주고, 작업이 완료되면 다시 Connection 객체를 pool에 반환한다. 이 때, 반환된 Connection 객체를 소멸시켜 DB 연동을 해제하지 않고, 연결되어 있는 상태 그대로 유지시켜 다음 DB 작업 요청 처리를 위해 대기시켜놓는다. 

![그림 2. 기존의 JDBC 방식과 DBCP 방식을 그림으로 그려보았다.](/images/2024-09-09/jdbc-server-server-and-db-architecture/2.png)

그림 2. 기존의 JDBC 방식과 DBCP 방식을 그림으로 그려보았다.

이러한 DBCP 방식은 대다수의 사용자들이 동시에 서버에 접속하거나 접속 빈도수가 빈번할 경우에 사용되는 방식이다. 물론 접속자 수가 적거나 아예 테스트용으로 서버를 사용하는 경우에는 기존의 JDBC 방식을 사용해도 문제가 없을 것이다. 이러한 이유로 실무에서 실제 서비스를 할 때에는 이 DBCP 방식이 더 많이 사용될 것이다. 

DBCP를 사용하기 위해서 보통 직접 구현하거나 외부 라이브러리를 사용하기도 한다. 이 DBCP를 구현한 라이브러리는 Tomcat에서도 제공해준다. Tomcat에서 제공하는 DBCP 라이브러리를 가져와 사용하는 자세한 방법은 아래 JNDI이란 개념도 다루고 나서 살펴보겠다. 

# JNDI (Java Naming and Directory Interface)

## 디렉토리 서비스 (Directory Service)

디렉토리 서비스는 네트워크 상에 분산되어 있는 여러 자원들을 하나로 관리하고, 디렉토리 내에 저장된 여러 네트워크 자원들을 사용자 또는 사용 프로그램이 요구할 때 해당 자원에 대한 정보 검색, 변경, 추가, 삭제 등의 서비스를 제공하는 기능 단위이다. 

디렉토리는 네트워크 자원에 해당하는 여러 데이터 집합을 의미한다. 

IT 회사와 같은 곳에서는 여러 대의 컴퓨터들이 존재할 것이며, 각종 자원들도 분산되어 있어 이를 관리할 프로그램이 필요할 것이다. 또한 그 중에는 중요한 자원들도 있어 사용자 권한, 인증 제도도 도입해야 할 것이다. 디렉토리 서비스는 이렇게 분산된 네트워크 상의 자원들을 하나로 관리하여 특정 자원에 대한 접근성도 빨라지고, 더 복잡해지지 않도록 자원을 체계적으로 관리할 수 있으며, 사용자 권한, 인증 등의 기능도 있어 보안성도 제공한다. 

## JNDI

JNDI는 Java Naming and directory interface의 약자로, 디렉토리 서비스에서 제공하는 데이터와 객체를 검색하고 참조(lookup)하는 용도의 자바 API이다. 보통 자바 앱과 외부 디렉토리 서비스(DB server 등)를 연결할 때 쓰인다고 한다. 즉, 자바 앱에서 DBCP 방식으로 DB와 연결하기 위해 JNDI을 활용할 수 있다는 뜻이기도 하다. 앞선 그림들을 보면 알겠지만, 자바 앱과 DB, web server와 WAS 등 하나의 웹 앱을 서비스하기 위한 여러 개체들이 따로 분산되어 있다. 그렇기에 디렉토리 서비스를 이용하면 자바 앱 입장에서 외부에 존재하는 DB에 접근할 수 있는 것이다. 

## JNDI를 이용하여 Tomcat에서 제공하는 DBCP 연결해보기

Tomcat에서 제공하는 DBCP를 연결해보자. 

[Apache Tomcat 9 (9.0.93) - JNDI Datasource How-To](https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html)

위와 같이 톰캣 공식 사이트에서 JNDI를 이용하여 어떻게 DBCP를 연결할 수 있을지에 대한 설명이 있다. 

해당 톰캣 문서에 따르면, 크게 두 가지의 작업을 해야 한다. 

1) server.xml 내부의 `<Context>` 의 자식 태그로 다음의 태그를 삽입해야 한다.

```xml
<Resource
  name="jdbc/TestDB"
  auth="Container"
  type="javax.sql.DataSource"
  maxTotal="100"
  maxIdle="30"
  maxWaitMillis="10000"
  username="javauser"
  password="javadude"
  driverClassName="com.mysql.jdbc.Driver"
  url="jdbc:mysql://localhost:3306/javatest"
/>
```

- Resource 속성들
    - name : JNDI 접근을 위함. 속성값의 맨 앞에는 “jdbc”를 꼭 쓰는 것이 관례. 그 뒤에는 연결하고자 하는 db명을 작성한다.
    - auth : 인증 처리. 여기서는 톰캣의 container에게 처리하도록 함.
    - type : 톰캣에서 만든 jdbc 클래스 네임. 이 후 자바 프로그램 내에서 DB 연동 시 DataSource라는 클래스를 사용하게 될 것이다.
    - maxTotal : 동시 접속자 제한. 여기서는 동시 접속자 수 최대 100명으로 제한되어 있음.
    - maxIdle : 접속자가 없더라도 지정된 수만큼 생성. 여기서는 30개의 동접자 자리 준비.
    - username과 password는 실제 사용될 DB 유저네임과 패스워드로 바꾼다.
    - driverClassName도 현재 사용하는 jdbc 드라이버 클래스명으로 교체. 여기서는 mariadb를 사용할 것이기에 `org.mariadb.jdbc.Driver` 으로 바꿀 예정.
    - url도 현재 접속하고자 하는 DB의 url을 입력.

2) WEB-INF 폴더 내의 web.xml(없으면 만든다) 피일 내부에 다음과 같이 작성.

```xml
<!-- <web-app> 태그 내부 -->
<description>MySQL Test App</description>
<resource-ref>
  <description>DB Connection</description>
  <res-ref-name>jdbc/TestDB</res-ref-name>
  <res-type>javax.sql.DataSource</res-type>
  <res-auth>Container</res-auth>
</resource-ref>
```

- res-ref-name 태그 내에 실제 접속할 db 명을 입력한다.

위 과정을 필자는 다음과 같이 진행하였다. 먼저 server.xml이다.

```xml
<!-- 생략 -->
<Context docBase="Festival" path="/" reloadable="true" source="org.eclipse.jst.jee.server:Festival">
  <Resource
  	name="jdbc/test_festival"
  	auth="Container"
  	type="javax.sql.DataSource"
  	maxTotal="100"
  	maxIdle="30"
  	maxWaitMillis="10000"
  	username="root"
  	password="1111"
  	driverClassName="org.mariadb.jdbc.Driver"
  	url="jdbc:mariadb://localhost:3308/test_festival"
	/>
</Context>
 <!-- 생략 -->
```

server.xml

(그저 테스트할 용도라 username과 password는 대충 지었다…)

그 다음, WEB-INF 폴더 내에 web.xml 파일을 생성하여 다음과 같이 작성하였다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>

<web-app xmlns="http://java.sun.com/xml/ns/j2ee"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://java.sun.com/xml/ns/j2ee
http://java.sun.com/xml/ns/j2ee/web-app_2_4.xsd"
    version="2.4">
  <description>Maria DB connection</description>
  <resource-ref>
    <description>DB Connection</description>
    <res-ref-name>jdbc/test_festival</res-ref-name>
    <res-type>javax.sql.DataSource</res-type>
    <res-auth>Container</res-auth>
  </resource-ref>
</web-app>
```

web.xml

3) MariaDB의 jdbc Driver를 설치를 아직 안하였다. 다음의 사이트에서 mariaDB의 jdbc driver를 다운로드 받는다.
    
  [Maven Repository: org.mariadb.jdbc » mariadb-java-client » 3.4.1](https://mvnrepository.com/artifact/org.mariadb.jdbc/mariadb-java-client/3.4.1)
    
  ![1.PNG](/images/2024-09-09/jdbc-server-server-and-db-architecture/3.png)
    
  다운로드 받은 jar파일을 프로젝트 폴더 내 WEB-INF/lib에 위치시킨 후, 해당 JAR 파일을 해당 프로젝트에 등록시킨다. 
    

이제 DB 연동 과정은 끝났다. 자바 코드에서 다음의 코드를 이용하여 바로 DB에 접속하여 사용할 수 있다. 

```java
import java.sql.Connection;
import java.sql.SQLException;
// 생략

// JNDI 방식으로 DBCP 연동 과정에서 필요한 패키지들.
import javax.naming.Context;
import javax.naming.InitialContext;
import javax.naming.NamingException;
import javax.sql.DataSource;

public class SomeDao {
  private Connection conn = null;
  // 생략
  // JNDI 방식으로 DBCP 연동 과정에 필요한 참조 변수들
	private Context ctx = null;
	private DataSource ds = null;
	  
	public SomeDao() {
	  try {
	    // #1 
			ctx = new InitialContext();
			ds = (DataSource) ctx.lookup("java:comp/env/jdbc/test_festival");
			      // #1 end
		} catch (NamingException e) {
			  e.printStackTrace();
		}
	}
	  
	public List<SomeDto> getDtoAll() {
	  // 생략
	  try {
	    conn = ds.getConnection();  // #2
	  } catch (SQLException e) {
	    e.printStackTrace();
	  }
	}
}
```

예제 1-1

즉, 다시 정리하자면…

```java
import java.sql.Connection;
import javax.naming.Context;
import javax.naming.InitialContext;
import javax.naming.NamingException;
import javax.sql.DataSource;

// 생략
try {
  Context ctx = new InitialContext();
  DataSource ds = (DataSource) ctx.lookup("java:comp/env/");
catch (NamingException e) {//...}

try {
  conn = ds.getConnection();
} catch (SQLException e) {//...}
// 생략
```

예제 1-2

위와 같이 사용하면 톰캣 제공 DBCP에 연결하여 사용할 수 있다. 이 DBCP를 사용하면 close() 메서드를 사용해도 Connection 객체가 소멸되어 DB 연결이 바로 해제되는 게 아니라 그저 사용된 Connection 객체가 Pool에 반환된다고 한다. 

---

Refences

[1] 에이콘아카데미(강남) 강의

[2] DBCP

[DBCP(DataBase Conncetion Pool), 커넥션풀 이란?](https://zzang9ha.tistory.com/376)

[3] JDBC, DBCP, JNDI

[[Java] JDBC, DBCP, JNDI 이해하기](https://jangjjolkit.tistory.com/34)

[4] JNDI

[[JAVA] JNDI란?](https://go-coding.tistory.com/76)

[5] 디렉토리 서비스

[디렉토리 서비스](https://ko.wikipedia.org/wiki/디렉토리_서비스)

[6] JNDI

[JNDI](https://ko.wikipedia.org/wiki/JNDI)

[7] JNDI

[JNDI란?  Java Naming and Directory Interface.](https://cheershennah.tistory.com/246)

[8] WAS

[[Web] Web Server와 WAS의 차이와 웹 서비스 구조 - Heee's Development Blog](https://gmlwjd9405.github.io/2018/10/27/webserver-vs-was.html)

[9] WAS

[[Web] 웹 서버와 WAS의 차이를 쉽게 알아보자](https://codechasseur.tistory.com/25)

[10] WAS

[[Web] 웹 서버와 WAS의 개념 이해](https://velog.io/@bky373/Web-웹-서버와-WAS)

[11] Tomcat JNDI 사용법

[Apache Tomcat 9 (9.0.93) - JNDI Datasource How-To](https://tomcat.apache.org/tomcat-9.0-doc/jndi-datasource-examples-howto.html)

[12] 디렉터리 서비스. 

[[IT리더쉽과 전략] 왜, 디렉토리 서비스가 IT 업무에 중요할까요?](https://cloud.gowit.co.kr/it리더쉽과-전략-왜-디렉토리-서비스가-it-업무에-중요/)

[13] 디렉터리 서비스. 

[TTA정보통신용어사전](https://terms.tta.or.kr/dictionary/dictionaryView.do?subject=디렉터리+서비스)

[14] [https://wordrow.kr/의미/디렉토리 서비스/](https://wordrow.kr/%EC%9D%98%EB%AF%B8/%EB%94%94%EB%A0%89%ED%86%A0%EB%A6%AC%20%EC%84%9C%EB%B9%84%EC%8A%A4/)