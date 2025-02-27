---
title: "[Spring Boot] 보안과 Spring Security"
category: "Spring"
tag: ["Spring", "Spring boot", "Filter", "Servlet", "인증", "인가", "보안", "Authentication", "Authorization", "Auth"]
---

웹 사이트 제작 시 고려해야할 요소 중 하나는 다름아닌 보안일 것이다. 권한이 없는 사용자가 다른 사용자의 정보에 함부로 접근, 조작하는 행위를 방지해야한다. 익명의 사용자가 특정 리소스에 접근하지 못하도록 하고자 할 때에는 현재 사용자에 대한 인증 기능도 필요할 것이다. 보안적인 측면 뿐만 아니라, 사용자 개인화된 서비스를 제공하기 위해선 우선 사용자가 누구인지부터 알아야 할 필요성도 있을 것이다. 

앞서 언급한 내용에는 이미 보안과 관련된 두 가지 용어를 설명하는 내용들이 나왔다. 인증과 인가이다.

- 인증 (Authentication) : 사용자가 누구인지 검증하는 단계로, 흔히 “로그인”이 대표적인 예이다.
    - 인증을 통해 익명의 사용자와 인증된 사용자로 나뉘므로, 익명의 사용자가 인증된 사용자만 이용할 수 있는 리소스에 대한 접근 거부를 할 수 있다. 이로 인해 애플리케이션 입장에서는 인증된 사용자는 신뢰할 수 있는 사용자라 볼 수 있다.
- 인가 (Authorization) : 인증을 거친 사용자가 특정 리소스에 접근할 권한을 가졌는지 검증하는 단계이다. 예를 들어 USER 권한을 가진 사용자는 ADMIN 권한을 가진 사용자들의 글들을 볼 수 없게끔 권한을 확인하는 것이 있겠다.

이와 더불어 한 가지 용어가 더 있다. 

- 접근 주체 (Principal) : 애플리케이션의 기능을 이용하는 주체로, 사용자, 디바이스, 시스템 등이 될 수 있다고 한다. 인증을 통해 신뢰할 수 있는 주체인지 검증받고, 인가를 통해 주체에게 부여된 권한을 확인하는 과정을 거친다.

# Spring Security 개념 및 원리

그런데 이러한 인증과 인가 과정을 개발자가 직접 구현하는 것은 그 난이도가 어렵다고 하며, 개발에 시간이 많이 들기도 하여 일일이 구현하는 방법은 매우 비효율적일 것이다. 그러나 스프링 기반 웹 애플리케이션을 제작한다면 Spring의 여러 하위 프로젝트들 중 하나로 Spring Security가 있어 이를 이용하면 손쉽게 인증과 인가 기능을 효율적으로 추가할 수 있다고 한다. Spring 공식 사이트에서도 Spring Security는 사실상 Spring 기반 애플리케이션의 보안 표준이라고도 하고 있다. 

스프링 공식 사이트에서는 Spring Security의 주요 특징으로 다음을 꼽고 있다. 

- 인증과 인가 기능을 쉽게 구현할 수 있도록 제공하고 있으며, 포괄적이고 확장 가능한 지원을 하기에 인증과 인가 과정에 대해 요구 사항에 맞는 커스텀도 가능하다고 한다.
- 인증, 인가 뿐만 아니라 CSRF(Cross Site Request Forgery) 등의 여러 보안적 공격도 방어할 수 있다고 한다.

## Spring Security의 기본적인 동작 구조 및 원리

Spring Security는 기본적으로 Servlet Filter를 기반으로 동작한다. 원래 Filter는 Spring Container 외부인 Servlet Container에 존재하기에 인증, 인가와 같은 보안 작업을 일반적인 Filter Chain 사이에 끼어넣어 처리하기 위해, 아래 그림 1-1와 같이 Spring에서는 DelegatingFilterProxy라는 필터 구현체를 제공하고 있다.

![그림 1-1. Spring Security의 필터 기반 동작 구조.](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/1.png)

그림 1-1. Spring Security의 필터 기반 동작 구조.

이 구현체는 Servlet Container 내의 필터 체인으로 들어온 요청을 Spring Container에서 관리하는 Spring Bean으로 구현된 Filter로 위임시키는 일종의 다리 역할을 수행한다. 이 덕분에 원래는 Spring Container 내에서 관리하는 Bean을 이용하여 요청을 가로채어 보안 관련 작업을 수행할 방법이 없었는데, DelegatingFilterProxy라는 존재 덕분에 이것이 가능해졌고, 이를 통해 Spring Bean으로 구현된 Filter 내에서 인증, 인가와 같은 보안 작업을 처리하는 것도 가능해진 것이다. 

위 그림을 보면 알 수 있듯, DelegatingFilterProxy 내에는 FilterChainProxy이라는 Bean 기반 Filter가 존재하여 실질적으로 보안 작업을 Spring Security에서 관리하는 SecurityFilterChain에 위임하는 기능을 한다. 이렇게 역할을 위임받은 SecurityFilterChain에서 여러 보안 관련 필터들을 통해 실질적으로 보안 작업을 처리하는 것이다. 이것이 Spring Security가 동작 가능하게 하는 원리인 것이다. 

한 편, FilterChainProxy는 Spring Boot를 사용하면 자동 구성(Auto-configuration)에 의해 자동으로 생성되기에 개발자가 직접 FilterChainProxy를 생성, 설정하는 작업을 하지 않아도 된다고 한다. 

이렇게 FilterChainProxy는 사실상 보안 관련 작업들의 시작점이 되기에 이러한 특징을 이용하면 다음의 작업들이 가능하다. 

