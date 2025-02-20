---
title: "[Servlet & JSP][MVC] Front Controller Pattern"
category: "Servlet & JSP"
tag: ["Servlet", "JSP", "MVC", "Model", "Controller", "View", "Front Controller"]
---

이 페이지는 이전의 [[Servlet & JSP] MVC pattern](/servlet%20&%20jsp/servlet-jsp-mvc-pattern/) 페이지에서 이어서 계속됩니다. 따라서 아직 이 글을 읽지 않았다면 먼저 읽고 다시 여기로 오시는 것을 추천드립니다. 

# 기존 MVC의 문제점 - Page Controller

앞선 [[Servlet & JSP] MVC pattern](/servlet%20&%20jsp/servlet-jsp-mvc-pattern/) 페이지에서 필자는 기존의 model 1 방식의 예제를 model 2로 바꾸었다. 이를 통해 코드의 가독성과 유지보수성을 높이는 것을 목표로 하였다. 

그런데 아직 여기에서도 문제점이 존재한다. 

![그림 1-1. 이전의 Model 2 방식의 예제를 다이어그램으로 그려 각 파일들 간의 관계를 묘사한 그림이다. ](/images/2024-10-06/servlet-jsp-mvc-front-controller-pattern/1.png)

그림 1-1. 이전의 Model 2 방식의 예제를 다이어그램으로 그려 각 파일들 간의 관계를 묘사한 그림이다. 

위 그림의 구조 중 Controller 역할을 하는 servlet들에 집중하여 살펴보면 알겠지만, 대부분의 controller들은 하나의 특정 HTTP 요청만을 받고 있는 구조이다. 즉, “하나의 요청, 하나의 컨트롤러”로 정리할 수 있는 일대일 관계를 지닌다. 이렇게 웹 사이트에서 특정 페이지나 이벤트에 대한 요청만을 처리하는 controller를 page controller라고 부른다. 

