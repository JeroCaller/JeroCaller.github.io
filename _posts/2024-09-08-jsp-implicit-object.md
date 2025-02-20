---
title: "[JSP] Implicit Object (내장 객체)"
category: "Servlet & JSP"
tag: ["JSP", "Implicit Object"]
---

# 내장 객체 개요

JSP에서는 수행문(scriptlet)과 표현식(expression)에서 객체 생성 및 참조 변수 선언, 초기화 과정을 거치지 않고 바로 특정 객체를 사용할 수 있는데, 이러한 객체들을 내장 객체(implicit object)라 한다. 즉, 이미 특정 객체 참조값이 초기화되어 있는 참조 변수가 있어 이를 가져다 쓰기만 하면 되는 편리함을 제공한다. JSP가 서블릿으로 변환될 때 자동으로 선언 및 초기화되는 객체를 의미한다. 

내장 객체로는 다음이 존재한다. 

| 참조변수명 | 자료형(data type) | 비고 |
| --- | --- | --- |
| request | javax.servlet.http.HttpServletRequest | request scope를 가지는 context이기도 하다. |
| response | javax.servlet.http.HttpServletResponse |  |
| pageContext | javax.servlet.jsp.PageContext | 하나의 JSP page의 context를 가진다. 즉, page scope를 가진다. 기존의 servlet에는 없던 개념 |
| session | javax.servlet.http.HttpSession | session scope를 가지는 context이기도 하다.  |
| application | javax.servlet.ServletContext | servlet context이다. application scope를 가진다. |
| out | javax.servlet.jsp.JspWriter | response.getWriter()가 반환하는 PrintWriter 출력 스트림 객체와 동일. `<%= %>` 가 있어 잘 안쓰인다.  |
| exception | java.lang.Throwable | error page에서 사용 가능한 Throwable 객체. page scope를 가진다.  |

여기서 scope는 해당 객체를 사용할 수 있는 범위라 보면 된다. 앞서 [[Servlet] Servlet Context와 정보 공유](/servlet%20&%20jsp/servlet-servlet-context와-정보-공유/) 페이지에서 request, session, servlet context가 각각 어느 정도의 범위까지 사용할 수 있는지에 대해 살펴보았는데, 그 범위를 의미한다. 즉, session은 하나의 브라우저에 대해서만 사용 가능하고, 브라우저 종료 및 세션 만료 이전까지 사용 가능하며, 모든 서블릿에서 사용 가능하다. 이것이 session의 scope가 되는 것이다. 

참고로 JSP 내장 객체인 request, session, application의 데이터 공유 범위도 servlet에서의 것과 동일하다. 

## pageContext

위 내용을 보면, 기존의 servlet에는 없던 page, pageContext란 개념이 나온다. page scope는 말 그대로 하나의 jsp page 내부 영역을 의미하며, 다른 페이지로부터 접근할 수 없다. 이러한 하나의 페이지 내부에 생성된 context가 pageContext이다. context이기에 마찬가지로 Attribute라는 이름이 붙은 메서드를 통해 페이지 영역 내에서 데이터를 저장 및 공유할 수 있다. 

다음은 pageContext를 이용하여 데이터를 해당 context에 저장 및 추출하여 사용해보는 예제이다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<%
		/*
			페이지 영역 내부라고 해서 모든 데이터가 page context 내부에 
			저장되는 건 아니다. 
			pageContext의 attribute란 이름이 붙은 
			메서드를 이용하여 데이터 저장, 검색, 삭제를 할 수 있는 것이다.
		*/
		String name = "kimquel";
	
		pageContext.setAttribute("name", "Jas Park");
	%>
	<p><%=(String)pageContext.getAttribute("name") %></p>
	
	<a href="pageContextTest2.jsp">다른 페이지로 이동하기</a>
</body>
</html>
```

pageContextTest.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<%=(String)pageContext.getAttribute(name)%>
</body>
</html>
```

pageContextTest2.jsp

pageContextTest.jsp를 실행하면 다음의 결과를 얻는다. 

![1.PNG](/images/2024-09-08/jsp-implicit-object/1.png)

![2.PNG](/images/2024-09-08/jsp-implicit-object/2.png)

page scope를 가지기에 다른 페이지에서 해당 데이터에 접근할 수 없음을 위 에러 페이지를 통해 알 수 있다. 

한 편, pageContext 객체에 있는 메서드에는 다음이 존재한다. 

- `void forward(String url)` : 특정 페이지로 forwarding하여 이동.
- `Exception getException()` : 발생한 예외 객체 반환.
- `Object getPage()` : 현재 JSP에 해당하는 객체 반환.
- `ServletRequest getRequest()` : 현재 jsp에서 사용하는 HttpServletRequest 객체 반환.
- `ServletResponse getResponse()` : 현재 jsp에서 사용하는 HttpServletResponse 객체 반환.
- `ServletContext getServletContext()` : 현재 jsp에서의 ServletContext 객체 반환.
- `HttpSession getSession()` : 현재 jsp에서의 HttpSession 객체 반환
- `void include(String url)` : 특정 url의 실행 결과를 현재 jsp에 포함한다.

다음은 include를 이용하여 외부 페이지를 불러오는 예제이다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>메인 페이지입니다.</h1>
	<%
		pageContext.include("pageContextInclude2.jsp");
		String name = "kimquel";
	%>
	<p>
		<span>사이트 소개</span>
		<span> | </span>
		<span>오시는 길</span>
		<span> | </span>
		<span>연락처</span>
	</p>
	<p><%=name %></p>
</body>
</html>
```

pageContextInclude.jsp

```html
<%@page import="java.util.Date"%>
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	String name = "jas Park";
%>

<hr/>

<h1>만나서 반갑습니다. </h1>
<p>오늘은 <%= new Date().toLocaleString() %> 입니다.</p>
<p><%=name %> </p>
<hr/>
```

pageContextInclude2.jsp

pageContextInclude.jsp를 실행하면 다음의 결과를 얻는다.

![1.PNG](/images/2024-09-08/jsp-implicit-object/3.png)

그런데 위 코드와 결과를 보면 한 가지 사실을 알 수 있다. pageContext 객체의 include를 이용하면 두 페이지에서 같은 이름의 변수를 선언해도 전혀 에러가 뜨지 않는다는 것이다. 

이는 기존의 include directive에서는 두 페이지에서 같은 변수명 선언 시 에러가 발생했던 것과 대조되는 결과이다. 

include directive는 두 페이지의 자바 코드를 하나로 합쳐 컴파일하는 원리인 반면, pageContext의 include 메서드 방식은 두 페이지의 자바 코드를 각각 따로 컴파일한 후, 컴파일된 결과가 포함된 HTML 코드를 가져오는 방식이기 때문이다. [[JSP] Directive (지시자)](/servlet%20&%20jsp/jsp-directive/) 페이지의 “include directive가 외부 페이지를 불러오는 원리” 챕터에 있는 그림의 원리를 따른다고 보면 된다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[3] JavaServer Pages™ Specification Version 2.3 Maintenace Release 3 (JSP 공식 문서)

[4] pageContext

[14. pageContext 기본객체](https://m.blog.naver.com/kimkwon429/220759316484)