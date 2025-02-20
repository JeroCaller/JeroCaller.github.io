---
title: "[Servlet & JSP] MVC pattern"
category: "Servlet & JSP"
tag: ["Servlet", "JSP", "MVC", "Model", "Controller", "View"]
---

MVC는 Model, View, Controller의 약자로, 웹 앱을 구성하는 요소들을 각각 3개로 나눠 개발하는 디자인 패턴이다. 

이전에는 웹 앱 과정이 다음과 같았다. 사용자로부터의 요청을 JSP가 직접 받는다. 그 후, 비즈니스 로직이나 DB 로직에 대해서는 각각 java bean이나 DAO 클래스를 이용하여 처리하였다. 이 java bean과 DAO 클래스가 Model에 해당한다. Model로부터 처리된 결과를 다시 받아 이를 똑같은 JSP가 사용자에게 응답 페이지로 보여주는 과정을 거쳤다. 이 때 JSP는 사용자로부터 응답을 받는 역할(Controller)과 동시에 응답 페이지를 사용자에게 보여주는 역할(View)도 같이 진행하는 것이다. 즉, 하나의 JSP가 두 개의 역할을 동시에 떠맡고 있는 셈이다. MVC 패턴은 이러한 역할들을 나눔으로써 코드의 가독성과 유지보수성을 확보하는 것이 목표이다. 

# MVC 패턴의 세 가지 구성 요소

MVC의 세 구성 요소를 정리하자면 다음과 같다.

- Model : DAO 클래스를 이용하여 DB와의 연동 로직 및 비즈니스 로직을 처리하는 곳이다. 자바의 일반적인 클래스 또는 java bean이 model 역할을 맡는다.
- View : 사용자에게 보여줄 응답 페이지를 구성한다. model로부터의 데이터를 controller를 통해 간접적으로 받아 이 데이터를 토대로 응답 페이지를 구성한다. 즉, presentation logic이 수행되는 곳이다. jsp가 이 역할을 맡는다.
- Controller : 사용자로부터의 요청을 받아 이 요청 처리에 관련된 DB 연동 로직 및 비즈니스 로직 처리를 model에 위임하여 처리하게 한 후, 그로부터 나온 데이터들을 응답 페이지로 보여줄 수 있는 view에 전송하는 역할을 한다. Controller는 view와 Model의 매개체 역할을 하며, 사용자 요청의 종류에 따라 어떤 model이 이 요청을 처리하게 할지, 그리고 model로부터 도출한 데이터들을 어느 view에 전달할지도 결정하는 “지휘자” 역할을 한다. Controller는 사용자로부터 오는 HTTP 요청을 받을 수 있어야 하기에 Servlet이 controller 역할을 맡는다. 즉, 정리하자면 controller의 역할은 대개 다음과 같다.
    - 클라이언트로부터 HTTP 요청을 통해 전달받은 입력 데이터(상태 정보)를 추출.
    - 페이지 이동
    - model과 view의 중간 매개체. model로부터 나온 데이터를 view에 전송.

이렇게 세 구성 요소로 분리하면 business logic과 presentation logic을 분리할 수 있게 되고, 이로 인해 business logic에서 수정할 부분이 있을 때 presentation logic을 건드리지 않고도 진행할 수 있기에 유지보수성을 높일 수 있다는 장점이 있다. 

# Model 1 VS Model 2

MVC 패턴에 대해 설명할 때 주로 Model 1 방식과 Model 2 방식으로 나뉘어 설명된다. 

## Model 1

앞서 맨 처음에 언급한 방식이 바로 Model 1이다. 즉, 하나의 jsp 페이지가 사용자로부터 요청을 받아 요청 정보를 추출하는 controller의 역할과 응답 페이지를 구성하는 view 이 두 역할을 동시에 맡아 처리하는 방식이다. 클라이언트로부터의 HTTP 요청을 받고 이를 처리하는 과정이 다음의 그림과 같이 선형적이라는 것이 특징이다. 

![그림 1-1. Model 1 방식을 도표로 그려보았다. ](/images/2024-09-23/servlet-jsp-mvc-pattern/1.png)

그림 1-1. Model 1 방식을 도표로 그려보았다. 

Model 1 방식은 View와 Controller의 역할을 JSP 하나가 맡기에 유지보수 관점에서는 좋지 않다. 반면 개발 속도가 상대적으로 빠르기에, 빠른 구현이 우선이거나 작고 가벼운 프로젝트일 떄, 또는 유지보수가 별로 필요없는 프로젝트에서는 model1 방식을 사용한다고 한다. 

## Model 2

사실 MVC 패턴이라함은 보통 model 2 방식을 가리킨다. model, controller, view라는 계층들을 서로 나눠 역할 분담을 함으로써 유지보수성을 높이는 디자인 패턴이라 볼 수 있다. model 2 방식을 그림으로 그려보면 model 1 방식이 선형적인 구조를 띄는 것에 비해 model 2는 비선형적 구조를 띄는 것을 확인할 수 있다. 

