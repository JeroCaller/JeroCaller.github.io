---
title: "[Servlet] 웹앱 구조와 Servlet의 Life Cycle"
category: "Servlet & JSP"
tag: ["Servlet", "Life Cycle", "Web", "Web App"]
use_math: false
---

# 웹 앱 구조

자바로 구현한 웹 앱은 서버 사이의 이식성이 기본적으로 보장된다고 한다. 이는 만약 톰캣 서버 기반으로 자바에서 웹 앱을 제작하였으면 이를 다른 제품의 서버에서도 웹 앱을 실행시킬 수 있다는 뜻이다. 그런데 이러한 서버 간 이식성을 보장하려면 모든 서버가 웹 앱을 인식하도록 이미 정의된 디렉토리 구조를 갖춰야 한다. 

## 웹 앱 배포를 위한 패키징

웹 앱을 다 만들고 나면 이를 하나의 묶음으로 패키징하여 서버에 배포할 수 있게 되는데, 이 때 WAR(Web ARchive) 파일로 패키징하면 된다. 앞서 제작한 MyFirstWebApp 프로젝트를 WAR 파일로 패키징해보자. 

1. 이클립스의 [Project Explorer]에서 패키징하길 원하는 웹앱 프로젝트 폴더에 마우스 우클릭 → [Export] → [WAR file]을 누른다. 
    
    ![20240821_09200825.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/1.png)
    
2. 다음과 같은 창이 뜨는데, [Destination] 입력란 옆에 [Browse] 버튼을 눌러 생성될 WAR 파일을 어디에 저장할 것인지 지정한다. 여기서는 WAR 파일의 구조를 나중에 볼 것이기에 작업 중이던 웹앱 프로젝트인 MyFirstWebApp 폴더로 지정하였다. 다 되었으면 [Finish] 버튼을 누른다. 
    
    ![스크린샷 2024-08-21 092306.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/2.png)
    
3. 그 후, 이클립스의 [Project Explorer] 창에서 키보드 F5를 눌러 새로고침하면 WAR 확장자 파일이 보일 것이다. 아래 사진에는 맨 아래 “MyFirstWebApp.war” 파일이 새로 생성된 것을 확인할 수 있다. 이로써 웹 앱 패키징을 완료한 것이다. 
    
    ![스크린샷 2024-08-21 092650.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/3.png)
    

## 웹 앱 디렉토리 구조 살펴보기

앞서 받은 .war 확장자 파일을 이클립스에서 더블클릭해보자. 그러면 다음과 같은 화면이 뜰 것이다. 

![1.PNG](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/4.png)

여기서 디렉토리 구조만 확대하면 다음과 같다.

![2.PNG](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/5.png)

위 디렉토리 구조에는 다음의 사실들이 있다. 

- 앞서 만든 웹앱의 루트 디렉토리가 되는 MyFirstWebApp의 이름이 url에서 포트번호 뒤 URI의 맨 첫 번째로 온다. 즉, 
`http://localhost:8080/MyFirstWebApp/login.html` 이라는 url에서 uri에 해당하는 `MyFirstWebApp/login.html` 경로의 맨 왼쪽에 오는 것을 알 수 있다.
- HTML, CSS, image등의 정적 파일과 JSP와 같은 동적 파일들은 웹 앱 루트 디렉토리 바로 아래에 위치하게 된다.
- WEB-INF 폴더 내부에는 웹 앱 실행 시 필수적인 web.xml, 컴파일된 클래스 파일들, 외부 라이브러리 파일(jar) 등이 포함되어 있다. 해당 폴더는 클라이언트에서 요청할 수 없는 디렉토리이다. 즉, 클라이언트 쪽에서 보면 완전히 가려져 있는 셈이다. 이 디렉토리는 반드시 루트 디렉토리 바로 아래에 위치해야 한다. 
만약 이 디렉토리를 url 경로로 요청하려고 하면 다음의 결과를 얻는다.
    
    ![3.PNG](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/6.png)
    
    즉, 해당 파일을 찾을 수 없다고 뜬다. 서버 쪽에서 클라이언트에 WEB-INF에 접근하지 못하도록 제한하는 것이다. 그만큼 웹 앱에 중요한 파일들이 모여있는 폴더라 보면 되겠다. 
    

# Context path 변경

앞서 언급했듯, 서버에 요청을 할 때 루트 디렉토리명을 일일히 작성하기 싫을 때 이를 생략해도 똑같이 동작하도록 하는 방법이 있다. 

1. 이클립스의 [Project Explorer]에서, MyFirstWebApp 디렉토리 아래에 Servers라는 디렉토리가 있을 것이다. 그 안에 있는 “server.xml” 파일을 더블 클릭한다. 
    
    ![4.PNG](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/7.png)
    
