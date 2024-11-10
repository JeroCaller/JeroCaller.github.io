---
title: "[Spring Data JPA] Entity Join"
category: "Spring"
tag: ["Spring Data JPA", "Java", "JPA", "Entity", "join"]
---

실무에서는 단 하나의 테이블을 가지고만 사용하지는 않을 것이다. 대부분의 경우 역할 구분을 위해 여러 개의 테이블들을 DB 내에 생성한 다음, 한 쪽 테이블에는 PK를, 또 다른 테이블에는 FK를 설정하여 이 둘을 Join하여 사용할 것이다. (PK-FK 관계가 아니더라도 필요에 따라 여러 개의 테이블들을 조인하여 필요한 정보들을 한꺼번에 취합할 때도 있을 것이다) 

Spring Data JPA에서의 Entity에 대해서도 마찬가지이다. 테이블과 매핑되는 Entity 클래스도 실무에서는 둘 이상의 Entity 클래스들이 필요할 것이다. 그리고 보통 이런 Entity들은 서로 연관 관계를 가질 것이다. 이 경우에도 역시 Join이 필요할 것이다. 이 Join에 대해, 실제 DB에서의 Join과 비슷한 면도 있겠지만, 다른 면들도 있다. 

이 페이지에서는 둘 이상의 Entity 클래스 간에 연관 관계가 있을 때 이들을 연결하기 위한 Join에 대해 살펴보겠다. 특히, 실무에서는 대부분 일대일, 일대다 관계, 그 중에서도 일대다 관계가 많다고 하므로 주로 일대다 관계를 중점으로 살펴보겠다. 

# Entity Mapping

DB에서는 두 테이블을 조인하면 부모 테이블에서 자식 테이블을 참조할 수 있고, 반대로 자식 테이블에서도 부모 테이블을 참조할 수 있다. 즉, 기본적으로 양방향으로 참조할 수 있다. 반면 Entity에서는 양방향뿐만 아니라 단방향으로도 연관 관계를 설정할 수 있다. 단방향이란 것은 두 엔티티 A, B가 있을 때, A는 B를 참조할 수 있으나 반대로 B는 A를 참조할 수 없는 관계를 의미한다. 두 엔티티 관계를 양방향으로 매핑할 때에는 사실 A에서 B로의 단방향 매핑과 B에서 A로의 단방향 매핑을 해주는 방식으로 한다. 

이러한 방향의 개념 때문에, join할 엔티티들간의 관계를 나타내는 어노테이션에도 다음과 같이 방향에 따라 존재한다. 

- OneToOne : 일대일 관계
- ManyToOne : 다대일 관계
- OneToMany : 일대다 관계
- ManyToMany : 다대다 관계

`@ManyToOne` 어노테이션은 주로 N : 1에서 N의 특성을 가지는 엔티티 클래스 내부에서 사용한다. 반면 `@OneToMany` 어노테이션은 1의 관계에 놓인 엔티티 클래스 내부에서 사용된다. 

코드를 통해 Entity Join에 대해 더 자세히 살펴보겠다. 여기서는 기존에 존재하는 DB 내 테이블들을 이용할 것이다. 필자는 “sql_practice”라는 DB 내에 “site_users”과 “classes” 테이블이 존재하며, 각 필드 설정은 다음과 같이 되어있다. 

![스크린샷 2024-11-07 143012.png](/images/2024-11-07/Spring-Data-Jpa-Entity-Join/1.png)

![스크린샷 2024-11-07 143022.png](/images/2024-11-07/Spring-Data-Jpa-Entity-Join/2.png)

classes 테이블이 site_users의 부모 테이블이 되며, site_users 테이블에는 class_number가 외래키로 설정되어 있다. 위 두 테이블은 classes 테이블이 1, site_users가 n이 되는 일대다 관계를 가지고 있다. 

자바 코드에서 엔티티를 이용하여 위 두 관계를 똑같이 재현하고자 한다. 다음이 그 코드이다. 