![그림 2-1. model 2 방식을 그림으로 그려보았다. ](/images/2024-09-23/servlet-jsp-mvc-pattern/2.png)

그림 2-1. model 2 방식을 그림으로 그려보았다. 

유지보수성이 상대적으로 뛰어나 유지보수 작업을 빈번히 해야하는 프로젝트, 또는 대규모 프로젝트일 때 사용될 수 있다. 반면 개발 속도는 model 1 방식에 비해 느리고 구조가 복잡하게 느껴질 수 있다는 단점도 존재한다. 

# 예제 코드로 MVC 패턴 살펴보기

이번에는 예제 코드를 통해 MVC 패턴을 살펴보도록 하겠다. 처음에는 model 1 방식으로 구성해보고, 이를 model 2 방식으로 바꿔보겠다. 그 후, 기초적인 MVC 패턴에서 보이는 문제점들을 해결하기 위해 차례대로 Front Controller Pattern → Command Pattern → Factory Pattern 들을 적용해보겠다. 

## Model 1 방식 예제

아주 간단한 (그리고 너무 초라한) 쇼핑몰 예제를 만들어보았다. 이 쇼핑몰을 “Jerocaller의 온라인 쇼핑몰!”이라고 부르겠다. 

다음은 그 결과물이다. 

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-09-23/servlet-jsp-mvc-pattern/1.mp4"
  controls="controles"
  muted="muted"
  width="100%"
></video>
</div>

자료 3-1. 쇼핑몰 예제 시연 영상

프로젝트 폴더 구조는 다음과 같다. 

![그림 3-1. model 1 프로젝트 구조.](/images/2024-09-23/servlet-jsp-mvc-pattern/3.png)

그림 3-1. model 1 프로젝트 구조.

model 1 방식 프로젝트에서, model 패키지 내부의 코드는 여기서는 생략하겠다. 여기선 MVC에 대해 살펴보는 것이 핵심이기 때문에, MVC와 관련이 없는 코드도 여기서 작성하면 페이지 길이가 너무 길어지기 때문이다. 여기서 살펴볼 것은 webapp 내부에 존재하는 jsp 또는 html 파일들이다. 

각 파일들의 관계도를 먼저 보면 다음과 같다. 

![그림 3-2. 쇼핑몰 예제의 파일들의 관계도. model 1 방식이다.](/images/2024-09-23/servlet-jsp-mvc-pattern/4.png)

그림 3-2. 쇼핑몰 예제의 파일들의 관계도. model 1 방식이다.

각 파일들의 코드는 다음과 같다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JeroCaller's Online Shopping mall</title>
  <link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
  <h1>JeroCaller의 온라인 쇼핑몰!</h1>
  <ul>
    <li>
      <a href="/MVCPatternStudy/login.html">로그인/회원가입</a>
    </li>
  </ul>
</body>
</html>
```

index.html

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>로그인</title>
<link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
	<form method="post" action="/MVCPatternStudy/loginProc.jsp">
		<ul>
			<li>
				아이디 : <input type="text" name="id" />
			</li>
			<li>
				패스워드 : <input type="password" name="pw" />
			</li>
		</ul>
		<button type="submit">로그인</button>
	</form>
	<p><a href="/MVCPatternStudy/signup.html">회원가입</a></p>
</body>
</html>
```

login.html

```html
<%@page import="java.util.List"%>
<%@page import="model.db.UserDTO"%>
<%@page import="model.db.UserDAO"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	// Business, DB logic
	String id = request.getParameter("id");
	String pw = request.getParameter("pw");
	
	UserDTO inputUser = new UserDTO();
	inputUser.setId(id);
	inputUser.setPassword(pw);
	
	UserDAO userDao = new UserDAO();
	List<UserDTO> allUsers = userDao.getAllUsers();
	
	if (allUsers.contains(inputUser)) {
		// 로그인한 현재 사용자의 정보를 세션에 저장.
		session.setAttribute("userInfo", inputUser);
		
		String url = "/MVCPatternStudy/shop.jsp";
		response.sendRedirect(url);  // 페이지 이동
	} else {
		// presentation logic
%>
	<script>
		alert("아이디 또는 패스워드가 맞지 않습니다.");
		history.back();
	</script>
<%
	}
%>
```

loginProc.jsp

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>회원 가입</title>
<link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
  <h1>회원 가입</h1>
  <form method="post" action="/MVCPatternStudy/signupProc.jsp">
    <ul>
      <li>
        사용할 아이디 : <input type="text" name="id" required="required" /> 
      </li>
      <li>
        사용할 패스워드 : <input type="password" name="pw" required="required" />
      </li>
    </ul>
    <button type="submit">회원가입하기</button>
  </form>
</body>
</html>

