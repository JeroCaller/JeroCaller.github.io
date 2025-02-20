---
title: "[JSP] Directive (지시자)"
category: "Servlet & JSP"
tag: ["JSP"]
---

# 개요

지시자(directive)는 JSP 파일 내 자바 코드가 서블릿으로 변환될 때 추가 정보를 같이 전달하여 그 정보도 같이 서블릿에 반영되게끔 하고자 할때 사용하는 스크립트 태그이다. 

내 개인적인 생각으로는 마치 HTML의 `<head>` 태그와도 비슷한 것 같다. 해당 태그 내부에는 HTML 페이지에 대한 메타데이터를 작성하고, 그 메타데이터를 토대로 브라우저 탭 제목 등의 설정도 할 수 있듯, 지시자도 jsp 코드가 서블릿으로 변환할 때 추가적으로 알려줄 정보들을 보내 설정을 할 수 있기 때문이다. 

지시자도 스크립트 태그로 분류되나, 다른 스크립트 태그들에 비해 성격이 달라 이렇게 따로 페이지를 만들어 작성한다. 

지시자 형태는 `<%@  %>` 이며, 지시자에는 다음과 같이 3종류가 있다고 한다. 

| 지시자 | 기능 |
| --- | --- |
| `<%@ page %>` | 서블릿 변환 시 추가로 전달할 페이지 정보 설정. |
| `<%@ include %>`  | 다른 JSP 코드를 불러와 해당 위치에 포함시킨다.  |
| `<%@ taglib %>` | XML 기반 태그를 사용할 수 있도록 선언. |

여기서는 page, include에 대해서만 알아보겠다. 

# page directive

page 지시자에 들어갈 수 있는 속성에는 대표적으로 다음이 있다. 

| 속성 | 기능 |
| --- | --- |
| contentType | 응답 페이지의 컨텐트 타입을 설정. “text/html; charset=UTF-8” 의 내용처럼 응답 페이지를 HTML로, 출력될 문자열을 UTF-8로 설정하는 것이 대표적 예 |
| isErrorPage | 현재 페이지를 에러 처리 전용 페이지로 설정할 것인지 여부.  |
| errorPage | 에러 발생 시 사용자에게 보여줄 jsp 응답 페이지 경로를 설정. 이 때의 응답 페이지는 isErrorPage 속성이 true인 페이지여야 한다.  |
| import | jsp 파일 내에 작성한 자바 코드에서 특정 클래스, 패키지를 import할 때 사용. |
| session | 현재 페이지에서 세션 객체를 사용할 것인지에 대한 여부.  |

## isErrorPage, errorPage

다음은 사용자로부터 숫자를 입력받으면 문자열 형태의 숫자를 int형으로 바꿔 이를 화면에 보여주는 예제이다. 아래의 makeError.jsp 파일은 webapp 폴더 바로 밑에 만들었고, simpleErrorPage.jsp는 webapp 폴더 아래 errorPages 폴더를 만들어 그 안에 만들었다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ page errorPage="errorPages/simpleErrorPage.jsp" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form action="makeError.jsp" method="post">
		<label for="number"> <input type="text" name="number" /> </label>
		<button type="submit">입력</button>
	</form>
	
	<%
		if (request.getMethod().equalsIgnoreCase("POST")) {
			
			String strNumber = request.getParameter("number");
			int num = Integer.parseInt(strNumber);
			
	%>
		<p>당신이 입력한 숫자는 : <%=num %></p>
	<%
		}
	%>
	
	
</body>
</html>
```

makeError.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ page isErrorPage="true" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>죄송합니다. 현재 고객님의 요청을 처리할 수 없습니다. </h1>
	
	<hr/>
	
	<h2>에러 보고</h2>
	<p> 에러명: <%=exception.getClass().getName() %> </p>
	<% StackTraceElement[] stElements = exception.getStackTrace(); %>
	<p> 에러 상세 정보 </p>
	<ul>
	<%
		for (StackTraceElement ele : stElements) {
	%>
		<li><%= ele.toString() %></li>
	<%
		}
	%>
	</ul>
</body>
</html>
```

simpleErrorPage.jsp

makeError.jsp를 서버에서 실행시키고, 숫자가 아닌 문자를 입력해보자.

![스크린샷 2024-09-05 193307.png](/images/2024-09-05/jsp-directive/1.png)

![스크린샷 2024-09-05 193318.png](/images/2024-09-05/jsp-directive/2.png)

그러면 위와 같이 makeError.jsp 파일에서 요청 처리 중에 에러가 발생하면 simpleErrorPage.jsp 에서 제공하는 페이지로 이동함으로써 사용자에게 보여줄 페이지를 따로 설정해줄 수 있다. 

