---
title: "[Spring Boot] 파일 업로드 및 다운로드 기능 구현하기"
category: "Spring"
tag: ["Spring", "Spring Boot", "파일 업로드 및 다운로드", "업로드", "다운로드", "react"]
---

이번 글에서는 스프링 부트를 이용하여 클라이언트가 요청한 파일을 서버에 업로드하는 기능과 이를 클라이언트에서 다운로드하는 기능을 구현하는 방법에 대해 소개하겠다. 

먼저 필자가 구현한 예제 사이트 시연 모습이다.

<div style="text-align:center; margin:1em;">
<video src="/resources/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/1.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

영상 1-1. 회원가입 및 로그인 시연 영상


<div style="text-align:center; margin:1em;">
<video src="/resources/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/2.mp4"
    controls="controles"
    muted="muted"
    width="70%"
></video>
</div>

영상 1-2. 로그인 후 파일 업로드 및 다운로드 시연 영상

파일 업로드 및 다운로드 기능과 더불어 이를 회원 개개인이 각자 사용할 수 있도록 회원 기능도 도입해보았다. 여기서는 파일 업로드와 다운로드 기능에 대해 자세히 살펴본 다음, 회원 정보 수정 또는 삭제 시 해당 파일들의 경로를 수정하거나 삭제하는 방법에 대해서도 살펴보겠다. 

이 예제 사이트를 구현하기 위해 사용한 기술들은 다음과 같다.

- BE
    - Spring Boot (v.3.4.1)
    - Gradle
    - Swagger
    - Spring Security (session 기반 인증 방식)
    - Spring Data JPA
    - REST API
- FE
    - React
    - Redux
    - react-router-dom
    - Axios
- DB : MariaDB

즉, 필자는 백엔드와 프론트엔드로 나눠 구현하였으며, 백엔드에서는 스프링부트, 프론트엔드에서는 리액트로 구현하였다. 각각의 소스 코드는 다음의 사이트들을 참고.

BE) 

[spring-study/FileUploadDownloadStudy at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/FileUploadDownloadStudy)

FE) 

