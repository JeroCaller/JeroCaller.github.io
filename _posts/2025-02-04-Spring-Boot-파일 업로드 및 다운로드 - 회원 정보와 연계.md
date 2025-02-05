---
title: "[Spring Boot] 파일 업로드 및 다운로드 - 회원 정보와 연계"
category: "Spring"
tag: ["Spring", "Spring Boot", "파일 업로드 및 다운로드", "업로드", "다운로드", "react"]
---

이전에 작성한 [[Spring Boot] 파일 업로드 및 다운로드 기능 구현하기](/spring/Spring-Boot-%ED%8C%8C%EC%9D%BC-%EC%97%85%EB%A1%9C%EB%93%9C-%EB%B0%8F-%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C-%EA%B8%B0%EB%8A%A5-%EA%B5%AC%ED%98%84/) 글에서는 스프링 부트로 REST API 제작 시 파일 업로드 및 다운로드 기능을 구현하는 방법에 대해서 소개하였다. 여기서는 회원 정보 수정 및 삭제에 따른 파일 정보 수정 및 삭제 연계에 대해 살펴보겠다. 여기서 다루는 예제 사이트 및 코드들의 전체 소스 코드 및 그 외 개발 환경 정보들은 이전 글에서 확인할 수 있으니 참고. 

# 회원 정보 수정 시 파일 경로와 DB 정보도 수정하기

지난 글에서 언급했지만, 필자는 회원 정보를 담는 테이블을 구성할 때 회원의 닉네임에 PK를 걸었다. 고유 번호를 PK로 하지 않은 이유는 실제로 회원 기능을 구현할 때 닉네임을 기준으로 데이터 검색할 일이 많을 거라고 생각하였고, 검색 빈도가 높은 컬럼에 PK을 적용함으로써 인덱스도 같이 적용하게끔 하여 검색 속도의 향상을 꾀하기 위함이었다. 그런데 막상 코딩해보니 이 설계가 오히려 불편하게 느껴졌었다. 회원 정보 수정 시 닉네임이 수정되면 회원 정보와 연계된 파일 테이블이 PK - FK 관계로 묶여 있었기에 닉네임을 먼저 수정할 수가 없다. 그래서 이 경우, 수정 후 닉네임을 포함한 정보를 먼저 회원 테이블에 삽입한 후, 기존 파일 정보들을 수정 후 회원 정보로 갱신한 뒤, 수정 전 닉네임이 담긴 회원 정보를 삭제해야 했다. 이 과정은 코드를 통해 후술할 것이다. 차라리 고유 번호를 PK로 두고 닉네임 컬럼을 따로 둔 다음, 닉네임 컬럼에 unique key를 걸어 인덱스를 적용하게끔 하는게 더 낫다는 생각이 들었다. 왜 이런 생각을 하게 되었는지는 아래 코드를 보면 알게 될 것이다. 

먼저, 회원 정보 수정을 위한 서비스 계층 코드는 다음과 같다. 

```java
// 생략...

@Service
@RequiredArgsConstructor
@Transactional
@Slf4j
public class MembersService {

	private final MembersRepository membersRepository;
	private final PasswordEncoder passwordEncoder;
	private final CustomUserDetailsService customUserDetailsService;
	private final AuthService authService;
	private final FileByMemberService fileByMemberService;
	private final SecurityContextLogoutHandler securityContextLogoutHandler;
	
	// 생략...
	
	/**
	 * 회원 정보 수정.
	 * 
	 * 패스워드의 경우, 수정 시 수정 전과 똑같은 비밀번호로 수정하려고 해도 
	 * 이 둘의 인코딩된 문자열은 서로 다르다. 따라서 닉네임, 비밀번호 
	 * 모두 수정 전과 똑같은 정보를 입력받아도 이를 반려할 방법이 없음. 
	 * 
	 * @param request - 새로 수정할 회원 정보
	 * @param pastUserInfo - 기존 회원 정보
	 * @return
	 * @throws IOException 
	 */
	public MembersHistory update(
		MembersRequest membersRequest, 
		HttpServletRequest request,
		HttpServletResponse response
	) throws 
		NotAuthenticatedUserException, 
		EntityNotFoundException, 
		IOException
	{
		
		// 현재 인증된 정보가 없으면 런타임 예외 발생하여 throw함.
		// 현재 인증된 사용자인지 판별과 동시에 기존 회원 정보 반환을 위한 코드.
		UserDetails pastUserInfo = AuthenticationUtils
			.getAuthedUserInfoAll();
		
		// 수정 전 회원 정보
		Members oldMember = membersRepository
			.findById(pastUserInfo.getUsername())
			.orElseThrow(() -> {
				String message = "조회된 수정 전 닉네임을 찾을 수 없습니다.";
				return new EntityNotFoundException(message);
			});
		
		// 수정 후 정보 삽입
		// 수정 후 패스워드는 암호화하여 DB에 저장한다.
		Members willBeUpdatedMember = Members.builder()
			.nickname(membersRequest.getNickname())
			.password(passwordEncoder.encode(membersRequest.getPassword()))
			.build();
		
		Members updatedMember = membersRepository.save(willBeUpdatedMember);
		
		// 수정 전 닉네임 정보를 가지고 있는 모든 파일들의 닉네임 정보를 수정 후 정보로 교체.
		fileByMemberService.updateUserInfoInFiles(oldMember, updatedMember);
		
		// 수정 전 정보 삭제
		// 수정 전후의 닉네임이 동일하다면 삭제하지 않는다. 
		if (!oldMember.getNickname().equals(updatedMember.getNickname())) {
			membersRepository.delete(oldMember);
		}
		
		// 세션에 저장된 인증 정보를 수정 후 정보로 갱신
		authService.login(membersRequest, request, response);
		
		return MembersHistory.builder()
			.pastNickname(pastUserInfo.getUsername())
			.newNickname(updatedMember.getNickname())
			.build();
		
	}
	
	// 생략...
	
}
```