- 여러 개의 SecurityFilterChain들이 존재할 경우, 어떤 Security Filter Chain에 보안 작업을 위임할지 결정하는 역할을 한다. Http Request의 요청 URI를 기반으로 판단하며, 이 URI과 매핑되는 첫 번째 SecurityFilterChain만 실행된다고 한다. 아래 참고 사진 1-1처럼 만약 “/api”라는 URI 요청이 들어오면 FilterChainProxy는 이 URI와 매핑될 SecurityFilterChain을 순차적으로 검색한다. 이 URI는 “/api/**”와 “/**” 모두 해당되지만, 맨 처음의 SecurityFilterChain이 “/api/**”와 매핑되어 있기에 해당 요청은 n번째 필터 체인이 아닌 0번째 필터 체인에서 처리되며, n번째 필터 체인으로 전달되지 않는다고 한다.
- Spring Security와 관련하여 어떠한 버그가 있어 트러블슈팅하고자 할 때 FilterChainProxy부터 디버그 작업을 수행하여 원인을 파악할 수 있다.
- 모든 SecurityFilter들에 대해, 메모리 누수(Memory leak) 방지를 위한 서버 내 저장된 인증 정보(SecurityContext) 삭제 작업 또는 공격을 방지하기 위한 방화벽(firewall) 설정 등 모든 보안 필터들에 공통적으로 특정 기능들을 적용할 수 있다.

![참고 사진 1-1. FilterChainProxy가 어떤 SecurityFilterChain에 보안 작업을 위임할지 결정하는 매커니즘. [https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-securityfilterchain](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-securityfilterchain) 참고함.](https://docs.spring.io/spring-security/reference/_images/servlet/architecture/multi-securityfilterchain.png)

참고 사진 1-1. FilterChainProxy가 어떤 SecurityFilterChain에 보안 작업을 위임할지 결정하는 매커니즘. [https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-securityfilterchain](https://docs.spring.io/spring-security/reference/servlet/architecture.html#servlet-securityfilterchain) 참고함.

공격 방지, 인증, 인가들의 작업들은 실질적으로 SecurityFilterChain을 구성하는 Security Filter들에서 처리하며, 여러 가지 작업들을 처리하기 위한 여러 가지의 Security Filter들이 제공된다. 한 편 기본적으로 Security Filter들에는 실행 순서가 정해져 있다고 한다. 예를 들면 CorsFilter가 CsrfFilter보다 앞에 있어 먼저 실행된다. Spring Security에서 제공하는 Security Filter에는 종류가 너무 많아 여기에 모든 Security Filter들의 실행 순서를 적기는 힘들다. 대신 다음의 사이트를 참고하면 되겠다.

[https://github.com/spring-projects/spring-security/blob/6.4.3/config/src/main/java/org/springframework/security/config/annotation/web/builders/FilterOrderRegistration.java](https://github.com/spring-projects/spring-security/blob/6.4.3/config/src/main/java/org/springframework/security/config/annotation/web/builders/FilterOrderRegistration.java)

다음은 위 사이트에 게재된 코드 일부이다.

```java
FilterOrderRegistration() {
	Step order = new Step(INITIAL_ORDER, ORDER_STEP);
	put(DisableEncodeUrlFilter.class, order.next());
	put(ForceEagerSessionCreationFilter.class, order.next());
	put(ChannelProcessingFilter.class, order.next());
	order.next(); // gh-8105
	put(WebAsyncManagerIntegrationFilter.class, order.next());
	put(SecurityContextHolderFilter.class, order.next());
	put(SecurityContextPersistenceFilter.class, order.next());
	put(HeaderWriterFilter.class, order.next());
	put(CorsFilter.class, order.next());
	put(CsrfFilter.class, order.next());
	put(LogoutFilter.class, order.next());
```

위 코드에 의하면 DisableEncodeUrlFilter → ForceEagerSessionCreationFilter → ChannelProcessingFilter 등의 순서대로 여러 Security Filter들이 실행된다는 것을 볼 수 있다. 이 필터들의 실행 순서들을 참고하면 개발자가 직접 만든 Security Filter를 어디에 위치해야 제대로 작동할지 결정할 수 있을 것이다. 예를 들면 기본으로 제공하는 Security Filter들 중에는 UsernamePasswordAuthenticationFilter라는 것이 있는데, 개발자가 만든 JWT(Json Web Token) 기반 인증 필터를 보통 이 필터 앞에 위치시킨다고 한다. (JWT 기반 인증에 대해서는 나중에 다른 글에서 다룰 예정)

## Spring Security에서의 인증 과정

앞서 Spring Security가 보안 관련 작업들을 어떻게 처리하는지에 대해 살펴보았으니, 이번에는 그 중 인증(Authentication)을 처리하는 과정에 대해 살펴보겠다. 사실 인증을 수행하는 방법에는 여러 가지가 있다고 하는데, 여기서는 간단하게 username과 password 기반 인증에 대해 살펴보겠다. 

![그림 2-1. username & password 기반 인증 처리 과정.](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/2.png)

그림 2-1. username & password 기반 인증 처리 과정.

1. 클라이언트로부터 인증을 위한 username, password 정보가 담긴 요청을 받으면, AuthenticationFilter에 위임된다. 
2. AuthenticationFilter 내부에서는 HttpServletRequest 객체로부터 username, password를 추출한 후, 이를 이용하여 인증용 토큰을 생성한다. 이는 UsernamePasswordAuthenticationToken이라는 객체를 이용하여 토큰화한다. 참고로 해당 객체는 Authentication이라는 인터페이스의 구현체이다. 
3. 이렇게 생성된 인증용 토큰을 AuthenticationManager라는 인터페이스에 전달한다. 일반적으로는 해당 인터페이스의 구현체인 ProviderManager라는 객체가 주로 사용된다.
4. ProviderManager는 전달받은 토큰을 다시 AuthenticationProvider에게 넘긴다. 참고로 인증 방법에는 username, password 기반 뿐만 아니라 OAuth, JWT 등 여러 가지가 있는데, 각각의 인증 방법을 각각의 AuthenticationProvider가 수행할 수 있다. 여기서는 username, password 기반 인증에 대해 설명하고 있으므로 당연히 이를 기반으로 인증을 수행할 AuthenticationProvider에게 해당 토큰이 전달될 것이다. 
5. AuthenticationProvider는 다시 해당 토큰을 UserDetailsService라는 서비스 객체에 전달하며, 해당 객체가 전달받은 토큰을 토대로 DB로부터 사용자의 username, password를 조회하여 이를 UserDetails 객체로 생성한 후, 다시 AuthenticationProvider에게 전달한다. 
6. UserDetails 객체를 전달받은 AuthenticationProvider에서 요청으로 들어온 사용자 정보와 UserDetails 객체 내에 담긴 정보를 비교하여 인증을 수행한다. 인증을 성공적으로 마치면 검증된 토큰을 다시 ProviderManager에게 전달한다. 
7. ProviderManager에서 AuthenticationFilter로 검증된 토큰을 전달한다.
8. 검증된 토큰을 전달받은 AuthenticationFilter는 이를 SecurityContextHolder 내부의 SecurityContext 내부에 Authentication 인터페이스의 형태로 저장한다. 이렇게 저장된 현재 사용자의 인증 정보는 서버 내에서 가져와 활용할 수도 있다. 

# 코드로 살펴보기

이제 코드를 통해 Spring Security를 사용하는 방법에 대해 살펴보겠다. Spring Security에서는 기본적으로 세션 기반 인증을 제공한다고 한다. 따라서 여기서는 세션 기반 인증으로 살펴보겠다. 이와 다른 인증 방식으로 토큰 기반 인증 방식이 있는데, JWT가 대표적 예이다. JWT 기반 인증에 대해서는 다른 글에서 다뤄보겠다. 

여기서 보일 코드는 다음을 기준으로 작성하였다. 

- Spring Data JPA
- Maria DB
- REST API

여기서는 Spring Security를 이용한 세션 기반 인증에 대해 초점을 맞춰 핵심만 설명할 것이라 코드의 많은 부분들을 생략할 것이다. 따라서 전체 소스 코드 및 DB 생성 쿼리문 등의 여러 정보들은 다음의 깃허브 repository를 참고하면 되겠다.

[JecaSpringTemplates/Eclipse/RestApi/TemplateType1 at master · JeroCaller/JecaSpringTemplates](https://github.com/JeroCaller/JecaSpringTemplates/tree/master/Eclipse/RestApi/TemplateType1)

Spring Security를 사용하기 위한 의존성은 gradle 기준으로 다음과 같다. 다음의 의존성을 굳이 build.gradle에 추가하지 않아도 Eclipse의 Spring Starter Project를 이용하면 이를 쉽게 추가할 수 있다. 

```java
implementation 'org.springframework.boot:spring-boot-starter-security'
```

그 후, 사용자를 나타내는 Member 엔티티 클래스를 다음과 같이 정의하였다.

```java
package com.example.data.entity;

import java.util.Arrays;
import java.util.Collection;

import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;

import com.example.common.UserRole;
import com.example.dto.request.TestMemberRequest;

import jakarta.persistence.Column;
import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import jakarta.persistence.Table;
import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.EqualsAndHashCode;
import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

@Entity
@Table(name = "tb_member")
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@ToString(callSuper = true)
@EqualsAndHashCode(callSuper = false, of = "username")
public class TestMember extends BaseEntity implements UserDetails {
	
	@Id
	@GeneratedValue(strategy = GenerationType.IDENTITY)
	@Column(length = 11, nullable = false, insertable = false)
	private int id;
	
	@Column(length = 20, nullable = false, unique = true, name = "nickname")
	private String username;
	
	@Column(length = 100, nullable = false)
	private String password;

	@Override
	public Collection<? extends GrantedAuthority> getAuthorities() {
		
		// 여기서는 유저 역할만 존재한다고 가정하고 유저 역할만 반환.
		GrantedAuthority userAuth = new SimpleGrantedAuthority(
			UserRole.USER.getRole()
		);
		
		return Arrays.asList(userAuth);
	}
	
	public static TestMember toEntity(TestMemberRequest memberRequest) {
		
		return TestMember.builder()
			.username(memberRequest.getUsername())
			.password(memberRequest.getPassword())
			.build();
	}
	
}

```

코드 1-1. TestMember.java

(위에선 TestMember라 하였으나 편의상 Test는 뺴고 Member라 칭하겠다.)

유저를 나타내는 Member 엔티티 클래스는 UserDetails 인터페이스를 구현하도록 하였다. 꼭 해당 인터페이스를 구현하도록 하지 않아도 되지만 인증된 사용자의 정보를 서버 내에서 좀 더 쉽게 사용하고자 한다면 해당 인터페이스를 구현하는게 더 편리할 것이다. 

UserDetails 인터페이스에 존재하는 메서드들로는 다음이 존재한다. (”default 여부”란에 Yes라 되어 있으면 반드시 구현하지 않아도 되는 메서드, No라 되어있으면 반드시 구현해야한다는 뜻)

| 메서드명 | 설명 | default 여부 |
| --- | --- | --- |
| getAuthorities() | 사용자의 권한 목록을 반환. 시큐리티 설정에서 특정 URI에 인가 설정 시 필요한 메서드. 해당 메서드를 오버라이딩할 때 역할명을 단순히 “USER”라 하지 않고 “ROLE_USER”와 같이 앞에 “ROLE_” 접두사를 붙여야 한다. 그래야 시큐리티에서 해당 역할을 제대로 인식할 수 있다.  | No |
| getPassword() | 사용자의 패스워드를 반환한다.  | No |
| getUsername() | 사용자의 이름을 반환한다. 유저 네임은 유저의 닉네임으로 설정할 수도 있고, 이메일로 설정할 수도 있다.  | No |
| isAccountNonExpired() | 해당 사용자 계정의 만료 여부를 반환. 반환값의 기본값은 true로, 만료되지 않았음을 의미 | Yes |
| isAccountNonLocked() | 계정이 잠겨있는지 여부를 반환. true 시 잠기지 않았다는 의미 | Yes |
| isCredentialNonExpired() | 비밀번호 만료 여부를 반환. true 시 만료되지 않음을 의미 | Yes |
| isEnabled() | 계정의 활성화 여부를 반환. true 시 활성화된 상태를 의미 | Yes |

그 다음, UserDetailsService 인터페이스 구현체를 다음과 같이 정의하였다.

```java
package com.example.business;

import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;
import org.springframework.stereotype.Service;

import com.example.data.entity.TestMember;
import com.example.data.repository.TestMemberRepository;

import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
public class CustomUserDetailsService implements UserDetailsService {
	
	private final TestMemberRepository testMemberRepository;

	@Override
	public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
		
		TestMember member = testMemberRepository.findByUsername(username)
			.orElseThrow(() -> new UsernameNotFoundException(
				username + "님은 존재하지 않습니다."
			));
		
		return member;
	}

}

```

코드 1-2. CustomUserDetailsService.java

UserDetailsService 인터페이스에는 username을 이용하여 user 정보를 DB로부터 가져오도록 하는 `loadUserbyUsername()` 추상 메서드를 제공하기에 이를 구현해야한다. 위 코드에서는 MemberRepository를 이용하여 username을 기반으로 하여 해당 사용자 정보를 검색하도록 하였다. 이렇게 검색된 사용자 정보는 UserDetails 객체로 반환된다. 앞서 Member 엔티티 클래스를 UserDetails 인터페이스의 구현체로 구현한 이유 중에 하나가 여기에 있다. 

그 다음으로 Spring Security 관련 설정 클래스를 다음과 같이 작성하였다. 사실 Spring Security의 핵심이 들어있는 클래스라 봐도 될 것이다. 

```java
package com.example.config;

import java.util.Arrays;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.config.annotation.authentication.configuration.AuthenticationConfiguration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.crypto.bcrypt.BCryptPasswordEncoder;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler;
import org.springframework.security.web.context.HttpSessionSecurityContextRepository;
import org.springframework.security.web.context.SecurityContextRepository;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;

/**
 * 스프링 시큐리티 설정 클래스. 
 * 
 * 세션 인증 기반.
 */