위 예제에서는 사실 에러 메시지가 사용자 화면에 보여지도록 해서는 안된다. 에러 메시지가 상세해서 보안성에도 좋지 않고, 사용자 입장에서도 썩 유쾌하지 않은 에러 메시지이기 때문이다. 여기서는 예외 객체 exception을 간단하게 다뤄보기 위해 사용하였다. 

위 코드를 보면, makeError.jsp에서는 요청 처리 중 에러가 날 것을 우려해 두 번째 줄에 페이지 지시자와 그 안에 errorPage 속성을 이용하여 에러 발생 시 에러 메시지 대신 사용자에게 보여줄 페이지로 이동되게끔 하고 있는 것이다. 

한 편, simpleErrorPage.jsp의 두 번째 줄에서도 에러와 관련된 페이지 지시자를 사용하고 있다. 해당 페이지에서 직접 에러를 처리할 것이기에 `isErrorPage="true"` 라고 설정하였다. 해당 속성값에 true로 지정하면 해당 페이지에서 exception이라는 객체를 다룰 수 있게 된다. 이 때 exception은 jsp에서 발생한 예외 객체를 참조하는 참조 변수로, 자동으로 생성되기에 개발자가 따로 예외 객체 참조 변수를 선언 및 초기화할 필요없이 바로 가져다 쓰면 되는 것이다. 

위 사진에서 볼 수 있는 점은, 브라우저의 URL 창에 있는 URL을 보면 페이지 이동 전과 후의 URL이 바뀌지 않은 것을 볼 수 있다. 즉, 페이지 지시자의 errorPage 및 isErrorPage 속성을 사용한 페이지 이동 방식은 forwarding 방식임을 알 수 있다. 

위에서 언급한 것들을 정리하면 다음과 같다.

- 페이지 지시자의 errorPage를 이용하면 에러 발생 시 사용자에게 대신 보여줄 페이지로 이동시킬 수 있다. 이렇게 에러 메시지 유출을 방지하여 보안성과 사용자 경험을 향상시킬 수 있다.
- isErrorPage를 true로 설정한 페이지 내에서 예외 객체인 exception을 사용할 수 있게 된다. 반대로 말하면 해당 속성값이 false이거나 아예 설정되지 않으면 exception을 사용할 수 없다.
- 페이지 지시자를 이용하여 에러 페이지로 이동시키는 방식은 forwarding 방식을 이용한다.

## import

import 속성은 jsp 내 자바 코드 내에서 다른 클래스 및 패키지를 import하여 사용해야할 때 쓸 수 있는 속성이다. 다음은 이를 활용한 예제이다. 

```html
<%@page import="java.util.List"%>
<%@page import="java.util.ArrayList"%>
<%@page import="java.util.Date, java.util.Calendar" %>
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<%
		List<String> arr = new ArrayList();
		arr.add("hi");
	%>
	<p> 현재 array list의 사이즈는 <%=arr.size() %>개 입니다. </p>
	<p>현재 날짜는 <%=new Date().toLocaleString() %> 입니다. </p>
</body>
</html>
```

useImport.jsp

![스크린샷 2024-09-05 195643.png](/images/2024-09-05/jsp-directive/3.png)

위 코드의 1~3번째 코드가 import 속성을 사용한 것이다. 이렇게 사용하면 자바 코드 내에서 해당 패키지 또는 클래스를 import하여 사용할 수 있게 된다. 

만약 둘 이상의 클래스들을 하나의 import 속성값에 넣고자 한다면 위 코드의 3번째 코드처럼 쉼표(,)를 구분자로 하여 구분해주면 된다. 

## session

JSP에서도 서블릿의 HttpSession을 사용할 수 있다. 이 때, 페이지 지시자의 속성으로 session=”true”를 사용하면 session 객체가 자동으로 session이라는 참조 변수에 할당되어 바로 사용할 수 있게 된다. 

사실 session의 기본값은 true이다. 

다음은 session을 이용하는 예제이다.

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ page session="true" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>당신의 세션 아이디</h1>
	<%= session.getId() %>
</body>
</html>
```

jspSession.jsp

![1.PNG](/images/2024-09-05/jsp-directive/4.png)

만약 페이지 지시자의 session 속성값을 false로 지정하면 서블릿에서처럼 일일이 request.getSession()을 통해 session 객체를 반환받아야 한다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ page session="false" %>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>당신의 세션 아이디</h1>
	
	<% HttpSession session = request.getSession(); %>
	<%= session.getId() %>
</body>
</html>
```

jspSession.jsp

# Include directive