```java
package com.jerocaller.model.entity;

import java.util.List;

import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.Id;
import jakarta.persistence.OneToMany;
import lombok.Getter;
import lombok.Setter;

@Entity
@Getter
@Setter
public class Classes {
	@Id
	private Integer classNumber;
	private String className;
	private Integer bonus;
	
	// SiteUsers 클래스에서 fetch type을 어떤 것을 주든 간에 
	// 여기서의 fetch type을 따른다. 
	// 여기서 EAGER로 하면 즉시 로딩으로 인해 조인된 두 테이블의 정보가 
	// 동시에 조회되고, LAZY로 하면 SiteUsers 엔티티의 테이블 정보가 먼저 
	// 조회된 다음에 Classes 데이터가 조회되는 순차적 방식이 된다. 
	@OneToMany(mappedBy = "superEntity", fetch = FetchType.EAGER)
	private List<SiteUsers> users;
}

```

예제 1-1. Classes.java

```java
package com.jerocaller.model.entity;

import jakarta.persistence.Entity;
import jakarta.persistence.FetchType;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import lombok.Getter;
import lombok.Setter;

@Entity
@Getter
@Setter
public class SiteUsers {
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	private Integer memberId;
	private String signUpDate;
	private Integer mileage;
	private String username;
	private Integer averPurchase;
	private String recommBy;
	
	@ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "class_number") // site_users 테이블의 외래키 필드.
	private Classes superEntity;
}

```

예제 1-2. SiteUsers.java

먼저 SiteUsers 클래스를 보겠다. 해당 클래스 내부에는 부모 엔티티인 Classes의 필드가 들어있다. 해당 필드에는 `@ManyToOne` 어노테이션이 적용되어 있는데, SiteUsers 엔티티가 N이고, Classes가 1의 관계임을 명시한다. 이렇게 두 엔티티 간 관계를 명시하는 어노테이션을 사용할 때에는 어노테이션 이름 가운데에 있는 `To`를 기준으로 왼쪽에 있는 관계가 이 어노테이션이 적용될 엔티티라고 보면 된다. 위 예제에서는 SiteUsers 클래스 내부에 `@ManyToOne` 어노테이션이 사용되었으므로 SIteUsers가 “Many”에 해당한다고 표시한 것이나 다름없다. 

`@ManyToOne` 어노테이션과 함께 `@JoinColumn` 어노테이션도 적용되어 있다. 이 어노테이션의 name 속성에는 상대 엔티티의 PK(`@Id`)가 걸린 필드와 연결될 자신 엔티티의 FK가 걸린 필드명을 입력해주면 된다. 앞서 데이터베이스 사진을 보면 알 수 있듯, site_users 테이블의 class_number라는 필드명에 FK가 걸려 있었다. 실제 테이블에서는 두 테이블이 참조 무결성 체크를 위해 참조 관계가 되려면 N의 관계에 놓인 테이블의 외래키와 1의 관계에 놓인 테이블의 기본키가 걸린 필드끼리 연결되어야 한다. 이러한 점을 JPA에서도 그대로 반영한 것이다. 

한 가지 주의할 점은 외래키가 될 필드(SiteUsers의 class_number)를 또 한 번 정의해서는 안된다. 즉, 아래 코드와 같이 구성하면 안된다. 

```java
@Entity
// 그 외 여러 어노테이션들...
public class SiteUsers {
    // 많은 필드들... 생략
    private Integer classNumber;  // X. 실행 시 중복된 필드가 정의되어 있다고 에러가 뜬다. 따라서 @JoinColumn을 통해 외래키로 지정될 필드를 이렇게 정의해선 안된다. 
    
    @ManyToOne(fetch = FetchType.LAZY)
	@JoinColumn(name = "class_number")
	private Classes superEntity;
}
```

예제 1-3. 

한 편, JPA에서는 이렇게 외래키를 가지는 엔티티가 주인(Owner)이 된다. 외래키를 가진 N의 관계를 가지는 자식 엔티티가 보통 주인 엔티티가 된다. 위 예제에서는 SIteUsers 엔티티가 주인이 되는 셈이다. 이러한 주인 엔티티는 외래키를 이용하여 새로운 종속 엔티티 객체를 추가, 수정, 제거할 수 있다. 또한 주인 엔티티를 통해 종속 엔티티의 필드값을 조작할 수 있다. SiteUsers 엔티티 객체를 통해 Classes 종속 엔티티 객체 내 className등의 필드값을 조작할 수 있다는 것이다. 반면 종속 엔티티(Inverse Entity)는 주인 엔티티 객체를 추가, 수정, 제거(INSERT, UPDATE, DELETE 등의 DML 작업)할 수 없으며, 주인 엔티티 객체 내 필드들을 조작할 수 없다. 단순히 읽기 작업만 가능하다. 

