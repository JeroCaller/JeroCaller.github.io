---
title: "[Servlet & JSP][MVC] Simple Factory Pattern"
category: "Servlet & JSP"
tag: ["Servlet", "JSP", "MVC", "Model", "Controller", "View", "Factory Pattern"]
---

# Factory Pattern

팩토리 패턴에는 크게 3가지의 패턴이 있다. 

- Simple Factory Pattern (심플 팩토리 패턴) - 디자인 패턴으로 보지 않는 것 같다. 하지만 여기서는 편의상 패턴이라고 하겠다.
- Factory Method Pattern (팩토리 메서드 패턴)
- Abstract Factory Pattern (추상 팩토리 패턴)

사실 진정한 팩토리 패턴은 팩토리 메서드, 추상 팩토리 패턴이 본격적인 패턴인 것 같다. 그러나 여기서는 심플 팩토리 패턴에 대해서만 소개하고, 나머지 두 패턴은 나중에 정리하도록 하겠다. 

# Simple Factory Pattern과 적용

Simple Factory Pattern은 아주 단순한 패턴으로, 객체 생성 로직을 별도의 클래스에 위임하는 패턴이다. [[Servlet & JSP][MVC] Command Pattern](/servlet%20&%20jsp/servlet-jsp-mvc-command-pattern/) 페이지에서 본 커맨드 패턴이 적용된 컨트롤러 클래스 코드를 다시 보겠다. 

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

위 코드를 보면 몇 가지 문제점이 있다. 

- 요청의 종류가 증가할수록 그에 대한 각각의 요청 처리 로직도 증가해야하기에 그에 따른 커맨드 클래스의 수도 증가할 것이다. 그러면 위 코드의 switch-case 문의 코드도 계속 증가할 것이다. 이는 “기능의 확장에는 열려있되, 기존 코드의 수정에는 닫혀있어야 한다”는 OCP(Open-Close Principle) 원칙에 위배된다.
- 어떤 클래스 내부에 다른 클래스의 객체를 직접 생성하는 것은 두 클래스 간의 결합도를 상승시키는 요인이다. A, B 클래스가 있고, A 클래스 내부에 B 객체를 직접 생성했다면, B 클래스 코드에 수정이 가해지면 자칫 A 클래스 내 기능이 잘 작동하지 않을 수도 있고, 이로 인해 A 클래스 내부 코드도 수정해야할 수 있다. 따라서 두 클래스 간의 결합도를 최소화하는 것이 좋다.

이를 위해서 위 코드에 Simple Factory Pattern을 적용해보겠다. 매우 간단하다. 커맨드 객체 생성 로직을 별도의 클래스로 옮기기만 하면 된다. 편의상 command 패키지 내부에 SimpleFactory.java 클래스를 생성하고, FrontController 클래스 내부에 있던 커맨드 객체 생성 코드를 해당 클래스에 그대로 옮겼다. 다음은 SimpleFactory 내 코드이다. 

```java
package command;

/**
 * 심플 팩토리 클래스. 
 * 팩토리 인스턴스 자체는 굳이 여러 개의 인스턴스를 만들 필요가 없으므로 
 * 싱글톤으로 구현해보았다. 
 */
public class SimpleFactory {
	private static SimpleFactory simpleFactory = new SimpleFactory();
	private SimpleFactory() {}
	public static SimpleFactory getInstance() {
		return simpleFactory;
	}
	
	public CommandInterface getCommand(String requestUri) {
		switch(requestUri) {
		  case "/login.do":
			  // LoginViewServlet
			  return new LoginViewCommand();
		  case "/loginProc.do":
			  // LoginProcServlet
			  return new LoginProcCommand();
		  case "/signup.do":
			  // SignUpViewServlet
			  return new SignUpViewCommand();
		  case "/signupProc.do":
			  // SignUpProcServlet
			  return new SignUpProcCommand();
		  case "/products.do":
			  // CategoryProductServlet
			  return new CategoryProductCommand();
		  case "/cart.do":
			  // ProductCartServlet
			  return new ProductCartCommand();
		  default:
			  // 비정상적인 요청에 대한 안내 페이지로 이동하게끔 함.
			  return new UnExpectedRequestViewCommand();
	  }
	}
}

```

SimpleFactory.java