@Configuration
@EnableWebSecurity
public class SecurityConfig {
	
	/**
	 * 모든 사용자 접근을 허락할 URI 작성.
	 */
	private final String[] permitAllUris = {
		"/swagger-ui/**", 
		"/v3/api-docs",
		"/v3/api-docs/**", 
		"/swagger-ui.html",
		"/swagger-ui/index.html",
		"/test/**",
		"/auth/**"
	};
	
	/**
	 * AuthenticationManager는 인증 로직의 중심 역할을 하며, 요청을 처리한다.
	 * AuthenticationConfiguration은 Spring Security의 AuthenticationManager를 
	 * 설정 및 관리하는 도우미 역할을 한다.
	 * 
	 * @param authConfig
	 * @return
	 * @throws Exception 
	 */
	@Bean
	public AuthenticationManager authenticationManager(
		AuthenticationConfiguration authConfig
	) throws Exception {
		return authConfig.getAuthenticationManager();
	}
	
	/**
	 * REST API 제작 및 세션 인증 기반을 위한 시큐리티 설정.
	 * 
	 * @param httpSecurity
	 * @return
	 * @throws Exception
	 */
	@Bean
	public SecurityFilterChain securityFilterChain(
		HttpSecurity httpSecurity
	) throws Exception {
		
		httpSecurity
			.csrf(csrf -> csrf.disable())
			.cors(cors -> cors.configurationSource(corsConfigurationSource()))
			.authorizeHttpRequests(auth -> auth
				.requestMatchers(permitAllUris)
				.permitAll()  // 지정된 URL에 대한 접근은 모든 이들에게 허용.
				.anyRequest().authenticated() // 그 외 요청은 인증 필요.
			)
			.logout(logout -> logout
				.logoutUrl("/auth/logout")
				.deleteCookies("JSESSIONID")
				.invalidateHttpSession(true)  // 세션 무효화
				.clearAuthentication(true)  // 인증 정보 무효화
				.permitAll()
			);
		
		return httpSecurity.build();
	} 
	