현재 위 코드에서는 두 엔티티 클래스는 양방향으로 매핑되어 있는 상태이다. 이는 Classes 클래스 내에 `private List<SiteUsers> users;` 필드와 같이 SiteUsers 엔티티 타입의 필드를 가지고 있고, 이 필드에 `@OneToMany` 어노테이션을 걸었기 때문이다. 만약 SiteUsers를 기준으로 단방향 매핑을 하고자 한다면 Classes 클래스 내에 SiteUsers를 지칭하는 필드도, `@OneToMany` 어노테이션도 적용되지 말아야 한다. 즉, 다음과 같이 되어야 단방향 매핑이라 할 수 있다.

```java
@Entity
@Getter
@Setter
public class Classes {
	@Id
	private Integer classNumber;
	private String className;
	private Integer bonus;
	
	// SiteUsers 객체 타입의 필드 선언도, `@OneToMany` 어노테이션도 없다. 
	// 그러면 SiteUsers에서만 `@ManyToOne`, `@JoinColumn` 과 더불어 
	// Classes 객체 타입 필드가 선언되어 있는 상태이므로 
	// SiteUsers에서 Classes 방향으로 매핑되어 있는 단방향 매핑 관계가 성립된다. 
}
```

예제 1-4.

한 편, `@OneToMany` 어노테이션만 걸면 양뱡향 매핑이 되기는 하지만 두 엔티티 클래스는 중 누가 주인인지를 스프링은 제대로 판단하지 못한다고 한다. 따라서 해당 어노테이션을 걸 때 `mappedBy` 속성도 같이 명시해준다. 이 속성을 명시하면 해당 엔티티에는 외래키가 생성되지 않는다. 외래키를 가지는 N의 관계를 가지는 엔티티가 주로 주인 엔티티가 되므로 mappedBy 속성을 명시하여 해당 엔티티는 주인 엔티티가 아님을 명확하게 표시해주는 것이다. 그리고 참조 관계의 두 테이블에서도 외래키를 가진 테이블은 단 하나뿐인데, 이를 반영해서 엔티티 간 연관 관계에 있어서도 한쪽에만 외래키를 주는 것이다. (mappedBy 속성을 명시하지 않으면 해당 엔티티에서도 외래키가 생성된다고 한다) mappedBy 속성값에는 상대 엔티티 클래스 내 자신 엔티티의 타입으로 선언된 필드명을 입력하면 된다. 위 예제에서는 `private Classes superEntity;` 라고 되어 있기에 `mappedBy = superEntity` 라고 명시하면 되겠다. 

일의 관계에 있는 Classes는 여러 개의 SiteUsers를 가질 수 있으므로 필드 타입이 단순히 SiteUsers가 아닌 `List<SiteUsers>` 로 선언되었다. 

# fetch type - LAZY vs EAGER

위 예제 코드들을 자세히 살펴보면 N의 관계를 가지는 주인 엔티티 SiteUsers 내에서는 `@ManyToOne` 어노테이션 속성에 `fetch = FetchType.LAZY` 가 설정되어 있고, 종속 엔티티인 Classes에서도 `fetch = FetchType.EAGER` 라고 설정되어 있다. 이 설정은 서로 조인되어 있는 두 엔티티 중 한 쪽의 엔티티가 다른 쪽 엔티티를 참조할 때 두 엔티티의 데이터를 동시에 가져올 것인지 아니면 참조된 엔티티는 참조될 때에만 데이터를 가져올 것인지를 정하는 속성이다. 이 때 두 엔티티의 데이터를 동시에 가져오는 것을 “즉시 로딩”, 참조될 엔티티는 참조될 시점에서만 로딩하는 것을 “지연 로딩”이라 한다. 즉시 로딩을 하고자 한다면 `EAGER` 를, 지연 로딩을 하고자 한다면 `LAZY` 로 설정하면 된다. 

