---
title: "[Servlet & JSP][MVC] Command Pattern"
category: "Servlet & JSP"
tag: ["Servlet", "JSP", "MVC", "Model", "Controller", "View", "Command Pattern"]
---

이 글은 이전의 [[Servlet & JSP][MVC] Front Controller Pattern](/servlet%20&%20jsp/servlet-jsp-mvc-front-controller-pattern/) 글에 이어서 작성된 글입니다. 따라서 아직 해당 글을 읽지 않으셨다면 먼저 읽고 오시는 것을 추천드립니다. 

# 기존 Front Controller에서의 문제점

이전의 Front Controller를 다루는 페이지에서 FrontController.java 의 코드를 다시보자. 

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

위 코드의 문제점은 다음과 같다. 

- Front Controller는 본래 모든 요청을 한 곳으로 받아 이를 대신 처리할 클래스에 위임하는 역할을 하지, 직접 요청 처리 로직을 구성하여 실행하는 역할이라 보기 어렵다. 그럼에도 위 코드에서는 Front Controller 클래스 내부에 요청 처리 로직이 그대로 들어있다. 사실상 Front Controller가 요청 처리를 직접 하는 것과 마찬가지라고 볼 수 있다. 즉, 제대로 된 역할 분담이 되어있다고 보기 어려우며, 따라서 요청 처리 로직을 다루는 클래스를 따로 만들어 요청 처리를 Front Controller가 위임하게 하는 것이 좋다.
- 요청 분기가 많아지거나 요청 처리 로직 코드의 양이 많아질수록 요청 분기를 구분하기 힘들어진다. 가독성이 저해되며, 이로 인해 특정 요청 처리 로직만 수정하려다가 실수로 다른 요청 처리 로직을 건드려 의도치 않은 버그 또는 에러의 원인이 될 수 있다.

따라서 FrontController 클래스 내부에 직접 요청 처리 로직을 작성하는 것은 옳지 않음을 알 수 있다. 그러면 어떻게 해야할까? 아무래도 요청 처리 로직을 FrontController 클래스로부터 분리하는 것이 좋을 것이며, 또한 요청의 종류도 프로젝트 규모가 커질수록 많을 것이므로 각각의 요청 처리 로직도 따로 분리하는 게 좋을 것이다. 다음과 같이 말이다.

![그림 1-1. Front Controller 클래스 내 요청 처리 로직을 따로 분리하는 구상도.](/images/2024-10-08/servlet-jsp-mvc-command-pattern/1.png)

그림 1-1. Front Controller 클래스 내 요청 처리 로직을 따로 분리하는 구상도.

이를 수행할 수 있는 디자인 패턴으로 Command Pattern이 존재한다. 

# Command Pattern

command pattern은 사용자로부터의 요청을 처리하기 위한 로직 자체를 객체 형태로 캡슐화하는 디자인 패턴이다. 앞선 그림 1-1에서의 묘사처럼 각각의 요청 처리 로직들을 각각의 자바 클래스로 분리하는 것이다. 그러면 Front Controller에서는 직접 요청 처리 로직 코드들을 짤 필요없이, 그저 요청 처리 코드가 들어있는 커맨드 클래스만 생성하여 실제로 로직이 들어있는 메서드만 호출하면 그만이다. 그러면 아무리 요청의 종류가 늘어나 그에 대한 요청 처리 로직 코드를 추가해야 한다고 해도 Front Controller 내부 코드의 양이 그리 많이 늘어나지 않고(늘어나봤자 커맨드 객체 생성 코드 한 줄만 추가되는 것이다. 로직 코드 자체를 Front Controller 안에 그대로 넣는 방식에 비하면 현저히 코드량이 줄어드는 것이다), 모든 요청 처리 로직을 따로 관리할 수 있기에 가독성과 유지보수성을 확보할 수 있다. 

command pattern을 적용하려면 요청 처리 로직을 캡슐화하는 커맨드 클래스들이 공통의 인터페이스를 구현하도록 하는 것이 좋다. 만약 그렇지 않으면 요청 분기마다 요청 처리 로직이 담긴 서로 다른 커맨드 객체들을 하나의 참조 변수로 담을 방법이 없기 때문이다. 인터페이스 구현 방법을 택하지 않으면 다음과 같을 것이다. 

