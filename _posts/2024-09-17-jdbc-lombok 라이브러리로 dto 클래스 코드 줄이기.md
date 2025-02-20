---
title: "[JDBC] Lombok 라이브러리로 DTO 클래스 코드 줄이기"
category: "Servlet & JSP"
tag: ["JDBC", "Lombok", "DTO"]
---

DTO(Data Transfer Object) 클래스 작성 시 DB의 필드에 해당하는 멤버 변수들을 선언하고 나면 각각의 필드들에 대한 getter, setter 메서드도 작성해야 한다. 이걸 일일이 작성하는 것은 번거롭다. 설령 이클립스에서 제공하는 자동으로 getter, setter 메서드를 생성해주는 기능을 사용하더라도, 만약 DB 구조가 바뀐다든지 하는 이유로 새로운 필드가 추가하거나 기존 필드를 삭제 또는 수정해야 하는 경우 이에 대한 setter, getter 메서드를 일일이 찾아 수정해야한다. 그런데 Lombok이라는 외부 라이브러리를 이용하면 개발자는 그저 DB 필드에 해당하는 변수들만 선언하면 되고, setter, getter 메서드는 전혀 정의하지 않아도 된다. 컴파일 과정에서 Lombok이 자동으로 클래스 내 필드명에 해당하는 getter, setter 메서드를 생성해주기 때문이다. 

# Lombok 설치 과정

1. 구글에 “lombok”이라 치면 다음과 같이 검색 결과가 뜬다. 
    
    ![1.PNG](/images/2024-09-17/jdbc-lombok%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A1%9C%20dto%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%BD%94%EB%93%9C%20%EC%A4%84%EC%9D%B4%EA%B8%B0/1.png)
    

참고용으로 해당 사이트의 주소를 남긴다. 

