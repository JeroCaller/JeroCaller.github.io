---
title: "[JSP] Standard Action tags"
category: "Servlet & JSP"
tag: ["JSP", "Action Tag"]
---

# Action tag 개요

여태까지 살펴본 JSP는 자바 코드 내에서 HTML 응답 페이지를 생성하는 servlet의 불편한 방식을 개선한 기술로, servlet과 반대로 HTML 페이지 내부에 자바 코드를 삽입하는 방식을 사용하여 HTML 응답 페이지를 요청에 따라 동적으로 생성해줌으로써 좀 더 편하게 응답 페이지를 생성할 수 있음을 살펴보았다. 

그런데, 여기까지 살펴본 JSP에서도 불편한 점이 눈에 띈다. 지금까지는 스크립트 태그를 이용하여 HTML 코드 내부에 자바 코드를 삽입하여 사용하는 방식을 취했다. 그런데, 애초에 HTML과 자바 코드를 분리할 수는 없는 것일까? HTML 코드와 자바 코드가 하나의 페이지 안에 섞이니 가독성도 떨어지고, 유지보수 관점에서도 별로 좋지 못할 것이다. 응답 페이지의 디자인만 수정하려다가 실수로 자바 코드를 건드려 더 이상 제대로 제 기능이 작동하지 못할 수도 있고, 반대로 자바 코드만 수정하여 응답 로직만 바꾸려다가 HTML 코드를 잘못 건드려 디자인이 이상해질 수도 있는 것이다. 

이러한 이유로, 자바 코드와 HTML을 서로 다른 파일로 분리하여 작성한 뒤, 이를 하나의 jsp 페이지로 합치는 방식이 고려되었다. 자바 코드를 불러올 때도 아예 태그 방식으로 불러와 HTML 코드와의 이질감이 생기지 않도록 하는 방법도 고려되었다. 이로 인해 탄생한 것이 action tag이다. 

참고로, 사용자에게 보여주기 위한 화면 디자인의 로직을 presentation logic이라 하고, 데이터 처리 등 어떤 작업을 처리하거나 특정 기능을 구현하기 위한 로직을 business logic이라 한다. 어떻게 보면 presentattion logic은 HTML와 같은 프론트엔드, business logic은 자바와 같은 백엔드 쪽이라 봐도 될 것이다. 

action tag는 자바 코드로 수행할 수 있는 작업(action)을 태그 형태로 정의한 것으로, 이로 인해 HTML 코드에 자연스럽게 포함되어 가독성을 확보하면서도 동시에 여전히 사용자 요청을 동적으로 처리한 결과를 응답 페이지에 포함시키는 기능도 유지할 수 있다. 

action tag는 다음과 같이 분류할 수 있다.

- Standard action tag (표준 액션 태그) : JSP 내장 태그이다. `<jsp: ...>` 의 형태를 지닌다. 따라서 커스텀 액션 태그를 만들 때 `<jsp: ...>` 형태를 사용해선 안된다.
- Custom Action tag (사용자 정의 태그) : 개발자가 만들 수 있는 태그.
- JSTL : 다른 사람들이 만들어 놓은 액션 태그들을 모아놓은 일종의 라이브러리 개념. 즉, 사용자 정의 액션 태그들을 모아놓은 라이브러리이며, 이후에는 내장 태그는 아니더라도 표준이 되었다.

# Standard action tag (표준 액션 태그)

이 페이지에서는 표준 액션 태그에 대해 살펴보겠다. 

표준 액션 태그에도 종류가 많지만 여기서는 대표적인 태그 몇 가지만 살펴보겠다. 

- java beans를 사용하는 액션 태그
    - `<jsp:useBean>`
    - `<jsp:setProperty>`
    - `<jsp:getProperty>`

- 그 외 표준 액션 태그
    - `<jsp:include>`
    - `<jsp:forward>`
    - `<jsp:param>`

## include 표준 액션 태그

`<jsp: include page="jsp_file.jsp"/>` 형태를 띠는 해당 태그는 page 속성값으로 지정한 외부 페이지를 불러와 현재 페이지에 포함시키는 태그이다. 이전에 배운 `<%@ include %>` 지시자나 `pageContext.include()` 와 동일하다. 다만, include 지시자와는 외부 페이지를 불러오는 방식이 다르다. 

우선 include 표준 액션 태그를 사용하여 외부 페이지를 불러와 현재 페이지에 포함시키는 예제를 살펴보자. 

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
		int myNum = 0;
	%>
	
	<jsp:include page="inc/header.jsp"></jsp:include>
	<h2>여기는 본문 영역입니다.</h2>
	<p>My number: <%= myNum %></p>
	<jsp:include page="inc/footer.jsp"></jsp:include>