2. 그러면 해당 파일이 다음과 같이 뜬다. 
    
    ![5.PNG](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/8.png)
    
3. 해당 파일에서 맨 아래로 스크롤 내리다보면 다음과 같이 `<Context>` 태그가 보일 것이다. 해당 태그의 path 속성값이 “/MyFirstWebApp”과 같이 되어 있을 것이다. 이를 맨 앞의 슬래쉬(/) 기호만 남긴 채 나머지는 모두 지워버린다. 즉, path=”/”가 되게 만든다. 그리고 해당 파일을 저장하자. 
    
    ![6.PNG](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/9.png)
    
4. 이제 톰캣 서버를 재시작한뒤, 브라우저 url 입력창에 루트 디렉토리명이 생략된 `http://localhost:8080/login.html` 를 입력해보자. 그러면 다음과 같이 뜬다. 
    
    ![7.PNG](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/10.png)
    

루트 디렉토리 이름을 제외하고 입력해도 요청이 잘 처리된 것을 확인할 수 있다. 

# 톰캣 서버의 구조

톰캣 서버의 내부 구조는 대략 다음과 같다.

![server-structure.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/11.png)

톰캣 서버 내부는 크게 웹 서버(Web server)와 서블릿 컨테이너(Servlet Container)로 구성되어 있다. 

- 웹 서버는 클라이언트에서 요청한 리소스가 정적 리소스라면 이를 웹 서버 내부에서 검색 후 응답한다. 동적 리소스의 요청을 처리하는 서블릿 컨테이너의 응답 속도보다 더 빠르다.
- 서블릿 컨테이너는 서블릿 객체를 생성 및 관리하는 곳으로, 동적 리소스에 대한 요청을 처리하는 곳이다. 처리 시간은 웹 서버에서의 정적 컨텐츠 관련 요청 처리 시간에 비해 많이 걸린다고 한다.
- web.xml 파일은 서블릿 컨테이너 내에서 어떤 서블릿 객체들이 생성되고 관리되어야 할지를 기술한 설정 파일이다.

클라이언트가 톰캣 서버에 요청을 보낼 때 이 요청을 처리하는 과정은 다음과 같다.

- 클라이언트의 요청은 정적 리소스에 대한 요청이건 동적 리소스이건 1차적으로 웹 서버에 전달된다. 정적 리소스 요청은 웹 서버에서 처리되어 응답된다. 그러나 동적 리소스에 대한 요청일 경우, 웹 서버에서 처리하지 못하므로 이 때 서블릿 컨테이너로 해당 요청을 전달한다.
- 서블릿 컨테이너는 WEB-INF 폴더에 위치한 web.xml 파일을 로드해온다. 해당 파일에 명시된 어떤 서블릿 객체를 생성하고 어떻게 관리할지에 대한 정보에 따라 서블릿 객체를 생성하여 동적 리소스 요청을 처리한다. 처리된 요청에 대한 응답은 다시 웹 서버로 전달한다.
- 웹 서버로 전달된 동적 리소스 요청에 대한 응답을 웹 서버는 다시 클라이언트에 전송한다. 그러면 사용자는 브라우저를 통해 응답 결과를 받아볼 수 있게 된다.

서블릿 컨테이너 내에서 서블릿 객체가 생성되고 요청에 대한 처리를 하려면 우선 web.xml 파일을 로드하여 해당 파일의 정보를 토대로 동작한다. 따라서 web.xml 파일에 오류가 있으면 서블릿 컨테이너가 제대로 작동하지 못한다. 

톰캣 서버 구동 시 “console”창에 찍힌 메세지들을 잘 보면 다음의 메시지가 존재한다. 

```java
INFO: 서버 엔진을 시작합니다: [Apache Tomcat/9.0.93]
```

이 메세지는 서블릿 컨테이너가 생성될 때 출력되는 로그이다. 위 로그에는 “서버 엔진”이란 말이 있는데, 서버 엔진이 곧 서블릿 컨테이너인 것이다. 

# 서블릿 구조 및 Life Cycle

## 서블릿 작성 규칙

일반적으로 자바에서 볼 수 있는 여러 클래스들과 달리, 서블릿 클래스에는 작성하기 위한 규칙들이 있다. 

- javax.servlet, javax.servlet.http 패키지의 API를 import해야 한다.
    - 그래야 HTTP 요청을 처리할 수 있는 것 같다. 해당 패키지에는 HttpServlet, HttpServletRequest, HttpServletResponse 클래스가 존재하기 때문이다.
- 서블릿 클래스는 public으로 선언해야 한다.
    - 서블릿 컨테이너가 서블릿 객체에 접근할 수 있어야 하기 때문.
    - 만약 public으로 정하지 않으면 후에 IllegalAccessException이 발생한다고 한다.
