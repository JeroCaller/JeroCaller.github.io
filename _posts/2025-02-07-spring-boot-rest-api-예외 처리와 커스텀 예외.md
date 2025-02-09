---
title: "[Spring Boot][REST API] 예외 처리와 커스텀 예외"
category: "Spring"
tag: ["Spring", "Spring Boot", "REST API", "예외 처리", "Exception", "Error", "유효성 검증", "Validation", "ArgumentResolver", "@RestControllerAdvice", "@ExceptionHandler"]
---

# 개요

클라이언트에서 전달받은 요청 처리 시 때로는 예외가 발생할 때도 있다. 그리고 그 예외를 개발자가 처리할 때도 있지만 대부분은 예외 상황에 알맞는 응답 데이터를 클라이언트에 전송한다. 클라이언트가 유효하지 않은 형식의 데이터를 요청한 경우(Bad request), 또는 아예 요청한 자원이 존재하지 않을 경우(Not Found) 등이 그 예이다. 이렇게 예외 상황을 클라이언트에 알림으로써 클라이언트가 그 다음 행동을 결정하는데에 도움을 줄 수 있다. 특정 예외 상황을 받으면 프론트 단에서 사용자에게 적절한 알림창을 띄운다든가, 인증되지 않은 사용자에 대해 로그인 화면으로 안내한다든가 등이 그 예가 되겠다. 

이와 관련하여 다른 분들의 글들도 보니 대부분 공통적으로 클라이언트에게 HTTP Status와 예외 상황을 설명하는 메시지 이 두 가지만큼은 전송하도록 고안하는 것을 발견하였고, 필자도 팀 프로젝트를 할 때 저 두 가지만큼은 응답 메시지에 포함시켜 전송하도록 하는 방법을 택하였다. 

여기까지는 문제가 없었다. 그런데 필자에게만큼은 거슬리는 점이 있었다. 예외 상황 발생 시 응답 데이터를 생성, 전송하는 코드를 어떻게 구성할 것이냐였다. 사실 이미 스프링에 대해 깊이 아는 분들에게는 다소 구식이라고 느낄 수 있겠지만, 서비스 계층에서 발생한 예외 처리를 컨트롤러 메서드 안에서 try ~ catch문으로 일일이 각 예외 상황에 대한 응답 메시지를 생성, 클라이언트에 전송하는 방식을 취했었다. 다음의 방식처럼 말이다. 

```java
@GetMapping("/{id}")
public ResponseEntity<ResponseJson> getMemberById(
	@PathVariable("id") int id
) {
		
	ResponseJson responseJson = null;
	int result = 0;
		
	try {
		result = memberService.getById(id);
	} catch (EntityNotFoundException enfe) {
		responseJson = ResponseJson.builder()
			.httpStatus(HttpStatus.NOT_FOUND)
			.message(enfe.getMessage())
			.build();
	} catch (RuntimeException re) {
		responseJson = ResponseJson.builder()
			.httpStatus(HttpStatus.NOT_FOUND)
			.message(re.getMessage())
			.build();
	}
		
	if (responseJson == null) {
		responseJson = ResponseJson.builder()
			.httpStatus(HttpStatus.OK)
			.message("조회 성공")
			.data(result)
			.build();
	}
		
	return responseJson.toResponse();	
}
```

코드 1-1. 예외 발생 시 각 예외 상황에 맞는 응답 데이터를 구성하는 필자만의 이전 방식. 

위와 같은 방식을 취하니 여러 가지로 부담이었다. 컨트롤러가 요청에 따른 적절한 응답을 선택해 전송하는 역할이란 건 알겠지만, 그거 치고는 코드가 너무 방대해지는 느낌이 들었고, 그로 인해 서비스 계층 코드를 다른 컨트롤러에서 재사용하는 것도 부담되는 구조였다(게다가 위 방식으로는 유효성 검사 실패 시 발생하는 예외도 처리할 수 없다. 이에 대해서는 후술할 예정). 하지만 필자가 생각할 수 있었던 최선의 방법은 위 방식대로 구성하는 것이었다. 이것에 대해 한동안 알게 모르게 신경이 쓰였는데, 스프링에서는 이것보다 훨씬 더 좋은 방식으로 예외 처리하는 방법을 제공한다는 알게 되었다. 이 글에서는 스프링에서의 예외 처리 방식에 대해 살펴보겠다. 그러면서 사실 그보다도 어쩌면 더 근본적인 주제일 수 있는 “응답 데이터를 구성하는 방식”에 대해서도 차차 고찰해볼것이다. 

# 예외와 에러

