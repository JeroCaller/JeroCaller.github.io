---
title: "[Spring Boot] HTTP 요청 및 응답"
category: "Spring"
tag: ["Java", "Spring", "Spring Boot", "Http"]
---

이 글에서는 Spring Boot 환경에서 HTTP 요청 데이터를 받고 응답 데이터를 클라이언트에 전달하는 방법에 대해 기술한다. 이에 관한 어노테이션에 대한 내용은 [[Spring Boot] Spring MVC Annotations](/spring/Spring-Boot-Spring-MVC-Annotations/) 페이지에 자세히 기술하였으니 해당 페이지 참고. 

# Form Bean을 이용하여 요청 정보 받기

Controller의 메서드에서 사용자로부터 입력받은 정보를 받고자 할 때 만약 입력받은 정보의 종류가 많다면 각각의 데이터에 대해 매개변수들을 만들어 받는 방식은 매개변수가 많아져 정보를 효율적으로 받는 방법이라 보긴 힘들 것이다. 이럴 때는 DTO 형태의 Form Bean 클래스를 하나 만들어 이를 매개변수로 사용하면 된다. 

사용자로부터 요청 정보를 입력받아 이를 Controller 또는 service(비즈니스 로직) 계층 및 repository (DB, DAO 계층)에 전달할 때 사용되는 DTO 객체는 Form Bean이라 부른다고 해서 여기서도 Form Bean이라고 하겠다. (HTML 페이지에서 form 태그를 이용하여 사용자의 여러 정보들을 한꺼번에 서버에 전송하기에 form bean이란 이름이 붙은 것으로 추측된다) 

다음은 Form Bean을 이용하여 사용자로부터 데이터를 입력받는 예제이다. 

```java
package com.jerocaller.controller.formbeans;

import lombok.Getter;
import lombok.Setter;

@Getter
@Setter
public class UserBean {
	private String name;
	private String job;
	private int age;
}

```

예제 1-1. 

위 클래스가 Form Bean에 해당하는 클래스로, 사용자로부터 입력받은 정보를 담아 Controller 및 기타 계층에 전달하는 역할을 한다. 여기서는 편리하게 lombok 라이브러리를 사용하여 getter 및 setter 메서드를 정의하였다. 

다음은 Controller 클래스 코드이다. 

```java
package com.jerocaller.controller;

import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestParam;

import com.jerocaller.controller.formbeans.UserBean;

@Controller
public class RequestParamController {
	
	@GetMapping("/userinfo")
	public String goToUserPage() {
		return "/param/userMain";
	}
	
	@PostMapping("/userinfo")
	public String showUserInfo(UserBean bean, Model model) {
		model.addAttribute("userData", bean);
		return "/param/userInfoWithBean";
	}
}

```

예제 1-2.

위 예제의 showUserInfo 메서드의 매개변수를 보면, UserBean 객체를 매개변수로 사용하고 있음을 볼 수 있다. 만약 각각의 정보들을 각각의 매개변수로 받으려고 했다면 다음과 같이 코드를 작성했어야 했을 것이다. 

```java
@PostMapping("/userinfo")
public String showUserInfo(
	@RequestParam("name") String name,
	@RequestParam("job") String job,
	@RequestParam("age") int age
) { 
	model.addAttribute("name", name);
	model.addAttribute("job", job);
	model.addAttribute("age", age);
	return "/param/userInfoWithBean";
}
```

예제 1-3.

이렇게 되면 코드의 길이가 늘어나고, 유저라는 한 사람의 정보를 파편적으로 관리한다는 단점이 있다. 따라서 이 경우 Form bean을 사용하는 것이 더 좋다. 이를 사용하면 `@RequestParam` 과 같은 별도의 어노테이션을 적용하지 않아도 된다는 장점도 챙길 수 있다. 

다음은 view에 해당하는 HTML 파일들의 내용이다. 참고로 templates.param 패키지 내부에 생성하였다. 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h3>유저 정보 입력</h3>
	<form th:action="@{/userinfo}" method="post">
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
		<input type="submit" value="입력" />
	</form>
