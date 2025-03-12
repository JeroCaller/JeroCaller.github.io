---
title: "[Spring Security] OAuth2로 외부 사이트 계정으로 손쉽게 인증하기 (3) - 구글의 CORS 미지원 문제 TroubleShooting"
category: "Spring"
tag: ["Spring", "Spring boot", "Spring Security", "인증", "보안", "Authentication", "Authorization", "Auth", "JWT", "OAuth", "OAuth2", "OAuth2 Client", "CORS", "Google", "Trouble Shooting"]
---

연관된 이전 글들

- [[Spring Security] OAuth2로 외부 사이트 계정으로 손쉽게 인증하기 (1) - 이론 및 설정 편](/spring/spring-security-oauth2로-외부-사이트-계정으로-손쉽게-인증하기-(1)-이론-및-설정-편/)
- [[Spring Security] OAuth2로 외부 사이트 계정으로 손쉽게 인증하기 (2) - OAuth2-Client로 쉽게 구현하기](/spring/spring-security-oauth2로-외부-사이트-계정으로-손쉽게-인증하기-(2)-OAuth2-Client로-쉽게-구현하기/)

이전 글 [[Spring Security] OAuth2로 외부 사이트 계정으로 손쉽게 인증하기 (2) - OAuth2-Client로 쉽게 구현하기](/spring/spring-security-oauth2로-외부-사이트-계정으로-손쉽게-인증하기-(2)-OAuth2-Client로-쉽게-구현하기/) 에서는 구글에서 CORS 미지원으로 인해 이를 해결하기 위해 필자가 참고한 다른 분들의 참고 자료들과는 그 구조를 조금 다르게 구성하였다고 했었다. 이 글에서는 이 문제를 해결해나간 과정에 대해 설명하고자 한다. CORS 문제까지 해결한 후의 전체 코드는 이미 해당 글에 보였으므로 해당 글을 참고하길 바란다. 

# 배경 지식

## CORS (Cross Origin Resource Sharing)

CORS(Cross Origin Resource Sharing), 즉 “교차 출처 리소스 공유”는 서로 다른 출처 간의 리소스 공유 허용 여부를 정하는 정책이다. 

여기서 출처(Origin)는 HTTP URL에서 protocol, host(domain), port 번호 이 세 개를 아우르는 개념이다. 

```jsx
https://www.mysite.com:8080
protocol - host - port
```

어떤 두 출처에 대해 이 세 가지 모두 동일하다면 서로 같은 출처이며, 이 셋 중 하나라도 다르면 그 둘은 서로 다른 출처로 판별된다. 개발 단계에서 백엔드에서 스프링 부트를 이용하여 제작한 서버의 출처는 보통 http://localhost:8080 이고, 리액트를 이용하여 제작한 프론트엔드의 출처는 http://localhost:3000인데, 프로토콜과 호스트는 같아도 포트번호가 다르기에 이 둘은 서로 다른 출처인 셈이다. 

즉, “교차 출처 리소스 공유”라는 것은 서로 다른 두 출처 사이에서 텍스트, 이미지 등의 리소스를 공유하는 것을 의미한다. 원래 기본적으로 브라우저에는 동일 출처 정책(Same Origin Policy, SOP)이 적용되어 있어 다른 출처로부터 리소스를 요청하거나 응답하는 것이 금지되어 있다. 이러한 동일 출처 정책의 이유는 다름아닌 보안 때문이다. 만약 이러한 정책이 없었다면 외부의 악성 웹 사이트로부터 악성 요청이 들어오는 것을 막기가 힘들 것이다. 따라서 CORS는 XSS(Cross Site Scripting), CSRF(Cross Site Request Forgery)와 같은 보안 공격을 방지하기 위해 도입된 개념인 것이다. 

동일 출처 정책에 따라 요청을 하는 출처와 응답을 하는 출처가 같은지를 비교하는 주체는 브라우저이며, 두 출처가 서로 같은 지 여부는 다음의 과정을 통해 진행된다고 한다.