스프링에서의 예외 처리 방식에 대해 알아가기 전에, 조금 더 기본적인 개념인 “예외”에 대해 먼저 살펴보겠다. 사실 예전에 [https://jerocaller.github.io/java/java-exception-handling/](/java/java-exception-handling/) 이에 대해 다룬 적이 있긴 하지만 조금 더 명확하게 정리하고자 한다. 

사실 필자도 모르게 “예외”와 “에러”를 혼동하여 사용하곤 하는데, 사실 소프트웨어 분야에서 이 둘의 개념에는 차이가 있다. “에러(Error)”는 하드웨어나 운영체제에서 발생하는 문제로, 자바에서는 가상머신(JVM) 단계에서 발생하는 것이 에러이다. 메모리 부족, stack 영역 메모리가 제한된 범위를 넘어가는 stack overflow 등이 대표적인 에러의 예이다. 이러한 에러는 소프트웨어 측면에서 해결할 수 없다는 것이 특징이다. 반면 예외(Exception)는 개발자가 제어, 처리 가능한 에러이자 예측 가능한 에러로, 소프트웨어 단계에서 이를 해결할 수 있다는 특징을 가진다. 사실 에러, 예외에도 여러 종류가 있다. 

![그림 1-1. 에러, 예외의 분류](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/1.png)

그림 1-1. 에러, 예외의 분류

위 그림 1-1에 기반하여 정리하면 다음과 같다. 

- 에러 발생 시점에 따른 분류
    - 컴파일 에러 (Compile Error) : 컴파일 단계에서 발생하는 에러, 즉 프로그램 실행 전 발생하는 에러. 주로 문법 오류와 같은 문제에 의해 발생한다. 이러한 문제점들은 대부분 개발자의 실수로 발생한 것이기에 개발자가 이러한 점들을 고칠 수 있다.
    - 런타임 에러 (Runtime Error) : 프로그램 실행 도중 발생한 에러.
- 런타임 에러 중 개발자가 예측, 통제 가능한 에러인가에 따른 분류
    - 시스템 에러 (System Error) : 앞서 “에러”의 개념에 대해 이야기한 부분이 여기에 해당한다. 하드웨어, 운영체제, 자바의 경우 자바 가상 머신에서 발생하는 문제들을 말한다. 개발자가 소프트웨어 측면에서 예측, 통제하기가 어려운 에러들이다.
    - 예외 (Exception) : 개발자가 예측, 제어, 처리 가능한 에러. 개발자는 예외를 처리하여 프로그램이 무사히 동작하도록 할 수 있고, 또는 아예 무시하고 계속 프로그램이 실행되게끔 놔둘 수도 있다.
- 예외 처리 강제 여부에 따른 분류
    - 검사형 예외 (Checked Exception) : 컴파일 단계에서 확인할 수 있는 예외로, 개발자가 반드시 처리해야 한다.
        - 자바에서의 예) Exception, IOException, SQLException 등
    - 비검사형 예외 (Unchecked Exception) : 런타임 (실행) 단계에서 확인 가능한 예외로, 반드시 처리하지 않아도 되는 예외.
        - 자바에서의 예) RuntimeException, NullPointerException, IllegalArgumentException 등

![그림 1-2. 자바 예외 클래스들의 상속 구조.](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/2.png)

그림 1-2. 자바 예외 클래스들의 상속 구조.

Checked Exception은 Exception 클래스를 상속받는 모든 예외 클래스들이 해당한다 보면 되고, Unchecked Exception은 RuntimeException 클래스를 상속받는 모든 예외 클래스들이 이에 해당한다고 보면 된다. 

처리되지 않은 예외는 예외가 발생한 메서드를 호출한 상위 메서드로 전파하고, 해당 상위 메서드에서도 예외를 처리하지 않으면 그 상위 메서드로 전파하는 구조이다. 만약 최상위 메서드에서도 예외를 처리하지 않는다면 자바 가상 머신으로 넘어가며, 여기에 도달하면 (에러의 경우) 프로그램을 종료시킨다고 한다. 

한 가지 사실은, Checked Exception을 throws로 처리하면 해당 예외 클래스까지 메서드의 Signature에 포함된다. 반면 Unchecked Exception은 메서드명 옆에 throws로 명시해도 Signature에 포함되지 않는다. 이로 인해 인터페이스의 추상 메서드를 오버라이딩할 때, 해당 추상 메서드에는 아무것도 throws하지 않는 상황에서 구현 메서드에서 Checked Exception을 throws하면 에러가 발생한다. 반면 Unchecked Exception에 대해서는 에러가 발생하지 않는다. 스프링을 이용하여 웹 사이트 구축 시 보통 다형성을 위해 서비스 계층에서는 추상화된 인터페이스를 만들고 이를 구현체가 오버라이딩하는 구조를 띄는데, 앞서 말한 이유로 인해 커스텀 예외를 사용하고자 한다면 되도록 RuntimeException 클래스를 상속받은 예외 클래스를 정의하여 사용하는 것이 좋을거라 생각된다. 

# 스프링에서의 예외 처리 방법

스프링에서는 예외 처리를 위해 `@(Rest)ControllerAdvice` 와 `@ExceptionHandler` 어노테이션을 이용한다. 조금 더 구체적으로는 다음의 두 가지 방법이 있다. 

- `@(Rest)ControllerAdvice` 와 `@ExceptionHandler` 어노테이션을 하나의 클래스에서 사용하여 모든 컨트롤러에서 발생하는 예외를 처리. - 전역적 예외 처리.
- `@ExceptionHandler` 어노테이션만을 이용하여 특정 컨트롤러 클래스 안에 예외 처리 메서드를 정의하여 해당 컨트롤러에서만 발생하는 예외를 처리. - 지역적 예외 처리.

두 가지 방법 모두 예외는 컨트롤러 계층에서만 처리 가능하므로, 서비스에서 예외가 발생하면 이를 컨트롤러 계층으로 throw하도록 구성해야 할 것이다. 또한, `@ControllerAdvice` 는 View가 존재하는 방식일 때 사용하고, REST API 제작 시에는 `@RestControllerAdvice` 를 사용하여 JSON 형식의 데이터를 응답할 수 있도록 한다. 여기서는 REST API 제작 시 사용되는 `@RestControllerAdvice` 에 대해서만 살펴보겠다. 

## 전역적인 수준에서 예외 처리

전역적인 수준에서의 예외 처리를 위해선 우선 별도의 자바 클래스를 생성한 후, 해당 클래스에 `@RestControllerAdvice` 어노테이션을 적용한다. 만약 특정 컨트롤러에 대해서만 예외 처리를 하게끔 하고프다면 `@RestControllerAdvice(basePackages = { "...", "..." })` 와 같이 속성값을 넣으면 된다. 그 후, 예외를 처리하고자 하는 메서드를 정의하고 그 메서드에 `@ExceptionHandler(value = ~Exception.class)` 형식의 어노테이션을 부여하면 된다. 해당 어노테이션의 value 속성값에는 해당 메서드에서 처리하고자 하는 예외 클래스를 대입하면 되며, 여러 개를 배열 형태로 넣어 한꺼번에 처리할 수도 있다. 다음은 필자가 작성해본 예시이다.