[Project Lombok](https://projectlombok.org/)

1. 다음과 같은 첫 화면이 보일텐데, 여기서 화면 위 중앙에 있는 [Download]를 누른다. 
    
    ![2.PNG](/images/2024-09-17/jdbc-lombok%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A1%9C%20dto%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%BD%94%EB%93%9C%20%EC%A4%84%EC%9D%B4%EA%B8%B0/2.png)
    
    ![3.PNG](/images/2024-09-17/jdbc-lombok%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A1%9C%20dto%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%BD%94%EB%93%9C%20%EC%A4%84%EC%9D%B4%EA%B8%B0/3.png)
    

1. 자신의 컴퓨터에 lombok.jar 파일을 다운로드 받았을 것이다. 우선 이 파일을 적절한 곳에 위치시킨다. 필자의 경우 `C:\java_libs` 경로 바로 아래에 위치시켰다. 
2. 만약 실행중인 이클립스가 있다면 이를 종료한다. 그 후 명령어 창 (cmd)을 켜서 lombok.jar 파일이 존재하는 경로로 이동한다. 그 후 `java -jar lombok.jar` 명령어를 입력한다. 만약 이 명령이 실행되지 않는다면 JAVA_HOME을 path에 추가하지 않았는지 확인해본다. 만약 성공했다면 다음처럼 새로운 창이 뜰 것이다. 
    
    ![4.PNG](/images/2024-09-17/jdbc-lombok%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A1%9C%20dto%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%BD%94%EB%93%9C%20%EC%A4%84%EC%9D%B4%EA%B8%B0/4.png)
    

“OK” 버튼을 누른다.

1. [Specify location]을 누른다. 
    
    ![5.PNG](/images/2024-09-17/jdbc-lombok%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A1%9C%20dto%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%BD%94%EB%93%9C%20%EC%A4%84%EC%9D%B4%EA%B8%B0/5.png)
    
    그러면 다음의 창이 뜰 것이다. 여기서 이클립스 실행 파일인 `eclipse.exe` 를 선택한다. 
    
    ![6.PNG](/images/2024-09-17/jdbc-lombok%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A1%9C%20dto%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%BD%94%EB%93%9C%20%EC%A4%84%EC%9D%B4%EA%B8%B0/6.png)
    
    선택 완료 시 [Select] 버튼 클릭. 그러면 아래 사진처럼 이전 화면으로 돌아올 것이다. “IDEs” 목록에 아까 선택했던 이클립스 실행 파일이 존재할 것이다. 오른쪽의 [Install/Update] 버튼을 클릭한다. 그 후 [Quit Installer] 버튼 클릭. 
    
    ![7.PNG](/images/2024-09-17/jdbc-lombok%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%EB%A1%9C%20dto%20%ED%81%B4%EB%9E%98%EC%8A%A4%20%EC%BD%94%EB%93%9C%20%EC%A4%84%EC%9D%B4%EA%B8%B0/7.png)
    
2. 다시 이클립스를 킨다. WEB-INF/lib 폴더에 앞서 다운로드한 lombok.jar를 복사, 붙여 넣었다. 해당 jar 파일을 프로젝트 폴더에 등록하는 것도 잊지말자. 해당 과정은 프로젝트 폴더명 우클릭 → [Build Path] → [Configure Build Path] 클릭 후 나오는 새 창에서, 왼쪽 목록의 [Java Build Path] 클릭 후 가운데 화면에서 [Libraries] 탭 클릭 후, [Classpath] 선택 후 오른쪽의 [Add JARs]를 클릭하여 앞서 WEB-INF/lib 폴더에 추가한 lombok.jar를 등록하면 된다. 

위와 같은 과정을 거치면 lombok 등록을 완료한 것이고, 사용 가능한 상태인 것이다. 이제 이 라이브러리를 이용해보자.

# Lombok 사용법

이제 lombok을 사용해보자. lombok 사용이 잘되는지 확인하기 위해 다음과 같이 임의로 Dto 클래스를 생성하였다. 

```java
package com.festival.db;

import java.sql.Date;

import lombok.Data;

@Data
public class FestDto {
	private int id;
	private String title = null;
	private String origin = null;
	private String content = null;
	private String image = null;
	private Date startDate = null;
	private Date endDate = null;
}

```

com.festival.db.FestDto.java

lombok 라이브러리에는 다양한 어노테이션을 제공해준다. 

| import | Annotation | 기능 |
| --- | --- | --- |
| lombok.NoArgsConstructor | NoArgsConstructor | 기본 생성자 추가. |
| lombok.AllArgsConstructor | AllArgsConstructor | 모든 멤버 변수 초기화가 가능한 생성자 추가. |
| lombok.Getter | Getter | Getter 메서드 추가. |
| lombok.Setter | Setter | Setter 메서드 추가. |
| lombok.ToString | ToString | ToString 메서드 추가. |
| lombok.EqualsAndHashCode | EqualsAndHashCode | equals(), hashCode() 메서드 추가. |
| lombok.Data | Data | AllArgsConstructor, Getter, Setter, ToString, EqualsAndHashCode 어노테이션 모두 포함. |

여기서는 간단하게 모든 어노테이션을 포함하는 @Data만 사용하였다. 

이제 webapp 폴더 바로 아래에 dtoTest.jsp 파일을 생성하고 다음의 코드를 작성하여보았다.

```java
<%@ page contentType="text/html; charset=UTF-8"%>
<%@ page import="com.festival.db.FestDto" %>

<%
	FestDto festDto = new FestDto();
	festDto.setId(12);
	festDto.setContent("wow!");
	
	System.out.println(festDto);
	System.out.println(festDto.getId() + ", " + festDto.getContent());
%>
```

dtoTest.jsp

이제 dtoTest.jsp를 톰캣 서버로 구동시키면 console 창에 다음의 메시지를 볼 수 있을 것이다. 

```java
FestDto(id=12, title=null, origin=null, content=wow!, image=null, startDate=null, endDate=null)
12, wow!
```

이를 통해 개발자가 직접 getter, setter 메서드를 작성하지 않아도 lombok에서 자동으로 추가해준다는 것을 알 수 있다. 이를 통해 우린 이제 일일이 getter, setter 메서드를 작성하지 않아도 된다는 장점을 챙길 수 있게 되었다. 

---

References

[1] 채규태, “채쌤의 Servlet&JSP 프로그래밍 핵심”, (쌤즈, 2023)