---
title: "[Spring Boot][OpenFeign] 외부 API 응답 데이터 가져와 DB에 저장하기"
category: "Spring"
tag: ["Spring", "Spring Boot", "Spring Data JPA", "Java", "OpenFeign", "Team Project", "Forklog"]
---

이 글은 Forklog라는 웹 사이트를 팀 프로젝트에서 제작하면서 알게 된 Spring Cloud Openfeign에 대해 다루는 글입니다. 여기서는 외부 API를 호출하여 얻어온 응답 데이터를 DB에 저장하는 과정을 중점적으로 다룰 것입니다. 이에 앞서 OpenFeign의 개념과 사용법을 다뤄본 후, 팀 프로젝트 과정에서 제가 이를 사용한 구체적인 과정을 서술하여 보겠습니다. 

# 개요

Forklog라는 음식점 웹 사이트를 제작하면서, 외부 API을 호출하여 얻어온 데이터를 DB에 저장해야하는 일이 있었다. 그 때까지는 외부 API를 프론트엔드 측에서 Fetch, Axios 등의 기술을 이용하여 호출하고, 그로부터 얻은 데이터를 화면에 출력하는 기술밖에 몰랐었다. 우리 팀에서는 Spring Boot를 사용하였고, 백엔드 쪽에서 외부 API를 호출하여 데이터를 얻어오는 방법이 있는가 조사하였다. Java라는 언어를 이용하여 외부와 HTTP 통신을 하여 소통을 하는 방법에는 여러 가지가 있는 것으로 파악되었지만, 모두 그 사용법이 매우 복잡하고 코드도 장황하다고 느꼈다. 그러다 마침 Spring Cloud OpenFeign이라는 기술을 접하게 되었는데, 이 기술은 다른 기술들에 비해 사용법이 매우 간단하여 이 기술을 채택하기로 하였다. 공식 문서에서도 선언형 REST Client (Declarative REST Client)라고 부르고 있다. 선언형은 어떤 작업을 할 것인지 프로그램에 알리기만 해도 프로그램이 자동으로 이를 수행하는 특성을 말한다. 이와 반대되는 키워드로 명령형(imperative)이 있는데, 이는 어떤 작업을 수행하기 위해 프로그램에게 그 절차를 명시하는 형태의 프로그래밍을 의미한다. 라면을 끓이고자 할 때 선언형은 그저 컴퓨터에게 “라면 끓여줘”라고만 선언해도 그 절차를 컴퓨터가 다 알아서 해주는 반면, 명령형은 라면 끓이는 절차를 하나하나 다 컴퓨터에게 알려줘야 하는 특징을 가진다. SQL은 SELECT, INSERT와 같은 키워드를 사용하기만 해도 그에 해당하는 작업을 프로그램에서 알아서 해주기에 대표적인 선언형 언어이다. 아무튼, Spring Cloud OpenFeign이 선언형이라는 것은 그만큼 사용자 입장에서는 HTTP 통신을 위한 사용이 훨씬 쉬워진다는 것을 의미한다. 

그리고 Spring Cloud OpenFeign이 사용하기 쉬운 또 다른 이유는 사용법이 Spring Data JPA와 거의 유사하기 때문이다. 어떻게 유사한지는 아래에서 예제 코드와 함께 후술할 부분을 살펴보면 알 수 있을 것이다. 

# OpenFeign 간단한 사용법

## 설정

OpenFeign은 Spring Cloud라는 의존성도 같이 포함해야 사용할 수 있다고 한다. 따라서 Spring Cloud 버전 및 스프링 부트 버전이 일치해야한다. 이 정보는 스프링 공식 문서인 다음의 문서에서 확인 가능