```java
package com.jerocaller.advice;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import com.jerocaller.dto.ApiResponse;

import jakarta.persistence.EntityNotFoundException;
import jakarta.servlet.http.HttpServletRequest;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@RestControllerAdvice
@Slf4j
@RequiredArgsConstructor
public class CustomExceptionHandler {
	
	private final HttpServletRequest request;
	
	@ExceptionHandler(value = RuntimeException.class)
	public ResponseEntity<Object> handleException(
		RuntimeException e
	) {
		
		log.info("컨트롤러 어드바이스에서 예외 처리됨.");
		
		return ApiResponse.builder()
			.httpStatus(HttpStatus.NOT_FOUND)
			.message(e.getMessage())
			.uri(request.getRequestURI())
			.build()
			.toResponse();
	}
	
	/**
	 * EntityNotFoundException은 RuntimeException의 자손 클래스이다. 
	 * 
	 * @param e
	 * @return
	 */
	@ExceptionHandler(value = EntityNotFoundException.class)
	public ResponseEntity<Object> handleEntityException(
		EntityNotFoundException e
	) {
		
		log.info("컨트롤러 어드바이스에서 EntityNotFoundException 예외 처리됨.");
		
		return ApiResponse.builder()
			.httpStatus(HttpStatus.NOT_FOUND)
			.message(e.getMessage())
			.uri(request.getRequestURI())
			.build()
			.toResponse();	
	}
	
}

```

코드 2-1. CustomExceptionHandler.java

위 코드에서 필자는 두 개의 메서드를 정의하였으며, 각각 RuntimeException과 EntityNotFoundException으로 설정하였다. 그리고 각각의 메서드에는 해당 예외를 처리할 로직을 작성하면 된다. 해당 메서드의 파라미터로 앞서 설정한 예외 객체 변수를 선언하며, 이 예외 객체 변수를 통해 예외 처리 로직을 구성할 수 있는 것이다. 필자의 경우 위 코드에서 볼 수 있듯 각각 HTTP status code를 Not Found로 하고 응답 메시지를 생성하여 반환하도록 하고 있다. uri에는 현재 클라이언트가 호출한 uri 정보를 담아 클라이언트에 전송하도록 하고 있다. 프론트엔드 화면이 복잡해지고 한 화면에서 여러 개의 REST API URI들을 호출하여 사용할 때 어느 URI을 호출하다가 발생한 에러인지 명확히 할 수 있기에 응답 메시지에 포함시켜보았다. 

이제 위 코드 2-1에서 설정한 예외 처리가 제대로 동작하는지 확인해보겠다. 테스트용으로 다음과 같이 간단한 서비스와 컨트롤러 코드를 작성해보았다. 

```java
package com.jerocaller.service;

import org.springframework.stereotype.Service;

import com.jerocaller.service.interf.MemberServiceinterface;

import jakarta.persistence.EntityNotFoundException;

@Service
public class MemberServiceImpl implements MemberServiceinterface<MemberServiceImpl> {
	
	private final int MEMBER_LIMIT = 3;

	@Override
	public int getById(int id) {
		
		if (id > MEMBER_LIMIT) {
			throw new RuntimeException("해당 번호의 회원을 찾을 수 없습니다.");
		}
		
		if (id < 0) {
			throw new EntityNotFoundException("음수 번호에 해당하는 회원은 없습니다.");
		}
		
		// 테스트용 데이터로 간주하여 반환.
		return id;
	}

}

```

코드 2-2. 서비스 계층 테스트용 코드.

위 서비스 코드에서는 간단하게 회원 id 번호가 3을 초과하는 값을 클라이언트가 입력하면 RuntimeException을, 음수를 입력하면 EntityNotFoundException을 throw하도록 하였다. 

```java
package com.jerocaller.controller;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.jerocaller.common.CustomResponseCode;
import com.jerocaller.dto.ApiResponse;
import com.jerocaller.dto.ApiResponseCustom;
import com.jerocaller.dto.MemberRegister;
import com.jerocaller.service.MemberServiceCustomImpl;
import com.jerocaller.service.MemberServiceImpl;
import com.jerocaller.service.interf.MemberServiceinterface;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;

@RestController
@RequestMapping("/members")
@RequiredArgsConstructor
public class MemberController {
	
	private final MemberServiceinterface<MemberServiceImpl> memberService;
	
	@GetMapping("/{id}")
	public ResponseEntity<Object> getMethodName(
		@PathVariable("id") int id,
		HttpServletRequest request
	) {
		
		int result = memberService.getById(id);
		
		return ApiResponse.builder()
			.httpStatus(HttpStatus.OK)
			.message("응답 성공")
			.uri(request.getRequestURI())
			.data(result)
			.build()
			.toResponse();
	}
}

```

코드 2-3. 컨트롤러 계층 테스트용 코드. 

먼저 앞서 코드 2-1로 정의한 예외 처리 클래스를 정의하기 전의 Swagger에서 예외 상황 발생 시의 모습을 보겠다. 

![사진 1-1. 예외 미처리 시 Swagger에서 예외 상황 발생 시 모습. ](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/3.png)

사진 1-1. 예외 미처리 시 Swagger에서 예외 상황 발생 시 모습. 

코드 2-1와 같이 예외 처리를 하지 않는다면 위 사진처럼 예외 상황 발생 시 그와 관련된 예외 메시지가 길게 뜬다. 개발 시에는 위와 같은 자세한 메시지가 문제점을 파악하는 데에 용이하겠지만, 실제 배포 시에는 위와 같이 에러 메시지를 그대로 노출 시키는 게 좋은 건 아니다. REST API라서 클라이언트가 보는 화면에 저런 긴 메시지가 직접적으로 표시될 일은 잘 없겠지만, 그럼에도 저 긴 메시지를 받는 입장에서는 무척 당황스러울 것이고, 정확히 무엇이 문제인지는 REST API를 직접 제작한 개발자 외에는 저 메시지를 읽어봐도 알기 힘들 것이다. 또한, 저 예외 메시지에는 어느 코드에서 문제가 일어났는지에 대한 정보도 있어서 보안 상으로도 좋은 모습이라 보기 힘들 것이다. 따라서 이러한 예외 처리를 해야 하며, 예외 상황 발생 시에는 클라이언트가 이해하기 쉬운 메시지로 바꿔 보여주는 것이 더 좋을 것이다. 

