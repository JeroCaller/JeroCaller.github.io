---
title: "[JSP] Java Bean and Action tag"
category: "Servlet & JSP"
tag: ["JSP", "Action Tag", "Java Bean", "Bean"]
---

# Java Bean

java bean은 자바 코드로 작성된 소프트웨어 컴포넌트로, 표준 액션 태그를 이용하여 자바로 작성된 클래스를 객체화하여 해당 객체의 기능들을 사용할 수 있다. 

java bean과 이를 가져다 사용하는 표준 액션 태그의 등장으로 script 태그의 사용을 최소화시키고, HTML과 자바 코드를 분리할 수 있게 되었다. 

만약 이게 없었다면, 어떤 기능을 클래스 또는 메서드로 만들어 사용하고자 했으면 script 태그의 선언문 `<%! %>` 에 정의하여 사용했어야 할 것이다. script 태그도 HTML 내부에 자바 코드를 사용하는 방식이기에 HTML 코드와 자바 코드가 뒤엉켜 가독성과 유지보수성이 떨어졌을 것이다. 

java bean을 사용하는 표준 액션 태그로 다음이 존재한다. 

- `<jsp:useBean>` : bean 객체를 인스턴스화하여 사용하거나, 이미 존재하는 객체를 사용할 때 쓴다. 이미 존재하는 객체를 사용하려면 해당 객체를 처음 생성한 `<jsp:useBean>` 태그의 속성값들과 완전히 동일해야 한다.
    - 속성들
    - class : bean으로 사용할 자바 클래스가 선언된 클래스명.
    - id : class 속성값으로 명시된 클래스로부터 객체를 생성했을 때, 생성된 객체의 참조값을 담을 참조변수명.
    - scope : bean 객체의 사용 범위 또는 bean 객체를 찾을 범위. “page”가 기본값으로 설정되어 있으며, 사용 가능한 값으로는 “page”, “request”, “session”, “application”이 있다.
    - 이 외에도 beanName, type 속성이 있다고 한다.
- `<jsp:setProperty>` : bean 객체의 setter 메서드를 호출한다.
    - 속성들
    - name : `jsp:useBean` 액션 태그의 id 속성으로 설정된 객체 참조 변수명. 어떤 bean 객체를 사용할 것인지 먼저 정해야하기에 해당 속성은 필수이다.
    - property : bean 객체의 setter 메서드명. 만약 setter 메서드의 이름이 `setUserName` 으로 정의되어 있다면 `property="userName"` 과 같이 작성하면 된다.
    - value : property 속성값으로 지정된 setter 메서드에 인자로 전달할 값을 명시한다. `<jsp:setProperty name="user" property="userName" value="kimquel">` 과 같이 지정할 수 있다.
    - param : 요청 파라미터의 특정 name 값으로 지정하면, 그 name에 매핑된 value가 setter 메서드의 인자로 전달된다. 여기서의 name은 form 태그 내부의 입력 요소의 name 속성값을 의미한다.
- `<jsp:getProperty>` : bean 객체의 getter 메서드를 호출한다.
    - 속성들
    - name : `jsp:setProperty` 의 name 속성과 동일
    - property : bean 객체의 getter 메서드명. 만약 getter 메서드명이 `getUserName` 이라고 정의되어 있다면 `property="userName"` 과 같이 작성하면 된다.

java bean 정의와 이를 사용하는 표준 액션 태그의 자세한 사용법은 다음에 소개.

# java bean 사용

자세한 사용법을 살펴보기 전에 우선 java bean 정의와 이를 사용하는 표준 액션 태그의 예제를 먼저 살펴보겠다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<jsp:useBean id="user" class="com.jsp_study.UserSay"></jsp:useBean>
	<jsp:setProperty property="userName" name="user" value="개발자"/>
	<jsp:setProperty property="say" name="user" value="반갑구리~"/>
	
	<h1>말말말</h1>
	
	<h3>개발자의 말</h3>
	<ul>
		<li>
			닉네임: <jsp:getProperty property="userName" name="user"/>
		</li>
		<li>
			남긴 말: <jsp:getProperty property="say" name="user"/>
		</li>
		<li>
			말을 한 날짜 및 시각 : <jsp:getProperty property="currentDateTime" name="user"/>
		</li>
	</ul>
</body>
</html>
```

beanTest.jsp

```java
package com.jsp_study;

