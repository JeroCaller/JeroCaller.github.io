---
title: "[Forklog] Controller와 Service 단에서의 코드 작성 스타일"
category: "Team Project"
tag: ["Team Project", "Forklog", "Spring", "Spring Boot", "MVC", "REST API", "Controller", "Service"]
---


팀 프로젝트에서 Spring을 이용한 백엔드 개발하면서 고민했던 것 중 하나는 Service 단과 Controller 단에서의 코드 작성 스타일을 어떻게 할 것이냐였다. Service 계층에서는 당연 비즈니스 로직을 작성하는 곳이고, Controller 단에서는 클라이언트의 요청 종류에 따라 이를 처리할 적절한 Service 클래스를 골라 이를 호출하여 얻어낸 데이터를 다시 클라이언트에 응답하는 일종의 지휘관 역할이라는 건 알고 있었지만, 구체적으로 코드를 어떤 스타일로 작성해야할지 고민이 많았었다. 여기서는 고민 끝에 정립한 나만의 스타일에 대해 정리해보고자 한다. 

# 응답 데이터 구조 정형화하기

이전 글인 [[Spring Boot][OpenFeign] 외부 API 응답 데이터 가져와 DB에 저장하기](https://jerocaller.github.io/spring/Spring-OpenFeign-%EC%99%B8%EB%B6%80-API-%EC%9D%91%EB%8B%B5-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%99%80-DB%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/) 에서도 언급한 적이 있지만, 백엔드에서 REST API를 제작할 때 응답 데이터의 구조가 상황에 따라 바뀌면 안 좋을거라 생각하여 처음부터 DTO 형태의 클래스를 만들어 이를 응답 데이터 전송에 사용하였다고 언급한 적이 있다. 여기에 더해 나는 클라이언트 측에서 요청한 데이터를 받지 못한 경우 왜 받지 못했는지를 클라이언트에서도 알 수 있도록 서버 측에서 메시지를 남겨 전송시킬 수 있는 프로퍼티도 마련하여 응답 데이터에 포함시켰다. 이렇게 실질적으로 클라이언트에 전송할 응답 데이터와, 응답 상태를 알려주는 이 두 종류의 정보를 한꺼번에 전송하기 위해 나는 다음과 같은 DTO 구조의 클래스를 하나 정의하였다.

```java
package com.acorn.response;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

/**
 * 만약 REST API 응답에서 에러가 발생했을 시 프론트엔드쪽에서도 에러 발생 원인을 좀 더 쉽게 
 * 파악하기 위해 정형화된 응답 데이터 구조를 정의함. 
 * 
 * @author JeroCaller
 */
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class ResponseJson {
	
	private HttpStatus status;
	private String message;
	private Object data;
	
	/**
	 * 현재 ResponseJson 객체를 ResponseEntity 객체로 변환.
	 * 
	 * @author JeroCaller
	 * @return
	 */
	public ResponseEntity<ResponseJson> toResponseEntity() {
		return ResponseEntity
				.status(this.status)
				.body(this);
	}
	
}

```

코드 1-1. ResponseJson.java

위 클래스에서는 클라이언트에서 직접 응답 받게 될 데이터 구조를 정의하고 있다. 응답 상태를 정의하는 status와, 응답 상태 메세지를 정의하는 message 필드, 그리고 실질적인 데이터를 담을 data 필드로 구성되어 있다. Controller 단에서는 응답 시 ResponseEntity를 이용하는데, 이 ResponseEntity를 Controller에서 반환할 수 있도록 ResponseJson을 ResponseEntity 객체로 바꾸는 `toResponseEntity()`  메서드도 정의하였다. 

위 코드에서 보인 ResponseJson 클래스에서 status, message에 담길 값들은 대부분 Service 계층 클래스에서의 데이터 조회 (또는 삽입, 수정과 같은 조작 작업) 결과에 따라 Controller 단에서 결정되는 구조를 띄도록 하였다. 다음은 Controller 코드의 한 예이다. 

```java
package com.acorn.controller;

import java.util.List;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.acorn.dto.CategoryGroupsFilterDto;
import com.acorn.process.CategoryProcess;
import com.acorn.response.ResponseJson;
import com.acorn.response.ResponseStatusMessages;

import lombok.RequiredArgsConstructor;

@RestController
@RequestMapping("/main/categories/filter")
@RequiredArgsConstructor
public class CategoryController {
	
	private final CategoryProcess categoryProcess;
	
	/**
	 * 모든 음식점 카테고리 (대분류, 중분류 포함) 정보 응답.
	 * 음식점 카테고리 대분류 DTO 내부에 카테고리 소분류 DTO가 포함되어 있는 구조.
	 * 
	 * @author JeroCaller
	 * @return
	 */
	@GetMapping("")
	public ResponseEntity<ResponseJson> getCategoryAll() {
		ResponseJson responseJson = null;
		
		List<CategoryGroupsFilterDto> categoryGroupsFilterDtos 
			= categoryProcess.getAllCategoryGroups();
		if (categoryGroupsFilterDtos.size() == 0) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(ResponseStatusMessages.NO_DATA_FOUND)
					.build();
		} else {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.OK)
					.message(ResponseStatusMessages.READ_SUCCESS)
					.data(categoryGroupsFilterDtos)
					.build();
		}
		
		return responseJson.toResponseEntity();
	}
	
}

```

코드 1-2. CategoryController.java

위 Controller 클래스는 DB 내에 존재하는 모든 음식점 카테고리 정보들을 반환하도록 한 코드가 작성되어있다. 여기서 ResponseJson이 사용되었는데, Service 클래스로부터 조회된 카테고리 데이터가 없을 경우와 있을 경우로 나뉘어서 그 응답 상태 데이터가 달라짐을 볼 수 있다. 만약 조회된 데이터가 없다면 Http Status 코드는 404 Not Found를 전송시키도록 하고, message 필드에는 “조회된 데이터가 없습니다.”와 같이 응답받은 데이터가 없는 이유를 클라이언트에 알려주도록 하였다. 반대로 조회된 데이터가 있다면 그 데이터를 data 필드에 넣었으며, Http Status Code도 200 OK로 설정하고, message에도 “데이터 조회 성공.” 이런 식으로 짧더라도 현재 응답 상태가 어떠한 상태인지를 묘사하는 문구를 넣도록 하였다. 참고로, 일반적인 경우에 message 필드에 넣을 메시지에서 공통적인 부분이 있었기에 이 부분도 아예 ResponseStatusMessage라는 인터페이스 내 문자열 타입 상수로 정의하였다. 

```java
package com.acorn.response;

/**
 * 응답 데이터로 HTTP 응답 상태를 상세히 설명한 메시지를 포함시키기 위한 용도.
 * 프론트엔드 단에서 응답 성공 및 실패 이유를 추측할 수 있게 하기 위함. 
 * 
 * 여기서는 일반적인 상황에서의 메시지를 담고 있으므로 특수한 상황에서의 메시지 필요 시 
 * 별도의 인터페이스 (또는 커스텀 예외 클래스 등)를 정의하여 메시지 정의하면 됨. 
 * 
 * 구현 강제 용도 X
 * 
 * @author JeroCaller 
 */
public interface ResponseStatusMessages {
	
	String NO_DATA_FOUND = "조회된 데이터가 없습니다.";
	String READ_SUCCESS = "데이터 조회 성공.";
	
	String CREATE_SUCCESS = "데이터 삽입 성공.";
	String CREATE_FAILED = "데이터 삽입 실패.";
	
	String UPDATE_SUCCESS = "데이터 변경 성공.";
	String UPDATE_FAILED = "데이터 변경 실패.";
	
	String DELETE_SUCCESS = "데이터 삭제 성공.";
	String DELETE_FAILED = "데이터 삭제 실패.";
	
	String NO_REQUEST_ARGS = "입력된 요청 인자 없음.";
	
	String AUTHORIZED_MEMBER = "인증된 사용자입니다.";
	String UNAUTHORIZED_MEMBER 
		= "비로그인 또는 인증되지 않은 사용자입니다. 미인증 사용자에게는 제공되지 않는 기능입니다.";
	
}

```

코드 1-3. ResponseStatusMessages.java

물론 실제로 팀 프로젝트를 진행하다보니 위와 같은 메시지만으로는 특정 케이스에 대한 설명을 하기에 부족하였다. 그럴 때는 아예 커스텀 예외 클래스를 정의하고, 서비스 단에서 데이터 조회가 안되거나 하는 비정상적인 상황들에 대해 커스텀 예외 클래스를 throw하도록 하였다. 이 때 이 커스텀 에외 클래스에 고정된 에러 메시지를 작성하여 이를 ResponseJson의 message 필드값으로 활용하도록 고안하였다. 이에 대한 자세한 내용은 후술. 

아무튼 위와 같이 ResponseJson이라는 클라이언트에 전송될 데이터 구조를 구성하는 클래스를 정의함으로써 응답 상태가 어떠한 상황이 되던간에 응답 데이터 구조는 불변하도록 고정시켰으며, 단순히 데이터만 전송하는 것이 아니라 현재 응답 상태가 어떠한지에 대한 정보까지 전달하여 클라이언트 측에서 이를 알 수 있게끔 구성하였다. 

# 커스텀 예외 활용

서비스 단에서 특정 조건의 데이터를 조회할 때 여러 상황들에 의해 조회되지 않을 수도 있었다. 따라서 그 경우 정확히 어떤 이유로 데이터가 조회되지 않는 것인지를 클라이언트에서도 파악할 수 있게 하고자 하였다. 이를 고민한 끝에, 커스텀 예외 클래스를 정의하여 이를 서비스 단에서 throw하도록 하고, 이를 받은 컨트롤러에서 그 커스텀 예외에 맞게끔 응답 데이터를 꾸미도록 고안하였다. 다음은 이에 대한 대표적인 사례이다.

```java
/**
	 * 지역 및 음식 카테고리 대분류 두 검색 조건이 적용되어 검색된 음식점 정보들을 반환.
	 * 
	 * 예를 들어 지역: 서울, 카테고리: 한식 입력 시 서울 지역 내 한식 카테고리에 해당하는 
	 * 모든 음식점 정보들을 페이징하여 반환.
	 * 
	 * @author JeroCaller
	 * @param location - 문자열 형태의 지역 주소명
	 * @param largeId - 카테고리 대분류 엔티티의 ID
	 * @param page
	 * @param size
	 * @return
	 */
	@GetMapping("/{location}/categories/large/{lid}")
	public ResponseEntity<ResponseJson> getEateriesByLocationAndCategoryLarge(
			@PathVariable(name = "location") String location,
			@PathVariable(name = "lid") int largeId,
			@RequestParam(name = "page", defaultValue = "1") int page,
			@RequestParam(name = "size", defaultValue = "10") int size
	) {
		ResponseJson responseJson = null;
		
		Pageable pageRequest = PageUtil.getPageRequestOf(page, size);
		Page<EateriesDto> eateries = null;
		try {
			eateries = eateriesMainProcess
					.getEateriesByLocationAndCategoryLarge(
							location, 
							largeId, 
							pageRequest
					);
		} catch (NoCategoryFoundException e) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(e.getMessage())
					.build();
			return responseJson.toResponseEntity();
		}
		
		// 클라이언트가 전달한 지역 주소에 해당하는 음식점 데이터가 없는 경우.
		if (PageUtil.isEmtpy(eateries)) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(ResponseStatusMessages.NO_DATA_FOUND)
					.build();
		} else {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.OK)
					.message(ResponseStatusMessages.READ_SUCCESS)
					.data(eateries)
					.build();
		}
		
		return responseJson.toResponseEntity();
	}
```

코드 2-1. EateriesMainController 클래스의 getEateriesByLocationAndCategoryLarge 메서드.

```java
/**
	 * 지역 및 카테고리 대분류 ID(PK) 검색 조건에 해당하는 음식점 정보 조회 및 반환
	 * 
	 * @author JeroCaller 
	 * @param location - 문자열 형태의 지역 주소명
	 * @param largeId - 카테고리 대분류 엔티티의 ID
	 * @param pageRequset
	 * @return
	 * @throws NoCategoryFoundException 입력된 카테고리 대분류 ID(largeId)가 DB 
	 * 내에 조회되지 않은 경우 발생.
	 */
	// 각자 다른 repository로부터 조회하는 코드가 있으므로 이를 트랜잭션으로 묶는 게 
	// 좋을 것 같다는 판단 하에 @Transactional 어노테이션 부여함.
	@Transactional(readOnly = true)
	public Page<EateriesDto> getEateriesByLocationAndCategoryLarge(
			String location,
			int largeId,
			Pageable pageRequset
	) throws NoCategoryFoundException {
		Optional<CategoryGroups> categoryGroupOpt = categoryGroupsRepository
				.findById(largeId);

		CategoryGroups categoryGroups = categoryGroupOpt.orElseThrow(
				() -> new NoCategoryFoundException(
						"카테고리 대분류 ID: " + String.valueOf(largeId)
					)
		);
		
		Page<Eateries> eateries = eateriesRepository.findByCategoryGroup(
				location, 
				categoryGroups.getNo(), 
				pageRequset
		);
		
		return eateries.map(EateriesDto :: toDto);
	}
```

코드 2-2. 앞선 코드 2-1에서 호출하여 사용된 EateriesMainProcess라는 서비스 클래스의 getEateriesByLocationAndCategoryLarge 메서드

서비스 계층에 해당하는 코드 2-2에서는 메서드 내부에서 NoCategoryFoundException이라는 하나의 커스텀 예외 클래스를 이용하였다. 

```java
package com.acorn.exception.category;

/**
 * 조회된 카테고리가 없을 때 발생시킬 커스텀 예외 클래스. 
 * 
 * RuntimeException을 상속받도록 하면 메서드 선언부에 throws를 추가하라는 
 * 이클립스 자동 기능이 제공되지 않음. 이를 위해 Exception을 대신 상속받도록 함.
 * 
 * @author JeroCaller
 */
public class NoCategoryFoundException extends Exception {
	
	public NoCategoryFoundException(String input) {
		super("해당 카테고리 조회 결과가 없습니다. 입력한 카테고리 관련 정보: " + input);
	}
	
}

```

코드 2-3. 코드 2-2에서 쓰이는 커스텀 예외 클래스 코드

![사진 1-1. 위 코드 2-1에서 작성한 REST API 호출 시 클라이언트 측에서 받을 수 있는 여러 가지 응답 상태 정보 명세. Status Code 부터 “응답 메시지”가 실제 클라이언트가 받을 수 있는 응답 상태 정보들이다. ](/images/2025-01-18/team-project-forklog-Controller%EC%99%80%20Service%20%EB%8B%A8%EC%97%90%EC%84%9C%EC%9D%98%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1%20%EC%8A%A4%ED%83%80%EC%9D%BC/1.png)

사진 1-1. 위 코드 2-1에서 작성한 REST API 호출 시 클라이언트 측에서 받을 수 있는 여러 가지 응답 상태 정보 명세. Status Code 부터 “응답 메시지”가 실제 클라이언트가 받을 수 있는 응답 상태 정보들이다. 

![사진 1-2. 앞선 코드 2-1에서 생성한 REST API 호출 시 정상적으로 음식점 데이터가 조회될 경우의 응답 데이터 구조](/images/2025-01-18/team-project-forklog-Controller%EC%99%80%20Service%20%EB%8B%A8%EC%97%90%EC%84%9C%EC%9D%98%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1%20%EC%8A%A4%ED%83%80%EC%9D%BC/2.png)

사진 1-2. 앞선 코드 2-1에서 생성한 REST API 호출 시 정상적으로 음식점 데이터가 조회될 경우의 응답 데이터 구조

![사진 1-3. 음식점 카테고리 정보를 DB에 존재하지 않는 정보로 전달했을 때 응답 받은 데이터. 사진 1-2과 비교했을 떄, 데이터 조회에 성공하건 실패하건 간에 status, message, data 이 세 가지 프로퍼티가 그대로 존재하는 것은 변하지 않는다. 이렇게 프로퍼티를 고정시키면 클라이언트 측에서 특정 상황에만 존재하는 프로퍼티를 처리하는 별도의 코드를 작성하지 않아도 되고, 혼란을 줄일 수 있다고 판단하였다. ](/images/2025-01-18/team-project-forklog-Controller%EC%99%80%20Service%20%EB%8B%A8%EC%97%90%EC%84%9C%EC%9D%98%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1%20%EC%8A%A4%ED%83%80%EC%9D%BC/3.png)

사진 1-3. 음식점 카테고리 정보를 DB에 존재하지 않는 정보로 전달했을 때 응답 받은 데이터. 사진 1-2과 비교했을 때, 데이터 조회에 성공하건 실패하건 간에 status, message, data 이 세 가지 프로퍼티가 그대로 존재하는 것은 변하지 않는다. 이렇게 프로퍼티를 고정시키면 클라이언트 측에서 특정 상황에만 존재하는 프로퍼티를 처리하는 별도의 코드를 작성하지 않아도 되고, 혼란을 줄일 수 있다고 판단하였다. 

코드 2-1, 2-2는 모두 클라이언트 측에서 “지역 주소” 문자열과 “음식점 카테고리” 이 두 가지 정보를 전달하였을 때 이 두 가지 조건에 맞는 음식점 정보들을 DB에서 조회하여 반환하기 위한 목적의 코드들이다. 그러다 보니 자연스레 서비스 단에서는 클라이언트가 전달한 “음식점 카테고리” 정보가 DB 내에 존재하는지, 그리고 “지역 주소”도 DB에 존재하는지 확인하는 것이 필요하다. 여기서는 “지역 주소”는 음식점 데이터 조회 시 조회된 데이터가 없다면 자연스레 주어진 “지역 주소”가 DB에 없다는 뜻이 되므로 별도의 검증 로직을 거치지는 않았다. 다만 음식점 카테고리 정보는 미리 DB에 존재하는지 확인하는 것이 좋다고 판단하였다. 그래서 코드 2-2의 서비스 계층에서의 메서드 내부 코드를 보면 맨 먼저 음식점 카테고리 PK 번호가 DB 내에 존재하는지부터 확인하고 있다. 그리고 여기서 주어진 음식점 카테고리 정보가 DB에 없음이 판별되면 바로 앞서 코드 2-3에서 정의한 커스텀 예외 클래스를 throw하도록 고안하였다. 그러면 Controller 단에서는 음식점 데이터가 조회되지 않은 이유를 “주어진 음식점 카테고리 정보가 DB에 없다”라고 명확하게 하여 클라이언트에 전송할 수 있게 된다. 이러한 이점을 노리고 커스텀 예외 클래스를 정의하여 서비스 계층에서 적극 활용한 것이다. 참고로 커스텀 예외 클래스 코드인 코드 2-3에서도 왜 이 예외가 발생하는지 그 이유를 메시지로 작성함으로써, Controller 단에서는 응답 데이터로 전송할 ResponseJson 클래스의 message 필드값에 해당 메시지를 자동으로 삽입하도록 하였다. 이렇게 하여 Controller 계층에서 일일이 응답 상태 값을 작성하지 않고 그저 상황에 맞게 응답 데이터만 구성하여 클라이언트에 전송하도록 하였다. 이것이 Controller 계층의 특징이며, “지휘관은 직원의 일까지 직접하지 않고 그저 지휘만 할 뿐이다”라는 그 특징을 지키는 방법 중의 하나라 생각했기 때문이다. 

어쨌거나 커스텀 예외 클래스를 정의하고 이를 서비스 단에 활용하면, 데이터 조회가 안되는 상세한 이유도 클라이언트에 전송하여 클라이언트 측에서 그 원인을 바로 파악할 수 있도록 구성하였다. 

# Controller와 Service에서 응답 데이터를 전송하기 위한 코드 스타일

나는 위에서 언급한 방식을 그대로 활용하여 내가 작업한 모든 백엔드 단에서 똑같은 스타일로 코드를 작성하여 작업해 나갔다. 공통적인 특징은 다음과 같다.

- 위에서 언급한 ResponseJson이라는 클라이언트가 직접 받을 응답 데이터 구조를 정의하는 클래스를 사용하여 응답 데이터 구조를 고정시켰다. 그리고 클라이언트에서도 응답 데이터의 상태와 그 원인을 파악할 수 있도록 status, message라는 필드도 추가하여 구성하였다.
- 서비스 클래스에서, 클라이언트가 전달한 여러 검색 조건들 중 일부는 DB 내 데이터와 일치하는 부분이 없어 데이터가 조회되지 않는 경우도 있다. 이 경우 어떤 검색 조건이 DB와 일치하지 않는지를 커스텀 예외 클래스를 이용하여 Controller에 전송하고, Controller는 해당 예외 상황에 맞게끔 응답 데이터를 구성하여 클라이언트에 전송하도록 하였다. 이렇게 하면 클라이언트가 서버에 전달한 여러 가지 검색 조건들 중 어떤 검색 조건이 유효하지 않았는지를 클라이언트측에서 바로 파악할 수 있기 때문이다.
- 클라이언트가 직접 받게될 응답 데이터를 구성하는 ResponseJson은 Service 계층의 메서드에서 바로 반환하도록 하지 않고 Controller에서 반환하도록 하였다. Service 계층에서는 데이터 조회(또는 그 외 삽입, 삭제 등의 작업) 결과로 나온 데이터만을 반환하도록 하였다. 상황에 따라 어떤 응답 데이터를 클라이언트에 보낼 지 판단하는 것은 Service가 아닌 Controller의 역할이라고 생각하였고, Service는 단순히 비즈니스 로직을 수행하여 특정 데이터들을 Controller에 전달하는 역할이라 생각했기 때문에 이렇게 구성한 것이다.
- 클라이언트가 전송한 검색 조건들이 보통 2, 3개 이상이면 각각의 검색 조건들이 DB 내 상황과 맞지 않아 조회되지 않을 경우 throw 하기 위한 각각의 커스텀 예외 클래스들을 정의하여 이를 사용하였고, Controller 단에서는 이를 다중 try ~ catch를 이용하여 각 예외 상황에 따른 응답 상태 메시지를 달리 하여 클라이언트에 전송하도록 구성하였다. 반면, 검색 조건이 1개 밖에 없을 경우엔 코드의 간결성을 위해 별도의 커스텀 예외 클래스를 작성하지 않고, Controller 단에서는 Service로부터 조회된 데이터가 있는지 없는지 여부만을 따져 이를 if문으로 처리하도록 하였다.

마지막 사항에 대해서는 다음의 코드를 통해 살펴볼 수 있다. 다음은 총 3개의 검색 조건들이 고려되었기에 Service 단에서 3개의 커스텀 예외 클래스가 사용되고, Controller 단에서 다중 try ~ catch로 처리하고 있는 코드이다.

```java
/**
	 * 사용자 맞춤 추천 음식점 정보 반환
	 * 
	 * 추천 알고리즘
	 * - 사용자 (로그인한 상황 가정) 즐겨찾기한 음식점 목록 추린 후, 
	 * 음식점 목록 내 같은 카테고리의 다른 음식점 조회하여 반환.
	 * 
	 * @author JeroCaller
	 * @return
	 */
	@GetMapping("/eateries/recommends")
	public ResponseEntity<ResponseJson> getEateriesForMember(
			@RequestParam(name = "page", defaultValue = "1") int page, 
			@RequestParam(name = "size", defaultValue = "10") int size
	) {
		
		ResponseJson responseJson = null;
		ResponseJson memberJson = getMemberInfo();
			
		// 생략...
			
		Pageable pageRequest = PageUtil.getPageRequestOf(page, size);
		MembersResponseDto memberInfo = (MembersResponseDto) memberJson.getData();
			
		Page<EateriesDto> eateries = null;
		boolean isException = true;
		try {
			eateries = memberEateriesProcess.getEateriesByRecommend(
						memberInfo.getNo() , 
						pageRequest
			);
			isException = false;
		} catch (NoMemberFoundException e) {
			// 미인증된 사용자로 판별
			responseJson = ResponseJson.builder()
					.status(HttpStatus.UNAUTHORIZED)
					.message(e.getMessage())
					.build();
		} catch ( NoCategoryFoundException | NoEateriesFoundException e) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(e.getMessage())
					.build();
		}
			
		if (!isException) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.OK)
					.message(ResponseStatusMessages.READ_SUCCESS)
					.data(eateries)
					.build();
		}
			
		return responseJson.toResponseEntity();
		
	}
```

코드 3-1. MembersEateriesController 클래스의 getEateriesForMember 메서드. 

```java
/**
	 * 현재 사용자가 즐겨찾기한 음식점들의 카테고리와 동일한 카테고리를 가지는 모든 
	 * 음식점들을 가져와 반환한다.
	 * 
	 * @author JeroCaller
	 * @param memberNo
	 * @return
	 * @throws NoMemberFoundException 
	 * @throws NoCategoryFoundException 
	 * @throws NoEateriesFoundException 
	 */
	// 여러 엔티티를 조회하므로 이 작업들을 하나의 트랙잭션으로 묶음
	// 또한 readOnly = true로 할 시 조회 과정만 있음을 명시할 수 있을 뿐만 아니라 
	// 조회 과정의 속도가 더 빨라진다고 한다.
	@Transactional(readOnly = true)  
	public Page<EateriesDto> getEateriesByRecommend(
			int memberNo, 
			Pageable pageRequest
	) throws NoMemberFoundException, 
		NoCategoryFoundException, 
		NoEateriesFoundException 
	{
		
		// 멤버 ID에 해당하는 멤버 엔티티 조회.
		Optional<Members> membersOpt = membersRepository.findById(memberNo);
		Members member = membersOpt.orElseThrow(
				() -> new NoMemberFoundException(String.valueOf(memberNo))
		);
		
		/*
		log.info("현재 로그인 사용자 정보");
		log.info("이메일 : " + member.getEmail());
		log.info("ID 번호: " + member.getNo());
		*/

		List<Categories> memberCategories = categoriesRepository
				.findByMemberFavorite(member);
		if (ListUtil.isEmpty(memberCategories)) {
			throw new NoCategoryFoundException("사용자가 즐겨찾기한 음식점이 없는 듯 합니다.");
		}
		
		/*
		log.info("사용자가 즐겨찾기한 음식점들의 카테고리 정보");
		memberCategories.forEach(cate -> {
			log.info("카테고리 대분류 번호: " + cate.getGroup().getNo());
			log.info("카테고리 소분류 번호: " + cate.getNo());
			log.info("카테고리 명: " + cate.getGroup().getName() + " " + cate.getName());
			log.info("-----------------");
		}); */
		
		Page<Eateries> eateries = eateriesRepository
				.findByCategoryIn(memberCategories, pageRequest);
		
		if (PageUtil.isEmtpy(eateries)) {
			throw new NoEateriesFoundException();
		}
		
		return eateries.map(EateriesDto :: toDto);
	}
```

코드 3-2. 서비스 계층인 MembersEateriesProcess 클래스의 getEateriesByRecommend 메서드. 

위 코드 3-2에서는 특정 조건을 만족하는 음식점 정보를 조회, 반환하기 위해서는 각각 현재 회원, 음식점 카테고리 정보들이 DB에 존재하는지 여부를 먼저 확인해야 하고, 여기에 음식점 정보 자체도 DB에 존재하는지 여부를 판단하기 위해 총 3개의 커스텀 예외가 사용되었다. 이 서비스 메서드를 호출한 Controller 계층(코드 3-1)에서는 다중 try ~ catch문을 이용하여 각각의 경우에 HTTP Status Code 및 응답 상태 메시지를 다르게 하여 클라이언트에 전송하도록 구성하고 있다. 

반면 다음의 코드에서는 “음식점 카테고리”라는 데이터의 존재 여부만을 따지므로 별도의 커스텀 예외를 사용하지 않고, try ~ catch 대신 if문을 사용하여 데이터 존재, 미존재 이 두 가지 상황에 대한 응답 데이터를 다르게 하고 있다. 

```java
package com.acorn.controller;

import java.util.List;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.acorn.dto.CategoryGroupsFilterDto;
import com.acorn.process.CategoryProcess;
import com.acorn.response.ResponseJson;
import com.acorn.response.ResponseStatusMessages;

import lombok.RequiredArgsConstructor;

@RestController
@RequestMapping("/main/categories/filter")
@RequiredArgsConstructor
public class CategoryController {
	
	private final CategoryProcess categoryProcess;
	
	/**
	 * 모든 음식점 카테고리 (대분류, 중분류 포함) 정보 응답.
	 * 음식점 카테고리 대분류 DTO 내부에 카테고리 소분류 DTO가 포함되어 있는 구조.
	 * 
	 * @author JeroCaller
	 * @return
	 */
	@GetMapping("")
	public ResponseEntity<ResponseJson> getCategoryAll() {
		ResponseJson responseJson = null;
		
		List<CategoryGroupsFilterDto> categoryGroupsFilterDtos 
			= categoryProcess.getAllCategoryGroups();
		if (categoryGroupsFilterDtos.size() == 0) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(ResponseStatusMessages.NO_DATA_FOUND)
					.build();
		} else {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.OK)
					.message(ResponseStatusMessages.READ_SUCCESS)
					.data(categoryGroupsFilterDtos)
					.build();
		}
		
		return responseJson.toResponseEntity();
	}
	
}

```

코드 3-3. CategoryController.java

```java
package com.acorn.process;

import java.util.List;
import java.util.stream.Collectors;

import org.springframework.stereotype.Service;

import com.acorn.dto.CategoryGroupsFilterDto;
import com.acorn.entity.CategoryGroups;
import com.acorn.repository.CategoryGroupsRepository;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class CategoryProcess {

	private final CategoryGroupsRepository categoryGroupsRepository;
	
	/**
	 * 모든 카테고리 대분류를 반환. 카테고리 대분류 내부에 소분류 포함되어 반환.
	 * 
	 * @author JeroCaller 
	 * @return
	 */
	public List<CategoryGroupsFilterDto> getAllCategoryGroups() {
		List<CategoryGroups> result = categoryGroupsRepository
				.findAll();
		
		return result.stream()
				.map(CategoryGroupsFilterDto :: toDto)
				.collect(Collectors.toList());
	}
	
}

```

코드 3-4. CategoryProcess.java

물론 음식점 카테고리가 조회되지 않을 경우를 위한 커스텀 예외 클래스를 만들어 이를 서비스 단에서 throw 하도록 하고, Controller 단에서 이를 try ~ catch로 처리할 수도 있겠지만, “음식점 카테고리”라는 단 하나의 검색 조건만을 상정하고 있기에 코드의 간결성을 위해 if ~ else 문으로 대신 처리하였다. 

이렇듯, 검색 조건의 개수에 따라 try ~ catch를 쓰기도, if ~ else 문을 사용하기도 하였다. 어쨌거나 이렇게 해서 코드의 일관성 및 간결성을 유지하려고 하였다. 

사실 위에서 언급한 코드 스타일 내용들은 모두 팀 프로젝트를 하면서 계속 고민하고 이것저것 시도하다가 얻게 된 스타일이다. 한 번 이렇게 스타일이 정립되니 새 기능 추가를 위해 다른 Controller, Service 코드를 새로 작성해야할 때에도 일관적인 코드를 작성할 수 있어 큰 장점이라고 생각되었다. 만약 코드 스타일이 정립되지 않았다면 어떨 땐 A라는 스타일로 작성했다가 또 어떨 땐 B라는 전혀 다른 스타일로 작성하는 등 코드의 일관성을 확보할 수 없었을 것이다. 또한 한 번 이렇게 코드 스타일이 정립되니, 오로지 비즈니스 로직에만 고민할 수 있게 되어 개발 속도도 더 빨라지는 체감을 하였다. 

이런 점에서는 장점이었으나, 몇 가지 의문 또는 아쉬운 점들도 있었다. 

- Service에서 각각의 경우에 대한 커스텀 예외 클래스를 throw 하도록 하면, 오히려 Service 코드의 재활용성이 떨어지진 않을까 걱정된다. 위 코드에서도 보면 알겠지만, Service에서 throw하는 커스텀 예외 클래스의 종류가 많아질수록 이를 처리하기 위한 try ~ catch 코드도 길어질 수밖에 없다. 그러면 똑같은 Service 코드를 다른 곳에서 재활용하기엔 부담스럽지 않을까, 란 생각이 든다.
- 어쩐지 위에서 작성한 코드 스타일도 코드가 장황하다고 느껴졌다. 조금 더 코드를 간결하게 작성하는 방법은 없었을까, 란 생각이 든다.

팀 프로젝트에서는 위에서 언급한 나의 코드 작성 스타일이 내가 고민하고 시도해낸 것들 중 최상의 것이었다. 그럼에도 위와 같은 아쉬운 점들이 있어서 사실 이것보다 더 좋은 방법이 있을 거라는 생각도 든다. 보통 장황한 코드를 간결하게 줄이는 방법 중 하나로 어노테이션을 사용하는 방법이 있는데, 스프링에서도 다양한 어노테이션을 제공하니 분명 위에서 소개한 방식보다 코드를 더 간결하게 해줄 어노테이션들이 있을거라 생각한다. 그래서 나중에 이에 대해서도 살펴봐야겠다. 그래서 위에서 언급한 아쉬운 점들을 해결할 수 있는 최상의 코드 작성 스타일이 있는지 좀 연구해봐야겠다. 

# 요청과 응답은 Map 대신 DTO 클래스로

클라이언트로의 응답 데이터 구성을 위해 ResponseJson이라는 DTO 형태의 클래스를 사용한 것처럼, 나는 요청 파라미터 또는 응답 데이터를 구성할 필드의 개수가 많을 경우 항상 Map 자료구조 대신 DTO 형태의 클래스를 정의하여 사용하였다. 이전에 [[Spring Boot][OpenFeign] 외부 API 응답 데이터 가져와 DB에 저장하기](https://jerocaller.github.io/spring/Spring-OpenFeign-%EC%99%B8%EB%B6%80-API-%EC%9D%91%EB%8B%B5-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EA%B0%80%EC%A0%B8%EC%99%80-DB%EC%97%90-%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/) 글에서 선보인 OpenFeign을 이용하여 외부 API 호출할 때에도 요청과 응답 모드 DTO 클래스를 사용하였다. 

```java
@GetMapping(value = "/local/search/keyword.json")
KeywordResponseDto getEateriesByKeyword(@SpringQueryMap KeywordRequestDto requestDto);
```

코드 4-1. 요청 파라미터와 응답 데이터 모두 각각 DTO 클래스를 정의하여 사용한 예시.

Map 자료구조 대신 DTO 클래스를 정의하여 사용한 이유는 다음과 같다. 

- 보통 요청 파라미터나 응답 데이터의 개수는 정해진 경우가 많다. 이러한 고정된 개수를 유지하기 위해 DTO 클래스를 사용한다. Map 자료구조를 사용할 경우, 언제 어디서든지 개발자가 고의이든 실수이든 필드를 여러 개 더 추가하거나 삭제할 수 있기에 이 경우 요청 파라미터나 응답 데이터의 필드 개수가 동적으로 변할 수 있어 이를 방지하기 위함이었다. 자바라는 언어가 정적 타입 언어로, 데이터 타입을 고정시켜 사용하듯이, 그러한 자바의 철학에 맞게끔 DTO 클래스를 만들어 고정된 개수의 필드만을 정의하여 사용한 것도 있다.

그래서 이 부분에 대해서는 특별하게 상황에 따라 필드의 개수가 변동적으로 변해서 Map 자료구조를 사용할 수밖에 없을 때를 제외하고는 되도록 DTO 클래스를 정의, 사용할 생각이다. 

