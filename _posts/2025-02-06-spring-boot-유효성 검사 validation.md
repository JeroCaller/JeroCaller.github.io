---
title: "[Spring Boot] 유효성 검사 Validation"
category: "Spring"
tag: ["Spring Boot", "Spring", "Validation", "유효성 검사", "Bean Validation", "Hibernate Validator"]
---

애플리케이션의 비즈니스 로직이 예외 상황을 겪지 않고 제대로 동작하기 위해 중요한 것 중 하나는 비즈니스 로직에서 다룰 데이터가 유효한지를 사전에 검증하는 것이다. 이렇게 입력된 데이터가 유효한지 사전에 검증하는 것을 유효성 검사 또는 데이터 검증이라고 한다. 

스프링 프레임워크로 웹 앱을 작성할 때에는 컨트롤러, 서비스, 레포지토리 등 여러 계층으로 나눠 개발을 할 것이고, 각 계층마다 DTO와 같은 객체를 이용하여 데이터를 주고받을 것인데, 각 계층에서 입력받을 데이터를 검사하는 것이 유효성 검사의 예가 될 것이다. 그런데 이러한 유효성 검사를 매번 해당 계층 내에서 실행하도록 구성하면 다음의 문제점들이 발생할 수 있다.

- 검증 로직을 계층별로 작성하면 이는 계층 별로 검증 로직이 흩어져 있다는 뜻으로, 흩어져 있는 검증 로직들을 하나로 관리하기가 어려워진다.
- 흩어진 검증 로직들에 서로 중복된 코드들이 있을 수 있다.
- 검증해야할 값들이 많아질수록 검증을 위한 코드의 양이 많아지며 이는 코드의 복잡성 증가와 가독성의 저하로 이어질 수 있다.
- 애초에 대부분의 계층들은 데이터 검증을 위한 계층이 아니라 자신의 본래 작업을 수행하기 위한 계층들이다. 컨트롤러 계층은 오로지 클라이언트의 요청을 받고 응답을 생성하는 곳이고, 서비스 계층은 오로지 비즈니스 로직을 수행하는 계층인데, 각 계층마다 데이터 검증 로직을 추가한다는 것은 한 계층의 책임이 둘 이상으로 늘어난다는 것이다. 이는 단일 책임 원칙에도 위배되어 별로 좋지 않다.

이러한 계층별 검증 로직의 문제점을 해결하기 위해 떠올릴 수 있는 방법 중 하나로는 아예 검증 로직을 각 계층마다 두는 것이 아니라 데이터를 전달하는 DTO 객체 자체에 어노테이션 형태로 적용하는 것이다. 이를 위해 Bean Validation[3]이라는 유효성 검사를 위한 명세서가 나타났으며, 스프링 부트에서는 이의 구현체인 Hibernate Validator를 표준으로 택하고 있다고 한다. 

<div style="text-align:center; margin:1em;">
<img src="https://docs.jboss.org/hibernate/validator/5.4/reference/en-US/html_single/images/application-layers.png" alt="image">
</div>

참고사진 1-1. 데이터 검증 로직을 각 계층마다 할 경우의 도식도. [6] 참고함.


<div style="text-align:center; margin:1em;">
<img src="https://docs.jboss.org/hibernate/validator/5.4/reference/en-US/html_single/images/application-layers2.png" alt="image">
</div>

참고사진 1-2. 데이터 검증 로직을 DTO 등의 데이터 전달 객체 자체에 둘 때의 도식도. [6] 참고함.

# 스프링 부트에서 유효성 검사 사용하기

스프링 부트에서 유효성 검사를 사용하기 위해선 관련 의존성을 추가해야 한다. 이클립스 내에서 Spring Starter Project를 이용하면 다음과 같이 I/O 탭에 있는 “Validation”을 추가하면 된다.

<div style="text-align:center; margin:1em;">
<img src="/images/2025-02-06/spring-boot-%EC%9C%A0%ED%9A%A8%EC%84%B1%20%EA%B2%80%EC%82%AC%20validation/1.png" alt="image">
</div>

사진 1-1. 이클립스에서의 Spring Starter Project를 이용하여 새 프로젝트 생성 시 유효성 검사 의존성을 추가하는 모습.