</body>
</html>
```

simpleMain.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	int myNum = 10;
%>

<h1>WELCOME TO MY PAGE</h1>
<p>My number: <%= myNum %></p>
<hr/>

```

inc/header.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	int myNum = 100;
%>

<hr/>
<span>사이트맵</span>
<span> | </span>
<span>오시는길</span>
<span> | </span>
<span>그외 정보</span>
<span> | </span>
<span>My number: <%=myNum %></span>
```

inc/footer.jsp

simpleMain.jsp를 실행하면 다음의 화면이 뜬다. 

![1.PNG](/images/2024-09-08/jsp-standard-action-tags/1.png)

include 표준 액션 태그를 이용하여 외부 페이지를 불러와 하나의 페이지로 합칠 수 있음을 알 수 있다. 

include 표준 액션 태그는 여러 jsp 페이지들이 있을 때, 각각의 페이지 내부에 있는 자바 코드를 각자 컴파일한 후, 컴파일 결과가 다시 HTML 코드에 삽입된다. 이 후, include 표준 액션 태그를 통해 외부 페이지, 즉 이미 컴파일된 결과가 삽입된 HTML 페이지를 불러오는 방식인 것이다. 그래서 위 코드에서 각 페이지마다 `myNum` 이라는 똑같은 변수명이 선언 및 초기화되었음에도 변수 중복 선언으로 인식되지 않아 에러 없이 잘 실행된 것이다. 

![include directive와 include  표준 액션 태그의 처리 방식의 차이를 그림으로 그려보았다. ](/images/2024-09-08/jsp-standard-action-tags/2.png)

include directive와 include  표준 액션 태그의 처리 방식의 차이를 그림으로 그려보았다. 

## forward 표준 액션 태그

`<jsp:forward page="url"/>` 형태의 forward 표준 액션 태그는 다른 페이지로 forwarding 방식으로 이동하고자 할 때 사용되는 액션 태그이다. 

다음은 해당 액션 태그를 실험해보는 예제이다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	ul {
		list-style: none;
		padding: 1em;
	}
	li {
		margin: 1em;
	}
</style>
</head>
<body>
	<form action="forwarding.jsp" method="post">
		<ul>
			<li>
				<label for="nickname">닉네임: </label>
				<input type="text" name="nickname" />
			</li>
			<li>
				<label for="say">하고 싶은 말 한 줄: </label>
				<input type="text" name="say" />
			</li>
		</ul>
		<button type="submit">제출</button>
	</form>
	
	<%
		if (request.getMethod().equalsIgnoreCase("POST")) {
	%>
		<jsp:forward page="forwarded.jsp"></jsp:forward>
	<%
		}
	%>
</body>
</html>
```

forwarding.jsp

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
		request.setCharacterEncoding("utf-8");
		String userName = request.getParameter("nickname");
		if (userName == null) {
			userName = "anonymous";
		}
		
		String say = request.getParameter("say");
		if (say == null) {
			say = "(해당 유저가 할 말을 입력하지 않았습니다.)";
		}
	%>
	<h1>입력 결과</h1>
	<hr/>
	<p>닉네임: <%= userName %> </p>
	<p>하고 싶은 말 </p>
	<p><%=say %></p>
	
</body>
</html>
```

forwarded.jsp

forwarding.jsp를 실행하면 다음의 결과를 얻는다. 

![2.PNG](/images/2024-09-08/jsp-standard-action-tags/3.png)

![3.PNG](/images/2024-09-08/jsp-standard-action-tags/4.png)

forward 액션 태그를 사용하면 개발자가 요청 데이터를 forwarding할 페이지에 전송할 코드를 작성하지 않아도 자동으로 다음 페이지로 요청 데이터가 전송된다는 것을 알 수 있다. 

## param 표준 액션 태그

`<jsp:param name="name" value="value"/>` 의 형태를 띠는 param 표준 액션 태그는 주로 앞서 본 include, forward 표준 액션 태그의 자식 요소로 쓰인다. 해당 태그는 name-value 쌍 데이터를 각각 name과 value 속성에 작성하여 request 객체에 포함시켜 전송할 때 쓰인다. 기존의 form 태그를 통해 사용자가 입력한 데이터 외에도 추가로 다른 페이지에 전송할 데이터가 있을 때 이 액션 태그를 사용하면 된다. 

다음은 앞서 살펴본 `jsp:forward` 액션 태그의 자식 요소로 `jsp:param` 를 사용하여 forwarding 시 개발자가 요청에 추가 데이터를 실어 전송하는 예제이다. 앞서 사용했던 forwarding.jsp, forwarded.jsp 파일을 수정하여 만들었다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<style>
	ul {
		list-style: none;
		padding: 1em;
	}
	li {
		margin: 1em;
	}
</style>
</head>
<body>
	<form action="forwarding.jsp" method="post">
		<ul>
			<li>
				<label for="nickname">닉네임: </label>
				<input type="text" name="nickname" />
			</li>
			<li>
				<label for="say">하고 싶은 말 한 줄: </label>
				<input type="text" name="say" />
			</li>
		</ul>
		<button type="submit">제출</button>
	</form>
	
	<%
		if (request.getMethod().equalsIgnoreCase("POST")) {
			request.setCharacterEncoding("utf-8");
	%>
		<jsp:forward page="forwarded.jsp">
			<jsp:param value="배울 게 너무 많네요 ㅠㅠ" name="devSay"/>
		</jsp:forward>
	<%
		}
	%>
</body>
</html>
```