```java
// 의사코드
public class FrontController {
  protected void doPost(...) {
    // 코드 생략
    String command = req.getParameter("command");
        
    // 이러면 커맨드 객체 생성 코드가 늘어나는데...?
    LoginCommand login = null;
    SignUpCommand signup = null;
    ProductCommand product = null;
    ...
    switch (command)  {
      case "LOGIN":
	      login = new LoginCommand();
	      break;
	    case "SIGNUP":
	      signup = new SignUpCommand();
	      break;
	    case "PRODUCT":
			  product = new ProductCommand();
			  break;
			...
    }
        
    // 어라? 이렇게 각 커맨드 객체의 메서드명이 통일되지 않으면 
    // 나중에 또 새로운 커맨드 객체를 추가할 때마다 
    // 코드의 양이 불어날텐데...?
    if (login != null) {
      login.doLogin();
    } else if (signup != null) {
      signup.executeLogin();
    } else if (...) {
	    ...
    }
    ...
  }
} 
```

위와 같이 커맨드 객체가 늘어날수록 코드의 양이 늘어나므로 사실상 커맨드 패턴의 효과를 볼 수 없다. 따라서 모든 커맨드 클래스들은 공통의 인터페이스를 구현하도록 해야 한다. 

# Command Pattern으로 예제 코드 수정하기

기존의 “JeroCaller의 온라인 쇼핑몰!” 예제 코드를 command pattern으로 바꿔보겠다. 

이를 위해 src/main/java 폴더에 command 패키지를 만든 후, 먼저 다음과 같이 구상 커맨드 클래스들이 공통으로 구현하게될 인터페이스를 정의하였다. 

```java
package command;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

public interface CommandInterface {
	/**
	 * 
	 * @param req
	 * @param resp
	 * @return 포워딩 대상 URI
	 * @throws ServletException
	 * @throws IOException
	 */
	String executeCommand(HttpServletRequest req, HttpServletResponse resp)
		throws ServletException, IOException;
}

```

CommandInterface.java

그 후, 각 요청에 따른 처리 로직을 커맨드 클래스로 각각 다음과 같이 캡슐화하여 정의하였다. 

```java
package command;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 로그인 페이지로 이동하는 커맨드 클래스. 
 */
public class LoginViewCommand implements CommandInterface {

	@Override
	public String executeCommand(HttpServletRequest req, HttpServletResponse resp)
		throws ServletException, IOException {
		return "/login.html";
	}
	
}

```

LoginViewCommand.java

```java
package command;

import java.io.IOException;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import model.db.UserDAO;
import model.db.UserDTO;

/**
 * 로그인 인증 로직 처리
 */
public class LoginProcCommand implements CommandInterface {

	@Override
	public String executeCommand(HttpServletRequest req, HttpServletResponse resp)
		throws ServletException, IOException {
		String targetUrl = null;
		
		String id = req.getParameter("id"); 
		String pw = req.getParameter("pw");
		
		HttpSession session = req.getSession();
		
		UserDAO userDao = new UserDAO();
		UserDTO oneUser = new UserDTO();
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
		
		return targetUrl;
	}
	
}

```

LoginProcCommand.java

```java
package command;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

/**
 * 회원가입 페이지로 이동하는 커맨드 클래스. 
 */
public class SignUpViewCommand implements CommandInterface {

	@Override
	public String executeCommand(HttpServletRequest req, HttpServletResponse resp)
		throws ServletException, IOException {
		return "/signup.html";
	}
	
}

```

SignUpViewCommand.java

```java
package command;

import java.io.IOException;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import model.db.UserDAO;
import model.db.UserDTO;

/**
 * 회원 가입 로직 처리 커맨드. 
 */
public class SignUpProcCommand implements CommandInterface {

	@Override
	public String executeCommand(HttpServletRequest req, HttpServletResponse resp)
		throws ServletException, IOException {
		String id = req.getParameter("id"); 
		String pw = req.getParameter("pw");
		
		UserDAO userDao = new UserDAO();
		UserDTO oneUser = new UserDTO();
		
		// 회원 가입 로직 처리
		oneUser.setId(id);
		oneUser.setPassword(pw);
		
		boolean isSuccess = userDao.insertUser(oneUser);
		
		req.setAttribute("isSignUpSuccess", isSuccess);
		return "/signupView.jsp";
	}
	
}

```

