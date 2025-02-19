---
title: "[Spring Boot] Filter VS Interceptor VS AOP"
category: "Spring"
tag: ["Spring", "Spring boot", "Filter", "Interceptor", "AOP", "Servlet"]
---

Spring Security에 대해 정리하려다가 그 전에 먼저 Filter, Interceptor에 대해 정리하는 게 좋을 것 같아서 이 글을 작성해본다. Spring Security를 사용하거나 그 개념을 잠깐 접해보니 은근 Filter에 대한 이야기도 알게 모르게 나오기도 하고, “[스프링에서의 예외 처리](/spring/spring-boot-rest-api-%EC%98%88%EC%99%B8-%EC%B2%98%EB%A6%AC%EC%99%80-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%98%88%EC%99%B8/)” 글에서도 잠깐 언급했지만 어쩌다보니 스프링의 구조에 대해서도 살펴보게 되었고 그러다보니 거기서도 Filter와 Interceptor의 개념도 간간히 나오는 것 같았다. 그래서 이 참에 제대로 정리하고자 한다. 

# 개요

이전에 AOP에 대해 배운 적이 있는데, 여러 모듈에 흩어져 있는 공통 로직들을 하나로 모듈화하여 핵심 로직(비즈니스 로직) 전후로 해당 부가 기능을 삽입하는 방식의 관점 지향 프로그래밍이다. 그런데 이렇게 핵심 기능 전후로 공통 기능들을 하나로 모듈화하여 삽입하는 기술이 AOP만 있는 것이 아니었다. Filter와 Interceptor도 이와 같은 기능을 한다. 이렇게 여러 모듈에 흩어져 있는 공통 기능들을 하나로 모아 모듈화하여 핵심 기능 전후로 적용하는 방식이기에 공통 기능들의 유지보수가 용이해진다는 장점이 있다. 

그렇다면 이 셋의 차이점은 무엇일까? 

![그림 1-1. Filter, Interceptor, AOP의 위치 및 호출 순서 그림.](/images/2025-02-17/spring-boot-filter-interceptor-aop/1.png)

그림 1-1. Filter, Interceptor, AOP의 위치 및 호출 순서 그림.

차이점을 먼저 정리해보자면 크게 다음과 같이 정리할 수 있겠다.

- 위치, 호출 순서 상의 차이
    - Filter는 스프링 컨테이너 밖에 있어 스프링과 무관한 작업을 처리할 때 사용할 수 있다.
        - 원래는 스프링 내에서 Filter를 사용할 수 없었으나, 이후에는 사용할 수 있게 되었다고 한다. 이에 대한 자세한 사항은 후술할 예정.
    - Interceptor, AOP는 모두 스프링 컨테이너 내부에 있어 스프링 bean에 접근 가능.
- 적용 대상
    - Filter, Interceptor는 Servlet 또는 Spring Bean을 대상으로 그 전후에 적용된다. (대략적으로 말하자면, 클래스 단위로 적용한다고 말할 수도 있겠다)
    - 반면 AOP는 비즈니스 로직을 포함하는 메서드 단위로 그 전후에 적용될 수 있다.
- 대상 지정하는 방법
    - Filter, Interceptor는 URL 주소를 이용하여 적용 대상을 지정할 수 있다.
    - AOP는 URL 주소, 파라미터, 어노테이션, 메서드 signature 등 PointCut 단위로 적용 대상을 지정할 수 있다.

하나씩 살펴보겠다.

# 개념

## Filter

앞서 본 그림 1-1을 보면 알겠지만, Filter는 WAS 단에 있으며, Spring Container 밖에 위치해있다. 이러한 위치를 보면 알 수 있겠지만, Filter는 서버로 들어오는 전역의 HTTP Request들을 가장 먼저 받으며, 이들에 대해 필터링하여 특정 작업을 처리하도록 할 수 있는 곳이다. 또한 스프링이 관리하는 개념이 아니라 J2EE (Java 2 Enterprise Edition)의 스펙이기에 기본적으로는 스프링 컨테이너 내에서 일어나는 일들에 대해선 관여할 수 없다. 다만 이후에 스프링 내에서도 필터를 정의하여 사용할 수 있게 되었다고 한다. 이러한 필터의 특징으로 인해, 필터는 스프링과는 무관하고, 전역적으로 처리해야하는 작업을 필터링하는 것이 가장 적합하며, 공통적인 인증, 인가 및 모든 요청에 대한 로깅, 감사, 보안(XSS 방어), 문자열 인코딩 처리 등의 작업이 필터에 적합하다고 한다. 