필자는 위 예제 코드를 작성하면서 “클래스 등급명이 포함된 모든 사이트 유저 정보 조회” 코드를 작성하였다. 다음은 그 결과물이다. 

![스크린샷 2024-11-08 092536.png](/images/2024-11-07/Spring-Data-Jpa-Entity-Join/3.png)

위 사진에서 “등급” 필드는 SiteUsers 엔티티의 외래키인 class_number 필드를 이용하여 Classes 엔티티의 class_name 필드를 참조하여 얻은 것이다. 이 과정에서 SiteUsers 엔티티에는 이미 Classes 엔티티를 필드로 가지고 있으므로 자바에서는 다음의 코드를 이용하여 SiteUsers로부터 연관된 Classes의 className을 가져올 수 있다. 

```java
// 의사 코드
SiteUsers user = siteUsersRepository.findById(memberId);
user.getSuperEntity().getClassName();  // SiteUsers 엔티티의 Classes 객체 타입 참조 변수인 
// superEntity 필드를 참조하여 Classes 엔티티 내 className 필드에 접근. 
```

참고로 위와 같이, 두 엔티티 간에는 단방향이든 양방향이든 이미 매핑이 되어 있기에 하나의 엔티티로부터 다른 엔티티의 정보를 가져올 수 있다. 이로 인해 굳이 JPQL에서 JOIN문을 사용할 필요가 없게 된다. 

즉, 지금은 SiteUsers 엔티티를 먼저 가져온 다음, 해당 엔티티를 이용하여 Classes 엔티티의 특정 필드를 참조하는 순서임을 기억하라. (SiteUsers → Classes)

필자도 위와 같은 화면을 출력하기 위해 다음과 같이 thymeleaf를 이용하여 html 문서를 구성하였다. 

```html
<!DOCTYPE html>
<html xmlns:th="https://www.thymeleaf.org">
<head>
<meta charset="UTF-8">
<title>Insert title here</title>
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-QWTKZyjpPEjISv5WaRU9OFeRpok6YctnYmDr5pNlyT2bRjXh0JMhjY6hW+ALEwIH" crossorigin="anonymous">
<script src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.3/dist/js/bootstrap.bundle.min.js" integrity="sha384-YvpcrYf0tY3lHB60NNkmXc5s9fDVZLESaAA55NDzOxhy9GkcIdslK1eN7N6jIeHz" crossorigin="anonymous"></script>
<link rel="stylesheet" href="/css/style.css" />
</head>
<body>
	<h2>모든 사이트 유저 조회</h2>
	<p><a th:href="@{/}">메인으로</a></p>
	<table class="table table-hover table-sm">
		<thead class="table-info">
			<tr>
				<th>회원번호</th>
				<th>유저네임</th>
				<!--  <th>클래스 넘버</th> -->
				<th>등급</th>
				<th>가입일</th>
				<th>보유 마일리지</th>
				<th>평균구매액</th>
				<th>추천인</th>
			</tr>
		</thead>
		<tbody class="table-light">
			<th:block th:if="${users != null && users.size > 0}">
				<tr th:each="user:${users}">
					<td th:text="${user.memberId}"></td>
					<td th:text="${user.username}"></td>
					<!-- <td th:text="${user.classNumber}"></td>  -->
					<td th:text="${user.superEntity.className}"></td>
					<td th:text="${user.signUpDate}"></td>
					<td th:text="${user.mileage}"></td>
					<td th:text="${user.averPurchase}"></td>
					<td th:text="${user.recommBy}"></td>
				</tr>
				<tr>
					<td colspan="7" th:text="|총 회원 수: ${users.size}명|"></td>
				</tr>
			</th:block>
			<th:block th:unless="${users != null && users.size > 0}">
				<tr>
					<td colspan="7">조회 결과 없음.</td>
				</tr>
			</th:block>
		</tbody>
	</table>
</body>
</html>
```

예제 2-1. userList.html 

users는 컨트롤러로부터 넘어오는 `List<SiteUsers>` 데이터이다. `<td th:text="${user.superEntity.className}"></td>` 이 구문을 통해 SiteUsers 엔티티 객체 내 Classes 객체를 참조하여 해당 엔티티 내 className이라는 필드를 참조하고 있음을 알 수 있다. 