	public CorsConfigurationSource corsConfigurationSource() {
		
		CorsConfiguration configuration = new CorsConfiguration();
		configuration.setAllowedOrigins(Arrays.asList("http://localhost:8080/", "http://localhost:3000"));
		configuration.setAllowedMethods(Arrays.asList("GET", "POST", "PUT", "PATCH", "DELETE"));
		configuration.setAllowedHeaders(Arrays.asList("*"));
		
		// 클라이언트로부터 인증 정보(credential) 전송 시
		// 이를 받도록 허용할 것인지 여부 설정. 
		// 여기 백엔드에서와 프론트엔드 둘 모두 true로 설정해야 둘 사이에서 인증 정보를 교환할 수 있다. 
		configuration.setAllowCredentials(true); 
		
		UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
		source.registerCorsConfiguration("/**", configuration);
		return source;
	}
	
	/**
	 * 사용자 패스워드 보호를 위한 패스워드 암호화 기능.
	 * 
	 * @return
	 */
	@Bean
	public PasswordEncoder passwordEncoder() {
		return new BCryptPasswordEncoder();
	}
	
	/**
	 * 인증 정보를 세션에 저장하기 위한 Bean.
	 * HttpSessionSecurityContextRepository은 
	 * SecurityContextRepository의 구현체이며, SecurityContext 객체 생성 후 
	 * 이 객체를 세션에 저장하는 역할을 한다. 또한 세션에 저장된 해당 객체를 
	 * 조회, 참조하기도 한다. 
	 * 
	 * @return
	 */
	@Bean
	public SecurityContextRepository securityContextRepository() {
		return new HttpSessionSecurityContextRepository();
	}
	
	@Bean
	public SecurityContextLogoutHandler getLogoutHandler() {
		return new SecurityContextLogoutHandler();
	}
}

