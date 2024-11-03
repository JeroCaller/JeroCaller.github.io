---
title: "[Spring] AOP 사용"
category: "Spring"
tag: ["Spring", "Java", "AOP"]
---

이 페이지에서는 AOP를 코드 상에서 어떻게 사용하는지 살펴볼 것이다. AOP에 대한 이론적인 설명은 [[Spring] Spring Framwork 개요](/spring/spring-framework-introduction/) 페이지의 “관점 지향 프로그래밍 (AOP, Aspect Oriented Programming)” 챕터에서 설명한 바 있으니 참고바란다. 

Maven을 이용하여 Java Application에서 AOP를 사용해보겠다. 그리고 여기서는 AspectJ 라이브러리를 이용하여 AOP를 구현해보겠다. 또한 어노테이션만을 사용하는 방식에 대해서만 살펴보겠다. 

# Annotation 기반 AOP 사용하기

먼저, aspectJ 라이브러리를 사용한 AOP를 사용하려면 다음의 dependency를 pom.xml에 등록해줘야 한다. 

```xml
<dependency>
    <groupId>org.aspectj</groupId>
    <artifactId>aspectjweaver</artifactId>
    <version>1.9.22.1</version>
</dependency>
```

핵심 로직 전후로 삽입하고자 하는 부가 기능에 해당하는 advise를 클래스 형태로 정의하면 된다. 다음은 그 예이다. 

```java
package pack.aop;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class LoginAdvice {
	
	@Around("execution(...)")
	public Object proceedLogin(ProceedingJoinPoint joinPoint) throws Throwable {
		
		// 핵심 로직 메서드 실행 전에 실행하고자 하는 부가 기능 로직은 여기에...
		
		Object returnResultOfTarget = joinPoint.proceed();
		
		// 핵심 로직 메서드 실행 후에 실행하고자 하는 부가 기능 로직은 여기에...
		
		return returnResultOfTarget;
	}
}

```

LoginAdvice.java

- `@Aspect` 어노테이션은 해당 클래스가 AOP에서 부가 기능 로직을 구현하는 advice 클래스임을 명시할 때 사용한다. 이 때 부가 기능 클래스를 사용하기 위해선 해당 클래스에도 `@Component` 어노테이션을 추가하여 bean으로 등록해줘야 한다.
- `ProceedingJoinPoint joinPoint` 를 매개변수로 하는 메서드 정의 시 해당 메서드에서 핵심 로직 전후로 부가 기능을 부여할 수 있다. 이 때 `joinPoint.proceed()` 메서드 호출 시점이 핵심 로직 실행 시점이다. 이 전후로 하여 부가 기능을 추가할 수 있는 구조이다.
- aspect를 구현하는 메서드에 `@Around` 어노테이션을 부여한다. 해당 어노테이션의 인자로는 해당 advice를 적용할 핵심 로직의 메서드명을 적으면 된다. 주로 execution으로 시작되며, 그 안에는 pointcut 표현식이 들어간다. 
`execution([접근제한자] 리턴타입 [패키지경로].클래스명.메서드명([파라미터]))`
위 형태에서 [] 괄호로 감싸진 곳은 생략 가능하다. pointcut 표현식에 대한 자세한 사항은 References [5] 참조.

내 생각 : 위 코드는 어쩐지 디자인 패턴 중 decorator 패턴같다. 기존 메서드 내부 코드는 전혀 건드리지 않은 채 추가 기능을 부여하는 디자인 패턴인데, 위 코드가 그것과 동일해보이기 떄문이다. 

다음은 AOP를 이용하여 로그인 부가 기능을 부여하는 예제이다. 

```java
package pack.business;

public interface WebPage {
	void printWebPage();
}

```

WebPage.java

```java
package pack.business;

import org.springframework.stereotype.Service;

@Service
public class FakeWebPage implements WebPage {
	
	@Override
	public void printWebPage() {
		System.out.println("제 웹 페이지에 오신 것을 환영합니다!");
	}
	
}

```

FakeWebPage.java   


```java
package pack.aop;

import java.util.Scanner;

import org.aspectj.lang.ProceedingJoinPoint;
import org.aspectj.lang.annotation.Around;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

@Aspect
@Component
@PropertySource("classpath:AdminData.properties")
public class LoginAdvice {
	
	@Value("${admin.id}")
	private String adminId;
	
	@Value("${admin.pw}")
	private String adminPw;
	
	@Around("execution(public void pack.business.*.printWebPage())")
	public Object proceedLogin(ProceedingJoinPoint joinPoint) throws Throwable {
		System.out.println("Hi from begin of Advice");
		
		boolean loginResult = loginLogic();
		
		if (!loginResult) return null; // 로그인 실패 시 웹 사이트 방문 불가.
		Object returnResultOfTarget = joinPoint.proceed();
		
		return returnResultOfTarget;
	}
	
	private boolean loginLogic() {
		Scanner scan = new Scanner(System.in);
		
		System.out.println("로그인 하기 - 로그인 후 웹 사이트 방문 가능");
		System.out.print("아이디: ");
		String id = scan.next();
		
		System.out.print("비밀번호: ");
		String pw = scan.next();
		
		if (!id.equals(adminId)) {
			System.out.println("아이디가 틀렸습니다.");
			scan.close();
			return false;
		}
		if (!pw.equals(adminPw)) {
			System.out.println("비밀번호가 틀렸습니다.");
			scan.close();
			return false;
		}
		
		System.out.println("로그인 성공!");
		
		scan.close();
		return true;
	}
}

```

