---
title: "[Spring Data JPA] Entity VS DTO"
category: "Spring"
tag: ["Spring", "Java", "JPA", "Spring Data JPA", "Entity", "DTO"]
---

# Entity VS DTO

몇몇 책들에서는 엔티티 객체에 담긴 데이터들을 직접 View에 전달하거나 반대로 사용자의 HTTP 요청에 의해 View에서 들어오는 데이터들을 DB에 영속화할 때 엔티티 객체 하나로 처리하도록 예제가 편성되어 있다고 한다. 그러나 다른 쪽에서는 이러한 점을 비판한다. 데이터를 전달할 때에는 엔티티 객체 자체를 이용하여 전달하는 게 아니라 데이터 전달 목적에 특화되어 있는 DTO(Data Transfer Object) 객체를 별도로 운용하는 것이 좋다고 한다. 

그렇다면 Entity 하나로 데이터 전달 목적까지 수행하게 하면 어떤 것이 문제가 될까?

- 목적 자체에 위배된다. Entity는 본래 DB와의 연동 및 영속성(persistence)을 목적으로 하는 객체이지 단순히 데이터를 계층 간 전달하는 목적이 아니다.
- 보안성에 문제가 된다. Entity만을 이용하여 View 단에 정보를 넘길 경우 사실상 클라이언트 쪽에서 서버에 숨겨져 있는 DB 내 테이블의 구조가 노출되는 것이나 마찬가지이다. 엔티티 객체는 기존 테이블의 구성과 똑같이 구성하거나 새로 생성할 때에도 그 자체로 테이블의 역할을 하기 때문이다. 또한, DB 내 테이블에는 비밀번호, 주민등록번호 등 매우 민감한 정보가 들어있을 수 있는데 Entity 객체로 View 쪽에 데이터를 전달할 경우 이러한 민감한 정보들까지도 노출될 위험성이 있다.
- View 단에 전달할 필요가 없는 데이터까지도 전달되어 네트워크 성능이 저하될 수 있다.

이러한 이유로 인해 데이터 전달 시에는 DTO라는 별도의 객체를 만들어 운용하는 것이 좋다. DTO를 만들어  Entity 대신에 데이터 전달에 사용할 경우의 장점은 다음과 같다. 

- 역할의 분담 : 애초에 Entity는 테이블 구조를 나타내며, 데이터의 영속성을 위한 객체이다. 이러한 역할과 데이터 전달이라는 역할을 분리할 수 있다.
- 보안성 확보 : Entity로 표현되는 테이블 구조를 클라이언트측으로부터 숨길 수 있고, 비밀번호, 주민등록번호 등 민감한 데이터를 넘기지 않게 할 수 있다. Entity 객체에 password라는 필드가 있을 경우 DTO 객체 내에서는 해당 필드를 정의하지 않으면 그만이다. 이러한 이유로 DTO를 별도로 운용해야 보안성도 확보할 수 있다.
- 성능 향상 : DTO는 Entity 객체가 보유한 모든 필드들 중 필요한 필드들만 정의하여 선별적으로 가져올 수 있다. 따라서 불필요한 데이터까지 네트워크 상에서 전달될 필요가 없게 되므로 네트워크 부하를 줄일 수 있다.
- 독립성 확보 : Entity 구조가 바뀌더라도 DTO와는 독립적이기에 데이터베이스 구조를 바꾸더라도 그에 따라 애플리케이션 로직도 변경할 필요가 없다.

# Entity와 DTO 변환 방법

이번에는 코드 상에서 Entity를 DTO로, DTO를 Entity로 변환하는 방법을 코드를 통해 살펴보겠다. Entity에서 DTO로 변환하는 기능은 toDto() 라는 메서드명을, DTO에서 Entity로 변환하는 기능은 toEntity() 라는 메서드명으로 정하여 구현해보겠다. 물론 꼭 이 이름으로 정의해야하는 것은 아니다. 그리고 필자는 toDto는 DTO 클래스 내부에, toEntity는 Entity 클래스 내부에 정의하였다. 이 역시도 사실 toDto와 toEntity를 가지는 별도의 유틸리티 클래스로 분리하여 정의해도 된다. 

```java
package com.jerocaller.model.entity;

import java.util.List;

import com.jerocaller.model.dto.ClassesDto;

import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.OneToMany;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class Classes {
	@Id
	private Integer classNumber;
	private String className;
	private Integer bonus;
	
	@OneToMany(mappedBy = "classes")
	private List<SiteUsers> users;
	
	public static Classes toEntity(ClassesDto dto) {
		return Classes.builder()
			.classNumber(dto.getClassNumber())
			.className(dto.getClassName())
			.bonus(dto.getBonus())
			.build();
	}
}

```