import java.text.SimpleDateFormat;
import java.util.Calendar;

public class UserSay {
	private String userName = null;
	private String say = null;
	
	public String getUserName() {
		return userName;
	}
	
	public void setUserName(String userName) {
		this.userName = userName;
	}
	
	public String getSay() {
		return say;
	}
	
	public void setSay(String say) {
		this.say = say;
	}
	
	public String getCurrentDateTime() {
		Calendar currentDateTime = Calendar.getInstance();
		SimpleDateFormat dFormat = new SimpleDateFormat("YYYY년 MM월 dd일 HH:mm:ss");
		return dFormat.format(currentDateTime.getTime());
	}
	
}

```

UserSay.java

jsp 파일은 webapp/beans 폴더에, UserSay.java 파일은 src/main/java의 com.jsp_study 패키지 내부에 생성하였다. 

beanTest.jsp 파일을 실행하면 브라우저에서 다음의 결과를 얻는다. 

![1.PNG](/images/2024-09-08/jsp-java-bean-and-action-tag/1.png)

위 코드를 하나씩 살펴보자. 

위의 jsp 코드를 보면 다음의 코드가 있다. 

```html
<jsp:useBean id="user" class="com.jsp_study.UserSay"></jsp:useBean>
```

위 코드를 자바 코드로 변환하면 다음과 같다. 

```html
<%@ page import="com.jsp_study.UserSay"%>
<!-- 생략 -->

<%
  UserSay user = new UserSay();
%>

```

즉, 자바 클래스를 가져와 이로부터 객체를 생성하여 해당 객체를 참조하는 참조 변수 user를 만든 것이다. 

setProperty와 합쳐서 보면 다음의 코드와도 같다. 

```html
<%@ page import="com.jsp_study.UserSay"%>
<!-- 생략 -->

<%
  UserSay user = new UserSay();
    
  user.setUserName("개발자");
  user.setSay("반갑구리~");
%>
```

여기에 getProperty와 합쳐서 보면 다음의 코드와도 같다.

```html
<%@ page import="com.jsp_study.UserSay"%>
<!-- 생략 -->

<%
  UserSay user = new UserSay();
    
  user.setUserName("개발자");
  user.setSay("반갑구리~");
%>
<h1>말말말</h1>
	
	<h3>개발자의 말</h3>
	<ul>
		<li>
			닉네임: <%= user.getUserName() %>
		</li>
		<li>
			남긴 말: <%= user.getSay() %>
		</li>
		<li>
			말을 한 날짜 및 시각 : <%= user.getCurrentDateTime() %>
		</li>
	</ul>
<!-- 생략 -->
```

bean 관련 액션 태그를 사용하면 객체 참조 변수 선언 및 초기화 과정과 setter 메서드를 통해 인스턴스의 멤버 변수 초기화하는 과정이 담긴 자바 코드를 아예 생략시키고 HTML 태그와 형태가 비슷한 액션 태그로 치환할 수 있어 자바 코드를 HTML로부터 분리할 수 있게 된다. 

# java bean과 bean 관련 표준 액션 태그 사용 규칙

java bean으로 사용될 클래스를 정의하고 이를 표준 액션 태그에서 사용할 때 정해진 규약을 지켜야 한다고 한다. 

- 자바 클래스에는 반드시 기본 생성자가 있어야 한다.
- 자바 클래스는 추상 클래스 또는 인터페이스가 아니여야 하며, 기본 생성자는 public으로 설정되어야 한다.
- 자바 클래스는 bean 액션 태그에서 사용할 수 있도록 getter, setter 메서드가 반드시 존재해야 한다.
- getter, setter 메서드 작성 규칙
    - getter 메서드는 반드시 어떤 값을 반환해야 한다. 즉, void가 작성되어선 안된다.
    - setter 메서드는 반드시 어떤 값을 인자로 받을 수 있게끔 매개변수를 하나 선언해야 하며, 매개변수는 단 한 개만 선언해야 한다. 반환값은 void로 해야 한다.
    - getter, setter 메서드 모두 메서드 이름은 반드시 각각 get과 set으로 시작되어야 하며, 그 뒤부터는 각 영단어마다 첫글자를 대문자로 작성해야 한다. 그래서 전체적인 메서드명이 camel case가 되도록 해야 한다. 예) `getUserName`, `setUserName`, `getName`
    - jsp set, getProperty 액션 태그에서 getter, setter 메서드를 property라는 속성을 통해 호출할 시, 각각 get, set의 이름을 빼고, get, set 바로 뒤에 오는 영단어의 첫글자는 소문자로 작성해야 한다. 예를 들어 getter 메서드명이 getUserName이라면, property=”userName”과 같이 속성값을 지정해야 한다.

# jsp useBean 액션 태그 추가 정보

`<jsp:useBean ... >` 액션 태그는 자식 요소(body라고 부른다)를 가질 수 있다. 이 경우, scriptlet `<% ... %>`이나 `<jsp:setProperty ... />` 액션 태그를 주로 사용할 수 있으며, body 부분은 bean 객체가 처음 생성 될 때에만 실행된다. 마치 자바 클래스의 생성자 메서드처럼 말이다. 

body 부분에 `<jsp:setProperty ... />` 를 사용하는 경우는 bean 객체의 특정 멤버 변수를 초기화할 때 사용될 수 있다. 

다음은 이를 활용하기 위해 앞서 본 파일들을 수정한 예제이다. 

```java
package com.jsp_study;

