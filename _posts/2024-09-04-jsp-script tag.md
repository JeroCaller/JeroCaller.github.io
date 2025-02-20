---
title: "[JSP] Script tag"
category: "Servlet & JSP"
tag: ["JSP"]
---

# 개요

스크립트 기반 태그, 또는 이 태그 전체를 스크립트 요소(script element)라고도 한다. HTML의 태그와 비슷하게 생겼다. 

JSP에서 제공하는 스크립트 태그에는 다음과 같이 존재한다. 

| 스크립트 태그명 | 형태 | 뜻 |
| --- | --- | --- |
| 주석문 (comment) | `<%-- 주석입니다 --%>` | JSP 주석. 주석 내 코드는 서블릿으로의 변환을 방지한다.  |
| 지시자(directive) | `<%@  %>` | 서블릿 변환 과정에서 전달할 페이지 정보를 설정.  |
| 수행문 (scriptlet) | `<% %>` | 자바 코드 작성 |
| 표현식 (expression) | `<%= 보여줄 값 %>` | 브라우저에 출력할 값을 기입한다. 서블릿에서의 `PrintWriter out resp.getWriter();`    에서 `out.println(보여줄 값)` 과 동일하다.  |
| 선언문(declaration) | `<%! %>` | 선언문 내에 작성된 자바 코드는 서블릿 클래스의 멤버 변수 및 메서드로 선언된다.  |

이 중에서 사실 본격적으로 HTML 내부에 자바 코드를 작성하게 할 수 있는 스크립트 태그는 scriptlet, expression, declaration이고, 지시자는 자바 코드를 작성하는 것이 아니라 주로 페이지 관련 정보를 설정할 때 쓰이는 개념이라 실제로 써보면 이 둘이 서로 다른 느낌이라는 것을 알 수 있을 것이다. 그래서 여기서는 지시자는 따로 분리하여 설명하겠다. 

# 주석문 (comment)

`<%-- --%>` 안에 들어갈 내용은 주석 처리된다. JSP에서의 주석과 마찬가지이다. 이 안에 다른 스크립트 태그를 넣어도 실행되지 않고 주석 처리된다. 다음의 코드를 보면 알 수 있다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JSP 입니다.</title>
</head>
<body>
	<%-- 
		hello, jsp! 
		다음의 코드가 실행될까요?
		<% String say = "hello"%>
		<%= say %>
	--%>
	<%
		request.setCharacterEncoding("UTF-8");
		response.setCharacterEncoding("euc-kr");
		if (request.getMethod().equalsIgnoreCase("GET")) {
	%>
		<form action="jspBasic1.jsp" method="post">	
			유저 네임: <input type="text" name="user-name" />
			<input type="submit" value="로그인" />
		</form>
	<%
		} else {
	%>
		<p> <%= request.getParameter("user-name") %> 님, 환영합니다. </p>
	<%
		}
	%>
</body>
</html>
```

jspBasic1.jsp

![주석 내부에 있던 표현식이 주석처리되는 바람에 실행이 되지 않아 브라우저 화면에 출력되지 않는다.](/images/2024-09-04/jsp-script%20tag/1.png)

주석 내부에 있던 표현식이 주석처리되는 바람에 실행이 되지 않아 브라우저 화면에 출력되지 않는다.

# 수행문 (Scriptlet)

`<% %>` 의 형태를 띠는 수행문은 사실상 본격적인 자바 코드를 작성하는 영역이라 볼 수 있다. 사용자의 요청을 동적 처리할 자바 코드를 작성하는 곳이기에 아무래도 다른 스크립트 태그에 비해 자주 쓰일 수밖에 없다. 실제로 해당 태그 내부에 작성된 자바 코드들은 서블릿으로 변환 시 service() 메서드 내부 코드로 변환된다.  이전에 서블릿에서 살펴봤듯, service() 메서드는 사용자 요청 방식에 따라 doGet, doPost 등의 메서드를 호출하는 메서드로, 사실상 사용자 요청을 처리하는 메서드라고 볼 수 있다. 

앞선 jspBasic1.jsp 파일 내용을 자세히 보면, 수행문 태그가 여러 개로 나뉘어 있는 것을 볼 수 있다. 이는 중간에 HTML 요소가 위치해야하기 때문이다. 스크립트 태그 내에 HTML 태그를 위치시킬 수 없고, 이는 서블릿 메서드 내부에 직접적으로 HTML 태그를 사용할 수 없기 때문이다. (앞서 서블릿을 살펴볼 때 응답 페이지 구성을 위해 html 태그를 out.println() 메서드에 문자열로 감싸 작성해야했음을 기억하면 이해가 갈 것이다)

이렇게 수행문 태그를 여러 개로 찢어놔도 결국에는 하나의 service() 메서드 내부 코드로 변환된다. (그리고 HTML 요소들은 모두 out.println() 메서드의 인자로 포장될 것이다. 앞선 페이지 [[JSP] JSP 개요](/servlet%20&%20jsp/jsp-jsp-개요/) 의 jspBasic1_jsp.java 코드를 보면 이해될 것이다)

수행문 태그 내에 작성한 자바 코드에 대해, 수행문을 지워버리면 화면에서는 자바 코드가 그대로 유출된다. 수행문 태그를 깜빡하고 빼먹을 일은 없겠지만, 그래도 혹시나 누락하면 사용자 화면에 자바 코드가 그대로 유출되는 것이니 사용자 입장에서도 당황스러울 것이고, 보안상으로도 안 좋을 것이다.

다음은 간단한 수행문이 작성된 jsp 코드이다.

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
		String say = "hi";
		out.println(say);
	%>
</body>
</html>
```

