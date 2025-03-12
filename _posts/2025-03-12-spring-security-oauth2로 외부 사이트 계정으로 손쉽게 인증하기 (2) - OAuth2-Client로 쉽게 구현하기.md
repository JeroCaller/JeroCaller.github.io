---
title: "[Spring Security] OAuth2로 외부 사이트 계정으로 손쉽게 인증하기 (2) - OAuth2-Client로 쉽게 구현하기"
category: "Spring"
tag: ["Spring", "Spring boot", "Spring Security", "인증", "보안", "Authentication", "Authorization", "Auth", "JWT", "OAuth", "OAuth2", "OAuth2 Client"]
---

이 글은 [[Spring Security] OAuth2로 외부 사이트 계정으로 손쉽게 인증하기 (1) - 이론 및 설정 편](/spring/spring-security-oauth2로-외부-사이트-계정으로-손쉽게-인증하기-(1)-이론-및-설정-편/) 글에 이어서 작성하는 글입니다.

# Spring Boot에서의 OAuth2-Client 의존성을 이용한 구현

## 코드 구현에 앞선 사전 정보들

이전 글에서의 “권한 부여 코드 승인 타입”의 인증 과정을 보면 꽤나 복잡한 것을 알 수 있다. 이로 인해 개발자 입장에서는 “저걸 다 구현해야한다고?”란 막막함이 올 것이다. 하지만 Spring Security에서 지원하는 OAuth2-Client란 의존성을 이용하면 해당 라이브러리에서 이미 많은 과정들을 구현해놓았기에 개발자는 다음의 사항들만 고려하면 된다. 

- 구글 등의 제 3자 서버로부터 로그인에 성공한 사용자의 아이디, 이메일 등의 기본 사항을 DB에 저장하는 기능.
- 로그인 성공 (또는 실패) 시 후처리.

지금부터는 스프링 부트를 이용하여 백엔드 측에서 어떻게 OAuth를 이용한 로그인 연동 기능을 구현할 수 있는지 살펴볼 것이다. 필자의 개발 환경 및 사용 기술들은 다음과 같다. 

- IDE : Eclipse
- Spring Boot 관련 기술
    - Build Tool : Gradle
    - 데이터 영속성 관련
        - DB: Maria DB
        - Spring Data JPA
    - 인증, 인가 관련
        - Spring Security
        - Oauth2 Client
        - JWT 기반 인증
    - REST API 방식
- REST API 방식 구현으로 인해 프론트엔드에서는 React로 화면 페이지 구성하는 CSR 방식 사용.

다음은 전체 소스 코드가 담긴 필자의 깃허브 repository URL이다. 

백엔드(스프링 부트)측 소스 코드