Gradle 기준으로 다음의 의존성이 추가된다.

```java
dependencies {
  // 생략...
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	// 생략...
}
```

유효성 검사 라이브러리를 사용해보기 위해 회원 가입 정보를 받는 REST API를 제작해보겠다. 회원 가입 정보를 받기 위한 DTO 클래스를 다음과 같이 정의하였다. 

```java
package com.jerocaller.dto;

import java.time.LocalDate;

import jakarta.validation.constraints.AssertTrue;
import jakarta.validation.constraints.Email;
import jakarta.validation.constraints.Max;
import jakarta.validation.constraints.Min;
import jakarta.validation.constraints.NotBlank;
import jakarta.validation.constraints.Past;
import jakarta.validation.constraints.Pattern;
import jakarta.validation.constraints.Positive;
import jakarta.validation.constraints.Size;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@ToString
public class MemberRequest implements RegisterRequest {
	
	/**
	 * 닉네임.
	 * null 및 "", " "은 불가. 
	 * 닉네임 문자열의 길이는 5이상 20이하의 범위만 허용.
	 */
	@NotBlank
	@Size(min = 5, max = 20)
	String nickname;
	
	/**
	 * 이메일 형식 검사.
	 */
	@Email
	String email;
	
	/**
	 * 현재보다 과거의 일시만 생년월일로 입력 가능.
	 */
	@Past
	LocalDate birthDate;
	
	/**
	 * 정규식을 이용하여 전화번호 형식 유효성 검증.
	 */
	// 01x-xxx(x)-xxxx
	@Pattern(regexp = "01\\d-\\d{3,4}-\\d{4}")
	String phone;
	
	/**
	 * 정수형으로 표현될 수 있는 회원 가입 가능한 나이는 
	 * 20세부터 80세까지로 지정.
	 */
	@Min(value = 20)
	@Max(value = 80)
	int age;
	
	/**
	 * 이 사이트에 쳐음 회원가입하는지 여부.
	 * 여기선 True만 허용.
	 */
	@AssertTrue
	boolean areYouNew;
	
	/**
	 * 회원 가입 시 발급받은 쿠폰 일련번호. 
	 * 양수만 유효.
	 */
	@Positive
	int couponCode;
	
}

```

코드 1-1. MemberRequest.java

여기서 RegisterRequest 인터페이스는 필자가 만든 인터페이스로, 유효성 검사와는 상관없으니 무시해도 된다. 

위 코드에서 각각의 필드에 다양한 유효성 검사 어노테이션들이 사용되었다. 예를 들어 nickname 필드에는 클라이언트가 공백 입력 또는 아무것도 입력하지 않는 것을 방지하기 위해 `@NotBlank` 라는 어노테이션을 사용하였으며, 아이디 길이 제한을 위해 `@Size` 라는 어노테이션도 적용하였다. 이렇게 유효성 검사용 어노테이션을 필드에 적용함으로써 해당 필드가 받을 데이터의 유효성 검사가 자동으로 수행되는 방식이다. 이로 인해 유효성 검사 로직을 개발자가 굳이 만들 필요도 없어 번거로움을 해소할 수 있다. 

위 DTO 클래스는 컨트롤러의 파라미터로 사용될 것이다. 위 코드에서 작성한 유효성 검사 어노테이션들이 제 기능을 하려면 파라미터 앞에 `@Valid` 어노테이션을 붙여야 한다. 다음은 클라이언트로부터 회원 가입 정보를 받기 위한 컨트롤러이다. 

```java
package com.jerocaller.controller;

import org.springframework.http.ResponseEntity;
import org.springframework.validation.annotation.Validated;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.jerocaller.dto.MemberRequest;
import com.jerocaller.dto.MemberRequestThree;
import com.jerocaller.dto.MemberRequestTwo;
import com.jerocaller.dto.RegisterRequest;
import com.jerocaller.validation.ValidationGroupOne;
import com.jerocaller.validation.ValidationGroupTwo;

import jakarta.validation.Valid;
import lombok.extern.slf4j.Slf4j;

@RestController
@RequestMapping("/members")
@Slf4j
public class MemberController {
	
	@PostMapping
	public ResponseEntity<Object> register(
		@Valid @RequestBody MemberRequest memberRequest
	) {
		
		logRegisterRequest(memberRequest);
		
		return ResponseEntity.ok(memberRequest);
		
	}
	
	private void logRegisterRequest(RegisterRequest request) {
		log.info("=== 새 회원 가입 요청 결과 ===");
		log.info(request.toString());
	}
	
}

```