```

signup.html

```html
<%@page import="model.db.UserDAO"%>
<%@page import="model.db.UserDTO"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>회원가입 결과</title>
<link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
	<%
		// business, DB logic
		String id = request.getParameter("id");
		String pw = request.getParameter("pw");
		
		UserDTO oneUser = new UserDTO();
		oneUser.setId(id);
		oneUser.setPassword(pw);
		
		UserDAO userDao = new UserDAO();
		boolean isSuccess = userDao.insertUser(oneUser);
		
		if (isSuccess) {
			// Presentation logic
	%>
			<p>회원가입에 성공하였습니다. 로그인 화면으로 돌아가 로그인해주세요.</p>
			<a href="/MVCPatternStudy/login.html">로그인 화면으로 돌아가기</a>
	<%
		} else {
	%>
			<p>회원가입에 실패했습니다. 다시 회원가입 해주세요.</p>
			<a href="/MVCPatternStudy/signup.html">회원가입 창으로 되돌아가기</a>
	<%
		}
	%>
</body>
</html>
```

signupProc.jsp

```html
<%@page import="model.db.UserDTO"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	// Business, DB logic
	UserDTO currentUser = (UserDTO) session.getAttribute("userInfo");
	String userName = null;
	
	if (currentUser == null) {
		userName = "guest";
	} else {
		userName = currentUser.getId();
	}
	
	// Presentation Logic
%>
<header>
	<span>JeroCaller's online shop</span>
	<span><%= userName %>님 환영합니다.</span>
</header>
<hr/>
```

header.jsp

```html
<%@page import="java.util.ArrayList"%>
<%@page import="model.bean.Item"%>
<%@page import="java.util.List"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JeroCaller's Online Shopping mall</title>
<link rel="stylesheet" href="/MVCPatternStudy/css/header.css">
<link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
	<% 
		// 사용자 요청을 받음. 
		request.setCharacterEncoding("utf-8");
	%>
	<jsp:include page="/webComponents/header.jsp"/>
	<div id="container">
		<!-- 
			왼쪽 사이드바. 카테고리 메뉴. 
			각 카테고리에 맞는 물품들이 나오도록 한다. 
		-->
		<aside>
			<jsp:include page="/webComponents/shops/shopCategory.jsp"/>
		</aside>
		
		<!-- 
			메인. 물품 목록들이 나오며, 사용자가 특정 물품을 선택하여 
			장바구니에 등록할 수 있도록 한다. 
		-->
		<main>
			<jsp:include page="/webComponents/shops/productList.jsp"/>
		</main>
		
		<!-- 오른쪽 사이드바. 사용자의 장바구니 목록 보이기 -->
		<aside>
			<jsp:include page="/webComponents/shops/cartList.jsp"/>
		</aside>
	</div>
	
	<jsp:include page="/webComponents/footer.jsp"/>
</body>
</html>
```

shop.jsp

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	String baseUrl = "/MVCPatternStudy/shop.jsp";
%>
<ul>
	<li>
		<form method="post" action="<%=baseUrl%>">
			<input type="hidden" name="command" value="CATEGORY"/>
			<input type="hidden" name="category" value="food"/>
			<button type="submit">먹을 거리</button>
		</form>
	</li>
	<li>
		<form method="post" action="<%=baseUrl%>">
			<input type="hidden" name="command" value="CATEGORY"/>
			<input type="hidden" name="category" value="cloth"/>
			<button type="submit">의류</button>
		</form>
	</li>
	<li>
		<form method="post" action="<%=baseUrl%>">
			<input type="hidden" name="command" value="CATEGORY"/>
			<input type="hidden" name="category" value="sundries"/>
			<button type="submit">잡화</button>
		</form>
	</li>
</ul>

```

shopCategory.jsp

```html
<%@page import="model.db.ProductDTO"%>
<%@page import="java.util.List"%>
<%@page import="model.db.ProductDAO"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%	
	// 사용자 요청 정보 추출
	String command = request.getParameter("command");
	String category = null;
	
	// DB 로직.
	ProductDAO productDao = new ProductDAO();

	if (command != null && command.equals("CATEGORY")) {
		category = request.getParameter("category");
		productDao.setSelectedCategory(category);
		
		// shop.jsp로부터 다른 종류의 요청이 들어와도 선택된 카테고리의 
		// 물품들은 그대로 테이블에 보이도록 하기 위해 선택된 카테고리명을 세션 context에 저장한다. 
		session.setAttribute("selectedCategory", category);
	} else {
		category = (String)session.getAttribute("selectedCategory");
		productDao.setSelectedCategory(category);
	}
	
	List<ProductDTO> products = productDao.getAllproducts();
	
	// presentation logic
%>
<table border="1">
	<tr>
		<th>상품</th>
		<th>가격</th>
		<th>장바구니 추가</th>
	</tr>
	<%
		// presentation logic
		if (products != null) {
			for (ProductDTO item : products) {
	%>
		<tr>
			<form method="post" action="/MVCPatternStudy/shop.jsp">
				<input type="hidden" name="command" value="CART"/>
				<input 
					type="hidden" 
					name="productName" 
					value="<%= item.getName() %>"
				/>
				<input 
					type="hidden" 
					name="price" 
					value="<%= item.getPrice() %>"
				/>
				<td><%= item.getName() %></td>
				<td><%= item.getPrice() %></td>
				<td><button type="submit">장바구니 추가</button></td>
			</form>
		</tr>
	<%
			}
		}
	%>
</table>
```