scriptetWithComment.jsp

![1.PNG](/images/2024-09-04/jsp-script%20tag/2.png)

이제 다음과 같이 수행문 태그만 삭제해보자.

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	String say = "hi";
	out.println("say");
</body>
</html>
```

scriptetWithComment.jsp (2)

![2.PNG](/images/2024-09-04/jsp-script%20tag/3.png)

자바 소스 코드가 그대로 유출되어버렸다!

> JSP에서는 수행문 안에 `out.println()` 을 잘 사용하지 않는다고 한다. 다음에 소개할 표현식 스크립트 태그가 이를 대체하기도 하고, 애초에 `out.println()` 은 서블릿에서 자주 쓰이는 코드인데, 서블릿의 문제점을 보완하려고 만든 JSP에서 굳이 서블릿 기술을 그대로 쓸 필요가 없기 때문이다.
> 

## 수행문과 return

다음은 브라우저 화면에서 사용자로부터 숫자를 하나 입력받으면 이를 응답 페이지로 출력해주는 예제이다.

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<form action="scriptletReturn.jsp" method="post">
		<label for="number">숫자 입력: </label>
		<input type="text" name="number" />
		<button type="submit">제출</button>
	</form>
</body>
</html>
```

scriptletReturn.html

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
		int num = 0;
		boolean isNotNumError = false;
		try {
			num = Integer.parseInt(request.getParameter("number"));
		} catch (NumberFormatException e) {
			isNotNumError = true;
		}
	%>
	<%
		if (isNotNumError) {
	%>
		<p>오류! 아무것도 입력하지 않았거나 숫자가 아닌 문자를 입력하셨습니다.</p>
	<%
		}
	%>
	
	<p>당신이 입력한 숫자는 <%=num %> 입니다. </p>
</body>
</html>
```

scriptletReturn.html

여기서 scriptletReturn.html을 서버에서 실행하고 다음과 같이 입력해보자.

![2.PNG](/images/2024-09-04/jsp-script%20tag/4.png)

![3.PNG](/images/2024-09-04/jsp-script%20tag/5.png)

분명 숫자가 아닌 문자를 입력했기에 응답 페이지에서는 오류 메시지만 나와야 한다. 그런데 위 결과물을 보면 “당신이 입력한 숫자는 0입니다.”라는 엉뚱한 메시지도 같이 나왔다. 이 메시지는 오로지 사용자가 숫자를 입력했을 때에만 출력할 메시지이다. 그러면 숫자가 아닌 문자를 입력받으면 해당 메시지가 출력되지 않게 하려면 어떻게 해야할까? 간단하다. 앞선 jsp 파일 내에 다음과 같이 return문 하나만 끼워넣어주면 된다. 

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
		int num = 0;
		boolean isNotNumError = false;
		try {
			num = Integer.parseInt(request.getParameter("number"));
		} catch (NumberFormatException e) {
			isNotNumError = true;
		}
	%>
	<%
		if (isNotNumError) {
	%>
		<p>오류! 아무것도 입력하지 않았거나 숫자가 아닌 문자를 입력하셨습니다.</p>
	
	<!-- return문 삽입. -->
	<%
		return;} 
	%>
	
	<p>당신이 입력한 숫자는 <%=num %> 입니다. </p>
</body>
</html>
```

scriptletReturn.jsp (1)

이제 다시 HTML 화면에서 숫자가 아닌 문자를 입력하면 다음의 응답 페이지가 출력된다.

![4.PNG](/images/2024-09-04/jsp-script%20tag/6.png)

엉뚱한 메시지가 나오지 않는 것을 확인할 수 있다. 수행문 코드는 서블릿으로 변환 시 모두 service()라는 메서드 내부 코드로 삽입된다는 사실만 기억하면 수행문 안에 return문을 사용하는 것이 전혀 이상한 일이 아니란 것을 알 수 있다. 실제로 서블릿으로 변환된 코드를 보면 다음과 같이 구성되어 있다. 