이 글 마지막에 존재하는 References [6]에 해당하는 “[Java Design Pattern](https://java-design-patterns.com/patterns/page-controller/#detailed-explanation-of-page-controller-pattern-with-real-world-examples)”이란 사이트에서는 이 page controller를 일종의 “백화점”에 비유하여 설명하고 있는데, 여기에 나의 주관적인 생각을 섞어보면 다음과 같이 비유를 들 수 있겠다. 

> 이마트, 홈플러스와 같은 대형마트나 또는 백화점에 가보면 정말 다양한 코너가 있다. 식품 코너, 의류 코너, 전자기기 코너 등등 다양하다. 그리고 각 코너에는 일종의 카운터가 있다. 의류 코너의 카운터에서는 의류에 대한 것만 계산을 진행하고 의류와 관련된 교환, 반품도 여기서 진행될 것이다. 전자기기 코너에서도 마찬가지로 전자기기 구매, 교환, 반품 등의 작업들이 전자기기 코너의 카운터에서 진행될 것이다. 이 각 코너들은 서로에게 영향을 전혀 끼치지 않는다. 의류 코너는 의류에 대한 것만, 전자기기 코너는 전자기기에 대해서만 다룬다. 여기서 대형마트가 웹 사이트라면 각각의 코너에 있는 손님의 요청(request)을 맞이하는 카운터가 page controller에 해당한다.
> 

물론 웹 사이트는 규모가 조금만 커져도 사용자의 요청의 종류도 다양해질 것이다. 그렇다고 해서 둘 이상의 서로 다른 성격의 HTTP 요청을 하나의 서블릿에서 처리하게 하는 것도 유지보수적 측면에서 별로 좋지 않을 것이다. 만약 한 직원이 의류 코너와 전자기기 코너를 동시에 떠맡게 되면 오히려 일에 제대로 집중하지 못할 것이다. 따라서 의류 전담 직원, 전자기기 전담 직원 이렇게 따로 두는 것이 좋을 것이다. 이러한 관점에서는 page controller가 좋다. 

하지만 이러한 page controller에도 단점이 존재한다. 

- 웹 사이트 규모가 커지고 그에 따라 사용자의 요청 종류가 다양해지면 그만큼 page controller에 해당하는 서블릿 파일들의 개수가 너무 많아져 파일 관리가 어려워진다.
- page controller 간에 중복되는 코드들이 존재하게 된다. 이는 이전에 필자가 보인 “JeroCaller의 온라인 쇼핑몰!”의 servlet 예제 코드들을 보면 알 수 있을 것이다. 대부분의 서블릿 클래스에는 다른 페이지로 이동하는 forward 관련 코드가 있는데, 이 코드 패턴이 대부분의 서블릿에서 중복적으로 나타난다. 사용자 요청 정보 추출 코드도 중복되는 것을 볼 수 있다. (String id, String pw 부분)
- 각각의 page controller들을 하나로 통제할 시스템의 부재로 인해 모든 page controller를 제대로 관리하기가 힘들다. 각 page controller 간에 중복되는 코드가 존재하는 이유도 이것 때문이다. 이 중복되는 코드들을 일괄적으로 똑같이 변경해야 할 때 page controller의 수가 많을 수록 매우 귀찮은 직업이 될 것이다.

이러한 문제점을 해결해줄 디자인 패턴으로 Front Controller Pattern이 존재한다. 

# 기존 문제점의 대안 - Front Controller Pattern

Front Controller는 웹 사이트에서 발생하는 모든 사용자 요청을 한 곳으로 받는 대표 컨트롤러이다. 정말로 모든 사용자 요청을 이 front controller가 다 받는다. 마치 종합 병원에 들어가면 처음에 보는 접수처와도 같다. 아픈 곳이 내과이든 외과이든 피부 쪽이든 이비인후과 쪽이건 종합 병원에 처음 들어가면 접수처로 가는 것과 같은 이치이다. 그러면 접수처에서는 환자가 어디가 아픈지를 듣고 어느 과로 안내할 지를 결정하여 환자에게 적절한 치료를 받게끔 도와주는 것이다. front controller도 이와 똑같은 원리이다. 우선 사용자의 모든 요청을 한 곳으로 받은 뒤, 요청이 어떤 종류인지를 분석한 후, 이 요청을 처리할 적절한 model과 처리 로직을 선택하여 그 곳에 위임을 하는 과정을 거친다. (접수처가 실제로 모든 일을 하는 것은 아니듯이, front controller도 요청 처리를 직접 하는 것은 아니다) 

![그림 2-1. Page Controller VS Front Controller의 차이점을 그려보았다.](/images/2024-10-06/servlet-jsp-mvc-front-controller-pattern/2.png)

그림 2-1. Page Controller VS Front Controller의 차이점을 그려보았다.

Front controller는 모든 사용자의 요청을 한 곳으로 받는 유일한 진입점이 되고, 그렇기에 모든 요청 처리를 중앙화하는 특징을 가진다. 

이러한 Front Controller를 사용하면 다음의 장점을 챙길 수 있다. 

- 흩어져 있던 요청 처리 로직을 하나로 모아 관리하기가 수월해진다.
- 기존의 page controller 방식에서 발생했던 각 컨트롤러들에서의 중복되는 코드들을 하나로 합쳐 중복을 제거할 수 있게 된다.
- 요청 진입점이 단 하나이기에 비정상적인 요청이 들어왔을 때 일괄적인 처리가 수월해진다. 만약 page controller 방식이었다면 비정상적인 요청이 들어왔을 때 이에 대한 방지 코드를 각각의 page controller들에게 적용해야하는 수고로움이 있을 텐데, 이러한 단점을 없애준다.

물론 front controller pattern에도 단점이 존재한다고 한다. 

- 요청 진입점이 단 하나이기에 요청이 한꺼번에 몰릴 경우 병목 현상이 발생할 수 있다.
- 요청 종류가 많아질수록 front controller 내 코드가 복잡해지고 방대해질 수 있다. 이 문제점은 다음 페이지에서 언급할 command pattern과 factory pattern으로 어느 정도 상쇄시킬 수 있다고 생각한다.

# Front Controller Pattern으로 쇼핑몰 예제 대체하기

이제 이전 페이지에서 본 “JeroCaller의 온라인 쇼핑몰!” 예제에 front controller를 적용해보겠다. 다음과 같은 형태가 되도록 리팩토링 해볼 것이다. 

![그림 3-1. Front controller로 구성해본 “JeroCaller의 온라인 쇼핑몰!” 예제 계획도.](/images/2024-10-06/servlet-jsp-mvc-front-controller-pattern/3.png)

그림 3-1. Front controller로 구성해본 “JeroCaller의 온라인 쇼핑몰!” 예제 계획도.

물론 다음과 같이 Front Controller를 필두로 하여 트리 구조로 컨트롤러들을 구성해볼 수도 있다. 

![그림 3-2. 중간 컨트롤러를 둘 때의 상상도](/images/2024-10-06/servlet-jsp-mvc-front-controller-pattern/4.png)

그림 3-2. 중간 컨트롤러를 둘 때의 상상도

필자가 보인 쇼핑몰 예제에는 요청의 종류가 크게 “로그인-회원가입”과 “쇼핑몰 관련 작업”으로 나뉠 수 있기에 위 그림과 같이 구성할 수도 있다. 

여기서는 그림 3-1과 같이 front controller가 직접 각 요청에 작업을 위임하는 방식으로 해볼 것이다. 

먼저, 기존에 있던 Controller 역할의 모든 서블릿 클래스에 붙은 `@WebServlet` 어노테이션을 주석 처리한다. 그래야 Front Controller이 제대로 작동하기 때문이다. 그 후, `FrontController` 라는 이름의 새 서블릿 클래스를 다음과 같이 생성하였다. 

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
import model.db.ProductDAO;
import model.db.ProductDTO;
import model.db.UserDAO;
import model.db.UserDTO;

@WebServlet("*.do")
public class FrontController extends HttpServlet {

	@Override
	protected void doGet(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		doPost(req, resp);
	}

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		/*
		System.out.println("요청 URI) " + req.getRequestURI());
		System.out.println("요청 URL) " + req.getRequestURL());
		System.out.println("Servlet Path) " + req.getServletPath()); 
		*/
		req.setCharacterEncoding("utf-8");
		
		HttpSession session = req.getSession();
		String requestUri = req.getServletPath();  // 요청 서블릿 URI 추출.
		String targetUrl = "";
		
		// 전체 또는 일부 서블릿에서 공통적으로 나타나는 중복 코드를 여기서 선언 및 초기화. 
		String id = req.getParameter("id"); 
		String pw = req.getParameter("pw");
		UserDAO userDao = new UserDAO();
		UserDTO oneUser = new UserDTO();
		
		// 작업 처리 후 포워딩이 필요한지에 대한 변수. 
		// 포워딩 불필요 시 해당 로직에서 false로 지정.
		boolean goForward = true;
		
		switch(requestUri) {
			case "/login.do":
				targetUrl = "/login.html";
				break;
			case "/loginProc.do":
				oneUser.setId(id);
				oneUser.setPassword(pw);
				
				List<UserDTO> allUsers = userDao.getAllUsers();
				
				if (allUsers.contains(oneUser)) {
					// 로그인한 현재 사용자의 정보를 세션에 저장.
					session.setAttribute("userInfo", oneUser);
					
					targetUrl = "/shop.jsp";
				} else {
					targetUrl = "/loginFailed.jsp";
				}
				break;
			case "/signup.do":
				targetUrl = "/signup.html";
				break;
			case "/signupProc.do":
				// 회원 가입 로직 처리
				oneUser.setId(id);
				oneUser.setPassword(pw);
				
				boolean isSuccess = userDao.insertUser(oneUser);
				
				req.setAttribute("isSignUpSuccess", isSuccess);
				targetUrl = "/signupView.jsp";
				break;
			case "/products.do":
				// 사용자 요청 정보 추출
				String category = null;
				
				ProductDAO productDao = new ProductDAO();
				
				category = req.getParameter("category");
				if (category != null) {
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
				
				targetUrl = "/shop.jsp";
				break;
			case "/cart.do":
				String productName = null;
				String priceStr;
				int price = -1;

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
				
				targetUrl = "/shop.jsp";
				break;
			default:
				// 비정상적인 요청에 대한 안내 페이지로 이동하게끔 함. 
				targetUrl = "/unexpectedRequestPage.html";
		}
		
		if (!goForward) return;
		
		// 각 요청 처리 로직마다 존재하는 페이지 이동 코드를 여기에 정의하여 중복 코드 제거. 
		RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
		dispatcher.forward(req, resp);
		
	}
	
}

```

FrontController.java

먼저, `@WebServlet("*.do")` 라고 함으로써 모든 서블릿 요청을 받도록 고안하였다. 그러면 login.do로 요청이 오든 signup.do로 요청이 오든 모두 FrontController로 향할 수밖에 없다. 그 후, `String requestUri = req.getServletPath();` 를 통해 들어온 요청이 어떤 요청인지(login.do인지 signup.do인지)를 판별한 후, 각 요청 케이스마다 적절하게 요청을 처리할 수 있게끔 switch문을 사용하였다. 

또한 `String id, pw` 부분이나 포워딩 코드도 기존의 Page Controller 방식에서는 여러 Page Controller마다 중복되어 나타났는데, Front Controller를 이용하면 여러 요청을 처리할 수 있게 되면서도 중복 코드를 제거할 수 있음을 알 수 있다. 

참고로 여기에는 “command” 관련 코드를 제거하였다. Model 1 방식에서는 shop.jsp가 productList.jsp, shopCategory.jsp로부터의 요청을 거치는 곳이라 어떤 곳에서 어떤 요청이 오는지를 구분하기 위해 어쩔 수 없이 command 요청 파라미터를 사용할 수밖에 없었다. 그러나 여기서는 이미 서블릿을 통해 요청 종류를 분리할 수 있게 되었기에 command 요청 파라미터가 필요 없어져서 여기서는 삭제하였다. 기존에 command 요청 정보를 전송하던 jsp 또는 html 파일에서도 command 관련 코드는 삭제하였다. 

참고로 위 코드에서는 각 요청마다 login.do, products.do 처럼 서블릿 URI을 매핑했기에 요청 종류를 구분할 수 있는데, 사실 이전에 쓴 command 요청 파라미터를 이용해도 된다. 만약 이를 사용했다면 FrontController 클래스 내부는 대략 다음의 구조를 띄었을 것이다. 

```java
package controller;

// 생략

@WebServlet("*.do")
public class FrontController extends HttpServlet {

  // 생략

	@Override
	protected void doPost(HttpServletRequest req, HttpServletResponse resp) 
		throws ServletException, IOException {
		// 생략
		String command = req.getParameter("command");
		switch(command) {
			case "LOGIN":
				// 코드
				break;
			case "LOGIN_PROC":
				// 코드
				break;
			case "SIGNUP":
				// 코드
				break;
			case "SIGNUP_PROC":
				// 코드
				break;
			case "PRODUCTS":
				// 코드
				break;
			case "CART":
				// 코드
				break;
			default:
				//코드
		}
		
		// 생략
		
	}
	
}

```

그러면 요청 페이지 쪽에서는 `<input type="hidden" name="command" value="CART"/>` 와 같이 요청 정보를 전달하여 어떤 요청을 전송할 것인지를 명확하게 밝히도록 할 수 있다. 

FrontController 클래스를 앞선 코드와 같이 작성한 후 index.html을 실행하면 그대로 잘 실행된다. 

<div style="text-align:center; margin:1em;">
<video src="/resources/2024-10-06/servlet-jsp-mvc-front-controller-pattern/1.mp4"
  controls="controles"
  muted="muted"
  width="100%"
></video>
</div>

# 예제에서 사용된 Front Controller의 문제점

그런데 문제점은 여기서 끝이 아니다. 다시 FrontController.java 를 보면 알겠지만, 해당 서블릿은 말 그대로 요청의 종류를 분별하여 적절히 처리할 곳으로 전송하는 역할을 하는데, 막상 코드를 보면 각 요청에 대한 처리 로직을 그대로 넣어서 서블릿 클래스의 코드 양도 방대해지고, 사실상 Front Controller 내에서 요청을 직접 처리하는 것이나 마찬가지인 셈이 되어버렸다. 분기별 코드를 그대로 넣으니 코드 수정 시 엉뚱한 다른 기능의 코드도 건드릴 가능성이 있어 가독성과 유지보수성도 매우 떨어지는 것을 볼 수 있다. 

이 문제점을 해결하기 위해, 로직 자체를 메서드화 시키는 (즉, 로직 자체를 캡슐화하는) Command Pattern을 사용하는 것이 좋겠다. 그러면 Front Controller에서는 그저 로직이 담긴 메서드를 포함하는 command 클래스만 생성하고 그 메서드만 호출하면 되기에 코드 양도 줄어들고, 가독성과 유지보수성도 확보할 수 있게 된다. 

Command Pattern에 대한 자세한 이야기와 예제에 적용하는 과정에 대해선 다음 페이지에서 다룰 예정이다. 

---

References

[1] 에이콘아카데미 (강남) 강의

[2] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)

[3] page controller가 무엇인지에 대한 설명

[What is the Page-Controller pattern exactly?](https://stackoverflow.com/questions/4298037/what-is-the-page-controller-pattern-exactly)

[4] front controller pattern에 대한 설명. page controller에 대한 설명도 있다. 

[https://github.com/binghe819/TIL/blob/master/OOP&설계/디자인패턴/Front Controller Pattern.md](https://github.com/binghe819/TIL/blob/master/OOP&%EC%84%A4%EA%B3%84/%EB%94%94%EC%9E%90%EC%9D%B8%ED%8C%A8%ED%84%B4/Front%20Controller%20Pattern.md)

[5] page controller가 무엇인지에 대한 설명

[Page Controller](https://martinfowler.com/eaaCatalog/pageController.html)

[6] page controller에 대한 설명. 해당 페이지는 java에서의 design pattern에 대해 다루고 있어 전반적인 디자인 패턴 공부에도 도움이 될 것 같다. 

[Page Controller Pattern in Java: Centralizing Web Page Logic for Cleaner Design](https://java-design-patterns.com/patterns/page-controller/#detailed-explanation-of-page-controller-pattern-with-real-world-examples)

[7] front controller가 무엇인지를 설명. 

[Front Controller Pattern in Java: Centralizing Web Request Handling](https://java-design-patterns.com/patterns/front-controller/#also-known-as)

[8] Front controller와 command pattern

[FrontController & Command Pattern - 프론트 컨트롤러와 커맨드 패턴](https://brewagebear.github.io/front-controller-and-command-pattern/#step-21-frontcontroller-pattern)