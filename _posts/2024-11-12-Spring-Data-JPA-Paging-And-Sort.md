---
title: "[Spring Data JPA] Paging And Sort"
category: "Spring"
tag: ["Spring", "Spring Boot", "Spring Data JPA", "Java", "Page", "Paging", "Sort"]
---

데이터를 조회한 결과를 화면에 출력할 때 조회된 데이터의 수가 너무 많으면 보통 이를 다 출력하지 않고 일부만 출력되도록 페이징 기능을 구현한다. 

![사진 1-1. 데이터들을 일정한 크기의 페이지별로 나눈 후, 사용자가 원하는 페이지 번호를 클릭하면 해당 데이터만 보여주도록 하는 모습. 위 사진에서는 원래 조회된 데이터가 총 24개이나 페이징 기능을 이용하여 화면에 10개만 출력되도록 하고 있다. 위 사진 아래쪽 빨간 네모 친 곳에 페이지네이션 요소가 있어 이를 통해 사용자는 원하는 페이지의 데이터만 조회할 수 있다. ](/images/2024-11-12/Spring-Data-JPA-Paging-And-Sort/1.png)

사진 1-1. 데이터들을 일정한 크기의 페이지별로 나눈 후, 사용자가 원하는 페이지 번호를 클릭하면 해당 데이터만 보여주도록 하는 모습. 위 사진에서는 원래 조회된 데이터가 총 24개이나 페이징 기능을 이용하여 화면에 10개만 출력되도록 하고 있다. 위 사진 아래쪽 빨간 네모 친 곳에 페이지네이션 요소가 있어 이를 통해 사용자는 원하는 페이지의 데이터만 조회할 수 있다. 

그런데 이 페이징 기능은 개발자가 직접 구현하려고 하면 생각보다 꽤 어렵고 번거로운 작업이 된다. 다행히도 Spring Data JPA에서는 repository를 통해 페이징에 관한 도구들을 제공하고 있어 개발자가 더 쉽게 페이징 기능을 구현하도록 도와주고 있다. 또한 페이징과 동시에 조회된 데이터들을 특정 기준에 따라 정렬할 수 있도록 하는 기능도 제공하고 있다. 이 글에서는 Spring Data JPA에서 제공하는 정렬 및 페이징의 기능에 대해 살펴보고, 이를 이용하여 실제로 페이징 및 페이지네이션을 구현하는 예제를 살펴보도록 하겠다. 

# Sort

원래 SQL에서도 조회된 데이터들을 특정 기준에 따라 정렬하여 가져올 수 있다. 이는 ORDER BY를 이용하면 된다. 

Spring Data JPA에서도 이와 같은 정렬 기능을 어렵지 않게 사용할 수 있다. 원래는 쿼리 메서드에서도 OrderBy란 이름을 추가하여 정렬을 할 수도 있다. 

```java
List<Users> findByUsernameOrderByClassNumberAsc(String username);
```

그러나 정렬 기준이 많아진다면 쿼리 메서드를 사용하는 방식은 메서드명이 너무 길어져 가독성이 좋지 않을 것이다. 

이럴 때 대신 사용할 수 있는 것이 Sort 클래스이다. `org.springframework.data.domain` 패키지에 존재하는 이 클래스의 사용방법은 대략 다음과 같다. 

1. 쿼리 메서드 매개변수에 Sort 객체 타입 변수가 들어갈 수 있는 매개변수를 마련한다. (단, findAll()과 같이 Spring Data JPA에서 기본으로 제공하는 메서드들은 굳이 repository에 정의하지 않아도 이미 이를 자동으로 제공하고 있다.)

```java
List<Users> findByUsername(String username, Sort mySort);
```

1. 이 repository를 사용하는 service 계층에서 다음과 같이 Sort 객체를 생성하면 된다. 

```java
import org.springframework.data.domain.Sort;
import org.springframework.data.domain.Sort.Order;

// ... 생략

Sort mySort = Sort.by(
	Order.asc("userClassInfo.classNumber"), // asc: 오름차순 정렬
	Order.desc("averPurchase"),  // desc: 내림차순 정렬
	Order.desc("mileage")
);

List<Users> users = repository.findByUserName(username, mySort);

// ... 생략
```