예제 1-1. Classes.java

```java
package com.jerocaller.model.entity;

import com.jerocaller.model.dto.SiteUsersDto;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@Getter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class SiteUsers {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer memberId;
	private String signUpDate;
	private Integer mileage;
	private String username;
	private Integer averPurchase;
	private String recommBy;
	
	@ManyToOne
	@JoinColumn(name = "class_number")
	private Classes classes;
	
	public static SiteUsers toEntity(SiteUsersDto dto) {
		return SiteUsers.builder()
			.memberId(dto.getMemberId())
			.signUpDate(dto.getSignUpDate())
			.mileage(dto.getMileage())
			.username(dto.getUsername())
			.averPurchase(dto.getAverPurchase())
			.recommBy(dto.getRecommBy())
			.build();
	}
}

```

예제 1-2. SiteUsers.java

```java
package com.jerocaller.model.dto;

import com.jerocaller.model.entity.Classes;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ClassesDto {
	private Integer classNumber;
	private String className;
	private Integer bonus;
	
	public static ClassesDto toDto(Classes entity) {
		return ClassesDto.builder()
			.classNumber(entity.getClassNumber())
			.className(entity.getClassName())
			.bonus(entity.getBonus())
			.build();
	}
}

```

예제 1-3. ClassesDto.java

```java
package com.jerocaller.model.dto;

import com.jerocaller.model.entity.Classes;
import com.jerocaller.model.entity.SiteUsers;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class SiteUsersDto {
	private Integer memberId;
	private String signUpDate;
	private Integer mileage;
	private String username;
	private Integer averPurchase;
	private String recommBy;
	
	private ClassesDto classesDto;
	
	public static SiteUsersDto toDto(SiteUsers entity) {
		Classes classes = entity.getClasses();
		ClassesDto classesDto = ClassesDto.toDto(classes);
		
		return SiteUsersDto.builder()
			.memberId(entity.getMemberId())
			.signUpDate(entity.getSignUpDate())
			.mileage(entity.getMileage())
			.username(entity.getUsername())
			.averPurchase(entity.getAverPurchase())
			.recommBy(entity.getRecommBy())
			.classesDto(classesDto)
			.build();
	}
}

```

예제 1-4. SiteUsersDto.java

위 예제에서는 1:N의 관계를 가지는 Classes와 SiteUsers 두 엔티티 객체에 대해 변환 메서드 구현을 수행하였다. 이 때 변환 시에는 lombok 라이브러리에서 제공하는 `@Builder` 어노테이션을 이용하여 builder 디자인 패턴으로 DTO 또는 Entity 객체를 생성하도록 하였다. 이 builder 패턴을 이용하면 특정 필드들을 제외하고 객체를 생성하더라도 개발자가 일일이 그러한 상황에 맞는 생성자 메서드를 오버로딩할 필요가 없다는 장점을 확보할 수 있다. 

또한 위 예제에서 Entity 객체에는 `@Setter` 어노테이션을 적용하지 않았다. 이는 애플리케이션 실행 도중에 의도치 않은 Entity 객체 내 정보의 변형을 방지하기 위함이다. setter 메서드는 애플리케이션 실행 도중에 호출되어 필드값을 변형시킬 수 있는 가변성을 가지고 있는데, 불변성이 필요할 경우 setter 메서드를 정의하지 않는 게 좋다. 

SiteUsersDto에서는 SiteUsers Entity에서 Classes Entity에 접근하여 등급명을 얻고자 하기 위해 ClassesDto 필드를 선언하였다. 한 가지 주의할 것은 헷갈려서 Classes라는 Entity 객체를 필드로 선언하지 않는 것이다. 앞서 언급했듯, Entity 객체가 직접 View에 노출되는 것은 좋지 않기 때문이며, 애초에 DTO 클래스 안에 Entity라는 성격이 전혀 다른 객체가 필드 타입이 되는 것도 말이 되지 않기 때문이다. 

# DTO 간 순환참조 문제

DTO로 변형시키는 toDto() 메서드 정의 시 주의할 점이 있다. 두 엔티티가 서로 양방향 연관관계를 맺고 있는 경우 이 두 엔티티의 구조를 DTO 클래스에도 그대로 반영하면 안된다. 만약 이를 반영하면 SiteUsersDto와 ClassesDto 클래스의 구조는 다음과 같이 된다. 

