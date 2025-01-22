---
title: "[Forklog][Trouble Shooting] 지역 & 카테고리 소분류 조회 안됨 이슈"
category: "Team Project"
tag: ["Team Project", "Forklog", "Trouble Shooting", "Page"]
---

실제 문서 작성일: 2024년 12월 30일


# 문제 개요

```java
Page<Eateries> findByAddressContainingAndCategory(
			String address, 
			Categories categories,
			Pageable pageRequest
	);
```

EateriesRepository

```java
@GetMapping("/{location}/categories/small/{sid}")
	public ResponseEntity<ResponseJson> getEateriesByLocationAndCategorySmall(
			@PathVariable(name = "location") String location,
			@PathVariable(name = "sid") int smallId,
			@RequestParam(name = "page", defaultValue = "1") int page,
			@RequestParam(name = "size", defaultValue = "10") int size
	) {
		ResponseJson responseJson = null;
		
		Pageable pageRequest = PageRequest.of(page, size);
		Page<EateriesDto> eateries = null;
		try {
			eateries = eateriesMainProcess
					.getEateriesByLocationAndCategorySmall(
							location, 
							smallId, 
							pageRequest
					);
		} catch (NoCategoryFoundException e) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(e.getMessage())
					.build();
			return responseJson.toResponseEntity();
		}
		
		if (eateries.getNumberOfElements() == 0) {
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

EateriesMainController

```java
public Page<EateriesDto> getEateriesByLocationAndCategorySmall(
			String location,
			int smallId,
			Pageable pageRequest
	) throws NoCategoryFoundException {
		Optional<Categories> categoryOpt = categoriesRepository.findById(smallId);
		categoryOpt.orElseThrow(() -> new NoCategoryFoundException(String.valueOf(smallId)));
		Categories category = categoryOpt.get();
		log.info("조회된 카테고리: " + category.toString() + " " + category.getGroup().getName());
		Page<Eateries> eateries = eateriesRepository
				.findByAddressContainingAndCategory(location, category, pageRequest);
		return eateries.map(EateriesDto :: toDto);
	}
```

EateriesMainProcess

![1.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-%EC%A7%80%EC%97%AD%20%26%20%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%20%EC%86%8C%EB%B6%84%EB%A5%98%20%EC%A1%B0%ED%9A%8C%20%EC%95%88%EB%90%A8%20%EC%9D%B4%EC%8A%88/1.png)

![2.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-%EC%A7%80%EC%97%AD%20%26%20%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%20%EC%86%8C%EB%B6%84%EB%A5%98%20%EC%A1%B0%ED%9A%8C%20%EC%95%88%EB%90%A8%20%EC%9D%B4%EC%8A%88/2.png)

분명 DB - eateries 테이블의 category_no를 보면서 요청값을 입력했음에도 조회되어야 할 데이터가 조회되지 않는 현상 발생.

```java
조회된 카테고리: Categories(no=27, name=냉면, group=com.acorn.entity.CategoryGroups@32b6bc9) 한식
```

EateriesMainProcess 에서의 Categories 엔티티 조회는 정상적으로 잘됨. 이를 미루어 볼 때 JpaRepository 문제인것 같기도 함.

“서울 서초”, “11” 입력 시 결과가 나오지만, “서울 강남”, “11”이라하면 조회되는 결과가 없음. 분명 둘 다 DB 상에 존재함. 

# 해결 시도

## 시도 1 (실패)

객체 동등성 문제인가 싶어서 Categories에 `@EqualsAndHashCode(of = {"no"})` 을 넣었음에도 작동하지 않음.

## 시도 2 (실패)

![1.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-%EC%A7%80%EC%97%AD%20%26%20%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%20%EC%86%8C%EB%B6%84%EB%A5%98%20%EC%A1%B0%ED%9A%8C%20%EC%95%88%EB%90%A8%20%EC%9D%B4%EC%8A%88/3.png)

위 쿼리문은 JPA 쿼리 메서드 실행 시 콘솔창에 출력되는 SQL문을 그대로 옮겨 실행한 모습이다. 강남구의 11번 카테고리 소분류에 대해 8건이 검색된다. 

그런데 이를 JPQL의 native Query로 그대로 옮겼음에도 swagger에서는 조회되지 않는다.

```java
@Query(value = """
		SELECT 
			e1_0.no,
			e1_0.address,
			e1_0.category_no,
			e1_0.description,
			e1_0.latitude,
			e1_0.longitude,
			e1_0.name,
			e1_0.rating,
			e1_0.tel,
			e1_0.thumbnail,
			e1_0.view_count 
		FROM eateries e1_0 
		WHERE e1_0.address LIKE CONCAT('%', ?1, '%') and e1_0.category_no = ?2;
	""", nativeQuery = true)
	Page<Eateries> findByAddressContainingAndCategory(
			String address, 
			int categoryNo,
			Pageable pageRequest
	);