SignUpProcCommand.java

```java
package command;

import java.io.IOException;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import model.db.ProductDAO;
import model.db.ProductDTO;

/**
 * 사용자가 특정 카테고리 클릭 시 해당 카테고리에 있는 모든 물품 데이터를 
 * 화면에 출력할 수 있도록 세션에 저장하는 커맨드. 
 */
public class CategoryProductCommand implements CommandInterface {

	@Override
	public String executeCommand(HttpServletRequest req, HttpServletResponse resp)
		throws ServletException, IOException {
		// 사용자 요청 정보 추출
		String category = null;
		ProductDAO productDao = new ProductDAO();
		category = req.getParameter("category");
		HttpSession session = req.getSession();
		
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
		
		return "/shop.jsp";
	}
	
}

```

CategoryProduct.java

```java
package command;

import java.io.IOException;
import java.util.ArrayList;
import java.util.List;

import javax.servlet.ServletException;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import javax.servlet.http.HttpSession;

import model.bean.Item;

/**
 * 물품 목록 중 사용자가 특정 물품의 "장바구니" 버튼 클릭 시 
 * 사용자의 장바구니 목록에 추가하는 커맨드. 
 */
public class ProductCartCommand implements CommandInterface {

	@Override
	public String executeCommand(HttpServletRequest req, HttpServletResponse resp)
		throws ServletException, IOException {
		String productName = null;
		String priceStr;
		int price = -1;
		
		HttpSession session = req.getSession();
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
		
		return "/shop.jsp";
	}
	
}

```

ProductCartCommand.java

위 커맨드 클래스들을 이용하면 FrontController 클래스 내부 코드는 다음과 같이 이전보다 훨씬 더 간결해진다. 

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

import command.CategoryProductCommand;
import command.CommandInterface;
import command.LoginProcCommand;
import command.LoginViewCommand;
import command.ProductCartCommand;
import command.SignUpProcCommand;
import command.SignUpViewCommand;
import command.UnExpectedRequestViewCommand;
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
		req.setCharacterEncoding("utf-8");

		String requestUri = req.getServletPath();  // 요청 서블릿 URI 추출.
		
		CommandInterface commander = null;
		
		switch(requestUri) {
			case "/login.do":
				// LoginViewServlet
				commander = new LoginViewCommand();
				break;
			case "/loginProc.do":
				// LoginProcServlet
				commander = new LoginProcCommand();
				break;
			case "/signup.do":
				// SignUpViewServlet
				commander = new SignUpViewCommand();
				break;
			case "/signupProc.do":
				// SignUpProcServlet
				commander = new SignUpProcCommand();
				break;
			case "/products.do":
				// CategoryProductServlet
				commander = new CategoryProductCommand();
				break;
			case "/cart.do":
				// ProductCartServlet
				commander = new ProductCartCommand();
				break;
			default:
				// 비정상적인 요청에 대한 안내 페이지로 이동하게끔 함.
				commander = new UnExpectedRequestViewCommand();
		}
		
		String targetUrl = commander.executeCommand(req, resp);
		
		if (targetUrl != null) {
			RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
			dispatcher.forward(req, resp);
		}
		
	}
	
}