코드 1-2. MemberController.java

요청으로 전달된 회원 가입 정보의 유효성 검사에 문제가 없다면 회원 가입 정보를 그대로 응답하도록 구성하였다. 

이제 이를 Swagger에서 실행해보자. request body에 다음과 같은 JSON 형태의 데이터를 작성하면,

```java
{
  "nickname": "kimquel1234",
  "email": "sql@mymail.net",
  "birthDate": "1990-03-04",
  "phone": "010-1111-2222",
  "age": 24,
  "areYouNew": true,
  "couponCode": 1073
}
```

데이터 1-1.


<div style="text-align:center; margin:1em;">
<img src="/images/2025-02-06/spring-boot-%EC%9C%A0%ED%9A%A8%EC%84%B1%20%EA%B2%80%EC%82%AC%20validation/2.png" alt="image">
</div>

사진 1-2. 유효성 검사 성공 시 모습.

위 사진과 같이 요청 정보가 그대로 응답되면서, HTTP Status Code는 200으로 유효성 검사를 통과하게 된다. 

이제 일부러 유효하지 않은 데이터로 바꿔보겠다. 

```java
{
  "nickname": "kimquel1234",
  "email": "sql@mymail.net",
  "birthDate": "2050-10-21",
  "phone": "010-1111-2222",
  "age": 242,
  "areYouNew": true,
  "couponCode": -1073
}
```

데이터 1-2. 

위 데이터는 각각 birthDate, age, couponCode가 유효성 검사에서 실패할 데이터들로 바뀌었다. 이대로 다시 Swagger에서 실행시켜보면 응답 메시지가 이전과 다르게 되어있을 것이다.

```java
Resolved [org.springframework.web.bind.MethodArgumentNotValidException: Validation failed for argument [0] in public org.springframework.http.ResponseEntity<java.lang.Object> com.jerocaller.controller.MemberController.register(com.jerocaller.dto.MemberRequest) with 3 errors: [Field error in object 'memberRequest' on field 'birthDate': rejected value [2050-10-21]; codes [Past.memberRequest.birthDate,Past.birthDate,Past.java.time.LocalDate,Past]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [memberRequest.birthDate,birthDate]; arguments []; default message [birthDate]]; default message [과거 날짜여야 합니다]] [Field error in object 'memberRequest' on field 'couponCode': rejected value [-1073]; codes [Positive.memberRequest.couponCode,Positive.couponCode,Positive.int,Positive]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [memberRequest.couponCode,couponCode]; arguments []; default message [couponCode]]; default message [0보다 커야 합니다]] [Field error in object 'memberRequest' on field 'age': rejected value [242]; codes [Max.memberRequest.age,Max.age,Max.int,Max]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [memberRequest.age,age]; arguments []; default message [age],80]; default message [80 이하여야 합니다]] ]
```

응답 상태 코드는 400 bad request로 응답되며, 위와 같은 예외 메시지가 뜬다. 자세히 보면 총 3개의 에러가 발생했다고 뜨는데, birthDate에 대해선 “과거 날짜여야 합니다”라는 문구가, couponCode에 대해선 “0보다 커야 합니다”, age에 대해선 “80 이하여야 합니다”라는 문구가 출력되었다. 이를 통해 의도했던 대로 DTO에 적용했던 유효성 검사 어노테이션들이 제 기능을 하여 유효하지 않은 데이터를 잘 검사했다는 것을 알 수 있다. 

개발자가 스프링 부트에서 사용 가능한 유효성 검사용 어노테이션에는 다음과 같이 존재한다고 한다. 

## 유효성 검사용 어노테이션 목록

문자열 검증

| 어노테이션 | 설명 |
| --- | --- |
| `@Null` | null 값만 허용 |
| `@NotNull` | null 값 비허용. “” 처럼 아예 값이 없거나 “ “ 처럼 빈 공백만 있는 경우는 허용. |
| `@NotEmpty` | null, “” 비허용. “ “은 허용. |
| `@NotBlank` | null, “”, “ “ 모두 비허용 |

