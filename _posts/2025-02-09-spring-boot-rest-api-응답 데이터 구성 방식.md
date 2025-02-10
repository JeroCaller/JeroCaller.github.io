---
title: "[Spring Boot][REST API] 응답 데이터 구성 방식"
category: "Spring"
tag: ["Spring Boot", "Spring", "REST API", "Response", "응답", "@JsonFilter", "HTTP Status", "예외 처리", "Error Code"]
---

이 글에서는 [“예외 처리와 커스텀 예외”](/spring/spring-boot-rest-api-%EC%98%88%EC%99%B8-%EC%B2%98%EB%A6%AC%EC%99%80-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%98%88%EC%99%B8/)라는 이전 글을 기반으로 하며, 스프링을 이용하여 REST API 제작 시 클라이언트에게 전송할 응답 데이터를 어떻게 구성할 것인가에 대해 다룰 것이다. 이전 글에서도 잠깐 언급했었지만, 필자는 기존에는 어떤 예외가 발생하느냐에 따른 응답을 컨트롤러 메서드 내에서 직접 결정하여 응답 데이터를 반환하는 방식을 주로 사용하였었다. 그러다보니 컨트롤러 내 코드의 양이 필요 이상으로 비대해진다는 문제점이 있었다. 이에 대해 스프링에서의 예외 처리 방식을 알게 되었고, 이를 이용하면 예외 발생 시의 응답 코드는 별도의 클래스 내에서 처리할 수 있게 되어 컨트롤러의 코드량이 줄어드는 이점을 체험하였다. 이 글에서는 이 방식을 참고하여 여기서 한 발자국 더 나아가, 클라이언트에게 전송할 응답 데이터를 생성하는 코드의 구성 방식에 대해 살펴보고자 한다. 여기서 언급될 방식이 정답은 아니고, 응답 데이터를 구성하는 방식 중 하나임을 참고바란다. 

# 응답 코드 목록 직접 정의하기

## 기존 HTTP Status Code의 한계점

이전 글인 [“예외 처리와 커스텀 예외”](/spring/spring-boot-rest-api-%EC%98%88%EC%99%B8-%EC%B2%98%EB%A6%AC%EC%99%80-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%98%88%EC%99%B8/) 에서 잠깐 언급한 코드가 있다. 그건 예외 발생 시, 또는 예외가 아니더라도 다양한 상황에 맞게끔 그에 알맞은 응답 코드를 enum 방식으로 구현했던 코드이다.

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

코드 1-1. CustomResponseCode.java

그리고 이를 이용하여 클라이언트에 응답으로 전송할 실질적인 데이터 역할을 하는 DTO 클래스를 다음과 같이 정의하였다. 