먼저 정렬하고자 하는 필드명들을 각각 Order.asc() 또는 Order.desc() 메서드의 매개변수로 전달한다. Order 클래스는 Sort의 내부 static 클래스이다. 그 후, Order로 생성한 정렬 기준 객체들을 Sort.by() 메서드의 인자로 전달하면 된다. 이렇게 정렬 기준이 등록된 Sort.by() 메서드의 반환값인 Sort 객체를 repository에 만들어놓은 쿼리 메서드의 인자로 전달하면 된다. 그러면 DB에서 조회된 데이터들이 개발자가 부여한 정렬 기준에 따라 정렬된 채로 넘어오게 된다. 

이를 확인해보자. 먼저 비교를 위해 정렬되지 않은 채로 모든 데이터를 조회하는 예제를 만들어보았다. 먼저 Service 클래스이다. 

```java
@Override
public void selectAll(Model model, String keyName) {
	List<SiteUsersDto> users = siteUsersRepository.findAll()
		.stream()
		.map(siteUsersConverter :: toDto)
		.collect(Collectors.toList());
	model.addAttribute(keyName, users);
}
```

예제 1-1. SiteUserService.java 

위 예제에서는 아무런 정렬 기준도 부여하지 않은 채 `findAll()` 메서드를 호출하여 모든 데이터를 조회한 후, 엔티티 형태의 데이터를 Dto로 변환하여 이를 Model에 담고 있는 모습이다. 이러한 조회된 데이터들을 화면에 출력해보면 다음과 같다. 

![사진 2-1. 정렬을 하지 않은 상태로 DB내 모든 데이터를 조회한 결과](/images/2024-11-12/Spring-Data-JPA-Paging-And-Sort/2.png)

사진 2-1. 정렬을 하지 않은 상태로 DB내 모든 데이터를 조회한 결과

위 사진은 데이터가 너무 많아 일부 데이터만 보여지고 있다. 아래로 스크롤해보면 모든 데이터를 볼 수 있다. 

DB에서는 애초에 “멤버 번호”를 기준으로 자동 정렬되어 있는 상태이기에 이를 그대로 가져와서 “멤버 번호” 기준으로 오름차순으로 정렬되어 있는 모습을 볼 수 있다. 다시 한 번 언급하지만, 이는 이미 DB에 정렬되어 있는 데이터들을 그대로 가져온 것이지, 자바 코드에서는 전혀 정렬을 하지 않았다. 그저 있는 데이터를 그대로 가져왔을 뿐이다. 

이번에는 특정 기준들을 부여하여 정렬된 데이터를 가져와보겠다. 필자는 다음과 같이 여러 개의 정렬 기준들을 설정하여 모든 데이터를 가져오도록 하였다. 

```java
public void selectAllSorted(Model model, String keyName) {
	// 1차 정렬 기준 : 등급 번호별 내림차순으로 정렬
	// SiteUsers 엔티티에 정의된 종속 엔티티 필드명을 그대로 사용하였다.
	Sort mySort = Sort.by(
		Order.asc("userClassInfo.classNumber"),
		Order.desc("averPurchase"),
		Order.desc("mileage")
	);
	List<SiteUsersDto> users = siteUsersRepository.findAll(mySort)
		.stream()
		.map(siteUsersConverter :: toDto)
		.collect(Collectors.toList());
	model.addAttribute(keyName, users);
	}
```

예제 1-2. SiteUserService.java 

필자는 1차 정렬 기준으로 유저의 등급을 표현하는 등급 숫자인 classNumber로 정하였으며, 2차로는 평균 구매액, 3차 정렬 기준으로는 보유 마일리지를 기준으로 하였다. 참고로 Order.asc(), Order.desc() 메서드 내부에 필드명을 적을 때에는 DB의 컬럼명이 아닌 Entity의 필드명을 입력해야 한다. 위 코드에서 `Order.asc("userClassInfo.classNumber")` 이 부분은 SiteUsers 라는 Entity 내부에 UserClassInfo라는 종속 엔티티의 필드명인 `userClassInfo` 을 이용한 것이다. 그리고 UserClassInfo 엔티티에는 classNumber라는 필드명이 있기에 이를 참조한 것이다. 참고를 위해 두 엔티티의 코드를 다음과 같이 첨부한다. 