다음은 코드 2-1을 적용했을 때 Swagger에서 예외 상황 발생 시의 모습이다. 

![사진 1-2. 예외 처리 시 Swagger에서 예외 상황 발생 시의 모습. ](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/4.png)

사진 1-2. 예외 처리 시 Swagger에서 예외 상황 발생 시의 모습. 

장황했던 에러 메시지 대신 무슨 일이 벌어졌는지를 message 프로퍼티를 통해 핵심만 보여주도록 하였다. 장황한 에러 메시지보다는 이쪽이 훨씬 더 상황 파악에 도움이 잘 될 것이다. 그리고 RuntimeException 발생 시 HTTP Status code로 Not Found를 응답하도록 설정한 것이 Swagger에서도 잘 적용된 것을 볼 수 있다. 

## 특정 컨트롤러 내에서의 예외 처리

`@ExceptionHandler` 어노테이션만으로 특정 컨트롤러의 예외만 처리할 수도 있다. 다음은 FileController 내에서 예외 처리 메서드를 작성한 코드이다.

```java
// 생략... 

@RestController
@RequestMapping("/files")
@RequiredArgsConstructor
@Slf4j
public class FileController {
	
	private final FileServiceInterface<FileServiceImpl> fileService;
	private final HttpServletRequest request;
	
	@GetMapping("/members/{memberId}")
	public ResponseEntity<Object> getMethodName(
		@PathVariable("memberId") int memberId
	) {
		
		List<Integer> filesId = fileService.getFilesIdByMember(memberId);
		
		return ApiResponse.builder()
			.httpStatus(HttpStatus.OK)
			.message("응답 성공")
			.uri(request.getRequestURI())
			.data(filesId)
			.build()
			.toResponse();
	
	}
	
	@ExceptionHandler(value = RuntimeException.class)
	public ResponseEntity<Object> handleException(
		RuntimeException e
	) {
		
		log.info("특정 컨트롤러 클래스 내에서 예외 처리됨.");
		
		return ApiResponse.builder()
			.httpStatus(HttpStatus.NOT_FOUND)
			.message(e.getMessage())
			.uri(request.getRequestURI())
			.build()
			.toResponse();
	}
	
}

```

코드 3-1. FileController.java

`@ExceptionHandler` 어노테이션이 적용된 handleException 메서드는 해당 컨트롤러 내에서 발생하는 예외들만 처리할 수 있다. Swagger에서 예외 상황 발생 시 다음과 같다. 

![사진 2-1. 지역적 예외 처리 시 Swagger에서의 모습.](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/5.png)

사진 2-1. 지역적 예외 처리 시 Swagger에서의 모습.

예외가 원헀던대로 잘 처리된 것을 볼 수 있다. 

## 예외 처리 우선순위

방금 보았던 사진 2-1에서 end point 요청 실행 시 이클립스 콘솔창에서의 메시지는 다음과 같았다. 

```java
2025-02-09T18:42:20.812+09:00[0;39m [32m INFO[0;39m [35m17132[0;39m [2m--- [ExceptionHandling] [nio-8080-exec-1] [0;39m[36mc.jerocaller.controller.FileController  [0;39m [2m:[0;39m 특정 컨트롤러 클래스 내에서 예외 처리됨.
```

이 메시지는 코드 3-1에서 예외 처리용 메서드 내부에 작성한 로그용 메시지와 일치한다. 해당 메서드와 앞서 전역적으로 예외 처리를 하기 위해 작성했던 메서드 둘 모두 똑같이 RuntimeException 예외를 처리하도록 하고 있었다. 여기서 지역 범위의 예외 처리용 메서드가 실행되었다는 것이고, 이는 똑같은 예외 클래스에 대한 처리 시 전역 범위의 예외 처리 메서드보다 지역 범위의 예외 처리 메서드가 우선순위가 더 높아 실행된다는 것을 알 수 있다. 

또한 앞서 코드 2-1~3에서 다뤘던 전역 범위의 예외 처리 관련 코드와 관련하여 Swagger에서 id를 음수로 입력하여 EntityNotFoundException을 일으키게 하면 이클립스 콘솔창에서는 RuntimeException을 처리하는 메서드 내부에 작성한 로그 메시지가 아니라, EntityNotFoundException 예외를 처리하는 메서드 내부에 작성된 로그 메시지가 출력된다. 참고로 EntityNotFoundException 예외 클래스는 RuntimeException의 자손 클래스이다. (중간에 PersistenceException 클래스가 있어 EntityNotFoundException이 이를 상속받고, PersistenceException은 RuntimeException을 상속받는 예외 클래스이다) 이로부터 알 수 있는 사실은, 예외 클래스가 자식 클래스일수록, 즉 더 구체적일수록 예외 처리 우선순위가 더 높다는 것이다. 

예외 처리 우선순위에 대해 정리하자면 다음과 같다.

- 같은 예외를 처리한다면 전역 범위에서 예외 처리하는 메서드보다 지역 범위에서 예외 처리하는 메서드의 실행 우선순위가 더 높다.
- 같은 범위의 예외를 처리한다면 상위 예외 클래스보다는 하위 예외 클래스를 처리하는 메서드의 실행 우선순위가 더 높다.

# 커스텀 예외를 이용하여 클라이언트에게 예외 상황 보고하기