1. 요청 헤더들 중 Origin 헤더의 값을 참조한다. 여기에는 요청을 하는 출처 URL이 담겨져 있다. ex) `Origin: http://localhost:3000`
2. 응답 헤더들 중 Access-Control-Allow-Origin이라는 헤더의 값을 참조한다. 여기에도 응답을 하는 출처의 URL이 담겨져 있다. ex) `Access-Control-Allow-Origin: http://localhost:3000` 
3. 브라우저는 이 두 헤더 값이 서로 같은 지를 비교하여 차단 여부를 결정하는 것이다. 서로 같으면 동일 출처 정책에 따라 허용하고, 같지 않으면 차단하여 브라우저 콘솔에 에러 메시지를 출력한다. 

이로 인해, 서버 측에서 정상적인 응답을 내려도 두 헤더값이 다르면 동일 출처 정책이 적용되어 브라우저에서 이를 차단하기에 브라우저에서는 개발자가 원하는 결과를 얻을 수 없는 것이다. 

이러한 원리에 따르면, 다른 출처 간의 요청-응답이 가능하려면 응답을 전송하는 서버 측에서 응답 헤더 중 Access-Control-Allow-Origin 헤더를 추가해줘야 한다. 스프링 시큐리티를 이용하여 서버 구축 시 다음과 같이 설정하면 다른 출처에서 오는 요청을 허락할 수 있다. 

```java
@Bean
public SecurityFilterChain securityFilterChain(
    HttpSecurity httpSecurity
) throws Exception {
        
    httpSecurity
        // 생략...
        .cors(cors -> cors.configurationSource(corsConfigurationSource()))
        // 생략...
        return httpSecurity.build();
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
        
    //configuration.setAllowedHeaders(Arrays.asList(
    //    "Access-Control-Allow-Origin"
    //));
        
    // 클라이언트로부터 인증 정보(credential) 전송 시
    // 이를 받도록 허용할 것인지 여부 설정. 
    // 여기 백엔드에서와 프론트엔드 둘 모두 true로 설정해야 둘 사이에서 인증 정보를 교환할 수 있다. 
    configuration.setAllowCredentials(true); 
        
    UrlBasedCorsConfigurationSource source = new UrlBasedCorsConfigurationSource();
    source.registerCorsConfiguration("/**", configuration);
    return source;
}
```

코드 1-1. 스프링 시큐리티 이용 시 CORS 설정

위 코드에서는 “http://localhost:3000”이라는 다른 출처에서의 요청을 허락하고, 해당 출처에서 GET, POST 등의 여러 HTTP method들을 이용한 요청을 허락하고 있다. 이러한 설정을 통해 응답 헤더에 자동으로 Access-Control-Allow-Origin 헤더가 추가되어 CORS가 허용되는 셈이다. 해당 헤더가 자동으로 추가됨을 확인하고자 한다면 다음의 필터를 작성하여 적용해보면 되겠다.

```java
package com.jerocaller.config.filter;

import java.io.IOException;

import org.springframework.web.filter.OncePerRequestFilter;

import jakarta.servlet.FilterChain;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;
import lombok.extern.slf4j.Slf4j;

/**
 * 구글 OAuth 로그인에 대해 CORS 에러가 발생하여 이를 바로잡기 위한 로깅 필터. 
 * 현재는 해당 문제 해결함.
 */
@Slf4j
public class CorsLoggingFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
        HttpServletRequest request, 
        HttpServletResponse response, 
        FilterChain filterChain
    ) throws ServletException, IOException {
        
        log.info("===== CORS 필터 logging 시작 =====");
        
        String originOnRequest = request.getHeader("Origin");
        log.info("CORS Request Origin: {}", originOnRequest);
        
        filterChain.doFilter(request, response);
        
        String accessControlAllowOrigin = response
            .getHeader("Access-Control-Allow-Origin");
        log.info(
            "CORS Response Headers: {}", 
            accessControlAllowOrigin
        );
        
        if (originOnRequest != null && accessControlAllowOrigin != null) {
            log.info(
                "CORS Are their Origin Same? : {}", 
                originOnRequest.equals(accessControlAllowOrigin)
            );
        }
        
        log.info("===== CORS 필터 logging 끝 =====");
        
    }
    
}

```