```java
package com.jerocaller.model.entity;

import java.time.LocalDate;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.PrePersist;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Entity
@Table(name = "site_users")
@Getter
@Setter
public class SiteUsers {
	@Id
	private Integer memberId;
	
	@Column(name = "sign_up_date")
	private LocalDate signUpDate;
	
	@Column(length = 11)
	private Integer mileage;
	
	@Column(length = 50)
	private String username;
	
	@Column(name = "aver_purchase", length = 11)
	private Integer averPurchase;
	
	@Column(name = "recomm_by", length = 50)
	private String recommBy;
	
	@ManyToOne(fetch = FetchType.EAGER)
	@JoinColumn(name = "class_number")
	private UserClassInfo userClassInfo;  // 이 필드를 이용
	
	@PrePersist
	private void initDate() {
		// 현재 날짜로 등록일 지정.
		signUpDate = LocalDate.now();
	}
}

```

예제 1-3. SiteUsers.java

```java
package com.jerocaller.model.entity;

import java.util.List;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.Id;
import jakarta.persistence.OneToMany;
import jakarta.persistence.Table;
import lombok.Getter;
import lombok.Setter;

@Entity
@Table(name = "classes")
@Getter
@Setter
public class UserClassInfo {
	@Id
	private Integer classNumber;  // 이 필드를 참조함.
	
	@Column(name = "class_name", length = 20)
	private String className;
	
	@Column(length = 11)
	private Integer bonus;
	
	@OneToMany(mappedBy = "userClassInfo", fetch = FetchType.EAGER)
	private List<SiteUsers> users;
}

```

예제 1-4. UserClassInfo.java

다시 본론으로 돌아와서, 필자가 정한 정렬 기준을 적용하여 모든 데이터를 조회해보면 다음과 같은 결과를 얻는다.

![사진 2-2. Sort를 이용하여 특정 기준으로 정렬을 적용한 상태에서 모든 데이터를 가져온 결과](/images/2024-11-12/Spring-Data-JPA-Paging-And-Sort/3.png)

사진 2-2. Sort를 이용하여 특정 기준으로 정렬을 적용한 상태에서 모든 데이터를 가져온 결과

“멤버 번호” 필드만 봐도 아까와는 확연히 다른 것을 알 수 있다. 일단 등급별로 오름차순으로 정렬하였기에 위와 같이 1등급에 해당하는 “vvip”에 해당하는 레코드들이 최상단에 모여 뜬 것을 볼 수 있다. 2차 정렬 기준으로는 “월 평균 구매액”을 기준으로 내림차순으로 정렬된 것을 볼 수 있다. 3차 기준으로 “보유 마일리지”를 택하였는데, 자세히 보면 “월 평균 구매액”이 존재하지 않는 “vvip” 등급을 가지는 유저 두 건의 보유 마일리지가 내림차순으로 정렬된 것을 볼 수 있다. 

# paging

페이징과 관련하여 Spring data JPA에서는 다음의 클래스 및 인터페이스들을 제공한다. 