</body>
</html>
```

예제 1-4. userMain.html

위 페이지는 사용자로부터 정보를 입력받는 페이지이다. 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h3>유저 입력 정보</h3>
	<ul th:object="${userData}">
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

예제 1-5. userInfoWithBean.html

위 페이지는 사용자가 입력한 정보를 출력하는 페이지이다. 

브라우저에서 http://localhost:8080/userinfo 를 입력하면 다음과 같이 사용자 정보 입력란이 나온다. 

![1.PNG](/images/2024-10-30/Spring-Boot-Http-request-and-response/1.png)

사용자 정보를 입력하고 “입력” 버튼을 누르면 다음과 같이 사용자가 입력한 정보가 잘 출력됨을 볼 수 있다. 

![2.PNG](/images/2024-10-30/Spring-Boot-Http-request-and-response/2.png)

Form Bean을 이용하여 사용자 요청 정보를 받을 때 주의할 점은, HTML 상에서 form 태그를 이용하여 사용자 정보를 전송할 때 input과 같은 입력 태그 내 name 속성에 들어갈 값과 Form Bean의 각 필드의 getter 메서드명과 일치해야한다는 점이다. 만약 필드명이 job이고 이 필드의 getter 메서드명이 getJob이라면 HTML에서의 입력 태그에서는 `<input type="text" name="job"/>` 와 같이 이름을 일치시켜야 한다는 것이다. 그래야 사용자 정보를 그대로 전달할 수 있기 때문이다. 만약 다음과 같이 HTML 내 입력 태그 내 name 속성값을 Form Bean 객체 내 getter 메서드명과 달리 하면 사용자 요청 정보를 애초에 제대로 받아 전달할 수가 없다. 

```html
<!DOCTYPE html>
<html xmlns:th="http://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
</head>
<body>
	<h3>유저 정보 입력</h3>
	<form th:action="@{/userinfo}" method="post">
		<ul>
			<li>
				<span>닉네임 : </span>
				<input type="text" name="username" /> <!-- 잘못된 name -->
			</li>
			<li>
				<span>직업 : </span>
				<input type="text" name="userjob" /> <!-- 잘못된 name -->
			</li>
			<li>
				<span>나이 : </span>
				<input type="number" name="userage" /> <!-- 잘못된 name -->
			</li>
		</ul>
		<input type="submit" value="입력" />
	</form>
</body>
</html>
```

예제 1-6. userMain.html

![3.PNG](/images/2024-10-30/Spring-Boot-Http-request-and-response/3.png)

컨트롤러로부터 사용자 정보를 받은 View에서는 내부적으로 Form Bean의 getter 메서드를 호출하여 해당 필드값을 얻어 출력하는 것이라 보면 되겠다. 

# Model을 이용하여 데이터를 담아 View에 전송하기

Spring MVC에서 만약 View를 사용한다면, 이는 서버에서 응답 “페이지”를 사용자 요청에 따라 동적으로 생성한 다음 이를 사용자에게 전송하는 방식을 사용한다는 것이다. 즉, SSR (Server Side Rendering) 방식을 이용하는 것이다. 이 경우, Model 이라는 객체의 addAttribute(key, value) 메서드를 통해 View에 전송할 데이터를 담아 View에서 출력한다. 이는 앞서 본 예제 1-2를 보면 확인할 수 있다. 

사실 model.addAttribute()는 기존 서블릿 내에서의 `request.setAttribute(key, value)`  와 동일하다. 즉, Request 객체의 context에 데이터를 저장하여 이를 view에 전송하는 방식인 것이다. 따라서 페이지 이동 시 redirect 방식을 취하면 model 객체에 저장한 데이터를 잃어버리기에, 이 경우 forward 방식을 사용하는 것이 좋다. forward 방식은 컨트롤러 클래스 내 메서드에서 문자열 형태로 HTML 파일명을 반환할 때 “forward:/mypage” 와 같이 앞에 “forward:” 키워드를 붙이면 된다. 사실 해당 키워드를 생략하여 “/mypage” 같이 해도 기본적으로 forward 방식으로 페이지 이동이 진행된다. 만약 redirect 방식의 페이지 이동을 원할 경우 “redirect:/mypage” 와 같이 맨 앞에 “redirect:” 를 추가하면 된다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)