코드 1-1. MembersService.update

위 코드에서 핵심만 살펴보면 다음과 같다. 

1) 수정 전 회원 Entity를 DB로부터 검색하고, 수정 후 회원 Entity를 생성하여 영속화한다. 

```java
// 현재 인증된 정보가 없으면 런타임 예외 발생하여 throw함.
// 현재 인증된 사용자인지 판별과 동시에 기존 회원 정보 반환을 위한 코드.
UserDetails pastUserInfo = AuthenticationUtils
	.getAuthedUserInfoAll();
		
// 수정 전 회원 정보
Members oldMember = membersRepository
	.findById(pastUserInfo.getUsername())
	.orElseThrow(() -> {
		String message = "조회된 수정 전 닉네임을 찾을 수 없습니다.";
		return new EntityNotFoundException(message);
	});
		
// 수정 후 정보 삽입
// 수정 후 패스워드는 암호화하여 DB에 저장한다.
Members willBeUpdatedMember = Members.builder()
	.nickname(membersRequest.getNickname())
	.password(passwordEncoder.encode(membersRequest.getPassword()))
	.build();
		
Members updatedMember = membersRepository.save(willBeUpdatedMember);
```

코드 1-2. 코드 1-1의 일부.

2) 여기서 닉네임이 바뀐 경우, 닉네임 필드에 PK가 걸려있으므로, 자식 테이블에 해당하는 파일 데이터들의 FK 키 정보를 수정 후의 회원 데이터로 갱신해야 한다. 또한 필자는 사용자의 닉네임을 따온 서브 디렉토리를 통해 해당 회원이 그동안 업로드한 파일들을 관리하므로, 해당 서브 디렉토리명도 수정 후 닉네임으로 바꿔줘야 한다. 

```java
// 수정 전 닉네임 정보를 가지고 있는 모든 파일들의 닉네임 정보를 수정 후 정보로 교체.
fileByMemberService.updateUserInfoInFiles(oldMember, updatedMember);
```

코드 1-3. 코드 1-2의 일부

3) 그 후, 수정 전 회원 정보를 DB에서 삭제한다. 이 때 수정 전후의 닉네임이 같다면 삭제하지 않는다.

```java
// 수정 전 정보 삭제
// 수정 전후의 닉네임이 동일하다면 삭제하지 않는다. 
if (!oldMember.getNickname().equals(updatedMember.getNickname())) {
	membersRepository.delete(oldMember);
}
```

코드 1-4. 코드 1-3의 일부

여기서 회원 정보, 그 중에서도 닉네임 수정으로 인해 DB 상의 파일 정보와, 회원 닉네임을 따온 서브 디렉토리의 이름을 바꾸는 로직은 다음과 같이 별도의 다른 서비스 코드에 작성하였다. 

```java
// 생략...

@Service
@RequiredArgsConstructor
@Transactional
@Slf4j
public class FileByMemberService {
	
	private final FileEntityRepository fileRepository;
	private final MembersRepository membersRepository;
	private final FilesLogging filesLogging;
	
	@Value("${file.upload-dir}")
	private String uploadBaseDir;
	
	private Path uploadBaseDirPath;
	
	@PostConstruct
	public void init() {
		// 파일 업로드를 위한 루트 디렉토리 경로 얻기.
		uploadBaseDirPath = Paths.get(uploadBaseDir);
	}
	
	/**
	 * 회원의 닉네임 수정 시 해당 유저가 보유한 모든 파일 DB 정보의 
	 * 닉네임 정보를 그에 맞게 바꾼다. 
	 * 
	 * @param oldMember
	 * @param newMember
	 * @throws IOException 
	 */
	public void updateUserInfoInFiles(Members oldMember, Members newMember) 
		throws IOException 
	{
		
		// 닉네임 정보가 바뀌지 않았다면 파일 정보 수정 작업도 불필요하므로 종료.
		if (oldMember.getNickname().equals(newMember.getNickname())) return;
		
		// 실제 디렉토리명을 새로운 유저 닉네임에 맞게 바꾼다.
		updateUserDirName(oldMember.getNickname(), newMember.getNickname());
		
		// DB 상에 저장되어 있는 파일의 경로 내에 있는 유저 닉네임을 딴 디렉토리명도 바꾼다.
		fileRepository.updateUserInfoWithPath(oldMember, newMember);
		
	}
	
	/**
	 * 파일을 저장하는 유저 닉네임을 딴 디렉토리명을 새로 바꾼 닉네임으로 변경.
	 * 
	 * 예) 닉네임이 kimquel1234 -> kimquel1111로 바뀐 경우,
	 * ./files/users/kimquel1234 -> ./files/users/kimquel1111로 변경.
	 * 
	 * @param oldName
	 * @param newName
	 * @return - 새로 생성된 회원 디렉토리의 전체 경로 (파일명 미포함).
	 * @throws IOException 
	 */
	private Path updateUserDirName(String oldName, String newName) 
		throws IOException 
	{
		
		Path original = Paths.get(oldName);
		Path fullPath = uploadBaseDirPath.resolve(original);
		log.info("원래 유저 디렉토리 경로: " + fullPath.toString());
		
		Path newNamedDirPath = fullPath.resolveSibling(newName);
		log.info("새로운 유저 디렉토리 경로: " + newNamedDirPath);
		
		// pathA.resolveSibling(pathB) : pathA로 지정된 
		// 경로의 부모 경로와 pathB를 합친 새로운 경로를 반환. 
		// 예) "./files/users/oh".resolveSibling("good")
		// => "./files/users/good"
		
		// 아래 코드는 디렉토리명을 다른 이름으로 바꾸는 기능을 함.
		
		// 참고 자료)
		// https://www.codejava.net/java-se/file-io/how-to-rename-move-file-or-directory-in-java
		// https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/nio/file/Files.html#move(java.nio.file.Path,java.nio.file.Path,java.nio.file.CopyOption...)
		// https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/nio/file/Path.html#resolveSibling(java.lang.String)
		return Files.move(fullPath, newNamedDirPath);
		
	}
	
	// 생략...
	
}

```