```java
package com.jerocaller.dto;

import java.util.Map;

import org.springframework.http.ResponseEntity;
import org.springframework.http.converter.json.MappingJacksonValue;

import com.fasterxml.jackson.annotation.JsonFilter;
import com.fasterxml.jackson.databind.ser.FilterProvider;
import com.fasterxml.jackson.databind.ser.impl.SimpleBeanPropertyFilter;
import com.fasterxml.jackson.databind.ser.impl.SimpleFilterProvider;
import com.jerocaller.common.CustomResponseCode;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ApiResponseCustom {
	
	@Builder.Default
	private CustomResponseCode responseCode = CustomResponseCode.OK;
	
	/**
	 * 클라이언트가 요청한 URI
	 */
	private String uri;
	
	/**
	 * 응답 데이터
	 */
	private Object data;
	
	/**
	 * 유효성 검사 실패 시의 에러 메시지 출력용. 
	 * 유효성 검사가 아닌 상황에서는 이 필드에 값을 넣지 않는다.
	 */
	private Map<String, String> validationMsg;
	
	@Getter
	@NoArgsConstructor
	@AllArgsConstructor
	@Builder
	@JsonFilter("validationJsonFilter")
	public static class DetailedApiResponseCustom {
		
		private String code;
		private String message;
		
		private Map<String, String> validationMsg;
		
		private String uri;
		private Object data;
		
	}
	
	public ResponseEntity<Object> toResponse() {
		
		DetailedApiResponseCustom response 
			= DetailedApiResponseCustom.builder()
			.code(this.getResponseCode().getCode())
			.message(this.getResponseCode().getMessage())
			.validationMsg(this.getValidationMsg())
			.uri(this.getUri())
			.data(data)
			.build();
		
		MappingJacksonValue mapped = getFilteredData(response);
		
		return ResponseEntity.status(this.getResponseCode()
				.getHttpStatus())
				.body(mapped);
		
	}
	
	/**
	 * JSON 응답용 DTO 클래스에서 특정 필드를 JSON 응답 프로퍼티에 추가할지 여부를 결정하는 메서드. 
	 * 
	 * validationMsg에 값이 하나라도 있으면 해당 필드를 응답 데이터에 포함시키고, 
	 * 그렇지 않으면 응답 데이터에서 제외시킨다. 
	 * 
	 * @param response
	 * @return
	 */
	private MappingJacksonValue getFilteredData(DetailedApiResponseCustom response) {
		
		SimpleBeanPropertyFilter filter;
		
		if (response.getValidationMsg() == null || 
			response.getValidationMsg().size() == 0
		) {
			// validationMsg 필드의 값이 null 이거나 데이터가 하나도 없을 경우 
			// 해당 필드를 JSON 응답 프로퍼티에서 제외시킨다. 
			filter = SimpleBeanPropertyFilter.serializeAllExcept("validationMsg");
		} else {
			// validationMsg 필드의 값이 존재한다면 해당 필드를 포함하여 
			// 모든 필드들을 JSON 응답 프로퍼티에 포함시킨다. 
			filter = SimpleBeanPropertyFilter.serializeAll();
		}
		
		// `@JsonFilter`로 지정된 필터명과 앞서 SimpleBeanPropertyFilter으로 
		// 해당 필터에 특정 필터 기능을 지정해놓은 filter를 등록. 
		FilterProvider filters = new SimpleFilterProvider()
			.addFilter("validationJsonFilter", filter);
		
		// 위에서 적용한 필터가 적용된 응답용 자바 객체를 반환. 
		MappingJacksonValue mapping = new MappingJacksonValue(response);
		mapping.setFilters(filters);
		
		return mapping;
	}

}

```

코드 1-2. ApiResponseCustom.java

위 코드의 `getFilteredData()` 메서드에 대해서는 후술할 예정이니 지금 당장은 무시해도 된다. 

그리고 위 DTO 클래스를 이용하여 컨트롤러 또는 예외 처리 메서드에서는 아래와 같은 방식으로 응답 데이터를 구성하여 클라이언트에게 전송하도록 하였다. 

```java
@PostMapping("/registration")
public ResponseEntity<Object> register( 
	@Valid @RequestBody MemberRegister register,
	HttpServletRequest request
) {
		
	return ApiResponseCustom.builder()
		.responseCode(CustomResponseCode.MEMBER_CREATED)
		.uri(request.getRequestURI())
		.data(register)
		.build()
		.toResponse();
}
```

코드 1-3. 컨트롤러 클래스 내 메서드 예시 코드.

위와 같은 체계를 이용하면 Swagger에서 다음과 같은 형식의 응답 데이터를 받을 수 있다. 

![사진 1-1. Swagger에서 받은 응답 데이터 형식의 예.](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/6.png)

사진 1-1. Swagger에서 받은 응답 데이터 형식의 예.

CustomResponseCode라는 커스텀 응답 코드를 정의한 후, 여기에 나열된 값들 중 하나를 ApiResponseCustom 객체에 할당시키면 해당 객체 내부에서는 이를 각각 개발자가 정의한 응답 코드와 그와 관련한 상세 메시지로 분리하여 응답 데이터를 생성하는 방식이다. 위 사진을 보면 알겠지만, 주목할 점은 HTTP Status Code가 아니라 개발자가 직접 정의한 응답 코드를 내보냈다는 것이다. HTTP Status Code를 사용하지 않은 이유는 이것만으로는 클라이언트에 전송할 리소스의 상태가 어떤지를 구체적으로 설명하는 데에 한계가 있었기 때문이다. 다시 코드 1-1을 보면 다음의 코드가 있음을 알 수 있을 것이다. 