코드 1-2. CorsLoggingFilter.java

스프링 시큐리티 이용 시 위 필터를 다음과 같이 적용하면 된다.

```java
@Bean
public SecurityFilterChain securityFilterChain(
    HttpSecurity httpSecurity
) throws Exception {
        
    httpSecurity
        // 생략...
        .addFilterBefore(new CorsLoggingFilter(), CorsFilter.class)
        // 생략...
        return httpSecurity.build();
}
```

코드 1-3. CorsLoggingFilter를 적용하기 위한 코드.

해당 서버를 실행하고, 프론트엔드 측에서 “http://localhost:3000” 출처를 가지고 이 서버에 요청을 전송하면 서버 측 콘솔창에 요청 헤더인 Origin의 값과 응답 헤더인 Access-Control-Allow-Origin의 값이 출력될 것이다. 확인해보면 `Access-Control-Allow-Origin: http://localhost:3000` 이 출력되며, 요청 헤더인 Origin의 값과 동일하다고 나온다. 즉, 앞선 코드 1-1과 같이 설정하면 서버 측에서 자동으로 해당 헤더를 응답에 포함시켜 브라우저로 전송하게 되며, CORS 문제 없이 정상적으로 응답을 받아 프론트엔드 측에서 응답을 토대로 화면을 구성할 수 있게 된다. 

한 편, `<a>`, `<img>` , `<form>` 과 같은 HTML 태그들은 기본적으로 CORS가 허용되어 있어 다른 출처라도 데이터를 가져올 수 있다. 즉, 요청을 받은 서버 측에서는 굳이 Access-Control-Allow-Origin 헤더를 추가해주지 않아도 된다. 그 덕분에 HTML 기술만 알아도 간단하게 외부 사이트로부터 이미지, 링크 등의 데이터를 문제 없이 가져올 수 있는 것이다. 

반면 Axios, fetch 등 자바스크립트 기반 기술들은 기본적으로는 CORS가 비허용되어 있다고 한다. 아무래도 XSS, CSRF 등의 여러 보안 공격들과 더불어 자바스크립트로 쿠키나 로컬 스토리지 등에 접근 가능케 하면 보안적 문제가 따를 수 있다는 등의 의견들을 종합해 보면 자바스크립트로 인한 잠재적 보안 위험 때문에 이렇게 자바스크립트 기반 기술들에 대해서만 엄격한 것으로 보인다. 따라서 이 경우 CORS 문제를 회피하기 위해서는 서버 측에서 Access-Control-Allow-Origin 응답 헤더를 추가해줘야 한다. 

# TroubleShooting

## 배경 및 문제 상황

CORS에 대한 배경 지식에 대해 살펴보았으니 이제부터 본격적으로 저번 글에서 잠시 언급했던 구글 OAuth 로그인 연동 구현 시 발생했던 CORS 문제에 대해 설명하겠다. 처음에 필자는 다른 참고 자료들과 비슷하게 백엔드 코드를 작성하였다. 다만 필자가 참고했던 대부분의 자료들은 SSR 방식을 택했거나, 설령 CSR 방식을 택하더라도 같은 스프링 부트 프로젝트 내 src/main/resouce/static 또는 templates 폴더 내(SSR 방식의 경우)에 프론트엔드 코드를 작성했기에 백엔드와 프론트엔드가 하나의 프로젝트 폴더 안에 있는 구조였다. 이 경우에는 백엔드와 프론트엔드 모두 같은 출처를 가지기에 CORS 문제에서 자유롭다. 하지만 필자는 백엔드와 프론트엔드의 분리를 원했었다. 백엔드에서는 여전히 스프링 부트를 이용하여 http://localhost:8080 출처를 가지는 서버를 구축하고, 프론트엔드에서는 리액트를 이용하여 구현하되 스프링 부트 프로젝트 외부에 별도의 프로젝트 폴더 내에 작성한 다음 http://localhost:3000 출처를 가지도록 구성하고자 하였다. 그러다보니 필자가 작성한 코드와 다른 참고 자료에서의 백엔드 코드 구조도 조금씩 달랐다. 대부분의 참고 자료에서는 OAuth를 이용한 로그인에 성공하면 그 후처리를 담당할 OAuth2SuccessHandler에서 원하는 작업을 처리한 후, 맨 마지막에 특정 컨트롤러 메서드로 리다이렉트하는 코드를 작성하였었다. 필자도 이와 똑같이 작성했었다. 그리고 필자는 REST API로 작성하고 있었으므로 해당 컨트롤러 메서드에서는 로그인에 성공한 현재 사용자의 구글 계정 닉네임 및 이메일을 JSON으로 구성하여 프론트엔드 측에 전송하려고 했었다. 그 후 프론트엔드 측에서는 axios를 이용하여 이 응답 결과를 받아 사용자 화면에 현재 사용자의 구글 로그인 정보를 보여주려고 구성하였다.