코드 1-5. FileByMemberService.updateUserInfoInFiles

위 코드도 하나씩 살펴보면 다음과 같다. 

1) 업로드될 파일들을 저장하는 루트 디렉토리 경로를 얻어온다.

```java
@Value("${file.upload-dir}")
private String uploadBaseDir;
	
private Path uploadBaseDirPath;
	
@PostConstruct
public void init() {
	// 파일 업로드를 위한 루트 디렉토리 경로 얻기.
	uploadBaseDirPath = Paths.get(uploadBaseDir);
}
```

코드 1-6. 코드 1-5의 일부.

2) 회원의 닉네임 수정으로 인해 기존 닉네임을 가지는 서브 디렉토리명을 수정 후 닉네임으로 변경하고, DB 상에서도 바뀐 파일 경로와 FK로 참조하는 회원 정보를 새로 반영한다. 

```java
public void updateUserInfoInFiles(Members oldMember, Members newMember) 
	throws IOException 
{
		
	// 닉네임 정보가 바뀌지 않았다면 파일 정보 수정 작업도 불필요하므로 종료.
	if (oldMember.getNickname().equals(newMember.getNickname())) return;
		
	// 실제 디렉토리명을 새로운 유저 닉네임에 맞게 바꾼다.
	updateUserDirName(oldMember.getNickname(), newMember.getNickname());
		
	// DB 상에 저장되어 있는 파일의 경로 내에 있는 유저 닉네임을 딴 디렉토리명도 바꾼다.
	fileRepository.updateUserInfoWithPath(oldMember, newMember);
		
}
```

코드 1-7. 코드 1-5의 일부. 

위 코드에서 회원의 서브 디렉토리명을 바꾸는 코드는 `updateUserDirName(oldMember.getNickname(), newMember.getNickname());` 이고, 바뀐 파일 경로와 회원 정보를 DB에 새로 반영하는 코드는 `fileRepository.updateUserInfoWithPath(oldMember, newMember);` 에서 진행된다. 

먼저, 실제 회원 닉네임을 딴 서브 디렉토리의 이름을 수정 후 닉네임으로 바꾸는 로직은 다음과 같다.

```java
private Path updateUserDirName(String oldName, String newName) 
	throws IOException 
{
		
	Path original = Paths.get(oldName);
	Path fullPath = uploadBaseDirPath.resolve(original);
	log.info("원래 유저 디렉토리 경로: " + fullPath.toString());
		
	Path newNamedDirPath = fullPath.resolveSibling(newName);
	log.info("새로운 유저 디렉토리 경로: " + newNamedDirPath);
		
	// pathA.resolveSibling(pathB) : pathA로 지정된 
	// 경로의 부모 경로와 pathB를 합친 새로운 경로를 반환. 
	// 예) "./files/users/oh".resolveSibling("good")
	// => "./files/users/good"
		
	// 아래 코드는 디렉토리명을 다른 이름으로 바꾸는 기능을 함.
		
	// 참고 자료)
	// https://www.codejava.net/java-se/file-io/how-to-rename-move-file-or-directory-in-java
	// https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/nio/file/Files.html#move(java.nio.file.Path,java.nio.file.Path,java.nio.file.CopyOption...)
	// https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/nio/file/Path.html#resolveSibling(java.lang.String)
	return Files.move(fullPath, newNamedDirPath);
		
}
```

코드 1-8. 코드 1-5의 일부.

수정 전 닉네임을 통해 해당 회원의 파일들이 담긴 서브 디렉토리 경로를 얻어오고, 이를 수정 후 닉네임으로 서브 디렉토리명을 바꾸는 과정을 거치고 있다. 

## clearAutomatically VS flushAutomatically

한 편, 회원 정보 수정으로 인한 DB 내 파일 정보 수정 코드는 `fileRepository.updateUserInfoWithPath(oldMember, newMember);` 이라고 하였는데, 해당 메서드는 다음과 같이 작성하였다. 

