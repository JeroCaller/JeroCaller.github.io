---
title: "[Spring Boot] Spring MVC Annotations"
category: "Spring"
tag: ["Spring", "Java", "MVC", "Annotation"]
---

# Application 관련

## `@SpringBootApplication`

(프로젝트명)Application.java 파일에서 해당 어노테이션을 볼 수 있다. 

```java
package com.jerocaller;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class SpringMvcAnnotationsApplication {

	public static void main(String[] args) {
		SpringApplication.run(SpringMvcAnnotationsApplication.class, args);
	}

}

```

SpringMvcAnnotationsApplication.java

해당 어노테이션의 내용은 다음과 같이 생겼다. 

```java
@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@Inherited
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(excludeFilters = { @Filter(type = FilterType.CUSTOM, classes = TypeExcludeFilter.class),
		@Filter(type = FilterType.CUSTOM, classes = AutoConfigurationExcludeFilter.class) })
public @interface SpringBootApplication
```

SpringBootApplication.class

`@ComponentScan` 어노테이션을 통해 해당 어노테이션이 적용된 메인 애플리케이션 클래스가 포함된 패키지 내 모든 bean (`@Component` 등으로 bean임을 명시한 클래스들) 들을 스캔해 스프링 컨테이너에 등록시킨다. 위 코드의 경우, `com.jerocaller` 패키지 내 모든 클래스들을 스캔한다. 이로 인해 만약 해당 패키지 바깥에 bean을 만든 경우 기본적으로 이를 스캔하지 못하는 것에 주의해야 한다. 

또한 `@EnableAutoConfiguration` 어노테이션을 통해 여러 가지 환경 설정들을 자동으로 진행시켜준다. 

# Controller 및 HTTP Request & Response 관련

## `@Controller`

적용 수준 : 클래스 

해당 클래스가 컨트롤러임을 명시한다. 만약 해당 어노테이션만 적용하고, 해당 클래스 내 메서드에 `@ResponseBody` 어노테이션을 적용하지 않는다면 해당 메서드들은 View의 URI를 반환한다. 즉, SSR(Server Side Rendering) 방식을 통해 서버에서 응답 “페이지”를 생성하여 클라이언트에 전송하고자 할 때 사용된다고 보면 된다. 

## `@RequestMapping`

적용 수준 : 클래스, 메서드 

클라이언트로부터 특정 HTTP 요청 URI를 받을 때 사용한다. 

인자

- value : 받고자 하는 특정 요청 URI. 예) ‘/login’
- method : 받고자 하는 특정 HTTP 메서드. `RequestMethod` 의 `GET, POST` 등의 상수를 사용한다.

다음은 해당 어노테이션을 적용한 예이다.

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class MyController {
	
	@RequestMapping(value = "/", method = RequestMethod.GET)
	public String goToIndex() {
		return "index";
	}
}

```

예제 1-1

위 예제의 경우, View를 사용하고 있는 구조이기에 컨트롤러 내 메서드는 이동하고자 하는 페이지의 파일명을 반환하면 된다. 그러면 해당 페이지로 이동하도록 할 수 있다. 위 코드의 경우, 브라우저 URL 창에서 `http://localhost/` 을 입력하면 index.html으로 이동할 수 있다. 

한 편, 해당 메서드가 반환하는 문자열 형태의 파일명은 기본적으로는 forward 방식이다. 만약 redirect 방식으로 이동하고자 한다면 “redirect:/index” 와 같이 명시해야 한다. 

`@RequestMapping` 어노테이션은 메서드 수준에서뿐만 아니라 클래스 수준에서도 설정 가능하다. 이 경우, 해당 클래스 내 모든 메서드들에 각자 할당될 요청 URI의 공통 부모 요청 URI를 설정할 수 있다. 다음의 예를 보겠다. 

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;

@Controller
@RequestMapping("/mypage")
public class MyPageController {
	
	@GetMapping("/myhome")
	public String goToMyHome(Model model) {
		model.addAttribute("msg", "나의 홈페이지");
		return "myhome";
	}
	
	@GetMapping("/config")
	public String goToConfig(Model model) {
		model.addAttribute("msg", "홈페이지 환경 설정");
		return "config";
	}
	