[react-study/file-upload-study at master · JeroCaller/react-study](https://github.com/JeroCaller/react-study/tree/master/file-upload-study)

# 파일 업로드

파일을 업로드할 때 이 파일을 저장하는 방법에는 크게 두 가지가 있다고 한다. 

- 파일은 디렉토리에 따로 보관하고, 이 파일 정보는 DB에 저장.
    - 파일을 저장하고 있는 디렉토리와 DB 이 두 가지가 필요하며, 따라서 이 둘의 연동 작업이 중요하다. DB 상에서만 파일 정보를 삭제한다고 해서 디렉토리 상에서도 파일이 삭제되진 않기 때문이다.
- 파일 자체를 DB에 저장.
    - 파일의 용량이 크면 그만큼 DB 용량을 크게 차지한다고 한다. 이로 인해 대용량의 파일 업로드 시에는 추천되지 않는다고 한다.

필자는 전자가 더 일반적이고, 상대적으로 조금 더 큰 용량의 파일도 저장할 수 있다고 판단하여 전자를 택하였다. 

## 사전 작업

파일 업로드 기능을 위해서는 다음과 같이 application.properties 파일에 몇가지 설정을 해야 한다.

```java
# 파일 업로드 관련 설정 ========
# 업로드 파일 저장 경로 설정
file.upload-dir=./files/users

# 스프링에서 파일을 다룰 수 있도록 허용
spring.servlet.multipart.enabled=true

# 한 파일의 최대 허용 용량
spring.servlet.multipart.max-file-size=5MB

# 한 파일의 최대 요청 허용 용량
spring.servlet.multipart.max-request-size=5MB
```

application.properties

먼저, 업로드된 파일들을 저장할 디렉토리 경로를 `file.upload-dir` 속성을 통해 설정한다. 여기서는 `./flies/users` 경로로 설정하였으며, 이는 스프링 부트가 실행되는 현재 폴더에서 files/users 라는 경로의 폴더에 모든 업로드되는 파일들을 저장할 루트 디렉토리가 된다. 

그 다음 Entity 클래스들을 정의하였다. 여기서는 회원 기능도 도입했기에 회원 Entity도 추가되었다. 

```java
package com.jerocaller.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.Id;
import jakarta.persistence.Table;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.EqualsAndHashCode;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Entity
@Table(name = "members")
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@ToString(callSuper = true)  // 부모 엔티티의 필드들까지 엔티티 객체 비교 대상 추가.
@EqualsAndHashCode(callSuper = false)
public class Members extends BaseEntity {
	
	@Id
	@Column(nullable = false, length = 20)
	private String nickname;
	
	@Column(nullable = false, length = 100)
	private String password;
	
}

```

코드 1-1. Members.java

```java
package com.jerocaller.entity;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.JoinColumn;
import jakarta.persistence.ManyToOne;
import jakarta.persistence.Table;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

@Entity
@Table(name = "files")
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class FileEntity extends BaseEntity {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(nullable = false, length = 11)
	private Integer id;
	
	// 서버에 실질적으로 저장되는 파일의 경로 
	@Column(name = "path", nullable = false, length = 500)
	private String filePath;
	
	@ManyToOne
	@JoinColumn(name = "member", nullable = false)
	private Members members;
	
}

```

코드 1-2. FileEntity.java

참고로 DB 설계는 다음 사진과 같다. 

![사진 1-1. ERD](/images/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/1.png)

사진 1-1. ERD

### JPA Auditing

위 두 Entity 클래스는 공통적으로 BaseEntity라는 클래스를 상속받는데, 해당 클래스 코드는 다음과 같다.

```java
package com.jerocaller.entity;

import java.time.LocalDateTime;

import org.springframework.data.annotation.CreatedDate;
import org.springframework.data.annotation.LastModifiedDate;
import org.springframework.data.jpa.domain.support.AuditingEntityListener;

import jakarta.persistence.Column;
import jakarta.persistence.EntityListeners;
import jakarta.persistence.MappedSuperclass;
import lombok.Getter;

/**
 * 여러 엔티티에서 공통적으로 나타나는 필드들을 하나의 부모 엔티티에서 
 * 공통적으로 다루고자 함.
 * 
 * 여기서는 새로운 어노테이션들이 등장하는데, 이를 사용하기 위해선 먼저 
 * @EnableJpaAuditing 어노테이션이 적용된 config 클래스를 먼저 정의해야 한다. 
 * 
 * (참고로 "누가" 엔티티 생성 및 수정했는지를 알고자 한다면 @CreatedBy, @ModifiedBy 
 * 어노테이션을 사용할 수 있도록 하는 AuditorAware를 Spring Bean으로 등록해야한다고 함. 
 * 여기선 생략)
 * 
 * 새 어노테이션 소개)
 * @EntityListeners - 엔티티를 DB에 적용하기 전후에 콜백 요청을 위한 어노테이션.
 * 여기서는 AuditingEntityListener 클래스를 대입하였는데, 이 클래스는 
 * 엔티티의 Auditing(감시 - 여기서는 누가 언제 데이터 생성 및 변경했는지를 감시) 정보를 
 * 주입하는 JPA 엔티티 리스너 클래스이다. 
 * 
 * @MappedSuperClass - 자식 엔티티가 이 엔티티 클래스를 상속받을 경우, 자식 클래스에게 
 * 부모 엔티티의 매핑 정보를 전달한다. 여기서는 안에 정의한 필드 및 Getter 등 클래스 수준에 
 * 적용된 모든 어노테이션을 의미하는 것 같다.
 * 
 * @CreatedDate - 데이터 생성 날짜를 자동으로 주입하는 어노테이션.
 * 
 * @LastModifedDate - 데이터 수정 날짜를 자동으로 주입하는 어노테이션.
 */
@EntityListeners(AuditingEntityListener.class)
@MappedSuperclass
@Getter
public class BaseEntity {
	
	@CreatedDate
	@Column(updatable = false)
	private LocalDateTime createdAt;
	
	@LastModifiedDate
	private LocalDateTime updatedAt;
	
}

```

코드 1-3. BaseEntity.java

BaseEntity는 Members와 FileEntity 이 두 클래스에서 공통적으로 나타나는 생성 일시 및 수정 일시를 별도로 분리하여 모은 클래스이다. 위 코드에는 새로운 어노테이션들이 보이는데, 주석으로 각각 설명해두었으니 위 코드 1-3의 주석을 참고하면 되겠다. 

이 BaseEntity를 이용하면 데이터 삽입 및 수정 시 자동으로 생성 일시와 수정 일시가 DB 내 해당 필드에 기록된다. Spring Data JPA에서는 이와 같이 데이터의 생성 및 변경을 감시(Audit)하는 기능인 JPA Auditing을 제공한다. 이 기능을 이용하려면 다음과 같이 별도의 설정 클래스를 정의해야 한다. 

```java
package com.jerocaller.config;

import org.springframework.context.annotation.Configuration;
import org.springframework.data.jpa.repository.config.EnableJpaAuditing;

/**
 * Jpa Audit 기능을 위한 설정 클래스.
 */
@Configuration
@EnableJpaAuditing
public class JpaAuditConfig {

}

```

코드 1-4. JpaAuditConfig.java

클래스 내부에 아무런 코드를 작성하지 않아도 되며, 오로지 `@EnableJpaAuditing` 만 적용하면 된다. 

참고로 JPA Auditing 기능에는 데이터 생성 및 수정 일시뿐만 아니라 생성 및 수정 주체, 즉 누가 Entity를 생성 또는 변경했는지까지 알 수 있다고 한다. 이는 각각 `@CreatedBy` 와 `@ModifiedBy` 어노테이션을 이용하면 된다고 하는데, 이 기능을 사용하려면 AuditorAware를 Spring Bean으로 등록할 필요가 있다고 한다[12]. 여기서는 이 기능이 필요하지 않아 추가하지 않았다. 

## API Endpoint 제작

필자는 백엔드에서는 REST API로 제작하므로 다음과 같이 파일 업로드를 위한 컨트롤러 메서드를 작성하였다. 

```java
package com.jerocaller.controller;

import java.util.List;
import java.util.Map;

import org.springframework.http.HttpStatus;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.DeleteMapping;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RequestPart;
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.multipart.MultipartFile;

import com.jerocaller.common.ResponseMessages;
import com.jerocaller.dto.FileResponse;
import com.jerocaller.dto.FileSuccessFailed;
import com.jerocaller.dto.ResponseJson;
import com.jerocaller.exception.NotAuthenticatedUserException;
import com.jerocaller.service.FileServiceInterface;
import com.jerocaller.service.FileServiceTypeOneImpl;
import com.jerocaller.util.AuthenticationUtils;

import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;

/**
 * 좀 더 엄격하게 하고자 한다면 A 사용자가 B 사용자의 파일을 
 * 열람, 다운로드하거나 새 파일 삽입하지 않도록 방지하는 기능도 추가할 수 있음. 
 */
@RestController
@RequestMapping("/files")
@RequiredArgsConstructor
public class FileController {
	
	private final FileServiceInterface<FileServiceTypeOneImpl> fileService;
	
	/**
	 * 클라이언트로부터 여러 개의 파일 업로드 요청 시 이를 처리한다. 
	 * 
	 * 프론트엔드로부터 요청이 들어오면 \@RequestParam 파라미터로 받아도 
	 * 파일을 업로드할 수 있는 것으로 알고 있었으나, Swagger에서 테스트할 때에는 
	 * \@PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE) 및
	 * \@RequestPart(name = "files") MultipartFile[] files 
	 * 와 같이 consumes 설정과 @RequestPart를 사용해야 작동되는 것을 확인함. 
	 * 
	 * @param files - 파일 전송 시 파일 정보는 request의 body가 아닌 
	 * header에 쿼리스트링 형태로 실어 전송해야한다.
	 * @return
	 */
	@PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
	public ResponseEntity<ResponseJson> uploadFiles(
		//@RequestParam("files") MultipartFile[] files
		@RequestPart(name = "files") MultipartFile[] files
	) {

		ResponseJson responseJson = null;
		
		List<String> results = (List<String>) fileService.uploadFiles(files);
		Map<String, String> failed = fileService.getFailedPaths();
		FileSuccessFailed fileResults = FileSuccessFailed.builder()
			.success(results)
			.failed(failed)
			.build();
		
		if (results.size() == 0) {
			// 클라이언트가 요청한 파일 모두 업로드에 실패한 경우.
			responseJson = ResponseJson.builder()
				.httpStatus(HttpStatus.INTERNAL_SERVER_ERROR)
				.message("요청한 파일 모두 업로드에 실패하였습니다.")
				.data(fileResults)
				.build();
		} else if (failed.size() > 0) {
			// 클라이언트가 요청한 파일들 중 일부만 업로드 성공한 경우.
			responseJson = ResponseJson.builder()
				.httpStatus(HttpStatus.PARTIAL_CONTENT)
				.message("요청한 파일들 중 일부만 업로드에 성공하였습니다.")
				.data(fileResults)
				.build();
		} else {
			// 모든 요청한 파일들이 업로드에 성공한 경우
			responseJson = ResponseJson.builder()
				.httpStatus(HttpStatus.CREATED)
				.message("요청한 모든 파일 업로드 성공")
				.data(fileResults)
				.build();
		}
		
		return responseJson.toResponseEntity();
		
	}
	
	// 생략...

```

코드 1-5. FileController.java

위 코드에서 주목할 부분은 바로 다음이다. 

```java
@PostMapping(consumes = MediaType.MULTIPART_FORM_DATA_VALUE)
public ResponseEntity<ResponseJson> uploadFiles(
	//@RequestParam("files") MultipartFile[] files
	@RequestPart(name = "files") MultipartFile[] files
) 
```

코드 1-6. FileController.java의 일부 코드.

클라이언트가 업로드 요청할 파일을 받으려면 MultipartFile 객체를 통해 받아야 한다. 또한 ‘Content-Type’을 “multipart/form-data”로 해야 파일로 받을 수 있기에 `@PostMapping` 어노테이션에 `consumes` 속성을 위와 같이 적용하였다. 그리고 한 번에 여러 개의 파일을 받을 수 있도록 배열 형태로 받도록 하였다. `List<MultipartFile>` 타입으로 받아도 된다. 

`@RequestParam` 으로도 파일 업로드를 받을 수 있는 것으로 알고 있었으나, 필자가 Swagger로 테스트했을 때는 파일 업로드를 할 수 없었다. 그래서 조사 결과 위와 같이 consumes 속성과 더불어 `@RequestPart` 어노테이션을 사용하니 그제서야 Swagger에서 파일 업로드 기능을 테스트해볼 수 있었다. 

`@RequestParam` 과 `@RequestPart` 모두 multipart/form-data 즉, 이미지와 같은 미디어 파일들을 받아들일수 있는 어노테이션인데, `@RequestParam`은 그 외 쿼리 스트링의 파라미터, form data도 받을 수 있지만 `@RequestPart` 는 multipart/form-data 타입에 특화되어 있는 어노테이션이라고 한다 .

다음은 Swagger에서 파일 업로드 기능을 테스트하는 화면이다.

![사진 1-2. Swagger에서 여러 파일들을 입력하여 업로드 하기 전 모습. ](/images/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/2.png)

사진 1-2. Swagger에서 여러 파일들을 입력하여 업로드 하기 전 모습. 

![사진 1-3. Swagger에서 여러 이미지 파일들을 업로드 요청한 후의 모습. ](/images/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/3.png)

사진 1-3. Swagger에서 여러 이미지 파일들을 업로드 요청한 후의 모습. 

사진 1-3의 “Curl” 부분을 자세히 보면, 여러 개의 파일 정보들을 입력하기 위해선 컨트롤러 메서드에서 인자로 정한 “files”에 맞게 “files=…” 식으로 여러 개 입력하는 방식임을 알 수 있다. 

프론트엔드에서는 이 API를 다음과 같이 사용하면 된다. 

```jsx
import { useRef, useState } from "react";
import { Link, useNavigate } from "react-router-dom";
import axios from "axios";

import * as utils from '../utils/utils';

const FileUpload = () => {

  const formElement = useRef(null);
  const [uploadFiles, setUploadFiles] = useState([]);
  const navigator = useNavigate();

  /**
   * 사용자가 입력한 파일 정보 추출.
   * 
   * 참고) 
   * change 이벤트 리스너 내부에서 event.target.files 를 통해 
   * 사용자가 입력한 여러 개의 파일들의 정보를 FileList라는 객체를 통해 
   * 접근 및 확인할 수 있다. 
   * 
   * 참고사이트)
   * https://developer.mozilla.org/en-US/docs/Web/API/File_API/Using_files_from_web_applications
   * 
   * @param {Event} event 
   */
  const handleFileChange = (event) => {
    const fileNames = [];
    const fileList = event.target.files;

    for (const file of fileList) {
      fileNames.push(file.name);  // 문자열 형태의 파일명만 추출하여 배열에 삽입.
    }
    setUploadFiles(fileNames);
  };
  
  /**
   * 사용자가 입력한 여러 파일들을 서버에 업로드.
   * 
   * @param {Event} event 
   */
  const handleFileUpload = (event) => {
    const formData = new FormData(formElement.current);
    const keyName = "files";
    
    // 업로드하고자 하는 파일 정보를 요청 정보로 변환.
    // request body에 넣으며, key 이름은 files로, 스프링 서버에서 지정한 
    // 이름으로 고정.
    if (uploadFiles && uploadFiles.length > 0) {
      uploadFiles.forEach((fileName) => {
        formData.append(keyName, fileName);
      });
    } else {
      alert("입력된 파일이 없습니다. 업로드할 파일을 먼저 선택해주세요.");
      return;
    }

    axios.post("http://localhost:8080/files", formData)
      .then(response => {
        console.log(response.data.message);
        alert(response.data.message);
        navigator("/mypage");
      })
      .catch(error => {
        if (error.status === utils.httpStatusMessages.INTERNAL_SERVER_ERROR) {
          alert(error.response.data.message);
        } else {
          console.log("예기치 못한 에러 발생");
          console.log(error);
        }
      });
  };
  
  return (
    <div>
      <h1>파일 업로드하기</h1>
      <form ref={formElement}>
        {/* accept: 특정 유형의 파일만 받는다. 여기서는 이미지 파일만 받도록 설정. */}
        <input type="file" 
          id="files" 
          name="files" 
          accept="image/*"
          multiple  // 여러 개의 파일 업로드 가능. 
          required 
          onChange={handleFileChange}
        />
        <button type="button" onClick={handleFileUpload}>파일 업로드하기</button>
      </form>
      <p><Link to="/mypage">업로드 취소</Link></p>
      {
        (uploadFiles && uploadFiles.length > 0 ) ? 
        uploadFiles.map((data, idx) => (<p key={idx}>{data}</p>)) :
        <></>
      }
    </div>
  );
};

export default FileUpload;
```

코드 1-7. FileUpload.jsx

```jsx
<form ref={formElement}>
  {/* accept: 특정 유형의 파일만 받는다. 여기서는 이미지 파일만 받도록 설정. */}
  <input
    type="file"
    id="files"
    name="files"
    accept="image/*"
    multiple // 여러 개의 파일 업로드 가능.
    required
    onChange={handleFileChange}
  />
  <button type="button" onClick={handleFileUpload}>
    파일 업로드하기
  </button>
</form>;
```

코드 1-8. 코드 1-7의 일부

위 코드 1-8에서처럼 `<input>` 태그 속성으로 multiple을 넣어 여러 개의 파일들을 입력 가능하도록 설정한다. 그러면 파일 다이얼로그가 뜰 때 Ctrl 키를 누른 상태에서 여러 개의 파일들을 선택하면 된다. 그리고 `accept="image/*"` 속성을 넣어 이미지 파일만 업로드 가능하게 구성하였다. 

```jsx
/**
 * 사용자가 입력한 여러 파일들을 서버에 업로드.
 *
 * @param {Event} event
 */
const handleFileUpload = (event) => {
  const formData = new FormData(formElement.current);
  const keyName = "files";

  // 업로드하고자 하는 파일 정보를 요청 정보로 변환.
  // request body에 넣으며, key 이름은 files로, 스프링 서버에서 지정한
  // 이름으로 고정.
  if (uploadFiles && uploadFiles.length > 0) {
    uploadFiles.forEach((fileName) => {
      formData.append(keyName, fileName);
    });
  } else {
    alert("입력된 파일이 없습니다. 업로드할 파일을 먼저 선택해주세요.");
    return;
  }

  axios
    .post("http://localhost:8080/files", formData)
    .then((response) => {
      console.log(response.data.message);
      alert(response.data.message);
      navigator("/mypage");
    })
    .catch((error) => {
      if (error.status === utils.httpStatusMessages.INTERNAL_SERVER_ERROR) {
        alert(error.response.data.message);
      } else {
        console.log("예기치 못한 에러 발생");
        console.log(error);
      }
    });
};
```

코드 1-9. 코드 1-7의 일부.

그 다음, 여러 개의 파일들을 API 호출을 통해 업로드하는 기능을 위와 같이 구현하였다. FormData 객체를 이용하였으며, `FormData.append(key, value)` 메서드와 forEach를 통해 여러 파일들의 정보를 value에 입력한다. 이 때 key는 “files”로 고정시킨다. 이렇게 하면 앞선 시연 영상에서 본 것처럼 여러 개의 파일 업로드를 할 수 있게 된다. 

## 업로드 로직

이제 파일 업로드 로직의 핵심이 되는 서비스 계층 코드이다. 먼저 서비스 인터페이스를 다음과 같이 정의하였다. 

```java
package com.jerocaller.service;

import java.util.List;
import java.util.Map;

import org.springframework.web.multipart.MultipartFile;

import com.jerocaller.dto.FileResponse;

/**
 * 조사 결과, 파일 업로드 및 다운로드 기능을 구현하는 방법에는 
 * 여러 가지가 있다. 따라서 각각의 방법들로 따로 구현하고, 
 * 다른 방법으로 구현한 서비스 클래스로 손쉽게 스위칭하기 위해 
 * 추상화를 이용하기 위해 인터페이스를 선언함. 
 * 파일 업로드 및 다운로드 기능을 구현하는 서비스 클래스들은 모두 
 * 이 인터페이스를 구현해야 함. 
 */
public interface FileServiceInterface<Service> {
	
	/**
	 * 클라이언트로부터 넘어온 파일과 그 경로를 각각 서버 내 폴더 및 
	 * DB에 저장한다. 
	 *
	 * @param files - 여러 개의 파일들의 업로드 허용
	 * @return 작업 결과 성공 또는 실패, 또는 그 외 결과를 어떻게 표시할지 
	 * 자유롭게 하기 위해 Object로 선언함. 
	 */
	Object uploadFiles(MultipartFile[] files);
	
	/**
	 * 현재 인증된 사용자의 파일들을 리스트로 반환.
	 * 
	 * @return
	 */
	List<FileResponse> getFiles();
	
	/**
	 * 파일 id 입력 시 해당 파일을 다운로드
	 * 
	 * @param id
	 * @return 작업 결과 성공 또는 실패, 또는 그 외 결과를 어떻게 표시할지 
	 * 자유롭게 하기 위해 Object로 선언함. 
	 */
	Object downloadByFileId(int id);
	
	/**
	 * 파일 id 입력 시 이에 해당하는 파일을 삭제. 
	 * 
	 * 파일 시스템 상에서도 삭제되어야 하고, DB에서도 
	 * 해당 파일 정보가 삭제되어야 한다. 
	 * 
	 * @param id
	 * @return 작업 결과 성공 또는 실패, 또는 그 외 결과를 어떻게 표시할지 
	 * 자유롭게 하기 위해 Object로 선언함. 
	 */
	Object deleteByFileId(int id);
	
	/**
	 * 여러 파일들의 업로드 혹은 다운로드 시 실패한 파일 경로들을 
	 * 로깅할 목적의 데이터를 반환하는 메서드.
	 * 
	 * @return
	 */
	default Map<String, String> getFailedPaths() {
		return null;
	}
	
}

```

코드 1-10. FileServiceInterface.java

조사 결과, 파일 업로드 로직에는 몇 가지가 더 있는 것으로 보였다. 따라서 각각의 로직을 구현하는 구현 서비스 클래스를 별도로 만들어 실험하기 위해 위와 같은 인터페이스를 선언하였다. 하지만 회원 기능, Security 기능 등 여러 다른 기능들도 구현하다보니 이 예제 사이트를 만드는데 생각보다 시간이 오래 걸려 여기서는 그 중 하나의 로직만 구현하였다. 다음은 파일 업로드 기능을 구현하는 서비스 구현 클래스 코드 일부이다.

```java
package com.jerocaller.service;

import java.io.IOException;
import java.io.UnsupportedEncodingException;
import java.net.MalformedURLException;
import java.net.URLEncoder;
import java.nio.file.Files;
import java.nio.file.Path;
import java.nio.file.Paths;
import java.nio.file.StandardCopyOption;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.core.io.Resource;
import org.springframework.core.io.UrlResource;
import org.springframework.http.HttpHeaders;
import org.springframework.http.MediaType;
import org.springframework.http.ResponseEntity;
import org.springframework.stereotype.Service;
import org.springframework.web.multipart.MultipartFile;

import com.jerocaller.dto.FileResponse;
import com.jerocaller.entity.FileEntity;
import com.jerocaller.entity.Members;
import com.jerocaller.exception.FileInfoInDBNotDeletedException;
import com.jerocaller.exception.FileNotDeletedException;
import com.jerocaller.exception.FileNotFoundOrReadableException;
import com.jerocaller.exception.IORuntimeException;
import com.jerocaller.exception.NotAuthenticatedUserException;
import com.jerocaller.repository.FileEntityRepository;
import com.jerocaller.repository.MembersRepository;
import com.jerocaller.util.AuthenticationUtils;

import jakarta.annotation.PostConstruct;
import jakarta.persistence.EntityNotFoundException;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Service
@RequiredArgsConstructor
@Slf4j
public class FileServiceTypeOneImpl implements FileServiceInterface<FileServiceTypeOneImpl> {
	
	private final FileEntityRepository fileRepository;
	private final MembersRepository membersRepository;
	
	private Path uploadBaseDirPath;
	
	// uploadFiles() 메서드 실행 도중 특정 파일이 업로드에 실패했을 때 
	// 어떤 파일이 업로드에 실패했는지 로깅하기 위한 필드.
	private final Map<String, String> failedPaths = new HashMap<String, String>();
	
	// 업로드에 성공한 파일명들을 담아 특정 메서드의 반환값이 될 필드.
	private final List<String> successFileNames = new ArrayList<String>();
	
	@Value("${file.upload-dir}")
	private String uploadBaseDir;
	
	/**
	 * <p>
	 * 파일 업로드를 위한 서버 내 베이스 디렉토리 생성(없을 시에만) 및 
	 * 경로 초기화. <br/>
	 * 업로드될 파일을 저장하기 위한 베이스 디렉토리 생성은 최초 단 한 번만 실행되면 되기에 
	 * 초기화 메서드를 통해 생성하도록 함. 
	 * </p>
	 * 
	 * <p>
	 * 어노테이션 설명) <br/>
	 * \@PostConstruct - 생성자를 이용한 DI(Dependency Injection, 의존성 주입)의 
	 * 과정을 떠올려보면 알겠지만, 생성자 호출 시 해당 생성자 메서드 내부에서 
	 * DI가 일어나 의존성이 주입된 후에야 스프링 빈이 생성되는 구조이다. 
	 * 그런데 어떤 메서드에 @PostConstruct 를 적용하면, DI가 진행된 이후에 
	 * 해당 메서드가 호출되기에, 주입된 의존성을 확인하면서 초기화 작업을 할 수 있게 된다. 
	 * 즉, 주입된 의존성을 이용한 초기화가 가능해진다. 
	 * 또한, 이 어노테이션이 적용된 초기화용 메서드는 bean lifecycle에서 오로지 단 한 번만 호출되며, 
	 * Bean 자체도 한 번만 인스턴스화되기에 불필요한 여러 번의 초기화 또는 빈 재생성을 방지할 수 있게 된다. <br/>
	 * 여기서는 @RequiredArgsConstructor 이라는 어노테이션을 이용하여 생성자 DI를 하기에 생성자 메서드를 
	 * 직접 정의하여 초기화 코드를 마련할 수 없어 사용하게 됨. 
	 * <br/>
	 * 참고 자료) <br/>
	 * https://hstory0208.tistory.com/entry/Spring-PostConstruct%EC%99%80-PreDestory-%EC%8A%A4%ED%94%84%EB%A7%81-%EB%B9%88-%EC%83%9D%EB%AA%85%EC%A3%BC%EA%B8%B0-%EA%B4%80%EB%A6%AC
	 * https://zorba91.tistory.com/223
	 * https://stackoverflow.com/questions/3406555/why-use-postconstruct
	 * </p>
	 * 
	 * @throws IOException
	 */
	@PostConstruct
	public void init() throws IOException {
		
		// 경로 URI를 토대로 실제로 물리적으로 베이스 디렉토리 생성 및 
		// 업로드된 파일들을 저장하기 위해 Path 객체로 변환.
		// Paths.get(String) : String을 Path 객체로 변환.
		uploadBaseDirPath = Paths.get(uploadBaseDir);
		log.info("베이스 디렉토리 절대 경로: " + uploadBaseDirPath.toFile().getAbsolutePath());
		
		// 만약 해당 베이스 디렉토리가 존재하지 않는다면 생성.
		if (Files.notExists(uploadBaseDirPath)) {
			Files.createDirectories(uploadBaseDirPath);
			log.info("업로드된 파일의 서버 내 저장을 위한 베이스 디렉토리가 생성되었습니다.");
		}
		
	}
	
	@Override
	public Map<String, String> getFailedPaths() {
		return failedPaths;
	}
	
	@Override
	public Object uploadFiles(MultipartFile[] files) throws 
		NotAuthenticatedUserException, 
		IORuntimeException,
		EntityNotFoundException
	{
		
		failedPaths.clear();
		successFileNames.clear();
		
		// 현재 사용자의 닉네임 추출. 
		// 미인증 사용자의 경우 NotAuthenticatedUserException 예외 발생
		// 다른 유저들과 구분하기 위해 유저 닉네임을 이름으로 하는 디렉토리 추가 후 
		// 여기에 파일들을 저장하도록 한다. 
		String username = AuthenticationUtils.getLoggedinUserInfo()
			.getNickname();
		
		// Path_A.resolve(String childDir) : Path_A/childDir로 URI를 합친 후 이 
		// 문자열을 다시 Path로 반환.
		Path baseUserDirPath = uploadBaseDirPath.resolve(username);
		if (Files.notExists(baseUserDirPath)) {
			try {
				// 존재하지 않는 모든 부모 디렉토리를 자동 생성해준다고 함.
				Files.createDirectories(baseUserDirPath);
			} catch (IOException e) {
				throw new IORuntimeException("유저 닉네임 디렉토리 생성 실패. \n" + e.getMessage());
			}
		}
		
		for (MultipartFile file : files) {
			// 파일 내용이 다르나 서버 내에 똑같은 파일명이 존재할 경우를 방지하기 위해 
			// 클라이언트가 전달한 파일명에 부가 정보를 추가한다. 
			// 여기서는 현재 시간이란 정보를 이용한다. 
			String fileName = System.currentTimeMillis() // 현재 시각을 밀리초로 반환.
				+ "_" 
				+ file.getOriginalFilename(); // 멀티파일의 원래 파일명.
			
			// 부모 디렉토리 경로와 방금 생성한 파일명을 합쳐 최종 경로를 생성함.
			Path filePath = baseUserDirPath.resolve(fileName); 
			log.info("서버 내에 저장될 파일의 최종 경로: " + filePath.toFile()
				.getAbsolutePath()
			);
			
			try {
				// 파일을 특정 경로로 복사 하기. 
				// 이미 동일 파일 존재 시 덮어쓰기
				// Files.copy() : InputStream으로부터 파일을 읽어 filePath 위치에 
				// 파일을 저장함. 
				// StandardCopyOption.REPLACE_EXISTING : 덮어쓰기 옵션
				Files.copy(file.getInputStream(), filePath, StandardCopyOption.REPLACE_EXISTING);
			} catch (IOException e) {
				// 업로드 실패한 파일의 전체 경로를 기록.
				// 다른 파일들은 계속 업로드할 수 있도록 예외를 던지지 않음. 
				// 예외가 발생한 파일 전체 경로 : 에러 메시지
				failedPaths.put(filePath.toFile().getAbsolutePath(), e.getMessage());
				continue;
			}
			
			// 파일 정보를 DB에 저장.
			Members currentMember = membersRepository.findById(username)
				.orElseThrow(() -> new EntityNotFoundException("존재하지 않는 유저입니다."));
			FileEntity fileEntity = FileEntity.builder()
				.filePath(filePath.toString())
				.members(currentMember)
				.build();
			fileRepository.save(fileEntity);
			
			// 파일 업로드 및 파일 경로의 DB 저장까지 성공적으로 마친 파일명을 기록.
			successFileNames.add(file.getOriginalFilename());
			
		}
		
		return successFileNames;
		
	}
	
	// 생략...

```

코드 1-11. FileServiceTypeOneImpl.java

위 코드가 매우 긴데, 절차는 다음과 같이 축약할 수 있겠다.

1) application.properties에 설정한 파일 루트 디렉토리 경로를 `@Value` 어노테이션을 통해 얻어온다. 