```java
// 생략...

public interface FileEntityRepository 
	extends JpaRepository<FileEntity, Integer> {
	
	// 생략...
	
	@Modifying(flushAutomatically = true)
	@Query("""
		UPDATE FileEntity f
		SET f.filePath = FUNCTION(
			'REPLACE',
			f.filePath, 
			:#{#oldMember.nickname}, 
			:#{#newMember.nickname}
		),
		f.members = :#{#newMember} 
		WHERE f.members = :#{#oldMember}
	""")
	void updateUserInfoWithPath(
		@Param("oldMember") Members oldMember,
		@Param("newMember") Members newMember
	);
	
}

```

코드 2-1. FileEntityRepository.updateUserInfoWithPath

위 코드에서는 서브 디렉토리명의 변경으로 인해 파일 경로를 새로 갱신하는 기능과 FK 키를 새 회원 정보로 갱신하는 코드가 들어가 있다. 여기서 주목해볼만한 점 중 하나는 `@Modifying(flushAutomatically = true)` 의 `flushAutomatically` 속성이다. 이 속성을 사용하지 않고 코드를 실행하면 다음의 에러 메시지가 뜨면서 실행되지 않는다.

```java
java.sql.SQLIntegrityConstraintViolationException: Cannot add or update a child row: a foreign key constraint fails
```

이는 부모 테이블에 없는 데이터로 자식 테이블의 FK 컬럼을 갱신하려고 했기 때문에 발생한 일이다. 이는 분명 이상한 일이다. 분명 앞선 코드 1-2에서 수정 후 회원 Entity까지 영속화했음에도 이런 에러가 뜨기 때문이다. 사실 이는 코드 1-2에서 수행한 수정 후 회원 Entity가 영속성 컨텍스트(Persistence Context)에만 존재하고 실제 DB에까지 저장되지는 않았기에 발생한 에러이다. 즉 아직까지 DB에는 수정 후의 회원 정보가 부모 테이블에 들어가 있지 않은 상태인 것이다. 이 상태에서 코드 2-1의 UPDATE문을 실행하려고 하니 당연히 부모 테이블에 없는 데이터를 FK로 변경하는 셈이기에 에러가 발생하는 것이다. 따라서 이 UPDATE문을 실행하기 전에 영속성 컨텍스트에 존재하는 정보들을 실제 DB에 먼저 반영해야만 하는데, 이를 수행하는 것이 `flushAutomatically = true` 속성이다. 실제로 해당 속성에 대해 다음과 같은 설명이 존재한다. 

> Defines whether we should flush the underlying persistence context before executing the modifying query.
> 

즉, 데이터를 변경하는 쿼리문을 실행하기 전에 영속성 컨텍스트 내부의 정보들을 DB에 먼저 저장할 것인지를 설정하는 속성인 것이다. 

이와 비슷한 속성으로 `clearAutomatically` 라는 속성도 있다. 역시 같은 `@Modifying` 어노테이션에서 사용할 수 있다. 해당 속성의 설명은 다음과 같이 되어 있다. 

> Defines whether we should clear the underlying persistence context after executing the modifying query.
> 

즉, 해당 속성은 데이터 변경 쿼리문을 실행한 후에 영속성 컨텍스트 내부를 비울 것인가를 설정하는 속성인 것이다. 사실 이 속성에 대해서는 이전 블로그 글에서 자세히 설명한 적이 있다. 

