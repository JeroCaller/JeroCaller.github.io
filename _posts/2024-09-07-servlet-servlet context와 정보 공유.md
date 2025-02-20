---
title: "[Servlet] Servlet Context와 정보 공유"
category: "Servlet & JSP"
tag: ["Servlet", "Context", "Servlet Context", "Session", "Request"]
---

# Servlet context

servlet context는 하나의 서블릿이 servlet container와 소통하기 위해 여러 메서드들을 정의해놓은 클래스이다. 자바에서는 javax.servlet.ServletContext 클래스가 존재한다. 

web app이란 것은 여러 servlet들의 집합인데, 하나의 JVM(Java Virtual Machine)이 하나의 web app을 실행한다. 또한 하나의 web app에는 하나의 context가 존재한다. 그래서 servlet context를 web app 그 자체로 정의하기도 한다. 

하나의 웹 앱 내부에 여러 개의 서블릿 객체들이 존재하고, 하나의 웹 앱에 하나의 servlet context가 존재하기에, 서블릿 객체들이 이 servlet context에 데이터를 저장하고 가져다 씀으로써 서로 데이터를 공유할 수 있다. 이 때 이 servlet context라는 공간에 데이터를 저장, 검색, 삭제하기 위해 사용하는 ServletContext 클래스의 메서드에는 각각 다음이 존재한다. 

- `void setAttribute(String name, Object value)` : name - value 쌍 데이터를 servlet context 영역에 저장.
- `Object getAttribute(String name)` : servlet context 영역에 저장된 특정 name에 해당하는 value를 반환.
- `Enumeration getAttributeNames()` : ServletContext에 등록된 모든 데이터들의 name을 반환.
- `void removeAttribute(String name)` : 특정 name과 그 value를 삭제한다.

위 메서드들 중, getAttribute, setAttribute에 대해 주의할 점은, 실제로 데이터를 저장할 때에는 무조건 Object 객체 형태로 저장한다는 점과, 이로 인해 데이터를 가져올 때 형변환이 필요하다는 것이다. 

정리하자면, servlet context는 하나의 웹앱에 생성되는 하나의 클래스이자 웹앱, 그리고 그 웹앱을 구성하는 웹 프로젝트 폴더 그 자체로, 다음의 두 가지 기능을 수행할 수 있다. 

1. servlet container와 소통하여 특정 정보를 얻을 수 있다. 
2. 하나의 웹앱 내부에 존재하는 모든 servlet 객체들이 서로 데이터를 공유할 수 있는 공간의 역할. attribute란 이름이 붙은 메서드들이 데이터 관련 작업을 수행한다. 

참고로, 1번의 기능을 하는 메서드에는 대표적으로 다음이 존재한다. 

- `String getContextPath()` : 웹앱의 컨텍스트 패스를 반환. 웹앱도 웹 프로젝트 폴더이므로, 사실상 실행 중인 웹 프로젝트 폴더의 경로를 반환한다고 볼 수 있다.
- `String getMimeType(String file)` : 특정 파일의 MIME 타입 반환.
- `RequestDispatcher getRequestDispatcher(String path)` : 특정 path의 실행 결과를 포함시키거나 요청의 이동을 위한 RequestDispatcher 객체를 반환. 이전의 [[Servlet] 페이지 이동](/servlet%20&%20jsp/servlet-페이지-이동/)에서의 forwarding을 위해 이 메서드를 사용하였음을 살펴보았었다.

위에서 소개한 ServletContext 객체의 관련 메서드 외에도 여러 메서드들이 있으니 이는 References [3]을 참조. 

다음은 앞서 설명한 servlet context를 그림으로 그려본 것이다. 

![servlet-context.drawio.png](/images/2024-09-07/servlet-servlet%20context%EC%99%80%20%EC%A0%95%EB%B3%B4%20%EA%B3%B5%EC%9C%A0/1.png)

참고로 서블릿 클래스 내에서 ServletContext 객체를 얻으려면 GenericServlet 클래스의 getServletContext() 메서드를 호출하면 된다. 