```java
@Value("${file.upload-dir}")
private String uploadBaseDir;
```

코드 1-12.

2) 여기서는 파일 업로드 시 `java.nio.file.Path` 인터페이스와 `java.nio.file.Files` 클래스가 자주 사용되므로, String 타입으로 얻어온 루트 디렉토리 경로를 Path 객체로 변환한다. 이 과정은 단 한 번만 거치면 되므로 `init()` 메서드에 작성하였다. 그리고 그와 동시에 해당 경로의 루트 디렉토리를 `Files.createDiretories()` 메서드를 통해 실제로 생성한다. 

```java
@PostConstruct
public void init() throws IOException {
		
	// 경로 URI를 토대로 실제로 물리적으로 베이스 디렉토리 생성 및 
	// 업로드된 파일들을 저장하기 위해 Path 객체로 변환.
	// Paths.get(String) : String을 Path 객체로 변환.
	uploadBaseDirPath = Paths.get(uploadBaseDir);
	log.info("베이스 디렉토리 절대 경로: " + uploadBaseDirPath.toFile().getAbsolutePath());
		
	// 만약 해당 베이스 디렉토리가 존재하지 않는다면 생성.
	if (Files.notExists(uploadBaseDirPath)) {
		Files.createDirectories(uploadBaseDirPath);
		log.info("업로드된 파일의 서버 내 저장을 위한 베이스 디렉토리가 생성되었습니다.");
	}
		
}
```