- HttpServlet 클래스를 상속한다.
- 기본 생성자가 반드시 있어야 한다. 매개변수가 존재하는 생성자로 바꿨으면 반드시 기본 생성자도 명시하여 정의해야 한다.
    - 서블릿 컨테이너는 서블릿 객체 생성 시 기본 생성자를 이용하기 때문이라고 한다.
- Life cycle(생명 주기)와 관련된 콜백 메서드를 오버라이딩한다.

앞서 작성한 LoginServlet 클래스를 보자.

```java
package com.cola.web.user;

// 패키지 임포트
import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class LoginServlet
 * HTTPServlet 클래스를 상속하고 있다. 
 * 또한 public으로 선언하였다. 
 */
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
  /**
   * @see HttpServlet#HttpServlet()
   * 기본 생성자
   */
  public LoginServlet() {
  	System.out.println("=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===");  // 변경된 코드.
  }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("=== GET 요청을 서블릿에서 처리하였습니다. ===");  // 변경된 코드.
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("=== POST 요청을 서블릿에서 처리하였습니다. ===");  // 변경된 코드.
	}

}

```

LoginServlet.java

위 코드에서 볼 수 있듯, 앞서 언급한 대부분의 규칙들은 이클립스에서 서블릿 생성 시 자동으로 지켜지고 있는 것을 볼 수 있다. 

## 서블릿 클래스 계층 구조

서블릿 클래스의 상속 구조는 다음과 같이 되어 있다. 

- interface Servlet (최상위 인터페이스)
- class GenericServlet
- class HttpServlet

즉, GenericServlet은 최상위 인터페이스인 Servlet을 받아 구현하고, HttpServlet은 그런 GenericServlet 클래스를 상속받는다. 사용자가 서블릿 클래스를 생성할 때 HttpServlet 클래스를 상속받아 사용하는 구조인 것이다. 

서블릿 컨테이너가 서블릿 인스턴스를 생성, 관리할 때 해당 인스턴스의 타입을 최상위 인터페이스인 Servlet 인터페이스로 자동 타입 변환을 한다고 한다. 따라서 이론적으로는 Servlet 인터페이스를 직접 구현받아도, GenericServlet 클래스를 상속받아 사용해도 된다. 

그러나, 클라이언트로부터 HTTP 요청을 받고 이를 서버에서 처리해야하는 작업에 대해서는 HttpServlet 클래스를 상속받아 사용해야 한다(다른 클래스 또는 인터페이스는 HTTP 요청 처리 기능이 없어서 그런 것 같다). 이러한 이유로 여태까지 LoginServlet 서블릿 클래스 생성 시 HttpServlet 클래스를 상속받아 정의한 것이었다. 

## 서블릿 객체 생성

web.xml 파일에 등록된 서블릿 클래스는 브라우저가 서버에 요청을 전달하고 서버에서 이를 처리하고자 할 때 그 객체가 생성된다. 이 때, 서블릿 컨테이너가 작동되는 시점이 아닌, 그보다 이후의 시점인 서버에 클라이언트의 요청이 전달될 때 서블릿 객체를 생성하는 것을 lazy-loading이라 한다. 즉, 서블릿 컨테이너가 서블릿 객체 생성 시점을 클라이언트의 요청 시점까지 늦추는 것이다. 이러한 방식은 클라이언트 요청에 즉각 반응할 수는 없고 조금 느리긴 하지만, 서버 메모리를 효율적으로 사용할 수 있다는 장단점이 존재한다. 

반대로, 서블릿 컨테이너가 작동되는 시점에 서블릿 객체를 생성하는 것을 pre-loading이라 한다. 이 loading은 클라이언트의 요청과 상관없이 무조건 서블릿 컨테이너가 작동될 시점에 생성된다. 이 방식은 클라이언트 요청에 대한 반응 속도는 빠르지만, 그 전까지는 사용되지 않는 객체를 생성하는 것이기에 서버 메모리를 낭비하는 장단점이 존재한다. 

만약 특정 서블릿 객체의 pre-loading을 원하면, web.xml 파일의 `<servlet>` 태그에 `<load-on-startup>1</load-on-startup>` 코드를 삽입한다. 이후 서버를 재구동하면 된다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>MyFirstWebApp</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.jsp</welcome-file>
    <welcome-file>default.htm</welcome-file>
  </welcome-file-list>
  <servlet>
    <description></description>
    <display-name>processLogin</display-name>
    <servlet-name>processLogin</servlet-name>
    <servlet-class>com.cola.web.user.LoginServlet</servlet-class>
    <load-on-startup>1</load-on-startup>  <!-- 추가 -->
  </servlet>
  <servlet-mapping>
    <servlet-name>processLogin</servlet-name>
    <url-pattern>/processLogin</url-pattern>
  </servlet-mapping>