```java
// 생략
public class MyServlet extends HttpServlet {
  // 생략
  protected void doPost(HttpServletRequest request, HttpServletResponse response) {
	  // 서블릿 컨텍스트 객체 참조를 반환하여 이를 참조 변수에 할당.
    ServletContext context = getServletContext();
  }
}
```

# 서블릿 간의 정보 공유

웹앱 실행 과정에서 여러 서블릿들 간에 데이터를 공유해야할 일이 생길 경우 사용할 수 있는 공유 객체로는 다음이 존재한다.

- HttpSession (session)
- HttpServletRequest (request)
- ServletContext (application)

데이터 공유를 위한 메서드들은 위 세 객체 모두 다음과 같이 존재한다. 

- `void setAttribute(String name, Object value)` : name-value 쌍의 데이터를 공유 객체에 저장.
- `Object getAttribute(String name)` : name에 해당하는 값을 반환한다.
- `Enumeration getAttributeNames()` : 공유 객체에 저장된 모든 데이터들의 name들을 Enumeration 타입으로 반환.
- `void removeAttribute(String name)` : 공유 객체에 저장된 데이터를 삭제.

위 세 객체들은 데이터를 다른 서블릿들과 공유할 수는 있지만 각자의 특성이 있어 해당 데이터를 언제까지 유지하고 이 데이터를 사용할 수 있는 범위가 어디까지인지 등에 차이가 있다. 그래서 데이터 공유를 위해 어떤 공유 객체를 선택할지 상황에 맞게 고려할 필요가 있다. 

## session

세션 객체에 데이터를 저장하는 모든 서블릿에서 접근하여 데이터를 사용할 수 있다. 다만, 세션은 하나의 브라우저에 하나씩 생성되는 구조로, 이로 인해 어떤 사용자가 다른 사용자의 세션 정보에 접근할 수는 없다. 

또한, 세션은 만료시간이 다 되었거나, 브라우저가 종료된 경우, 또는 웹앱이나 서버가 종료될 때 세션도 삭제가 되므로 세션 객체에 저장된 공유 데이터들도 그 시점에 사라진다. 

세션은 서버의 메모리를 차지한다. 그래서 세션을 사용하는 접속자가 많은 상황에서는 서버에 부담이 가므로, 이 경우에는 세션에 공유 데이터를 저장하는 것이 별로 좋은 선택은 아니다. 

## HttpServletRequest

HttpServletRequest(이하 request로 줄여 말하겠다) 객체는 클라이언트가 서버에 요청하는 순간 생성되고, 서버가 클라이언트로 응답을 보내는 순간 삭제된다. 그래서 이 사실만 놓고 보면 request 객체에 저장된 공유 데이터는 하나의 서블릿에서만 사용 가능한 것처럼 보인다. 

하지만 forwarding을 사용하면 request 객체에 저장된 공유 데이터를 다른 서블릿에서도 사용할 수 있게 된다. 이전에 [[Servlet] 페이지 이동](/servlet%20&%20jsp/servlet-페이지-이동/) 에서 forwarding을 다뤘을 때 `RequestDispatcher.forward(request, response);` 를 이용하면 forwarding을 할 수 있음을 살펴보았다. forward()라는 메서드에 request 객체를 인자로 대입하면, 최초로 클라이언트로부터 요청받아 생성된 request 객체를 그대로 다른 서블릿에 전달할 수 있게 된다. 이로 인해 request 객체에 데이터를 저장하면 다른 서블릿에서도 그 데이터를 그대로 전달하여 공유할 수 있게 되는 것이다. 

이론적으로는 request 객체 내 공유 데이터를 하나의 웹앱을 구성하는 다른 모든 서블릿에서도 사용할 수 있다. 단, 정말로 모든 서블릿들이 최소 한 번씩 request 객체 내 데이터를 꺼내 사용하도록 하려면 최초로 클라이언트로부터 요청을 받은 서블릿에서 다른 모든 서블릿으로 연속적으로 forwarding해야 할 것이다. 그래서 실질적으로는 request 객체의 공유 데이터를 공유할 수 있는 서블릿 객체 범위는 세션보다는 작음을 유추할 수 있다. 세션은 마음만 먹으면 모든 서블릿들이 세션 객체 내 데이터에 접근하도록 할 수 있기 때문이다. 