코드적으로 살펴보면, legacy적인 방식으로 web.xml 파일에 등록하여 사용할 수 있으며, `jakarta.servlet.Filter` 인터페이스를 구현하면 된다 (원래는 `javax.servlet` 에 있었으나 `jakarta` 로 이전되었다고 한다). 해당 인터페이스에는 다음의 메서드들이 있어 이들을 오버라이딩하면 된다.

- `init()` - 필터 인스턴스가 초기화될 때 딱 한 번 호출되는 메서드로, 말 그대로 초기화할 코드가 있을 떄 이 메서드 안에 작성하면 된다. 초기화할 코드가 없다면 생략해도 된다.
- `doFilter()` - 사실상 Filter 인터페이스에서 핵심적인 기능을 하는 메서드. 이 메서드 내에서 필터 통과 전후에 적용하고자 하는 코드를 작성하면 된다. 이 메서드의 파라미터로 `FilterChain chain` 이 주어지는데, `chain.doFilter()` 메서드 호출 코드 앞에는 요청에 대해 적용하고자 하는 로직을(즉 서블릿 실행 이전 단계), 해당 메서드 뒤에는 응답(서블릿 실행 후 단계)에 대해 적용하고자 하는 로직을 작성하면 된다. 만약 `doFilter()` 메서드 내부에서 `FilterChain` 객체의 `doFilter()` 를 호출하지 않으면 요청을 처리할 서블릿이 실행되지 않는다고 한다.
- `destroy()` - WAS가 종료되기 전에 딱 한 번 호출된다. 이 과정에서 필요한 추가 작업을 이 메서드 안에 작성할 수도 있다. 불필요 시 생략 가능.

그래서 보통은 `doFilter()` 만 오버라이딩하여 사용한다고 한다. 

필터는 여러 개 적용할 수 있는데, 하나의 필터에서 작업을 처리한 후 다음 필터로 넘기는 식이다. 이렇게 여러 개의 필터들을 정의하여 한 필터에서 다른 필터로 요청이 순차적으로 전달되는 것을 필터 체인(Filter Chain)이라 한다. 그리고 더 이상 요청을 전달할 필터가 없다면 그제서야 서블릿으로 요청이 넘어가 실행되는 구조이다. 서블릿으로 넘어간 요청이 처리되어 생성된 응답도 필터를 거치므로 응답에 대해서도 필터를 통해 특정 작업을 수행할 수도 있다. 

서블릿은 서블릿 컨테이너 구동 후 클라이언트로부터 요청이 들어와야 서블릿이 생성되어 실행되는 지연 로딩(lazy-loading) 방식인데 반해, 필터는 서블릿 컨테이너 구동 시점에 생성되는 pre-loading 방식이다. 이러한 방식으로 인해 서블릿보다 필터가 먼저 생성되고, 그렇기에 요청이 들어오면 필터가 먼저 이 요청을 가로채 사전 작업을 진행할 수 있는 것이다. 

아래에 코드를 통해 살펴보겠지만, 필터에서는 요청, 응답 객체 자체를 조작할 수 있다. 아예 새로운 요청, 응답 객체로 교체하여 다음 필터 또는 서블릿에 넘길 수도 있다는 것이다. 

## Interceptor