물론 Java SE 또는 스프링에서 제공하는 예외 클래스들도 있지만, 개발자가 직접 Throwable, Exception, RuntimeException 들 중 하나를 택하여 이를 상속받는 예외 클래스를 정의하여 사용할 수도 있다. 필자가 팀 프로젝트를 진행하거나 예제 코드를 작성할 때마다 느낀 것은 기본으로 제공하는 예외 클래스만으로는 특정 상황에 알맞는 예외 클래스를 찾기 힘들다는 것이다. 그래서 필자도 왠만하면 커스텀 예외 클래스를 정의하여 사용하는 편이다. 

커스텀 예외 클래스를 정의하여 사용할 때의 이점은 다음과 같다고 생각한다. 

- 예외 클래스의 이름을 구체적으로 지을 수 있어 예외 클래스 이름만으로도 어떤 예외인지 쉽게 예측 가능하도록 할 수 있다.
- Jav SE 또는 스프링 등 자바 진영에서 기본으로 제공하는 예외 클래스만으로는 특정 상황에 맞는 예외가 없는 경우도 있다. 이 경우 명확히 하기 위해 커스텀 예외 클래스를 정의하여 사용하는 것이 낫다.
- 직접 예외 클래스를 정의하면 그 내부에 개발자가 원하는 기능을 추가할 수 있다. 예를 들면 특정 예외에 맞는 에러 응답 코드를 미리 해당 예외 클래스 내부의 필드에 할당시킴으로써 특정 예외 발생 시 어떤 에러 코드를 선택하여 응답을 해야할지 결정하는 부담을 컨트롤러부터 덜어낼 수 있게 된다.
- 어떤 예외가 발생했을 때 Java SE 또는 스프링에서 지원하는 표준 예외 클래스들만을 사용할 경우, 예외 발생 시 이 예외가 개발자가 예상한 범위 내의 예외인지 아니면 개발자도 예상 못한 예외인지 제대로 구분하기가 어렵다. 반면, 개발자가 예상하는 예외들을 커스텀 예외 클래스를 정의하여 사용, 처리하도록 하면 예외 발생 시 해당 예외가 예상 못한 예외인지 파악하기 수월해진다.
- 특정 상황에서 표준 예외는 발생하지 않지만 이를 예외로 처리하고자 할 때 유용하다. 예를 들어 버그가 발생하는 경우 예외, 에러와 달리 에러가 명시적으로 발생하지 않기에 이 경우 커스텀 예외 클래스를 throw하여 예외를 명시적으로 일으키게 바꿀 수 있다.

이제 커스텀 예외 클래스를 정의하여 이를 서비스 코드에 적용시키고, 이를 앞서 다룬 방법을 이용하여 예외 처리하는 것까지 살펴보겠다.

커스텀 예외 클래스를 정의하기에 앞서, 먼저 필자는 다음과 같은 응답 코드들을 enum 형식으로 정의하였다.

```java
/**
 * 클라이언트에 전송할 커스텀 응답 코드 모음. 
 */
@Getter
@AllArgsConstructor
public enum CustomResponseCode {
	
	// 기본값 용도. 다른 값으로 초기화해야함.
	DEFAULT(
		HttpStatus.INTERNAL_SERVER_ERROR, 
		"DEFAULT", 
		"기본값 용도. 다른 값으로 초기화해야 함."
	),
	
	// 응답 상태 양호 코드
	OK(HttpStatus.OK, "OK", "응답 성공"),
	MEMBER_CREATED(HttpStatus.CREATED, "MEMBER_REGISTER", "회원 가입 성공"),
	
	// 응답 에러 코드
	MEMBER_NOT_FOUND(HttpStatus.NOT_FOUND, "ACCOUNT-001", "조회된 회원 없음."),
	MEMBER_REGISTER_BAD_REQUEST(
		HttpStatus.BAD_REQUEST, 
		"ACCOUNT-002", 
		"잘못된 형식의 회원 가입 정보입니다."
	),
	FILE_NOT_FOUND(HttpStatus.NOT_FOUND, "FILE-001", "조회된 파일 없음.");
	
	// 필드 정의
	private final HttpStatus httpStatus;
	private final String code;
	private final String message;
}

```

코드 4-1. CustomResponseCode.java

원래 필자가 조사했을 때에는 대부분의 사람들은 에러 코드에 대해서만 위와 같이 따로 쳬계적으로 만들어 정의하였지만, 필자는 에러 상태의 응답이든 정상 상태의 응답이든 간에 JSON 응답 형식의 일관성을 유지하기 위해 정상 상태의 응답 코드와 에러 상태의 응답 코드를 위와 같이 하나의 enum 클래스 내부에 정의하였다. 

다음은 필자가 정의한 커스텀 예외 클래스이다. 여러 특정 상황들에서 사용하려면 각 상황에 맞게끔 여러 파생 예외 클래스들을 다양하게 정의해야 하기에 모든 커스텀 예외 클래스들의 최상위 클래스를 다음과 같이 만들었다. 

```java
/**
 * 커스텀 예외 클래스의 최상위 부모 클래스. 
 * 모든 커스텀 예외 클래스들은 이 클래스를 상속받아 정의하면 됨.
 */
@Getter
public class CustomException extends RuntimeException {
	
	private static final long serialVersionUID = 3578550418308671229L;
	
	protected CustomResponseCode responseCode;

	public CustomException() {
		super();
		this.responseCode = CustomResponseCode.DEFAULT;
	}
	
	public CustomException(CustomResponseCode responseCode) {
		super();
		this.responseCode = responseCode;
	}
	
	public CustomException(String message) {
		super(message);
		this.responseCode = CustomResponseCode.DEFAULT;
	}

}

```

코드 4-2. CustomException.java

모든 커스텀 예외 클래스들의 최상위 클래스가 될 CustomException은 RuntimeException을 상속받도록 하였다. Exception이 아닌 RuntimeException을 택한 이유는, 우선 커스텀 예외 클래스들이 쓰이게 될 모든 에외들은 런타임 시점에서 발생하기에 그에 맞게 설정한 것도 있고, 또 다른 이유로는 앞서 언급했듯, 추상 메서드를 정의하고 이를 구현할 때 구현체에서 발생할 수 있는 여러 예외들을 유연하게 throws 할 수 있도록 하기 위함이다. 