redirect 방식으로는 request 객체 내 데이터를 다른 서블릿과 공유할 수 없다. redirect 방식은 중간에 무조건 클라이언트에 응답을 보내야 하는데, 이 때 request 객체가 삭제되기 때문이다. 클라이언트로부터 최초로 요청을 받았을 때 생성된 request 객체를 A라 할 때, 해당 서블릿에서 redirect를 하면 redirect 자동 요청을 클라이언트가 하게끔 설정되어 있는 응답을 클라이언트에게 보낼 때 A 객체가 삭제된다. 그 후, 클라이언트가 다른 경로로 리다이렉트 요청을 하면 전혀 다른 request 객체 B가 생성되는 식이다. 이로 인해 redirect 방식으로는 reqeust 객체를 이용하여 데이터를 공유할 수 없으며, 꼭 redirect 방식을 사용하면서도 다른 서블릿 객체들과 데이터를 공유해야만 하는 상황에서는 session 또는 servlet context를 대신 이용해야 할 것이다. 

사실 URL에 쿼리 문자열을 이용하면 redirect를 해도 똑같은 정보를 다른 서블릿에 전송할 수 있기는 하다. 다만 앞서 언급했듯, 최초 request 객체가 응답 전송에 의해 소멸된다는 것은 변치 않는다. 

## Servlet Context

servlet context는 서버가 실행되는 순간, 더 정확히는 servlet container가 실행되는 순간에 생성되고, 웹앱 또는 서버가 종료되면 servlet context 객체도 삭제된다. 이러한 특성 때문에, 클라이언트에 응답을 보낼 때 request 객체가 삭제되거나 만료 시간 도래 또는 브라우저 종료로 session 객체가 삭제되는 것에 비하면 데이터를 더 오래 유지할 수 있다는 특징을 지닌다. 

servlet context 객체 내에 저장된 공유 데이터는 기본적으로 모든 서블릿 객체에서 접근하여 사용할 수 있으며, 세션과는 달리 모든 클라이언트가 접근하여 공동으로 사용할 수 있다. 변수에 비유하자면 전역 변수나 마찬가지인 셈이다. 그래서 만약 한 사용자가 servlet context 내 데이터를 바꾸게끔 설계했다면 이 결과가 다른 사용자들의 화면에도 반영된다. 한 사용자가 데이터를 바꾸면 다른 사용자의 결과에는 전혀 영향을 주지 않는 session과 대비되는 점이다. 

## 공유 객체 정리

다음은 앞서 살펴본 새 공유 객체의 특징들을 간략히 정리한 것이다. 

|  | request | session | servlet context |
| --- | --- | --- | --- |
| 객체 생성 시점 | 클라이언트가 요청 시 | 클라이언트의 요청 내 쿠키에 세션 ID가 없거나, 이전에 만료되어 세션 객체가 삭제된 상황에서 클라이언트가 재요청 할 때. | 서버 구동 시. |
| 객체 소멸 시점 | 클라이언트에 응답 전송 시 | 세션 만료 시간 경과 또는 브라우저 종료. | 서버 종료 시 |
| 데이터 공유 서블릿 객체 범위 | forwarding된 서블릿. | 하나의 클라이언트에 한해 모든 서블릿에서 접근 가능.  | 모든 서블릿에서 접근 가능. |
| 클라이언트의 데이터 접근 범위 | 요청을 한 특정 클라이언트만 (지역 변수 특성) | 특정 클라이언트만 (지역 변수 특성) | 모든 클라이언트가 특정 데이터에 접근 가능. (전역 변수 특성) |
| 데이터 보존 기간이 가장 긴 순위 | 3 | 2 | 1 |
| 데이터 접근 범위가 가장 큰 순위 | 3 | 2 | 1 |

---

References

[1] 에이콘아카데미(강남) 강의

[2] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[3] ServletContext 객체와 servlet context에 대한 공식 설명

[ServletContext (Java(TM) EE 8 Specification APIs)](https://javaee.github.io/javaee-spec/javadocs/javax/servlet/ServletContext.html)

[4] [Servlet Container 정리](https://velog.io/@jihoson94/Servlet-Container-정리)