Filter와 달리 Interceptor는 스프링 내에서 사용하는 기술로, DispatcherServlet과 Handler(Controller) 사이에 위치하여, DipatcherServlet에서 Controller로 넘어가는 요청 또는 Controller에서 DispatcherServlet로 이동되는 응답을 가로채어 부가 기능을 추가할 수 있도록 하는 개념이다. Spring Context 내에 존재하는 개념이라, Spring Container 내에 있는 모든 Bean 객체에 접근 가능하여 부가 기능을 적용할 수 있다고 한다. 코드 상으로는 org.springframework.web.servlet.HandlerInterceptor 인터페이스를 구현하면 Interceptor를 구현할 수 있으며, 이 인터페이스에는 컨트롤러로 요청이 전달되기 전에 실행할(즉, Controller의 메서드 실행 전) `prehandle()` , 그리고 컨트롤러 메서드 실행 이후 view 페이지를 렌더링하기 전에 호출하는 `posthandle()` , view 페이지 렌더링 이후에 실행되는 `afterCompletion()` 메서드를 제공한다. `postHandle()` 메서드의 경우 파라미터에 org.springframework.web.servlet.ModelAndView 객체가 포함되어 있기에, 서버에서 응답으로 view 페이지를 렌더링하는 SSR(Server Side Rendering) 방식일 때 사용하기 적합하다. 그리고 `afterCompletion()` 메서드는 아예 view 페이지가 렌더링 된 이후에 실행되는 메서드라 REST API 기반 백엔드 서버 구축 시에는 사용하기 적합하지 않은 메서드라 볼 수 있다. 

앞서 언급한 `prehandle()`, `posthandle()`, `afterCompletion()` 메서드들은 모두 그 파라미터로 HttpServletRequest, HttpServletResponse를 받는다. 필터의 `doFilter()` 에서는 ServletRequest, ServletResponse를 파라미터로 받아 HTTP 뿐만 아니라 다른 프로토콜에 대해서도 특정 부가 기능을 설정할 수 있는 것에 반해, 인터셉터에서는 오로지 HTTP 프로토콜에 대해서만 특정 부가 기능을 설정할 수 있는 셈이다. 

또한, 필터에서는 FilterChain 객체 파라미터가 있어 이 객체의 `doFilter()` 의 인자로 요청, 응답 객체를 전달할 수 있기에 요청, 응답 객체 자체를 조작할 수 있는 반면, 인터셉터에는 그런 파라미터나 메서드가 없기에 요청, 응답 객체 자체를 조작할 수는 없다. 다만 요청, 응답 객체의 특정 값들을 조작하는 것까지만 가능하다. 

인터셉터는 컨트롤러 직전에 요청을 가로채어 특정 부가 기능을 수행할 수 있다는 특징 떄문에 컨트롤러로 전달된 데이터를 가공하는 역할을 수행할 수 있으며, REST API 제작 시 API의 end point가 컨트롤러의 메서드와 매핑되는 관계이기에 API 호출에 대한 logging 및 감사(auditing) 역할을 수행할 수 있다. 또한 필터에서는 WAS 단에서 모든 요청들을 가로채어 공통적인 인증, 인가, 보안 작업을 할 수 있기에, Spring context 내에 포함된 인터셉터에서는 세부적인 인증, 인가, 보안 작업이 적합하다. 

## AOP

AOP(Aspect Oriented Programming, 관점 지향 프로그래밍)은 기존의 객체 지향 프로그래밍 (OOP, Object Oriented Programming)을 보완하는 프로그래밍 방식으로, 핵심 기능들에서 공통적으로 중복되어 나타나는 부가 기능 즉 횡단관심사(cross-cutting concern)들을 하나로 모아 모듈화한 후, 이 부가 기능이 필요한 핵심 기능 전후로 삽입하는 기술이다. 

HTTP 요청, 응답에 대해 적용하는 Filter, Interceptor와 달리 AOP는 비즈니스 로직에 대해 적용한다. 그리고 AOP는 메서드 수준에서만 적용한다는 차이점도 존재한다. 

Filter, Interceptor는 적용 대상에 대해 URL을 이용하여 매핑하는 방법밖에 없지만, AOP는 파라미터, 어노테이션, 메서드 등 point cut을 기준으로 적용 대상과 매핑할 수 있다. 이와 비슷한 또 다른 차이점으로, Filter, Interceptor의 메서드 파라미터로 HttpServletRequest, HttpServletResponse를 사용하여 HTTP 요청, 응답에 대해 부가 기능을 적용하는 것에 비해, AOP에서는 ProceedingJoinPoint를 파라미터로 사용하기에 join point를 기준으로 부가 기능을 적용한다는 차이점도 존재한다. 