`<%@ include %>` 의 형태를 지니는 include 지시자는 외부 페이지를 불러올 때 사용한다. 이러한 용도로만 사용되어서 해당 지시자의 속성은 오로지 file 하나 뿐이다. 이 속성에 불러올 파일 경로를 입력하면 된다. 

이 지시자를 이용하면, 웹 페이지에서 자주 반복되어 나오는 일부 패턴들을 따로 파일로 분리한 다음, 필요할 때마다 가져와서 사용할 수 있게 된다. 예를 들면, 하나의 사이트 내에서 웹 페이지의 header와 footer 부분의 내용과 형식이 반복적으로 나올 수도 있다. 이럴 때 따로 header와 footer만 따로 파일로 분리한 다음, 필요할 때마다 include 지시자를 이용하여 가져오면 코드량도 줄일 수 있고, 재사용성도 확보할 수 있다. 

다음은 간단하게 웹 페이지의 헤더와 푸터를 각각 구성할 페이지들이다. 우선 inc 폴더를 만든 후, 각각 header.jsp와 footer.jsp으로 만들었다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>

<h1>Welcome to My Site!</h1>

<span>Main Menu</span> <span>|</span>
<span>사이트 소개</span> <span>|</span>
<span>상품 목록</span> <span>|</span>

<hr/>
```

header.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>

<hr/>

<span>회사 소개</span> <span>|</span>
<span>오시는 길</span> <span>|</span>
<span>연락처</span> <span>|</span>
```

footer.jsp

위 파일들을 각각 실행해보면 다음의 화면이 나온다. 

![스크린샷 2024-09-06 093044.png](/images/2024-09-05/jsp-directive/5.png)

![스크린샷 2024-09-06 093106.png](/images/2024-09-05/jsp-directive/6.png)

그리고 이 두 파일을 불러와 사용할 파일 includeDirective.jsp를 다음과 같이 만들었다.

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<%@ include file="inc/header.jsp" %>
	
	<h2>여기는 메인 페이지입니다.</h2>
	
	<%@ include file="inc/footer.jsp" %>
</body>
</html>
```

includeDirective.jsp

위 파일을 실행하면 다음의 결과를 얻는다. 

![스크린샷 2024-09-06 093213.png](/images/2024-09-05/jsp-directive/7.png)

이와 같이 외부 파일을 불러와 페이지를 구성할 수도 있는 것이다. 

## include directive가 외부 페이지를 불러오는 원리

include directive가 외부 페이지, 특히 자바 코드가 포함되어 있는 다른 jsp 페이지를 불러오는 원리는 다음과 같다.

1. 둘 이상의 JSP 파일에 있는 자바 소스 코드들을 하나의 소스 코드로 합친다. 즉, 하나의 서블릿 클래스 내부의 코드로 합쳐진다. 
2. 합쳐진 하나의 자바 소스 코드가 컴파일되어 그 결과가 HTML에 삽입된다. 

![include 지시자와 include 액션 태그의 외부 페이지를 불러오는 원리를 그림으로 그려보았다. ](/images/2024-09-05/jsp-directive/8.png)

include 지시자와 include 액션 태그의 외부 페이지를 불러오는 원리를 그림으로 그려보았다. 

위 사진의 왼쪽 영역이 include 지시자가 하나 이상의 외부 페이지를 불러오는 원리이다. 오른쪽 영역은 나중에 다룰 include 액션 태그라는 것으로, 해당 태그도 외부 페이지를 불러오는 역할을 하나, 그 원리가 다르다. 이는 나중에 해당 액션 태그를 다룰 때 살펴보겠다. 

위 원리대로 작동하는지 어떻게 증명할 수 있을까? 

두 개의 jsp 페이지를 준비하고, 각 파일 내 자바 코드로 같은 이름의 변수를 선언한다. 그리고 하나의 페이지에서 다른 페이지를 include 지시자를 이용하여 불러와보자. 만약 앞서 언급한 것처럼 하나의 서블릿으로 합치는 방식이라면 분명 에러가 날 것이다. 이를 직접 확인해보자. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<%@ include file="includeTwo.jsp" %>
	<%
		int num = 0;
	%>
	
	<%= num %>
</body>
</html>
```

includeOne.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	int num = 1000;
%>
```

includeTwo.jsp

여기서 includeOne.jsp를 실행하면 다음의 결과를 얻는다. 

![1.PNG](/images/2024-09-05/jsp-directive/9.png)

똑같은 변수를 다시 선언한 것으로 인식하기에 위와 같은 에러가 발생한 것이다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[3] [ServletResponse (Java(TM) EE 8 Specification APIs)](https://javaee.github.io/javaee-spec/javadocs/javax/servlet/ServletResponse.html#setContentType-java.lang.String-)

[4] JavaSever Pages Specification Version2.3 Maintenace Release3