```java
public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
      throws java.io.IOException, javax.servlet.ServletException {
      // 생략
      try {
      response.setContentType("text/html; charset=UTF-8");
      pageContext = _jspxFactory.getPageContext(this, request, response,
      			null, true, 8192, true);
      _jspx_page_context = pageContext;
      application = pageContext.getServletContext();
      config = pageContext.getServletConfig();
      session = pageContext.getSession();
      out = pageContext.getOut();
      _jspx_out = out;

      out.write("\r\n");
      out.write("<!DOCTYPE html>\r\n");
      out.write("<html>\r\n");
      out.write("<head>\r\n");
      out.write("<meta charset=\"UTF-8\">\r\n");
      out.write("<title>Insert title here</title>\r\n");
      out.write("</head>\r\n");
      out.write("<body>\r\n");
      out.write("	");

		int num = 0;
		boolean isNotNumError = false;
		try {
			num = Integer.parseInt(request.getParameter("number"));
		} catch (NumberFormatException e) {
			isNotNumError = true;
		}
	
      out.write('\r');
      out.write('\n');
      out.write('	');

		if (isNotNumError) {
	
      out.write("\r\n");
      out.write("		<p>오류! 아무것도 입력하지 않았거나 숫자가 아닌 문자를 입력하셨습니다.</p>\r\n");
      out.write("	\r\n");
      out.write("	<!-- return문 삽입. -->\r\n");
      out.write("	");

		return;} 
	
      out.write("\r\n");
      out.write("	\r\n");
      out.write("	<p>당신이 입력한 숫자는 ");
      out.print(num );
      out.write(" 입니다. </p>\r\n");
      out.write("</body>\r\n");
      out.write("</html>");
    } catch (java.lang.Throwable t) {
      if (!(t instanceof javax.servlet.jsp.SkipPageException)){
        out = _jspx_out;
        if (out != null && out.getBufferSize() != 0)
          try {
            if (response.isCommitted()) {
              out.flush();
            } else {
              out.clearBuffer();
            }
          } catch (java.io.IOException e) {}
        if (_jspx_page_context != null) _jspx_page_context.handlePageException(t);
        else throw new ServletException(t);
      }
    } finally {
      _jspxFactory.releasePageContext(_jspx_page_context);
    }
  }
}
```

scriptReturn_jsp.java

if문을 만족하면 return문을 만나 결국 service 메서드를 빠져나가게 되어 그 밑의 코드들은 실행되지 않는다. 그래서 문자 입력 시에도 엉뚱한 결과가 나오지 않게 된 것이다. 

그러나 서블릿 코드로 변환된 위 코드를 자세히 보면 알겠지만, 되도록 return문은 쓰지 않는 것이 좋겠다. 우선 PrintWriter라는 입출력 스트림 객체인 out은 쓰고 나면 반드시 close()로 닫아줘야 하는데, return문 때문에 실행되지 않을 것이다. 또한 위 코드의 finally를 보면 어떤 작업을 하는 코드인지는 모르겠으나 어쨌건 finally를 썼다는 건 실행 도중 예외가 발생하건 아니건 service 메서드 실행 후에 리소스 반환 등 어떠한 사후 처리 작업을 하고 끝내야 한다는 것은 유추할 수 있을 것이다. 그런데 중간에 return문을 만나게 되면 이러한 작업조차 못한 채 끝내게 되므로 시스템 상 어떠한 문제가 발생할지도 모를 일이다. 그러니 가급적이면 수행문 코드 내에서 return문은 쓰지 않는 게 좋을 것 같다. 위 예제의 경우, 코드를 조금이라도 덜 쓰려고 return문을 쓴 것이지만, 방금 말한 사유로 인해 return 대신 else 문을 사용하는 것이 좋겠다. 

# 표현식(expression)

`<%= %>` 형식을 가지는 표현식 태그는 그 내부에 실제 값을 넣으면 브라우저에 그대로 출력된다. 서블릿에서 `out.println` 의 역할을 하는 것이다. 실제값이 들어있는 변수나 반환값이 존재하는 메서드 호출 코드를 표현식 안에 작성하는 편이다. 

앞선 jspBasic1.jsp 코드의 `<p> <%= request.getParameter("user-name") %> 님, 환영합니다. </p>` 가 대표적인 예이다. 

