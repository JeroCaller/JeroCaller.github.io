---
title: "[Forklog][Trouble Shooting] Page 객체 응답 직렬화 이슈"
category: "Team Project"
tag: ["Team Project", "Forklog", "Trouble Shooting", "Page"]
---


```java
Serializing PageImpl instances as-is is not supported, meaning that there is no guarantee about the stability of the resulting JSON structure!
	For a stable JSON structure, please use Spring Data's PagedModel (globally via @EnableSpringDataWebSupport(pageSerializationMode = VIA_DTO))
	or Spring HATEOAS and Spring Data's PagedResourcesAssembler as documented in https://docs.spring.io/spring-data/commons/reference/repositories/core-extensions.html#core.web.pageables.
```

Page 객체를 클라이언트에 json으로 전송하면 잘 전송되지만 이클립스 콘솔에서 위와 같은 경고 메시지가 뜬다. 더군다나 다음과 같이 json 객체 구조가 의도치 않게 복잡해지는 이슈도 있다.

```java
{
  "status": "OK",
  "message": "데이터 조회 성공",
  "data": {
    "startPage": 1,
    "endPage": 2,
    "currentPage": 1,
    "pageRange": [
      1,
      2
    ],
    "pages": {
      "content": [
        {
          "reservationNo": 196,
          "reservationDate": "2024-10-24",
          "reservationTime": "09:00:00",
          "memberName": "test",
          "customerName": "김예담",
          "serviceName": "커트"
        },
        {
          "reservationNo": 197,
          "reservationDate": "2024-10-24",
          "reservationTime": "10:00:00",
          "memberName": "박지영",
          "customerName": "오정훈",
          "serviceName": "파마"
        },
        {
          "reservationNo": 198,
          "reservationDate": "2024-10-24",
          "reservationTime": "11:00:00",
          "memberName": "이호",
          "customerName": "정서린",
          "serviceName": "파마,염색"
        }
      ],
      "pageable": {
        "pageNumber": 0,
        "pageSize": 3,
        "sort": {
          "empty": true,
          "unsorted": true,
          "sorted": false
        },
        "offset": 0,
        "unpaged": false,
        "paged": true
      },
      "last": false,
      "totalPages": 2,
      "totalElements": 6,
      "size": 3,
      "number": 0,
      "sort": {
        "empty": true,
        "unsorted": true,
        "sorted": false
      },
      "first": true,
      "numberOfElements": 3,
      "empty": false
    }
  }
}
```

“pages”의 내부인 “content”등이 Page 객체에 의해 생성된 프로퍼티들이다. 실제 데이터인 “content” 영역을 제외하면 온갖 복잡한 구성의 프로퍼티들이 의도치 않게 추가된다. 

이 이유는 Page 객체를 json으로 전송하기 위해 중간에 직렬화할 때 이슈가 있기 때문이라고 한다. 따라서 스프링 버전 3.3 이후부터 이 이슈를 해결하기 위해 PagedModel 클래스 또는 `@EnableSpringDataWebSupport(pageSerializationMode = PageSerializationMode.VIA_DTO)` 을 제공하고 있다고 한다. 전자의 경우 특정 메서드에만 선별적으로 적용할 수 있는 것으로 보이며, 후자의 경우 웹 애플리케이션 전체에 걸쳐 적용할 수 있다고 한다. 

어노테이션 사용 방법의 경우, Application.java로 이름이 끝나는 클래스에 적용하면 된다. 

```java
package com.erp;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.web.config.EnableSpringDataWebSupport;
import org.springframework.data.web.config.EnableSpringDataWebSupport.PageSerializationMode;

/**
 * 스프링 버전 3.3부터 Page 객체를 JSON으로 응답 시 직렬화 이슈가 있다고 한다. 
 * 게다가, Page 객체를 그대로 응답 시 JSON 응답 구조가 복잡해지며, 
 * 콘솔창에서도 이와 관련된 경고 메시지가 출력된다. 
 * 따라서 아래 어노테이션을 다음 속성과 더불어 Application.java에 등록하면...
 * @EnableSpringDataWebSupport(pageSerializationMode = PageSerializationMode.VIA_DTO)
 * 
 * 개발자가 만든 DTO를 기준으로 직렬화가 진행된다. 
 * 그러면 JSON 응답 구조도 간결해지며, 해당 경고도 나타나지 않는다. 
 */
@SpringBootApplication
@EnableSpringDataWebSupport(pageSerializationMode = PageSerializationMode.VIA_DTO)
public class AcornProjectErpApplication {

	public static void main(String[] args) {
		SpringApplication.run(AcornProjectErpApplication.class, args);
	}

}
```

그러면 더 이상 관련 경고창이 뜨지 않으며, JSON 응답 구조도 다음과 같이 간결해진다. 

```java
{
  "status": "OK",
  "message": "데이터 조회 성공",
  "data": {
    "startPage": 1,
    "endPage": 3,
    "currentPage": 2,
    "pageRange": [
      1,
      2,
      3
    ],
    "pages": {
      "content": [
        {
          "reservationNo": 197,
          "reservationDate": "2024-10-24",
          "reservationTime": "10:00:00",
          "memberName": "박지영",
          "customerName": "오정훈",
          "serviceName": "파마"
        }
      ],
      "page": {
        "size": 1,
        "number": 1,
        "totalElements": 6,
        "totalPages": 6
      }
    }
  }
}
```

---

References

[1] [[Spring] [Memo] 메서드, 변수 명 선택, 중복된 @Transactional 처리, Pagenation 시 추가 데이터 압축 등 추가 Tips](https://hanstory33.tistory.com/194)

[2] [[Spring] 스프링 3.3에서 추가된 `pageSerializationMode = VIA_DTO`](https://nhahan.tistory.com/153)

[3] [Pagination in a Spring Boot REST API](https://bootify.io/spring-boot/pagination-in-spring-boot-rest-api.html)

[4] [EnableSpringDataWebSupport (Spring Data Core 3.4.2 API)](https://docs.spring.io/spring-data/commons/docs/current/api/org/springframework/data/web/config/EnableSpringDataWebSupport.html)