forwarding.jsp

```html
<%@page import="java.util.Enumeration"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<%!
		public String getParameterWithoutNull(HttpServletRequest req, String paramName, String replaceNull) {
			String strValue = req.getParameter(paramName);
			if (strValue == null) {
				strValue = replaceNull;
			}
			return strValue;
		}
	%>
	<%
		String userName = getParameterWithoutNull(request, "nickname", "anonymous");
		String say = getParameterWithoutNull(request, "say", "(해당 유저가 할 말을 입력하지 않았습니다.)");
		String devSay = getParameterWithoutNull(request, "devSay", "(개발자가 할 말이 없나봅니다.)");
	%>
	<h1>입력 결과</h1>
	<hr/>
	<p>닉네임: <%= userName %> </p>
	<p>하고 싶은 말 </p>
	<p><%=say %></p>
	<hr/>
	<p>개발자의 말 </p>
	<p><%=devSay %> </p>
	
	<hr/>
	
	<p>모든 파라미터 name-value 값 출력</p>
	<ul>
	<%
		Enumeration<String> names = request.getParameterNames();
		while(names.hasMoreElements()) {
			String name = names.nextElement();
			String value = request.getParameter(name);
	%>
		<li><%=name %> : <%=value %></li>
	<% 
		}
	%>
	</ul>
	
</body>
</html>
```

forwarded.jsp

![4.PNG](/images/2024-09-08/jsp-standard-action-tags/5.png)

위와 같이 forwarding 시 전송될 요청에 실을 데이터를 추가하여 전송할 수 있다. 

이번에는 jsp:include에 적용해보겠다. 앞서 include 표준 액션 태그에서 살펴본 simpleMain.jsp, inc/header.jsp, inc/footer.jsp 파일을 다음과 같이 수정하였다. 

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
		int myNum = 0;
		
		request.setCharacterEncoding("utf-8");
	%>
	
	<jsp:include page="inc/header.jsp">
		<jsp:param value="반갑구리~" name="headerSay"/>
	</jsp:include>
	<h2>여기는 본문 영역입니다.</h2>
	<p>My number: <%= myNum %></p>
	<jsp:include page="inc/footer.jsp">
		<jsp:param value="바닥글이다구리~" name="footerSay"/>
	</jsp:include>
</body>
</html>
```

simpleMain.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	int myNum = 10;
%>

<h1>WELCOME TO MY PAGE</h1>
<p>My number: <%= myNum %></p>
<p><%= request.getParameter("headerSay") %></p>
<hr/>

```

inc/header.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>

<%
	int myNum = 100;
%>

<hr/>
<span>사이트맵</span>
<span> | </span>
<span>오시는길</span>
<span> | </span>
<span>그외 정보</span>
<span> | </span>
<span>My number: <%=myNum %></span>

<p><%= request.getParameter("footerSay") %></p>
```

inc/footer.jsp

simpleMain.jsp를 실행하면 다음의 화면이 뜬다. 

![5.PNG](/images/2024-09-08/jsp-standard-action-tags/6.png)

include 액션 태그를 사용하는 쪽이 include 액션 태그의 page 속성값에 명시되어 불려올 페이지에 대해 “요청”을 하는 것과 같다. 따라서 simpleMain.jsp 에서 jsp:include 요소의 자식 요소로 jsp:param을 이용하여 추가 데이터를 요청에 실어 전송하는 방식인 것이다. 

java beans를 사용하는 `<jsp:useBean />` , `<jsp:setProperty />`  , `<jsp:getProperty />`  액션 태그는 다른 페이지에서 다루겠다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] JavaSever Pages Specification Version2.3 Maintenace Release3

[3] presentation logic VS business logic

[프리젠테이션 로직? 비즈니스 로직?](https://blog.naver.com/semi7623/100005637337)

[4] action tags

[JSP - Action Tags - GeeksforGeeks](https://www.geeksforgeeks.org/jsp-action-tags/)

[5] 표준 액션 태그

[[JSP] 액션 태그(Action Tag)](https://velog.io/@imok-_/JSP-액션-태그Action-Tag)