- `Page<Entity>` : 특정 페이지의 데이터들만을 담기 위해 사용되는 인터페이스. `java.util.List` 를 상속받는다고 한다.
- `Pageable` : 페이지 요청에 사용되는 인터페이스. 주로, 쿼리 메서드의 매개변수의 타입으로 사용되며, 이 인터페이스의 구현체인 `PageRequest.of()` 메서드를 이용하여 실질적인 페이지 요청 정보를 repository에 전달하여 요청하는 방식이다.
- `PageRequest` : `Pageable` 인터페이스의 구현체이며, 여러 메서드가 있지만 대표적으로 `of()` 메서드를 이용하여 실질적인 페이지 요청 정보를 담아 전달하여 특정 페이지의 데이터만 조회하도록 할 수 있다.
    - `of(int pageNumber, int pageSize)` : 전체 페이지 중 조회하고자 하는 특정 페이지와, 한 페이지 당 데이터의 개수를 설정한다. 예를 들어 전체 데이터가 20개가 있고, 한 페이지 당 5개의 데이터만 보여지도록 하고 싶다면 총 4페이지가 생성될 것이다. 그 중 2페이지에 해당하는 데이터만 보고자 한다면 `of(2, 5)` 와 같이 작성하면 된다. 조회된 전체 데이터의 개수를 토대로 한 페이지 당 데이터의 개수만 명시하면 총 페이지 수도 자동으로 내부적으로 계산되는 식이다.
    - `of(int pageNumber, int pageSize, Sort sort)` : 조회된 특정 페이지 내 데이터들이 특정 기준으로 정렬하고자 할 때 세 번째 인자로 `Sort` 객체를 전달한다.
- `PageImpl<T>` : `Page` 인터페이스의 구현체이다. Repository로부터 받아온 `Page<Entity>` 의 결과를 `Page<DTO>` 형태로 변환할 때 사용할 수 있다. Entity에서 DTO로 변환하기 위한 구체적인 사용 예는 다음과 같다.

```java
public Page<SiteUsersDto> selectPages(String keyName, Pageable pageRequest) {
	Page<SiteUsers> pageWithEntity = siteUsersRepository.findAll(pageRequest);

	List<SiteUsersDto> pageDtos = pageWithEntity.stream()
		.map(siteUsersConverter :: toDto)
		.collect(Collectors.toList());
		
	// PageImpl은 Page라는 인터페이스의 구현체이다. 
	// PageImpl 생성자 매개변수에는 
	// 첫 번째 인자에는 List<Dto> 형태의 데이터를, 
	// 두 번째 인자에는 페이지 요청 정보가 담긴 PageRequest 객체를, 
	// 세 번째 인자에는 전체 데이터의 개수를 대입한다. 
	// PageImpl을 통해 궁극적으로 Page<Entity>를 Page<Dto)로 변환 가능하다.
	Page<SiteUsersDto> result = new PageImpl<SiteUsersDto>(
		pageDtos, 
		pageRequest, 
		pageWithEntity.getTotalElements()
	);
		
	return result;
}
```

예제 2-1. SiteUserService.java

1. 먼저 repository로부터 `Page<Entity>` 형태의 데이터를 받아온다. 
2. Page는 List를 상속하므로 `List<DTO>` 형태로 변환 가능하다. 이를 이용한다. 
3. `PageImpl<DTO>` 객체를 생성한다. 생성자 메서드의 인자로는 첫 번째는 앞선 2번에서 만든 `List<DTO>` 를, 두 번째는 페이지 요청에 사용되었던 `Pageable` 객체를, 세 번째 인자로는 전체 데이터 수를 대입한다. 이 `PageImpl<DTO>` 객체를 부모 인터페이스인 `Page<DTO>` 타입 참조 변수에 담을 수 있다. 

![그림 1-1. Page 관련 인터페이스 및 클래스 간 관계 UML](/images/2024-11-12/Spring-Data-JPA-Paging-And-Sort/Spring_Data_JPA_Page_UML.png)

그림 1-1. Page 관련 인터페이스 및 클래스 간 관계 UML

한 편 `Page` 인터페이스에는 다음과 같은 메서드들을 제공하여 페이지 관련 정보들을 확인할 수 있다. 

- `getTotalPages()` : 전체 페이지 수
- `getTotalElements()` : 전체 데이터 수
- `getNumber()` : 현재 페이지 번호 (기본은 인덱스 0부터 시작한다)
- `getNumberOfElements()` : 현재 페이지 하나에 담긴 데이터 수
- `getSize()` : 페이지 당 최대 데이터 수
- `hasNext()` : 다음 페이지 존재 여부
- `hasPrevious()` : 이전 페이지 존재 여부
- `hasContent()` : 내용 존재 여부

이 메서드들을 활용하면 페이징 기능 및 페이지네이션을 구현할 수 있다. 

다음은 위 메서드들의 반환값을 확인하기 위한 코드 및 실행결과이다.