</web-app>
```

web.xml

이클립스의 “console” 창을 보면 다음과 같은 메시지가 있다. 

```bash
(생략)
INFO: 서비스 [Catalina]을(를) 시작합니다.
8월 26, 2024 6:04:20 오전 org.apache.catalina.core.StandardEngine startInternal
INFO: 서버 엔진을 시작합니다: [Apache Tomcat/9.0.93]
8월 26, 2024 6:04:20 오전 org.apache.jasper.servlet.TldScanner scanJars
INFO: 적어도 하나의 JAR가 TLD들을 찾기 위해 스캔되었으나 아무 것도 찾지 못했습니다. 스캔했으나 TLD가 없는 JAR들의 전체 목록을 보시려면, 로그 레벨을 디버그 레벨로 설정하십시오. 스캔 과정에서 불필요한 JAR들을 건너뛰면, 시스템 시작 시간과 JSP 컴파일 시간을 단축시킬 수 있습니다.
=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===
8월 26, 2024 6:04:20 오전 org.apache.coyote.AbstractProtocol start
INFO: 프로토콜 핸들러 ["http-nio-8080"]을(를) 시작합니다.
8월 26, 2024 6:04:21 오전 org.apache.catalina.startup.Catalina start
INFO: 서버가 [409] 밀리초 내에 시작되었습니다.
```

위 메세지에서, `INFO: 서버 엔진을 시작합니다: [Apache Tomcat/9.0.93]` 라는 문구가 나온 시점에서 서블릿 컨테이너가 작동하기 시작했음을 알 수 있다. 그리고 바로 밑에 `=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===` 이라는 메시지가 떴다. 앞서 LoginServlet 클래스의 기본 생성자 내부에 필자가 작성한 메시지 출력 코드가 여기서 뜬 것이다. 이를 통해 해당 서블릿 객체가 pre-loading 되었음을 알 수 있다. 

pre-loading을 원치 않는다면 아까 작성한 load-on-startup 태그를 삭제한다. 

## 서블릿 컨테이너 내부 구조 및 작동 원리

서블릿 컨테이너에서는 클라이언트 요청을 처리하기 위해 생성한 서블릿 객체의 여러 콜백 메서드들을 호출하여 처리하며, 서블릿 객체의 생성, 관리, 소멸의 과정에서도 모두 서블릿 객체의 콜백 메서드를 호출하여 관리하는 과정을 가진다. 

이러한 콜백 메서드들은 서블릿 컨테이너가 직접 호출하는 것은 아니고, 서블릿 컨테이너 내부에 여러 스레드 객체들을 생성 및 관리하는 스레드 풀(Thread Pool)을 생성하여 각 스레드 객체가 각 서블릿 객체와 연결되어 스레드 객체가 서블릿 객체의 콜백 메서드를 적재적소에 호출하는 방식이다. 

스레드와 스레드 풀에 관한 자세한 내용은 [스레드 (thread)](/java/java-thread/) 페이지 참조. 

서블릿 컨테이너 내에서 서블릿 객체의 생명 주기에 관해 다음의 과정을 따른다.

![사진 1. 서블릿 컨테이너 내부 구조](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/12.png)

사진 1. 서블릿 컨테이너 내부 구조

1. 서블릿 컨테이너가 구동되면, 스레드 풀을 생성하고 필요에 따라 스레드 객체들을 생성한다. 
2. 클라이언트로부터 동적 컨텐츠에 대한 요청을 받으면, 우선 해당 요청을 처리할 서블릿 객체가 존재하는지 확인한다. 메모리에 없으면 web.xml 설정 파일에 등록된 정보에 따라 해당 서블릿 클래스(.class)를 메모리에 적재(loading)한다. 그 후, 해당 서블릿 클래스로부터 기본 생성자를 호출함으로써 해당 서블릿 객체를 생성한다. 
3. 생성된 서블릿 객체에 대해, 서블릿 객체에 있는 `init()` 메서드를 서블릿 컨테이너가 호출함으로써 서블릿 객체의 맴버 변수들을 초기화한다. 이렇게 `init()` 메서드 호출로 초기화된 후에 스레드 풀로부터 스레드 객체 하나를 할당받고, 해당 스레드 객체가 서블릿 객체의 `service()` 메서드를 호출한다. 
4. `service()` (HttpServlet 클래스에 정의되어 있고, 이를 개발자가 LoginServlet처럼 상속받아 간접적으로 사용하는 것이다) 메서드는 전달받은 HTTP 요청 방식이 GET이면 `doGet()` 메서드를, POST면 `doPost()` 메서드를 호출한다. 
5. 만약 서블릿 객체가 리로딩되어야 하거나 필요가 없어 삭제될 때에는 서블릿 객체의 `destroy()`라는 메서드를 호출하여 삭제한다. 

1 → 2 → 3 으로 이어지는 과정에서, .class 파일 로딩 → 서블릿 객체 생성 → `init()` 메서드 호출 과정은 최초의 서블릿 요청에 대해서만 단 한 번 실행된다. 그 이후 동일한 요청이 들어온다면 그 때부터는 새로운 스레드가 할당된 후 `service()` 메서드만 호출한다. 이를 확인하고자 한다면 앞서 만든 MyFirstWebApp 프로젝트를 톰캣 서버에 구동시켜 http:localhost:8080/login.html 파일에서 아이디, 비밀번호를 각각 입력하고 “로그인” 버튼을 눌러보자. 그러면 이클립스의 “Console” 창에서 처음에는 다음의 메시지가 뜰 것이다. 

```bash
=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===
=== POST 요청을 서블릿에서 처리하였습니다. ===
```

다시 login.html 화면으로 돌아와 같은 요청을 또 하면 다음의 메시지가 뜬다.

```bash
=== POST 요청을 서블릿에서 처리하였습니다. ===
```

이를 통해 최초의 요청에 대해서만 서블릿 객체가 생성되고 그 다음부터는 같은 객체를 재활용하여 요청을 처리한다는 것을 알 수 있다. 

서블릿 객체들이 생성될 때마다 그에 할당할 스레드 객체들을 무한정 생성하고, 한 번 생성한 스레드 객체를 딱 한 번만 작업하고 삭제하기에는 메모리 상으로 한계가 있고 성능 상으로도 안 좋을 수 있기에 스레드 풀을 따로 만들어 정해진 개수의 스레드 객체들만을 생성한 후, 작업이 끝난 스레드 객체는 바로 삭제하지 않고 다음 서블릿 객체에 할당하도록 하여 스레드 객체들을 재활용하는 방식을 택한 것 같다. 또한, 특정 요청을 처리할 서블릿 객체를 삭제하지 않고 똑같은 요청이 다음에 들어오면 같은 객체가 처리하게끔 하여 무분별한 서블릿 객체의 생성을 막아 메모리를 효율적으로 사용하는 구조임을 유추할 수 있다. 

정말 위에서의 설명과 같은 방식으로 서블릿 객체가 운용되는지 확인하기 위해 LoginServlet 클래스 내부 코드에 상위 클래스인 HttpServlet으로부터 상속받은 몇몇 콜백 메서드들을 오버라이딩해보겠다. 여기서는 `service()`, `init()`, `destroy()`에 대해 실행해보겠다. 아래 코드에서 `@Override` 표시가 되어있는 메서드들이 오버라이딩한 메서드들이다. 

```java
package com.cola.web.user;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * Servlet implementation class LoginServlet
 */
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
  /**
   * @see HttpServlet#HttpServlet()
   */
  public LoginServlet() {
    System.out.println("=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===");
  }

	/**
	 * @see HttpServlet#doGet(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("=== GET 요청을 서블릿에서 처리하였습니다. ===");
	}

	/**
	 * @see HttpServlet#doPost(HttpServletRequest request, HttpServletResponse response)
	 */
	protected void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
		System.out.println("=== POST 요청을 서블릿에서 처리하였습니다. ===");
	}

	@Override
	/**
	 * service 메서드는 HttpServlet 클래스에 정의된 메서드이다. 
	 */
	protected void service(HttpServletRequest arg0, HttpServletResponse arg1) throws ServletException, IOException {
		System.out.println("==== service 메서드가 호출되었습니다. ====");
	}
	
	@Override
	/**
	 * destroy는 GenericServlet에 정의된 메서드이다. 
	 */
	public void destroy() {
		System.out.println("==== destroy 메서드가 호출되어 서블릿 객체가 삭제됩니다. ====");
	}

	@Override
	/**
	 * init은 GenericServlet에 정의된 메서드이다.
	 */
	public void init() throws ServletException {
		 System.out.println("==== init 메서드가 호출되어 서블릿 객체 내 멤버 변수가 초기화됩니다.");
	}

}