[[Spring Boot] Spring Data JPA](/spring/Spring-Data-Jpa/#bulk-%EC%97%B0%EC%82%B0%EA%B3%BC-modifying)

JPQL을 이용하여 UPDATE, DELETE과 같은 데이터 변경 작업을 수행하면 변경 사항에 해당하는 여러 데이터들을 한꺼번에 직접 DB에서 변경하는 bulk 연산이 이루어진다. 이 작업은 영속성 컨텍스트를 무시하므로, bulk 연산이 일어난 이후에는 실제 DB 내 데이터와 영속성 컨텍스트 내 내용이 달라진다. 이 상태에서 JPA를 이용하여 데이터를 조회하려고 하면 영속성 컨텍스트에는 변경 전 데이터가 그대로 있으므로 이를 그대로 가져오게 된다. 이러한 문제점을 방지하기 위해서는 bulk 연산 후에 영속성 컨텍스트 내부를 비워줘야 그제서야 DB로부터 새로 반영된 데이터를 가져와 조회할 수 있게 되는데, 이를 가능케하는 속성이 바로 clearAutomatically 속성인 것이다. 

## Function In JPQL

코드 2-1에서 볼 수 있는 또 다른 사실은 `FUNCTION()` 함수를 사용했다는 것이다. 파일 경로 중 일부만 변경된 것이므로 이를 손쉽게 반영하기 위해 `replace()` 라는 database function 중 하나를 사용하려고 하였다. 그런데 이 함수는 JPQL에서는 기본적으로 제공하지 않는다. 따라서 이러한 database function을 JPQL에서도 사용할 수 있게 해주는 `function()`이라는 함수를 사용한 것이다. 해당 함수의 첫 번째 인자로는 실행하고자 하는 database function의 이름을 문자열로 입력한다. 그리고 두 번째 인자부터는 첫 번째 인자로 입력한 database function에서 요구하는 인자들을 순서대로 대입하면 된다. `replace()` 함수는 `replace(변경하고자 하는 컬럼명, 변경전 문자열, 변경 후 문자열)` 이렇게 인자들이 구성되어 있는데 이를 그대로 `function()` 함수에 두 번째 인자부터 작성하면 되는 것이다. 

이렇듯 JPQL은 SQL과 비슷해보이나 다른 점도 많은데, 그 중 하나가 SQL에서는 지원하나 JPQL에서는 지원하지 않는 키워드 또는 함수들이다. SQL에서만 사용 가능한 database function의 경우에는 위와 같이 `function()` 함수를 이용해야 JPQL 내부에서도 그대로 사용할 수 있다. JPQL에서 지원하는 키워드 및 함수들에 대한 정보는 다음의 사이트에서 자세히 볼 수 있다.

[Java Persistence/JPQL - Wikibooks, open books for an open world](https://en.wikibooks.org/wiki/Java_Persistence/JPQL)

## 회원 정보 수정 실행 결과

위와 같이 백엔드에서 구현한 회원 정보 수정 API을 호출하여 사용할 프론트엔드측 핵심 로직은 다음과 같다.

```jsx
import { useRef, useState } from "react";
import { useDispatch } from "react-redux";
import { useNavigate } from "react-router-dom";
import axios from "axios";

import * as utils from '../../utils/utils';
import actionTypes from "../../redux/actions";

/**
 * 회원 정보 수정 화면.
 * 
 * @returns 
 */
const ChangeProfile = ({ userInfo }) => {

  const form = useRef(null);
  const navigator = useNavigate();

  // 회원 정보 수정 시 이를 redux store에도 반영.
  const userInfoDispatch = useDispatch();

  // 사용자에게 화면에 보여줄 메시지.
  const [ message, setMessage ] = useState('');

  /**
   * 수정된 회원 정보를 서버에 등록.
   * 
   * @param {Event} event 
   */
  const handleUpdateUserInfo = (event) => {

    const formData = new FormData(form.current);
    const requestData = utils.extractJsonFromFormData(formData);

    // 유효성 검사
    // 아이디 공백 비허용
    if (requestData.nickname.trim() === '') {
      setMessage("아이디 공백은 비허용입니다.");
      return;
    }

    // 패스워드 공백 비허용
    if (requestData.password.trim() === '') {
      setMessage("패스워드 공백은 비허용입니다.");
      return;
    }

    setMessage(''); // 메시지 초기화.

    if (!window.confirm("정말 수정하시겠습니까?")) {
      alert("수정 취소되었습니다.");
      return;
    }

    axios.put("http://localhost:8080/members", requestData)
      .then(response => {
        if (utils.isSuccessHttpStatusCode(response.status)) {
          const responseData = response.data.data;
          
          // 새로 수정된 닉네임으로 redux store에 새로 저장.
          userInfoDispatch({
            type: actionTypes.STORE_AUTH,
            payload: { 
              nickname: responseData.newNickname, 
              loggedIn: true
            }
          });

          alert(`회원 정보 수정 완료!\n닉네임: ${responseData.newNickname}`);
          navigator("/mypage");
        }
      })
      .catch(error => {
        const expectedStatus = [
          utils.httpStatusMessages.UNAUTHORIZED,
          utils.httpStatusMessages.NOT_FOUND,
          utils.httpStatusMessages.INTERNAL_SERVER_ERROR,
        ];

        if (error.status in expectedStatus) {
          setMessage(`회원 정보 수정 실패. 에러 원인)\n ${error.response.data.message}`);
        } else {
          utils.defaultAxiosErrorHandler(error);
        }

      });

  };

  return (
    <div className="display-area">
      <h2>회원 정보 수정</h2>
      <form ref={form}>
        <ul>
          <li>
            <label htmlFor="nickname">닉네임: </label>
            <input type="text" 
              id="nickname" 
              name="nickname" 
              defaultValue={ userInfo ? userInfo.nickname : '???'}
              required="required" 
            />
          </li>
          <li>
            <label htmlFor="password">새 패스워드: </label>
            <input type="password" 
              id="password"
              name="password"
              required="required"
            />
          </li>
        </ul>
        <button type="button" onClick={handleUpdateUserInfo}>회원 정보 수정</button>
      </form>
      <div className="display-message">
        { message }
      </div>
    </div>
  );
};

export default ChangeProfile;
```

코드 3-1. ChangeProfile.jsx

회원 닉네임을 수정하기 전 각각 사이트와 DB에서의 모습, 그리고 파일 디렉토리의 모습은 다음과 같다.

![사진 1-1. 닉네임 수정 전 사이트 화면.](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/1.png)

사진 1-1. 닉네임 수정 전 사이트 화면.

![사진 1-2. 닉네임 수정 전 DB 내 회원 테이블.](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/2.png)

사진 1-2. 닉네임 수정 전 DB 내 회원 테이블.

![사진 1-3. 닉네임 수정 전 DB 내 파일 테이블.](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/3.png)

사진 1-3. 닉네임 수정 전 DB 내 파일 테이블.

![사진 1-4. 닉네임 수정 전 로컬 서버 파일 디렉토리 모습.](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/4.png)

사진 1-4. 닉네임 수정 전 로컬 서버 파일 디렉토리 모습.

그리고 회원 정보 수정 화면에서 닉네임을 변경하면 다음과 같이 변한다. 

![사진 1-5. 회원 정보 수정 화면에서 닉네임을 abcisbasic1122에서 abcisbasic2233으로 바꾸고 있다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/5.png)

사진 1-5. 회원 정보 수정 화면에서 닉네임을 abcisbasic1122에서 abcisbasic2233으로 바꾸고 있다. 

![사진 1-6. 닉네임 수정 후 마이 페이지 화면. 자세히 보면 변경된 아이디와 파일 경로 내 서브 디렉토리명이 바뀐 것을 볼 수 있다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/6.png)

사진 1-6. 닉네임 수정 후 마이 페이지 화면. 자세히 보면 변경된 아이디와 파일 경로 내 서브 디렉토리명이 바뀐 것을 볼 수 있다. 

![사진 1-7. 수정 후의 닉네임이 DB 내 회원 테이블에 반영되었다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/7.png)

사진 1-7. 수정 후의 닉네임이 DB 내 회원 테이블에 반영되었다. 

![사진 1-8. 수정된 닉네임이 DB 내 관련 파일 정보에도 반영되었다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/8.png)

사진 1-8. 수정된 닉네임이 DB 내 관련 파일 정보에도 반영되었다. 

![사진 1-9. 업로드된 파일들을 관리하는 해당 사용자의 디렉토리명도 바뀐 닉네임으로 수정되었다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/9.png)

사진 1-9. 업로드된 파일들을 관리하는 해당 사용자의 디렉토리명도 바뀐 닉네임으로 수정되었다. 

# 회원 탈퇴 시 해당 회원의 모든 파일 삭제하기

회원 탈퇴를 위한 서비스 코드는 다음과 같다.

```java
public MembersResponse unregister(
	HttpServletRequest request, 
	HttpServletResponse response
) throws 
	NotAuthenticatedUserException, 
	EntityNotFoundException, 
	DirectoryNotEmptyException, 
	DirectoryDeleteFailedException 
{
		
	// 현재 인증된 사용자 정보 없을 시 예외 throw하고 종료됨.
	UserDetails currentUserInfo = AuthenticationUtils.getAuthedUserInfoAll();
		
	// 회원이 보유한 모든 파일 삭제
	fileByMemberService.deleteAllFilesByUsername(currentUserInfo.getUsername());
		
	// 회원 정보 DB 상에서 삭제
	membersRepository.deleteById(currentUserInfo.getUsername());
		
	// 세션에 저장되어 있는 인증 정보 무효화.
	securityContextLogoutHandler.logout(
		request, 
		response, 
		AuthenticationUtils.getAuth()
	);
		
	return MembersResponse.builder()
		.nickname(currentUserInfo.getUsername())
		.build();
		
}
```

코드 4-1. MembersService.unregister

회원 정보를 DB 상에서 삭제하기 전, 해당 회원이 보유한 파일을 디렉토리 상에서 삭제하고, DB 상에서도 삭제해야 한다. 이는 위 코드 4-1에서 `fileByMemberService.deleteAllFilesByUsername(currentUserInfo.getUsername());` 을 호출하여 실행하고 있다. 해당 메서드의 코드는 다음과 같다. 

```java
public void deleteAllFilesByUsername(String username) throws 
	EntityNotFoundException, 
	DirectoryNotEmptyException,
	DirectoryDeleteFailedException
{
		
	filesLogging.clearAll();
		 
	// 유저 닉네임으로 파일들을 찾아 삭제하기 위해 
	// repository로부터 해당 닉네임을 가지는 회원 정보 Entity를 가져온다. 
	Members member = membersRepository.findById(username)
		.orElseThrow(() -> new EntityNotFoundException(
			"파일 삭제 작업 전 예외 발생: 해당 유저가 DB 상에 존재하지 않음. 유저 정보: " + username
		));
		
	List<FileEntity> files = fileRepository.findByMembers(member);
		
	// 유저의 파일이 없을 경우 파일 삭제 작업은 불필요하므로 여기서 종료.
	if (files == null || files.size() == 0) return;
		 
	for (FileEntity file: files) {
		Path filePath = Paths.get(file.getFilePath());
			
		// 먼저 서버 내에 물리적으로 존재하는 파일들을 모두 삭제한다.
		try {
			Files.deleteIfExists(filePath);
		} catch (IOException e) {
			// 다른 파일들의 삭제를 위해 
			// 예외가 발생해도 다음 파일로 넘어가도록 함.
			// 대신 실패한 파일 경로 및 원인을 기록.
			filesLogging.getFailedPaths().put(
				file.getFilePath(), 
				"물리적 파일 삭제 실패. \n" + e.getMessage()
			);
			continue;
		}
			
		// DB 내 해당 파일 정보들을 삭제 
		try {
			fileRepository.delete(file);
		} catch (Exception e) {
			filesLogging.getFailedPaths().put(
				file.getFilePath(),
				"DB 삭제 실패. \n" + e.getMessage()
			);
			continue;
		}
			
		// 여기까지 오면 파일의 물리적 삭제 및 DB 내 삭제 
		// 모두 성공으로 간주.
		filesLogging.getSucceedFileNames().add(file.getFilePath());
	}
		
	// 유저 닉네임으로 지어진 디렉토리 자체를 삭제.
	Path fullPath = uploadBaseDirPath.resolve(Paths.get(username));
	try {
		Files.deleteIfExists(fullPath);
	} catch (IOException e) {
		if (e instanceof DirectoryNotEmptyException) {
			throw new DirectoryNotEmptyException(
				"해당 유저 닉네임의 디렉토리 삭제 실패. 해당 디렉토리가 비어있지 않기 때문입니다."
			);
		} else {
			throw new DirectoryDeleteFailedException(e.getMessage());
		}
	}
		
}
```

코드 4-2. FileByMemberService.deleteAllFilesByUsername

위 코드를 정리하면 다음과 같다. 

1) 닉네임을 토대로 해당 회원 Entity 객체를 Repository로부터 가져온다.