```java
public Page<SiteUsersDto> selectPages(String keyName, Pageable pageRequest) {
	Page<SiteUsers> pageWithEntity = siteUsersRepository.findAll(pageRequest);
		
	// Page 인터페이스에 어떤 메서드가 있는지 확인해보기
	log.info("=== From Method [selectPages] ===");
	log.info("페이징 관련 메서드 확인해보기");
	log.info("전체 페이지 수: " + pageWithEntity.getTotalPages());
	log.info("전체 데이터 수: " + pageWithEntity.getTotalElements());
	log.info("현재 페이지 번호 (기본은 인덱스 0부터 시작한다): " + pageWithEntity.getNumber());
	log.info("현재 페이지 하나에 담긴 데이터 수 : " + pageWithEntity.getNumberOfElements());
	log.info("페이지 당 최대 데이터 수: " + pageWithEntity.getSize());
	log.info("다음 페이지 존재 여부: " + pageWithEntity.hasNext());
	log.info("이전 페이지 존재 여부: " + pageWithEntity.hasPrevious());
	log.info("내용 존재 여부: " + pageWithEntity.hasContent());
		
	log.info("=== 페이징 관련 메서드 확인해보기 끝 ===");
		
	List<SiteUsersDto> pageDtos = pageWithEntity.stream()
		.map(siteUsersConverter :: toDto)
		.collect(Collectors.toList());
		
	// PageImpl은 Page라는 인터페이스의 구현체이다. 
	// PageImpl 생성자 매개변수에는 
	// 첫 번째 인자에는 List<Dto> 형태의 데이터를, 
	// 두 번째 인자에는 페이지 요청 정보가 담긴 PageRequest 객체를, 
	// 세 번째 인자에는 전체 데이터의 개수를 대입한다. 
	// PageImpl을 통해 궁극적으로 Page<Entity>를 Page<Dto)로 변환 가능하다.
	Page<SiteUsersDto> result = new PageImpl<SiteUsersDto>(
		pageDtos, 
		pageRequest, 
		pageWithEntity.getTotalElements()
	);
		
	return result;
}
```

예제 2-2. SiteUserService.java

![사진 3-1. 한 페이징 당 데이터 개수를 10개로 잡고, 1페이지를 조회한 모습.](/images/2024-11-12/Spring-Data-JPA-Paging-And-Sort/4.png)

사진 3-1. 한 페이징 당 데이터 개수를 10개로 잡고, 1페이지를 조회한 모습.

```java
전체 페이지 수: 3
전체 데이터 수: 24
현재 페이지 번호 (기본은 인덱스 0부터 시작한다): 0
현재 페이지 하나에 담긴 데이터 수 : 10
페이지 당 최대 데이터 수: 10
다음 페이지 존재 여부: true
이전 페이지 존재 여부: false
내용 존재 여부: true
```

예제 2-2 콘솔창 결과

# 페이징 및 페이지네이션 구현

앞선 페이지 관련 API들을 토대로 페이징 및 페이지네이션 기능을 구현해보겠다. 먼저 구현 완료한 결과물부터 보겠다. 

<video src="/resources/2024-11-12/Spring-Data-JPA-Paging-And-Sort/20241117_161128.mp4"
    controls="controles"
    muted="muted"
    width="100%"
></video>

현재 데이터가 적은 관계로 한 페이지 당 보여주는 데이터의 수가 3개로 매우 적다. 그럼에도 페이지네이션 작동 여부를 확인하기 위해 일부러 페이지 수를 늘리기 위해 한 페이지당 데이터 수를 3개로 설정하였다. 

페이지 요청 정보(PageRequest)를 받고 이를 토대로 repository에 요청하는 로직은 앞선 예제에서 보인 SiteUserService.java 의 selectPages() 메서드에서 수행하고 있으니 참고바란다. 다시 정리하자면 다음의 방식대로 흘러간다.