AOP는 비즈니스 로직 단에서의 로깅, 트랜잭션, 에러 처리 등의 작업에 적합하다. 

AOP에 대한 더 자세한 이야기는 이전에 별도의 글로 다룬 적이 있기에 해당 글들을 참고하면 되겠다. 

[https://jerocaller.github.io/spring/spring-framework-introduction/#관점-지향-프로그래밍-aop-aspect-oriented-programming](/spring/spring-framework-introduction/#%EA%B4%80%EC%A0%90-%EC%A7%80%ED%96%A5-%ED%94%84%EB%A1%9C%EA%B7%B8%EB%9E%98%EB%B0%8D-aop-aspect-oriented-programming)

[https://jerocaller.github.io/spring/Spring-How-To-Use-AOP/](/spring/Spring-How-To-Use-AOP/)

# 코드로 살펴보기

## Spring Boot에서의 Filter

필터는 스프링 컨테이너 밖에 있어 스프링과 무관한 기술이었으나 나중에 스프링에서도 사용할 수 있게 되었다고 한다. 이를 가능하게 하는 것이 DelegatingFilterProxy 라고 한다. “Delegating”은 “위임하다”라는 의미가 있는데, 실제로 DelegatingFilterProxy는 WAS 단에 있는 필터 체인 사이에 존재하여 사용자의 요청을 필터링할 역할을 Spring Bean에 “위임”하는 기능을 한다. 즉, WAS단으로 넘어온 사용자의 요청과 이에 대해 필터 역할을 할 Spring Bean과 매핑해주는 징검다리 역할을 해주는 것이다. 이를 구현하기 위해 DelegatingFilterProxy 내부에는 실질적으로 필터 역할을 Spring Bean에 위임하는 역할을 하는 FilterChaingProxy 라는 구현체를 포함하고 있다고 한다. 

![DelegatingFilterProxy구조.drawio.png](/images/2025-02-17/spring-boot-filter-interceptor-aop/2.png)

web.xml 방식 사용 시에는 사용자 정의 Filter가 아닌 DelegatingFilterProxy를 등록해야 하며, Filter 역할을 할 Spring Bean은 DispatcherServlet이 초기화되기 이전에 먼저 생성되어야 필터 역할을 할 수 있으므로 사용자 정의 Filter Bean은 `@Configuration` , `@Bean` 을 이용하여 먼저 등록하도록 구성해야 한다고 한다. 

그러나 Spring Boot에서는 자동 구성(auth-configuration) 기능을 제공하기에 DelegatingFilterProxy를 직접 다루거나 등록하지 않아도 사용할 수 있다고 한다. 

스프링 부트에서 다음과 같이 필터 클래스를 정의할 수 있다. 다음 코드는 다음 필터 또는 서블릿으로 넘기기 전에 요청 Context에 문자열 데이터를 저장하고, 서블릿 실행 후 응답이 올 때에는 앞서 저장한 요청 Context 내 문자열 데이터를 다시 꺼내 출력하는 간단한 예제이다. 

```java
package com.jerocaller.filter;

import java.io.IOException;

import com.jerocaller.common.RequestAttributeNames;

import jakarta.servlet.Filter;
import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.http.HttpFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;

@WebFilter(urlPatterns = "/test/*")
@Slf4j
public class RequestFilter extends HttpFilter implements Filter {

	@Override
	protected void doFilter(
		HttpServletRequest request, 
		HttpServletResponse response, 
		FilterChain chain
	) throws IOException, ServletException {
		
		log.info("Request Filter 호출");
		
		request.setAttribute(RequestAttributeNames.REQUEST_FILTER, "Hi From request filter");
		
		// 요청이 다음 필터 또는 서블릿으로 전달되기 전에 처리하고자 하는 작업을 
		// doFilter 메서드 호출 전에 작성할 수 있다.
		chain.doFilter(request, response);
		// 서블릿에서 요청을 처리하고 생성된 응답을 가로채어 여기서 사후 작업 로직을 작성할 수 있다. 
		
		String requestFilterValue = (String) request.getAttribute(
			RequestAttributeNames.REQUEST_FILTER
		);
		log.info("requestFilterValue: {}", requestFilterValue);
		
		log.info("request Filter 끝");
		
	}
	
}

```

코드 1-1. RequestFilter.java

위 코드에서는 `jakarta.servlet.Filter` 인터페이스를 구현하는 필터 클래스를 정의하였다. 해당 인터페이스에는 `doFilter(ServletRequest request, ServletResponse response, FilterChain chain)` 메서드가 있어 이를 오버라이딩할 수 있다. 이 때 해당 메서드의 파라미터는 Servlet~이란 이름으로 시작한다. 이는 꼭 HTTP 프로토콜뿐만 아니라 파일 전송을 위한 FTP 등 다양한 프로토콜에 대해서도 필터 기능을 적용할 수 있게 하기 위함이다. 만약 HTTP에 대해서만 상정하고자 한다면 위 코드처럼 `jakarta.servlet.http.HttpFilter` 클래스를 상속받도록 한 후, 해당 클래스의 `doFilter(HttpServletRequest request, HttpServletResponse response, FilterChain chain)` 메서드를 오버라이딩하면 된다. 

`chain.doFilter()` 메서드에서 주목할 만한 또 한가지 특이점은, 이 메서드에는 각각 ServletRequest, ServletResponse를 넘길 수 있는데, 이로 인해 아예 새로운 객체 자체를 넘길 수도 있다는 것이다. 즉, 필터에서는 요청, 응답 객체 자체를 조작할 수가 있다는 것이다. 반면 Interceptor는 `chain.doFilter()` 같은 것이 없어서 오로지 요청, 응답 객체 내 필드 값들만 변경할 수 있다. 

필터를 적용할 URL를 지정해야하기에 위 코드에서처럼 `@WebFilter` 어노테이션을 사용하였다. 

이후 이 필터를 Spring Bean으로 등록하기 위해선 두 가지 방법 중 하나를 택할 수 있다. 첫 번째 방법은 `main()` 메서드를 가지고 있는 ~Application이란 이름으로 끝나는 클래스 또는 설정을 위한 일반 자바 클래스에 `@ServletComponentScan(basePackages = "...")` 와 같은 형식의 어노테이션을 적용하는 방법이다. 

```java
package com.jerocaller.config;

import org.springframework.boot.web.servlet.ServletComponentScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration

@Configuration
@ServletComponentScan(basePackages = "com.jerocaller.filter")
public class FilterConfig {}

```

코드 1-2. FilterConfig.java

그런데 여러 개의 필터들을 적용하고, 이 필터들의 적용 순서를 개발자가 직접 설정하고자 할 때에는 이 방법으로는 불가능하다. 이를 위해선 두 번째 방법을 쓰는 것이 좋을 것이다. 설정을 위한 자바 클래스 내부에 FilterRegistrationBean를 이용하는 방식으로, 다음과 같은 형식으로 사용하면 되겠다. FilterRegistrationBean 클래스의 `setOrder()` 를 사용하여 필터들의 실행 순서를 정하면 된다. 

```java
package com.jerocaller.config;

import org.springframework.boot.web.servlet.FilterRegistrationBean;
import org.springframework.boot.web.servlet.ServletComponentScan;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import com.jerocaller.filter.OnceFilter;
import com.jerocaller.filter.RequestFilter;
import com.jerocaller.filter.TimeCheckerFilter;

/**
 * 여러 필터들의 호출 순서가 상관없다면 아래 클래스 내부의 코드는 모두 주석 처리하고, 
 * <code>@ServletComponentScan</code> 어노테이션만 적용하면 되고, 
 * 반대로 호출 순서를 정해야한다면 해당 어노테이션은 주석 처리하고 아래 코드를 
 * 주석 해제한다. 
 */
@Configuration
//@ServletComponentScan(basePackages = "com.jerocaller.filter")
public class FilterConfig {
	
	@Bean
	public FilterRegistrationBean<RequestFilter> sessionFilter() {
		
		FilterRegistrationBean<RequestFilter> bean 
			= new FilterRegistrationBean<RequestFilter>();
		bean.setFilter(new RequestFilter());
		//bean.addUrlPatterns("/test/*");
		bean.setOrder(2);
		return bean;
	}
	
	@Bean
	public FilterRegistrationBean<TimeCheckerFilter> timeCheckerFilter() {
		
		FilterRegistrationBean<TimeCheckerFilter> bean 
			= new FilterRegistrationBean<TimeCheckerFilter>();
		bean.setFilter(new TimeCheckerFilter());
		//bean.addUrlPatterns("/test/*");
		bean.setOrder(1);
		return bean;
	}
	
	@Bean
	public FilterRegistrationBean<OnceFilter> onceFilter() {
		
		FilterRegistrationBean<OnceFilter> bean 
			= new FilterRegistrationBean<OnceFilter>();
		bean.setFilter(new OnceFilter());
		bean.setOrder(3);
		return bean;
	}
}

```

코드 1-3. FilterConfig.java

## OncePerRequestFilter

서블릿은 클라이언트로부터 요청을 받으면 해당 요청을 처리할 수 있는 서블릿을 생성, 메모리에 저장한 후, 동일한 요청이 오면 메모리에 저장된 서블릿을 재활용하는 방식이다. 이러한 구조로 인해 하나의 요청에 필터가 두 번 실행될 수도 있다고 한다. 이를 방지하기 위해 스프링에서는 org.springframework.web.filter.OncePerRequestFilter라는 추상 클래스를 제공하며, 이를 상속받아 `doFilterInternal()` 을 구현하면 요청 당 단 한 번만 필터를 수행할 수 있다고 한다. 

```java
package com.jerocaller.filter;

import java.io.IOException;

import org.springframework.web.filter.OncePerRequestFilter;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.annotation.WebFilter;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;

@Slf4j
@WebFilter(urlPatterns = "/test/*")
public class OnceFilter extends OncePerRequestFilter {

	@Override
	protected void doFilterInternal(
		HttpServletRequest request, 
		HttpServletResponse response, 
		FilterChain filterChain
	) throws ServletException, IOException {
		
		log.info("OncePerRequestFilter 호출");
		
		filterChain.doFilter(request, response);
		
		log.info("OncePerRequestFilter 끝");
		
	}

}

```

코드 1-4. OnceFilter.java

## Spring Boot에서 인터셉터 구현하기

앞서 언급했듯, 인터셉터를 구현하려면 org.springframework.web.servlet.HandlerInterceptor 인터페이스의 구현체를 정의하면 된다. 다음은 컨트롤러 실행 전에 Request Context에 문자열 데이터를 저장하고, 컨트롤러 실행 후에는 앞서 Request Context에 저장한 문자열 데이터를 꺼내 출력하는 예제이다.

```java
package com.jerocaller.interceptor;

import org.springframework.stereotype.Component;
import org.springframework.web.servlet.HandlerInterceptor;
import org.springframework.web.servlet.ModelAndView;

import com.jerocaller.common.RequestAttributeNames;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;

@Component
@Slf4j
public class RequestInterceptor implements HandlerInterceptor {
	
	@Override
	public boolean preHandle(
		HttpServletRequest request, 
		HttpServletResponse response, 
		Object handler
	) throws Exception {
		
		log.info("Request Interceptor prehandle 호출");
		
		request.setAttribute(
			RequestAttributeNames.REQUEST_INTERCEPTOR, 
			"Hi From Request Interceptor!"
		);
		log.info("Request Interceptor prehandle 끝");
		
		return HandlerInterceptor.super.preHandle(request, response, handler);
	}

	@Override
	public void postHandle(
		HttpServletRequest request, 
		HttpServletResponse response, 
		Object handler,
		ModelAndView modelAndView
	) throws Exception {
		
		log.info("Request Interceptor postHandle 호출");
		
		String requestInterceptorValue = (String) request.getAttribute(
				RequestAttributeNames.REQUEST_INTERCEPTOR
		);
		log.info("Request Interceptor value: {}", requestInterceptorValue);
		
		log.info("Request Interceptor postHandle 끝");
		HandlerInterceptor.super.postHandle(request, response, handler, modelAndView);
	}

}

```

코드 2-1. RequestInterceptor.java

`prehandle()` 메서드의 반환값은 boolean인데, true를 반환하면 다음 인터셉터 또는 인터셉터가 없다면 handler로 요청이 전달되고, false면 해당 인터셉터가 이미 응답까지 처리했다고 간주하여 요청이 다른 인터셉터나 핸들러로 전달되지 않는다고 한다. 

이렇게 만든 인터셉터들을 등록해주는 과정도 필요하다. org.springframework.web.servlet.config.annotation.WebMvcConfigurer 인터페이스의 구현체에서 `addInterceptors(InterceptorRegistry registry)` 메서드를 오버라이딩해야 한다. 그리고 registry 파라미터의 `addInterceptor()` 메서드의 인자로 이전에 만든 인터셉터 객체를 추가하면 된다. 

```java
package com.jerocaller.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.InterceptorRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import com.jerocaller.interceptor.RequestInterceptor;
import com.jerocaller.interceptor.TimeCheckerInterceptor;

import lombok.RequiredArgsConstructor;

@Configuration
@RequiredArgsConstructor
public class WebConfig implements WebMvcConfigurer {
	
	private final RequestInterceptor requestInterceptor;
	private final TimeCheckerInterceptor timeCheckerInterceptor;
	
	/**
	 * 인터셉터 등록 순서에 따라 호출 순서도 그에 따라 달라진다. 
	 */
	@Override
	public void addInterceptors(InterceptorRegistry registry) {
		//registry.addInterceptor(requestInterceptor)
		//	.addPathPatterns("/test/**");
		
		registry.addInterceptor(timeCheckerInterceptor)
			.addPathPatterns("/test/**");
		
		registry.addInterceptor(requestInterceptor)
			.addPathPatterns("/test/**");
	}

}

```

코드 2-2. WebConfig.java

이 때, 둘 이상의 인터셉터들을 등록할 때 코드 순서가 곧 호출 순서가 된다. 위 코드에서는 timeCheckerInterceptor가 먼저 호출되고 그 후에 requestInterceptor가 호출되는 순서가 된다. 이 호출 순서를 바꾸고자 한다면 코드 순서도 뒤바꾸면 된다. 

위에서 보인 예제 코드들의 전체 소스 코드는 다음의 사이트 참고. 

[spring-study/FilterAndInterceptor at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/FilterAndInterceptor)

---

References

[1] [[Spring] Filter, Interceptor, AOP 차이 및 정리](https://goddaehee.tistory.com/154)

[2] [[Spring] Filter, Interceptor, AOP의 차이점에 대해](https://memodayoungee.tistory.com/114)

[3] [[Spring] Filter, Interceptor, AOP 차이 및 AOP를 사용하여 Logging을 구현한 이유](https://velog.io/@miot2j/Spring-Filter-Interceptor-AOP-%EC%B0%A8%EC%9D%B4-%EB%B0%8F-AOP%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-Logging%EC%9D%84-%EA%B5%AC%ED%98%84%ED%95%9C-%EC%9D%B4%EC%9C%A0)

[4] [[spring]filter vs interceptor vs AOP](https://goodteacher.tistory.com/412)

[5] 스프링에서도 필터 사용 가능.

[[Spring@MVC] 스프링과 Filter](https://goodteacher.tistory.com/590)

[6] Handler Interceptor

[05. Handler Interceptor](https://goodteacher.tistory.com/259?category=825795)

[7] [Spring Filter, Interceptor, AOP 비교](https://carnival.tistory.com/77)

[8] 스프링 부트에서의 filter 사용법

[Spring Boot: 필터에서 doFilter와 FilterChain이란?](https://curiousjinan.tistory.com/entry/spring-filterchain-dofilter#4.%20Spring%20Framework%EC%97%90%EC%84%9C%EC%9D%98%C2%A0%ED%95%84%ED%84%B0%20%EC%B2%B4%EC%9D%B4%EB%8B%9D(FilterChain)-1)

[9] [Spring AOP, Filter, Interceptor 차이](https://russell-seo.tistory.com/20)

[10] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023), p273~p305, p396

[11] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024), p374~376