```java
MEMBER_NOT_FOUND(HttpStatus.NOT_FOUND, "ACCOUNT-001", "조회된 회원 없음."),
FILE_NOT_FOUND(HttpStatus.NOT_FOUND, "FILE-001", "조회된 파일 없음.");
```

코드 1-4. 코드 1-1의 일부. 

HTTP Status 중에는 요청한 자원이 없을 경우 404 Not Found를 보여줄 수 있는데, 문제는 “Not Found” 만으로는 정확히 무엇을 찾을 수 없다는 것인지 직관적으로 알기 어렵다는 것이다. 서비스 계층의 하나의 메서드 내부에서 특정 회원의 파일을 찾는다고 가정할 때, 특정된 회원을 못찾을 수도 있고, 회원을 찾았다고 하더라도 그 회원의 특정 파일을 못 찾을 수도 있는 것이다. 이 경우에 그저 Not Found라고만 보내면 회원과 파일 둘 중 무엇을 못찾았다는 건지 클라이언트 입장에서는 알기가 어렵다. 이러한 모호성을 회피하기 위해 직접 응답 코드를 만들었던 것이다. 

또 다른 문제점이 있다. 클라이언트로부터 여러 개의 데이터를 서버에 저장하거나 조회해달라는 요청을 받았을 때, 어떠한 이유로 요청한 여러 개의 데이터들 중 일부만을 저장 또는 조회에 성공했다는 상황이 있다고 가정해보자. 이 경우 어쨌든 일부 데이터에 대해서는 작업이 성공했기에 이를 실패라고 보기엔 애매하다. 그래서 HTTP Status Code 중 200번 대 중에서 적절한 상태 코드를 찾아보았다. 필자도 팀 프로젝트에서 이것을 경험한 적이 있었는데, 그 때는 206 Partial Content라는 HTTP 상태 코드를 발견하였고, 그 이름과 상황이 매칭되는 것 같아서 이를 사용했었다. 그런데 나중에 알고보니 해당 코드는 동영상, 이미지 등의 파일을 청크 단위로 나눴을 때 클라이언트가 그 중 일부만을 요청하고, 그에 대한 응답이 성공했을 경우에 사용하는 코드라고 한다. 이는 필자가 원했던 것과 전혀 다른 내용이었다. 이 상황에서 만약 개발자가 206 Partial Content라는 응답 코드를 반환하면 클라이언트 측에서는 당황할지도 모른다. 이와 같이 때로는 HTTP Status Code만으로는 특정 상황을 정확히 묘사하기가 어렵다는 문제도 있다. 

이렇게, HTTP Status Code만 사용해서는 모호성들이 존재하기에 이러한 모호성을 없애기 위해서는 차라리 개발자가 응답 코드들을 직접 정의하고, 이에 대한 명세서를 클라이언트에 배포하여 각 응답 코드들의 의미를 알려주는 것이 더 나을 거란 생각이 들었다. 앞서 보인 코드 1-1도 이러한 점에서 응답 코드를 직접 정의한 것이었다. 