```java
// 예시

// repository layer
public interface MyInter extends JpaRepository<Entity, Id> {
	
	Page<Entity> findByClassNumber(Integer classNumber, Pageable pageRequest);
}

// service layer
// 정렬 기준이 있다면 선택적으로 정렬 기준 생성
Sort mySort = Sort.by(
	Order.asc("..."), 
	Order.desc("..."),
	//...
);

// 페이지 요청 정보 생성.
Pageeable pageRequest = PageRequest.of(2, 10, mySort);

// 페이지 요청 정보를 쿼리 메서드의 인자로 전달하여 해당 페이지 데이터를 받는다.
Page<Entity> result = repository.findByClassNumber(1, pageRequest);
```

다시 필자의 예제로 돌아와, Controller에서는 사용자가 요청한 페이지 번호를 받도록 구성하였다.

```java
@GetMapping("/userList")
	public String showAllUsers(
		Model model, 
		PageRequestBean pRequest, 
	) {
	SiteUserService serviceImpl = (SiteUserService)service;
		
	// 사용자 요청 재가공 로직
	ReprocessRequestOfPage reprocessor = new ReprocessRequestOfPage();
	Pageable pageRequest = reprocessor.getReprocessedPageRequest(pRequest);
		
	// Repository에 페이지 요청 전달 및 응답 데이터 받기
	Page<SiteUsersDto> pageResult = serviceImpl.selectPages("pages", pageRequest);
		
	// 페이지 네비게이션을 위한 관련 정보 생성 (응답 데이터 생성)
	final int BLOCKS = 5; // 페이지네이션에 뜨게할 페이지 블록 개수.
	PageResponseBean<SiteUsersDto> pageResponse 
		= new PageResponseBean<SiteUsersDto>(pageResult, BLOCKS);
		
	// 응답
	model.addAttribute("users", pageResult);
	model.addAttribute("nav", pageResponse);
		
	return "userList";
}
```

예제 3-1. SiteController.java

여기서는 사용자의 요청 페이지 번호를 `PageRequestBean pRequest` 을 통해 받고 있다. 이를 토대로 repository에 페이지 요청 정보를 전달할 수 있게끔 사용자로부터 넘어온 페이지 요청 정보를 `ReprocessRequestOfPage` 라는 클래스 내부에서 재가공하도록 하였다. 그 후 service 클래스로부터 재가공한 페이지 요청 정보를 전달하여 그 응답으로 페이지 데이터들을 얻는다. 그 후 view 쪽에서 손쉽게 페이지네이션 기능을 구현할 수 있게끔 `PageResponseBean` 클래스를 이용하여 관련 정보들도 페이지 데이터와 함께 같이 View에 전달하는 방식으로 구성하였다. 

```java
package com.jerocaller.requestbean;

import lombok.Getter;
import lombok.Setter;

/**
 * 원래 여러 개의 요청 정보가 있을 것으로 기대하여 
 * 이렇게 form bean 객체를 정의하였지만 
 * 막상 만들고 나니 요청 정보가 하나뿐이지만 
 * 이걸 지우고 다시 컨트롤러 단에서 requestParam으로 
 * 받는 식으로 고치면 나중에 또 요청 정보 추가할 일이 
 * 생길까봐 이렇게 남긴다. 
 */
@Getter
@Setter
public class PageRequestBean {
	private Integer selectedPage = 1;
}

```

예제 3-2. PageRequestBean.java

```java
package com.jerocaller.requestbean;

import org.springframework.data.domain.PageRequest;
import org.springframework.data.domain.Pageable;
import org.springframework.data.domain.Sort;
import org.springframework.data.domain.Sort.Order;

/**
 * 사용자 요청 정보를 repository에 잘 전달할게끔 
 * 요청을 재가공한다. 
 */
public class ReprocessRequestOfPage {
	private final int PAGE_SIZE = 3;
	
	public Pageable getReprocessedPageRequest(PageRequestBean pRequest) {
		// 정렬 및 페이지 요청은 서비스에서 자체적으로 정하기보단
		// 컨트롤러 단에서 서비스로 요청하게끔 하여 조금 더 유연하고 동적인 요청을 가능하게끔
		// 하는 것이 좋지 않을까란 생각에 정렬 및 페이지 요청 코드는 컨트롤러 단에서 해보았다.
		Sort pageSort = Sort.by(
			Order.asc("userClassInfo.classNumber"), 
			Order.desc("averPurchase"),
			Order.desc("mileage")
		);

		// 페이지네이션 블록 개수가 유지되는지 보고자 한다면
		// 두 번째 인자를 3으로 설정.
		Pageable pageRequest = PageRequest.of(
			pRequest.getSelectedPage() - 1, 
			PAGE_SIZE,
			pageSort
		);
		return pageRequest;
	}
}

```