import java.text.SimpleDateFormat;
import java.util.Calendar;

public class UserSay {
	private String userName = null;
	private String say = null;
	private String onlyOne = null;
	
	public String getUserName() {
		return userName;
	}
	
	public void setUserName(String userName) {
		this.userName = userName;
	}
	
	public String getSay() {
		return say;
	}
	
	public void setSay(String say) {
		this.say = say;
	}
	
	public String getCurrentDateTime() {
		Calendar currentDateTime = Calendar.getInstance();
		SimpleDateFormat dFormat = new SimpleDateFormat("YYYY년 MM월 dd일 HH:mm:ss");
		return dFormat.format(currentDateTime.getTime());
	}
	
	/* 새로 추가한 코드 */
	public String getOnlyOne() {
		return onlyOne;
	}
	
	public void setOnlyOne(String say) {
		this.onlyOne = say;
	}
	
}

```

UserSay.java

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>

	<jsp:useBean id="user" class="com.jsp_study.UserSay" scope="session">
		<%= "bean 객체 초기화" %>
		<jsp:setProperty property="onlyOne" name="user" value="bean 객체 최초 생성 시에만 보인다."/>
		<jsp:getProperty property="onlyOne" name="user"/>
	</jsp:useBean>
	<jsp:setProperty property="userName" name="user" value="개발자"/>
	<jsp:setProperty property="say" name="user" value="반갑구리~"/>
	
	<h1>말말말</h1>
	
	<h3>개발자의 말</h3>
	<ul>
		<li>
			닉네임: <jsp:getProperty property="userName" name="user"/>
		</li>
		<li>
			남긴 말: <jsp:getProperty property="say" name="user"/>
		</li>
		<li>
			말을 한 날짜 및 시각 : <jsp:getProperty property="currentDateTime" name="user"/>
		</li>
	</ul>
</body>
</html>
```

beanTest.jsp

beanTest.jsp를 실행하면 다음의 결과를 얻는다. 

![1.PNG](/images/2024-09-08/jsp-java-bean-and-action-tag/2.png)

맨 처음 해당 페이지를 볼 때에는 위와 같이 맨 첫 번째 줄의 텍스트를 볼 수 있다. 이제 브라우저에서 새로고침을 해보자. 

![2.PNG](/images/2024-09-08/jsp-java-bean-and-action-tag/3.png)

맨 첫 번째 줄의 텍스트가 더 이상 보이질 않는다. 이는 bean 사용 시 scope를 session으로 설정하였기에, bean 객체는 세션 객체가 된 것이다. 두 번째 페이지 요청부터는 이미 서버 메모리에 해당 세션 객체가 생성되어 있기에 useBean 액션 태그의 body 부분이 더 이상 실행되지 않는 것이다. 

한 편, useBean의 id 속성값은 그 뒤에서 scriptlet 태그 내부에서도 그대로 사용 가능하다. 

```html
<!-- 생략 -->
<jsp:useBean id="user" class="com.jsp_study.UserSay"/>

<%
	user.setUserName("jeongDB");
	user.setSay("아 퇴근하고 싶다");
%>
```

# jsp setProperty 추가 정보