```java
package com.jerocaller.model.dto;

import com.jerocaller.model.entity.Classes;
import com.jerocaller.model.entity.SiteUsers;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class SiteUsersDto {
	private Integer memberId;
	private String signUpDate;
	private Integer mileage;
	private String username;
	private Integer averPurchase;
	private String recommBy;
	
	private ClassesDto classesDto;
	
	public static SiteUsersDto toDto(SiteUsers entity) {
		Classes classes = entity.getClasses();
		ClassesDto classesDto = ClassesDto.toDto(classes);  // #1
		
		return SiteUsersDto.builder()
			.memberId(entity.getMemberId())
			.signUpDate(entity.getSignUpDate())
			.mileage(entity.getMileage())
			.username(entity.getUsername())
			.averPurchase(entity.getAverPurchase())
			.recommBy(entity.getRecommBy())
			.classesDto(classesDto)
			.build();
	}
}

```

예제 2-1. SiteUsersDto.java

```java
package com.jerocaller.model.dto;

import java.util.List;
import java.util.stream.Collectors;

import com.jerocaller.model.entity.Classes;
import com.jerocaller.model.entity.SiteUsers;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.Setter;

@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class ClassesDto {
	private Integer classNumber;
	private String className;
	private Integer bonus;
	
	// 순환참조 문제 발생
	private List<SiteUsersDto> usersDto;
	
	public static ClassesDto toDto(Classes entity) {
		// 순환참조 문제 코드 시작점
		List<SiteUsers> siteUsers = entity.getUsers();
		List<SiteUsersDto> siteUsersDto = siteUsers.stream()
				.map(SiteUsersDto :: toDto)  // #2
				.collect(Collectors.toList());
		// 순환참조 문제 코드 끝
		
		return ClassesDto.builder()
			.classNumber(entity.getClassNumber())
			.className(entity.getClassName())
			.bonus(entity.getBonus())
			.usersDto(siteUsersDto)
			.build();
	}
}

```

예제 2-2. ClassesDto.java

위 상태에서 프로젝트를 실행하면 다음과 같이 stack overflow 문제가 발생한다.

![1.PNG](/images/2024-11-10/Spring-Data-Jpa-Entity-Vs-Dto/1.png)

위 두 코드를 보면 SiteUsersDto 클래스의 #1이라 주석 처리한 곳에서 ClassesDto의 toDto() 메서드를 호출하고 있다. 또한 ClassesDto 클래스의 #2이라 주석 처리한 곳에는 SiteUsersDto 클래스의 toDto() 메서드를 호출하고 있다. 이렇게 두 DTO 클래스가 서로의 클래스 내 toDto 메서드를 서로 참조하고 있기에 순환참조 문제가 발생한 것이다. 예를 들어 ClassesDto.toDto() 가 먼저 호출된 경우, 내부적으로 #2 코드를 만나 SiteUsers.toDto()가 호출되는데, 해당 메서드 내부에서도 다시 ClassesDto.toDto() 메서드를 호출하고 있기에 순환참조 문제가 발생한 것이다. 

이러한 문제를 해결하기 위해서는 두 DTO 클래스 중 한 쪽에서는 다른 DTO 클래스를 타입으로 하는 필드 선언을 하지 말아야 한다. 필자의 경우 ClassesDto 클래스 내 `private List<SiteUsersDto> usersDto;` 필드를 제거하였고, ClassesDto.toDto() 메서드 내 SiteUsersDto 관련 코드를 제거하니 순환참조 문제에 더 이상 빠지지 않게 되었다. 즉, 두 Entity가 양방향 매핑이 되어있다고 해서 DTO에서까지 그렇게 해선 안되는 것이다. 차라리 방금 언급한 것처럼 DTO에서는 단방향 매핑 형태로 한 쪽의 DTO 클래스 내부에서만 다른 DTO를 참조하도록 설계하거나, 아예 두 DTO 모두 상대의 DTO를 참조하지 않도록 한 후, JPQL의 Join 등을 이용하여 두 테이블 정보를 읽어오도록 하는 것이 더 좋을 것이다. 

# Entity와 DTO 간 변환 위치

필자가 조사를 해본 결과, Entity와 DTO 간 변환 위치를 어느 layer에서 할지에 대한 논쟁이 있었다. Controller 단에서 변환할 것이냐, Service 계층에서 변환할 것이냐에 대한 논쟁이었다. 