최대, 최소값 검증 (BigDecimal, BigInteger, int, long 등의 타입 지원)

| 어노테이션 | 설명 |
| --- | --- |
| `@DecimalMax(value="numberString"` | 문자열 형태의 값보다 작은 값만을 허용 |
| `@DecimalMin(value="numberString"` | value보다 큰 값만 허용 |
| `@Min(value=number)` | value 포함 그 이상의 값만 허용 |
| `@Max(value=number)` | value 포함 그 이하의 값만 허용 |

값 범위 검증 (BigDecimal, BigInteger, int, long 등의 타입 지원)

| 어노테이션 | 설명 |
| --- | --- |
| `@Positive` | 양수 허용. 0은 비허용 |
| `@PositiveOrZero` | 0을 포함한 양수 허용. |
| `@Negative` | 음수 허용. 0은 비허용. |
| `@NegativeOrZero` | 0을 포함한 음수 허용. |

시간 검증 (Date, LocalDate, LocalDateTime 등의 타입 지원)

| 어노테이션 | 설명 |
| --- | --- |
| `@Future` | 현재보다 미래의 날짜 허용 |
| `@FutureOrPresent` | 현재 포함 미래 날짜 허용. |
| `@Past` | 현재보다 과거 날짜 허용. |
| `@PastOrPresent` | 현재 포함 과거 날짜 허용. |

Boolean 검증

| 어노테이션 | 설명 |
| --- | --- |
| `@AssertTrue` | true만 허용. null은 검증하지 않음.  |
| `@AssertFalse` | false만 허용. null은 검증하지 않음.  |

그외 검증

| 어노테이션 | 검증 |
| --- | --- |
| `@Email` | 이메일 형식인지 검증. “”는 허용. 값에 `@` 가 있는지 검증하는 방식이라 엄격한 검증 방식은 아님.  |
| `@Digits(integer = num1, fraction = num2)` | num1의 정수 자릿수 및 num2의 소수 자릿수 허용. |
| `@Pattern(regexp = "expression")` | 주어진 정규식에 일치하는지 검증. 정규식은 java.util.regex.Pattern 패키지의 컨벤션을 따른다고 함. |

# 특정 그룹에만 유효성 검증 적용하기 - `@Validated`

유효성 검사를 적용할 여러 필드들에도 그룹별로 나눠서 특정 필드들에만 선택적으로 유효성 검사를 적용 또는 미적용시킬 수도 있다. 이를 위해 메서드 파라미터에는 `@Valid` 대신 `@Validated` 어노테이션을 사용하며, 유효성 검사가 적용될 DTO 필드에 적용된 어노테이션의 groups 속성을 통해 몇몇 유효성 검사 필드들을 특정 그룹으로 묶을 수 있다. 

다음은 새로운 회원 가입용 DTO 클래스를 생성하여 필드들 중 특정 필드의 유효성 검사 어노테이션을 특정 그룹으로 묶은 코드이다. 

```java
import com.jerocaller.validation.ValidationGroupOne;
import com.jerocaller.validation.ValidationGroupTwo;

// 생략...,

@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@ToString
public class MemberRequestTwo implements RegisterRequest {
	
	/**
	 * 닉네임.
	 * null 및 "", " "은 불가. 
	 * 닉네임 문자열의 길이는 5이상 20이하의 범위만 허용.
	 */
	@NotBlank
	@Size(min = 5, max = 20)
	String nickname;
	
	/**
	 * 이메일 형식 검사.
	 */
	@Email
	String email;
	
	/**
	 * 현재보다 과거의 일시만 생년월일로 입력 가능.
	 */
	@Past
	LocalDate birthDate;
	
	/**
	 * 정규식을 이용하여 전화번호 형식 유효성 검증.
	 */
	// 01x-xxx(x)-xxxx
	@Pattern(regexp = "01\\d-\\d{3,4}-\\d{4}")
	String phone;
	
	/**
	 * 정수형으로 표현될 수 있는 회원 가입 가능한 나이는 
	 * 20세부터 80세까지로 지정.
	 */
	@Min(value = 20, groups = ValidationGroupOne.class)
	@Max(value = 80, groups = ValidationGroupOne.class)
	int age;
	
	/**
	 * 이 사이트에 쳐음 회원가입하는지 여부.
	 * 여기선 True만 허용.
	 */
	@AssertTrue
	boolean areYouNew;
	
	/**
	 * 회원 가입 시 발급받은 쿠폰 일련번호. 
	 * 양수만 유효.
	 */
	@Positive(groups = ValidationGroupTwo.class)
	int couponCode;
	
}

```