	@GetMapping("")   //  domain/mypage
	public String goToMyPage(Model model) {
		model.addAttribute("msg", "마이 페이지");
		return "mypage";
	}
}

```

예제 1-2

위 컨트롤러에 따르면 각각 `/mypage/myhome` , `/mypage/config` , `/mypage` 의 요청 URI을 받아 처리하는 메서드가 된다. 즉 컨트롤러 클래스 수준으로 매핑한 요청 URI인 `/mypage` 는 그 아래의 모든 메서드들의 공통 URI가 된다. 다음은 위 컨트롤러를 기반으로 만든 간단한 웹 페이지들이다. 브라우저 URL을 보면 앞서 설명한 내용들을 확인할 수 있을 것이다. 

![스크린샷 2024-10-29 093124.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/1.png)

![스크린샷 2024-10-29 093129.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/2.png)

![스크린샷 2024-10-29 093133.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/3.png)

![스크린샷 2024-10-29 093139.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/4.png)

단, `@RequestMapping` 어노테이션을 메서드 수준에서 적용하고자 할 때에는 대신 `@GetMapping`, `@PostMapping` 등 `@(HTTP 메서드명)Mapping` 어노테이션을 대신 사용하는 것이 좋다고 한다. 

## `@RequestParam`

적용 수준 : 매개변수 

`@RequestParam` 어노테이션은 사용자로부터 넘어오는 요청 정보를 받을 때 사용하는 어노테이션이다. 예를 들어 “/login” 이라는 요청 URI이 있고 그 뒤에 요청 정보가 쿼리 스트링 형태로 “/login?id=abc&password=123”가 붙어 있다면, 이를 컨트롤러 클래스의 메서드가 매개변수 형태로 캐치하게 할 수 있다. 다음은 컨트롤러 내 메서드에서 사용자의 요청 정보를 받는 예제이다. 

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class RequestParamController {
	
	@GetMapping("/login")
	public String loginProcess(
			@RequestParam("id") String id, 
			@RequestParam("password") String password,
			Model model
	) {
		// key 이름과 value 이름이 꼭 같을 필요는 없음.
		// key, value 모두 "id"인 건 우연일 뿐.
		model.addAttribute("id", id);
		model.addAttribute("pw", password);
		return "/param/showParam";
	}
}

```

예제 2-1

다음은 위 컨트롤러에서 요청 정보를 잘 추출하여 받았는지 확인하기 위한 용도의 html 파일 내용이다. 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h1>입력 정보</h1>
	<p th:text="${id}"></p>
	<p th:text="${pw}"></p>
</body>
</html>
```

예제 2-2. templates.param 패키지 내부의 showParam.html

스프링 부트 앱 실행 후 브라우저 URL 입력창에 `localhost:8080/login?id=abc&password=123` 이라고 입력하면 컨트롤러 메서드의 매개변수에 `@RequestParam` 어노테이션이 적용된 각각의 매개변수에 key-value 쌍의 요청 데이터 중 value가 전달된다. 이 요청 정보를 `model.addAttribute()` 메서드를 통해 request context에 저장하고 이를 view에 전송함으로써 view에서 해당 데이터를 다음과 같이 보여줄 수 있는 것이다. 

![1.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/5.png)

`@RequestParam()` 어노테이션의 매개변수에는 요청 정보 데이터의 key 이름을 문자열 형태로 입력한다. `@RequestParam("id")` 와 같이 말이다. 이렇게 하면 해당 어노테이션이 적용된 메서드의 매개변수의 변수명은 요청 데이터의 key 이름과 반드시 같을 필요는 없다. 

References [2]의 서적 내용에 따르면, 메서드 매개변수의 변수명이 요청 데이터의 key 이름과 동일하다면 `@RequestParam` 어노테이션에 아무런 인자도 적지 않아도 된다고 한다. 앞서 든 위 예제를 예로 들면 다음과 같다. 

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class RequestParamController {
	
	@GetMapping("/login")
	public String loginProcess(
			@RequestParam String id,   // 어노테이션에 인자 안 줌.
			@RequestParam String password,  // 어노테이션에 인자 안 줌.
			Model model
	) {
		// key 이름과 value 이름이 꼭 같을 필요는 없음.
		// key, value 모두 "id"인 건 우연일 뿐.
		model.addAttribute("id", id);
		model.addAttribute("pw", password);
		return "/param/showParam";
	}
}

```