코드 1.13.

3) 그 다음은 모두 위 코드의 `uploadFiles()` 메서드 내부에서 일어난다. 필자가 구현한 사이트에는 회원 별 파일 업로드 기능이므로 현재 인증된 사용자 정보를 먼저 불러온다. 

```java
String username = AuthenticationUtils.getLoggedinUserInfo()
	.getNickname();
```

코드 1-14.

4) 업로드된 파일들을 사용자 별로 구분하여 관리하기 위해 사용자 닉네임을 딴 서브 디렉토리를 생성한다. (사실 어차피 DB 상에서 사용자 별로 파일들을 구분할 수 있기에 루트 디렉토리에 모든 파일들을 모아 관리해도 될 것이다)

```java
// Path_A.resolve(String childDir) : Path_A/childDir로 URI를 합친 후 이 
// 문자열을 다시 Path로 반환.
Path baseUserDirPath = uploadBaseDirPath.resolve(username);
if (Files.notExists(baseUserDirPath)) {
	try {
		// 존재하지 않는 모든 부모 디렉토리를 자동 생성해준다고 함.
		Files.createDirectories(baseUserDirPath);
	} catch (IOException e) {
		throw new IORuntimeException("유저 닉네임 디렉토리 생성 실패. \n" + e.getMessage());
	}
}
```