코드 2-1. MemberRequestTwo.java

위 코드에서는 age 필드는 `ValidationGroupOne` 이라는 인터페이스를 groups 속성으로 지정하여 그룹화하였고, couponCode 필드에서는 `ValidationGroupTwo` 라는 인터페이스를 groups 속성으로 지정하여 또 다른 그룹으로 묶었다. 각각의 인터페이스들은 그저 정의만 하면 될 뿐 그 안에 아무것도 입력하지 않아도 된다. 

위 DTO 객체를 테스트하기 위해 다음과 같은 메서드를 기존 컨트롤러 클래스에 추가하였다. 

```java
import org.springframework.validation.annotation.Validated;

import com.jerocaller.validation.ValidationGroupOne;
import com.jerocaller.validation.ValidationGroupTwo;

// 생략...

@RestController
@RequestMapping("/members")
@Slf4j
public class MemberController {
	
	// 생략...
	
	@PostMapping("/validation")
	public ResponseEntity<Object> registerValidation(
		@Validated @RequestBody MemberRequestTwo request
	) {
		
		logRegisterRequest(request);
		
		return ResponseEntity.ok(request);
		
	}
	
	@PostMapping("/validation/group/one")
	public ResponseEntity<Object> registerValidationOne(
		@Validated(ValidationGroupOne.class) 
		@RequestBody MemberRequestTwo request
	) {
		
		logRegisterRequest(request);
		
		return ResponseEntity.ok(request);
		
	}
	
	@PostMapping("/validation/group/two")
	public ResponseEntity<Object> registerValidationTwo(
		@Validated(ValidationGroupTwo.class)
		@RequestBody MemberRequestTwo request
	) {
		
		logRegisterRequest(request);
		
		return ResponseEntity.ok(request);

	}
	
	// 생략...
	
}

```

코드 2-2. MemberController.java

위 코드를 보면 알겠지만 `@Valid` 와 똑같이 유효성 검사를 적용시킬 DTO 객체 파라미터에 `@Validated` 어노테이션을 적용한 것을 볼 수 있다. 해당 어노테이션에 들어갈 속성값은 생략해도 되고, 특정 그룹 인터페이스 클래스를 넣을 수도 있다. 

Swagger에서 Request Body에 다음과 같은 데이터를 작성하고 실행해보았다.

```java
{
  "nickname": "kimquel1234",
  "email": "sql@mymail.net",
  "birthDate": "1990-01-10",
  "phone": "010-1111-2222",
  "age": 242,
  "areYouNew": true,
  "couponCode": -1073
}
```

데이터 2-1.

분명 위 데이터에서는 age, couponCode가 유효하지 않은 데이터임을 알 수 있다. 그런데 `/members/validation` 엔드포인트에 대해 적용하면 200번의 HTTP status code를 받게 된다. 즉, 유효성 검사를 모두 통과했다는 결과를 받는다. 이는 `@Validated` 어노테이션에 아무런 그룹 인터페이스를 속성으로 넣지 않으면, 그룹화되지 않은 유효성 검사 어노테이션만 실행되어 유효성 검사가 실행되기 때문이다. 그룹화 되지 않은 어노테이션에 대해서는 여전히 유효성 검사가 실행되므로, age, couponCode를 제외한 다른 필드에서 유효하지 않은 데이터를 넣으면 여전히 이를 알아챈다. 

이번에는 `/members/validation/group/one` 엔드포인트를 똑같은 데이터로 실행해보면 다음의 결과를 얻는다.