productList.jsp

```html
<%@page import="java.util.ArrayList"%>
<%@page import="model.bean.Item"%>
<%@page import="java.util.List"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	// business, DB logic
	String command = request.getParameter("command");
	String productName = null;
	String priceStr;
	int price = -1;
	
	List<Item> items = (ArrayList<Item>)session.getAttribute("items");

	if (command != null && command.equals("CART")) {
		productName = request.getParameter("productName");
		priceStr = request.getParameter("price");
		price = Integer.parseInt(priceStr);
		
		// 사용자가 선택한 물품 정보 담기
		Item selectedItem = new Item();
		selectedItem.setProductName(productName);
		selectedItem.setPrice(price);
		selectedItem.setEa(1);
		
		// 사용자가 선택한 물품을 장바구니에 담기.
		if (items == null) {
			items = new ArrayList<Item>();
			items.add(selectedItem);
		} else {
			if (items.contains(selectedItem)) {
				// 사용자가 선택한 물품이 이미 장바구니에 있을 경우 수량을 추가. 
				// 총 수량에 맞게끔 총 가격도 수정. 
				Item alreadyExistItem = items.get(items.indexOf(selectedItem));
				alreadyExistItem.setEa(
					alreadyExistItem.getEa() + selectedItem.getEa()
				);
				alreadyExistItem.setPrice(
					alreadyExistItem.getPrice() + selectedItem.getPrice()
				);
			} else {
				items.add(selectedItem);
			}
		}
		// 세션에 장바구니 정보 저장. 
		session.setAttribute("items", items);
	}
%>
<!-- Presentation Logic -->
<h3>장바구니</h3>
<table border="1">
	<tr>
		<th>상품명</th>
		<th>수량</th>
		<th>총 가격</th>
	</tr>
	<%
		if (items != null) {
			for (Item oneItem : items) {
	%>
		<tr>
			<td><%=oneItem.getProductName()%></td>
			<td><%=oneItem.getEa()%></td>
			<td><%=oneItem.getPrice()%></td>
		</tr>
	<%
			}
		}
	%>
</table>
```

cartList.jsp

위 코드들과 그림 3-1과 같이 보면 알겠지만, 꽤 많은 jsp 파일 내부에서 business logic과 presentation logic이 한 페이지에 뒤섞여 있음을 알 수 있다. 즉, 하나의 jsp가 Controller와 View의 역할을 동시에 하는 파일들이 꽤 많음을 알 수 있다. 

따라서 이번에는 Controller와 View의 역할을 나눠 jsp는 view의 역할만을 하고, 페이지 이동, 사용자 요청 추출, 비즈니스 로직들은 Controller의 역할을 할 서블릿으로 분리하여 model 2 방식으로 리팩토링 해보겠다. 

## Model 2 방식 예제

앞서 살펴본 Model 1 방식 예제를 Model 2 방식으로 리팩토링 하기 위해 다음의 구조를 참조할 것이다. 

![그림 4-1. 기존 Model 1 방식을 Model 2 방식으로 고쳤다. ](/images/2024-09-23/servlet-jsp-mvc-pattern/5.png)

그림 4-1. 기존 Model 1 방식을 Model 2 방식으로 고쳤다. 

위 그림을 보면 이제 Controller 역할은 오로지 Servlet만이 맡게 되었고, jsp는 무조건 view의 역할만 담당하도록 역할이 분리되었다. 

다음은 Model 2 방식으로 바뀐 코드들이다. 먼저 index.html에서 login.html로 넘어갈 때의 코드들이다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="X-UA-Compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>JeroCaller's Online Shopping mall</title>
  <link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
  <h1>JeroCaller의 온라인 쇼핑몰!</h1>
  <ul>
    <li>
      <!-- <a href="/MVCPatternStudy/login.html">로그인/회원가입</a> -->
      <a href="/MVCPatternStudy/login.do">로그인/회원가입</a>
    </li>
  </ul>
</body>
</html>
```

index.html

```java
package controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/login.do")
public class LoginViewServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		// 로그인 화면으로 이동.
		String targetUrl = "/login.html";
		RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
		dispatcher.forward(req, resp);
	}
	
}