[Spring Cloud](https://spring.io/projects/spring-cloud)

![사진 1-1. Spring Boot와 OpenFeign 버전 호환표](/images/2025-01-17/Spring-OpenFeign-%EC%99%B8%EB%B6%80%20API%20%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B0%80%EC%A0%B8%EC%99%80%20DB%EC%97%90%20%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/1.png)

사진 1-1. Spring Boot와 OpenFeign 버전 호환표

만약 스프링부트 버전이 3.4.X라면 build.gradle에 다음과 같이 설정

```jsx
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.4.0'   // 스프링 부트 버전
	id 'io.spring.dependency-management' version '1.1.6'
}

group = 'com.example'
version = '0.0.1-SNAPSHOT'

java {
	toolchain {
		languageVersion = JavaLanguageVersion.of(21)
	}
}

configurations {
	compileOnly {
		extendsFrom annotationProcessor
	}
}

repositories {
	mavenCentral()
}

ext {
	set('springCloudVersion', "2024.0.0")  // 스프링 클라우드 버전.
}

dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-data-jpa'
	implementation 'org.springframework.boot:spring-boot-starter-web'
	implementation 'org.springframework.cloud:spring-cloud-starter-openfeign'  // OpenFeign 의존성
	compileOnly 'org.projectlombok:lombok'
	developmentOnly 'org.springframework.boot:spring-boot-devtools'
	annotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.springframework.boot:spring-boot-starter-test'
	testRuntimeOnly 'org.junit.platform:junit-platform-launcher'
	
	// Swagger
	implementation 'org.springdoc:springdoc-openapi-starter-webmvc-ui:2.7.0'
}

dependencyManagement {
	imports {
		mavenBom "org.springframework.cloud:spring-cloud-dependencies:${springCloudVersion}"
	}
}

tasks.named('test') {
	useJUnitPlatform()
}

```

코드 1-1. build.gradle

스프링부트에서는 다행히도 3.4.x 버전까지는 스프링 클라우드와 스프링 부트 버전을 자동으로 맞춰주고 있어 개발자가 따로 build.gradle을 조작하지 않아도 된다. 

혹시 몰라 application.properties에서 다음과 같이 설정함.

```java
spring.application.name=OpenFeignTest

# feign configuration
# 통신 요청 후 서버 연결 시간이 5초 경과 시 connection time-out 처리하도록 한다.
spring.cloud.openfeign.httpclient.connection-timeout=5000

# 응답 데이터 읽는 시간이 5초 초과하면 read time-out 처리하도록 함. 
spring.cloud.openfeign.httpclient.ok-http.read-timeout=5000
```

코드 1-2. application.properties

여기서는 외부 API로부터 데이터를 백엔드단에서 가져올 수 있는지 여부만 확인해볼 것이기에 DB 연동은 하지 않는다. 

그 후에 ~application.java 모듈에서 `@EnableFeignClients` 어노테이션을 적용한다.

```java
package com.jerocaller;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableFeignClients  // 요렇게!!
public class OpenFeignTestApplication {

	public static void main(String[] args) {
		SpringApplication.run(OpenFeignTestApplication.class, args);
	}

}

```

코드 1-3. OpenFeign 사용을 위한 설정.

아래 후술할 `@FeignClient` 어노테이션이 적용된 인터페이스를 사용할 때, 해당 인터페이스가 포함된 패키지만을 지정하여 그 패키지 내부에서만 OpenFeign 사용이 가능하도록 하고자 한다면 `@EnableFeignClients(basePackages = "com.jerocaller.feign")` 과 같이 `basePackages` 속성으로 해당 패키지 경로를 따로 명시해주면 된다. 

## 외부 API 호출하여 데이터 가져와보기

그 후 임의의 패키지를 만들고 그 안에 feign을 적용할 인터페이스 하나를 정의한다. 

![사진 1-2. OpenFeign 예제 패키지 구조.](/images/2025-01-17/Spring-OpenFeign-%EC%99%B8%EB%B6%80%20API%20%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B0%80%EC%A0%B8%EC%99%80%20DB%EC%97%90%20%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/2.png)

사진 1-2. OpenFeign 예제 패키지 구조.

```java
package com.jerocaller.feign;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient(name = "myFeign" , url = "https://jsonplaceholder.typicode.com")
public interface MyFeignInterface {
	
	@GetMapping("/posts/{id}")
	Object getOnePost(@PathVariable("id") Integer id);
	
	@GetMapping("/comments/{id}")
	Object getOneComment(@PathVariable("id") Integer id);
}

```

코드 2-1. OpenFeign을 이용하여 외부 API를 호출하는 방법

Feign 사용법은 Spring Data JPA에서 JPARepository를 이용하는 법과 매우 유사하다. 

`@FeignClient` 에 들어가는 속성

- name (또는 value) : 반드시 입력해야 한다고 한다. 이 이름은 “Spring Cloud LoadBalancer Client”라는 것을 생성할 때 사용되는 이름으로, 이름 자체는 사용자가 아무렇게나 줘도 되는 것으로 파악된다. 그런데 지금 단계에서는 Spring Cloud LoadBalancer라는 것과 전혀 관계 있는 것이 없어 여기서는 무시해도 되겠다. 어쨌거나 이름은 아무렇게라도 부여해야 한다. 그렇지 않고 생략하면 오류가 뜬다.
- url : 외부 API의 공통 URI. 여기서는 jsonplaceholder라는 API 제공 사이트를 이용하여 실험해볼 것이다.
    
    [JSONPlaceholder - Free Fake REST API](https://jsonplaceholder.typicode.com/)
    

만약 다음과 같이 `@FeignClient` 에 name 또는 value 속성을 부여하지 않으면…

```java
package com.jerocaller.feign;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;

@FeignClient(url = "https://jsonplaceholder.typicode.com")  // name, value 부여 안함
public interface MyFeignInterface {
	
	@GetMapping("/posts/{id}")
	Object getOnePost(@PathVariable("id") Integer id);
	
	@GetMapping("/comments/{id}")
	Object getOneComment(@PathVariable("id") Integer id);
}

```

코드 2-2

다음의 에러 메시지가 뜬다.

```java

java.lang.IllegalStateException: Either 'name' or 'value' must be provided in @FeignClient
	at org.springframework.cloud.openfeign.FeignClientsRegistrar.getClientName(FeignClientsRegistrar.java:461) ~[spring-cloud-openfeign-core-4.2.0.jar:4.2.0]
	at org.springframework.cloud.openfeign.FeignClientsRegistrar.registerFeignClients(FeignClientsRegistrar.java:201) ~[spring-cloud-openfeign-core-4.2.0.jar:4.2.0]
	at org.springframework.cloud.openfeign.FeignClientsRegistrar.registerBeanDefinitions(FeignClientsRegistrar.java:155) ~[spring-cloud-openfeign-core-4.2.0.jar:4.2.0]
	at org.springframework.context.annotation.ImportBeanDefinitionRegistrar.registerBeanDefinitions(ImportBeanDefinitionRegistrar.java:86) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.annotation.ConfigurationClassBeanDefinitionReader.lambda$loadBeanDefinitionsFromRegistrars$1(ConfigurationClassBeanDefinitionReader.java:387) ~[spring-context-6.2.0.jar:6.2.0]
	at java.base/java.util.LinkedHashMap.forEach(LinkedHashMap.java:986) ~[na:na]
	at org.springframework.context.annotation.ConfigurationClassBeanDefinitionReader.loadBeanDefinitionsFromRegistrars(ConfigurationClassBeanDefinitionReader.java:386) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.annotation.ConfigurationClassBeanDefinitionReader.loadBeanDefinitionsForConfigurationClass(ConfigurationClassBeanDefinitionReader.java:149) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.annotation.ConfigurationClassBeanDefinitionReader.loadBeanDefinitions(ConfigurationClassBeanDefinitionReader.java:121) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.annotation.ConfigurationClassPostProcessor.processConfigBeanDefinitions(ConfigurationClassPostProcessor.java:430) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.annotation.ConfigurationClassPostProcessor.postProcessBeanDefinitionRegistry(ConfigurationClassPostProcessor.java:290) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.support.PostProcessorRegistrationDelegate.invokeBeanDefinitionRegistryPostProcessors(PostProcessorRegistrationDelegate.java:349) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.support.PostProcessorRegistrationDelegate.invokeBeanFactoryPostProcessors(PostProcessorRegistrationDelegate.java:118) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.support.AbstractApplicationContext.invokeBeanFactoryPostProcessors(AbstractApplicationContext.java:791) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.context.support.AbstractApplicationContext.refresh(AbstractApplicationContext.java:609) ~[spring-context-6.2.0.jar:6.2.0]
	at org.springframework.boot.web.servlet.context.ServletWebServerApplicationContext.refresh(ServletWebServerApplicationContext.java:146) ~[spring-boot-3.4.0.jar:3.4.0]
	at org.springframework.boot.SpringApplication.refresh(SpringApplication.java:752) ~[spring-boot-3.4.0.jar:3.4.0]
	at org.springframework.boot.SpringApplication.refreshContext(SpringApplication.java:439) ~[spring-boot-3.4.0.jar:3.4.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:318) ~[spring-boot-3.4.0.jar:3.4.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1361) ~[spring-boot-3.4.0.jar:3.4.0]
	at org.springframework.boot.SpringApplication.run(SpringApplication.java:1350) ~[spring-boot-3.4.0.jar:3.4.0]
	at com.jerocaller.OpenFeignTestApplication.main(OpenFeignTestApplication.java:12) ~[main/:na]
	at java.base/jdk.internal.reflect.DirectMethodHandleAccessor.invoke(DirectMethodHandleAccessor.java:103) ~[na:na]
	at java.base/java.lang.reflect.Method.invoke(Method.java:580) ~[na:na]
	at org.springframework.boot.devtools.restart.RestartLauncher.run(RestartLauncher.java:50) ~[spring-boot-devtools-3.4.0.jar:3.4.0]
```

그러니 아무 이름이라도 부여해줘야 한다. 

한 편 MyFeignInterface 코드를 다시 보면, 그 내부에 호출하고자 하는 API 엔드포인트를 `@GetMapping` 등을 이용하여 사용한다. 여기서부터는 RestController에서의 사용법과 동일하다. 위 코드에서는 사용자의 고유 id 번호를 입력하면 그 사용자의 글 또는 댓글을 가져오고자 두 개의 메서드를 정의하였다. 

이게 끝이다. 이제 이를 호출할 service와, 이를 응답으로 내보내줄 controller를 만들기만 하면 끝이다.

```java
package com.jerocaller.service;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

import com.jerocaller.feign.MyFeignInterface;

import lombok.extern.slf4j.Slf4j;

@Service
@Slf4j
public class FeignService {
	
	@Autowired
	private MyFeignInterface myFeignInterface;
	
	public Object getOnePost(Integer id) {
		Object result = myFeignInterface.getOnePost(id);
		log.info("=== posts 로그 ===");
		log.info(result.toString());
		log.info("=== posts 로그 끝 ===");
		return result;
	}
	
	public Object getOneComment(Integer id) {
		Object result = myFeignInterface.getOneComment(id);
		log.info("=== comment 로그 ===");
		log.info(result.toString());
		log.info("=== comment 로그 끝 ===");
		return result;
	}
}

```

코드 2-3. com.jerocaller.service.FeignService

```java
package com.jerocaller.controller;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RestController;

import com.jerocaller.service.FeignService;

@RestController
public class FeignController {
	
	@Autowired
	private FeignService feignService;
	
	@GetMapping("/posts/{id}")
	public Object getOnePost(@PathVariable("id") Integer id) {
		return feignService.getOnePost(id);
	}
	
	@GetMapping("/comments/{id}")
	public Object getOneComment(@PathVariable("id") Integer id) {
		return feignService.getOneComment(id);
	}
}

```

코드 2-4. com.jerocaller.controller.FeignController

Swagger에서 테스트해보겠다. 

![사진 1-3.](/images/2025-01-17/Spring-OpenFeign-%EC%99%B8%EB%B6%80%20API%20%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B0%80%EC%A0%B8%EC%99%80%20DB%EC%97%90%20%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/3.png)

사진 1-3.

![사진 1-4. ](/images/2025-01-17/Spring-OpenFeign-%EC%99%B8%EB%B6%80%20API%20%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B0%80%EC%A0%B8%EC%99%80%20DB%EC%97%90%20%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/4.png)

사진 1-4. 

![사진 1-5.](/images/2025-01-17/Spring-OpenFeign-%EC%99%B8%EB%B6%80%20API%20%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B0%80%EC%A0%B8%EC%99%80%20DB%EC%97%90%20%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/5.png)

사진 1-5.

```java
[2m2024-12-19T11:34:30.915+09:00[0;39m [32m INFO[0;39m [35m19708[0;39m [2m--- [OpenFeignTest] [io-8080-exec-10] [0;39m[36mcom.jerocaller.service.FeignService     [0;39m [2m:[0;39m === posts 로그 ===
[2m2024-12-19T11:34:30.915+09:00[0;39m [32m INFO[0;39m [35m19708[0;39m [2m--- [OpenFeignTest] [io-8080-exec-10] [0;39m[36mcom.jerocaller.service.FeignService     [0;39m [2m:[0;39m {userId=1, id=1, title=sunt aut facere repellat provident occaecati excepturi optio reprehenderit, body=quia et suscipit
suscipit recusandae consequuntur expedita et cum
reprehenderit molestiae ut ut quas totam
nostrum rerum est autem sunt rem eveniet architecto}
[2m2024-12-19T11:34:30.915+09:00[0;39m [32m INFO[0;39m [35m19708[0;39m [2m--- [OpenFeignTest] [io-8080-exec-10] [0;39m[36mcom.jerocaller.service.FeignService     [0;39m [2m:[0;39m === posts 로그 끝 ===
[2m2024-12-19T11:35:06.244+09:00[0;39m [32m INFO[0;39m [35m19708[0;39m [2m--- [OpenFeignTest] [nio-8080-exec-1] [0;39m[36mcom.jerocaller.service.FeignService     [0;39m [2m:[0;39m === comment 로그 ===
[2m2024-12-19T11:35:06.245+09:00[0;39m [32m INFO[0;39m [35m19708[0;39m [2m--- [OpenFeignTest] [nio-8080-exec-1] [0;39m[36mcom.jerocaller.service.FeignService     [0;39m [2m:[0;39m {postId=1, id=1, name=id labore ex et quam laborum, email=Eliseo@gardner.biz, body=laudantium enim quasi est quidem magnam voluptate ipsam eos
tempora quo necessitatibus
dolor quam autem quasi
reiciendis et nam sapiente accusantium}
[2m2024-12-19T11:35:06.245+09:00[0;39m [32m INFO[0;39m [35m19708[0;39m [2m--- [OpenFeignTest] [nio-8080-exec-1] [0;39m[36mcom.jerocaller.service.FeignService     [0;39m [2m:[0;39m === comment 로그 끝 ===
```

이클립스 콘솔창 출력 결과

외부 API를 호출하여 가져온 JSON 데이터를 이제 백엔드에서 다룰 수 있게 되었다! 이를 이용하면 JPA와 함께 사용하여 외부에서 가져온 데이터를 DB에 저장할 수 있겠다.

# 실제 프로젝트 적용기

위에서 언급한 OpenFeign 간단한 사용법과 달리, 실제 프로젝트에 적용할 때에는 조금 더 많은 정보들을 알아야 했다. 다음은 실제로 팀 프로젝트에 OpenFeign을 적용하면서 추가로 배웠던 정보들을 기술한다. 

## Header에 인증 정보 추가하기

팀 프로젝트에서 Kakao API를 호출하여 얻은 데이터를 DB에 저장하고자 하였다. 그런데 Kakao API를 호출하려면 여기서 제공하는 API Key를 요청 헤더에 추가하여 전송해야 했다. 처음에 나는 `@FeignClient` 가 적용된 인터페이스 내부에서 `@RequestMapping` 류의 어노테이션들을 사용할 수 있으니 `@GetMapping(value = "...", headers = "key=value")` 형태로 header에 정보를 추가하려고 하였다. 그런데 왜인지 이 방법이 통하지 않았었다. 지금에 와서 추가적으로 조사해보면, 이것도 엄연히 header에 정보를 추가하는 방법 중 하나로 언급되는데, 내가 정보를 잘못 입력해서 요청 헤더에 들어가지 않았었던걸로 추측된다. 아무튼 그 때 당시에는 이 방법이 통하지 않아 당황했었는데, 팀장님의 도움으로 다음의 방법을 이용하면 요청 정보를 추가하는 것이 가능함을 알게되었다.

```java
package com.acorn.api.openfeign;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import feign.Logger;
import feign.Logger.Level;
import feign.RequestInterceptor;

@Configuration
public class KakaoOpenFeignConfig {
	
    @Value("${kakao.rest_api_key}")
    private String kakaoApiKey;

    @Bean
    public RequestInterceptor kakaoRequestInterceptor() {
        return requestTemplate -> {
            requestTemplate.header("Authorization", "KakaoAK " + kakaoApiKey);
        };
    }
}

```

코드 3-1. 

참고로 Kakao API 사용 시 요청 헤더에 추가해야만 했던 정보는 다음과 같다.

![사진 2-1. Kakao API 호출 시 반드시 넣어야 하는 요청 헤더 정보](/images/2025-01-17/Spring-OpenFeign-%EC%99%B8%EB%B6%80%20API%20%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B0%80%EC%A0%B8%EC%99%80%20DB%EC%97%90%20%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/6.png)

사진 2-1. Kakao API 호출 시 반드시 넣어야 하는 요청 헤더 정보

Feign에서 제공하는 RequestInterceptor 인터페이스를 사용하여 요청 헤더에 정보를 넣는 방식인 것이다. 참고로 RequestInterceptor 인터페이스의 코드는 다음과 같다.

```java
public interface RequestInterceptor {

  /** Called for every request. Add data using methods on the supplied {@link RequestTemplate}. */
  void apply(RequestTemplate template);
}
```

코드 3-2. RequestInterceptor.java

코드 3-1에서 RequestTemplate라는 클래스를 이용하여 람다 함수를 구성하고 이를 반환하는 구조를 띄는데, 이는 RequestInterceptor의 apply 메서드를 구현한 것으로 보인다. 

그리고 코드 3-1에서 작성한 Config 클래스를 Feign interface에 다음과 같이 적용하면 된다. 

```java
package com.acorn.api.openfeign;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.cloud.openfeign.SpringQueryMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

import com.acorn.dto.openfeign.kakao.blog.BlogRequestDto;
import com.acorn.dto.openfeign.kakao.blog.BlogResponseDto;
import com.acorn.dto.openfeign.kakao.geo.location.GeoLocationResponseDto;
import com.acorn.dto.openfeign.kakao.image.ImageRequestDto;
import com.acorn.dto.openfeign.kakao.image.ImageResponseDto;
import com.acorn.dto.openfeign.kakao.keyword.KeywordRequestDto;
import com.acorn.dto.openfeign.kakao.keyword.KeywordResponseDto;

/**
 * 카카오 API 호출하여 데이터를 받기 위한 OpenFeign 라이브러리 제공 인터페이스 사용. 
 */
@FeignClient(
		name="KakaoRestOpenFeign", 
		url = "https://dapi.kakao.com/v2", 
		configuration = KakaoOpenFeignConfig.class
)
public interface KakaoRestOpenFeign {
   // 생략...	
}

```

코드 3-3. 설정 클래스 적용.

즉, `@FeignClient` 어노테이션의 configuration 속성에 해당 설정 클래스를 대입하면 되는 것이다. 그러면 KakaoRestOpenFeign 인터페이스 내에 정의한 모든 메서드들은 Kakao API 요청 시 헤더에 인증 정보가 공통적으로 추가되어 실행되는 것이다. 

## Logging

한 편, 요청 정보가 잘 전달되고 있는지 또는 응답 정보가 제대로 오는지를 Logging으로 확인하고 싶었었다. 이 때 다음의 방법을 이용하였다. 먼저 application.properties에서 다음의 속성을 추가하였다.

```java
# feign log
logging.level.com.acorn.api.openfeign.KakaoRestOpenFeign=DEBUG
```

코드 4-1. application.properties

여기서 logging.level 이후에는 logging을 적용하고자 하는 패키지 경로를 입력하면 된다. 여기서는 KakaoRestOpenFeign 인터페이스에 대해서만 logging을 적용할 것이기에 해당 인터페이스명까지 명시하였다. 

그 후, 앞선 코드 3-1에서 봤던 설정 클래스 내부에 다음의 메서드를 추가하였다.

```java
package com.acorn.api.openfeign;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

import feign.Logger;
import feign.Logger.Level;
import feign.RequestInterceptor;

@Configuration
public class KakaoOpenFeignConfig {
	
    @Value("${kakao.rest_api_key}")
    private String kakaoApiKey;

    @Bean
    public RequestInterceptor kakaoRequestInterceptor() {
        return requestTemplate -> {
            requestTemplate.header("Authorization", "KakaoAK " + kakaoApiKey);
        };
    }
    
    /**
     * Feign Logging을 위한 설정.
     * 
     * REST API 키가 이클립스 콘솔창에 노출되니 정말 필요할 때만 사용!
     * @author JeroCaller
     */
    @Bean
    public Logger.Level feignLoggerLevel() {
    	return Level.FULL;
    }
    
}

```

코드 4-2. Logging 설정 메서드 추가.

위 코드에서는 Logger.Level 객체에 대해 FULL을 반환하도록 하였다. 반환 가능한 옵션으로 다음이 존재한다.

- NONE : (기본값) logging을 하지 않는다.
- BASIC : 요청 메서드, URL, 응답 status code, 실행 시간에 대해서만 logging한다.
- HEADERS : 요청, 응답 헤더에 관한 기본적인 정보들을 logging한다.
- FULL : 요청, 응답의 헤더, 바디, 메타데이터를 logging한다.

다음은 Feign 호출 시 콘솔창에서 볼 수 있는 log 내역이다.

![사진 3-1. 이클립스 콘솔창에 출력된 요청, 응답 정보. 앞서 요청 header에 추가한 authorization 정보도 출력되는 것을 볼 수 있다. ](/images/2025-01-17/Spring-OpenFeign-%EC%99%B8%EB%B6%80%20API%20%EC%9D%91%EB%8B%B5%20%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EA%B0%80%EC%A0%B8%EC%99%80%20DB%EC%97%90%20%EC%A0%80%EC%9E%A5%ED%95%98%EA%B8%B0/7.png)

사진 3-1. 이클립스 콘솔창에 출력된 요청, 응답 정보. 앞서 요청 header에 추가한 authorization 정보도 출력되는 것을 볼 수 있다. 

위와 같이 요청, 응답 정보를 logging하여 볼 수 있다. 다만 주의할 점은 API Key와 같은 민감한 정보도 출력되기에 이러한 log가 외부에 유출되지 않도록 주의해야 한다는 것이다. 

## DTO를 이용하여 요청 정보 전달 및 응답 데이터 받기

Kakao API 공식 문서를 보니 요청 파라미터로 전달할 정보가 꽤 많았다. 따라서 이 파라미터 하나하나에 일일이 `@RequestParam` 어노테이션을 부여하는 방식은 부적절하다고 느꼈다. 이렇게 되면 Feign interface의 메서드 파라미터로 너무 많은 파라미터를 정의하게 되기에 부적절하다고 생각했다. 그래서 대신 DTO 클래스를 하나 만들어 이를 대신 사용하는 것을 고려하였다. 

```java
package com.acorn.api.openfeign;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.cloud.openfeign.SpringQueryMap;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;

import com.acorn.dto.openfeign.kakao.blog.BlogRequestDto;
import com.acorn.dto.openfeign.kakao.blog.BlogResponseDto;
import com.acorn.dto.openfeign.kakao.geo.location.GeoLocationResponseDto;
import com.acorn.dto.openfeign.kakao.image.ImageRequestDto;
import com.acorn.dto.openfeign.kakao.image.ImageResponseDto;
import com.acorn.dto.openfeign.kakao.keyword.KeywordRequestDto;
import com.acorn.dto.openfeign.kakao.keyword.KeywordResponseDto;

/**
 * 카카오 API 호출하여 데이터를 받기 위한 OpenFeign 라이브러리 제공 인터페이스 사용. 
 */
@FeignClient(
		name="KakaoRestOpenFeign", 
		url = "https://dapi.kakao.com/v2", 
		configuration = KakaoOpenFeignConfig.class
)
public interface KakaoRestOpenFeign {
	
	/**
	 * 카카오 API - "키워드 장소 검색" 요청 메서드.
	 * 지역명과 같은 키워드 입력 시 그에 해당하는 음식점 정보들을 얻어올 수 있다. 
	 * 
	 * 참고)
	 * Get 요청에서 요청 파라미터를 DTO로 넘기고자 할 경우 
	 * 해당 인자 앞에 `@SpringQueryMap`을 붙여야 요청 정보가 제대로 전달됨. 
	 * 이를 작성하지 않는 경우 요청 정보가 전달되지 않아 검색 결과가 없는 것으로 조회되는 버그 발생.
	 * 
	 * 참고 사이트)
	 * https://developers.kakao.com/tool/rest-api/open/get/v2-local-search-keyword.%7Bformat%7D
	 * 
	 * @author JeroCaller
	 * @return
	 */
	@GetMapping(value = "/local/search/keyword.json")
	KeywordResponseDto getEateriesByKeyword(@SpringQueryMap KeywordRequestDto requestDto);
	
	// 생략
	
}

```

코드 5-1. KakaoRestOpenFeign.java

```java
package com.acorn.dto.openfeign.kakao.keyword;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;

/**
 * 카카오 API - "키워드로 장소 검색하기" API 요청 DTO
 * 
 * @author JeroCaller (JJH)
 */
@Getter
@NoArgsConstructor
@AllArgsConstructor
@Builder
//@JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class)
public class KeywordRequestDto {
	
	private String query;
	
	@Builder.Default
	/*@JsonProperty("category_group_code")
	private String categoryGroupCode = "FD6";
	*/
	// JsonProperty, JsonNaming이 먹히지 않아 부득이하게 스네이크 케이스로 작성함...
	private String category_group_code = "FD6";
	
	private String x;
	private String y;
	private String radius;
	private String rect;
	private Integer page;
	private Integer size;
	private String sort;
	
}

```

코드 5-2. KeywordRequestDto.java

코드 5-1의 `getEateriesByKeyword` 메서드를 보면 파라미터에 `KeywordRequestDto`  라는 POJO를 사용하고 있음을 볼 수 있다. 그리고 그 앞에 `@SpringQueryMap` 어노테이션을 적용하였다. 이 어노테이션을 적용해야 사용자 정의 POJO 또는 Map 자료구조가 요청 파라미터임을 스프링에서 인식할 수 있게 된다. 만약 이 어노테이션을 적용하지 않으면 요청은 잘 전송되나 응답 데이터를 하나도 받지 못할 것이다. 

한 편, 응답 데이터에도 프로퍼티가 너무 많아 이를 별도의 DTO를 만들어 응답 데이터를 받도록 고안하였다. 다음은 코드 5-1에서 보인 `KeywordResponseDto` 라는 클래스 내부에 사용된 `KeywordDocumentDto` 라는 클래스 코드이다.

```java
package com.acorn.dto.openfeign.kakao.keyword;

import com.fasterxml.jackson.databind.PropertyNamingStrategies;
import com.fasterxml.jackson.databind.annotation.JsonNaming;

import lombok.Getter;
import lombok.NoArgsConstructor;
import lombok.ToString;

/**
 * 카카오 API - "키워드로 장소 검색하기" 응답 데이터의 "document" 
 * 부분을 담당하는 DTO
 * 
 * 참고 자료)
 * https://developers.kakao.com/docs/latest/ko/local/dev-guide#search-by-keyword-response-body-document
 * 
 * @author JeroCaller
 */
@Getter
@NoArgsConstructor
@ToString
@JsonNaming(PropertyNamingStrategies.SnakeCaseStrategy.class)
public class KeywordDocumentDto {
	
	private String id;
	private String placeName;
	private String categoryName;
	private String categoryGroupCode;
	private String categoryGroupName;
	private String phone;
	private String addressName;
	private String roadAddressName;
	private String x;
	private String y;
	private String placeUrl;
	private String distance;
	
}

```

코드 5-3. KeywordDocumentDto.java

그런데 Kakao API 공식 문서를 보면 응답 데이터의 프로퍼티 키가 다음과 같이 Snake Case로 작성되어 있었다. 

```java
"documents": [
    {
      "address_name": "서울 강남구 대치동 996-16",
      "category_group_code": "FD6",
      "category_group_name": "음식점",
      "category_name": "음식점 > 한식 > 해장국",
      "distance": "",
      "id": "27531028",
      "phone": "02-558-7905",
      "place_name": "중앙해장",
      "place_url": "http://place.map.kakao.com/27531028",
      "road_address_name": "서울 강남구 영동대로86길 17",
      "x": "127.065472540919",
      "y": "37.508273597184"
    },
  ...
```

코드 5-4. Kakao API - “키워드로 장소 검색하기” 응답 예시

자바 내에서 이 응답 데이터를 받을 DTO 클래스의 필드명은 모두 Camel Case로 작성되어있다. 이 표기법이 불일치하면 응답 데이터를 제대로 받지 못해 null과 같은 엉뚱한 데이터를 받을 수 있다. 따라서 이 표기법을 일치시켜야하는데, 다행히도 `@JsonNaming` 이나 `@JsonProperty` 어노테이션을 이용하면 된다. `@JsonProperty` 어노테이션은 특정 필드에 적용하는 어노테이션으로 다음과 같이 사용할 수 있다.

```java
import com.fasterxml.jackson.annotation.JsonProperty;
// 생략

@JsonProperty("category_group_code")
private String categoryGroupCode = "FD6";
```

코드 5-5. `@JsonProperty` 사용 예시

위 예시는 Snake Case로 작성된 프로퍼티를 Camel Case로 변형하여 그 데이터를 받겠다는 뜻이다. 

그런데 적용할 필드의 개수가 너무 많을 경우 일일이 필드에다가 `@JsonProperty` 어노테이션을 적용하는 것은 너무 수고롭다. 그래서 클래스 레벨에서 모든 필드에 적용할 수 있는 어노테이션이 바로 `@JsonNaming` 어노테이션이다. 코드 5-3을 보면 그 사용법을 알 수 있다. Kakao API로부터 응답 받는 Json 데이터의 프로퍼티들이 모두 Snake Case로 작성되어 있기에 `@JsonNaming` 어노테이션에 `PropertyNamingStrategies.SnakeCaseStrategy.class` 를 적용한 것이다. 이러한 어노테이션을 사용하면 서로 다른 표기법을 자동으로 인식하게끔 하여 안전하게 데이터를 받을 수 있다.

> 코드 5-1을 자세히 보면 알겠지만 `private String categoryGroupCode = "FD6";` 라고 Camel 표기법으로 작성한 후 `@JsonNaming`, `@JsonProperty` 모두 적용해봐도 실제로 log를 통해 확인해보면 Snake Case로 변환되지 않았다. 그래서 코드 5-1에서는 해당 필드에 대해서만 어쩔 수 없이 Snake Case로 작성하였다. `@SpringQueryMap` 을 만나면서 `@JsonNaming` 이 적용되지 않는 것으로 추측되지만 정확한 이유는 지금도 잘 모르겠다.
> 

## 외부 API로 가져온 데이터를 DB에 저장 With Spring Data JPA

OpenFeign을 이용하여 외부 API로부터 데이터를 가져왔으니 이를 DB에 저장하는 건 쉽다. 팀 프로젝트에서는 DB 관련 작업을 Spring Data JPA를 이용하여 진행하였기에 JpaRepository의 `save()`를 이용하여 DB에 데이터를 저장할 수 있었다. 실제로는 연동 과정의 코드가 복잡해서 일부만 소개하겠다.

```java
/**
	 * 조회된 API를 DB에 저장.
	 * 
	 * @author JeroCaller
	 * @param response
	 * @return 
	 * @throws DuplicatedInDBException - API로부터 가져온 데이터가 DB에 이미 있을 경우 발생하는 커스텀 예외.
	 */
	@Transactional
	private int saveApi(List<KeywordDocumentDto> response) throws DuplicatedInDBException {
		List<Eateries> eateries = new ArrayList<Eateries>();

		for (KeywordDocumentDto document : response) {
			// 확인 결과 kaka API에서 특정 페이지 이상 넘기면 아무리 페이지 번호를 증가시켜도
			// 똑같은 데이터가 반복적으로 응답되는 현상을 확인함.
			// 따라서 중복 데이터 삽입을 방지.
			Optional<Eateries> duplicated = eateriesRepository
					.findByNameAndAddress(
							document.getPlaceName(), 
							document.getRoadAddressName()
					);	
			if (duplicated.isPresent()) {
				// 응답 데이터 전송을 위해 컨트롤러에 넘길 데이터를 커스텀 예외 객체에 저장.
				DuplicatedInDBException customException = new DuplicatedInDBException();
				customException.setDuplicated(duplicated.get());
				customException.setSavedNum(eateries.size());
				throw customException;
			}
			
			// 맨 첫 요소는 불필요한 정보.
			// API에서 제공하는 카테고리 정보의 길이가 동적이고, 어디까지 상세 정보를 제공하는 지 몰라
			// " > "을 구분자로 구분한 파편 정보들을 별도의 DTO가 아닌 String[]에 담도록 함.
			String[] categoryTokens = document.getCategoryName().split(" > ");

			Categories categoryEntity = null;

			// 소분류 항목 있을 경우.
			if (categoryTokens.length > 2) {
				categoryEntity = saveAndReturnCategory(categoryTokens[1], categoryTokens[2]);
			} else if (categoryTokens.length > 1) {
				categoryEntity = saveAndReturnCategory(categoryTokens[1], null);
			}

			//log.info("road address name from kakao api");
			//log.info(document.getRoadAddressName());
			
			// 검색 결과 정확성을 위해 주소 중분류까지 첨부하여 검색 
			// (중분류는 공백 기준 분할 후 나오는 두 번째 문자열 토큰까지만을 고려.)
			// ( 예) 경기 용인시 처인구 -> 경기 용인시 (원래는 용인시 처인구까지가 중분류임) )
			// placeName() : 음식점명
			String[] addressTokens = null;
			if (!document.getRoadAddressName().trim().isEmpty()) {
				addressTokens = document.getRoadAddressName().split(" ");
			} 
			String address = "";
			
			// API로부터 조회된 음식점의 도로명 정보가 아예 없는 경우도 있음.
			if (addressTokens != null) {
				address = addressTokens[0] + " " + addressTokens[1];
			}

			String apiQuery = document.getPlaceName() + " " + address;
			log.info("apiQuery : " + apiQuery);
			ImageDocumentDto imageDocumentDto = imageSearchProcess.getOneImage(apiQuery);
			BlogDocumentsDto blogDocumentsDto = blogSearchProcess.getOneBlog(apiQuery);
			
			String imageResult = "";
			String blogResult = "";
			
			log.info("image result");
			if (imageDocumentDto != null && imageDocumentDto.getImageUrl() != null) {
				imageResult = imageDocumentDto.getImageUrl();
				log.info(imageDocumentDto.toString());
			} else {
				log.info("image NOT FOUND");
			}
			
			log.info("blog result");
			if (blogDocumentsDto != null && blogDocumentsDto.getContents() != null) {
				blogResult = blogDocumentsDto.getContents();
				log.info(blogDocumentsDto.toString());
			} else {
				log.info("blog NOT FOUND");
			}
			
			Eateries newEateries = Eateries.builder()
					.name(document.getPlaceName())
					.longitude(new BigDecimal(document.getX()))
					.latitude(new BigDecimal(document.getY()))
					.address(document.getRoadAddressName())
					.category(categoryEntity)
					.tel(document.getPhone())
					.thumbnail(imageResult)
					.description(blogResult)
					.build();
			eateriesRepository.save(newEateries);
			eateries.add(newEateries);
		}
		
		return eateries.size();

	}

	/**
	 * API로부터 들어온 음식점 카테고리 정보를 DB로부터 조회하여 외래키 매핑 또는 DB 내에 해당 정보 없을 시 
	 * 해당 엔티티 새 생성 및 반환.
	 * 
	 * saveApi 메서드에서 사용됨.
	 * 
	 * @author JeroCaller
	 * @param largeCate - 음식 대분류 카테고리명
	 * @param smallCate - 음식 소분류 카테고리명
	 * @return
	 */
	@Transactional
	private Categories saveAndReturnCategory(String largeCate, String smallCate) {
		Categories categoryEntity = null;
		
		// 음식점 카테고리 소분류가 입력되지 않아도 대분류만으로도 조회할 수 있도록 구성.
		if (smallCate == null || smallCate.trim().equals("")) {
			if (largeCate != null && !largeCate.trim().equals("")) {
				smallCate = "기타";
			} else {
				return null;
			}
		}

		// 음식 카테고리 대분류가 DB에 있는지 먼저 확인.
		CategoryGroups categoryGroupEntity = categoryGroupsRepository.findByName(largeCate);

		if (categoryGroupEntity == null) {
			// API로부터 새로 들어온 음식 대분류 카테고리 정보를 DB에 저장.
			categoryGroupEntity = categoryGroupsRepository.save(
					CategoryGroups.builder()
					.name(largeCate)
					.build()
			);
		}

		categoryEntity = categoriesRepository.findByNames(largeCate, smallCate);
		if (categoryEntity == null) {
			// 새 카테고리 DB에 삽입
			categoryEntity = categoriesRepository.save(
					Categories.builder()
					.name(smallCate)
					.group(categoryGroupEntity)
					.build()
			);
		}
		//log.info("category Entity");
		//log.info(categoryEntity.toString());

		return categoryEntity;
	}
```

코드 6-1. 실제 외부 API로부터 가져온 데이터를 DB에 저장하는 코드. Spring Data JPA를 이용하였다. 

여기서 외부 API로부터 가져온 데이터를 DB에 저장하는 코드는 `saveAndReturnCategory` 메서드 부분과 다음의 코드이다.

```java
Eateries newEateries = Eateries.builder()
					.name(document.getPlaceName())
					.longitude(new BigDecimal(document.getX()))
					.latitude(new BigDecimal(document.getY()))
					.address(document.getRoadAddressName())
					.category(categoryEntity)
					.tel(document.getPhone())
					.thumbnail(imageResult)
					.description(blogResult)
					.build();
	eateriesRepository.save(newEateries);
```

코드 6-2. 코드 6-1에서 일부 발췌

외부 API로 가져온 응답 데이터를 그대로 DB에 넣는 게 아니라, Eateries라는 음식점 정보를 가지고 있는 Entity 형태로 데이터를 넣어야 했기에 위 코드 6-2처럼 Eateries Entity를 생성한 후, 외부 API로 가져온 데이터가 담겨져 있는 document로부터 일부 데이터를 가져와 각각의 필드들의 값을 설정하였다. 그 후 해당 Eateries Entity를 `save()` 메서드의 인자로 넣어 영속화하도록 하고 있다. 

음식점의 카테고리 정보를 표현하는 Categories, CategoryGroups Entity도 외부 데이터로부터 가져온 음식점 카테고리 대분류 및 소분류 문자열을 가져와 이를 DB에 저장하는 과정을 거치고 있다. 

이렇게 Spring Data JPA와 같이 이용하면 JpaRepository의 save() 메서드만을 이용하여 외부 API에서 가져온 데이터를 DB에 저장할 수 있게 된다. 

한 편 실제 팀 프로젝트에서, 백엔드 단에서 외부 API를 호출하여 얻은 데이터를 DB에 저장하는 설계에 관한 자세한 설명은 [[Forklog] 팀프로젝트 시작 - 되돌아보며](https://jerocaller.github.io/team%20project/team-project-forklog-%ED%8C%80%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8-%EC%8B%9C%EC%9E%91-%EB%90%98%EB%8F%8C%EC%95%84%EB%B3%B4%EB%A9%B0/#%EB%B0%B1%EC%97%94%EB%93%9C%EB%A5%BC-%EA%B2%BD%ED%97%98%ED%95%98%EB%A9%B0) 글을 참고.

---

References

[1] [Spring Cloud OpenFeign Features :: Spring Cloud Openfeign](https://docs.spring.io/spring-cloud-openfeign/reference/spring-cloud-openfeign.html)

[2] [Spring Cloud](https://spring.io/projects/spring-cloud)

[3] [[Spring] OpenFeign이란? OpenFeign 소개 및 사용법](https://mangkyu.tistory.com/278)

[4] [FeignClient 사용법에 대해 알아봅시다. (Spring Cloud OpenFeign)](https://velog.io/@choidongkuen/FeignClient-%EC%82%AC%EC%9A%A9%EB%B2%95%EC%97%90-%EB%8C%80%ED%95%B4-%EC%95%8C%EC%95%84%EB%B4%85%EC%8B%9C%EB%8B%A4.-Spring-Cloud-OpenFeign)

[5] [[Spring Cloud] Spring Cloud OpenFeign - 기본 개념 및 활용](https://velog.io/@mrcocoball2/Spring-Cloud-Spring-Cloud-OpenFeign-%EA%B8%B0%EB%B3%B8-%EA%B0%9C%EB%85%90-%EB%B0%8F-%ED%99%9C%EC%9A%A9)

[6] [Spring Cloud OpenFeign 예제로 배우기 - 타깃코더스](https://targetcoders.com/openfeign-%EC%98%88%EC%A0%9C/)

[7] [Feign Client 로 HTTP 통신 하기](https://velog.io/@shdrnrhd113/Feign-Client-%EB%A1%9C-HTTP-%ED%86%B5%EC%8B%A0-%ED%95%98%EA%B8%B0)

[8] 선언형 VS 명령형 (내 블로그)

[[Python][RDBMS] 관계형 데이터베이스와 파이썬 개요](https://jerocaller.github.io/python/RDBMS-python/)

[9] Java, Spring에서 외부 API 호출하는 방법들 비교

[[Spring] 스프링에서 외부 API 호출하는 방법들](https://jie0025.tistory.com/531)

[10] OpenFeign 사용법. Header에 정보를 넣는 법도 소개되어 있음. 

[우아한 feign 적용기 \| 우아한형제들 기술블로그](https://techblog.woowahan.com/2630/)

[11] OpenFeign에서 Header에 값 추가하는 다른 방법

[openfeign에서 header에 값 추가하는 방법](https://rudaks.tistory.com/entry/openfeign%EC%97%90%EC%84%9C-header%EC%97%90-%EA%B0%92-%EC%B6%94%EA%B0%80%ED%95%98%EB%8A%94-%EB%B0%A9%EB%B2%95)

[12] OpenFeign에서 request interceptor 사용법

[How to add a request interceptor to a feign client?](https://stackoverflow.com/questions/40262132/how-to-add-a-request-interceptor-to-a-feign-client)

[13] [Introduction to Spring Cloud OpenFeign \| Baeldung](https://www.baeldung.com/spring-cloud-openfeign)