이렇게 응답 코드, 그 중에서도 에러 코드를 별도로 정의하여 사용하는 방식은 비단 필자만 사용하는 방식이 아니라고 단언할 수 있다. 이에 대해 조사했을 때 적지 않은 블로거들도 에러 코드를 직접 정의하는 방식에 대해 이미 소개하고 있었다. 뿐만 아니라 실제로 카카오에서 제공하는 OpenAPI 사이트인 [Kakao Developers](https://developers.kakao.com/docs/latest/ko/rest-api/reference) 에서도 에러 코드에 대해서는 별도로 정립하여 문서화하고 있고, [공공 데이터 포탈 사이트](https://www.data.go.kr/data/15102239/openapi.do)에서도 에러 코드를 별도로 정의하여 사용하고 있다. 이를 미루어볼 때 기존 HTTP Status Code만 사용하는 것이 아닌, 에러 코드를 직접 정의하여 사용하는 방식이 결코 불필요한 방식이 아님을 알 수 있을 것이다. 

![참고 사진 1-1. Kakao Developers에서의 에러 코드 명세 일부. [https://developers.kakao.com/docs/latest/ko/rest-api/reference](https://developers.kakao.com/docs/latest/ko/rest-api/reference) 에서 확인 가능하다. ](/images/2025-02-09/spring-boot-rest-api-%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B5%AC%EC%84%B1%20%EB%B0%A9%EC%8B%9D/1.png)

참고 사진 1-1. Kakao Developers에서의 에러 코드 명세 일부. [https://developers.kakao.com/docs/latest/ko/rest-api/reference](https://developers.kakao.com/docs/latest/ko/rest-api/reference) 에서 확인 가능하다. 

![참고 사진 1-2. 공공 데이터 포탈([https://www.data.go.kr/index.do](https://www.data.go.kr/index.do)) 에서 제공하는 “[기상청_전국 해수욕장 날씨 조회서비스](https://www.data.go.kr/data/15102239/openapi.do)” 이라는 OpenAPI의 명세 중 에러 코드 명세 일부. ](/images/2025-02-09/spring-boot-rest-api-%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B5%AC%EC%84%B1%20%EB%B0%A9%EC%8B%9D/2.png)

참고 사진 1-2. 공공 데이터 포탈([https://www.data.go.kr/index.do](https://www.data.go.kr/index.do)) 에서 제공하는 “[기상청_전국 해수욕장 날씨 조회서비스](https://www.data.go.kr/data/15102239/openapi.do)” 이라는 OpenAPI의 명세 중 에러 코드 명세 일부. 

물론 이렇게 에러 코드를 직접 정의하면 이에 대한 명세서도 작성해야 클라이언트도 식별할 수 있기 때문에 매우 수고롭긴 할 것이다. 

필자는 JSON 형태의 응답 데이터 형식의 일관성을 위해 에러 코드 뿐만 아니라 정상 상태의 응답 코드도 같이 정의하여 일원화하여 코드 1-1과 같이 작성해보았었다. 정상 상태의 응답 코드까지 정의하는 것에 대해서는 조사할 때는 찾아보기 힘들었는데, 그래서 이에 대해서는 넣을지 말지 고려해보는 것도 좋겠다. 

## 에러 코드를 커스텀 예외 클래스에 할당하여 컨트롤러 부담 줄이기

위와 같이 필자가 정의한 응답 코드 중 에러 코드에 대해서는 커스텀 예외 클래스와 함께 사용하는 것이 컨트롤러 계층의 부담을 줄여준다. 이는 이전 글인 [“예외 처리와 커스텀 예외”](/spring/spring-boot-rest-api-%EC%98%88%EC%99%B8-%EC%B2%98%EB%A6%AC%EC%99%80-%EC%BB%A4%EC%8A%A4%ED%85%80-%EC%98%88%EC%99%B8/) 에서도 다룬 적이 있다. 이번에는 앞서 정의한 에러 코드를 커스텀 예외 클래스와 결합하여 사용하는 관점에서 다시 한 번 살펴보겠다. 

먼저 모든 커스텀 예외 클래스들이 상속받도록 강제할 최상위 커스텀 예외 클래스를 다음과 같이 정의하였다. 

```java
package com.jerocaller.exception;

import com.jerocaller.common.CustomResponseCode;

import lombok.Getter;

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

코드 2-1. CustomException.java

위 코드에서는 앞서 코드 1-1로 정의한 CustomResponseCode의 값들 중 하나를 할당 받을 수 있도록 해당 타입의 필드를 선언하였고, 생성자 메서드를 통해 파생 클래스에서도 특정 값으로 초기화할 수 있도록 하였다. 

아래 코드는 위 코드 2-1을 상속받아 구현하는 커스텀 예외 클래스로, 각각 회원과 파일 정보가 없을 때 사용할 커스텀 예외 클래스이다. 

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

코드 2-2. MemberNotFoundException.java

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

코드 2-3. FileNotFoundException.java

위 두 개의 파생 클래스에서는 앞서 정의한 응답 코드 enum 객체에 정의한 값 중 하나를 각각 초기화하고 있는 것을 볼 수 있다. 

별도의 클래스를 만들어 앞서 만든 커스텀 예외에 대한 처리를 담당하도록 하였다. 

```java
@RestControllerAdvice
@RequiredArgsConstructor
@Slf4j
public class CustomExceptionHandlerTwo {
	
	private final HttpServletRequest request;
	
	// 생략...
	
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
	
	// 생략...
	
}
```

코드 2-4. CustomExceptionHandlerTwo.java

예를 들어, 특정 회원을 조회하는 API를 제작하고자 한다면 다음과 같이 컨트롤러 코드를 구성할 수 있다 .

```java
@RestController
@RequestMapping("/members")
@RequiredArgsConstructor
public class MemberController {
	
	private final MemberServiceinterface<MemberServiceCustomImpl> memberService;
	
	@GetMapping("/{id}")
	public ResponseEntity<Object> getMethodName(
		@PathVariable("id") int id,
		HttpServletRequest request
	) {
		
		int result = memberService.getById(id);

		return ApiResponseCustom.builder()
			.uri(request.getRequestURI())
			.data(result)
			.build()
			.toResponse();
	}
	
	// 생략...
	
}
```

코드 2-5. MemberController.java

컨트롤러 메서드에서는 서비스 로직의 결과가 정상적일 경우에 대해서만, 즉 정상 상태의 응답 데이터만을 구성하면 된다. 이는 앞서 커스텀 예외 클래스를 정의한 다음, 각각의 예외 클래스 내부에 이전에 정의한 에러 코드를 할당하여 매핑하고, 이러한 커스텀 예외를 별도의 클래스에서 처리하도록 하였기에 가능한 일이다. 이러한 방식을 이용하면 컨트롤러가 모든 응답 경우를 고려하지 않아도 되기에 코드량도 적어지고 부담도 덜해진다는 장점이 있다. 

# 특정 필드를 조건에 따라 선택적으로 응답 JSON에 추가하거나 제외하기

앞서 보았던 코드 1-2의 ApiResponseCustom 클래스는 클라이언트의 요청에 따라 JSON 형태로 응답을 보내기 위한 DTO 클래스이며, 모든 응답에 대해 공통적으로 사용할 클래스이기도 하다. 이 클래스 내부에는 클라이언트로부터 요청과 함께 넘어온 데이터의 형식이 유효한지를 검사하기 위한 유효성 검사 실행 후 유효하지 않은 데이터가 무엇이고 왜 유효하지 않은지 정보를 담기 위한 validationMsg이라는 필드가 정의되어 있는 것을 볼 수 있다. 그런데 이는 유효성 검사 시에만 필요한 필드이지 평상시에는 불필요한 필드이다. 따라서 유효성 검사 시에만 JSON 응답 데이터에 해당 필드를 포함시키고, 나머지 경우에는 아예 포함시키지 않도록 하고 싶다. 이럴 땐 어떻게 해야할까? 

이는 사실 앞선 코드 1-2에서 보인 바가 있다. ApiResponseCustom 클래스 내부에는 실질적으로 클라이언트에게 전송하기 위한 응답 데이터 구조를 이루는 DetailedApiResponseCustom 내부 클래스가 정의되어 있는 것을 볼 수 있다. 이렇게 응답 데이터가 될 DTO 클래스에 먼저 `@JsonFilter` 어노테이션을 적용해준다. 

```java
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@JsonFilter("validationJsonFilter")  // 적용
public static class DetailedApiResponseCustom {
		
	private String code;
	private String message;
		
	private Map<String, String> validationMsg;
		
	private String uri;
	private Object data;
		
}
```

코드 3-1. ApiResponseCustom.DetailedApiResponseCustom

그 후 ApiResponseCustom 클래스 내부에 다음의 메서드를 정의한다. 

```java
/**
	* JSON 응답용 DTO 클래스에서 특정 필드를 JSON 응답 프로퍼티에 추가할지 여부를 결정하는 메서드. 
	* 
	* validationMsg에 값이 하나라도 있으면 해당 필드를 응답 데이터에 포함시키고, 
	* 그렇지 않으면 응답 데이터에서 제외시킨다. 
	* 
	* @param response
	* @return
	*/
private MappingJacksonValue getFilteredData(DetailedApiResponseCustom response) {
		
	SimpleBeanPropertyFilter filter;
		
	if (response.getValidationMsg() == null || 
		response.getValidationMsg().size() == 0
	) {
		// validationMsg 필드의 값이 null 이거나 데이터가 하나도 없을 경우 
		// 해당 필드를 JSON 응답 프로퍼티에서 제외시킨다. 
		filter = SimpleBeanPropertyFilter.serializeAllExcept("validationMsg");
	} else {
		// validationMsg 필드의 값이 존재한다면 해당 필드를 포함하여 
		// 모든 필드들을 JSON 응답 프로퍼티에 포함시킨다. 
		filter = SimpleBeanPropertyFilter.serializeAll();
	}
		
	// `@JsonFilter`로 지정된 필터명과 앞서 SimpleBeanPropertyFilter으로 
	// 해당 필터에 특정 필터 기능을 지정해놓은 filter를 등록. 
	FilterProvider filters = new SimpleFilterProvider()
		.addFilter("validationJsonFilter", filter);
		
	// 위에서 적용한 필터가 적용된 응답용 자바 객체를 반환. 
	MappingJacksonValue mapping = new MappingJacksonValue(response);
	mapping.setFilters(filters);
		
	return mapping;
}
```

코드 3-2. ApiResponseCustom.getFilteredData()

validationMsg 필드에 값이 존재할 때에만 응답 데이터에 포함시키고, null이거나 크기가 0일 경우, 즉 아무것도 입력하지 않는 경우에는 해당 필드가 응답 데이터에서 제외되도록 필터링 처리하였다. 앞서 사용한 어노테이션 `@JsonFilter` 는 해당 어노테이션이 적용된 클래스가 위와 같은 응답 데이터 필터링의 대상으로 설정되도록 하는 역할인 셈이다. 

위 코드를 정리하면 다음과 같다. 

1. JSON 응답 데이터에 대해 특정 기준으로 필터링하기 위한 필터 변수를 `SimpleBeanPropertyFilter` 타입으로 선언한다. 
2. 여기서는 앞서 말했듯 `@JsonFilter` 가 적용된 필터 대상인 DetailedApiResponseCustom 클래스 내 validationMsg 필드에 값이 존재할 때에만 해당 필드를 JSON 응답 데이터에 포함시키는 필터를 구현할 것이다. 해당 필드에 값이 없는 경우 이 필드를 제외하기 위해 `SimpleBeanPropertyFilter.serializeAllExcept("validationMsg")` 과 같이 사용하여 `serializeAllExcept` 메서드에 제외하고자 하는 필드명을 문자열로 대입한다. 만약 해당 필드에 값이 있으면 `serializeAll()` 메서드를 호출하여 해당 필드 포함 모든 필드들을 응답 데이터에 포함시킨다.
3. 이로서 생성된 필터 객체를 FilterProvider에 등록한다. `SimpleFilterProvider()` 객체의 `addFilter()` 메서드에 첫 번째 인자로는 `@JsonFilter` 어노테이션 속성값을, 두 번째 인자로는 방금 만든 필터 객체를 대입하여 등록한다. 
4. JSON 응답 데이터로 매핑하기 위한 MappingJacksonValue 객체를 생성하고, 이 때 응답 데이터의 대상이 될 DTO 클래스를 생성자 메서드 인자로 대입한다. 
5. 앞서 생성한 MappingJacksonValue 객체에 적용할 필터를 `setFilter()`를 통해 설정한다. 
6. 5번 과정으로 인해 필터가 등록된 MappingJacksonValue 객체를 반환한다. 

위와 같은 과정을 거쳐 특정 필드를 조건에 따라 응답 데이터에 포함시킬수도, 제외시킬 수도 있는 것이다. 이렇게 필터링된 데이터를 적용, 반환하는 코드는 다음과 같다. 

```java
public ResponseEntity<Object> toResponse() {
		
	DetailedApiResponseCustom response 
		= DetailedApiResponseCustom.builder()
		.code(this.getResponseCode().getCode())
		.message(this.getResponseCode().getMessage())
		.validationMsg(this.getValidationMsg())
		.uri(this.getUri())
		.data(data)
		.build();
		
	MappingJacksonValue mapped = getFilteredData(response);
		
	return ResponseEntity.status(this.getResponseCode()
		.getHttpStatus())
		.body(mapped);
}
```

코드 3-3. ApiResponseCustom.toResponse()

`MappingJacksonValue mapped = getFilteredData(response);` 이 코드를 통해 앞서 만든 `getFilteredData()` 메서드를 호출하여 필터링된 객체를 얻고 이를 ResponseEntity의 body에 실어 반환하면 되는 것이다. 위 코드 3-1~3이 합쳐져 코드 1-2를 구성하고 있다. 그리고 이렇게 만든 ApiResponseCustom 클래스를 호출하여 사용하는 것은 앞선 코드 2-4~5에서 살펴볼 수 있다. 

Swagger에서는 다음과 같은 결과를 얻을 수 있다. 먼저 유효성 검사와 전혀 연관이 없는 API 호출 후의 응답 데이터 모습이다. 

![사진 2-1. 유효성 검사와 전혀 상관없는 API 호출 시의 응답 데이터 구조.](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/7.png)

사진 2-1. 유효성 검사와 전혀 상관없는 API 호출 시의 응답 데이터 구조.

해당 API를 구성하는 컨트롤러 메서드와 예외 처리 코드에서는 ApiResponseCustom 객체 생성 시 validationMsg 필드에 아무런 값도 할당하지 않았다. 이로 인해 앞선 코드 3-2의 JSON 응답 데이터 필터링 메서드에 의해 해당 필드가 응답 데이터에 포함되지 않은 것을 볼 수 있다. 

다음은 클라이언트로부터 요청 받은 데이터에 유효성 검사를 거치는 API 호출 시의 모습이다. 

![사진 2-2. 클라이언트로부터 요청 받은 데이터에 유효성 검사를 거칠 때 특정 데이터가 유효하지 않을 경우의 응답 데이터 구조. ](/images/2025-02-07/spring-boot-rest-api-%EC%98%88%EC%99%B8%20%EC%B2%98%EB%A6%AC%EC%99%80%20%EC%BB%A4%EC%8A%A4%ED%85%80%20%EC%98%88%EC%99%B8/9.png)

사진 2-2. 클라이언트로부터 요청 받은 데이터에 유효성 검사를 거칠 때 특정 데이터가 유효하지 않을 경우의 응답 데이터 구조. 

위 사진에서는 클라이언트가 요청으로 전송한 데이터들 중 일부가 유효하지 않을 경우 어떤 데이터가 어떻게 유효하지 않은 지에 대한 정보를 서버 내에서 validationMsg 필드에 할당하였기 때문에 해당 필드가 응답 데이터에 보이는 것을 볼 수 있다. 다음은 똑같은 API에 대해 모든 데이터가 유효성 검사를 통과할 경우의 모습이다. 

![사진 2-3. 클라이언트로부터 요청받은 데이터 모두가 유효성 검사에 통과했을 떄의 응답 데이터 구조.](/images/2025-02-09/spring-boot-rest-api-%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B5%AC%EC%84%B1%20%EB%B0%A9%EC%8B%9D/3.png)

사진 2-3. 클라이언트로부터 요청받은 데이터 모두가 유효성 검사에 통과했을 떄의 응답 데이터 구조.

모든 데이터가 유효성 검사에 통과하였으므로 서버 내에서는 validationMsg 필드에 할당할 값이 없고, 이로 인해 해당 필드가 응답 데이터에 포함되지 않은 것이다. 이와 같이 특정 DTO 클래스의 필드를 조건에 따라 JSON 응답 데이터에 포함시킬수도, 제외시킬 수도 있는 것이다. 

앞서 소개한 방법 이외에도, 아예 조건에 상관없이 DTO 클래스 내 특정 필드를 무조건 응답 데이터에서 제외시키고자 한다면 특정 필드에 `@JsonIgnore` 어노테이션을 적용하면 되고, 여러 필드들을 한꺼번에 응답 데이터에서 제외시키고자 한다면 이 어노테이션 대신 `@JsonIgnoreProperties({"필드명1", "필드명2", ...})` 어노테이션을 DTO 클래스 수준에 적용시키면 된다. 이를 이용하면 민감한 정보를 JSON 응답 데이터에서 제외시켜 보안을 지키는 방법으로 활용될 수도 있는 것이다. 

# 글을 마치며…

이번 글에서는 REST API 제작 시 응답 데이터를 구성하는 방식에 대해 살펴보았다. 이에 대해 이 글에서 다룬 핵심들을 정리하면 다음과 같다. 

- 응답 코드(에러 코드) 직접 정의하여 사용하기
- 커스텀 예외 클래스를 생성하고 각각의 예외 클래스에 에러 코드를 할당하고, 스프링에서 제공하는 예외 처리 방식을 활용하여 컨트롤러의 부담 줄이기
- DTO 클래스 내 특정 필드를 응답 데이터에서 조건에 따라 제외 또는 포함시키는 방법

이전 글에서 다룬 스프링에서 제공하는 예외 처리와 더불어 이번 글에서 다룬 개념들을 공부하니, 클라이언트에게 응답을 전송하는 것에도 이렇게 정성을 들여야 한다는 것, 그리고 그와 관련된 개념들이 은근 많다는 것을 느끼게 되었다. 그럼에도 응답 데이터를 구성하는 방식을 체계화할 수 있겠다는 생각도 들어 매우 유익했다. 다른 예제 코드를 작성하거나 실제 프로젝트에서도 이러한 방식들을 사용하여 응답 데이터를 구성해봐야겠다. 

---

References

[1] [Spring - REST API에서 직접 정의한 Error code를 사용하는 이유!](https://jaehoney.tistory.com/240)

[2] [Best Practices for REST API Error Handling \| Baeldung](https://www.baeldung.com/rest-api-error-handling-best-practices)

[3] 응답용 DTO의 특정 필드를 JSON 응답에서 제외시키는 방법.

[[Project] 2차 프로젝트 - ResponseDTO의 특정 필드 JSON 응답에서 제외시키기 - keepgoing](https://keepgoing.inblog.ai/project-2%EC%B0%A8-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-responsedto%EC%9D%98-%ED%8A%B9%EC%A0%95-%ED%95%84%EB%93%9C-json-%EC%9D%91%EB%8B%B5%EC%97%90%EC%84%9C-%EC%A0%9C%EC%99%B8%EC%8B%9C%ED%82%A4%EA%B8%B0-33179)

[4] `@JsonFilter` 를 이용한 JSON 응답 시 사용될 필드값 필터링.

[[Spring] @JsonFilter 를 이용한 필드값 필터링하기](https://bepoz-study-diary.tistory.com/188)

[5] 206 partial content에 대한 설명이 있음.

[그림 Http&Network 4장\] 결과를 전달하는 HTTP 상태 코드](https://hololo-kumo.tistory.com/218)

[6] 206 partial content

[206 Partial Content - HTTP \| MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status/206)

[7] Kakao Developers 제공 API 명세

[Kakao Developers](https://developers.kakao.com/docs/latest/ko/rest-api/reference)

[8] 공공 데이터 포탈 내 OpenAPI 중 하나. 

[기상청_전국 해수욕장 날씨 조회서비스](https://www.data.go.kr/data/15102239/openapi.do)