예제 2-3. 

그런데 위와 같이 하면 브라우저 창에서 다음과 같은 에러 메시지가 출력된다. 

```java
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.

Tue Oct 29 22:05:32 KST 2024
There was an unexpected error (type=Internal Server Error, status=500).
Name for argument of type [java.lang.String] not specified, and parameter name information not available via reflection. Ensure that the compiler uses the '-parameters' flag.
java.lang.IllegalArgumentException: Name for argument of type [java.lang.String] not specified, and parameter name information not available via reflection. Ensure that the compiler uses the '-parameters' flag.
	at org.springframework.web.method.annotation.AbstractNamedValueMethodArgumentResolver.updateNamedValueInfo(AbstractNamedValueMethodArgumentResolver.java:186)
	at org.springframework.web.method.annotation.AbstractNamedValueMethodArgumentResolver.getNamedValueInfo(AbstractNamedValueMethodArgumentResolver.java:161)
	at org.springframework.web.method.annotation.AbstractNamedValueMethodArgumentResolver.resolveArgument(AbstractNamedValueMethodArgumentResolver.java:107)
	at org.springframework.web.method.support.HandlerMethodArgumentResolverComposite.resolveArgument(HandlerMethodArgumentResolverComposite.java:122)
	at org.springframework.web.method.support.InvocableHandlerMethod.getMethodArgumentValues(InvocableHandlerMethod.java:224)
	at org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:178)
	at org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:118)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:926)
	at org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:831)
	at org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)
	at org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1089)
	at org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:979)
	at org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1014)
	at org.springframework.web.servlet.FrameworkServlet.doGet(FrameworkServlet.java:903)
	at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:564)
	at org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:885)
	at jakarta.servlet.http.HttpServlet.service(HttpServlet.java:658)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:195)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140)
	at org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:51)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140)
	at org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140)
	at org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140)
	at org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)
	at org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:116)
	at org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:164)
	at org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:140)
	at org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:167)
	at org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:90)
	at org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:483)
	at org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:115)
	at org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:93)
	at org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:74)
	at org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:344)
	at org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:384)
	at org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:63)
	at org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:905)
	at org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1741)
	at org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:52)
	at org.apache.tomcat.util.threads.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1190)
	at org.apache.tomcat.util.threads.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:659)
	at org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:63)
	at java.base/java.lang.Thread.run(Thread.java:1583)
```

현재 이 에러의 원인은 파악하지 못했다. 만약 이러한 에러가 난다면 `@RequestParam("key")` 와 같이 해당 어노테이션에 요청 데이터 key 이름을 문자열 형태로 넣어 생략하지 않는 것이 좋겠다. 

한 편, 만약 사용자의 요청 정보 형태가 정해지지 않고 동적으로 변할 때가 있을 수 있다. 예를 들면 어떨 땐 사용자가 “myfood=burger”와 같은 정보를 입력할 때도 있지만 또 어떨 떈 “myfood”라는 정보 자체를 입력하지 않을 수도 있다. 이러한 상황과 더불어 모든 요청 정보를 받고자 할 때 컨트롤러 메서드 내 매개변수 타입을 `Map<String, String>` 으로 주면 된다. 이 때에는 `@RequestParam` 에 인자를 적지 않는다. 다음이 그 예이다. 

```java
package com.jerocaller.controller;

import java.util.Map;
import java.util.Set;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class ParamTestController {
	
	//@Autowired
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@GetMapping("/something")
	/**
	 * 사용자가 어떤 요청 정보를 주던 동적으로 받을 수 있게 되었다. 
	 * 
	 * @param allParams
	 * @return
	 */
	public String something(
			@RequestParam Map<String, String> allParams
			//@RequestParam("param") Object param
	) {
		logger.info("hi");
		//logger.info("param : " + param);
		logAllParams(allParams);
		return "index";
	}
	
	private void logAllParams(Map<String, String> allParams) {
		logger.info("======================================");
		Set<String> keys = allParams.keySet();
		keys.forEach(oneKey -> {
			String oneRecord = String.format(
					"key : %s, value : %s", 
					oneKey, 
					allParams.get(oneKey)
			);
			logger.info(oneRecord);
		});
		logger.info("======================================");
	}
}

```