```

코드 1-3. SecurityConfig.java

위 코드의 각 부분에 대한 설명은 다음과 같다. 

1. Spring Security 관련 설정을 위해 클래스에 `@EnableWebSecurity` 를 적용한다.  
2. `public AuthenticationManager authenticationManager` - 인증 과정에서 필요한 AuthenticationManager 객체를 얻기 위한 메서드. 해당 객체는 뒤에 후술할 로그인 기능에 직접 쓰일 것이다. 
3. `public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity)` 메서드에서는 csrf 등의 공격 방지를 위한 설정 및 인증, 인가와 관련된 필터를 설정하는 부분이다. 앞서 설명한 SecurityFilterChain을 여기서 설정하는 것이다. 
    - `.csrf(csrf -> csrf.disable())` - CSRF(Cross-Site Request Forgery, 사이트 간 요청 위조)는 웹 애플리케이션에 대한 보안 공격 중 하나로, 특정 웹 애플리케이션의 신뢰할 수 있는 사용자가 악성 웹 사이트 방문 시 공격자가 해당 사용자가 가진 정보를 이용하여 해당 웹 애플리케이션에 악성 요청을 전송하여 보안을 약화시키거나 의도치 않은 데이터의 수정, 삭제 등의 작업을 하게끔 하여 피해를 끼치는 방식의 공격이라고 한다. 여기서는 인증, 인가에 대해 더 초점을 맞춰 살펴볼 것이라 CSRF 공격 방지 기능을 비활성화하였다.
    - `.cors()` -  CORS (Cross-Origin Resource Sharing)는 서로 다른 출처(Origin)들 간의 리소스 공유 요청을 서버가 허용해주는 메커니즘이다. 서버에서 REST API 형태로 리소스를 제공하는 경우, 클라이언트에서는 AJAX를 이용하여 해당 API를 호출하여 리소스를 가져와야 하는데, 자바스크립트 기반의 AJAX를 이용한 다른 출처로의 요청은 기본적으로 동일 출처 정책 (Same-Origin Policy)에 의해 불가능하게 되어있다. 이는 CSRF, XSS(Cross-Site Scripting) 등의 여러 보안 공격을 막기 위해 존재하는 것이라고 한다. 만약 특정 클라이언트 출처는 신뢰할 수 있다면 이 사이트에 대한 리소스 공유를 허락하기 위한 설정이 필요한데, 위 코드에서는 `public CorsConfigurationSource corsConfigurationSource()` 메서드를 정의하여 이를 수행하고 있다.
    - `authorizeHttpRequests()` - 인증, 인가 설정을 위한 메서드.
        - `requestMatchers()` - 메서드에 특정 인증, 인가 기능을 설정할 URI들을 명시한다.
        - `permitAll()` - `requestMatchers()` 메서드의 인자로 명시된 모든 URI들에 대해, 익명 사용자를 비롯한 모든 권한의 사용자들의 접근을 허용한다. 즉, 해당 URI들에 접근하기 위해 별도의 인증, 인가가 필요하지 않다는 뜻.
        - `anyRequest()` - 그 밖의 모든 URI들에 대한 요청을 지칭.
        - `authenticated()` - 인증된 사용자의 요청만 허락. 별도의 인가는 필요없음.
    - `logout()` - 로그 아웃에 대한 설정을 할 수 있다. 위 코드에서는 로그아웃 요청 URI 설정, 로그아웃 시 특정 쿠키 삭제 및 세션 무효화, 인증 정보 삭제 기능들을 설정하였으며, `permitAll()` 을 통해 모든 사용자가 로그아웃 요청을 할 수 있게 하였다.
4. `public PasswordEncoder passwordEncoder()` - 사용자의 패스워드를 DB에 저장할 때는 평문 그대로 저장하는 게 아니라 암호화된 상태로 저장되어야 하는데, 해당 메서드에서 어떤 방식으로 암호화할지를 결정할 수 있다. 여기서는 `BCryptPasswordEncoder()` 방식을 이용하도록 하였다. 해당 인코딩 방식은 기본적으로 쓰이는 방식인데, 사실 다음의 사항들을 신경써야 한다면…
    - 엣날 방식의 암호화 방식을 사용하며, 최신 방식으로 변경하기 어려운 경우.
    - 미래에 더 나은 암호화 방식이 등장하여 이로 바꾸는 것을 고려할 경우.
    
    대신 `PasswordEncoderFactories.createDelegatingPasswordEncoder()` 을 사용하는 것이 좋다고 한다. 
    
5. `public SecurityContextRepository securityContextRepository()` - 사용자의 인증 요청 후, 인증이 필요한 리소스 접근 요청 시 사용자의 인증 정보를 기억하여 사용자가 또 인증을 하지 않도록 하기 위해 작성한 메서드. 여기서는 `HttpSessionSecurityContextRepository()` 객체를 반환하도록 하였는데, 해당 객체는 사용자의 인증 정보를 담는 SecurityContext를 HttpSession과 연결시켜 세션에 저장하도록 해주는 객체이다. 즉 사용자의 상태 정보를 기억하는 stateful 방식을 위한 객체라 볼 수 있다. 
6. `public SecurityContextLogoutHandler getLogoutHandler()` - 개발자가 수동으로 로그아웃 기능을 구현할 수 있는데, 이를 위해 `SecurityContextLogoutHandler` 객체를 이용하도록 설정하였다. 

이 외에도 더 필요한 설정이 있거나 불필요한 설정이 있다면 추가 또는 삭제, 수정할 수 있다. 

다음은 로그인, 로그아웃 기능을 구현하는 서비스 클래스의 코드이다.

```java
package com.example.business;

import org.springframework.context.annotation.Primary;
import org.springframework.security.authentication.AuthenticationManager;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.context.SecurityContext;
import org.springframework.security.core.context.SecurityContextHolder;
import org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler;
import org.springframework.security.web.context.SecurityContextRepository;
import org.springframework.stereotype.Service;