코드 1-15. 

5) 서버에 저장할 파일의 이름을 변경한다. 이는 파일명이 중복될 수 있기 때문인데, 여기서는 현재 시각을 밀리초로 나타낸 시각 정보를 원래 파일명 앞에 추가하는 방식을 선택하였다. 

```java
// 파일 내용이 다르나 서버 내에 똑같은 파일명이 존재할 경우를 방지하기 위해 
// 클라이언트가 전달한 파일명에 부가 정보를 추가한다. 
// 여기서는 현재 시간이란 정보를 이용한다. 
String fileName = System.currentTimeMillis() // 현재 시각을 밀리초로 반환.
	+ "_" 
	+ file.getOriginalFilename(); // 멀티파일의 원래 파일명.
			
// 부모 디렉토리 경로와 방금 생성한 파일명을 합쳐 최종 경로를 생성함.
Path filePath = baseUserDirPath.resolve(fileName); 
```

코드 1-16. 

6) 새로 생성된 파일명을 토대로 해당 파일을 서버 내 디렉토리에 저장한다. 여기서 `Files.copy()` 메서드가 핵심적으로 사용된다.

```java
try {
	// 파일을 특정 경로로 복사 하기. 
	// 이미 동일 파일 존재 시 덮어쓰기
	// Files.copy() : InputStream으로부터 파일을 읽어 filePath 위치에 
	// 파일을 저장함. 
	// StandardCopyOption.REPLACE_EXISTING : 덮어쓰기 옵션
	Files.copy(file.getInputStream(), filePath, StandardCopyOption.REPLACE_EXISTING);
} catch (IOException e) {
	// 업로드 실패한 파일의 전체 경로를 기록.
	// 다른 파일들은 계속 업로드할 수 있도록 예외를 던지지 않음. 
	// 예외가 발생한 파일 전체 경로 : 에러 메시지
	failedPaths.put(filePath.toFile().getAbsolutePath(), e.getMessage());
	continue;
}
```