```

LoginServlet.java

이 파일을 저장하면 서블릿이 리로딩될 것이다. 그 후 login.html 웹 페이지에서 로그인을 시도하면 이클립스 콘솔창에서 다음의 결과를 얻는다. 

```java
=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===
==== init 메서드가 호출되어 서블릿 객체 내 멤버 변수가 초기화됩니다.
==== service 메서드가 호출되었습니다. ====
```

LoginServlet 클래스 내 오버라이딩한 메서드 내용들과 출력결과를 비교해보면, 차례대로 기본 생성자 → `init()` → `service()` 메서드가 순서대로 호출되었음을 알 수 있다. 

`destroy()` 메서드를 실험해보기 위해선 서블릿 객체가 삭제되어야 한다. 서블릿 객체를 삭제하는 데에는 두 가지 방법이 있다.

- 아예 서버를 종료한다. 서블릿 컨테이너는 종료 전에 컨테이너 내부에 존재하는 서블릿 객체들을 모두 삭제하고, 이 삭제를 위해 `destroy()` 메서드를 호출하기 때문.
- 서버가 실행되는 상태에서 서블릿 객체의 코드를 수정하면 기존 서블릿 객체가 삭제되어야 새 서블릿 객체가 생성되는데, 이를 리로딩(reload)이라 한다. 즉, 서블릿 객체가 메모리 상에서 삭제되었다가 새 코드로 수정된 새 서블릿 객체가 메모리에 로딩되는 과정을 거치는 것이다. 이 과정에서 `destroy()` 메서드를 호출해야 기존 서블릿 객체를 삭제시킬 수 있다.

여기서는 두 번째 방법을 써보자. LoginServlet.java 파일 아무 곳에 공백을 넣거나 주석을 넣자. 코드를 잘못 건드려 에러가 나지 않도록 하면서도 사실상 코드에 수정을 가하는 것이다. 그 후 저장하고 console 창을 보면 다음의 메시지가 뜬다. 

```bash
=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===
==== 서블릿 객체의 맴버 변수가 초기화되었습니다. ====
==== service() 메서드를 호출하여 요청을 처리하였습니다. =====
8월 27, 2024 11:13:35 오후 org.apache.catalina.core.StandardContext reload
INFO: 이름이 []인 컨텍스트를 다시 로드하는 작업이 시작되었습니다.
==== 서블릿 객체를 삭제하였습니다. ====
8월 27, 2024 11:13:36 오후 org.apache.jasper.servlet.TldScanner scanJars
INFO: 적어도 하나의 JAR가 TLD들을 찾기 위해 스캔되었으나 아무 것도 찾지 못했습니다. 스캔했으나 TLD가 없는 JAR들의 전체 목록을 보시려면, 로그 레벨을 디버그 레벨로 설정하십시오. 스캔 과정에서 불필요한 JAR들을 건너뛰면, 시스템 시작 시간과 JSP 컴파일 시간을 단축시킬 수 있습니다.
8월 27, 2024 11:13:36 오후 org.apache.catalina.core.StandardContext reload
INFO: 이름이 []인 컨텍스트를 다시 로드하는 것을 완료했습니다.