```java
// 유저 닉네임으로 파일들을 찾아 삭제하기 위해 
// repository로부터 해당 닉네임을 가지는 회원 정보 Entity를 가져온다. 
Members member = membersRepository.findById(username)
	.orElseThrow(() -> new EntityNotFoundException(
		"파일 삭제 작업 전 예외 발생: 해당 유저가 DB 상에 존재하지 않음. 유저 정보: " + username
	));
```

코드 4-3. 코드 4-2의 일부.

2) 해당 회원이 보유한 모든 파일들의 Entity를 조회해 가져온다. 

```java
List<FileEntity> files = fileRepository.findByMembers(member);
```

코드 4-4. 코드 4-2의 일부. 

3) 파일 Entity에 저장되어 있는 파일 경로를 Path 객체로 변환한 후, 디렉토리 상에서 해당 파일을 삭제한다. 

```java
Path filePath = Paths.get(file.getFilePath());
			
// 먼저 서버 내에 물리적으로 존재하는 파일들을 모두 삭제한다.
try {
	Files.deleteIfExists(filePath);
} catch (IOException e) {
	// 다른 파일들의 삭제를 위해 
	// 예외가 발생해도 다음 파일로 넘어가도록 함.
	// 대신 실패한 파일 경로 및 원인을 기록.
	filesLogging.getFailedPaths().put(
		file.getFilePath(), 
		"물리적 파일 삭제 실패. \n" + e.getMessage()
	);
	continue;
}
```