코드 1-17.

7) 파일 경로를 참조할 수 있도록 파일 경로 정보를 DB에 저장한다. 

```java
// 파일 정보를 DB에 저장.
Members currentMember = membersRepository.findById(username)
	.orElseThrow(() -> new EntityNotFoundException("존재하지 않는 유저입니다."));
FileEntity fileEntity = FileEntity.builder()
	.filePath(filePath.toString())
	.members(currentMember)
	.build();
fileRepository.save(fileEntity);
```

코드 1-18.

위 절차와 같이 거치면 파일 업로드 기능을 구현할 수 있다. 

# 클라이언트에서 업로드한 파일 목록 읽어들이기

파일을 업로드하였다면 이를 클라이언트에서 브라우저 화면 상에서 볼 수 있도록 해야 한다. 이를 위해 먼저 백엔드의 서비스 클래스에서 다음의 메서드를 정의하였다. 

```java
@Override
public List<FileResponse> getFiles() throws 
	EntityNotFoundException,
	NotAuthenticatedUserException
{
		
	String username = AuthenticationUtils.getLoggedinUserInfo()
		.getNickname();
	log.info("현재 사용자 닉네임: " + username);
		
	Members currentMember = membersRepository.findById(username)
		.orElseThrow(() -> new EntityNotFoundException("존재하지 않는 유저입니다."));
		
	List<FileEntity> fileEntities = fileRepository.findByMembers(currentMember);
		
	return fileEntities.stream()
		.map(FileResponse :: toDto)
		.collect(Collectors.toList());
		
}
```

코드 2-1. FileServiceTypeOneImpl.getFiles

그리고 백엔드에서 정보를 받아 화면에 출력하는 프론트엔드 코드는 다음과 같이 작성하였다. 

```jsx
import axios from "axios";
import { useEffect, useState } from "react";
import { useDispatch, useSelector } from "react-redux";

import * as utils from '../../utils/utils';
import actionTypes from "../../redux/actions";

import FileCard from "./fileSubComponents/FileCard";
import FileCardContainer from "./fileSubComponents/FileCardContainer";

/**
 * 파일 정보를 리스트로 보여준다. 
 * 
 * @param {*} param0 
 * @returns 
 */
const FileList = ({ userInfo }) => {

	const [fileInfoList, setFileInfoList] = useState([]);
	const [isAuthError, setIsAuthError] = useState(false);
	const isFileChanged = useSelector((state) => state.fileChangeReducer.isFileChanged);
	//console.log("isFileChanged : " + isFileChanged);
	const fileDispath = useDispatch();

	const readFileFromServer = () => {
		axios.get("http://localhost:8080/files")
			.then(response => {
				if (utils.isSuccessHttpStatusCode(response.status)) {
					const responseData = response.data.data;
					setFileInfoList(responseData);
				}
			})
			.catch(error => {
				switch (error.status) {
					case utils.httpStatusMessages.UNAUTHORIZED:
						setIsAuthError(true);
						break;
					case utils.httpStatusMessages.NOT_FOUND:
						setFileInfoList([]);
						break;
					default:
						console.log("예기치 못한 에러 발생");
						console.log(error);
				}
			});
	};

	useEffect(() => {
		readFileFromServer();
	}, []);

	if (isFileChanged) {
		readFileFromServer();
		fileDispath({
			type: actionTypes.FILE_CHANGED,
			payload: { isFileChanged: false }
		});
	}

	if (userInfo == null || !userInfo.loggedIn || isAuthError) {
		return (
			<div>
				<p>현재 사용자의 인증 정보를 얻어올 수 없습니다.</p>
				<p>인증을 위한 세션이 만료되어서일 수도 있으니 재로그인을 시도해보세요.</p>
			</div>
		);
	}

	if (fileInfoList.length === 0) {
		return (
			<div>
				<p>조회된 데이터가 없습니다.</p>
			</div>
		);
	}

	return (
		<div>
			<FileCardContainer>
				{fileInfoList.map(data => (<FileCard key={data.id} fileInfo={data}/>))}
			</FileCardContainer>
		</div>
	);
};

export default FileList;
```

코드 2-2. FileList.jsx

위 코드에서는 백엔드로부터 현재 인증된 사용자의 파일 정보들을 가져오는 로직이 주를 이루고 있다. 실제로 각 파일 정보들을 출력하는 코드는 다음의 FileCard.jsx 파일에서 카드 형태로 출력하게끔 하였다. 

```jsx
// 생략...

const FileCard = ({ fileInfo }) => {

  // 생략...

  return (
    <li key={fileInfo.id} className="file-card">
      {/* DB로부터 가져온 파일 경로의 맨 앞에 "."이 붙어있으므로, 이를 제거해야 이미지가 정상적으로 출력됨. */}
      <img src={fileInfo.filePath.substring(1)} width="50%" />
      <p>id: {fileInfo.id}</p>
      <p>path: {fileInfo.filePath}</p>
      {/*<button onClick={handleDownload}>다운로드</button> */}
      <p>
        <a href={`http://localhost:8080/files/download/${fileInfo.id}`}>다운로드</a>
      </p>
      <button type="button" onClick={deleteFile}>삭제하기</button>
    </li>
  );
};

export default FileCard;

```

코드 2-3. FileCard.jsx

![사진 2-1. 파일 목록](/images/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/4.png)

사진 2-1. 파일 목록

그런데 사실 위 코드들만으로는 위 사진 2-1 처럼 이미지를 볼 수가 없다. 위 코드들만으로는 원래 다음의 사진처럼 보인다.

![사진 2-2. 파일 목록. 이미지 로딩에 실패했다. ](/images/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/5.png)

사진 2-2. 파일 목록. 이미지 로딩에 실패했다. 

이는 이미지 경로 접근 시 실제 파일이 저장된 경로로 접근하도록 설정하지 않았기 때문이다. 이 문제를 해결하기 위해서는 백엔드에서 다음의 설정 클래스를 정의해야 한다. 

```java
package com.jerocaller.config;

import java.nio.file.Path;
import java.nio.file.Paths;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

import lombok.extern.slf4j.Slf4j;

/**
 * WebMvcConfigurer : Spring MVC에 관한 설정. 웹 환경 설정용.
 */
@Configuration
public class WebConfig implements WebMvcConfigurer {
	