```

LoginViewServlet.java

위 서블릿은 단순히 index.html에서 login.html으로 페이지 이동하는 역할만 할 뿐이다. 사실 여기서는 서블릿을 사용하지 않아도 되었는데, 다른 곳에서도 두 페이지 간의 이동에서 서블릿이 중간 매개체 역할을 하기에 일관성을 위해 여기서도 서블릿을 두었다. 

index.html에서 하이퍼링크가 걸린 a 태그의 텍스트를 클릭하는 것은 그 자체로 하이퍼링크로 연결된 login.html 페이지를 클라이언트가 HTTP 요청하는 것과 동일하다. 따라서 이러한 HTTP 요청과 응답을 처리하는 역할도 Controller 역할을 하는 서블릿에게 위임한 것이다. 

로그인 화면이 되는 login.html의 코드는 다음과 같다. 

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>로그인</title>
<link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
	<!--<form method="post" action="/MVCPatternStudy/loginProc.jsp"> -->
	<form method="post" action="/MVCPatternStudy/loginProc.do">
		<ul>
			<li>
				아이디 : <input type="text" name="id" />
			</li>
			<li>
				패스워드 : <input type="password" name="pw" />
			</li>
		</ul>
		<button type="submit">로그인</button>
	</form>
	<!-- <p><a href="/MVCPatternStudy/signup.html">회원가입</a></p> -->
	<p><a href="/MVCPatternStudy/signup.do">회원가입</a></p>
</body>
</html>
```

login.html

이 페이지에는 로그인하여 쇼핑몰 페이지로 이동하는 것과 회원가입 페이지로 이동하는 두 갈래가 있다. 여기서 먼저 회원가입부터 살펴보겠다. 

```java
package controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

@WebServlet("/signup.do")
public class SignUpViewServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		// 페이지 이동.
		String targetUrl = "/signup.html";
		RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
		dispatcher.forward(req, resp);
	}
	
}

```

SignUpViewServlet.java

위 서블릿도 login.html에서 “회원가입” 버튼을 누르면 “signup.html”로 이동시키는 역할을 맡고 있다. 역시나 HTTP 요청-응답 과정이 포함되어 있기에 서블릿을 사용하였다. 

다음은 signup.html과 이 페이지에 연동된 서블릿 코드이다. 

```html
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>회원 가입</title>
<link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
  <h1>회원 가입</h1>
  <!-- <form method="post" action="/MVCPatternStudy/signupProc.jsp">  -->
  <form method="post" action="/MVCPatternStudy/signupProc.do">
    <ul>
      <li>
        사용할 아이디 : <input type="text" name="id" required="required" /> 
      </li>
      <li>
        사용할 패스워드 : <input type="password" name="pw" required="required" />
      </li>
    </ul>
    <button type="submit">회원가입하기</button>
  </form>
</body>
</html>

```

signup.html

```java
package controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import model.db.UserDAO;
import model.db.UserDTO;

@WebServlet("/signupProc.do")
public class SignUpProcServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		// 회원 가입 로직 처리
		String id = req.getParameter("id");
		String pw = req.getParameter("pw");
		
		UserDTO oneUser = new UserDTO();
		oneUser.setId(id);
		oneUser.setPassword(pw);
		
		UserDAO userDao = new UserDAO();
		boolean isSuccess = userDao.insertUser(oneUser);
		
		req.setAttribute("isSignUpSuccess", isSuccess);
		
		// 페이지 이동.
		String targetUrl = "/signupView.jsp";
		RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
		dispatcher.forward(req, resp);
	}
	
}

```

SignUpProcServlet.java

signup.html에서 사용자가 회원가입을 위한 아이디, 패스워드의 정보를 넘기면 서블릿에서 DB에 해당 데이터들을 삽입한 후, 회원 가입 결과를 보여주는 “signupView.jsp”로 포워딩하고 있다. 해당 페이지 코드는 다음과 같다. 

```java
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
<%
	boolean isSuccess = (boolean)request.getAttribute("isSignUpSuccess");

	if (isSuccess) {
		// Presentation logic
%>
	<p>회원가입에 성공하였습니다. 로그인 화면으로 돌아가 로그인해주세요.</p>
	<a href="/MVCPatternStudy/login.do">로그인 화면으로 돌아가기</a>
<%
	} else {
%>
	<p>회원가입에 실패했습니다. 다시 회원가입 해주세요.</p>
	<a href="/MVCPatternStudy/signup.do">회원가입 창으로 되돌아가기</a>
<%
	}
%>

</body>
</html>
```

signupView.jsp

위 jsp는 원래 Model 1 예제의 signupProc.jsp이었다. 여기서 DB, Business 로직을 서블릿으로 이동한 것이다. 이렇게 하니 비즈니스 로직과 프레젠테이션 로직을 최대한 분리할 수 있고, 이를 통해 각 로직을 별개의 장소에 담아 관리하기 수월해짐을 알 수 있다. 물론 위 코드를 보면 알겠지만, 서블릿으로부터 전달한 데이터를 추출하기 위한 자바 코드는 어쩔 수 없이 들어가 있어 사용자 요청 정보 추출 기능과 프레젠테이션 로직을 완전히 분리했다고 보기는 어렵다고 생각한다. 그래도 이 정도면 가독성과 유지보수성이 확실히 올랐다고 생각한다. 