```

FrontController.java

Command Pattern을 이용하니 이전과 비교하여 다음의 장점들이 생겼다. 

- Front Controller 내부 코드가 이전보다 훨씬 간결해져 가독성이 향상되었다.
- 요청 처리 로직이 각각의 커맨드 객체로 분리되니 Front Controller의 본래 역할인 “요청 처리 위임”에 더 충실해졌다.
- 각 요청별 처리 로직을 각각의 커맨드 객체의 메서드로 정의하니 특정 요청 처리 로직을 수정해야할 때 다른 요청 처리 로직을 건드리는 실수를 현저히 낮출 수 있다.

또한, 커맨드 클래스들이 공통의 커맨드 인터페이스를 구현하도록 한 결과, FrontController 클래스 내부에서 `commander` 라는 공통의 변수 하나로도 요청 분기별로 다양한 커맨드 객체들의 참조를 받을 수 있다는 장점도 있다. 즉, Command Pattern도 다형성(polymorphism)을 이용한 디자인 패턴의 예라고 볼 수 있다. 

개인적으로 아쉬운 점은, 원래 Front Controller라는 하나의 클래스에 모여 있던 요청 처리 로직들이 각기 다른 커맨드 클래스로 분리되니 `String id, pw`  또는 DAO, DTO 객체 생성 코드가 각 커맨드 클래스마다 중복적으로 나타난다는 것이다. 이는 Page Controller 방식에서 나타났던 똑같은 문제점이다. 이는 내가 아직 더 좋은 방법을 생각해내지 못한 것일 수도 있겠지만, 이러한 단점이라도 Front Controller 내의 코드의 간결성 확보, Front Controller 본연의 역할에 충실해졌다는 장점이 더 크다고 느껴 Command Pattern을 쓰는 게 더 좋다는 판단이 든다. 

# 아직 남아있는 과제

그러나 아직 FrontController 클래스 내부에는 문제점이 존재한다. 사용자 요청의 종류가 더 많아진다면 그만큼 더 많은 커맨드 객체들의 생성 코드가 계속 추가가 될 것이다. 이건 이것대로 코드의 양이 증가하는 셈이다. 또한 하나의 클래스 내부에 다른 클래스의 객체를 생성하는 것은 두 클래스 간의 의존도를 높여 좋지 않은 방법이라고 한다. 따라서 이에 대한 대안으로 Factory Pattern이 존재한다. MVC에 이 패턴을 적용하는 예는 다음 페이지에서 살펴보겠다. 

# 내 생각

사실 커맨드 패턴에 대해 조사해보니 invoker, receiver 등의 여러 개념이 나왔다. 이 글에서는 “로직을 캡슐화”하는 것에 초점을 맞춰 설명하다보니 해당 개념들을 생략하였고, 솔직히 아직까지는 invoker, receiver 등의 개념을 MVC 패턴에 어떻게 적용할지 몰라 생략한 것도 있다. 팩토리 패턴도 마찬가지로 종류가 여러 가지가 있는 듯 하다. 심플 팩토리 패턴, 팩토리 메서드 패턴, 추상 팩토리 패턴 이렇게 나뉘는 것 같다. MVC에서 내가 적용하고자 하는 패턴은 심플 팩토리 패턴에 해당하는 것 같다. 이런 상세한 개념들까지는 아직 제대로 이해하지 못하였기에, 커맨드 패턴의 좀 더 상세한 개념들과 여러 팩토리 패턴에 대해서는 나중에 디자인 패턴을 공부할 때 제대로 정리해야겠다. 지금은 MVC 패턴 예제에서 보여지는 문제점을 해결하는 데에 초점을 맞출 것이라 디자인 패턴의 여러 개념들이 생략될 수 있으니 참고 바란다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] [FrontController & Command Pattern - 프론트 컨트롤러와 커맨드 패턴](https://brewagebear.github.io/front-controller-and-command-pattern/#step-21-frontcontroller-pattern)

[3] [커맨드 패턴](https://ko.wikipedia.org/wiki/커맨드_패턴)

[4] [커맨드 패턴](https://refactoring.guru/ko/design-patterns/command)

[5] [Command Pattern in Java: Empowering Flexible Command Execution](https://java-design-patterns.com/patterns/command/#also-known-as)

[6] [Sup2's blog-커맨드(Command) 패턴 Feat.Java](https://sup2is.github.io/2020/07/02/command-parttern.html)

[7] [MVC 패턴 구현(2) : 모델 2 구조를 이용한 MVC 패턴(2)](https://velog.io/@jsj3282/MVC-패턴-구현2-모델-2-구조를-이용한-MVC-패턴2)

[8] factory pattern 

[Factory 패턴 (1/3) - Simple Factory](https://bcp0109.tistory.com/366)

[9] factory pattern

[[디자인 패턴] 팩토리 패턴 종류/개념/예제](https://cjw-awdsd.tistory.com/54)