Controller에서 변환 작업을 수행해야한다는 측의 입장은 다음과 같았다. 

- service layer에서 미리 DTO로 전환할 경우 여러 controller에서 하나의 service 내 메서드를 사용할 수 있는 코드 재사용성이 떨어진다.
    - 필자는 아직 왜 Service layer에서 DTO로 전환 시 여러 controller에서의 코드 재사용성이 떨어진다는 건지 이해하지 못했다. 아마도 DTO만으로는 엔티티의 모든 정보를 알 수 없기 때문인 것으로 추측된다. 앞서 언급했듯, DTO에서는 엔티티의 특정 필드값은 받지 않도록 설정할 수 있다고 했는데, 이로 인해 DTO만으로는 엔티티 구조를 모두 파악할 수 없다. 이로 인해 직접 엔티티에 접근할 필요가 있는 작업에 대해서는 DTO를 반환하면 해당 작업을 할 수 없기 때문에 이러한 주장이 나온 것 같다.
- 요청에 의해 넘어온 DTO 정보가 DB에 더 가까운 Service 계층에서까지 넘어오면 자칫 DTO가 Repository 계층까지 넘어가는 경우도 있다.

반면 Service에서 변환 작업을 수행해야한다는 측의 입장은 다음과 같았다.

- Controller는 표현 계층과 매우 가까운 계층인데, 여기서까지 DTO가 아닌 Entity를 사용한다면 자칫 실수로 View에게까지 민감한 정보를 넘겨 보안성이 취약해질 수 있다.
- Controller에서까지 Entity를 사용하는 경우 Entity 구조 변경이 Controller의 변경으로까지 이어질 수 있어 유지보수성이 떨어질 수 있다.
- 여러 Entity 객체들로부터 정보들을 취합하여 DTO를 생성해야 하는 경우, 이러한 로직 자체가 Service 로직이기 때문에 Service에서 변환 작업을 수행해야 한다.

사실 이것이 “논쟁”인 것을 보면 아직 결론 내려진 정답은 없는 것으로 보인다. 어떤 사람들은 상황에 따라 유연하게 적용해야한다고 말하는 걸 보면 더더욱 그런 것 같다. 

필자 개인적으로는 Controller가 아닌 Service 계층에서 변환 작업을 하는 것이 더 타당하다는 생각이다. MVC 패턴에서 Controller는 보통 요청, 응답을 각각 적절한 Model, View에 전달하는 일종의 “지휘부” 역할이지 직접적으로 어떠한 로직을 수행하는 역할은 아니다. 그렇기에 Controller에게 지우는 부담을 최소화해야 하고, 그렇기에 Controller 내 코드의 양도 최소화해야한다고 생각한다. 이러한 입장에서 봤을 때 Entity와 DTO 간 변환 작업도 적어도 Controller 내부에서 해선 안된다고 생각한다. 사장이 사원의 일을 직접 하진 않는 것처럼 말이다. 이처럼 MVC의 Model, View, Controller 각자의 명확한 역할 분담을 위해서는 Entity, DTO 간 변환 작업은 Service에서 하는 것이 더 적절하다는 생각이다. 

물론 필자는 아직까지 Controller에서 변환 작업을 해야 여러 컨트롤러에서 공통의 서비스 메서드를 사용할 수 있다는 점, DTO는 엄연히 View에서 사용자의 요청을 담아온 데이터인데 View가 Service라는 Model에 직접 들어와선 안된다, 등의 이야기에 대해서는 잘 감이 안온다. 이는 아직 필자가 실전 경험이 부족하기 때문일 수도 있겠다. 그렇기에 지금은 Service 단에서 변환 작업을 해야 한다는 게 맞다는 생각이지만, 실무 경험을 쌓아가면서 조금 더 고민해보는 것도 좋을 것 같다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] [역할 분리를 위한 Entity, DTO 개념과 차이점](https://wildeveloperetrain.tistory.com/101)

[4] [📚 Dto와 Entity를 분리하는 이유, 분리하는 방법](https://velog.io/@0sunset0/Dto%EC%99%80-Entity%EB%A5%BC-%EB%B6%84%EB%A6%AC%ED%95%98%EB%8A%94-%EC%9D%B4%EC%9C%A0-%EB%B6%84%EB%A6%AC%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)

[5] [DTO의 사용 범위에 대하여](https://tecoble.techcourse.co.kr/post/2021-04-25-dto-layer-scope/)