```

위 출력 결과에서 `==== 서블릿 객체를 삭제하였습니다. ====` 메시지가 뜬 걸로 보아 코드 수정으로 인해 destroy() 메서드가 호출되었음을 알 수 있다.

# web.xml과 서블릿 설정

앞서 서버에서 클라이언트의 요청을 처리하기 위해 서블릿이 어떻게 동작되는지를 살펴보았다. 여기서 알 수 있듯, 서블릿 클래스를 사용하려면 해당 서블릿 정보가 반드시 web.xml에 등록되어 있어야 한다. 여기서는 web.xml에 대해 살펴보겠다. 

다음은 web.xml 파일이다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>MyFirstWebApp</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.jsp</welcome-file>
    <welcome-file>default.htm</welcome-file>
  </welcome-file-list>
  <servlet>
    <description></description>
    <display-name>processLogin</display-name>
    <servlet-name>processLogin</servlet-name>
    <servlet-class>com.cola.web.user.LoginServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>processLogin</servlet-name>
    <url-pattern>/processLogin</url-pattern>
  </servlet-mapping>
</web-app>
```

web.xml

여기서 다음을 더 자세히 보겠다. 

```xml
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
```

맨 위에서 두 번째 줄에 나오는 `<web-app>`이 해당 xml의 최상위 요소이다. 해당 요소의 속성으로 xmlns와 xsi:schemaLocation이 보인다. 이러한 설정에 의해, web.xml 에서는 `xsi:schemaLocation=http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd` 이라는 파일에 등록된 요소들만을 사용해야 한다. web.xml에는 그 중 `<display-name>`, `<welcome-file-list>`, `<servlet>`, `<servlet-mapping>` 요소들이 사용되었다.

각 요소들에 대한 설명