코드 4-5. 코드 4-2의 일부

4) 파일이 디렉토리 상에서 삭제되었으므로 이제 DB 상에서도 해당 파일 정보를 삭제한다. 이러한 3~4번 과정을 모든 조회된 파일들에 대해 수행한다. 

```java
// DB 내 해당 파일 정보들을 삭제 
try {
	fileRepository.delete(file);
} catch (Exception e) {
	filesLogging.getFailedPaths().put(
	file.getFilePath(),
		"DB 삭제 실패. \n" + e.getMessage()
	);
	continue;
}
```

코드 4-6. 코드 4-2의 일부.

5) 마지막으로 회원 닉네임으로 지어진 파일 저장용 디렉토리도 삭제한다.

```java
// 유저 닉네임으로 지어진 디렉토리 자체를 삭제.
Path fullPath = uploadBaseDirPath.resolve(Paths.get(username));
try {
	Files.deleteIfExists(fullPath);
} catch (IOException e) {
	if (e instanceof DirectoryNotEmptyException) {
		throw new DirectoryNotEmptyException(
			"해당 유저 닉네임의 디렉토리 삭제 실패. 해당 디렉토리가 비어있지 않기 때문입니다."
		);
	} else {
		throw new DirectoryDeleteFailedException(e.getMessage());
	}
}
```

코드 4-7. 코드 4-2의 일부.

프론트엔드에서 이 기능을 호출하는 회원 탈퇴 기능은 다음과 같이 구현하였다. 

```javascript
import axios from "axios";
import { useState } from "react";
import { useNavigate } from "react-router-dom";

import * as utils from '../../utils/utils';

/**
 * 회원 설정 관련 위험 영역. 
 * 여기서는 회원 탈퇴 기능만 구현. 
 * 
 * @returns 
 */
const DangerZone = () => {

  const navigator = useNavigate();
  const [ message, setMessage ] = useState('');

  /**
   * 회원 탈퇴.
   * 
   * @param {Event} event 
   */
  const handleUnregister = (event) => {
    if(!window.confirm("정말로 탈퇴하시겠습니까?")) return;

    axios.delete("http://localhost:8080/members")
    .then(response => {
      if (utils.isSuccessHttpStatusCode(response.status)) {
        alert("회원 탈퇴하였습니다. 그동안 감사했습니다.");
        navigator("/"); // 메인 페이지로 이동.
      }
    })
    .catch(error => {
      const expectedStatus = [
        utils.httpStatusMessages.UNAUTHORIZED,
        utils.httpStatusMessages.NOT_FOUND,
      ];
      if (error.status in expectedStatus) {
        setMessage(`회원 탈퇴 실패. \n ${error.response.data.message}`);
      } else {
        utils.defaultAxiosErrorHandler(error);
      }
    });

  };

  return (
    <div className="display-area">
      <h3>Danger Zone</h3>
      <button type="button" onClick={handleUnregister}>회원 탈퇴</button>
      <div className="display-message">{message}</div>
    </div>
  );
};

export default DangerZone;
```

코드 4-8. DangerZone.jsx

이제 회원 탈퇴 기능과 해당 회원의 파일 전체 삭제 기능이 제대로 작동하는지 확인해보겠다. 다음은 회원 탈퇴전 모습이다.

![사진 2-1. 회원 탈퇴 전 마이 페이지](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/10.png)

사진 2-1. 회원 탈퇴 전 마이 페이지

![사진 2-2. 회원 탈퇴 전 회원 정보 DB](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/11.png)

사진 2-2. 회원 탈퇴 전 회원 정보 DB

![사진 2-3. 회원 탈퇴 전 탈퇴 대상인 abcisbasic2233이 보유한 전체 파일 정보가 DB 내에 있다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/12.png)

사진 2-3. 회원 탈퇴 전 탈퇴 대상인 abcisbasic2233이 보유한 전체 파일 정보가 DB 내에 있다. 