```

![2.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-%EC%A7%80%EC%97%AD%20%26%20%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%20%EC%86%8C%EB%B6%84%EB%A5%98%20%EC%A1%B0%ED%9A%8C%20%EC%95%88%EB%90%A8%20%EC%9D%B4%EC%8A%88/4.png)

더더욱 어이가 없는 것은, 정작 응답 결과에서 totalElements의 수가 8개로, heidiSql로 검색했던 결과 개수와 일치한다는 것이다. 

findByAddressContainingOrCategory 로 이름을 바꾸고 그대로 “서울 강남구”, “11”로 했을 때는 결과가 잘 나왔다. 다만 이 경우 “11”이 아닌 다른 종류의 음식점도 나와서 우리가 원하는 결과는 아니다. 어쨌거나 결과가 나오는데, And로 바꾸면 “서울 강남구”에 대해서만 나오지 않는다. 더 어이 없는 것은 “서울 서초구”와 같은 다른 몇몇 지역 데이터는 잘 나온다는 것이다. 

## 시도 3 (성공)

`PageRequest.of(page, size)` 메서드에서 페이지 번호를 받는 곳이 zero-base이기에 발생한 문제였음. `PageRequest.of(1, size)` 라고 입력하면 사실상 2페이지로 인식됨. 따라서 이 경우 1페이지를 요청하고자 한다면 `PageRequest.of(0, size)` 이라고 해야 함.

```java
@GetMapping("/{location}/categories/small/{sid}")
	public ResponseEntity<ResponseJson> getEateriesByLocationAndCategorySmall(
			@PathVariable(name = "location") String location,
			@PathVariable(name = "sid") int smallId,
			@RequestParam(name = "page", defaultValue = "1") int page,
			@RequestParam(name = "size", defaultValue = "10") int size
	) {
		ResponseJson responseJson = null;
		
		Pageable pageRequest = PageRequest.of(page - 1, size);  // 수정
		Page<EateriesDto> eateries = null;
		try {
			eateries = eateriesMainProcess
					.getEateriesByLocationAndCategorySmall(
							location, 
							smallId, 
							pageRequest
					);
		} catch (NoCategoryFoundException e) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(e.getMessage())
					.build();
			return responseJson.toResponseEntity();
		} 
		
		log.info("eateries: " + eateries.toString());
		log.info("" + eateries.getNumberOfElements());
		log.info("" + eateries.getTotalElements());
		if (eateries.getNumberOfElements() == 0) {
		//if (eateries.getTotalElements() == 0) {
			responseJson = ResponseJson.builder()
					.status(HttpStatus.NOT_FOUND)
					.message(ResponseStatusMessages.NO_DATA_FOUND)
					.data(eateries)
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

EateriesMainController.java

![1.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-%EC%A7%80%EC%97%AD%20%26%20%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%20%EC%86%8C%EB%B6%84%EB%A5%98%20%EC%A1%B0%ED%9A%8C%20%EC%95%88%EB%90%A8%20%EC%9D%B4%EC%8A%88/5.png)

![2.PNG](/images/2025-01-22/team-project-forklog-trouble-shooting-%EC%A7%80%EC%97%AD%20%26%20%EC%B9%B4%ED%85%8C%EA%B3%A0%EB%A6%AC%20%EC%86%8C%EB%B6%84%EB%A5%98%20%EC%A1%B0%ED%9A%8C%20%EC%95%88%EB%90%A8%20%EC%9D%B4%EC%8A%88/6.png)

이제야 정상적으로 동작한다. 

# 해결 이후

zero-based 때문에 이러한 문제가 발생했다는 걸 안 나는 원래 일상에서 페이지 번호는 1부터 시작하기에 굉장히 헷갈릴 것이라 생각하였기에 다음과 같이 아예 유틸리티 클래스를 만들어 사용하였다. 

```java
package com.acorn.utils;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;

public class PageUtil {
	
	/**
	 * PageRequest.of(page, size)에 들어가는 페이지 번호는 zero-based이다. 
	 * 즉, 페이지 번호를 1 입력하면 내부적으로는 2번 페이지로 인식된다. 
	 * 0을 입력해야 1번째 페이지로 입력됨. 
	 * 그러나 현실에서는 보통 페이지 번호를 1부터 시작한다. 
	 * 이러한 혼란을 없애기 위해 one-based의 메서드로 재정의함.
	 * 
	 * @author JeroCaller 
	 * @param page - 1부터 시작하는 페이지 번호. 범위는 page >= 1이어야 함.
	 * @param size
	 * @return
	 */
	public static Pageable getPageRequestOf(int page, int size) {
		return PageRequest.of(page - 1, size);
	}
	
	/**
	 * PageRequest.of(page, size)에 들어가는 페이지 번호는 zero-based이다. 
	 * 즉, 페이지 번호를 1 입력하면 내부적으로는 2번 페이지로 인식된다. 
	 * 0을 입력해야 1번째 페이지로 입력됨. 
	 * 그러나 현실에서는 보통 페이지 번호를 1부터 시작한다. 
	 * 이러한 혼란을 없애기 위해 one-based의 메서드로 재정의함.
	 * 
	 * @author JeroCaller 
	 * @param page - 1부터 시작하는 페이지 번호. 범위는 page >= 1이어야 함.
	 * @param size
	 * @return
	 */
	public static Pageable getPageRequestOf(
			int page, 
			int size, 
			Sort sort
	) {
		return PageRequest.of(page - 1, size, sort);
	}
	
	/**
	 * 현재 Page의 개수가 0 또는 null인지 판별
	 * 
	 * @author JeroCaller 
	 * @param <T>
	 * @param pages
	 * @return
	 */
	public static <T> boolean isEmtpy(Page<T> pages) {
		return (pages == null || pages.getNumberOfElements() == 0);
	}
}

```

`getPageRequestOf` 메서드가 one-based로 바꿔주는 메서드이다. 사실 page - 1만 하면 되지만 이를 컨트롤러나 서비스 계층에서 매번 이렇게 작성하는 것은 비효율적이고 가독성에도 별로 좋지 않을 거란 판단에 위와 같이 유틸리티 클래스로 만들었다. 호출문은 다음과 같다.

`Pageable pageRequest = PageUtil.getPageRequestOf(page, size);` 

---

References

[1] PageRequest is zero-based

[Pagination in spring does not work when i use findAllBy Containing and Ignore Case?](https://stackoverflow.com/questions/70906396/pagination-in-spring-does-not-work-when-i-use-findallby-containing-and-ignore-ca)

[2] [PageRequest (Spring Data Core 3.4.1 API)](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/PageRequest.html#of(int,int))