그리고 위 코드에서는 앞선 코드 4-1로 정의한 커스텀 응답 코드용 enum 객체 타입의 필드를 선언하였고, 생성자 메서드에서 특정 응답 코드를 할당할 수 있도록 정의하였다. 이렇게 하면 이 커스텀 예외 클래스를 상속받는 파생 클래스에서 각자에게 알맞는 응답 코드들을 배정할 수 있게 된다. 

그 후, 필자는 다음과 같이 최상위 커스텀 예외 클래스들을 상속받는 파생 예외 클래스들을 각각 정의하였다. 

```java
package com.jerocaller.exception;

import com.jerocaller.common.CustomResponseCode;

public class FileNotFoundException extends CustomException {
	
	private static final long serialVersionUID = 4822375173773667798L;

	public FileNotFoundException() {
		super(CustomResponseCode.FILE_NOT_FOUND);
	}

}
```

코드 4-3. FileNotFoundException.java

```java
package com.jerocaller.exception;

import com.jerocaller.common.CustomResponseCode;

public class MemberNotFoundException extends CustomException {

	private static final long serialVersionUID = 3822901348668702169L;
	
	public MemberNotFoundException() {
		super(CustomResponseCode.MEMBER_NOT_FOUND);
	}

}
```

코드 4-4. MemberNotFoundException.java

그 후, 앞서 만들었던 서비스 계층의 코드와 거의 똑같은 또 다른 서비스 계층 클래스를 다음과 같이 정의하였다. 

```java

@Service
@Slf4j
public class MemberServiceCustomImpl implements MemberServiceinterface<MemberServiceCustomImpl> {
	
	private final int MEMBER_LIMIT = 3;
	
	@Override
	public int getById(int id) {
		
		log.info("{} 서비스 객체 호출됨.", this.getClass().getName());
		
		if (id > MEMBER_LIMIT) {
			throw new MemberNotFoundException();
		}
		
		// 테스트용 데이터로 간주하여 반환.
		return id;
	}

}

```

코드 4-5. MemberServiceCustomImpl.java

```java
@Service
@Slf4j
public class FileServiceCustomImpl implements FileServiceInterface<FileServiceCustomImpl> {
	
	private final int MEMBER_LIMIT = 3;
	private final int LOOF_LIMIT = 10;
	private final Random random = new Random();
	
	@Override
	public List<Integer> getFilesIdByMember(int memberId) {
		
		log.info("{} 서비스 객체 호출됨.", this.getClass().getName());
		
		if (memberId > MEMBER_LIMIT) {
			throw new FileNotFoundException();
		}
		
		// 테스트용 가짜 데이터 생성.
		List<Integer> filesId = new ArrayList<Integer>();
		for (int i = 0; i < LOOF_LIMIT; i++) {
			filesId.add(random.nextInt(1, 100));
		}
		
		return filesId;
	}

}

```

코드 4-6. FileServiceCustomImpl.java

이전과 다른 점은 예외 클래스를 앞서 정의한 커스텀 예외 클래스들로 바꿨다는 것 뿐이다. 나머지 로직은 이전과 동일하다. 

이제 이 커스텀 예외들을 처리할 예외 처리를 다음과 같이 별도의 클래스를 정의하여 작성하였다. 

```java
@RestControllerAdvice
@RequiredArgsConstructor
@Slf4j
public class CustomExceptionHandlerTwo {
	
	private final HttpServletRequest request;
	
	@ExceptionHandler(value = RuntimeException.class)
	public ResponseEntity<Object> handleRuntimeException(
		RuntimeException e
	) {
		
		log.info("전역 예외 처리용 컨트롤러 어드바이스에서 {} 예외 처리됨", e.getClass().getName());
		
		return ApiResponse.builder()
			.httpStatus(HttpStatus.NOT_FOUND)
			.message(e.getMessage())
			.uri(request.getRequestURI())
			.build()
			.toResponse();
	}
	
	@ExceptionHandler(value = CustomException.class)
	public ResponseEntity<Object> handleCustomException(
		CustomException e
	) {
		
		log.info("커스텀 예외 처리용 컨트롤러 어드바이스에서 {} 예외 처리함.", e.getClass().getName());
		
		return ApiResponseCustom.builder()
			.responseCode(e.getResponseCode())
			.uri(request.getRequestURI())
			.build()
			.toResponse();
	}
	
}

```

코드 4-7. CustomExceptionHandlerTwo.java

(이전에 만들었던 예외 처리 코드들은 모두 주석 처리하였다) 이제 앞서 Swagger에서 실행해보았던 end point들을 다시 호출해보면 이전과 똑같이 예외 처리가 되며, 대신 응답 메시지가 약간 달라진다. 

![사진 3-1. 커스텀 예외 클래스 적용 후 회원 조회 중 예외 발생 시의 모습. ](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/6.png)

사진 3-1. 커스텀 예외 클래스 적용 후 회원 조회 중 예외 발생 시의 모습. 

![사진 3-2. 커스텀 예외 클래스 적용 후 파일 조회 중 예외 발생 시의 모습.](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/7.png)

사진 3-2. 커스텀 예외 클래스 적용 후 파일 조회 중 예외 발생 시의 모습.

모든 파생 예외 클래스들이 상속받을 최상위 커스텀 예외 클래스를 정의하여 사용하니 예외 처리도 최상위 커스텀 예외 클래스만을 가지고 처리하면 되기에 예외 처리 코드도 줄어들고, 커스텀 예외 클래스의 사용과 스프링에서 제공하는 예외 처리, 그리고 응답 코드 체계 마련으로 인해 컨트롤러 계층에서의 코드량이 확연이 줄어든다는 이점이 있다. 그래서 컨트롤러 계층에서는 오로지 서비스 코드가 예외없이 정상적으로 동작할 때의 응답 코드만 작성하면 된다. 