예제 2-4

브라우저에서 요청 정보를 아무렇게나 준 뒤, 이클립스의 콘솔창을 봐보자. 

![2.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/6.png)

![1.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/7.png)

![3.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/8.png)

![4.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/9.png)

key-value 쌍의 요청 데이터의 이름, 개수에 상관없이 모든 요청 정보를 받을 수 있음을 확인하였다. 

## `@PathVariable`

적용 수준 : 매개변수

사용자로부터 전달받은 요청 정보 형태가 `?key1=value1&key2=value2` 의 쿼리 스트링 형식이 아니라 `/key1/value1/key2/value2` 의 URI 형태일 때 해당 요청 정보들을 추출할 때 사용하는 어노테이션으로, 사용 방법은 `@RequestParam` 과 거의 동일하다. 다만 `@GetMapping` 등 요청 URI 매핑 관련 어노테이션 사용 시 해당 어노테이션에 명시할 요청 URI에 실제 사용자가 입력한 값이 들어갈 자리에 중괄호를 이용하여 `{user}` 와 같이 작성한 후, 메서드 매개변수에 `@PathVariable` 을 거는 것이 다를 뿐이다. 다음은 이 설명을 보여주는 예제이다. 

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class RequestParamController {

	// 생략
	
	@GetMapping("/userinfo/name/{userName}/job/{userJob}")
	public String extractUserInfo(
			@PathVariable("userName") String uname,
			@PathVariable("userJob") String ujob, 
			Model model
	) {
		model.addAttribute("name", uname);
		model.addAttribute("job", ujob);
		return "/param/userInfo";
	}
}

```

예제 3-1

그리고 브라우저 창의 URL 입력 창에 정해진 형식에 맞게 요청 URI 형태로 요청 정보를 싣고 엔터키를 입력하면 다음과 같이 요청 정보를 컨트롤러에서 잘 추출했음을 알 수 있다. 

![2.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/10.png)

매핑 어노테이션 인자로 전달되는 요청 URI에서 실제 요청 데이터가 들어갈 자리에 중괄호와 그 안에 변수명을 적은 뒤, 해당 메서드의 매개변수에 적용될 `@PathVariable` 어노테이션 인자에 들어갈 인자에도 똑같은 변수명을 작성하였다. 이렇게 하면 매개변수의 변수명이 매핑 어노테이션과 `@PathVariable` 인자에 명시된 변수명과 같지 않아도 요청 정보가 잘 전달된다. 사실 `@PathVariable` 어노테이션에 아무런 인자를 명시하지 않는 경우, 매핑 어노테이션의 URI에 적힌 중괄호 내 변수명과 메서드 매개변수명이 일치해야 한다. 다음과 같이 말이다. 

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestParam;

@Controller
public class RequestParamController {

	@GetMapping("/userinfo/name/{userName}/job/{userJob}")
	public String extractUserInfo(
			@PathVariable String userName,
			@PathVariable String userJob, 
			Model model
	) {
		model.addAttribute("name", userName);
		model.addAttribute("job", userJob);
		return "/param/userInfo";
	}
	
}

```

예제 3-2

그런데 필자는 `@PathVariable` 어노테이션에 인자를 생략하면 에러가 발생하는 관계로 현재는 해당 어노테이션에 변수명을 기입하여 사용하고 있다. 

## `@ResponseBody`

적용 수준 : 클래스, 메서드

View를 전혀 사용하지 않고 클라이언트에 응답 데이터를 JSON 형식으로 보내고자 할 때 사용하는 어노테이션이다. (즉, CSR(Client Side Rendering) 방식에서 사용된다) 컨트롤러 클래스에 적용하면 해당 클래스 내 모든 메서드들은 JSON 형태의 데이터를 반환하게 된다. 반대로 컨트롤러 클래스에 `@Controller` 어노테이션만 적용한 상태에서 특정 메서드에만 `@ResponseBody` 어노테이션을 적용하면 해당 메서드만 JSON 형태의 데이터를 반환한다. 