다음은 수행문과 표현식을 이용하여 사용자로부터 정수 하나를 입력받게 한 뒤, 1부터 해당 정수까지의 총합을 구하여 이를 응답 페이지로 보여주는 예제이다.

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<% if (request.getMethod().equalsIgnoreCase("GET")) { %>
	<h1>1부터 n까지의 총합 구하기. </h1>
	<form action="expression.jsp" method="post">
		<label for="num">n에 들어갈 정수 입력: </label>
		<input type="text" name="num" /><br>

		<button type="submit">합계 구하기</button>
	</form>
	<% } else {
		int total = 0;
		int n = Integer.parseInt(request.getParameter("num"));
		for (int i = 1; i <= n; i++) {
			total += i;
		}
	%>
		<!-- 표현식 스크립트 태그 사용 -->
		<p>1부터 <%= n %> 까지의 합은 <%=total %> 입니다. </p> 
	<% } %>
</body>
</html>
```

expression.jsp

![스크린샷 2024-09-04 181527.png](/images/2024-09-04/jsp-script%20tag/7.png)

![스크린샷 2024-09-04 181537.png](/images/2024-09-04/jsp-script%20tag/8.png)

표현식에서 주의할 점은, 식 뒤에 세미콜론(;)을 붙이면 안된다는 것이다. 만약 붙이게 되면, 표현식은 서블릿에서 `out.println(표현식)` 으로 변환되는데, 이 때 `<%=total;%>` 와 같이 식 뒤에 세미콜론을 붙이면 사실상 `out.println(total;);` 와 같기 때문이다. 

# 선언문 (declaration)

`<%! %>` 의 형태를 가지는 선언문 태그 내부에 변수나 메서드를 선언하면 서블릿으로 변환 시 각각 서블릿 클래스 내 필드 및 메서드로 변환된다. 다음은 선언문을 이용한 예제이다.

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<%!
		private String somethingToSay = null;
	
		public String getSay() {
			return somethingToSay;
		}
		
		public void setSay(String say) {
			somethingToSay = say;
		}
	%>
	
	<p>하고 싶은 말 설정 전: <%= getSay() %> </p>
	
	<%
		setSay("안녕하세요. hi. 123");
	%>
	
	<p>하고 싶은 말 설정 후: <%= getSay() %> </p>

</body>
</html>
```

scriptDeclaration.jsp

![1.PNG](/images/2024-09-04/jsp-script%20tag/9.png)

위 코드와 결과물을 보면, 선언문 내에 선언된 변수 및 메서드가 서블릿 클래스의 멤버가 된다는 것을 알 수 있다. 만약 그런 게 아니였다면 위와 같이 정상적으로 동작하지 못했을 것이다. 만약 service() 메서드 내부 코드로 변환되었더라면 애초에 getter, setter 메서드가 그 안에 들어갈 수 없다. 자바에서는 메서드 안에 메서드를 선언할 수가 없기 때문이다. 자바에서 메서드는 반드시 클래스가 선행되어야 존재할 수 있는 개념이기 때문이다. 

실제로 서블릿으로 변환된 코드를 보면 다음과 같다. 

```java
package org.apache.jsp;

import javax.servlet.*;
import javax.servlet.http.*;
import javax.servlet.jsp.*;

public final class scriptDeclaration_jsp extends org.apache.jasper.runtime.HttpJspBase
  implements org.apache.jasper.runtime.JspSourceDependent,
  org.apache.jasper.runtime.JspSourceImports {

	private String somethingToSay = null;
	
	public String getSay() {
		return somethingToSay;
	}
		
	public void setSay(String say) {
		somethingToSay = say;
	}
	// 생략
		
	public void _jspService(final javax.servlet.http.HttpServletRequest request, final javax.servlet.http.HttpServletResponse response)
    throws java.io.IOException, javax.servlet.ServletException {
    //생략
      
	  out.write("\r\n");
    out.write("<!DOCTYPE html>\r\n");
    out.write("<html>\r\n");
    out.write("<head>\r\n");
    out.write("<meta charset=\"UTF-8\">\r\n");
    out.write("<title>Insert title here</title>\r\n");
    out.write("</head>\r\n");
    out.write("<body>\r\n");
    out.write("	");
    out.write("\r\n");
    out.write("	\r\n");
    out.write("	<p>하고 싶은 말 설정 전: ");
    out.print( getSay() );
    out.write(" </p>\r\n");
    out.write("	\r\n");
    out.write("	");

		setSay("안녕하세요. hi. 123");
	
    out.write("\r\n");
    out.write("	\r\n");
    out.write("	<p>하고 싶은 말 설정 후: ");
    out.print( getSay() );
    out.write(" </p>\r\n");
    out.write("\r\n");
    out.write("</body>\r\n");
    out.write("</html>");
      
    // 생략
  }
}
```

scriptDeclaration_jsp.java

선언문은 실제로는 사용할 일이 거의 없다고 한다. 실제로 자주 쓰이는 것은 앞서 본 scriptlet과 expression이라고 한다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)