```java
package controller;

import java.io.IOException;

import javax.servlet.RequestDispatcher;
import javax.servlet.ServletException;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;

import command.CommandInterface;
import command.SimpleFactory;

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
		SimpleFactory sFact = SimpleFactory.getInstance();
		commander = sFact.getCommand(requestUri);
		
		String targetUrl = commander.executeCommand(req, resp);
		
		if (targetUrl != null) {
			RequestDispatcher dispatcher = req.getRequestDispatcher(targetUrl);
			dispatcher.forward(req, resp);
		}
		
	}
	
}

```

FrontController.java

위와 같이 코드를 변경하였다. 변경된 코드를 통해 다음을 알 수 있다. 

- 커맨드 객체 생성 로직이 SimpleFactory라는 별도의 클래스로 이동되었다. 즉, 커맨드 객체 생성의 역할도 SimpleFactory 클래스에 위임하게 되었다. 이로 인해, 새로운 커맨드 클래스가 만들어져도 서블릿 내 코드에는 추가하거나 수정해야할 사항이 전혀 없다. 그저 SimpleFactory 클래스 내부에 case 문 하나만 더 추가하면 그만이다. 따라서 서블릿 입장에서는 OCP 원칙이 지켜지고 있다고 말할 수 있다.
- 커맨드 객체 생성 로직이 별도의 클래스로 위임되면서, 컨트롤러 역할의 서블릿은 본연의 역할인 “모든 종류의 HTTP 요청을 받는다”에 충실해질 수 있다. 심지어는 “요청 종류에 맞게 적절한 처리 로직 담당 클래스에 위임한다”라는 역할까지도 SimpleFactory 클래스가 대신 한다고 봐도 되겠다.

이처럼 Simple Factory 패턴으로도 몇몇 이점을 챙길 수 있음을 보았다. 하지만 Simple Factory만으로는 부족한 점도 있다. 

- SimpleFactory 클래스는 OCP 원칙이 지켜지지 않고 있다. 새로운 커맨드 클래스가 추가되면 해당 클래스 내에 case문이 더 추가되어야 하는 구조이기 때문이다.

사실 OCP와 커맨드, 팩토리 패턴을 살펴보면서 느낀 것은, 기능의 확장은 자유롭게 하고, 대신 기존 코드의 수정을 최소화하려면 역시 새로운 “모듈”이 추가되는 방식으로 진행되어야 한다는 것이다. 즉, 기능이 추가될 때마다 기존 클래스의 코드에 무언가를 수정하거나 추가하도록 하는 것이 아니라, 새로운 클래스가 생성되도록 해야 한다는 것이다. 커맨드 패턴이 딱 그 예이다. “코드가 늘어나는 게 아니라 모듈이 늘어나게 해라.” 그래서 Simple Factory Pattern의 단점인 OCP 원칙 위반 문제를 해결하려면 추상화된 팩토리 인터페이스를 하나 선언한 후, 구상 팩토리 클래스에서 이를 구현하도록 하는 것이 좋을 것이다. “추상 팩토리 패턴”도 이 방식을 채택하고 있는 것 같다. 제대로 된 (?) 패턴인 팩토리 메서드 패턴과 추상 팩토리 패턴은 나중에 다른 페이지에서 정리해보도록 해보겠다. 

> 이 페이지에서 다룬 Simple Factory pattern이 적용된 “JeroCaller의 온라인 쇼핑몰!” 예제 소스 코드는 다음의 사이트에서 확인 가능하다.
> 
> 
> [servlet-jsp-study/MVCPatternStudy/src/main at main · JeroCaller/servlet-jsp-study](https://github.com/JeroCaller/servlet-jsp-study/tree/main/MVCPatternStudy/src/main)
> 

---

References

[1] 에이콘아카데미(강남) 강의

[2] [Factory 패턴 (1/3) - Simple Factory](https://bcp0109.tistory.com/366)

[3] [[디자인 패턴] 팩토리 패턴 종류/개념/예제](https://cjw-awdsd.tistory.com/54)

[4] [💠 완벽하게 이해하는 OCP (개방 폐쇄 원칙)](https://inpa.tistory.com/entry/OOP-💠-아주-쉽게-이해하는-OCP-개방-폐쇄-원칙)

[5] [개방-폐쇄 원칙](https://ko.wikipedia.org/wiki/개방-폐쇄_원칙)