다음은 해당 어노테이션을 메서드 수준에 적용해본 예제이다. 

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.ResponseBody;

import com.jerocaller.model.UserDto;

@Controller
public class RestTestController {
	
	// View 사용 시의 예시. 
	// View 사용 시 어떻게 데이터를 응답 페이지(View)로 
	// 전달하는 지에 대해서 설명하는 용도이므로 
	// 따로 해당 View 페이지를 만들진 않았다. 
	@GetMapping("/withViewTest")
	public String useView(Model model) {
		UserDto dto = new UserDto();
		dto.setName("kimquel");
		dto.setJob("개발자");
		dto.setAge(30);
		
		model.addAttribute("data", dto);
		return "withView";
	}
	
	@GetMapping("/restTest")
	@ResponseBody
	public UserDto userJsonData() {
		UserDto dto = new UserDto();
		dto.setName("kimquel");
		dto.setJob("개발자");
		dto.setAge(30);
		
		return dto;
	}
	
	@GetMapping("/restTest2")
	@ResponseBody
	public String strJsonData() {
		return "hi";
	}
	
	@GetMapping("/restTest3")
	@ResponseBody
	public String strJsonData2() {
		return "{\"name\":\"jeongdb\",\"job\":\"요리사\",\"age\":29}";
	}
	
	// HTTP Header의 Content-type을 json으로 설정.
	@GetMapping(value = "/restTest4", produces = "application/json; charset=UTF-8")
	@ResponseBody
	public String strJsonData3() {
		return "{\"name\":\"jeongdb\",\"job\":\"요리사\",\"age\":29}";
	}
}

```

예제 4-1.

`/restTest` 를 요청 URI로 하는 메서드를 보면 DTO와 같은 자바 객체를 반환하도록 하면 웹 페이지 상에서 다음과 같이 JSON 형태의 데이터로 자동 변환되어 응답되는 것을 볼 수 있다.

![4.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/11.png)

DTO 객체 내 필드값들이 JSON 형태로 반환될 수 있는 것은 Spring Boot 프로젝트 생성 시 자동으로 설치되는 jackson이라는 라이브러리가 DTO 객체 내 필드값들을 JSON 형태로 변환해주기에 가능한 것이다. 그리고 이는 `@ResponseBody` 어노테이션을 통해 수행된다. 

한 편, `/restTest3` 을 브라우저의 URL 입력창에 입력하면 다음의 결과를 볼 수 있다. 

![6.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/12.png)

String 형태로 값을 반환하는 컨트롤러 메서드의 경우, 아무리 반환 데이터를 JSON 형태에 맞춰 반환하였다 하더라도 기본적으로는 Content-Type이 text 형태인 상태로 응답을 하게 된다. 만약 Content-Type도 JSON 형태이길 원한다면 `@GetMapping` 와 같은 요청 매핑 어노테이션의 “produces” 인자에 Content-Type을 명시하면 된다.

```java
@GetMapping(value = "/restTest4", produces = "application/json; charset=UTF-8")
```

`/restTest4` 로 요청을 보내면 다음과 같이 같은 String 형식으로 반환해도 JSON 형식으로 클라이언트에 전달되는 것을 알 수 있다. 

![7.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/13.png)

한 편, `@ResponseBody` 어노테이션을 메서드에 적용하면, 이를 적용하지 않은 다른 메서드는 그대로 View를 사용하는지를 확인하기 위해 View에 해당하는 다음의 HTML 페이지를 thymeleaf로 준비하였다. 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h3>서버로부터 전송받은 응답 데이터 출력</h3>
	<ul th:object="${data}">
		<li>
			<span>닉네임 : </span>
			<span th:text="*{name}"></span>
		</li>
		<li>
			<span>직업 : </span>
			<span th:text="*{job}"></span>
		</li>
		<li>
			<span>나이 : </span>
			<span th:text="*{age}"></span>
		</li>
	</ul>
</body>
</html>
```

withView.html

브라우저 URL 입력창에 요청 URI를 `/withViewTest` 로 입력하면 다음의 결과를 얻는다. 

![8.PNG](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/14.png)

즉, `@ResponseBody` 어노테이션을 특정 메서드에만 적용하면 다른 메서드에 영향을 끼치지 않음을 알 수 있다. 