[spring-study/OAuthStudy at master · JeroCaller/spring-study](https://github.com/JeroCaller/spring-study/tree/master/OAuthStudy)

프론트엔드(React)측 소스 코드

[react-study/test_oauth2_login at master · JeroCaller/react-study](https://github.com/JeroCaller/react-study/tree/master/test_oauth2_login)

OAuth 관련한 핵심 기능만 중점적으로 살펴볼 것이기에 이 글에서는 전체 소스 코드의 많은 부분들을 생략할 것이다. 따라서 위 사이트들을 통해 전체 소스 코드를 확인하여 참고하면 되겠다. 

코드를 살펴보기에 앞서 전체적인 흐름 파악 및 헷갈릴 수 있는 모호한 점을 해소하기 위한 설명을 먼저 하자면, 여기서는 OAuth를 이용, 구글과 연동하여 “로그인”하는 것에만 초점을 맞출 것이다. 실제 상용 웹 앱에서는 필요하다면 리소스 서버로부터 사용자에 대한 추가 데이터들을 가져올 수 있을 것이다. 예를 들면 구글 드라이브에 저장된 사용자의 특정 데이터라든지, 네이버의 경우 “네이버 블로그” 등의 여러 생태계가 있기에 이에 대한 사용자의 데이터에 접근할 수도 있겠다. 하지만 여기서는 간단하게 “로그인”에 대해서만 살펴볼 것이다. 

이에 대해, 앞서 인증 과정에서 설명했듯 리소스 서버에 사용자의 개인 정보 또는 데이터를 요청하기 위해 권한 서버에서 발급하는 액세스 토큰을 사용하는데, “로그인”에 한정하여 보자면 이 과정조차도 OAuth2 Client를 이용하면 거의 자동으로 이루어지기에 개발자는 이 액세스 토큰에 대해서도 신경쓰지 않아도 된다. 다만 여기서 필자가 보일 코드에서는 “액세스 토큰” 및 “리프레시 토큰”을 사용하였는데, 여기서의 두 토큰은 사용자가 클라이언트 앱 서버에서 보유하고 있는 여러 데이터들을 요청할 때 필요한 토큰이지, 구글 등 제 3자의 리소스 서버와의 통신을 위한 토큰이 아님을 밝힌다. 

구글 등 제 3자에서 제공하는 액세스 토큰 및 리프레시 토큰을 단순히 리소스 오너의 리소스를 얻기 위한 용도가 아니라, 사용자가 클라이언트 앱 서버로부터 접근에 인증이 필요한 특정 데이터를 요청할 때에도 그 범위를 확장하여 사용할 수도 있지 않을까, 란 생각이 든다. 이전에 [JWT에 대해 다룬 글](/spring/spring-security-jwt-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D/)에서도 잠깐 언급했던 내용이지만, MSA 방식을 이용하여 서버를 여러 개 만들어 분산시켜 놓은 시스템에서 하나의 토큰만으로도 여러 종류의 자원에 접근할 수 있다고 했으니 말이다. 하지만 여기까지는 필자가 직접 구현하여 확인해보진 않아서 정말로 그게 가능한지에 대해선 확답을 내리기가 좀 그렇다… 그런데 인증, 인가 과정을 제 3자 provider에게 위임하였는데 이 토큰을 그대로 클라이언트 앱 내부에서도 사용한다면 보안적으로 조금 위험할 것 같다는 생각이 든다. 이 토큰이 한 번 탈취 당하면 사용자 입장에서는 해당 클라이언트 앱에서의 계정 및 관련 데이터가 유출되는 것 뿐만 아니라 구글 등 제 3자 사이트의 계정 및 해당 계정에 있는 사용자 개인 데이터들도 같이 유출될 수 있어 이는 위험한 것 같다. 그래서 기술적으로는 가능하더라도, 보안적인 측면을 따지면 리소스 서버 요청용 토큰과 클라이언트 앱 내부에서 사용하기 위한 용도의 토큰을 분리하여 운영하는 게 좋을 것 같다. 

아무튼 이제 코드를 살펴보겠다. 

## 코드로 살펴보기

### 백엔드

스프링 부트 프로젝트 생성 시 Spring Security와 OAuth2 Client 의존성을 추가해야 한다. 이클립스의 Spring Boot Starter Project나 spring.io에서 제공하는 “[spring initializr](https://start.spring.io/)” 이용 시 각각 “Spring Security”, “OAuth2 Client”를 선택하여 추가하면 된다. 해당 의존성들은 build.gradle에서 다음과 같이 추가할 수도 있다. 

```
implementation 'org.springframework.boot:spring-boot-starter-oauth2-client'
implementation 'org.springframework.boot:spring-boot-starter-security'
```

참고로 OAuth2-Client 의존성은 단독으로는 사용할 수 없고, 반드시 Spring Security 의존성과 함께 사용해야 한다고 한다. 

application.properties 파일에는 이전에 구글 클라우드 콘솔 사이트에서 얻었던 Client ID 및 Client Secret을 다음의 형태로 입력한다(이전 글 참고). 

```
spring.security.oauth2.client.registration.google.client-id=your_id
spring.security.oauth2.client.registration.google.client-secret=your_secret
```

이 두 가지 정보는 외부에 유출되면 안되는 중요 정보이기에 실수로 깃허브에 그대로 올리거나 하지 않도록 각별한 주의를 기울여야 한다. 

registration 다음에는 리소스 제공자 회사의 이름을 입력하면 되겠다. 여기서는 구글을 이용하므로 google이라고 입력하였다. 카카오면 kakao, 네이버면 naver 식인 것이다. 

참고로 말하자면, 구글, 깃허브 등의 몇몇 사이트들에 대해서는 authorization-uri, user-info-uri 등의 몇몇 클라이언트 속성들의 값이 이미 정해져 있다고 한다. 스프링 부트 내부적으로는 이러한 정보들을 CommonOAuth2Provider라는 클래스 내부에 저장하여 정의한 상태라고 한다(해당 클래스는 적어도 이 글에서는 개발자가 직접 사용할 일이 없는 클래스이다). 따라서 해당 속성들을 추가적으로 개발자가 지정할 필요는 없다고 한다. 

다만 카카오, 네이버 등의 다른 제공자들의 경우에는 따로 기본 설정된 클라이언트 속성이 없다고 한다. 따라서 이 경우 개발자가 별도로 클라이언트 속성들을 지정해줘야 한다고 한다. 

다음은 이에 대한 예시이다. 

```
spring:
  security:
    oauth2:
      client:
        registration:
          okta:
            client-id: okta-client-id
            client-secret: okta-client-secret
        provider:
          okta:	
            authorization-uri: https://your-subdomain.oktapreview.com/oauth2/v1/authorize
            token-uri: https://your-subdomain.oktapreview.com/oauth2/v1/token
            user-info-uri: https://your-subdomain.oktapreview.com/oauth2/v1/userinfo
            user-name-attribute: sub
            jwk-set-uri: https://your-subdomain.oktapreview.com/oauth2/v1/keys
```

참고 코드 1-1. 출처: 스프링 공식 문서, [https://docs.spring.io/spring-security/reference/servlet/oauth2/login/core.html#oauth2login-common-oauth2-provider](https://docs.spring.io/spring-security/reference/servlet/oauth2/login/core.html#oauth2login-common-oauth2-provider)

위 예시는 okta라는 제공자를 기준으로 한 예시이다. 다른 분의 블로그에서도 카카오 기준 설정 코드를 볼 수 있다.

[[Spring] Spring Security + OAuth2 + JWT](https://do5do.tistory.com/20)

연동 로그인을 구현하기 위한 핵심 기능들을 잘 보여주는 SecurityConfig 클래스 코드부터 보이겠다.

```java
package com.jerocaller.config;

import java.util.Arrays;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.security.config.annotation.web.builders.HttpSecurity;
import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
import org.springframework.security.config.http.SessionCreationPolicy;
import org.springframework.security.crypto.factory.PasswordEncoderFactories;
import org.springframework.security.crypto.password.PasswordEncoder;
import org.springframework.security.oauth2.client.web.AuthorizationRequestRepository;
import org.springframework.security.oauth2.core.endpoint.OAuth2AuthorizationRequest;
import org.springframework.security.web.AuthenticationEntryPoint;
import org.springframework.security.web.SecurityFilterChain;
import org.springframework.security.web.access.AccessDeniedHandler;
import org.springframework.security.web.authentication.UsernamePasswordAuthenticationFilter;
import org.springframework.security.web.authentication.logout.SecurityContextLogoutHandler;
import org.springframework.web.cors.CorsConfiguration;
import org.springframework.web.cors.CorsConfigurationSource;
import org.springframework.web.cors.UrlBasedCorsConfigurationSource;
import org.springframework.web.filter.CorsFilter;

import com.jerocaller.business.CustomOAuth2UserService;
import com.jerocaller.business.CustomOidcUserService;
import com.jerocaller.config.filter.CorsLoggingFilter;
import com.jerocaller.config.oauth.OAuth2FailureHandler;
import com.jerocaller.config.oauth.OAuth2SuccessHandler;
import com.jerocaller.jwt.JwtAuthenticationFilter;

import lombok.RequiredArgsConstructor;

@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {
    
    private final CustomOAuth2UserService customOAuth2UserService;
    private final CustomOidcUserService customOidcUserService;
    private final JwtAuthenticationFilter jwtAuthenticationFilter;
    private final AuthorizationRequestRepository<OAuth2AuthorizationRequest>
        authorizationRequestRepository;
    private final OAuth2SuccessHandler oAuth2SuccessHandler;
    private final AuthenticationEntryPoint authenticationEntryPoint;
    private final AccessDeniedHandler accessDeniedHandler;
    
    private final String[] permitAllRequestUris = {
        "/swagger-ui/**", 
        "/v3/api-docs",
        "/v3/api-docs/**", 
        "/swagger-ui.html",
        "/swagger-ui/index.html",
        "/auth/**",
        "/members/registration",
        "/jwt/tokens/**",
        "/oauth2/**"
    };
    
    @Bean
    public SecurityFilterChain securityFilterChain(
        HttpSecurity httpSecurity
    ) throws Exception {
        
        httpSecurity
            .csrf(csrf -> csrf.disable())
            .httpBasic(httpBasic -> httpBasic.disable())
            .formLogin(formLogin -> formLogin.disable())
            .logout(logout -> logout.disable())
            .sessionManagement(manager -> manager
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS))
            .cors(cors -> cors.configurationSource(corsConfigurationSource()))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers(permitAllRequestUris).permitAll()
                .anyRequest().authenticated()
            )
            .addFilterBefore(new CorsLoggingFilter(), CorsFilter.class)
            .addFilterBefore(
                jwtAuthenticationFilter, 
                UsernamePasswordAuthenticationFilter.class
            )
            .oauth2Login(login -> login
                .authorizationEndpoint(endPoint -> endPoint
                    .authorizationRequestRepository(
                        authorizationRequestRepository
                    )
                )
                .userInfoEndpoint(endPoint -> endPoint
                    // userService: Google과 같이 OIDC 프로토콜 사용 provider에 
                    // 대해선 동작하지 않음.
                    // 그럼에도 만약 구글 뿐만 아니라 다른 third-party provider들도 
                    // 사용할 예정이라면 확장성을 위해서라면 둘 다 작성하는 것이 좋겠다. 
                    .userService(customOAuth2UserService)
                    .oidcUserService(customOidcUserService)
                )
                .successHandler(oAuth2SuccessHandler)
                .failureHandler(new OAuth2FailureHandler())
            )
            .exceptionHandling(handler -> handler
                .authenticationEntryPoint(authenticationEntryPoint)
                .accessDeniedHandler(accessDeniedHandler)
            );
        
        return httpSecurity.build();
    }
    
    /**
     * Spring Bean 간의 순환 참조 방지를 위해 static으로 선언함. 
     * 
     * 이 설정 클래스에서 의존성으로 주입받은 MemberService 내부에 
     * PasswordEncoder를 의존성 주입받는 코드가 있기에 순환 참조에 걸림.
     * 
     * @return
     */
    @Bean
    public static PasswordEncoder passwordEncoder() {
        return PasswordEncoderFactories.createDelegatingPasswordEncoder();
    }
    
    @Bean
    public SecurityContextLogoutHandler securityContextLogoutHandler() {
        return new SecurityContextLogoutHandler();
    }
    
    public CorsConfigurationSource corsConfigurationSource() {
        
        CorsConfiguration configuration = new CorsConfiguration();
        configuration.setAllowedOrigins(Arrays.asList(
            "http://localhost:3000"
        ));
        configuration.setAllowedMethods(Arrays.asList(
            "GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS"
        ));
        configuration.setAllowedHeaders(Arrays.asList("*"));
               
        // 클라이언트로부터 인증 정보(credential) 전송 시
        // 이를 받도록 허용할 것인지 여부 설정. 
        // 여기 백엔드에서와 프론트엔드 둘 모두 true로 설정해야 둘 사이에서 인증 정보를 교환할 수 있다. 
        configuration.setAllowCredentials(true); 
        
        UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
        source.registerCorsConfiguration("/**", configuration);
        return source;
    }
    
}

```

코드 1-1. SecurityConfig.java

이 코드의 OAuth에 대한 핵심은 SecurityFilterChain 설정 메서드 내 `.oauth2Login()` 메서드에 있다. 해당 코드만 다시 발췌하면 다음과 같다. 

```java
.oauth2Login(login -> login
    .authorizationEndpoint(endPoint -> endPoint
         .authorizationRequestRepository(
             authorizationRequestRepository
          )
    )
    .userInfoEndpoint(endPoint -> endPoint
        // userService: Google과 같이 OIDC 프로토콜 사용 provider에 
        // 대해선 동작하지 않음.
        // 그럼에도 만약 구글 뿐만 아니라 다른 third-party provider들도 
        // 사용할 예정이라면 확장성을 위해서라면 둘 다 작성하는 것이 좋겠다. 
        .userService(customOAuth2UserService)
        .oidcUserService(customOidcUserService)
     )
     .successHandler(oAuth2SuccessHandler)
     .failureHandler(new OAuth2FailureHandler())
)
```

코드 1-2. 코드 1-1 일부 발췌

이 코드에는 OAuth를 이용한 로그인 기능을 구현하기 위해 다음과 같은 설정들이 되어 있다. 

- `.authorizationRequestRepository(authorizationRequestRepository)` - Authorization Code Grant Type에서는 이전 글에서 설명했듯 인증을 위해 여러 번의 redirect가 일어난다. 이로 인해 요청과 요청 사이에서 인증 관련 정보라는 상태를 어딘가 저장해놓아야 하는데, 서버의 세션과 브라우저의 쿠키가 있겠다. 여기서는 쿠키 방식을 택하였으며 이를 위해`AuthorizationRequestRepository<OAuth2AuthorizationRequest>` 인터페이스를 구현하는 OAuth2AuthorizationRequestBasedOnCookieRepository 라는 클래스를 정의하였다. 여기서는 해당 클래스를 사용하였다.
- `.userService()` - OAuth 프로토콜을 사용하는 제 3자 provider들에 대해, 권한 서버로부터 인증 완료 후 얻은 액세스 토큰을 이용하여 리소스 서버에 리소스를 요청하여 받아오는 bean을 등록하는 메서드이다. 여기에서는 리소스 서버로부터 가져온 사용자 정보를 DB에 저장하기 위해 해당 기능을 추가한 클래스를 등록하기 위해 작성하였다. 그런데 구글만 사용하는 여기서는 해당 코드가 실행되지 않는데, 그 이유에 대해서는 아래에 후술하겠다. 어쨌든 여기서는 해당 코드가 실행되지 않기도 하고, 아래에 후술할 OidcUserService와 코드가 사실상 거의 같기 때문에 이 글에서는 생략하겠다.
- `.oidcUserService()` - OIDC(OpenID Connect) 프로토콜을 사용하는 제 3자 provider들에 대해, 권한 서버로부터 인증 완료 후 얻은 액세스 토큰을 이용하여 리소스 서버로부터 리소스를 얻어오는 bean을 등록하는 메서드이다. 후술하겠지만, Google과 같은 일부 제 3자 provider들은 OIDC 프로토콜을 지원하는데, 이들에 대한 해당 기능을 적용하고자 할 때 사용된다.
- `successHandler()` - OAuth 로그인 성공 시 이를 처리할 핸들러를 등록한다. 후술하겠지만 여기서는 클라이언트 앱 내에서 사용할 목적의 액세스, 리프레시 토큰 발급 및 전송, 그리고 앞서 인증을 위해 잠시 쿠키에 저장해놨던 임시 정보들을 삭제하는 기능들을 포함하였다.
- `failureHandler()` - OAuth 로그인 실패 시 이를 처리할 핸들러를 등록한다. 후술하겠지만 여기서는 프론트엔드 측에 이미 설정된 로그인 실패 시 처리 페이지의 URI로 리다이렉트 하도록 설정하였다.

즉, OAuth 로그인에 대해 개발자는 다음의 것들만 구현하면 되는 것이다.

- 요청 간 인증 상태 정보 저장을 위한 쿠키 저장 기능
- 리소스 서버로부터 리소스 오너의 리소스를 가져왔을 때 이를 DB에 저장하는 추가 기능.
- 로그인 성공 또는 실패 후의 처리.

그러면 하나씩 살펴보겠다.

먼저, 리소스 서버로부터 리소스를 가져오며, 이 사용자 정보를 DB에 저장하는 코드이다.

```java
package com.jerocaller.business;

import java.util.Map;

import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserRequest;
import org.springframework.security.oauth2.client.oidc.userinfo.OidcUserService;
import org.springframework.security.oauth2.core.OAuth2AuthenticationException;
import org.springframework.security.oauth2.core.oidc.user.OidcUser;
import org.springframework.security.oauth2.core.user.OAuth2User;
import org.springframework.stereotype.Service;

import com.jerocaller.common.RoleNames;
import com.jerocaller.data.entity.Member;
import com.jerocaller.data.repository.MemberRepository;

import jakarta.transaction.Transactional;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

/**
 * OIDC 지원 provider에 대한 클래스. 
 * 리소스 서버에서 제공하는 사용자 정보를 불러오기 위한 클래스.
 * 여기서는 얻어온 사용자 정보를 DB에 저장하는 기능을 추가하기 위해 작성함. 
 * 기존에 리소스 서버로부터 사용자 정보를 가져오는 기능은 
 * OidcUserService.loadUser()를 이용함. 
 * 
 * 참고) 
 * org.springframework.security.config.oauth2.client.CommonOAuth2Provider라는 
 * 클래스에는 google, github 등 몇몇 provider들의 클라이언트 속성들이 미리 정의되어 있다. 
 * 그런데 google의 경우, builder.scope에 "openid"란 값이 포함되어 있다. 
 * 이는 google이 openid라는 프로토콜을 지원하는 것이라고 한다. 
 * openid는 OAuth를 확장하여 만든 버전이라고 한다. 
 * 그리고 OAuth는 인가가 주 목표이며, 범용 목적이지만 
 * OIDC(OpenID Connect)은 통합 인증이 주 목적이라고 하며, 즉 두 프로토콜의 목적이 다르다고 한다. 
 * 어쨌거나 Google은 OIDC를 지원하기에, CustomOAuth2UserService와 같이 
 * OAuth 기반 UserService 코드가 실행되지 않는다. 
 * 따라서 대신 다음과 같이 OIDC 기반 UserService를 만들어야 한다. 
 */
@Service
@RequiredArgsConstructor
@Slf4j
public class CustomOidcUserService extends OidcUserService {
    
    private final MemberRepository memberRepository;
    
    @Override
    public OidcUser loadUser(OidcUserRequest userRequest) 
        throws OAuth2AuthenticationException 
    {
        
        log.info("=== CustomOidcUserService.loadUser() 호출됨 ===");
        
        OidcUser oidcUser = super.loadUser(userRequest);
        
        saveOrUpdate(oidcUser);
        return oidcUser;
    }
    
    /**
     * 기존에 계정이 있을 경우 리소스 서버로부터 제공받은 유저 정보로 업데이트하고, 
     * 없을 경우 리소스 서버로부터 제공받은 유저 정보로 새로 저장.
     * 
     * @param oAuth2User  - OidcUser 인터페이스는 OAuth2User 인터페이스를 상속받기에 
     * OAuth2User 타입을 대신 사용해도 된다. 
     * @return
     */
    @Transactional
    public Member saveOrUpdate(OAuth2User oAuth2User) {
        
        Map<String, Object> attributes = oAuth2User.getAttributes();
        String email = (String) attributes.get("email");
        String name = (String) attributes.get("name");
        Member member = memberRepository.findByEmail(email)
            .map(entity -> entity.update(name))
            .orElse(
                Member.builder()
                    .email(email)
                    .username(name)
                    .role(RoleNames.USER)
                    .password("GoogleOAuth") // NN 회피용. 실제 사용되는 패스워드 아님.
                    .build()
            );
        return memberRepository.save(member);
    }
    
}

```

코드 1-3. CustomOidcUserService.java

위 코드에서는 기존에 존재하는 OidcUserService 클래스를 상속받아 구현하였으며, 해당 클래스의 `loadUser()` 메서드를 오버라이딩하였다. 본래 해당 메서드에는 리소스 서버로부터 사용자 정보를 가져오는 기능이 이미 구현되어 있기에, 여기서는 일단 `super.loadUser(userRequest)` 를 호출하여 사용자 정보를 `OidcUser` 타입으로 가져오도록 하였다. 그 후, `saveOrUpdate()` 메서드를 정의하여 `OidcUser` 객체로부터 사용자의 구글 이메일, 닉네임을 추출, DB에 이미 존재하는 회원이었다면 구글 계정에 연동하도록 닉네임을 구글 계정 닉네임으로 업데이트하게끔 하였고, DB에 없다면 새로운 회원으로 등록하게끔 설계하였다. 

한 편, 원래는 이 클래스 대신 `public class CustomOAuth2UserService extends DefaultOAuth2UserService` 를 구현했었다. 이름만 다를 뿐 기능은 위 코드와 동일하게 작성하였다. 그런데 확인해보니 해당 클래스는 정작 실행이 되지 않았다. `org.springframework.security.config.oauth2.client.CommonOAuth2Provider` 클래스를 보면 이 이유를 유추할 수 있었다. 

```java
public enum CommonOAuth2Provider {

	GOOGLE {

		@Override
		public Builder getBuilder(String registrationId) {
			ClientRegistration.Builder builder = getBuilder(registrationId,
					ClientAuthenticationMethod.CLIENT_SECRET_BASIC, DEFAULT_REDIRECT_URL);
			builder.scope("openid", "profile", "email");
			builder.authorizationUri("https://accounts.google.com/o/oauth2/v2/auth");
			builder.tokenUri("https://www.googleapis.com/oauth2/v4/token");
			builder.jwkSetUri("https://www.googleapis.com/oauth2/v3/certs");
			builder.issuerUri("https://accounts.google.com");
			builder.userInfoUri("https://www.googleapis.com/oauth2/v3/userinfo");
			builder.userNameAttributeName(IdTokenClaimNames.SUB);
			builder.clientName("Google");
			return builder;
		}

	},
	// 생략...
```

코드 1-4. CommonOAuth2Provider.java

여기에 구글의 클라이언트 속성 설정 코드가 있는 것을 볼 수 있다. 그런데 자세히 보면 `builder.scope()` 메서드에 “openid”라고 작성되어 있는 것을 볼 수 있다. openid는 OIDC(OpenID Connect)로, 기존의 OAuth를 확장한 프로토콜이라고 한다. 즉, 구글에서는 연동을 위해 OAuth가 아닌 OIDC 프로토콜을 대신 사용한다고 볼 수 있겠다. 스프링 시큐리티 측에서는 OAuth 프로토콜을 사용하는 provider에 대해선 OAuth 관련 클래스들만 작동하고, OIDC 프로토콜을 사용하는 provider에 대해선 OIDC 관련 클래스들만 작동한다고 한다. 이는 스프링 공식 문서에서도 확인할 수 있는 대목이었다.

> The presence of the `openid` scope in the above configuration indicates that OpenID Connect 1.0 should be used. This instructs Spring Security to use OIDC-specific components (such as `OidcUserService`) during request processing. Without this scope, Spring Security will use OAuth2-specific components (such as `DefaultOAuth2UserService`) instead.
> 

[OAuth2 :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html#oauth2-client-log-users-in)

그래서 결국 위 코드 1-3을 작성함으로써, DB에 저장이 되지 않는 문제를 해결하였다. 이 문제에 대해선 다음의 블로그 글을 통해 도움을 받을 수 있었으며, 해당 블로그 글에서 이 문제에 대한 더 자세한 내용을 볼 수 있다. 

[[OAuth2] 구글 로그인이 안 된다면](https://ryumodrn.tistory.com/59)

이번에는 요청 간 인증 상태 정보를 쿠키에 임시로 저장하기 위한 코드이다.

```java
package com.jerocaller.config.oauth;

import org.springframework.security.oauth2.client.web.AuthorizationRequestRepository;
import org.springframework.security.oauth2.core.endpoint.OAuth2AuthorizationRequest;
import org.springframework.stereotype.Component;
import org.springframework.web.util.WebUtils;

import com.jerocaller.common.CookieNames;
import com.jerocaller.common.ExpirationTime;
import com.jerocaller.data.dto.request.CookieRequest;
import com.jerocaller.util.CookieUtil;

import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;

/**
 * OAuth2에 대해 인증 요청 관련 상태를 쿠키에 저장, 불러오기 기능을 위한 클래스.
 * 
 * AuthorizationRequestRepository는 권한 인증 흐름에서 클라이언트의 요청을 유지하기 위해 
 * 사용되는 요청 저장소 역할을 한다. 
 */
@Component
@RequiredArgsConstructor
public class OAuth2AuthorizationRequestBasedOnCookieRepository 
    implements AuthorizationRequestRepository<OAuth2AuthorizationRequest> {
    
    private final CookieUtil cookieUtil;

    @Override
    public OAuth2AuthorizationRequest loadAuthorizationRequest(
        HttpServletRequest request
    ) {
        
        Cookie cookie = WebUtils.getCookie(
            request, 
            CookieNames.OAUTH2_AUTHORIZATION_REQUEST
        );
        
        return cookieUtil.deserialize(
            cookie, 
            OAuth2AuthorizationRequest.class
        );
    }

    @Override
    public void saveAuthorizationRequest(
        OAuth2AuthorizationRequest authorizationRequest, 
        HttpServletRequest request,
        HttpServletResponse response
    ) {
        
        // 권한(인증) 요청이 없다면 기존 저장소에 저장된 인증 요청 관련 상태 삭제 및 종료.
        if (authorizationRequest == null) {
            removeAuthorizationRequestCookies(response);
            return;
        }
        
        // 인증 권한 요청 관련 상태를 쿠키에 실어 응답하도록 구성.
        CookieRequest cookieRequest = CookieRequest.builder()
            .cookieName(CookieNames.OAUTH2_AUTHORIZATION_REQUEST)
            .cookieValue(cookieUtil.serialize(authorizationRequest))
            .maxAge(ExpirationTime.OAUTH2_AUTH_REQUEST_IN_SECONDS)
            .build();
        cookieUtil.addCookie(response, cookieRequest);
        
    }
    
    /**
     * 참고 자료를 따라 구현함. 이 메서드에서 단순히 loadAuthorizationRequest() 메서드의 반환값을 
     * 그대로 반환하도록 한 이유는 권한 요청을 위한 상태 정보들을 쿠키에 담아 임시로 저장하는 방식을 채택할 경우, 
     * 이를 삭제하기 위해 별도의 메서드를 만들어 해당 메서드를 사용할 의도로 추정됨. 
     * 쿠키에 담긴 요청 정보를 삭제한다는 것을 부각 시키기 위한 의미도 있을 것으로 추측됨. 
     * 따라서 이 경우 별도의 메서드를 만들었기에 이 메서드를 오버라이딩할 필요가 없어 
     * 임시로 이러한 코드를 둔 것으로 추측됨. 
     * 
     */
    @Override
    public OAuth2AuthorizationRequest removeAuthorizationRequest(
        HttpServletRequest request,
        HttpServletResponse response
    ) {
        return loadAuthorizationRequest(request);
    }
    
    /**
     * 보안을 위해 권한 요청 관련 상태 정보들이 담긴 쿠키를 삭제한다. 
     * 
     * @param response
     */
    public void removeAuthorizationRequestCookies(
        HttpServletResponse response
    ) {
        
        cookieUtil.deleteCookies(
            response, 
            CookieNames.OAUTH2_AUTHORIZATION_REQUEST
        );
        
    }

}

```

코드 1-5. OAuth2AuthorizationRequestBasedOnCookieRepository.java

- `loadAuthorizationRequest` - 권한 요청을 가져오는 메서드. 여기서는 권한 요청을 쿠키에 담기 때문에 여러 쿠키들 중 OAuth 권한 요청 정보가 담긴 쿠키를 가져오고, 그 값을 역직렬화하여 객체로 변환하고 이를 반환한다.
- `saveAuthorizationRequest` - 권한 요청을 쿠키에 저장하는 메서드. 만약 들어온 권한 요청이 없다면 기존에 브라우저에 저장한 OAuth 권한 요청 정보가 담긴 쿠키를 삭제한다.
- `removeAuthorizationRequestCookies` - 권한 요청이 담긴 쿠키를 삭제하는 메서드. OAuth 인증이 끝난 후에는 권한 요청 정보들이 필요없어지므로 보안을 위해 해당 쿠키들을 삭제하는 것이다.

쿠키의 값에는 텍스트만 넣을 수 있기에 자바 객체를 텍스트로 직렬화하거나 반대 과정의 역직렬화으로 텍스트를 자바 객체로 변환하는 기능이 필요하다. 이 기능은 다음과 같이 구현하여 사용하였다. 

```java
package com.jerocaller.util;

import java.util.ArrayList;
import java.util.Base64;
import java.util.List;

import org.springframework.stereotype.Component;
import org.springframework.util.SerializationUtils;

import com.jerocaller.data.dto.request.CookieRequest;

import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

@Component
public class CookieUtil {
    
    public String extractJwtFromCookies(
        HttpServletRequest request, 
        String jwtCookieName
    ) {
        
        String token = null;
        Cookie[] cookies = request.getCookies();
        
        if (cookies != null) {
            for (Cookie cookie : cookies) {
                if (cookie.getName().equals(jwtCookieName)) {
                    token = cookie.getValue();
                    break;
                }
            }
        }
        
        return token;
    }
    
    public void addCookie(
        HttpServletResponse httpServletResponse,
        CookieRequest cookieRequest
    ) {
        
        Cookie cookie = new Cookie(
            cookieRequest.getCookieName(), 
            cookieRequest.getCookieValue()
        );
        
        // 클라이언트의 조작에 의한 변형 방지. XSS 공격 방지
        cookie.setHttpOnly(true);
        
        // HTTPS에서만 사용할 경우 true로 설정. HTTP에서도 사용할 경우 false.
        cookie.setSecure(false);
        
        // 전체 경로에서 사용 가능.
        cookie.setPath("/");
        
        cookie.setMaxAge(cookieRequest.getMaxAge());
        httpServletResponse.addCookie(cookie);
        
    }
    
    public void deleteCookies(
        HttpServletResponse httpResponse, 
        String... cookieNames
    ) {
        
        List<Cookie> cookies = new ArrayList<Cookie>();
        for (String cookieName : cookieNames) {
            Cookie cookie = new Cookie(cookieName, null);
            cookie.setPath("/");
            cookie.setHttpOnly(true);
            cookie.setMaxAge(0);
            cookies.add(cookie);
        }
        
        cookies.forEach(cookie -> {
           httpResponse.addCookie(cookie);
        });
        
    }
    
    /**
     * 객체 직렬화 후 쿠키의 값으로 변환
     * 
     * @param obj
     * @return
     */
    public String serialize(Object obj) {
        return Base64.getUrlEncoder()
            .encodeToString(SerializationUtils.serialize(obj));
    }
    
    public <T> T deserialize(Cookie cookie, Class<T> cls) {
        
        byte[] decodedBytes = Base64.getUrlDecoder().decode(cookie.getValue());
        Object deserializedObj = SerializationUtils.deserialize(decodedBytes);
        return cls.cast(deserializedObj);
    }
    
}

```

코드 1-6. CookieUtil.java

위 코드에서 각각 `serialize()` 및 `deserialize()` 메서드가 쿠키 값의 직렬화 및 역직렬화를 구현하고 있다. 한 편 이 클래스에서는 그 외에도 특정 쿠키를 추가하거나 삭제하는 메서드, JWT 사용 시 쿠키에 담긴 JWT를 추출하는 메서드도 포함되어 있다. 

이번에는 OAuth 로그인 성공 시 후처리를 담당할 핸들러이다.

```java
package com.jerocaller.config.oauth;

import java.io.IOException;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.core.Authentication;
import org.springframework.security.oauth2.core.user.OAuth2User;
import org.springframework.security.web.authentication.SimpleUrlAuthenticationSuccessHandler;
import org.springframework.stereotype.Component;

import com.jerocaller.business.MemberService;
import com.jerocaller.business.RefreshTokenService;
import com.jerocaller.common.ExpirationTime;
import com.jerocaller.data.dto.request.CookieRequest;
import com.jerocaller.data.entity.Member;
import com.jerocaller.data.entity.RefreshToken;
import com.jerocaller.jwt.JwtTokenProvider;
import com.jerocaller.util.CookieUtil;
import com.jerocaller.util.MapUtil;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.RequiredArgsConstructor;
import lombok.extern.slf4j.Slf4j;

@Component
@RequiredArgsConstructor
@Slf4j
public class OAuth2SuccessHandler 
    extends SimpleUrlAuthenticationSuccessHandler {
    
    private final JwtTokenProvider jwtTokenProvider;
    private final OAuth2AuthorizationRequestBasedOnCookieRepository 
        authorizationRequestRepository;
    private final MemberService memberService;
    private final RefreshTokenService refreshTokenService;
    private final CookieUtil cookieUtil;
    
    @Value("${frontend.url.oauth2.login.success}")
    private String REDIRECT_URI;
    
    @Override
    public void onAuthenticationSuccess(
        HttpServletRequest request, 
        HttpServletResponse response,
        Authentication authentication
    ) throws IOException, ServletException {
        
        // 리프레시 토큰 DB 저장을 위해 DB로부터 현재 유저 정보 추출.
        OAuth2User oAuth2User = (OAuth2User) authentication.getPrincipal();
        Member member = getMemberFromDB(oAuth2User);
        
        // 구글로부터 얻어온 사용자 정보에는 무엇이 있는지 logging
        MapUtil.logMap(oAuth2User.getAttributes());
        
        // 액세스 토큰 및 리프레시 토큰 발행 및 두 토큰 모두 쿠키에 전송
        publishTokensAndAddToCookie(request, response, member);
        
        // 인증을 위해 생성, 저장된 관련 설정값들을 보안을 위해 쿠키, 세션에서 제거.
        clearAuthenticationStates(request, response);
        
        getRedirectStrategy().sendRedirect(request, response, REDIRECT_URI);
        
    }
    
    private Member getMemberFromDB(OAuth2User oAuth2User) {
        
        String emailByOAuth = oAuth2User.getAttribute("email");
        
        Member member = memberService.getByEmail(emailByOAuth);
        
        return member;
    }
    
    private void publishTokensAndAddToCookie(
        HttpServletRequest request, 
        HttpServletResponse response, 
        Member member
    ) {
        
        // 액세스 토큰 발급
        String accessToken = jwtTokenProvider.createToken(
            member, 
            ExpirationTime.ACCESS_TOKEN_IN_MILLI_SECONDS
        );
        
        // 리프레시 토큰 발급
        RefreshToken refreshToken = refreshTokenService
            .createAndSaveRefreshToken(member);
        
        // 두 토큰 모두 쿠키에 전송
        CookieRequest accessTokenRequest = CookieRequest
            .toRequestForAccessToken(accessToken);
        CookieRequest refreshTokenRequest = CookieRequest
            .toRequestForRefreshToken(refreshToken.getRefreshToken());
        addTokenToCookie(
            request, 
            response, 
            accessTokenRequest, 
            refreshTokenRequest
        );
        
    }
    
    private void addTokenToCookie(
        HttpServletRequest request, 
        HttpServletResponse response,
        CookieRequest ...cookieRequests
    ) {
        
        for (CookieRequest cookieRequest : cookieRequests) {
            // 기존에 해당 토큰이 쿠키에 있다면 이를 먼저 삭제
            cookieUtil.deleteCookies(response, cookieRequest.getCookieName());
            
            // 새 토큰을 쿠키에 담아 응답에 싣는다.
            cookieUtil.addCookie(response, cookieRequest);
        }
        
    }
    
    /**
     * 인증 과정에서 임시로 생성, 저장된 인증 정보를 삭제. 
     * 쿠키 및 세션에 있을 수 있는 인증 정보 삭제.
     * 
     * @param request
     * @param response
     */
    private void clearAuthenticationStates(
        HttpServletRequest request, 
        HttpServletResponse response
    ) {
        
        // 인증 과정에서 세션에 저장되어 있을 수 있는 인증 관련 정보 삭제.
        super.clearAuthenticationAttributes(request);
        
        // 쿠키에 존재하는 인증 관련 정보 삭제.
        authorizationRequestRepository
            .removeAuthorizationRequestCookies(response);
        
    }
    
}

```

코드 1-7. OAuth2SuccessHandler.java

이 클래스는 SimpleUrlAuthenticationSuccessHandler라는 기존에 존재하는 클래스를 상속하여 구현하였고, 해당 클래스는 AuthenticationSuccessHandler라는 인터페이스를 구현하는데, 시큐리티 설정 코드인 `successHandler()` 메서드 인자에는 해당 인터페이스 타입의 객체를 넣기만 하면 되기에 사실 AuthenticationSuccessHandler를 바로 구현해도 된다. 

여기서 JwtTokenProvider, RefreshTokenService 등 JWT 관련한 코드들이 있는데, 이는 이전에 “[[Spring Security] JWT 기반 인증](/spring/spring-security-jwt-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D/)” 글에서 JWT 기반 인증을 구현하기 위해 사용한 코드들을 그대로 가져와 사용하고 있다. 여기서는 이를 조금씩 변형시켜 사용하였다. 이에 대한 전체 소스 코드는 앞서 소개한 백엔드 깃허브 repository 내에 jwt 및 exception.security 패키지, 그리고 business 패키지의 RefreshTokenService, TokenService 등을 참고하면 되겠다. JWT에 대한 개념은 [[Spring Security] JWT 기반 인증](/spring/spring-security-jwt-%EA%B8%B0%EB%B0%98-%EC%9D%B8%EC%A6%9D/) 이 글을 참조.

어쨌거나 해당 부모 클래스의 `onAuthenticationSuccess()` (AuthenticationSuccessHandler 인터페이스에 속해있다)를 오버라이딩하였다. 여기서는 코드에서도 볼 수 있겠지만, OAuth 로그인 성공 후 다음의 작업들을 수행하도록 하였다.

- 액세스 및 리프레시 토큰 발급 및 리프레시 토큰의 DB 저장.
- 두 토큰을 쿠키로 담아 응답에 싣는다.
    - 원래 일반적으로는 리프레시 토큰만 쿠키에 싣고, 액세스 토큰은 응답 바디에 실어 응답한다고 한다. 그러나 필자는 이후에 다룰 “구글 OAuth의 CORS 미지원 문제” 해결을 위해 어쩔 수 없이 두 토큰을 쿠키에 담은 것이었다. 이에 대해선 나중에 별도의 글에서 설명하겠다.
- OAuth 인증 과정에서 사용된 임시로 저장된 쿠키들을 보안을 위해 삭제.
- 프론트엔드 도메인으로 리다이렉트.

프론트엔드 도메인 측 URI는 application.properties 파일에 다음과 같이 지정하였다. 

```java
frontend.url.oauth2.login.success=http://localhost:3000/oauth2/login/success
```

코드 1-8. application.properties 일부

위 속성의 key, value 모두 필자가 임의대로 설정하였다. 

여기서는 앞서 말했듯 백엔드와 프론트엔드를 분리하는 구조를 택하였는데, 이 방법이건, 같은 스프링 부트 프로젝트 내부에 Thymeleaf 등의 도구들을 이용하여 화면을 렌더링하여 같은 도메인 내에서 처리하는 방법을 택하건 결국 공통적으로는 successHandler에서 로그인 성공 후 관련 응답을 생성하는 컨트롤러 메서드로 리다이렉트한 후, 해당 메서드로부터 응답을 받아와 이를 토대로 화면을 구성하는게 일반적이다. 그러나 필자는 이러한 방식으로 구현할 수 없었다. CSR 방식인데다가, 프론트엔드 측에서는 자바스크립트 측 기술인 axios를 이용하여 토큰을 받아 이를 토대로 화면을 렌더링하도록 구성하였으나, 결국에는 구글의 CORS 미지원으로 인해 CORS 에러가 생겼었다. 이에 대해선 나중에 자세히 후술하겠는데, 이 CORS 문제를 해결하기 위해 위 코드에서는 두 토큰 모두 쿠키에 담은 후, 바로 프론트엔드 도메인 측으로 리다이렉트하도록 구성한 것이다. 그러다보니 서버 내 컨트롤러 메서드로 리다이렉트하도록 구성한 다른 참고 자료들에 비하면 확연히 다르게 구성하긴 했다. 

다음은 OAuth 로그인 실패 시 처리할 핸들러 코드이다. 

```java
package com.jerocaller.config.oauth;

import java.io.IOException;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.security.core.AuthenticationException;
import org.springframework.security.web.authentication.AuthenticationFailureHandler;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class OAuth2FailureHandler implements AuthenticationFailureHandler {
    
    @Value("${frontend.url.oauth2.login.failure}")
    private String REDIRECT_URI;

    @Override
    public void onAuthenticationFailure(
        HttpServletRequest request, 
        HttpServletResponse response,
        AuthenticationException exception
    ) throws IOException, ServletException {
        
        log.error("=== OAuth2 로그인 중 에러 발생 ===");
        log.error(exception.getMessage());
        response.sendRedirect(REDIRECT_URI);
        
    }
    
}

```

코드 1-9. OAuth2FailureHandler.java

여기서도 로그인 실패 시 프론트엔드 도메인의 특정 URI로 리다이렉트하여 로그인 실패 관련 화면은 프론트엔드에서 구성하도록 위임하였다. 해당 URI도 마찬가지로 application.properties에 필자가 임의로 다음과 같이 설정하였다. 

```java
frontend.url.ouath2.login.failure=http://localhost:3000/oauth2/login/failure
```

코드 1-10. application.properties 일부

이로서 OAuth 로그인 기능을 구현하기 위한 백엔드 쪽 코드는 모두 완성하였다. 여기서 필자는 프론트엔드 측에서 구글 로그인 창을 요청할 URI 및 권한 코드를 받아올 redirect URI 모두 구성하지 않았다. 이 둘 모두 OAuth2-client 의존성에서 기본적으로 제공하는 URI를 이용하였기 때문이다. 이 경우 개발자가 별도의 컨트롤러를 작성하지 않아도 된다. 

프론트엔드 쪽에서 사용자가 구글 로그인 버튼을 눌러 구글 로그인 창을 요청할 URI은 기본적으로 “/oauth2/authorization/my-oidc-client”으로 지정되어 있다고 한다. 여기서는 구글을 이용하기에 “/oauth2/authorization/google” URI를 이용하면 된다. 물론 이 URI를 개발자가 원하는대로 바꿀 수도 있는데, 스프링 공식 문서에 따르면 다음과 같은 방식으로 바꾸면 된다고 한다. 

```java
@Configuration
@EnableWebSecurity
public class OAuth2LoginSecurityConfig {

	@Bean
	public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
		http
			.oauth2Login(oauth2 -> oauth2
			    ...
			    .authorizationEndpoint(authorization -> authorization
			        .baseUri("/login/oauth2/authorization")
			        ...
			    )
			);
		return http.build();
	}
}
```

참고 코드. OAuth 로그인 URI를 커스터마이징하는 코드. `.oauth2Login()` 의 `.authorizationEndpoint()` 의 `.baseUri()` 메서드에 원하는 URI를 입력하면 된다. 출처: [https://docs.spring.io/spring-security/reference/servlet/oauth2/login/advanced.html#oauth2login-advanced-login-page](https://docs.spring.io/spring-security/reference/servlet/oauth2/login/advanced.html#oauth2login-advanced-login-page)

또한, 권한 서버로부터 권한 코드를 받아올 redirection uri도 기본적으로는 “/login/oauth2/code/my-oidc-client” 형식으로 지정되어 있다고 한다. 구글을 사용하고 있으므로 “/login/oauth2/code/google”라는 URI을 사용하게 된다. 이는 이전 글에서 구글 클라우드 콘솔의 OAuth 클라이언트 등록 시 “승인된 Redirect URI”에 입력한 그 URI이다. 한 편 이 URI 또한 개발자가 원하는대로 커스터마이징 할 수 있다. 스프링 공식 문서에 따르면 다음과 같은 형식으로 바꿀 수 있다고 한다. 

```java
@Configuration
@EnableWebSecurity
public class OAuth2LoginSecurityConfig {

    @Bean
	public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
		http
			.oauth2Login(oauth2 -> oauth2
			    .redirectionEndpoint(redirection -> redirection
			        .baseUri("/login/oauth2/callback/*")
			        ...
			    )
			);
		return http.build();
	}
}
```

참고 코드. OAuth 인증 과정에서 권한 서버로부터 권한 코드를 받기 위해 사용될 Redirect URI를 커스터마이징 하는 코드. `.oauth2Login()` 의 `.redirectEndpoint()` 의 `.baseUri()` 메서드의 인자에 원하는 URI를 입력하면 된다. 출처: [https://docs.spring.io/spring-security/reference/servlet/oauth2/login/advanced.html#oauth2login-advanced-redirection-endpoint](https://docs.spring.io/spring-security/reference/servlet/oauth2/login/advanced.html#oauth2login-advanced-redirection-endpoint)

이 경우에는 application.properties에 redirect-uri라는 속성으로 해당 URI를 지정해야 할 것이다. 또한 구글 클라우드 콘솔에 지정한 “승인된 Redirect URI”도 이와 동일하게 맞췄는지 확인해야 할 것이다. 

어찌돼었건 백엔드 측에서는 OAuth 로그인 연동 기능을 모두 구현하였다. 

### 프론트엔드

여기서는 OAuth를 이용한 구글 연동 로그인이 되는지 여부만 확인해볼 것이기에 프론트엔드 측에서는 굉장히 간단하게 구성하였다. 여기서 사용된 리액트 컴포넌트로는 3가지 뿐이다. 구글 로그인 성공 시 구글 계정의 닉네임, 이메일 주소, 클라이언트 앱에서의 role 정보를 보여줄 MyPage, 구글 로그인 요청용 Login, 로그인 실패 시 스프링 부트 서버에 의해 리다이렉트되어 보여질 로그인 실패 알림 페이지 LoginFailure이다. 

```jsx
import './App.css';
import Login from './pages/Login';
import axios from 'axios';
import { Route, Routes } from 'react-router-dom';
import MyPage from './pages/MyPage';
import LoginFailure from './pages/LoginFailure';

axios.defaults.withCredentials = true;

function App() {

  return (
    <div className='App'>
      <Routes>
        <Route path="/" element={<Login />} />
        <Route path="/oauth2/login/success" element={<MyPage />} />
        <Route path="/oauth2/login/failure" element={<LoginFailure/>} />
      </Routes>
    </div>
  );
}

export default App;

```

코드 2-1. App.js

```jsx
import React from 'react';
import ReactDOM from 'react-dom/client';
import './index.css';
import App from './App';
import reportWebVitals from './reportWebVitals';
import { BrowserRouter } from 'react-router-dom';

const root = ReactDOM.createRoot(document.getElementById('root'));
root.render(
  <React.StrictMode>
    <BrowserRouter>
      <App />
    </BrowserRouter>
  </React.StrictMode>
);

// If you want to start measuring performance in your app, pass a function
// to log results (for example: reportWebVitals(console.log))
// or send to an analytics endpoint. Learn more: https://bit.ly/CRA-vitals
reportWebVitals();

```

코드 2-2. index.js

여기서는 SPA(Single Page Application)를 구현하기 위해 react-router-dom을 사용하여 페이지 이동하게끔 하였다. 

```jsx
import axios from 'axios';

const Login = () => {

  /**
   * 주의사항) 구글 OAuth에서는 CORS를 지원하지 않는다고 한다. 
   * 따라서 axios와 같은 방식을 이용한 요청은 CORS 문제에 의해 
   * 구글 로그인 창이 뜨지 않는 문제점이 발생한다. 
   * 
   * 따라서 <a href="..."> 또는 <form>을 대신 이용해야 한다. 
   * 아래 함수는 CORS 문제에 의해 작동하지 않는 함수.
   * 
   * @param {Event} event 
   */
  const handleOAuthLogin = (event) => {
    event.preventDefault();

    axios.get("http://localhost:8080/oauth2/authorization/google", {
      headers: {
        'Content-Type': 'application/json;charset=utf-8'
      }
    })
      .then(response => {
        console.log(response);
        console.log(response.data);
      })
      .catch(error => {
        console.log(error);
        console.log(error.code);
      });
  
  }

  return (
    <div>
      <h3>소셜 로그인</h3>
      {/*<button onClick={handleOAuthLogin}>Google</button>*/}
      {<a href="http://localhost:8080/oauth2/authorization/google">Google</a>}
    </div>
  );
};

export default Login;
```

코드 2-3. Login.jsx

위 코드의 주석으로도 설명하였지만, 구글의 경우 CORS를 지원하지 않기에 이 문제를 피하기 위해선 axios, fetch 같은 자바스크립트 기술을 사용하여 사용자의 구글 계정 정보를 가져와 화면을 구성하는 방식을 택할 수 없었다. 그래서 대신 CORS 문제를 회피할 수 있도록 a 태그를 이용하였다. a, form 등의 HTML 태그를 이용하면 CORS 문제를 회피할 수 있다고 하여 위와 같이 작성한 것이다. 

```jsx
import { useEffect, useState } from "react";
import { useNavigate } from "react-router-dom";
import axios from "axios";

const MyPage = () => {

  // 로그인 성공한 인증자의 현재 정보를 가져와 저장하기 위한 상태 변수.
  const [myInfo, setMyInfo] = useState(null);

  const navigator = useNavigate();

  // 로그인 성공 후 현재 유저의 정보를 백엔드로부터 가져온다. 
  useEffect(() => {
    axios.get("http://localhost:8080/members/my")
      .then(response => {
        console.log(response);
        if (response.data != null) {
          setMyInfo(response.data.data);
        }
      })
      .catch(error => {
        console.error(error);
      });
  }, []);

  /**
   * 
   * @param {Event} event 
   */
  const handleLogout = (event) => {
    event.preventDefault();

    axios.post("http://localhost:8080/auth/logout")
      .then(response => {
        alert("로그아웃에 성공하였습니다.");

        // 메인 페이지로 이동
        navigator("/");
      })
      .catch(error => {
        console.error(error);
      });
  };

  if (myInfo == null) {
    return (
      <div>
        <h3>인증에 성공하였으나 인증 정보를 가져오는데에 실패했습니다.</h3>
      </div>
    );
  }

  return (
    <div>
      <h3>소셜 로그인 성공</h3>
      <p>사용자 정보</p>
      <ul>
        <li>username : {myInfo.username}</li>
        <li>email : {myInfo.email}</li>
        <li>role : {myInfo.role}</li>
      </ul>
      <button onClick={handleLogout}>로그아웃하기</button>
    </div>
  );
};

export default MyPage;
```

코드 2-4. MyPage.jsx

위 코드는 OAuth 로그인 성공 시 백엔드의 OAuth2SuccessHandler에 의해 리다이렉트되는 곳으로, 프론트엔드 도메인으로 리다이렉트된 뒤에는 응답 받은 쿠키 내 액세스 토큰을 이용하여 현재 인증된 사용자의 구글 계정 정보를 보여주는 REST API를 호출하여 현재 사용자의 정보를 보여주도록 구성하였다. 

여기서부터는 이제 구글로 요청할 일이 없기에 구글과 관련된 CORS 문제로부터 자유로워 axios를 이용해도 된다. 스프링 부트 서버 내에는 앞서 시큐리티 설정 코드에서도 봤겠지만 CORS 지원을 위한 설정을 하였기에 프론트엔드 측에서 데이터를 axios로 받을 수 있게 된다. 

다음은 로그인 실패 시 리다이렉트될 페이지를 구성하는 코드이다. 여기서는 간단히 로그인에 실패했다는 문구만 출력하였다. 

```jsx
import { Link } from "react-router-dom";

const LoginFailure = () => {

  return (
    <div>
      <p>소셜 로그인에 실패했습니다. </p>

      <Link to={"/"}>로그인 화면으로 돌아가기</Link>
    </div>
  );
};

export default LoginFailure;
```

코드 2-5. LoginFailure.jsx

## 구글 로그인 시연

![사진 1-1. 첫 화면. “Google”을 누르면 구글 로그인 창으로 리다이렉트된다. ](/images/2025-03-12/spring-security-oauth2%EB%A1%9C%20%EC%99%B8%EB%B6%80%20%EC%82%AC%EC%9D%B4%ED%8A%B8%20%EA%B3%84%EC%A0%95%EC%9C%BC%EB%A1%9C%20%EC%86%90%EC%89%BD%EA%B2%8C%20%EC%9D%B8%EC%A6%9D%ED%95%98%EA%B8%B0%20(2)%20-%20OAuth2-Client%EB%A1%9C%20%EC%89%BD%EA%B2%8C%20%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0/1.png)

사진 1-1. 첫 화면. “Google”을 누르면 구글 로그인 창으로 리다이렉트된다. 

![사진 1-2. 첫 구글 로그인 화면. 여기에 이메일 입력 후 패스워드 입력하여 구글 계정으로 로그인하는 과정을 진행하면 된다. 참고로 이전에 구글 클라우드 콘솔에서 설정했던 앱 이름인 “springboot-test”가 뜬 것도 볼 수 있다. ](/images/2025-03-12/spring-security-oauth2%EB%A1%9C%20%EC%99%B8%EB%B6%80%20%EC%82%AC%EC%9D%B4%ED%8A%B8%20%EA%B3%84%EC%A0%95%EC%9C%BC%EB%A1%9C%20%EC%86%90%EC%89%BD%EA%B2%8C%20%EC%9D%B8%EC%A6%9D%ED%95%98%EA%B8%B0%20(2)%20-%20OAuth2-Client%EB%A1%9C%20%EC%89%BD%EA%B2%8C%20%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0/2.png)

사진 1-2. 첫 구글 로그인 화면. 여기에 이메일 입력 후 패스워드 입력하여 구글 계정으로 로그인하는 과정을 진행하면 된다. 참고로 이전에 구글 클라우드 콘솔에서 설정했던 앱 이름인 “springboot-test”가 뜬 것도 볼 수 있다. 

![사진 1-3. “springboot-test”라는 클라이언트 앱에 처음 로그인하는 경우 해당 앱에서 사용자의 구글 계정 정보 접근에 허용할 것인지를 물어보는 화면이다. 여기서 “계속”을 누르면 로그인을 계속 진행할 수 있다. ](/images/2025-03-12/spring-security-oauth2%EB%A1%9C%20%EC%99%B8%EB%B6%80%20%EC%82%AC%EC%9D%B4%ED%8A%B8%20%EA%B3%84%EC%A0%95%EC%9C%BC%EB%A1%9C%20%EC%86%90%EC%89%BD%EA%B2%8C%20%EC%9D%B8%EC%A6%9D%ED%95%98%EA%B8%B0%20(2)%20-%20OAuth2-Client%EB%A1%9C%20%EC%89%BD%EA%B2%8C%20%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0/3.png)

사진 1-3. “springboot-test”라는 클라이언트 앱에 처음 로그인하는 경우 해당 앱에서 사용자의 구글 계정 정보 접근에 허용할 것인지를 물어보는 화면이다. 여기서 “계속”을 누르면 로그인을 계속 진행할 수 있다. 

![사진 1-4. 구글 로그인 성공 후 구글 계정 정보를 가져와 화면에 출력한 모습. 여기에선 유저네임 및 이메일, 그리고 이 클라이언트 앱에서 사용자의 역할(role)만 출력하도록 하였다. 여기서 role은 백엔드 서버에서 무조건 USER로 설정하였다. 한 편 오른쪽을 보면 백엔드 서버에서 보내준 두 토큰이 쿠키로 저장된 것도 볼 수 있다. 이 시기에는 DB에 해당 사용자의 구글 계정 정보도 저장되어 있다. ](/images/2025-03-12/spring-security-oauth2%EB%A1%9C%20%EC%99%B8%EB%B6%80%20%EC%82%AC%EC%9D%B4%ED%8A%B8%20%EA%B3%84%EC%A0%95%EC%9C%BC%EB%A1%9C%20%EC%86%90%EC%89%BD%EA%B2%8C%20%EC%9D%B8%EC%A6%9D%ED%95%98%EA%B8%B0%20(2)%20-%20OAuth2-Client%EB%A1%9C%20%EC%89%BD%EA%B2%8C%20%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0/4.png)

사진 1-4. 구글 로그인 성공 후 구글 계정 정보를 가져와 화면에 출력한 모습. 여기에선 유저네임 및 이메일, 그리고 이 클라이언트 앱에서 사용자의 역할(role)만 출력하도록 하였다. 여기서 role은 백엔드 서버에서 무조건 USER로 설정하였다. 한 편 오른쪽을 보면 백엔드 서버에서 보내준 두 토큰이 쿠키로 저장된 것도 볼 수 있다. 이 시기에는 DB에 해당 사용자의 구글 계정 정보도 저장되어 있다. 

![사진 1-5. 로그아웃하기. 여기서는 클라이언트 앱에서의 로그아웃만 진행되는 것이지 구글 계정에서 자체적으로 로그아웃되는 것은 아니다. 보통 크롬 브라우저를 사용하면 크롬 브라우저에 구글 계정으로 로그인된 상태일 텐데, 거기서 로그아웃을 해야 구글 계정을 완전히 로그아웃할 수 있다. 한 편, 로그아웃 실행 후 두 토큰이 담긴 쿠키가 삭제되어 로그아웃 절차가 잘 진행된 것을 볼 수 있다. ](/images/2025-03-12/spring-security-oauth2%EB%A1%9C%20%EC%99%B8%EB%B6%80%20%EC%82%AC%EC%9D%B4%ED%8A%B8%20%EA%B3%84%EC%A0%95%EC%9C%BC%EB%A1%9C%20%EC%86%90%EC%89%BD%EA%B2%8C%20%EC%9D%B8%EC%A6%9D%ED%95%98%EA%B8%B0%20(2)%20-%20OAuth2-Client%EB%A1%9C%20%EC%89%BD%EA%B2%8C%20%EA%B5%AC%ED%98%84%ED%95%98%EA%B8%B0/5.png)

사진 1-5. 로그아웃하기. 여기서는 클라이언트 앱에서의 로그아웃만 진행되는 것이지 구글 계정에서 자체적으로 로그아웃되는 것은 아니다. 보통 크롬 브라우저를 사용하면 크롬 브라우저에 구글 계정으로 로그인된 상태일 텐데, 거기서 로그아웃을 해야 구글 계정을 완전히 로그아웃할 수 있다. 한 편, 로그아웃 실행 후 두 토큰이 담긴 쿠키가 삭제되어 로그아웃 절차가 잘 진행된 것을 볼 수 있다. 

# 글을 마치며…

이번 글에서는 스프링 부트에서 OAuth2-client를 이용하여 구글 연동 로그인을 구현하고, 프론트엔드 측에서는 리액트로 구현하여 구글 로그인 관련 화면을 구성하는 방식에 대해 살펴보았다. 이 글에서도 잠깐 언급했지만, 구글의 경우 CORS 미지원 문제가 있어 구글 로그인 창으로의 리다이렉트가 안되었던 문제를 겪었었다. 이 글에서 보인 코드들은 이 문제를 해결한 후의 코드이다. 다음 글에서는 구글 CORS 미지원 문제와 이를 해결하려고 필자가 거친 과정들에 대해 설명하겠다. 

---

References

[1] 신선영, “스프링 부트 3 백엔드 개발자 되기 - 자바 편, 2판”, ch10, (골든래빗)

[2] 스프링 공식 사이트에서의 OAuth2 튜토리얼

[Getting Started \| Spring Boot and OAuth2](https://spring.io/guides/tutorials/spring-boot-oauth2)

[3] Spring Security 공식 문서 내 OAuth2에 대한 문서

[OAuth2 :: Spring Security](https://docs.spring.io/spring-security/reference/servlet/oauth2/index.html)

[4] Spring Security에서 제공하는 OAuth2-Client 라이브러리를 이용한 OAuth2 구현

[oauth2-client 라이브러리를 이용하여 OAuth2 구현하기 (Kakao, Github)](https://rrddo.tistory.com/250)

[5] OAuth2-Client를 이용한 OAuth2 구현. (REST API)

[[Spring] Spring Security + OAuth2 + JWT](https://do5do.tistory.com/20)

[6] “*[Security] Spring JWT 인증 With REST API(OAuth2.0 추가) (3)***”**

[[Security] Spring JWT 인증 With REST API(OAuth2.0 추가) (3)](https://gilssang97.tistory.com/58)

[7] “*Spring _구글 로그인 REST API 구현 OAuth2***”**

[Spring _구글 로그인 REST API 구현 OAuth2](https://dev-chw.tistory.com/21)

[8] 프론트엔드 측의 CORS 에러 방지를 위한 구글 콘솔 설정 방법

[소셜로그인 2. 구글 콘솔에서 Oauth 앱 생성하기 - 승인된 리다이렉션 URI란](https://yelimkim98.tistory.com/45)

[9] 프론트엔드 측의 CORS 에러 발생 및 해결책

[#2. OAuth2 구현시 CORS 에러](https://dvdhan.tistory.com/193)

[10] 구글 로그인 구현 시 CORS 문제 발생 시 프론트 측에서의 해결책.

[[로그인] 구글 로그인 CORS 에러 해결하기](https://velog.io/@bosun0214/%EB%A1%9C%EA%B7%B8%EC%9D%B8-302-redirect-%EC%97%90%EB%9F%AC-%ED%95%B4%EA%B2%B0%EA%B8%B0)

[11] CustomOAuth2UserService 작동 안되는 원인과 해결책

[[OAuth2] 구글 로그인이 안 된다면](https://ryumodrn.tistory.com/59)