![사진 2-4. 회원 탈퇴 전 탈퇴 대상 회원의 파일들이 저장된 디렉토리.](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/13.png)

사진 2-4. 회원 탈퇴 전 탈퇴 대상 회원의 파일들이 저장된 디렉토리.

회원 탈퇴 후의 모습은 다음과 같다.

![사진 2-5. 회원 탈퇴 버튼을 눌러 회원 탈퇴를 해보았다.](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/14.png)

사진 2-5. 회원 탈퇴 버튼을 눌러 회원 탈퇴를 해보았다.

![사진 2-6. 회원 탈퇴 후의 회원 테이블. abcisbasic2233의 회원 정보가 삭제되었다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/15.png)

사진 2-6. 회원 탈퇴 후의 회원 테이블. abcisbasic2233의 회원 정보가 삭제되었다. 

![사진 2-7. 회원 탈퇴 후의 파일 테이블. abcisbasic2233 회원이 보유한 모든 파일 정보들이 삭제되었다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/16.png)

사진 2-7. 회원 탈퇴 후의 파일 테이블. abcisbasic2233 회원이 보유한 모든 파일 정보들이 삭제되었다. 

![사진 2-8. 회원 탈퇴 후의 디렉토리 구조. abcisbasic2233 회원의 디렉토리가 삭제되었다. ](/images/2025-02-04/Spring-Boot-%ED%8C%8C%EC%9D%BC%20%EC%97%85%EB%A1%9C%EB%93%9C%20%EB%B0%8F%20%EB%8B%A4%EC%9A%B4%EB%A1%9C%EB%93%9C%20-%20%ED%9A%8C%EC%9B%90%20%EC%A0%95%EB%B3%B4%EC%99%80%20%EC%97%B0%EA%B3%84/17.png)

사진 2-8. 회원 탈퇴 후의 디렉토리 구조. abcisbasic2233 회원의 디렉토리가 삭제되었다. 

이로써 정상적으로 회원 탈퇴 기능을 구현하였다. 

# 글을 마치며

앞선 회원 정보 수정 챕터에서 보았듯, 닉네임을 PK로 잡은 것과, 회원별로 디렉토리를 구분하는 방법을 택한 것 때문에 코드의 복잡성이 조금 더 늘어난 감이 없지 않아 있다. 닉네임을 PK로 잡으니 자식 테이블인 파일 테이블과의 참조 무결성을 지키기 위해 update문이 아닌 수정 후 정보를 먼저 insert한 후 수정 전 정보를 delete해야 하는 구조로 인해 회원 정보가 업데이트된 시각을 기록하는 기능이 쓸모없어지기도 했다. 따라서 고유 번호를 PK로 잡고, 닉네임은 Unique Key를 걸어 닉네임 중복 가입 방지 및 검색 속도 향상을 도모하면서 그와 동시에 앞서 보인 코드 복잡성을 줄이고, 회원 정보 수정 시각 기록 기능을 살리는 것이 더 좋은 방법으로 보인다. 

또한, 파일 디렉토리 구조의 경우, 회원 별로 디렉토리를 구분하여 파일을 저장하는 방식은 회원 정보 수정 시 디렉토리도 변경하는 작업도 동반되는데, 이를 위한 코드가 추가되어야 할 뿐만 아니라 경로도 같이 바뀌는 것과 마찬가지기에 파일의 양과 회원 수가 많을 수록 이는 컴퓨터 자원을 소모하는 작업이 될 것으로 보인다. 이 경우, AWS와 같은 클라우드 사이트에서 서비스할 경우 비용이 발생할 수도 있지 않을까, 란 생각도 든다. 따라서 디렉토리 구조는 그냥 회원 구분없이 하나의 루트 디렉토리 내에서 모든 파일들을 관리하는 게 더 좋을 것으로 보인다. 이는 어차피 DB를 통해 어떤 파일이 어떤 회원의 것인지를 판별할 수 있기에 가능한 일이다. 

그럼에도 이 글에서 기존 방식 그대로 보여준 것은 이러한 단점들을 통해 반면교사로서 배운 점들이 있기 때문이다. 어쨌건 파일 업로드 및 다운로드 기능을 실무에서 구현하게 되면 이러한 점들을 고려해야겠다. 

---

References

[1] replace() database function 및 해당 함수를 update문에 적용하는 법

[[SQL] 특정 문자만 UPDATE](https://coding-house.tistory.com/175)

[2] JPQL에 function() 적용

[[JPA] JPQL @Query에 각 DB function() 사용해보기](https://whitewise95.tistory.com/234)

[3] JPQL에 대한 상세 설명 문서. function()에 대한 설명도 있다. 

[Java Persistence/JPQL - Wikibooks, open books for an open world](https://en.wikibooks.org/wiki/Java_Persistence/JPQL)

[4] Files를 이용하여 파일 또는 디렉토리의 이름을 바꾸거나 이동하는 방법.

[How to rename/move file or directory in Java](https://www.codejava.net/java-se/file-io/how-to-rename-move-file-or-directory-in-java)

[5] Files.move() 설명 공식 문서.

[Java® Platform, Standard Edition & Java Development Kit Version 21 API Specification](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/nio/file/Files.html#move(java.nio.file.Path,java.nio.file.Path,java.nio.file.CopyOption...))

[6] Path.resolveSibling() 설명 공식 문서.

[Java® Platform, Standard Edition & Java Development Kit Version 21 API Specification](https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/nio/file/Path.html#resolveSibling(java.lang.String))