예제 3-3. ReprocessRequestOfPage.java

사용자로부터 받은 페이지 요청 정보를 토대로 repository에 페이지 요청을 하기 위해 요청을 재가공하는 로직이다. 특정 정렬 기준을 `Sort` 로 구성하고 있으며, 이를 페이지 요청 관련 클래스인 `PageRequest` 의 `of()` 메서드의 세 번째 인자로 넘기고 있다. 그 외에도 각각 사용자의 요청 페이지 번호 및 페이지 크기 정보를 `Pageable` 참조 변수에 담은 후 이를 반환하고 있다. 재가공되어 나온 페이지 요청 정보는 앞선 예제에서 본 service 클래스의 메서드 인자로 전달되어 그에 해당하는 페이지 데이터를 받아오게 된다. 

```java
package com.jerocaller.responsebean;

import org.springframework.data.domain.Page;

import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Getter
@NoArgsConstructor
@Slf4j
public class PageResponseBean<T> {
	private Integer startPage;
	private Integer endPage;
	private Integer currentPage;
	private Integer blockNumToShow;
	
	/**
	 * 
	 * @param pageInfo
	 * @param blockNumToShow - 홀수 권장
	 */
	public PageResponseBean(Page<T> pageInfo, Integer blockNumToShow) {
	    this.blockNumToShow = blockNumToShow;
		
		// 현재 선택된 페이지 블록이 페이지네이션에서 정 가운데에 오도록 한다. 
		Integer halfBlocks = (blockNumToShow / 2);
		
		currentPage = pageInfo.getNumber() + 1;
		
		Integer startDiff = currentPage - halfBlocks;
		
		// 기본 로직.
		// 예를 들어 halfBlock = 2 라 하고
		// currentPage = 4라고 한다면
		// startPage = Math.max(currentPage - 2, 1)
		// 라고 하면 startPage = 2가 된다. 
		// 만약 현재 선택된 페이지가 1에 가까워 왼쪽에 더 표시할 
		// 페이지 블록이 없을 경우를 대비해 max()를 사용하였으며, 
		// 두 번째 인자로 1번째 페이지를 지정하도록 하였다. 
		// endPage = Math.min(currentPage + 2, totalPage)
		// 라 하면 totalPage = 8일 때 endPage = 6이 된다. 
		// 이 역시도 현재 선택된 페이지가 totalPage에 가까울 때 
		// 더 이상 오른쪽에 표시할 페이지 블록이 없을 때 대신 끝 
		// 페이지가 표시되게끔 한 것이다. 
		// 위 예에 따르면 startPage = 2, endPage = 6, 
		// currentPage = 4가 되므로 화면에 표시되는 페이지 번호들은 
		// (이전) 2, 3, [4], 5, 6 (다음) 이 되어 총 5개의 페이지 블록이 보이면서도 
		// 현재 선택된 페이지 번호가 정 가운데에 놓이도록 할 수 있다. 
		
		Integer maxFormular = startDiff;
		Integer minFormular = currentPage + halfBlocks;
		
		// 페이지 블록 개수가 일정하게 표시되도록 하기 위한 로직. 
		// 아래 로직이 없다면 1 또는 끝 페이지에 가까울 때 
		// 정해진 블록 개수보다 더 적게 화면에 표시되는 버그가 나타난다. 
		if (startDiff <= 0) {
			minFormular = blockNumToShow;
		} else if (startDiff > (pageInfo.getTotalPages() / 2)) {
			maxFormular = pageInfo.getTotalPages() - blockNumToShow + 1;
		}
		
		// 페이지네이션에서 맨 왼쪽에 표시될 페이지 번호
		startPage = Math.max(maxFormular, 1);
		
		// 페이지네이션에서 맨 오른쪽에 표시될 페이지 번호.
		endPage = Math.min(minFormular, pageInfo.getTotalPages());
		
		log.info("startPage : " + startPage);
		log.info("endPage : " + endPage);
		log.info("currentPage: " + currentPage);
	}
}

```