```java
Resolved [org.springframework.web.bind.MethodArgumentNotValidException: Validation failed for argument [0] in public org.springframework.http.ResponseEntity<java.lang.Object> com.jerocaller.controller.MemberController.registerValidationOne(com.jerocaller.dto.MemberRequestTwo): [Field error in object 'memberRequestTwo' on field 'age': rejected value [242]; codes [Max.memberRequestTwo.age,Max.age,Max.int,Max]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [memberRequestTwo.age,age]; arguments []; default message [age],80]; default message [80 이하여야 합니다]] ]
```

에러 메시지를 자세히 보면 age 필드에 대해서만 유효성 검사를 통과하지 못했다고 뜨는데, 이는 `@Validated` 어노테이션 속성에 `ValidationGroupOne` 인터페이스가 적용되어 있고, 이와 똑같은 그룹 클래스가 적용된 필드가 오로지 age 뿐이었으므로 해당 필드에 대해선 유효성 검증이 진행되었기 때문이다. 반면 전혀 다른 그룹에 해당되는 couponCode에는 유효성 검증이 진행되지 않아 에러 메시지에 뜨지 않았음을 알 수 있다. 또 한 가지 사실은, 그룹화가 전혀 되지 않은 필드들에 대해서는 유효성 검증이 진행되지 않는다는 것이다. 즉, `ValidationGroupOne` 으로 그룹화된 필드에 대해서만 유효성 검사가 진행된다는 것을 알 수 있다. 

정리하자면…

- `@Validated` 어노테이션에 아무런 그룹 인터페이스를 적용하지 않으면 그룹화되지 않은 필드들에 대해서만 유효성 검증이 진행된다.
- `@Validated` 어노테이션에 특정 그룹 인터페이스를 속성으로 적용하면 그 그룹에 속한 필드들에 대해서만 유효성 검증이 진행된다. 다른 그룹 또는 아무런 그룹에도 속하지 않은 필드에 대해서는 유효성 검사가 진행되지 않는다.

이러한 점을 이용하여 하나의 DTO 클래스를 그대로 사용하되, 상황에 따라 유연한 유효성 검증이 필요할 때 이 기능을 이용하면 되겠다. 

# 커스텀 Validation 만들어 유효성 검사하기

한 편, hibernate validation에서 기본으로 제공하는 유효성 검사 어노테이션만으로는 부족할 때 커스텀 유효성 검사 어노테이션을 제작하여 사용할 수도 있다. 이를 위해서는  `jakarta.validation.ConstraintValidator` 인터페이스를 구현한 클래스와 커스텀 어노테이션 인터페이스를 제작하면 된다. 동일한 정규식을 다른 곳에서도 중복하여 사용하거나 정규식의 의미를 명확히 하고자 할 때, 즉 `@Pattern` 대용으로 사용할 수도 있겠다. 여기서는 앞서 보인 전화번호 검증을 위한 `@Pattern` 어노테이션 사용 대신 `@Phone` 이라는 어노테이션을 정의하여 사용해보겠다. 

먼저 `ConstraintValidator` 인터페이스의 구현체를 다음과 같이 정의한다. 

```java
package com.jerocaller.validation;

import com.jerocaller.annotation.validation.Phone;

import jakarta.validation.ConstraintValidator;
import jakarta.validation.ConstraintValidatorContext;

public class PhoneValidator implements ConstraintValidator<Phone, String> {

	@Override
	public boolean isValid(String value, ConstraintValidatorContext context) {
		
		if (value == null) return false;
		
		return value.matches("01\\d-\\d{3,4}-\\d{4}");
		
	}

}

```

코드 3-1. PhoneValidator.java

`ConstraintValidator` 인터페이스에는 두 개의 Type Parameter를 받는데, 첫 번째는 개발자가 정의할 어노테이션 인터페이스명을 기입하며, 두 번째에는 해당 어노테이션을 적용할 필드의 타입을 명시하면 된다. 여기서는 `@Phone` 이라는 어노테이션을 만들 것이며, 해당 어노테이션은 String 타입을 가지는 필드에 적용할 것이므로 `ConstraintValidator<Phone, String>` 과 같이 기입하였다. 