# 유효성 검사 예외 처리하기

이전 글인 [[Spring Boot] 유효성 검사 Validation](/spring/spring-boot-유효성-검사-validation/) 글에서는 DTO 계층에 Hibernate Validator에서 제공하는 유효성 검사 어노테이션을 적용하여 유효성 검사를 하는 것에 대해 다뤘다. 그리고 유효하지 않은 형식의 데이터 입력 시 MethodArgumentNotValidException 이라는 예외가 발생하며, 정확히 어느 필드에서 유효하지 않은 데이터가 입력되었는지 확인할 수 있는 메시지도 출력됨을 볼 수 있었다. 그런데 사실 해당 예외가 발생할 때 이를 처리하는 방법에 대해서는 논하지 않았었다. 여기서는 앞서 배운 `@RestControllerAdvice` 및 `@ExceptionHandler` 로 해당 예외를 처리하는 것에 대해 살펴보겠다. 

## 컨트롤러 메서드 내부에서는 MethodArgumentNotValidException 예외를 처리할 수 없는 이유

컨트롤러 메서드 내부에서 직접 try ~ catch문으로 예외 처리를 하는 방식으로는 유효성 검사 실패 시 발생하는 MethodArgumentNotValidException 예외를 처리할 수 없었음을 필자는 확인하였다. 심지어는 AOP를 이용하여 별도의 advice를 생성하여 사용했음에도 해당 예외를 처리할 수가 없었다. 후자의 경우 필자가 알지 못하는 다른 방법이 있었을지도 모르겠다. 이렇게 생각하는 이유는 후술하겠지만 `@RestControllerAdvice` 를 이용하면 해당 예외를 처리할 수 있는데, 어노테이션 이름에 advice가 있으니 이 어노테이션도 AOP를 이용한다는 것이고, 이는 AOP로도 해당 예외를 처리할 수 있다는 것을 암시하고 있기 때문이다. 

컨트롤러 메서드 내부에서의 try ~ catch문 작성 방식으로는 MethodArgumentNotValidException를 처리할 수 없는 이유로는 해당 예외가 발생하는 위치(혹은 순서라고 표현해야 할지도 모르겠다) 때문일거라고 추측된다. 유효성 검사를 위한 `@Valid`, `@Validated` 어노테이션은 컨트롤러 메서드의 파라미터 수준에 적용된다. 이러한 파라미터들은 클라이언트의 HTTP 요청 시 전송되는 데이터들을 담는데, 클라이언트의 HTTP 요청으로 넘어온 데이터와 컨트롤러 계층에서의 메서드 파라미터와 바인딩 시켜주는 역할을 ArgumentResolver가 담당하게 된다. 그런데 이 ArgumentResolver의 위치는 Handler Adaptor와 Handler(Controller) 사이에 위치해 있다(정확히는 위치의 개념보다는 Handler Adaptor가 ArgumentResolver를 호출 한 후에 Handler로 HTTP 요청을 위임하는 호출 순서의 차이라고 보는 게 맞을 것 같다).

![그림 2-1. Spring Context 내부에서 ArgumentResolver의 위치. ](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/8.png)

그림 2-1. Spring Context 내부에서 ArgumentResolver의 위치. 

또한, 컨트롤러에서 예외가 발생할 때 이를 별도로 처리하지 않는다면 컨트롤러 → 인터셉터 → 디스패처 서블릿 → 필터 → WAS 순으로 예외가 전파된다. 

이러한 사실들로 미루어볼 때, 유효성 검사가 실패할 때 발생하는 MethodArgumentNotValidException 예외는 컨트롤러 이전인 ArgumentResolver 단계에서 발생하기에 컨트롤러의 메서드 내부에서 해당 예외를 try ~ catch문으로 처리하려고 해봤자 컨트롤러까지 요청이 전달된 것이 아니기에 해당 예외를 처리할 수 없는 것이라고 필자는 추측한다. 

## 유효성 검사 실패 시 어느 데이터가 유효하지 않은지를 응답 메시지로 구성하기

어찌돼었건 본론으로 넘어와서, 앞서 배운 스프링에서의 예외 처리를 이용하여 유효성 검사 실패 시 발생하는 예외를 처리하는 방법에 대해 살펴보겠다. 사실 예외 처리 코드의 구조 자체는 앞서 보았던 것과 동일하고, MethodArgumentNotValidException 예외 클래스를 등록하는 것만 차이가 있다. 하지만 여기서는 유효성 검사 대상인 필드들이 여러 개가 있을 때 어느 필드에서 어떠한 이유로 유효성 검사에 실패했는지 그 메시지를 응답 메시지에 실어 클라이언트에게 전송하도록 하는 방법도 포함시켰기에 이에 대해 소개하겠다. 

앞서 예외 처리를 위해 만들었던 코드 4-7의 CustomExceptionHandlerTwo 클래스 내부에 다음의 메서드를 추가한다. 

```java
@ExceptionHandler(value = MethodArgumentNotValidException.class)
public ResponseEntity<Object> handleValidationException(
	MethodArgumentNotValidException e
) {
		
	log.info("컨트롤러 어드바이스에서 유효성 검사 예외 처리.");
		
	// key: field name, value : message
	Map<String, String> validationExceptionMsg 
		= new HashMap<String, String>();
		
	log.info("=== 유효성 검사 예외 관련 정보 ===");
	e.getBindingResult().getFieldErrors().forEach(error -> {
		log.info(error.getField());
		log.info(error.getDefaultMessage());
		log.info(error.getCode());
		log.info(error.getObjectName());
			
		validationExceptionMsg.put(
			error.getField(), 
			error.getDefaultMessage()
		);
	});
	log.info("=== 유효성 검사 예외 관련 정보 끝 ===");
		
	return ApiResponseCustom.builder()
		.responseCode(CustomResponseCode.MEMBER_REGISTER_BAD_REQUEST)
		.uri(request.getRequestURI())
		.validationMsg(validationExceptionMsg)
		.build()
		.toResponse();
}
```