## `@RestController`

적용 수준 : 클래스

View를 전혀 사용하지 않고 JSON 형태의 응답 “데이터”만 클라이언트에 전송하고자 할 때 사용하는 어노테이션이다. 클래스 수준에 적용하며, 해당 어노테이션은 컨트롤러 클래스에 `@Controller` + `@ResponseBody` 를 적용한 것과 같다. `@RestController` 어노테이션이 적용된 컨트롤러 클래스 내 모든 메서드들은 JSON 형태의 데이터를 반환한다. 이를 통해 클라이언트에 JSON 형태의 응답 데이터를 전송할 수 있다. 

## `@RequestBody`

적용 수준 : 매개변수 

클라이언트로부터 POST 방식으로 요청 정보를 받을 때, 해당 리소스들을 HTTP의 body에 담아 전송된다. 일반적으로는 HTTP body에 데이터가 담길 때에는 JSON 형태로 저장된다고 한다. 이러한 HTTP body 내 요청 데이터들을 추출할 때 사용되는 어노테이션이며, 컨트롤러 메서드의 매개변수에 적용한다. 만약 요청 정보의 형태를 예측할 수 없다면 Map 컬렉션을 사용해도 되며, 반대로 요청 정보 형태가 정해져 있다면 DTO(Form Bean) 객체 참조 변수로 받을 수도 있다. 

다음은 사용자로부터 정보를 입력받고 이를 HTTP body에 실어 전송하면 컨트롤러에서 이를 받을 수 있는지 테스트해보는 예제이다. 만약 정상적으로 컨트롤러에서 데이터를 받을 수 있다면 해당 컨트롤러가 전송받은 데이터를 조금 수정하여 다시 클라이언트에 전송한 후, 이를 자바스크립트를 이용하여 그 결과를 출력한다. 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<script type="module" src="/js/fetch.js"></script>
</head>
<body>
	<h3>유저 정보 입력</h3>
	<form th:action="@{/requestBody}" method="post" name="userForm">
		<ul>
			<li>
				<span>닉네임 : </span>
				<input type="text" name="name" />
			</li>
			<li>
				<span>직업 : </span>
				<input type="text" name="job" />
			</li>
			<li>
				<span>나이 : </span>
				<input type="number" name="age" />
			</li>
		</ul>
		<input type="submit" value="입력"/>
	</form>
</body>
</html>
```

requestBody.html

타임리프로 작성하였고, form 태그를 이용하여 서버에 요청 전송하도록 하였으나, 사실 자바스크립트를 사용하고 있기에 사실상 AJAX로 서버에 요청을 하는 방식이다. 

```jsx
const requestUrl = userForm.action;
const submitBtn = document.querySelector("input[type=submit]");
let jsonData = {};
let resultData;

submitBtn.addEventListener("click", event => {
	event.preventDefault();
	for (let i = 0; i < userForm.elements.length; i++) {
		if (userForm.elements[i].name !== "") {
			jsonData[userForm.elements[i].name] 
				= userForm.elements[i].value;
		} 
	}
	
	fetch(requestUrl, {
		method: 'POST',
		headers: {
			'Content-Type': 'application/json; charset=utf-8'
		},
		body: JSON.stringify(jsonData)
	}).then(response => response.json())
	.then(data => {
		constructResult(data);
	});
	
});

/**
 * 서버로부터 가져온 데이터를 화면에 구성.
 */
function constructResult(jsonData) {
	let responseRootElement = document.createElement("div");
	let ulElement = document.createElement("ul");
	
	for(let d in jsonData) {
		//console.log(d);
		//console.log(jsonData[d]);
		
		let liElement = document.createElement("li");
		let textNode = document.createTextNode(`${d} : ${jsonData[d]}`);
		liElement.appendChild(textNode);
		ulElement.appendChild(liElement);
	}
	responseRootElement.append(ulElement);
	
	document.querySelector("body")
		.appendChild(responseRootElement);
}

```

fetch.js

위 자바스크립트 파일에서 사실상 사용자 요청 정보를 AJAX로 요청하고, 서버로부터 오는 응답 데이터도 여기서 받아 화면에 구성하는 역할을 수행한다. 

다음은 컨트롤러 코드이다.

```java
package com.jerocaller.controller;