setProperty 태그에 value를 사용하면 매번 해당 페이지를 요청받을 때마다 setter 메서드에 똑같은 인자가 전달된다. 만약 고정된 값이 아니라, 전송된 요청에 실려 있는 데이터를 인자로 전달하고자 한다면 value 대신 param 속성을 이용하면 된다. 해당 속성에 들어가는 값은 요청으로 받은 name-value 쌍 데이터의 name을 기입하면 된다. 여기서의 name은 결국 HTML의 form 태그 내부의 입력 요소의 name 속성값을 의미한다. 

다음은 사용자로부터 데이터를 입력받으면 이를 응답 페이지로 보여주는데, 이 과정에서 setProperty 표준 액션 태그와 그 내부에 param 속성을 사용한 예제이다. 

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
	
	<%
		if (request.getMethod().equalsIgnoreCase("GET")) {
	%>
		<form action="showUserSay.jsp" method="post">
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
		} else { 
			request.setCharacterEncoding("utf-8");
	%>

	<jsp:useBean id="user2" class="com.jsp_study.UserSay"/>
	<jsp:setProperty property="userName" name="user2" param="nickname"/>
	<jsp:setProperty property="say" name="user2" param="say"/>
	
	<h1>말말말</h1>
	
	<ul>
		<li>
			닉네임: <jsp:getProperty property="userName" name="user2"/>
		</li>
		<li>
			남긴 말: <jsp:getProperty property="say" name="user2"/>
		</li>
		<li>
			말을 한 날짜 및 시각 : <jsp:getProperty property="currentDateTime" name="user2"/>
		</li>
	</ul>
	
	<%
		}
	%>
</body>
</html>
```

showUserSay.jsp

![3.PNG](/images/2024-09-08/jsp-java-bean-and-action-tag/4.png)

![4.PNG](/images/2024-09-08/jsp-java-bean-and-action-tag/5.png)

위 예제에서…

```html
<jsp:useBean id="user2" class="com.jsp_study.UserSay"/>
<jsp:setProperty property="userName" name="user2" param="nickname"/>
<jsp:setProperty property="say" name="user2" param="say"/>
```

이 부분은 scriptlet으로 변환하면 다음과 같다. 

```html
<%
  UserSay user2 = new UserSay();
  String reqUserName = request.getParameter("nickname");
  String reqSay = request.getParameter("say");
   
  user2.setUserName(reqUserName);
  user2.setSay(reqSay);
%>
```

한 편, param 속성을 생략하면, param 속성값은 자동으로 property 속성값으로 설정된다고 한다. 예를 들어 `<jsp:setProperty property="userName" name="user">` 라고 한다면 이는 곧 `<jsp:setProperty property="userName" name="user" param="userName">`  과도 같다. 이러한 이유로, form 태그 내 입력 요소의 name 속성값과 bean 객체의 setter 메서드명의 set 이후의 이름을 통일시키는 것이 param 속성을 생략하면서도 문제 없이 요청 파라미터를 setter 메서드에 전달할 수 있다. 

만약 위 코드에서 

```html
<jsp:setProperty property="userName" name="user2"/>
```

와 같이 param을 생략해버리면, 이에 매핑된 form 요소 내부의 input 요소의 name 속성값인 “nickname”과 위 표준 액션 태그의 property 속성값이 다르므로 다음의 결과를 얻는다. 

![5.PNG](/images/2024-09-08/jsp-java-bean-and-action-tag/6.png)

이번에는 form 태그 내 입력 요소의 name 속성값을 setter 메서드의 이름에 맞춰 바꿔보겠다. 

```html
<!-- 생략 -->
<li>
  <label for="nickname">닉네임: </label>
  <input type="text" name="userName" />
</li>
<!-- 생략 -->
<jsp:useBean id="user2" class="com.jsp_study.UserSay"/>
<jsp:setProperty property="userName" name="user2"/>
<jsp:setProperty property="say" name="user2"/>
<!-- 생략 -->
```

showUserSay.jsp

다시 실행해보면 다음의 결과를 얻는다.

![6.PNG](/images/2024-09-08/jsp-java-bean-and-action-tag/7.png)

---

References

[1] 에이콘아카데미(강남) 강의

[2] JavaSever Pages Specification Version2.3 Maintenace Release3

[3] java bean

[Bean 개념 정리](https://velog.io/@skyni/Bean-개념-정리)

[4] java bean

[Java Bean VS Spring Bean](https://jjingho.tistory.com/10)