	@Value("${file.upload-dir}")
	private String uploadBaseDirString;
	
	/**
	 * 정적 리소스(이미지, CSS, JS 등) 경로 추가 설정 담당.
	 * 업로드 설정이 목표. 
	 * 
	 * 이 메서드를 정의하지 않으면 프론트엔드에서 서버로부터 파일 경로를 받아도 
	 * img 태그를 이용하여 이미지를 화면에 출력할 수 없다. 따라서 이 설정은 필수!
	 */
	@Override
	public void addResourceHandlers(ResourceHandlerRegistry registry) {
		
		// 업로드 될 파일들이 저장될 폴더의 정보를 얻고 그 절대 경로를 얻는다. 
		Path uploadBaseDir = Paths.get(uploadBaseDirString);
		String fullPath = uploadBaseDir.toFile().getAbsolutePath();
		
		// application.properties 파일에 설정한 file.upload-dir의 경로에 "."과 같이 
		// 상대 경로 표기법이 들어가 있는 경우 이를 제거함으로써, 정적 자원들이 들어있는 경로에 접근 가능한 
		// 경로 패턴 문자열을 동적으로 생성한다. 
		// 예) file-upload-dir = ./upload/files 라 되어 있는 경우, 
		// => /upload/files 로 바꾼다. 
		Path normalizedUploadBaseDir = Paths.get(uploadBaseDirString).normalize();
		String contextPath = "/" + normalizedUploadBaseDir.toString().replace("\\", "/");
		
		// 정적 자원을 제공하기 위해 registry.addResourceHandler(String pattern) 메서드를 
		// 사용한다. "/files/users/**"라는 문자열이 해당 메서드의 인자로 전달된 경우, 
		// /files/users/test.png 와 같은 URL이 들어오면 그 위치에 존재하는 test.png를 
		// 반환할 수 있도록 설정한다. 
		
		// registry.addResourceHandler("/files/users/**") // 이것과 동일
		registry.addResourceHandler(contextPath + "/**")
			.addResourceLocations("file:" + fullPath + "/");
		
		// "file:" + uploadPath + "/" => 
		// 파일 시스템의 uploads 디렉토리를 나타냄. 
		// "file:" 접두사를 붙여 이 경로가 파일 시스템의 경로임을 지정한다. 
		// 해당 접두사는 WebMvcConfigurer에 대한 설정을 위함. 
	}
	
}

```

코드 2-4. WebConfig.java

WebMvcConfigurer 인터페이스의 addResourceHandlers() 메서드를 구현하여 앞서 설정한 파일 루트 디렉토리를 등록해야 한다. 그래야 클라이언트에서 이미지를 불러올 때 알맞는 경로를 찾아 불러올 수 있게 되는 것이다. 

# 파일 다운로드

백엔드에서 파일 다운로드 기능을 구현하는 서비스 코드는 다음과 같다.

```java
@Override
public Object downloadByFileId(int id) throws 
	EntityNotFoundException, 
	IORuntimeException,
	FileNotFoundOrReadableException
{
		
	FileEntity targetFile = fileRepository.findById(id)
		.orElseThrow(() -> new EntityNotFoundException("존재하지 않는 파일입니다."));
		
	// 서버 상에 존재하는 파일 경로 추출.
	// normalize() : ".", ".."과 같이 상대 경로 지정에 쓰이는 문자들을 
	// 불필요한 문자로 간주하고 삭제해주는 기능.
	// 예) abc/./test.png -> abc/test.png
	Path filePath = Paths.get(targetFile.getFilePath()).normalize();
	log.info("서버 내에 저장된 파일의 최종 경로: " + filePath.toFile()
		.getAbsolutePath()
	);
		
	// 파일, 이미지 등의 모든 리소스를 표현하는 객체
	// 기존 문자열 형식의 경로에서, file://~ 프로토콜로 시작하는 URI 형식으로 변경됨.
	Resource resource = null;
	try {
		resource = new UrlResource(filePath.toUri());
	} catch (MalformedURLException e) {
		throw new IORuntimeException("Path -> Resource로 변경 중 URI가 잘못 생성되었습니다.");
	}
		
	if (!resource.exists() || !resource.isReadable()) {
		String exceptionPath = null;
		try {
			exceptionPath = resource.getURL().getPath();
			throw new FileNotFoundOrReadableException(
				"해당 파일이 존재하지 않거나 열 수 없습니다.", 
				exceptionPath
			);
		} catch (IOException e) {
			throw new IORuntimeException("resource 객체로부터 URL 문자열로 변환 중 예외 발생.");
		}
	}
		
	// 파일의 MIME-TYPE 정보 파악 후 타입 결정 (이미지인지 동영상인지 등을 판별)
	String contentType = null;
	try {
		contentType = Files.probeContentType(filePath);
	} catch (IOException e) {
		contentType = "application/octet-stream";
	}
		
	// 만약 MIME-TYPE을 읽어오지 못한다면 다운로드 작업 수행.
	if (contentType == null) {
		// 해당 타입은 브라우저가 해석하지 못하는 타입이다. 
		// 브라우저가 해석하지 못하는 컨텐트 타입은 무조건 다운로드를 실행하는 
		// 특성이 있으니 이를 이용하는 것이다. 
		contentType = "application/octet-stream";
	}
	log.info("파일의 MIME-TYPE : " + contentType);
		
	// 파일의 원래 이름 가져오기
	String originalFileName = null;
	try {
		// 파일명에 한글이 포함된 경우 다운로드 도중 예외가 발생하므로 이를 utf-8로 인코딩한다. 
		originalFileName = URLEncoder.encode(filePath.getFileName().toString(), "utf-8");
	} catch (UnsupportedEncodingException e) {
		log.error("잘못된 인코딩 방식입니다.");
		originalFileName = filePath.getFileName().toString();
	}
		
	// CONTENT_DISPOSITION : 해당 파일이 브라우저에서 열리지 않도록 함.
	return ResponseEntity.ok()
		.contentType(MediaType.parseMediaType(contentType))
		.header(HttpHeaders.CONTENT_DISPOSITION, "attachment;filename=\"" + originalFileName + "\"")
		.body(resource);
		
}
```

코드 3-1. FileServiceTypeOneImpl.downloadByFileId

위 코드에 나타난 절차들을 정리하면 다음과 같다. 

1) 메서드 인자로 주어진 id값을 토대로 해당 파일을 DB로부터 조회한다. 

```java
FileEntity targetFile = fileRepository.findById(id)
	.orElseThrow(() -> new EntityNotFoundException("존재하지 않는 파일입니다."));
```

코드 3-2.

2) 조회된 파일 Entity에는 파일 경로 정보가 있는데, 이를 가져와 최종적으로 `org.springframework.core.io.Resource` 객체로 변환한다. Resource 객체는 정적 리소스를 표현하는 객체로, 이 객체를 클라이언트에 반환해야 해당 파일을 다운로드받을 수 있게 된다. 

```java
// 서버 상에 존재하는 파일 경로 추출.
// normalize() : ".", ".."과 같이 상대 경로 지정에 쓰이는 문자들을 
// 불필요한 문자로 간주하고 삭제해주는 기능.
// 예) abc/./test.png -> abc/test.png
Path filePath = Paths.get(targetFile.getFilePath()).normalize();
log.info("서버 내에 저장된 파일의 최종 경로: " + filePath.toFile()
	.getAbsolutePath()
);
		