이번에는 login.html 페이지에서의 로그인 인증 절차 코드를 보자. Model 1에서는 사용자가 입력한 정보를 검증하는 로직과 로그인 실패 시 실패 결과를 보여주는 로직이 loginProc.jsp 한 페이지에 섞여 들어있었다. 그러나 서블릿을 사용하면 다음과 같이 완전히 두 로직을 분리할 수 있다. 

```java
package controller;

import java.io.IOException;
import java.util.List;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import model.db.UserDAO;
import model.db.UserDTO;

@WebServlet("/loginProc.do")
public class LoginProcServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		
		// 로그인 인증 로직.
		String id = req.getParameter("id");
		String pw = req.getParameter("pw");
		
		UserDTO inputUser = new UserDTO();
		inputUser.setId(id);
		inputUser.setPassword(pw);
		
		UserDAO userDao = new UserDAO();
		List<UserDTO> allUsers = userDao.getAllUsers();
		
		HttpSession session = req.getSession();
		String targetUrl = "";
		
		if (allUsers.contains(inputUser)) {
			// 로그인한 현재 사용자의 정보를 세션에 저장.
			session.setAttribute("userInfo", inputUser);
			
			targetUrl = "/shop.jsp";
		} else {
			targetUrl = "/loginFailed.jsp";
		}
		
		RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
		dispatcher.forward(req, resp);
	}
	
}

```

LoginProcServlet.java

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<script>
		alert("아이디 또는 패스워드가 맞지 않습니다.");
		history.back();
	</script>
</body>
</html>
```

loginFailed.jsp

로그인 성공 시 이동될 메인 쇼핑몰 페이지에 대해 살펴보겠다. shop.jsp는 기존 코드와 비교했을 때 변한 것이 거의 없다. 다만, 여기서도 Controller가 도입되었기에 shop.jsp는 더 이상 Controller의 역할도 같이 하지 않고 오로지 View의 역할만 하게 되었다. 

shop.jsp에서 바뀐 거라고는 request의 인코딩 코드만 빠진 것일 뿐이다. 

```html
<%@page import="java.util.ArrayList"%>
<%@page import="model.bean.Item"%>
<%@page import="java.util.List"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>JeroCaller's Online Shopping mall</title>
<link rel="stylesheet" href="/MVCPatternStudy/css/header.css">
<link rel="stylesheet" href="/MVCPatternStudy/css/style.css">
</head>
<body>
	<%-- 
		// 사용자 요청을 받음. 
		request.setCharacterEncoding("utf-8");
	--%>
	<jsp:include page="/webComponents/header.jsp"/>
	<div id="container">
		<!-- 
			왼쪽 사이드바. 카테고리 메뉴. 
			각 카테고리에 맞는 물품들이 나오도록 한다. 
		-->
		<aside>
			<jsp:include page="/webComponents/shops/shopCategory.jsp"/>
		</aside>
		
		<!-- 
			메인. 물품 목록들이 나오며, 사용자가 특정 물품을 선택하여 
			장바구니에 등록할 수 있도록 한다. 
		-->
		<main>
			<jsp:include page="/webComponents/shops/productList.jsp"/>
		</main>
		
		<!-- 오른쪽 사이드바. 사용자의 장바구니 목록 보이기 -->
		<aside>
			<jsp:include page="/webComponents/shops/cartList.jsp"/>
		</aside>
	</div>
	
	<jsp:include page="/webComponents/footer.jsp"/>
</body>
</html>
```

shop.jsp

그리고 header.jsp도 바뀌지 않았다. LoginProcServlet에서 로그인 정보를 세션에 담은 채로 shop.jsp로 전달하는데, 이 페이지에서 header.jsp를 include하고 있기에 자연스레 header.jsp에도 세션 정보가 전달되기 때문이다. 세션으로부터 사용자 로그인 정보를 추출하고, 로그인한 사람인지 비로그인 사용자인지도 구별해야 하기에 다음과 같이 코드는 그대로 두었다. 

```html
<%@page import="model.db.UserDTO"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	// Business Logic
	UserDTO currentUser = (UserDTO) session.getAttribute("userInfo");
	String userName = null;
	
	if (currentUser == null) {
		userName = "guest";
	} else {
		userName = currentUser.getId();
	}
	
	// Presentation Logic
%>
<header>
	<span><a href="/MVCPattenStudy/index.html">JeroCaller's online shop</a></span>
	<span><%= userName %>님 환영합니다.</span>