코드 5-1. CustomExceptionHandlerTwo.handleValidationException

MethodArgumentNotValidException 예외 객체에 존재하는 `getBindingResult().getFieldErrors()` 메서드를 통해 에러가 발생한 모든 필드들에 대한 정보를 List 형태로 얻어온다. 그리고 forEach 메서드를 이용하여 유효성 검사 통과 실패로 발생한 모든 필드들의 이름과 에러 원인을 각각 Map 객체에 기록하도록 하였다. 그리고 이 Map 객체를 클라이언트 응답에 포함시켜 반환하도록 고안하였다. 

위와 같이 구성하면, 회원 가입 시 입력되는 모든 데이터들에 대해 유효성 검사를 적용했을 때 유효하지 않은 몇몇 데이터를 입력하면 다음과 같이 어떤 데이터에서 어떻게 유효하지 않은지를 클라이언트에 보여줄 수 있게 된다. 

![사진 4-1. 유효성 검사 실패 시 예외 처리를 이용하여 유효하지 않은 데이터들의 정보를 응답 데이터로 전송한 결과.](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/9.png)

사진 4-1. 유효성 검사 실패 시 예외 처리를 이용하여 유효하지 않은 데이터들의 정보를 응답 데이터로 전송한 결과.

# 글을 마치며…

지금까지 스프링에서의 예외 처리 방법에 대해 살펴보았다. 스프링에서의 예외 처리 방법은 생각보다 어렵지 않음을 알게 되었다. 이러한 예외 처리 방법과 커스텀 예외 클래스까지 같이 사용하니 예전에 필자가 사용하던 방식보다 컨트롤러 계층에서의 코드의 양이 한층 줄어들어 필자 개인적으로도 마음에 드는 방식이다(커스텀 예외를 사용하는 것은 이전에도 알고 활용하긴 했었다). 

그리고 사실 위 글에서 소개한 코드들에서 설명 또는 아예 코드 자체를 생략한 것들이 있다. 이들은 모두 응답 데이터를 구성하는 방식에 대한 코드들이었고, 이에 대한 주제는 별도의 글로 다루고자 하기 위함이다. 이 글에서는 “스프링에서의 예외 처리 방법”이 메인 주제이기에 이 핵심을 흐리지 않기 위해 생략한 것도 있다. 그래서 “응답 데이터를 구성하는 체계적인 방식”에 대한 고찰은 사실상 해당 주제를 다루는 별도의 글에서 본격적으로 다룰 것이다. 이번 글에서는 그 고찰을 위한 기초 단계라 보면 되겠다. 

이 글에서 필자가 작성한 코드들이 포함된 전체 소스 코드는 다음의 사이트에서 확인 가능하다. 

[spring-study/ExceptionHandling at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/ExceptionHandling)

---

References

[1] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[2] 

[Spring Boot에선 예외를 어떻게 처리할까?(전역, 커스텀 예외처리)](https://dev-allday.tistory.com/77)

[3]

[[SpringBoot] Custom Exception 처리](https://velog.io/@rungoat/SpringBoot-Custom-Exception-%EC%B2%98%EB%A6%AC)

[4] `@ResponseStatus` 어노테이션

[ResponseEntity vs @ResponseStatus](https://joojimin.tistory.com/54)

[5] `@ResponseStatus` 와 `ResponseEntity` 동시 사용

[ResponseEntity vs @ResponseStatus 차이와 동시 사용 시 발생할 문제점](https://strong-park.tistory.com/entry/ResponseEntity-vs-ResponseStatus-%EC%B0%A8%EC%9D%B4%EC%99%80-%EB%8F%99%EC%8B%9C-%EC%82%AC%EC%9A%A9-%EC%8B%9C-%EB%B0%9C%EC%83%9D%ED%95%A0-%EB%AC%B8%EC%A0%9C%EC%A0%90)

[6] `@ResponseStatus` 어노테이션 - 해당 어노테이션은 SSR 방식에 적합하지, REST API 방식에는 적합하지 않다고 함. 

[ResponseEntity와 @ResponseStatus의 차이](https://velog.io/@haroya01/ResponseEntity%EC%99%80-ResponseStatus%EC%9D%98-%EC%B0%A8%EC%9D%B4)

[7] 유효성 검사 예외 시 처리.

[스프링 부트 유효성 검증 예외처리하는 방법(jakarta validation)](https://jincchan.tistory.com/42)

[8] 스프링 예외 처리 관련.

[[Spring] 예외처리 (@ExceptionHandler , @ControllerAdvice,@RestControllerAdvice), 에러코드,실제 적용 \[미완\]](https://keeeeeepgoing.tistory.com/175)

[9] Filter VS Interceptor

[[Spring] 필터(Filter) vs 인터셉터(Interceptor) 차이 및 용도 - (1)](https://mangkyu.tistory.com/173)

[10] 스프링에서의 예외 전파 구조에 대한 설명이 있음. 

[[Spring] 스프링의 다양한 예외 처리 방법(ExceptionHandler, ControllerAdvice 등) 완벽하게 이해하기 - (1/2)](https://mangkyu.tistory.com/204)

[11] Filter VS Interceptor VS AOP

[[Spring] Filter, Interceptor, AOP의 차이점에 대해](https://memodayoungee.tistory.com/114)

[12] Argument Resolver

[[Spring] Argument Resolver란 (HandlerMethodArgumentResolver, WebMvcConfigurer)](https://kangworld.tistory.com/289)

[13] 스프링 컨텍스트 구조

[[Spring] Dispatcher-Servlet(디스패처 서블릿)이란? 디스패처 서블릿의 개념과 동작 과정](https://mangkyu.tistory.com/18)