import com.example.business.interf.AuthInterface;
import com.example.data.entity.TestMember;
import com.example.dto.request.TestMemberRequest;
import com.example.dto.response.TestMemberResponse;
import com.example.util.AuthUtil;

import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;

@Service
@RequiredArgsConstructor
@Primary
public class AuthServiceImpl implements AuthInterface<
	TestMemberResponse, 
	TestMemberRequest
> {
	
	private final AuthenticationManager authenticationManager;
	private final SecurityContextRepository securityContextRepository;
	private final SecurityContextLogoutHandler logoutHandler;
	
	@Override
	public TestMemberResponse login(
		TestMemberRequest loginRequest, 
		HttpServletRequest httpRequest,
		HttpServletResponse httpResponse
	) {
		
		// 유저 닉네임 및 패스워드 기반으로 인증 토큰을 생성.
		UsernamePasswordAuthenticationToken token = 
			new UsernamePasswordAuthenticationToken(
				loginRequest.getUsername(),
				loginRequest.getPassword()
			);
		
		// 인증 매니저로 인증 시도. 내부적으로
		// CustomUserDetailService 클래스의 loadUserByUsername() 호출하여
		// 사용자 정보를 획득. 따라서 개발자가 별도로 CustomUserDetailService를 
		// 호출하여 사용할 필요가 없다고 함. 
		Authentication authentication = authenticationManager
			.authenticate(token);
		
		// 인증 성공 시 SecurityContextHolder에 인증 객체 전달.
		// 이제 어느 클래스건 SecurityContextHolder.getContext().getAuthentication()
		// 만 호출하면 현재 인증된 사용자 정보에 접근 가능.
		SecurityContext securityContext = SecurityContextHolder.getContext();
		securityContext.setAuthentication(authentication); // 인증 정보를 세션에 저장.
		
		// 인증 정보가 포함된 세션을 서버에 영속화하여 다른 request가 와도 인증 정보를 유지하도록 한다.
		// 이 코드를 사용하지 않으면 인증을 하더라도 그 인증 정보가 유지되지 않아서 
		// 다른 request가 오면 다시 익명 사용자로 변경된다는 문제가 발생한다. 
		securityContextRepository.saveContext(
			securityContext, 
			httpRequest, 
			httpResponse
		);
		
		// 세션에 저장된 유저 정보를 DTO로 변환하여 반환.
		return TestMemberResponse
			.toDto((TestMember) AuthUtil.getCurrentUserInfo());
	}

	@Override
	public void logout(
		HttpServletRequest httpRequest, 
		HttpServletResponse httpResponse
	) {
		
		logoutHandler.logout(httpRequest, httpResponse, AuthUtil.getAuth());

	}
	
}