그런데 여기서 문제가 생겼다. 브라우저에서는 애초에 구글 로그인 화면조차도 보여지지 않는 것이었다. 결과적으로 이는 구글에서 CORS를 지원하지 않기 때문에 벌어진 일이었다. 처음부터 이를 알지 못했던 필자는 다음과 같은 여러 방법들을 사용하여 해결하려고 하였다. 

## 해결 과정

1. 백엔드 측에서 CORS 관련 logging하여 원인 파악 시도 (실패) - 이 때 당시 브라우저의 콘솔창을 보니 CORS 문제 때문임을 알게 되었다. 하지만 이 당시만 하더라도 필자가 만든 백엔드 서버에서 CORS 설정을 잘못한 것으로 착각하고 있었다. 분명 시큐리티 설정 코드에 CORS 관련 코드도 포함시켰는데 CORS 문제였다는 게 이해가 가질 않았다. 브라우저 콘솔창에 띄워진 CORS 에러 메시지에는 응답 헤더에 Access-Control-Allow-Origin 자체가 없다고 나왔었고, 그래서 필자는 앞서 보인 CorsLoggingFilter를 추가하여 정말로 해당 응답 헤더가 추가되지 않았는지 확인했었다. 하지만 로깅 결과 응답 헤더가 잘 포함되었으며, 요청 헤더인 Origin과 http://localhost:3000으로 그 값까지 일치하였다. 이 두 값이 일치하면 분명 CORS 문제가 일어나지 않았어야 했다. 
그런데 logging하면서 알게 된 또 다른 사실은 정작 구글 권한 서버로의 리다이렉트 URL은 잘 설정되어 리다이렉트 시도를 정상적으로 하고 있다는 것이었다. 이는 처음에는 백엔드 서버에서 구글 서버로의 요청 자체가 잘못된 것인지도 의심했었는데, 영락없는 CORS 문제임을 확인시켜주는 대목이었다. 
그런데 사실 이 때까지만 해도 필자는 크게 착각하고 있었다. 사실 필자가 만든 백엔드 서버에서의 CORS 설정은 문제가 없었다. CORS 문제는 내 백엔드 서버가 아닌 구글 서버 때문이었다. 
2. 구글 클라우드 콘솔 쪽에서 “승인된 javascript 원본”에 프론트엔드 측 URI 추가 (실패) - 조금 더 조사해본 결과, CORS 문제는 필자가 만든 백엔드 서버가 아닌 구글 서버에 의한 문제였음을 파악하였다. 그리고 조사해보니 대부분 이 CORS 문제를 피하기 위해 구글 클라우드 콘솔 사이트에 등록한 OAuth 클라이언트 화면에서 “승인된 Javascript 원본”에 프론트엔드측 URI을 추가하여 해결하였다는 글들이 적지 않게 보였었다. 그래서 필자도 http://localhost:3000 도메인을 “승인된 Javascript 원본”에 추가하여 등록하였었다. 그런데 이상하게도 여전히 CORS 문제가 해결되지 않았었다. 여기서 필자는 머리가 매우 아파지기 시작했다. 분명 다른 이들은 이렇게 해결하였다는데 나만 안된다니! 이러면 이 문제를 어떻게 해결해야 하는건지 몰라 한동안 골치 아팠다.
3. 프론트엔드 측에서 axios 대신 `<a>` 태그로 백엔드 서버에 요청하기 (CORS 문제는 피했으나…) - 그러다 문득 다음의 블로그 글을 발견했다.
    
    [[로그인] 구글 로그인 CORS 에러 해결하기](https://velog.io/@bosun0214/%EB%A1%9C%EA%B7%B8%EC%9D%B8-302-redirect-%EC%97%90%EB%9F%AC-%ED%95%B4%EA%B2%B0%EA%B8%B0)
    
    이 분도 나와 비슷한 문제를 겪었었는데, 여기서 필자가 알게 된 사실은 첫 번째는 구글에서는 CORS 자체를 지원하지 않는다는 것이었고, 두 번째는 이 분은 axios가 아닌 form, href 등의 HTML 태그로 요청하도록 수정했더니 해당 문제를 피할 수 있었다고 한다. 그래서 필자도 axios가 아닌 a 태그로 대체한 후, 다시 한 번 구글 로그인 요청을 해봤더니 구글 로그인 페이지로 잘 리다이렉트 되었다. 구글 로그인까지도 순조로웠다. 그런데 여기서 다른 문제에 봉착하였다. 구글 로그인 성공 후 화면에는 JSON 형태의 데이터들이 고스란히 보이는 것이었다. 이 때 당시 백엔드 측에서는 OAuth2SuccessHandler에서 로그인 성공 응답을 위한 특정 컨트롤러 메서드로 리다이렉트하게 한 후, 해당 메서드에서 바로 JSON 형태의 응답 데이터를 전송하도록 했다. 그런데 프론트엔드 측에서는 axios를 지우고 a 태그로만 요청하도록 하였으니, 이 응답 데이터를 받을 수단이 없어 화면에 고스란히 출력된 것이었다. 그래서 여기서 필자는 또 한 번의 고비를 겪게 되었다. 
    참고로 이 때쯤 되어서야 애초에 구글 서버 측에서 Access-Control-Allow-Origin 응답 헤더 자체를 주지 않으니 브라우저에서 해당 헤더가 없다는 이유로 CORS 문제를 발생시킨 것을 깨닫게 되었다. 필자는 그 동안 필자가 만든 백엔드 서버에서 CORS 설정을 잘못한 줄 알았는데, 아무리 백엔드 서버에서 CORS 설정을 올바르게 해도 애초에 구글 서버에서 CORS를 미지원하기에 발생했던 문제임을 그 때서야 깨닫게 되었다. 
    
4. 백엔드 측에서 리다이렉트를 아예 프론트 측으로 설정한다 (성공, 그러나…) - 또 다른 문제에 봉착한 필자는 고민을 계속 하다가 문득 “백엔드 쪽에서 리다이렉트 하는 URI를 꼭 같은 서버로 해야 한다는 보장이 있나?” 라는 생각이 들어 리다이렉트 URI를 아예 “http://localhost:3000”으로 틀어보았다. 그리고 필자가 참고했던 자료들에서는 원래 이 URI 끝에 백엔드 서버 내에서 자체적으로 생성한 액세스 토큰을 쿼리 스트링 형식으로 첨부하여 리다이렉트하도록 되어 있었다. 그래서 필자도 똑같이 URI 끝에 액세스 토큰을 요청 파라미터로 추가하여 리다이렉트 하도록 하였다. 그랬더니 여전히 구글 로그인까지는 성공할 수 있었다. 이미 이 땐 CORS 문제는 피한 셈이다. 그런데 로그인 성공 이후 화면을 보니 로그인 전 화면이었던 “http://localhost:3000/” URI의 화면으로 돌아왔다. 여기까지는 예상한 과정이었다. 그런데 브라우저 URL을 보니 그 끝에 액세스 토큰이 버젓이 있는 것이었다. 사실 이는 당연한 게, 프론트엔드 측에서 이 브라우저 URL 창에 뜬 URL로부터 토큰을 추출한 뒤, 다시 원하는 페이지로 이동하도록 하는 코드를 추가하지 않았기에 당연한 결과였다. 하지만 이 생각까지 했음에도 필자는 괜히 신경이 쓰였다. 아무리 순식간에 그러한 과정이 처리되더라도 애초에 액세스 토큰이라는 중요한 정보가 사용자가 보는 브라우저 URL에 고스란히 보여지도록 하는 게 보안적으로 문제가 없을까, 란 생각이 들었었다. 사실 지금 생각해보면 애초에 OAuth 인증 과정에서 권한 서버와 클라이언트 앱 서버 간의 통신이 브라우저를 통해 리다이렉트 방식으로 진행되고, 이 과정에서 client id 등의 중요 정보를 요청 URL에 포함시킨다. 보안적으로 가장 안전하다는 Authorization Code Grant Type에서 이러한 방식을 취하는 것을 보면 리다이렉트 URL에 중요 정보를 요청 파라미터로 추가해도 보안적으로 문제가 없는 것 같다. 그럼에도 필자는 여전히 보안적으로 우려가 되어 조금 다른 방법을 택했다. 
5. 프론트엔드 측에서 로그인 성공 화면을 보여줄 URI 설정 후, 백엔드 측에서 이 URI로 리다이렉트 하도록 설정 (성공) - 결국 필자가 택한 방법은 다음과 같았다. 
    
    ![그림 1-1. 최종적으로 필자가 선택한 구조.](/images/2025-03-13/spring-security-OAuth2%EB%A1%9C%20%EC%99%B8%EB%B6%80%20%EC%82%AC%EC%9D%B4%ED%8A%B8%20%EA%B3%84%EC%A0%95%EC%9C%BC%EB%A1%9C%20%EC%86%90%EC%89%BD%EA%B2%8C%20%EC%9D%B8%EC%A6%9D%ED%95%98%EA%B8%B0%20(3)%20-%20%EA%B5%AC%EA%B8%80%EC%9D%98%20CORS%20%EB%AF%B8%EC%A7%80%EC%9B%90%20%EB%AC%B8%EC%A0%9C%20trouble%20shooting/1.png)
    
    그림 1-1. 최종적으로 필자가 선택한 구조.
    
- 프론트엔드 측에서는 구글 로그인 성공 시 리다이렉트될 URI를 설정한다. 필자의 경우 “/oauth2/login/success”로 설정했으며, 이에 해당하는 컴포넌트 MyPage.jsx를 사용하였다.
- 백엔드에서는 OAuth2SuccessHandler 내에서 액세스 및 리프레시 토큰 발급 후 이 두 토큰을 쿠키에 담는다. 그 후 “http://localhost:3000/oauth2/login/success” URL로 리다이렉트한다.
- 리다이렉트로 넘어온 프론트엔드 측의 MyPage.jsx에서는 이제 액세스 토큰이 담긴 쿠키가 있으므로, 현재 로그인에 성공한 사용자의 구글 계정 정보를 화면에 보이기 위해 백엔드에 요청을 한다. 여기서는 “/members/my”로 요청한다. 여기서부터는 구글 서버로 요청하는 일이 없기에 axios를 사용해도 CORS 문제를 겪지 않는다.
- 이 요청을 받은 백엔드에서는 요청 쿠키로 넘어온 액세스 토큰으로부터 사용자 닉네임을 추출한 후, 이를 통해 DB에 저장된 해당 사용자의 구글 계정 정보(닉네임, 이메일)를 조회하여 이를 응답한다.
- 프론트엔드 측에서는 응답 받은 사용자 정보를 토대로 화면을 구성하여 사용자 화면에 보인다.

로그인 실패의 경우에도 이와 비슷한 구조를 택하였다. 이렇게 하니 CORS 문제도 회피할 수 있었고, 사용자 정보를 보여주는 화면도 문제없이 보여줄 수 있게 되었다. 

# 고려해볼만한 대안

사실 필자가 택한 방식에서 조금 더 고려해볼만한 점이 있다. 원래 액세스 토큰을 요청할 때에는 Authorization 헤더에 토큰 정보를 추가하여 요청한다. 그런데 필자가 택한 방식에서는 해당 토큰을 애초에 쿠키로 응답하기도 하고, HttpOnly를 true로 하여 자바스크립트로 접근하는 것도 방지되어 있기에 이 상태로는 프론트엔드 측에서 액세스 토큰을 요청 헤더에 싣는게 불가능하다. 그래서 다음의 대안을 생각해볼 수 있겠다. 

1. 이 때만큼은 액세스 토큰이 담긴 쿠키에 대해 HttpOnly를 false로 하여 자바스크립트로 접근 가능하도록 한다. 
2. 1번에서 보안적인 문제가 우려될 경우, 백엔드에서 별도의 엔드포인트를 만든다. 이 엔드포인트로 요청을 하면 백엔드에서는 요청으로 들어온 쿠키로부터 액세스 토큰을 추출한 뒤 이를 응답 바디에 포함시켜 전송한다. 이 때 해당 쿠키는 삭제하도록 한다. 

이 방식이 다소 번거롭다면 앞서 언급했던 방식을 사용해도 될 것 같다. 즉, 백엔드 측에서는 리다이렉트 URI에 요청 파라미터로 액세스 토큰을 담아 리다이렉트하고 이를 프론트엔드 측에서 추출해나가는 방식이다. 단지 필자는 아주 잠시라도 브라우저 URL에 액세스 토큰 정보가 보인다는 것이 보안적으로 꺼려져서 이 방법을 사용하지 않은 것일 뿐, 기능 자체는 이 방식도 문제없이 작동한다. 

# 글을 마치며…

이로서 OAuth의 개념 및 작동 원리와 더불어 구글을 이용한 로그인 연동 기능 구현 및 이 과정에서 발생한 CORS 문제도 해결하여 결국 OAuth에 대해 모두 살펴보았다. 필자 개인적으로는 이전의 스프링 시큐리티만큼이나 어려웠던 개념이었다. 스프링 시큐리티를 공부할 때에도 시큐리티의 작동 원리 및 이를 이용하여 인증, 인가를 구현해보는 과정이 너무 어려웠고, 글로 정리할 때에도 헷갈리는 개념이 많아 다른 개념들을 공부할 때에는 짧게는 하루 안에, 길게는 3일 안에 블로그 글 작성까지 마치는 편인데 이 경우엔 1주일이나 넘게 걸렸었다. OAuth도 마찬가지로 개념도 어려웠으며, 특히 CSR 방식을 택해 백엔드와 프론트엔드로 분리했을 때의 코드를 참고할 자료가 생각보다 적어서 CORS 문제까지 해결하고 구글 연동 로그인 기능을 완벽히 구현할 때까지 오랜 시간이 걸렸다. OAuth도 총 1주일 넘게 걸린 것 같다. 다른 블로거 분들도 인증, 인가가 특히나 어렵고 지루하다고 말하는데, 확실히 직접 겪어보니 굉장히 어려웠다. 그래도 이렇게 공부를 했으니 실전에서는 인증, 인가를 더 쉽게 구현할 수 있지 않을까, 란 생각도 든다. 

---

References

[1] 프론트엔드 측의 CORS 에러 방지를 위한 구글 콘솔 설정 방법

[소셜로그인 2. 구글 콘솔에서 Oauth 앱 생성하기 - 승인된 리다이렉션 URI란](https://yelimkim98.tistory.com/45)

[2] 프론트엔드 측의 CORS 에러 발생 및 해결책

[#2. OAuth2 구현시 CORS 에러](https://dvdhan.tistory.com/193)

[3] 구글 로그인 구현 시 CORS 문제 발생 시 프론트 측에서의 해결책.

[[로그인] 구글 로그인 CORS 에러 해결하기](https://velog.io/@bosun0214/%EB%A1%9C%EA%B7%B8%EC%9D%B8-302-redirect-%EC%97%90%EB%9F%AC-%ED%95%B4%EA%B2%B0%EA%B8%B0)

[4] CORS

[🌐 악명 높은 CORS 개념 & 해결법 - 정리 끝판왕 👏](https://inpa.tistory.com/entry/WEB-%F0%9F%93%9A-CORS-%F0%9F%92%AF-%EC%A0%95%EB%A6%AC-%ED%95%B4%EA%B2%B0-%EB%B0%A9%EB%B2%95-%F0%9F%91%8F)