`ConstraintValidator` 인터페이스에는 `isValid` 라는 추상 메서드가 있는데, 여기서 검증 로직을 구현하면 된다. 위 코드에서는 null 값에 대해선 유효하지 않은 데이터로 간주하고, 전화번호 형식을 검증하는 정규식인 `01\\d-\\d{3,4}-\\d{4}` 을 기입하여 해당 정규식에 해당하는 데이터면 true를 반환하여 유효한 데이터로 확인하고 그렇지 않으면 false를 반환하게 하여 유효하지 않은 데이터로 판별하도록 하였다. 

이제 `Phone`  이라는 어노테이션 인터페이스를 다음과 같이 정의하면 된다.

```java
package com.jerocaller.annotation.validation;

import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

import com.jerocaller.validation.PhoneValidator;

import jakarta.validation.Constraint;

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = PhoneValidator.class)
public @interface Phone {
	String message() default "전화번호 형식과 일치하지 않습니다. 01x-xxx(x)-xxxx 형태여야 합니다.";
	Class[] groups() default {};
	Class[] payload() default {};
}

```

코드 3-2. Phone.java

어노테이션을 정의하고자 한다면 `@interface` 형태로 어노테이션을 정의하면 된다. 그리고 위 코드에서 보이는 어노테이션들을 적용하면 된다. 

- `@Target` - 커스텀 어노테이션을 어디에 적용할 수 있는지 그 대상을 명시한다. 여기서는 필드에 적용할 어노테이션이므로 `ElementType.FIELD` 값을 주었다.  이와 같이 `ElementType` 이라는 enum의 값 중 일부를 사용하면 된다. 해당 enum에는 다음의 값들이 존재한다.
    - PACKAGE, TYPE, CONTSTRUCTOR, FIELD, METHOD, ANNOTATION_TYPE, LOCAL_VARIABLE, PARAMETER, TYPE_PARAMETER, TPYE_USE
- `@Retention` - 커스텀 어노테이션의 적용, 유지 범위를 설정한다. `RetentionPolicy` 라는 enum의 값을 사용한다. 다음의 값들이 사용 가능하다.
    - RUNTIME - 컴파일 이후에도 JVM에 의해 계속 참조되도록 한다. 리플렉션, 로깅에 많이 사용된다고 한다.
    - CLASS - 컴파일러가 클래스를 참조할 때까지 유지.
    - SOURCE - 컴파일 전까지만 유지.

사실 어노테이션 인터페이스를 정의할 때에는 위 두 가지 어노테이션이 기본인데, 여기서는 유효성 검사를 위한 커스텀 어노테이션을 제작하는 것이므로 한 가지 어노테이션을 추가해야 한다. 

- `@Constraint` - `validatedBy` 속성에 `ConstraintValidator` 인터페이스의 구현 클래스를 대입하여 해당 구현 클래스와 매핑한다.

즉, 앞서 작성한 PhoneValidator 클래스를 속성값으로 대입하면 된다. 

그리고 어노테이션 인터페이스 내부에는 각각 다음의 요소들을 선언하였다.

- `message()` 유효성 검사에서 통과하지 못한 경우 에러 메시지로 출력할 메시지.
- `groups()` 유효성 검사에서 그룹화를 위한 속성.
- `payload()` 사용자가 추가 정보를 위해 전달하는 값.

사실 위 세 요소들은 앞서 보았던  Hibernate Validator 기본 제공 어노테이션들에도 공통적으로 존재하는 요소들이다. 

여기까지 하면 커스텀 어노테이션 제작을 완료한 것이다. 이제 해당 어노테이션을 전화번호 형식 유효성 검사를 적용할 DTO 내 필드에 적용하면 되겠다. 다음은 그 코드이다.