- `<display-name>` : 생략 가능. 웹앱의 이름을 지정한다.
- `<welcome-file-list>`: `<welcome-file-list>`의 자식 요소인 `<welcom-file>` 태그 내부에 등록한 파일명이 웰컴 파일이 된다. 웰컴 파일은 사용자가 서버에 접근할 때 가장 먼저 사용자에게 제공되는 파일이라서 URL에 해당 파일명을 입력하지 않아도 서버가 자동으로 인식한다. 이러한 이유로, `<welcome-file>` 태그 안에 login.html이라고 적으면 `http://localhost:8080/` 까지만 입력해도 이전과 똑같은 로그인 페이지에 들어갈 수 있다. 
원래는 `index.jsp` 를 웰컴 파일에 등록한다고 한다. 필자는 아직 jsp를 몰라서 당분간은 login.html 파일을 웰컴 파일로 사용하겠다.
- `<servlet>` : 서블릿과 직접적으로 연관된 요소 중 하나. 서블릿 클래스 등록에 사용되며, 세부 정보는 자식 요소인 `<servlet-name>`과 `<servlet-class>` 요소에 기입한다.
    - `<servlet-name>` : 생성될 서블릿 객체의 이름이다. 필자가 앞서 만든 LoginServlet이라는 서블릿 클래스의 이름과는 별도로 짓는 것 같다. 서블릿 컨테이너는 해당 태그에 설정된 이름으로 서블릿 객체들을 식별하기에 web.xml 파일에 등록되는 서블릿 객체 및 클래스들의 이름이 중복되어서는 안된다.
    - `<servlet-class>` : 패키지 경로를 포함한 서블릿 클래스명을 기입한다.
- `<servlet-mapping>` : 서블릿과 직접적으로 연관된 요소 중 하나. 브라우저가 특정 요청을 할 수 있도록 요청 URL과 해당 요청을 처리할 서블릿을 서로 연결(매핑)할 때 사용하는 요소이다. 자식 요소로 `<servlet-name>`과 `<url-pattern>` 요소가 있다.
    - `<servlet-name>` : 앞서 `<servlet>` 요소 내부에 등록되어 있는 서블릿 중 하나의 이름을 그대로 적으면 된다.
    - `<url-pattern>` : URL 형식 지정. 실제 브라우저에서 URL에 어떻게 입력해야 해당 서블릿으로 요청 처리할 수 있을지 그 URL 형식을 지정하는 것이다. 필자는 해당 요소에 `/processLogin` 이라 되어 있는데, 이 경우, 브라우저 URL의 맨 끝에 `/processLogin` 을 붙이면 결국에는 이 경로와 매핑된 LoginServlet 클래스의 객체가 생성되어 해당 요청을 처리하게 되는 것이다. 이 요소에 지정된 이름을 토대로 HTML의 form 태그의 action 속성에 그대로 지정하여 매핑함으로써, 폼 태그에 입력된 정보와 함께 제출되면 해당 서블릿으로 요청을 처리할 수 있는 구조이다. (앞서 action 속성값을 서블릿 객체 이름과 똑같이 `processLogin` 이라고 한 이유가 여기에 있다)

위 사실들 중 몇 가지를 알아보자. 

먼저 `<welcome-file>` 태그에 `login.html` 이라고 추가해보고 서버를 재구동한 뒤, `http://localhost:8080` 까지만 입력해보자.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>MyFirstWebApp</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.jsp</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>login.html</welcome-file> <!-- 추가한 코드 -->
  </welcome-file-list>
  <servlet>
    <description></description>
    <display-name>processLogin</display-name>
    <servlet-name>processLogin</servlet-name>
    <servlet-class>com.cola.web.user.LoginServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>processLogin</servlet-name>
    <url-pattern>/processLogin</url-pattern>
  </servlet-mapping>
</web-app>
```

web.xml

![스크린샷 2024-08-28 092456.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/13.png)

브라우저 url 입력창의 주소 맨 끝에 `login.html`  을 붙이지 않았음에도 해당 로그인 페이지가 잘 뜨는 것을 확인할 수 있다. 참고로 `login.html` 을 덧붙여도 똑같이 작동되는 것을 확인하였다. 

다음으로, `<servlet-mapping>` 태그 내 자식 태그들과 `<servlet>` 태그의 자식 태그인 `<servlet-name>` 태그 내 정보를 다음과 같이 바꿔보았다.

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>MyFirstWebApp</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.jsp</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>login.html</welcome-file>
  </welcome-file-list>
  <servlet>
    <description></description>
    <servlet-name>login</servlet-name> <!-- 바뀐 곳 -->
    <servlet-name>processLogin</servlet-name>
    <servlet-class>com.cola.web.user.LoginServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>login</servlet-name>  <!-- 바뀐 곳 -->
    <url-pattern>/login.do</url-pattern> <!-- 바뀐 곳 -->
  </servlet-mapping>
</web-app>
```

web.xml

그 후, login.html → form 태그 → action 속성값을 `<url-pattern>` 값과 똑같은 (슬래시 기호는 제외) `login.do` 라고 입력한다. 