```

코드 1-4. AuthServiceImpl.java

로그인, 즉 인증을 구현하기 위한 코드는 앞서 살펴본 Spring Security의 인증 처리 과정과 위 코드의 주석을 참고하면 이해하는 데에 어려움이 없을 거라 생각되어 여기서는 추가적인 설명을 생략하겠다. 

다음은 위 코드들을 기반으로 작성한 Spring Boot 애플리케이션을 Swagger로 실행했을 때의 결과물이다.

먼저, 로그인하지 않은 상태에서 인증이 필요한 리소스 요청을 할 때의 모습이다. 여기서는 단순하게 현재 사용자의 인증 정보를 조회하는 API로 구현하였다.

![사진 1-1. 미인증된 상태에서 인증이 필요한 API 호출 시의 결과](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/3.png)

사진 1-1. 미인증된 상태에서 인증이 필요한 API 호출 시의 결과

미인증된 사용자라며 응답이 거부된 것을 볼 수 있다. 이번에는 인증을 한 후에 다시 리소스를 요청해보겠다. 먼저 다음과 같이 인증을 하였다. 필자는 이미 이전에 테스트용으로 계정을 DB에 저장해놓았기에 다음과 같이 바로 로그인하였다. 

![사진 1-2. 로그인을 위해 유저 이름과 패스워드를 입력하였다. ](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/4.png)

사진 1-2. 로그인을 위해 유저 이름과 패스워드를 입력하였다. 

![사진 1-3. 로그인 요청 후 받은 응답 내용. 로그인에 성공하였다.](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/5.png)

사진 1-3. 로그인 요청 후 받은 응답 내용. 로그인에 성공하였다.

인증에 성공한 것을 볼 수 있다. 이제 앞서 실패한 API를 다시 호출해보면 다음의 결과를 볼 수 있다. 

![사진 1-4. 인증 후 인증이 필요한 API 호출 후의 응답. 정상적으로 응답된 것을 볼 수 있다. ](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/6.png)

사진 1-4. 인증 후 인증이 필요한 API 호출 후의 응답. 정상적으로 응답된 것을 볼 수 있다. 

이로서 Spring Security를 이용하여 세션 기반 인증을 구현하는데에 성공하였다. 

## 인가

이번 글에서 보일 코드의 전체 소스 코드 및 DB 테이블 생성 쿼리문은 다음의 깃허브 repository에서 확인할 수 있다. 

[spring-study/JWTAuthWithRefreshTokenStudy at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/JWTAuthWithRefreshTokenStudy)

권한별로 특정 리소스에 대한 접근 허용 여부를 달리하고자 한다면 먼저 각 유저별로 권한을 부여해줘야 한다. 여기서는 간단하게 USER, STAFF, ADMIN 이란 역할들만 존재한다고 가정하였다. 그리고 USER 권한을 가진 사용자는 오로지 USER 권한을 가진 다른 이들의 댓글만 볼 수 있도록 하고, STAFF 권한을 가진 사용자는 USER, STAFF 권한을 가진 다른 사용자들의 댓글만 볼 수 있도록, ADMIN은 모든 권한을 가진 사용자들의 댓글을 볼 수 있도록 하고자 한다고 가정해보곘다. 

먼저 각 사용자들의 역할들을 기록하도록 다음과 같이 DB 테이블 내에 “role_name”이라는 컬럼을 마련하였다. 

![사진 2-1. ](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/7.png)

사진 2-1. 

> 이번에는 패스워드 암호화 방식 설정 시 앞서 언급한 `PasswordEncoderFactories.createDelegatingPasswordEncoder()` 를 이용하였는데, 이를 이용하면 암호화된 패스워드는 위 사진처럼 `{id}encodedPassword` 형태로 생성된다. {id}에는 암호화 방식이 적히며, 그 뒤에는 실제로 암호화된 패스워드가 기록되는 형식이다.
> 

그 후, SecurityFilterChain 설정 코드에서 다음과 같이 특정 URI들에 대한 인가 기능을 부여할 수 있다. 

```java
@Bean
public SecurityFilterChain securityFilterChain(HttpSecurity httpSecurity) 
    throws Exception 
{
        
  httpSecurity
    // 생략...
    .authorizeHttpRequests(auth -> auth
      .requestMatchers(permitAllRequestUris).permitAll()
      .requestMatchers("/comments/others/users").hasAnyRole(
        RoleNames.USER, RoleNames.STAFF, RoleNames.ADMIN
      )
      .requestMatchers("/comments/others/staffs").hasAnyRole(
        RoleNames.STAFF, RoleNames.ADMIN
      )
      .requestMatchers("/comments/others/admins").hasAnyRole(
        RoleNames.ADMIN
      )
      .anyRequest().authenticated()
    )
    // 생략...
    ;
        
  return httpSecurity.build();
}
```

코드 2-1. SecurityConfig.securityFilterChain()

위 코드에서 볼 수 있듯, `requestMatcher()` 메서드로 지정한 특정 URI들에 대해 `hasAnyRole()` 메서드를 이용하여 특정 권한을 가진 사용자들만 해당 URI 요청을 허용하도록 설정할 수 있다. `hasAnyRole()` 메서드의 인자로 지정된 여러 역할들 중 하나라도 만족하는 사용자라면 해당 URI에 접근할 수 있는 것이다. 참고로 단 하나의 역할에 대해서만 요청을 허락하고자 한다면 `hasRole()` 메서드를 대신 사용할 수도 있다. 

주의할 점이 있는데, `hasAnyRole()` ,  `hasRole()` 메서드의 인자로 대입한 역할명에는 자동적으로 앞에 `ROLE_` 접두어가 붙는다. 해당 메서드들은 인가 과정을 위해 UserDetails 객체의 `getAuthorities()` 메서드를 호출한다. 따라서 해당 메서드를 오버라이딩할 때 역할명 앞에 반드시 `ROLE_` 접두어를 붙이도록 해야 한다. 

```java
@Override
public Collection<? extends GrantedAuthority> getAuthorities() {
        
  GrantedAuthority auth = new SimpleGrantedAuthority(
    // ROLE_PREFIX = "ROLE_"
    // ex) "ROLE_" + "USER" => "ROLE_USER"
    RoleNames.ROLE_PREFIX + this.role
  );
                
  return Arrays.asList(auth);
}
```

코드 2-2. 

다음은 Swagger에서 실행한 결과물이다. 먼저 USER 권한을 가진 사용자로 로그인할 시 권한 별 댓글 API를 요청한 후의 응답 결과들이다.

![사진 2-2. USER 권한이 필요한 댓글 API 호출 결과. USER 권한을 가진 다른 모든 사용자들의 댓글을 볼 수 있다. ](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/8.png)

사진 2-2. USER 권한이 필요한 댓글 API 호출 결과. USER 권한을 가진 다른 모든 사용자들의 댓글을 볼 수 있다. 

![사진 2-3. STAFF 또는 ADMIN 권한이 필요한 댓글 API 요청 결과. 해당 권한을 가진 사용자들의 댓글을 볼 수 없다. ](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/9.png)

사진 2-3. STAFF 또는 ADMIN 권한이 필요한 댓글 API 요청 결과. 해당 권한을 가진 사용자들의 댓글을 볼 수 없다. 

다음은 STAFF 권한으로 로그인 한 후 각각의 댓글 API를 호출한 결과이다.

![사진 2-4. STAFF 권한을 가진 사용자가 STAFF 권한이 필요한 댓글 API를 호출한 결과. USER 권한이 필요한 댓글 API을 호출해도 댓글을 볼 수 있다. 다만 ADMIN 권한이 필요한 댓글 API에 대해서는 요청이 여전히 거부된다. ](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/10.png)

사진 2-4. STAFF 권한을 가진 사용자가 STAFF 권한이 필요한 댓글 API를 호출한 결과. USER 권한이 필요한 댓글 API을 호출해도 댓글을 볼 수 있다. 다만 ADMIN 권한이 필요한 댓글 API에 대해서는 요청이 여전히 거부된다. 

ADMIN 권한으로 로그인 시 USER, STAFF 권한을 가진 사용자들 뿐만 아니라 같은 ADMIN 권한을 가진 사용자들의 댓글도 볼 수 있게 된다. 

![사진 2-5. ADMIN 권한을 가진 사용자가 ADMIN 권한을 필요로 하는 댓글 API 호출 결과. 정상적으로 댓글을 볼 수 있다.](/images/2025-02-27/spring-boot-%EB%B3%B4%EC%95%88%EA%B3%BC%20spring%20security/11.png)

사진 2-5. ADMIN 권한을 가진 사용자가 ADMIN 권한을 필요로 하는 댓글 API 호출 결과. 정상적으로 댓글을 볼 수 있다.

### 역할 계층 설정하기 - RoleHierarchy

인가 설정을 위한 위 코드 2-1을 다시 살펴보면 USER 권한이 필요한 URI에 대해 USER, STAFF, ADMIN 모두가 볼 수 있도록 설정한 것을 볼 수 있다. 그런데 사실 생각해보면 STAFF 권한을 가진 사용자는 자연스레 USER 권한으로 할 수 있는 작업들을 모두 할 수 있고, ADMIN 권한을 가진 사용자는 USER, STAFF 권한으로 할 수 있는 작업들을 모두 할 수 있도록 하는 것이 자연스럽다. 이렇듯 역할에도 계층이 있는데, 위 코드 2-1에서는 Spring Security가 개발자가 정한 역할들의 계층을 알 방도가 없기에 어쩔 수 없이 개발자가 일일이 여러 역할들을 설정한 것이다. 지금이야 역할이 세 개뿐이니 별 상관없다 느낄지 모르겠지만, 만약 역할을 많이 만들어야 하고, 역할들 간의 계층 구조가 복잡하다면 위 코드처럼 구성하기엔 너무 번거롭고, 만약 역할 계층에 새로운 역할이 들어오거나 삭제, 또는 역할 구조가 수정된다면 일일이 `hasAnyRole()` 메서드에 전달한 역할들의 정보를 바꿔야하기에 유지 보수 관점에서도 별로 좋지 못할 것이다. 

다행히도 역할의 계층 구조를 Security에 알릴 수 있는 방법이 있다. 바로 `RoleHierarchy` 인터페이스를 이용하는 것이다. 다음은 `@EnableWebSecurity` 어노테이션을 적용한 클래스 내부에 작성한 역할 계층 구조 설정 메서드이다. 

```java
@Bean
public RoleHierarchy roleHierarchy() {
  return RoleHierarchyImpl.withDefaultRolePrefix()
    .role(RoleNames.ADMIN).implies(RoleNames.STAFF)
    .role(RoleNames.STAFF).implies(RoleNames.USER)
    .build();
}
```

코드 3-1. SecurityConfig.java

위 코드를 해석하자면, ADMIN 역할에는 STAFF 역할을 포함하고, STAFF 역할에는 USER 역할을 포함시킨다는 뜻이다. 상위 역할에 대해서는 `role()` 메서드를, 하위 역할은 `implies()` 메서드를 이용하여 두 역할 간의 상하 구조를 설정할 수 있는 것이다. 

위와 같이 역할 계층을 설정하면 앞서 본 코드 2-1에서의 인가 코드를 다음과 같이 축약할 수 있다.

```java
/*
.requestMatchers("/comments/others/users").hasAnyRole(
RoleNames.USER, RoleNames.STAFF, RoleNames.ADMIN
)
.requestMatchers("/comments/others/staffs").hasAnyRole(
 RoleNames.STAFF, RoleNames.ADMIN
)
.requestMatchers("/comments/others/admins").hasAnyRole(
RoleNames.ADMIN
) */
.requestMatchers("/comments/others/users").hasRole(RoleNames.USER)
.requestMatchers("/comments/others/staffs").hasRole(RoleNames.STAFF)
.requestMatchers("/comments/others/admins").hasRole(RoleNames.ADMIN)
```

코드 3-2. 코드 2-1의 일부를 수정한 모습. 

그러면 자연스레 USER 권한이 필요한 URI에 대해선 STAFF, ADMIN 권한을 보유한 사용자들도 접근 가능해진다. 위 코드로 바꾸고 Swagger에서 실험해봐도 앞서 본 결과와 동일하다. 

# 글을 마치며…

이 글에서는 Spring Security를 이용하여 Spring 기반 웹 애플리케이션 제작 시 어떻게 인증, 인가 기능을 적용할 수 있는지에 대해 살펴보았다. 이 글에서는 세션 기반 인증에 대해 살펴보았는데, 다음 글에서는 JWT 기반 인증에 대해 살펴보겠다. 

---

References

[1] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024), p373~

[2] Spring Security

[Daum 카페](https://cafe.daum.net/flowlife/HrhB/81)

[3] Spring Security With Session, JWT

[Daum 카페](https://cafe.daum.net/flowlife/HrhB/104)

[4] Spring Security Official Docs

[Spring Security](https://spring.io/projects/spring-security#learn)

[5] Spring Security architecture

[Architecture :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/architecture.html)

[6] 참고할만한 글: 패스워드 저장 방식

[Password Storage :: Spring Security](https://docs.spring.io/spring-security/reference/features/authentication/password-storage.html)

[7] Spring Security 인증 아키텍처

[Servlet Authentication Architecture :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/authentication/architecture.html)

[8] Role 계층 설정 방법

[Authorization Architecture :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/authorization/architecture.html#authz-hierarchical-roles)

[9] 인가(Authorization) 관련 설정법

[Authorize HttpServletRequests :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/authorization/authorize-http-requests.html)

[10] Spring Security 관련 Filter들의 적용 순서.

[https://github.com/spring-projects/spring-security/blob/6.4.3/config/src/main/java/org/springframework/security/config/annotation/web/builders/FilterOrderRegistration.java](https://github.com/spring-projects/spring-security/blob/6.4.3/config/src/main/java/org/springframework/security/config/annotation/web/builders/FilterOrderRegistration.java)

[11] [Spring Security란? 사용하는 이유부터 설정 방법까지 알려드립니다! I 이랜서 블로그](https://www.elancer.co.kr/blog/detail/235?seq=235)

[12] 에이콘아카데미(강남) 강의

[13] 신선영, “스프링 부트 3 백엔드 개발자 되기 - 자바 편, 2판”, (골든래빗)

[14] 인증 정보를 서버 측에 저장하는 방법. 

[Persisting Authentication :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/authentication/persistence.html)

[15] CSRF

[Cross Site Request Forgery (CSRF) :: Spring Security](https://docs.spring.io/spring-security/reference/features/exploits/csrf.html)

[16] CORS

[🌐 악명 높은 CORS 개념 & 해결법 - 정리 끝판왕 👏](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F)

[17] CORS

[교차 출처 리소스 공유 (CORS) - HTTP \| MDN](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

[18] CORS

[교차 출처 리소스 공유](https://ko.wikipedia.org/wiki/%EA%B5%90%EC%B0%A8_%EC%B6%9C%EC%B2%98_%EB%A6%AC%EC%86%8C%EC%8A%A4_%EA%B3%B5%EC%9C%A0)