```java
import com.jerocaller.annotation.validation.Phone;

// 생략...

@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@ToString
public class MemberRequestThree implements RegisterRequest {
	
	/**
	 * 닉네임.
	 * null 및 "", " "은 불가. 
	 * 닉네임 문자열의 길이는 5이상 20이하의 범위만 허용.
	 */
	@NotBlank(message = "닉네임은 공백을 허용하지 않으며, 반드시 입력해야 합니다.")
	@Size(min = 5, max = 20, message = "5글자 이상 20글자 이하만 가능합니다.")
	String nickname;
	
	/**
	 * 이메일 형식 검사.
	 */
	@Email
	String email;
	
	/**
	 * 현재보다 과거의 일시만 생년월일로 입력 가능.
	 */
	@Past(message = "생년월일은 현재보다 과거의 일시만 입력할 수 있습니다.")
	LocalDate birthDate;
	
	/**
	 * 정규식을 이용하여 전화번호 형식 유효성 검증.
	 */
	// 01x-xxx(x)-xxxx
	@Phone
	String phone;
	
	/**
	 * 정수형으로 표현될 수 있는 회원 가입 가능한 나이는 
	 * 20세부터 80세까지로 지정.
	 */
	@Min(value = 20, message = "이 사이트의 회원 가입 가능 연령은 만 20세부터입니다.")
	@Max(value = 80, message = "이 사이트의 회원 가입 제한 연령은 만 80세까지입니다.")
	int age;
	
	/**
	 * 이 사이트에 쳐음 회원가입하는지 여부.
	 * 여기선 True만 허용.
	 */
	@AssertTrue
	boolean areYouNew;
	
	/**
	 * 회원 가입 시 발급받은 쿠폰 일련번호. 
	 * 양수만 유효.
	 */
	@Positive
	int couponCode;
	
}

```

코드 3-3. MemberRequestThree.java

위 코드에서는 `String phone` 필드에 `@Phone` 커스텀 어노테이션을 적용하였다. 다음은 위 DTO 객체를 파라미터로 사용한 컨트롤러 메서드이다. 앞서 만든 컨트롤러 클래스에 추가하면 된다. 

```java
@PostMapping("/validation/custom")
public ResponseEntity<Object> registerCustomValidation(
	@Valid @RequestBody MemberRequestThree request
) {
		
	logRegisterRequest(request);
		
	return ResponseEntity.ok(request);
		
}
```

코드 3-4.

Swagger에서 `/members/validation/custom` 엔드포인트에 대해 다음의 데이터를 넣고 실행해보면 

```java
{
  "nickname": "kimquel1234",
  "email": "sql@mymail.net",
  "birthDate": "1990-03-04",
  "phone": "010-1111-2222",
  "age": 24,
  "areYouNew": true,
  "couponCode": 1073
}
```

유효성 검사를 통과하며, phone 필드에 유효하지 않은 형식의 전화번호 데이터를 다음과 같이 입력하면

```java
{
  "nickname": "kimquel1234",
  "email": "sql@mymail.net",
  "birthDate": "1990-03-04",
  "phone": "010-1a11-22b2",
  "age": 24,
  "areYouNew": true,
  "couponCode": 1073
}
```

다음과 같이 에러 메시지가 등장한다. 

```java
Resolved [org.springframework.web.bind.MethodArgumentNotValidException: Validation failed for argument [0] in public org.springframework.http.ResponseEntity<java.lang.Object> com.jerocaller.controller.MemberController.registerCustomValidation(com.jerocaller.dto.MemberRequestThree): [Field error in object 'memberRequestThree' on field 'phone': rejected value [010-1a11-22b2]; codes [Phone.memberRequestThree.phone,Phone.phone,Phone.java.lang.String,Phone]; arguments [org.springframework.context.support.DefaultMessageSourceResolvable: codes [memberRequestThree.phone,phone]; arguments []; default message [phone]]; default message [전화번호 형식과 일치하지 않습니다. 01x-xxx(x)-xxxx 형태여야 합니다.]] ]
```

---

References

[1] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[2] Spring 공식 문서 - Java Bean Validation

[Java Bean Validation :: Spring Framework](https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html)

[3] Bean Validation 명세. 

[Jakarta Bean Validation - Jakarta Bean Validation 3.0](https://beanvalidation.org/3.0/)

[4] Validation in Spring Boot

[Validation in Spring Boot \| Baeldung](https://www.baeldung.com/spring-boot-bean-validation)

[5]

[JavaBean Validation과 Hibernate Validator 그리고 Spring Boot](https://medium.com/@SlackBeck/javabean-validation%EA%B3%BC-hibernate-validator-%EA%B7%B8%EB%A6%AC%EA%B3%A0-spring-boot-3f31aee610f5)

[6] 

[Hibernate Validator 5.4.3.Final - JSR 349 Reference Implementation: Reference Guide](https://docs.jboss.org/hibernate/validator/5.4/reference/en-US/html_single/#preface)