```html
<!DOCTYPE html>
<html>
<head>
	<meta charset="UTF-8">
	<title>JeroCaller의 홈페이지 로그인</title>
    <link rel="stylesheet" href="./style.css">
</head>
<body>
    <div id="container">
	    <h1>Welcome!</h1>
        <img src="./images/jerocaller.jpg" width="50%">
	    <form action="login.do" method="post">  <!-- action 속성값 바뀜 --> 
	    	<fieldset>
	    		<legend>로그인</legend>
	    		<label for="id">아이디 : </label> 
                <input type="text" id="id" name="id" /> 
	    		<label for="password">비밀번호 : </label>
                <input type="password" id="password" name="password" />
                <input type="submit" value="로그인" />
	    	</fieldset>
	    </form>
    </div>
</body>
</html>
```

login.html

web.xml 파일을 수정하면 서버를 재구동해야 제대로 작동하니 서버를 재시작하자. 그 후 브라우저에서 로그인 화면 새로고침 후, 로그인 버튼을 눌러보자. 

![스크린샷 2024-08-28 093251.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/14.png)

이클립스 콘솔창은 다음과 같다.

```bash
8월 28, 2024 9:32:33 오전 org.apache.coyote.AbstractProtocol start
정보: 프로토콜 핸들러 ["http-nio-8080"]을(를) 시작합니다.
8월 28, 2024 9:32:33 오전 org.apache.catalina.startup.Catalina start
정보: 서버가 [372] 밀리초 내에 시작되었습니다.
=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===
==== init 메서드가 호출되어 서블릿 객체 내 멤버 변수가 초기화됩니다.
==== service 메서드가 호출되었습니다. ====
```

`service()` 메서드가 호출된 것으로 보아, login.do 요청 url이 잘 매핑되었음을 알 수 있다. 

## 어노테이션으로 더 쉽게 서블릿 설정하기

web.xml으로 서블릿 클래스를 등록하고, 이를 수정할 때마다 서버를 재시작하는 것은 좀 번거롭다. 자바에서는 web.xml을 대신하여 좀 더 쉽게 서블릿 설정을 해줄 수 있는 기능을 annotation을 통해 제공해준다. 

이를 확인하기 위해 우선 web.xml 파일 내에서 `<servlet>`, `<servlet-mappin>` 태그 전체를 주석 처리하겠다. 

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://java.sun.com/xml/ns/javaee" xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd" id="WebApp_ID" version="2.5">
  <display-name>MyFirstWebApp</display-name>
  <welcome-file-list>
    <welcome-file>index.html</welcome-file>
    <welcome-file>index.jsp</welcome-file>
    <welcome-file>index.htm</welcome-file>
    <welcome-file>default.html</welcome-file>
    <welcome-file>default.jsp</welcome-file>
    <welcome-file>default.htm</welcome-file>
    <welcome-file>login.html</welcome-file>
  </welcome-file-list>
  <!-- 
  <servlet>
    <description></description>
    <display-name>processLogin</display-name>
    <servlet-name>login</servlet-name>
    <servlet-class>com.cola.web.user.LoginServlet</servlet-class>
  </servlet>
  <servlet-mapping>
    <servlet-name>login</servlet-name>
    <url-pattern>/login.do</url-pattern>
  </servlet-mapping>
  -->
</web-app>
```

web.xml

이 상태에서 서버를 재구동하고 브라우저의 로그인 페이지에서 “로그인” 버튼을 누르면 404 상태 코드 및 에러 메시지가 뜬다. 이제 LoginServlet.java에서 다음과 같이 코드를 수정한다. 

```java
package com.cola.web.user;

import java.io.IOException;
import javax.servlet.ServletException;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.annotation.WebServlet;  // 추가한 코드

@WebServlet("/login.do")   // 서블릿 요청 매핑 url를 괄호 안에 작성.
/**
 * Servlet implementation class LoginServlet
 */
public class LoginServlet extends HttpServlet {
	private static final long serialVersionUID = 1L;
       
    /**
     * @see HttpServlet#HttpServlet()
     */
    public LoginServlet() {
    	System.out.println("=== 로그인을 위한 서블릿 인스턴스가 생성되었습니다. ===");
    }
// 생략
```

LoginServlet.java

위 파일을 저장하고 서버 재구동 및 “로그인” 버튼을 눌러보자.

![스크린샷 2024-08-28 123215.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/15.png)

![스크린샷 2024-08-28 123221.png](/images/2024-08-21/servlet-%EC%9B%B9%EC%95%B1%20%EA%B5%AC%EC%A1%B0%EC%99%80%20servlet%EC%9D%98%20life%20cycle/16.png)

web.xml 파일 내 설정이 주석 처리되어 없어졌음에도 `@WebServlet` 어노테이션 하나만으로도 서블릿 요청이 잘 되는 것을 확인할 수 있다. 

---

References

[1] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)