</header>
<hr/>
```

header.jsp 

model에 해당하는 UserDTO가 header.jsp라는 View 코드 내부에 직접 사용된 것을 볼 수 있다. MVC 패턴의 단점 중에는 Model과 View의 의존성을 완전히 분리시킬 수 없다는 이야기가 자주 나오는데, 이것도 한 예시가 되지 않을까 생각된다. Model - view 의존성 문제는 하나의 controller에 여러 개의 view 또는 model들이 복잡하게 연결되어 있을 때 발생하는 문제라고 한다. LoginProcServlet도 앞선 그림 4-1을 보면 하나의 Controller에 shop.jsp와 header.jsp라는 두 개의 view와 연결되어 있음을 알 수 있다. 이로 인해 model - view를 완전히 분리시키지 못한 것 같기도 하다. 물론 여기서 더 좋은 방법이 있을텐데 내가 떠올리지 못한 걸 수도 있다. MVC 패턴의 단점에 대해서는 나중에 한 번에 정리해보겠다. 

한 편, shop.jsp에서 shopCategory.jsp에 해당하는 카테고리들 중 하나를 택하면 productList.jsp를 통해 그 카테고리의 물품들만을 보여주는 과정에도 서블릿 하나가 추가되었다. Model 2 방식으로 변모된 코드는 다음과 같다. 

```html
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	String baseUrl = "/MVCPatternStudy/products.do";
%>
<ul>
	<li>
		<form method="post" action="<%=baseUrl%>">
			<input type="hidden" name="command" value="CATEGORY"/>
			<input type="hidden" name="category" value="food"/>
			<button type="submit">먹을 거리</button>
		</form>
	</li>
	<li>
		<form method="post" action="<%=baseUrl%>">
			<input type="hidden" name="command" value="CATEGORY"/>
			<input type="hidden" name="category" value="cloth"/>
			<button type="submit">의류</button>
		</form>
	</li>
	<li>
		<form method="post" action="<%=baseUrl%>">
			<input type="hidden" name="command" value="CATEGORY"/>
			<input type="hidden" name="category" value="sundries"/>
			<button type="submit">잡화</button>
		</form>
	</li>
</ul>

```

shopCategory.jsp

```java
package controller;

import java.io.IOException;
import java.util.List;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import model.db.ProductDAO;
import model.db.ProductDTO;

@WebServlet("/products.do")
public class CategoryProductServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		// 사용자 요청 정보 추출
		String command = req.getParameter("command");
		String category = null;
		
		HttpSession session = req.getSession();
		
		// DB 로직.
		ProductDAO productDao = new ProductDAO();

		if (command != null && command.equals("CATEGORY")) {
			category = req.getParameter("category");
			productDao.setSelectedCategory(category);
			
			// shop.jsp로부터 다른 종류의 요청이 들어와도 선택된 카테고리의 
			// 물품들은 그대로 테이블에 보이도록 하기 위해 선택된 카테고리명을 세션 context에 저장한다. 
			session.setAttribute("selectedCategory", category);
		} else {
			category = (String)session.getAttribute("selectedCategory");
			productDao.setSelectedCategory(category);
		}
		
		// 선택된 카테고리에 해당하는 모든 물품 정보들을 세션에 저장.
		List<ProductDTO> products = productDao.getAllproducts();
		session.setAttribute("products", products);
		
		// 포워딩으로 페이지 이동
		String targetUrl = "/shop.jsp";
		RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
		dispatcher.forward(req, resp);
	}
	
}

```

CategoryProductServlet.java

```java
<%@page import="java.util.ArrayList"%>
<%@page import="model.db.ProductDTO"%>
<%@page import="java.util.List"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	List<ProductDTO> products 
		= (ArrayList<ProductDTO>) session.getAttribute("products");
%>
<table border="1">
	<tr>
		<th>상품</th>
		<th>가격</th>
		<th>장바구니 추가</th>
	</tr>
	<%
		// presentation logic
		if (products != null) {
			for (ProductDTO item : products) {
	%>
		<tr>
			<form method="post" action="/MVCPatternStudy/cart.do">
				<input type="hidden" name="command" value="CART"/>
				<input 
					type="hidden" 
					name="productName" 
					value="<%= item.getName() %>"
				/>
				<input 
					type="hidden" 
					name="price" 
					value="<%= item.getPrice() %>"
				/>
				<td><%= item.getName() %></td>
				<td><%= item.getPrice() %></td>
				<td><button type="submit">장바구니 추가</button></td>
			</form>
		</tr>
	<%
			}
		}
	%>
</table>
```

productList.jsp

기존 Model 1 방식과 비교했을 때 productList.jsp에서도 비즈니스 로직이 대부분 서블릿으로 사라져 프레젠테이션 로직에만 집중되어 있음을 볼 수 있다. 

productList.jsp에서 보여지는 물품들에서 장바구니 추가 버튼 클릭 시 사용자의 장바구니 목록에 추가되는 과정에도 다음과 같이 서블릿이 관여하게 되었다. 

```java
package controller;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import model.bean.Item;