import java.util.Map;
import java.util.Set;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.ResponseBody;

import com.jerocaller.dto.UserDto;

@Controller
public class RequestResponseController {
	
	private Logger logger = LoggerFactory.getLogger(getClass());
	
	@GetMapping("/requestBody")
	public String goToForm() {
		return "requestBody";
	}
	
	// produces를 빼도 실행은 된다. 
	// 다만 크롬 브라우저에서 F12 -> 개발자 도구 -> network에서 requestBody.html을 클릭하여 
	// HTTP 응답 정보를 보면 'text/plain; charset=UTF-8'로 설정되며, 응답 데이터가 텍스트 
	// 형태로 전송됨을 의미한다. 
	@PostMapping(value = "/requestBody", produces = "application/json; charset=UTF-8")
	//@PostMapping("/requestBody")
	@ResponseBody
	public String handleRequest(
			@RequestBody Map<String, Object> userData
	) {
		logger.info("userData : " + userData);
		String responseData = rotateMapAll(userData);
		return responseData;
	}
	
	private String rotateMapAll(Map<String, Object> data) {
		Set<String> keys = data.keySet();
		StringBuilder stringBuilder = new StringBuilder("{");
		
		String newInsertedString = "OH";
		
		logger.info("======================");
		
		/*
		 * json 형태 예시)
		 * {"name" : "김큐엘OH", "job" : "개발자OH", "age" : "32OH" }
		 */
		keys.forEach(oneKey -> {
			logger.info("key : " + oneKey + ", value : " + data.get(oneKey));
			stringBuilder.append("\"" + oneKey + "\" : ");
			stringBuilder.append("\"" + data.get(oneKey));
			stringBuilder.append(newInsertedString);
			stringBuilder.append("\", ");
		});
		logger.info("======================");
		
		// 마지막에 추가한 불필요한 쉼표 문자 제거
		stringBuilder.deleteCharAt(stringBuilder.lastIndexOf(","));
		stringBuilder.append("}");
		
		logger.info("stringBuilder: " + stringBuilder);
		
		return stringBuilder.toString();
	}
}

```

RequestResponseController.java

클라이언트로부터 받는 사용자 정보를 `Map<String, Object>` 컬렉션 파라미터를 이용하여 받는 것을 볼 수 있다. 사실 DTO 객체를 사용해도 된다. 

브라우저 상에서 실행해보면 다음의 결과를 볼 수 있다. 

![화면 캡처 2024-10-30 182057.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/15.png)

![화면 캡처 2024-10-30 182107.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/16.png)

이를 통해 컨트롤러에서 요청 정보를 잘 받아 다시 응답 데이터를 보낸 후 이를 클라이언트에서 받아 화면에 구성하는 데에 성공하였다. 

컨트롤러 코드를 보면 `@PostMapping` 어노테이션에 produces 속성값을 넣든 안넣든 정상적으로 실행된다. 다만 크롬 브라우저 기준 개발자 도구에서 network 탭을 보면 응답 HTTP Header 정보가 다르다. 

![화면 캡처 2024-10-30 181328.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/17.png)

produces 속성값을 쓰지 않으면 응답 데이터의 형식이 텍스트로 전송되는 것을 알 수 있다. 즉, 겉보기만 JSON 형식처럼 생긴 텍스트일 뿐, 사실상 JSON 형식은 아니라는 것이다. 반면 produces 속성값을 명시하면 다음의 결과를 볼 수 있다. 

![화면 캡처 2024-10-30 181355.png](/images/2024-10-28/Spring-Boot-Spring-MVC-Annotations/18.png)

응답 데이터의 형식을 json 형태로 응답하고 있음을 알 수 있다. 

혹시라도 나중에 JSON 형태로 응답 또는 요청 데이터를 받았다고 생각하였는데 버그나 오류가 발생하면 Content-Type을 json 형식으로 했는지 확인해보면 되겠다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[3] [[Spring] 스프링에서 많이 사용하는 어노테이션 정리 (Annotation)](https://zz132456zz.tistory.com/40)

[4] [MVC 어노테이션 정리](https://tech-monster.tistory.com/108)