이제 Classes 엔티티 클래스에서 `@OneToMany(mappedBy = "superEntity", fetch = FetchType.EAGER)` 를 통해 fetch type을 EAGER로 설정하였다. 그 후 회원 목록을 출력하는 userList.html 페이지 요청을 한 후 이클립스 내 콘솔창을 보면 다음과 같이 hibernate에서 내부적으로 생성한 SQL문을 볼 수 있다.

```html
Hibernate: 
    select
        c1_0.class_number,
        c1_0.bonus,
        c1_0.class_name,
        u1_0.class_number,
        u1_0.member_id,
        u1_0.aver_purchase,
        u1_0.mileage,
        u1_0.recomm_by,
        u1_0.sign_up_date,
        u1_0.username 
    from
        classes c1_0 
    left join
        site_users u1_0 
            on c1_0.class_number=u1_0.class_number 
    where
        c1_0.class_number=?
```

Classes 엔티티 객체의 fetch type을 EAGER로 했을 때 Hibernate 내부적으로 생성된 SQL문. 

위 쿼리문을 자세히 보면 알겠지만, Classes와 SIteUsers 두 테이블 모두의 정보를 SELECT하여 가져오는 것을 알 수 있다. EAGER로 설정하면 두 테이블의 정보를 동시에 가져온다는 증거이다. 

이번에는 Classes 엔티티 객체 내 fetch type을 LAZY로 하고 똑같이 실행하여 내부적으로 생성된 쿼리문을 살펴보겠다. 그러면 다음의 쿼리문을 얻게된다. 

```java
    /* <criteria> */ select
        su1_0.member_id,
        su1_0.aver_purchase,
        su1_0.mileage,
        su1_0.recomm_by,
        su1_0.sign_up_date,
        su1_0.class_number,
        su1_0.username 
    from
        site_users su1_0
Hibernate: 
    /* <criteria> */ select
        su1_0.member_id,
        su1_0.aver_purchase,
        su1_0.mileage,
        su1_0.recomm_by,
        su1_0.sign_up_date,
        su1_0.class_number,
        su1_0.username 
    from
        site_users su1_0
[2m2024-11-08T12:25:30.726+09:00[0;39m [32mDEBUG[0;39m [35m19712[0;39m [2m---[0;39m [2m[EntityJoin] [nio-8080-exec-3][0;39m [2m[0;39m[36morg.hibernate.SQL                       [0;39m [2m:[0;39m 
    select
        c1_0.class_number,
        c1_0.bonus,
        c1_0.class_name 
    from
        classes c1_0 
    where
        c1_0.class_number=?
Hibernate: 
    select
        c1_0.class_number,
        c1_0.bonus,
        c1_0.class_name 
    from
        classes c1_0 
    where
        c1_0.class_number=?
```

Classes 엔티티 객체의 fetch type을 LAZY로 했을 때 Hibernate 내부적으로 생성된 SQL문. 출력 결과 중 일부만 가져왔다.

위 쿼리문들을 자세히 보면 알겠지만, 한 번 SELECT문이 사용될 때 하나의 테이블에서만 정보를 추출하는 것을 알 수 있다. 즉, 하나의 엔티티를 참조한 후, 연결되어 있는 엔티티를 이후에 참조하는 순차적 방식을 따른다는 것을 알 수 있다. 

한 편, `@OneToMany` 어노테이션에서의 fetch 속성의 기본값은 LAZY 이고 `@ManyToOne` 어노테이션에서는 기본값이 EAGER이다. 

# 나중에 더 살펴볼 주제들

- 1 : N 의 관계를 가지는 두 엔티티에 대해 왜 하필 1도 아니고 N의 관계를 가지는 엔티티를 “주인 엔티티”로 설정되어야 하는지에 대해 나중에 더 살펴봐야겠다.

---

References

[1] 에이콘아카데미(강남) 강의

[2] 박영권 - “데이터 과학자 + 프로그래머 세상” 

[Daum 카페](https://cafe.daum.net/flowlife)

[3] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)