예제 3-4. PageResponseBean.java 

위 코드는 페이지네이션 UI를 구현할 때 정해진 일정한 개수의 페이지 블록만 나오게 하면서도 현재 선택된 페이지 번호와 가까운 번호들만 출력되게끔 하기 위한 로직이다. 생성자 메서드에서 이 로직을 구현하였으며, View 측에서 손쉽게 페이지네이션 관련 정보를 얻어 프레젠테이션 로직을 쉽게 구현할 수 있도록 관련 정보들을 필드에 담아 getter 메서드로 얻을 수 있게끔 하였다. 앞서 보인 영상 결과물에서 페이지의 어떤 번호를 누르던 항상 5개의 페이지 블록만 나오던 것도 이 클래스의 생성자 메서드에 해당 로직을 구현하였기에 가능한 일이었다. 

이러한 페이지네이션 정보를 받아 페이지네이션 화면을 구성하는 View는 다음과 같이 구성되어 있다. 참고로 thymeleaf를 사용하였다.

```html
<!-- 생략 -->
<nav aria-label="Page navigation example">
  <ul class="pagination justify-content-center">
  <!-- 이전 버튼 시작 -->
  <th:block th:if="${nav.currentPage - 1 > 0}">
    <li class="page-item">
	  <a class="page-link" 
	    th:href="@{/site/userList(selectedPage=${nav.currentPage - 1})}"
	  >이전</a>
    </li>
  </th:block>
  <th:block th:unless="${nav.currentPage - 1 > 0}">
    <li class="page-item">
      <a class="page-link disabled" href="#">이전</a>
    </li>
  </th:block>
  <!-- 이전 버튼 끝 -->
  <!-- 페이지 번호 버튼 구성 -->
  <th:block th:each="page : ${#numbers.sequence(nav.startPage, nav.endPage)}">
    <th:block th:if="${nav.currentPage == page}">
      <li class="page-item active">
    </th:block>
    <th:block th:unless="${nav.currentPage == page}">
      <li class="page-item">
    </th:block>
        <a class="page-link" 
          th:href="@{/site/userList(selectedPage=${page})}"
          th:text="${page}"
        ></a>
      </li>
  </th:block>
  <!-- 페이지 번호 버튼 구성 끝 -->
  <!-- 다음 버튼 구성 -->
  <th:block th:if="${nav.currentPage + 1 <= nav.endPage}">
    <li class="page-item">
      <a class="page-link" 
        th:href="@{/site/userList(selectedPage=${nav.currentPage + 1})}"
      >다음</a>
    </li>
  </th:block>
  <th:block th:unless="${nav.currentPage + 1 <= nav.endPage}">
    <li class="page-item">
      <a class="page-link disabled" href="#">다음</a>
    </li>
  </th:block>
  <!-- 다음 버튼 구성 끝 -->
  </ul>
</nav>
```

예제 3-5. userList.html

> 위에서 보인 예제 코드의 전체 소스 코드는 다음의 사이트에서 확인할 수 있다.
> 
> 
> [spring-study/SpringDataJpaPaging/src/main at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/SpringDataJpaPaging/src/main)
> 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[4] paging 및 sort 공식 API 문서

[org.springframework.data.domain (Spring Data Core 3.3.5 API)](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/domain/package-summary.html)

[5] 페이지네이션(pagination)이란?

[페이지네이션 (Pagination) \| 컴포넌트 - KRDS](https://uiux.egovframe.go.kr/guide/component/component_03_06.html)

[6] 페이지네이션(pagination)이란?

[http://61.107.76.13/Li/04_25.html](http://61.107.76.13/Li/04_25.html)

[7] 클래스 UML 관련 정보

[UML: 클래스 다이어그램과 소스코드 매핑](https://www.nextree.co.kr/p6753/)