LoginAdvice.java

```java
admin.id=kim
admin.pw=1111
```

AdminData.properties

```java
package pack.business;

import org.springframework.context.annotation.AnnotationConfigApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.EnableAspectJAutoProxy;

@Configuration
@ComponentScan(basePackages = {"pack.aop", "pack.business"})
@EnableAspectJAutoProxy
public class Main {
	public static void main(String[] args) {
		AnnotationConfigApplicationContext context 
			= new AnnotationConfigApplicationContext(Main.class);
		
		WebPage web = context.getBean("fakeWebPage", WebPage.class);
		web.printWebPage();
		
		context.close();
	}
}

```

Main.java

```java
Hi from begin of Advice
로그인 하기 - 로그인 후 웹 사이트 방문 가능
아이디: kim
비밀번호: 1111
로그인 성공!
제 웹 페이지에 오신 것을 환영합니다!

```

Main.java 콘솔창 출력결과

위와 같이 AOP를 이용하여 로그인이라는 부가 기능을 추가할 수 있음을 알 수 있다. 

# AOP 관련 어노테이션 정리

- `@EnableAspectJAutoProxy` : 메인 클래스에서 사용하는 어노테이션. 이 어노테이션을 적용해야 별도의 XML 설정 없이도 개발자가 설정한 advice가 실행된다. 이 어노테이션이 없다면 advice가 실행되지 않는다.
- `@Around` : target 호출 전후로 advice 내용(부가 기능)이 주입되어 실행된다.
    - 이 어노테이션을 적용할 메서드는 그 매개변수로 `ProceedingJoinPoint` 타입의 매개변수를 선언해야 한다. 그리고 해당 매개변수의 proceed() 메서드를 호출하도록 해야 한다. 해당 메서드가 핵심 로직을 수행해준다. 그 결과값의 타입은 Object이므로, 해당 메서드의 반환타입도 Object로 설정해준다.
- `@Before` : target 실행 이전에 advice 주입 시 사용되는 어노테이션
- `@after` : target 실행 이후에 advice 주입 시 사용되는 어노테이션
- `@AfterReturning` : target 메서드 실행 결과가 정상적으로 나올 경우 그 이후에 실행될 advice를 주입한다. target 메서드 실행 도중 예외가 발생하고 이를 해당 메서드 내에서 처리하지 못하면 실행되지 않는 것 같다.
- `@AfterThrowingAdvice` : `@AfterReturning` 어노테이션과 반대로, target 메서드 실행 도중 예외가 발생하여 이를 throw 했을 때에만 advice를 실행시키게끔 하는 어노테이션.

다음은 앞서 살펴본 로그인 예제에서 `@Before`와 `@After` 어노테이션을 추가로 사용해본 예제이다. 앞선 예제는 그대로 놔둔 상태에서 다음의 클래스들을 추가하였다. 

```java
package pack.aop;

import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class BeforeAdvice {
	
	@Before("execution(public void pack.business.*.printWebPage())")
	public void doBeforeAdvice() {
		System.out.println("==== From Before Advice ====");
		System.out.println("============================");
	}
}

```

BeforeAdvice.java

```java
package pack.aop;

import org.aspectj.lang.annotation.After;
import org.aspectj.lang.annotation.Aspect;
import org.springframework.stereotype.Component;

@Aspect
@Component
public class AfterAdvice {
	
	@After("execution(public void pack.business.*.printWebPage())")
	public void doAfterAdvice() {
		System.out.println("===== From After Advice =====");
		System.out.println("웹 페이지를 종료합니다.");
		System.out.println("=============================");
	}
}

```

AfterAdvice.java

```java
==== From Before Advice ====
============================
Hi from begin of Advice
로그인 하기 - 로그인 후 웹 사이트 방문 가능
아이디: kim
비밀번호: 1111
로그인 성공!
제 웹 페이지에 오신 것을 환영합니다!
===== From After Advice =====
웹 페이지를 종료합니다.
=============================

```

Main.java 콘솔창 실행결과

`@Around` 어노테이션은 target 메서드 실행 전후로 advice를 적용하는 방식이기에 ProceedingJoinPoint을 자료형으로 하는 매개변수를 통해 target 메서드를 내부에서 호출해야 하는 방식이었지만, `@Before`, `@After` 어노테이션은 각각 target 메서드 실행 이전, 이후에 advice를 적용하는 방식이기에 해당 메서드에는 매개변수나 반환타입이 없어도 된다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] advice types

[AOP Concepts :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/aop/introduction-defn.html)

[4] AspectJ library

[@AspectJ support :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/aop/ataspectj.html)

[5] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife/HrhB/27)

[6] [AOP를 통해 Custom Annotation 처리하기](https://hwannny.tistory.com/62)

[7] Advice annotations

[Declaring Advice :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/aop/ataspectj/advice.html)