@WebServlet("/cart.do")
public class ProductCartServlet extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		req.setCharacterEncoding("utf-8");
		
		// business, DB logic
		String command = req.getParameter("command");
		String productName = null;
		String priceStr;
		int price = -1;
		
		HttpSession session = req.getSession();

		if (command != null && command.equals("CART")) {
			List<Item> items = (ArrayList<Item>)session.getAttribute("items");
			
			productName = req.getParameter("productName");
			priceStr = req.getParameter("price");
			price = Integer.parseInt(priceStr);
			
			// 사용자가 선택한 물품 정보 담기
			Item selectedItem = new Item();
			selectedItem.setProductName(productName);
			selectedItem.setPrice(price);
			selectedItem.setEa(1);
			
			// 사용자가 선택한 물품을 장바구니에 담기.
			if (items == null) {
				items = new ArrayList<Item>();
				items.add(selectedItem);
			} else {
				if (items.contains(selectedItem)) {
					// 사용자가 선택한 물품이 이미 장바구니에 있을 경우 수량을 추가. 
					// 총 수량에 맞게끔 총 가격도 수정. 
					Item alreadyExistItem = items.get(items.indexOf(selectedItem));
					alreadyExistItem.setEa(
						alreadyExistItem.getEa() + selectedItem.getEa()
					);
					alreadyExistItem.setPrice(
						alreadyExistItem.getPrice() + selectedItem.getPrice()
					);
				} else {
					items.add(selectedItem);
				}
			}
			// 세션에 장바구니 정보 저장. 
			session.setAttribute("items", items);
		}
		
		// 포워딩으로 페이지 이동
		String targetUrl = "/shop.jsp";
		RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
		dispatcher.forward(req, resp);
	}
	
}

```

ProductCartServlet.java

```html
<%@page import="java.util.ArrayList"%>
<%@page import="model.bean.Item"%>
<%@page import="java.util.List"%>
<%@ page contentType="text/html; charset=UTF-8"%>
<%
	List<Item> items = (ArrayList<Item>) session.getAttribute("items");
%>
<!-- Presentation Logic -->
<h3>장바구니</h3>
<table border="1">
	<tr>
		<th>상품명</th>
		<th>수량</th>
		<th>총 가격</th>
	</tr>
	<%
		if (items != null) {
			for (Item oneItem : items) {
	%>
		<tr>
			<td><%=oneItem.getProductName()%></td>
			<td><%=oneItem.getEa()%></td>
			<td><%=oneItem.getPrice()%></td>
		</tr>
	<%
			}
		}
	%>
</table>
```

cartList.jsp

역시나 Controller 역할의 서블릿이 추가됨으로써 cartList.jsp의 코드가 한층 간결해짐을 알 수 있다. 

# 예제를 통해 살펴본 결론

앞서 아주 간단한 쇼핑몰 예제를 Model 1으로 구성해 본 후, 이를 Model 2로 바꿔보았다. 그 과정에서 우린 Model 2 방식에 대해 다음을 알게 되었다. 

- Controller 역할의 Servlet의 등장으로 인해 비즈니스 로직과 프레젠테이션 로직을 분리하여 별도로 관리할 수 있게 되었다. 이로 인해 기존 jsp의 코드도 줄어들어 코드 가독성도 확보하면서도 동시에 유지보수성도 확보할 수 있게 되었다.

이러한 장점 때문에 MVC 패턴이 생겨난 것임을 몸소 체감하게 되었다. 

그런데 아직 여기서 끝이 아니다. 위에서 살펴본 Model 2 방식에도 문제점이 존재하는데, 이는 “Front Controller Pattern”을 다루는 다른 페이지([[Servlet & JSP][MVC] Front Controller Pattern](/servlet%20&%20jsp/servlet-jsp-mvc-front-controller-pattern/) )에서 이어서 설명하겠다. 

---

References

[1] 에이콘아카데미 (강남) 강의

[2] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[3] [JSP Model1, JSP Model2, Spring MVC pattern](https://juyoungit.tistory.com/119)

[4] front controller pattern

[https://github.com/binghe819/TIL/blob/master/OOP&설계/디자인패턴/Front Controller Pattern.md](https://github.com/binghe819/TIL/blob/master/OOP&%EC%84%A4%EA%B3%84/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4/Front%20Controller%20Pattern.md)

[5] MVC

[모델-뷰-컨트롤러](https://ko.wikipedia.org/wiki/모델-뷰-컨트롤러?source=post_page-----1d74fac6e256--------------------------------)

[6] [[아키텍처 패턴] MVC 패턴이란?](https://medium.com/@jang.wangsu/디자인패턴-mvc-패턴이란-1d74fac6e256)

[7] [여기도 MVC, 저기도 MVC! MVC 패턴이 뭐야?](https://velog.io/@langoustine/여기도-MVC-저기도-MVC-MVC-패턴이-뭐야)

[8] [FrontController & Command Pattern - 프론트 컨트롤러와 커맨드 패턴](https://brewagebear.github.io/front-controller-and-command-pattern/#step-31-command-pattern)