// 파일, 이미지 등의 모든 리소스를 표현하는 객체
// 기존 문자열 형식의 경로에서, file://~ 프로토콜로 시작하는 URI 형식으로 변경됨.
Resource resource = null;
try {
	resource = new UrlResource(filePath.toUri());
} catch (MalformedURLException e) {
	throw new IORuntimeException("Path -> Resource로 변경 중 URI가 잘못 생성되었습니다.");
}
```

코드 3-3. 

3) 해당 파일의 MIME-TYPE, 즉 어떤 타입인지 판별한다. 만약 이 정보를 읽어오지 못한다면 Content-Type을 “application/octet-stream”으로 설정한다. 이 타입은 브라우저가 해석하지 못하는 타입으로, 브라우저가 해석하지 못하는 컨텐트 타입은 무조건 다운로드 시키는 특성이 있는데, 이를 이용한 것이다. 

```java
// 파일의 MIME-TYPE 정보 파악 후 타입 결정 (이미지인지 동영상인지 등을 판별)
String contentType = null;
try {
	contentType = Files.probeContentType(filePath);
} catch (IOException e) {
	contentType = "application/octet-stream";
}
		
// 만약 MIME-TYPE을 읽어오지 못한다면 다운로드 작업 수행.
if (contentType == null) {
	// 해당 타입은 브라우저가 해석하지 못하는 타입이다. 
	// 브라우저가 해석하지 못하는 컨텐트 타입은 무조건 다운로드를 실행하는 
	// 특성이 있으니 이를 이용하는 것이다. 
	contentType = "application/octet-stream";
}
```

코드 3-4.

4) 파일명을 추출하여 앞서 얻은 컨텐트 타입, Resource 객체 정보와 함께 ResponseEntity 객체에 실어 클라이언트에 응답하는 로직을 구성한다. 

```java
// 파일의 원래 이름 가져오기
String originalFileName = null;
try {
	// 파일명에 한글이 포함된 경우 다운로드 도중 예외가 발생하므로 이를 utf-8로 인코딩한다. 
	originalFileName = URLEncoder.encode(filePath.getFileName().toString(), "utf-8");
} catch (UnsupportedEncodingException e) {
	log.error("잘못된 인코딩 방식입니다.");
	originalFileName = filePath.getFileName().toString();
}
		
// CONTENT_DISPOSITION : 해당 파일이 브라우저에서 열리지 않도록 함.
return ResponseEntity.ok()
	.contentType(MediaType.parseMediaType(contentType))
	.header(HttpHeaders.CONTENT_DISPOSITION, "attachment;filename=\"" + originalFileName + "\"")
	.body(resource);
```

코드 3-5.

파일명에 한글이 포함된 경우 다운로드 기능이 실행되지 않는다. 따라서 `URLEncoder.encode()` 메서드를 통해 파일명을 “utf-8”로 인코딩하는 작업을 추가하였다. 

한 편, HTTP Header에는 “Content-Disposition”이라는 header가 있는데, 이 값을 “attachment; filename=”filename.jpg”와 같이 지정하면 해당 파일을 다운로드하게끔 설정할 수 있다. 위 코드에서는 이를 적용한 것이다. 

위와 같이 구현한 기능을 Swagger에서 테스트해보면 다음과 같다.

![사진 3-1. Swagger에서 파일 다운로드](/images/2025-01-28/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20%EA%B8%B0%EB%8A%A5%20%EA%B5%AC%ED%98%84/6.png)

사진 3-1. Swagger에서 파일 다운로드

위 사진에서 파란 글씨의 “Download file”을 클릭하면 파일을 다운로드받을 수 있다. 

프론트엔드에서는 단순히 다음의 코드만 추가하면 해당 링크를 통해 파일을 다운로드받을 수 있게 된다. 

```jsx
<a href={`http://localhost:8080/files/download/${fileInfo.id}`}>다운로드</a>
```

코드 3-6. 파일 다운로드 REST API를 호출하는 코드. 

# 글을 마치며

파일 업로드 및 다운로드 기능 구현을 위한 핵심 정보는 이 글에서 모두 설명하였다. 사실 개념적으로는 살펴볼 게 없어 거의 코드로만 설명이 이루어졌다. 이와 더불어 회원 정보 수정 시 파일 경로 및 DB 정보 갱신하는 방법과 회원 탈퇴 시 해당 회원의 모든 파일들을 삭제하는 추가적인 기능에 대해서는 여기서는 글이 길어진 관계로 별도의 글에서 다루도록 하겠다. 

---

References

[1] 에이콘아카데미(강남) 강의

[2] 각각 로컬 서버와 AWS S3 서버에서 사용할 파일 업로드, 다운로드 기능을 구현한 예. 이와 더불어 서비스 계층에서 인터페이스를 선언한 뒤 이를 구현 클래스에서 구현하도록 서비스 인터페이스를 두는지에 대한 개인적 의문을 풀어준 글. 

[[Spring Boot] 파일 업로드 / 다운로드](https://velog.io/@asws1457/Spring-Boot-%ED%8C%8C%EC%9D%BC-%EC%97%85%EB%A1%9C%EB%93%9C-%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C)

[3] 스프링 공식 사이트에서 소개한 파일 업로드 기능 구현 방법

[Getting Started \| Uploading Files](https://spring.io/guides/gs/uploading-files)

[4] StringUtils라는 유용한 기능

[[Spring Boot]FileUpload 구현시 필요한 REST API의 개념과 적용방법](https://wjrmffldrhrl.github.io/REST-API/)

[5] 여러 파일 업로드 요청 시 처리.

[파일 업로드, 다운로드, 이미지 미리보기 구현(Spring boot With React)](https://khdscor.tistory.com/125)

[6] swagger에서 파일 업로드 테스트 관련 

[Spring - MissingServletRequestPartException 원인과 해결법](https://green-bin.tistory.com/44)

[7] swagger에서 파일 업로드 테스트 관련 - `@RequestPart` 어노테이션

[RequestBody vs RequestPart vs RequestParam vs ModelAttribute](https://middleearth.tistory.com/35)

[8] swagger에서 파일 업로드 테스트 관련 - `@RequestPart` 어노테이션

[[Spring Boot] @RequestParam vs @RequestPart](https://somuchthings.tistory.com/160)

[9] swagger에서 파일 업로드 테스트 관련 - `@RequestPart` 어노테이션

[Swagger에서 MultipartFile과 DTO 한 번에 받는 @RequestPart 요청을 실행할 수 있도록 만들기](https://velog.io/@sckwon770/Swagger%EC%97%90%EC%84%9C-MultipartFile%EA%B3%BC-DTO-%ED%95%9C-%EB%B2%88%EC%97%90-%EB%B0%9B%EB%8A%94-RequestPart-%EC%9A%94%EC%B2%AD%EC%9D%84-%EC%8B%A4%ED%96%89%ED%95%A0-%EC%88%98-%EC%9E%88%EB%8F%84%EB%A1%9D-%EB%A7%8C%EB%93%A4%EA%B8%B0)

[10] swagger에서 파일 업로드 테스트 관련

[[Spring boot] Swagger Spring doc에서 API Request의  Content-Type(multipart/form-data)이 지정되지 않는 문제](https://velog.io/@jsb100800/Spring-boot-Swagger-issue1)

[11] webMvcConfigurer.addResourceHandlers

[WebMvcConfigurer (Spring Framework 6.2.2 API)](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/web/servlet/config/annotation/WebMvcConfigurer.html#addResourceHandlers(org.springframework.web.servlet.config.annotation.ResourceHandlerRegistry))

[12] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[13] content-disposition, attachment

[Content-Disposition - HTTP \| MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Disposition)