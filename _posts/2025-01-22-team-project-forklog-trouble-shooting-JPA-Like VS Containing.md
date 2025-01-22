---
title: "[Forklog][Trouble Shooting][JPA] Like VS Containg"
category: "Team Project"
tag: ["Team Project", "Forklog", "Trouble Shooting", "Spring Data JPA", "JPA"]
---

실제 문서 작성일: 2024년 12월 29일

```java
package com.acorn.repository;

import java.math.BigDecimal;

import org.springframework.data.domain.Page;
import org.springframework.data.domain.Pageable;
import org.springframework.data.jpa.repository.JpaRepository;

import com.acorn.entity.Categories;
import com.acorn.entity.Eateries;

public interface EateriesRepository extends JpaRepository<Eateries, Integer> {
	
	boolean existsByNameAndLongitudeAndLatitude(String name, BigDecimal longitude, BigDecimal latitude);
	
	Page<Eateries> findByAddressContaining(String address, Pageable pageRequest);
	
	Page<Eateries> findByAddressLikeAndCategory(
			String address, 
			Categories categories,
			Pageable pageRequest
	);
}

```

findByAddressLikeAndCategory라 정의하였고, DB 내 정보들을 참조하여 주소와 카테고리 검색 조건에 따른 조회를 시도하였다. 분명 데이터가 나와야 했는데 아무것도 조회되지 않았다. 이 때 주소 입력값을 “서울”로 주었다. 현재에서는 “서울 강남구”, “서울 서초구”와 같이 DB에 존재하고, “서울”만 존재하는 값은 없었다. 

containing은 `%:param%` 과 같이 `%` 와일드 카드 문자가 입력값 주위를 감싸지만, like는 그렇지 않기에 발생한 문제였다. `findByAddressContainingAndCategory` 로 명칭을 변경하니 예상대로 데이터가 조회된다. 

LIKE에 `%` 와 같은 와일드 카드를 전혀 사용하지 않는다면 이는 `=` equal의 뜻을 가지고 있다.