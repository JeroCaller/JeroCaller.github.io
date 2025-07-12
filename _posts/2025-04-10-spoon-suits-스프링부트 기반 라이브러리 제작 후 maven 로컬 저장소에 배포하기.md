---
title: "[Spoon Suits] 스프링부트 기반 라이브러리 제작 후 로컬 maven 저장소에 배포하기"
category: "Personal Project"
tag: ["Personal Project", "Library", "Spring Boot", "Spoon Suits", "Maven", "Maven Local Repository", "Gradle"]
---

<div style="text-align:center; margin:1em;">
  <img src="https://raw.githubusercontent.com/JeroCaller/Spoon-Suits/main/docs-resources/spoon-suits-icon.png" alt="Spoon Suits logo" width="50%">
  <p style="margin-top: 3px">(라이브러리의 아이콘을 직접 만들어보았다.)</p>
</div>

그동안 자바와 스프링에 대해 공부해오고 이에 대한 예제를 직접 만들어보거나 프로젝트를 만들어보니 한 가지 느낀 것이 있었다. 의외로 여러 프로젝트에서 공통적으로 사용되는 기능들, 매번 “아, 이 기능은 필요하네” 라고 느끼는 기능들이 여럿 있었다는 것이다. 그럴 때마다 매번 기존에 작성했던 코드를 복붙하여 사용해왔다. 그런데 이런 작업도 의외로 번거롭고 불편했다. `.java` 확장자를 가지는 파일 내에는 맨 위에 해당 모듈이 속한 패키지 주소를 적어야 한다. 그래서 복붙을 하여 다른 프로젝트에 가져다 사용하면 그 패키지 주소가 현재 속한 프로젝트의 것과 일치할 리가 없다. 이런 차이점의 해소는 IDE의 자동 기능으로 어찌한다 하더라도, 애초에 필요한 기능들이 여러 프로젝트에 파편화되어 존재하였기에 내가 원하는 기능이 어느 프로젝트에 있었는지 일일이 찾아봐야 한다는 것이 가장 큰 불편함이었다. 그래서 gradle 빌드 도구를 이용하는 내게 이러한 생각이 어느 순간부터 강하게 들었다. 

“lombok과 같은 라이브러리들도 build.gradle 파일에 한두줄만 그 의존성을 명시하면 lombok 내 모든 기능들을 바로 사용할 수 있는데, 나도 내가 직접 라이브러리를 만들어 그렇게 할 순 없을까?”

즉 필자는 여태동안 다음의 한 줄을 꿈꿔왔던 것이다.

```
// build.gradle
implementation: 'com.jerocaller.libs:my-library:1.0.1'
```

이 한 줄만으로 내가 만든 기능들을 모아놓은 하나의 라이브러리를 모두, 그리고 아주 빠르게 사용할 수 있게 되는 것. 그래서 더 이상 이곳저곳 흩어져 있는 기능들을 찾지 않아도 되며, 심지어는 매 프로젝트마다 비슷한 코드를 다시 구현해야한다는 번거로움과 시간적 낭비를 최소화하는 것이 필자의 소박해보이지만 강렬히 원하던 꿈이었다. 실제로 매번 사용되는 기능들을 모아 라이브러리화하고, 이를 배포하여 쉽게 사용하도록 하는 것은 “코드의 재사용성” 측면에서 중요한 것이라 생각한다. “코드의 재사용성”이란 그 말 한마디에는 사실 많은 걸 함축하고 있다. 더 이상 같은 기능을 사용하기 위해 일일히 같은 코드를 작성하지 않아도 된다는 점. 코드 복붙보다도 더 빠르게 사용할 수 있어 시간 비용을 최소화할 수 있다는 점. 테스트 코드의 작성 등으로 검증만 되어 있다면 그 기능의 안정성을 고려하지 않아도 편하게 사용할 수 있다는 점까지. 누군가는 이렇게까지 말한다. “코드는 자산이 아니라 부채이다.” 코드의 양이 많아질수록 내가 이해해야하고 관리해야하는 코드도 많아진다는 것을 뜻하기 때문이다. 그러한 의미에서 검증되고 문서화가 잘된 라이브러리를 제작하여 이를 가져다 쓴다면 프로젝트 내에서 작성해야할, 그리고 관리해야할 코드의 양도 줄어들어 부담도 덜할 것이다. pom.xml 형태의 maven을 사용하던, gradle을 사용하건, 아니면 jar 파일을 직접 프로젝트에 추가하건 간에 외부 라이브러리를 사용해본 사람이라면 다 알겠지만, 외부 라이브러리에 존재하는 기능들이 코드로 어떻게 구현되어 있는지, 그리고 심지어는 그 동작 원리를 모르더라도 사용법만 알면 바로 사용 가능하다는 것을 알 것이다. REST API에서도 사용자는 그 내부의 작동 원리를 모르더라도 개발자가 작성한 문서대로 그 사용법만 지키면 서버에서 제공하는 resource를 바로 사용할 수 있는 것도 이와 비슷한 관점이라 할 수 있겠다. 즉, 라이브러리를 제작하고 이를 다른 프로젝트에서 가져다 쓴다는 것은 그 라이브러리 자체가 “은닉화”의 특성을 가지기에 편리하게 사용할 수 있다는 것이다. 이는 OOP의 핵심 원리 중 하나인 “캡슐화”, “정보 은닉화”와도 부합한다. 

이러한 이유들로 인해, 또는 앞으로의 프로젝트에서도 쉽게 사용하기 위해서라도 라이브러리 제작의 중요성을 간절히 느끼고 있었기에 결국 스프링부트 기반 라이브러리를 제작하게 되었다. 심지어는 웹 앱을 만드는 개인 프로젝트보다도 더 먼저 제작하게 되었다. 

조사해보니, 라이브러리를 배포하는 방법은 크게 두 가지가 있었다. 하나는 로컬에 배포하여 특정 기기 하나에서만 사용할 수 있는 방법, 또 하나는 온라인에 배포하여 어떤 기기에서든지 사용할 수 있는 방법 이렇게 있었다. 외부에 노출되어선 안되는 코드들이 있다거나 나 혼자만 사용해야하는 이유가 있다면 로컬 배포 방법만 고려하면 되겠지만, 필자는 그럴 이유가 없기에 로컬 배포 방법과 온라인 배포 방법 모두를 다뤄볼 것이다. 이 글에서는 첫 번째로 로컬로 배포하는 방법에 대해 살펴볼 것이다. 그 후 다른 글에서 온라인으로 배포하는 방법에 대해 다뤄볼 것이다. 

# 스프링부트로 제작한 라이브러리 사양 및 관련 정보

- github repository: [https://github.com/JeroCaller/Spoon-Suits](https://github.com/JeroCaller/Spoon-Suits)
- Libraray name : Spoon Suits
- IDE: Intellij
- Java version: JDK 21
- build tool : Gradle
- Issue tracker : Github Issues
- Project management: Github Projects
- Dependencies in Gradle(Groovy)
    - Spring boot starter
    - Spring boot starter web
    - Spring security
    - Spring data jpa
    - Spring validation
    - Lombok
    - jjwt
- Test
    - Spring boot starter test
    - junit
    - lombok
    - selenium

# 스프링부트로 라이브러리 제작하기

스프링부트 환경에서 라이브러리를 만드는 과정은 일반적인 프로젝트와 비슷하다. starter-web이든 security든 필요한 의존성이 있다면 이를 추가하면 된다. 반면 일반적인 앱을 만드는 것과의 차이점들도 다소 존재한다. 그래서 웹 앱 만들듯이 만들면 에러, 버그가 난다. 그래서 아래에 소개할 방법들을 모두 적용해야 한다. 

## 일반적인 웹 앱과 라이브러리 제작의 차이점 1. “implemetation”

필자의 경우, 쿠키, JWT, Security, JPA 등 여러 분야에서의 기능들을 하나의 라이브러리에 만들 생각이었다. 그래서 처음에는 build.gradle 파일에 들어갈 거의 모든 의존성들에 “implementation”을 붙였다. 주로 웹 앱만 만들어봤고 라이브러리는 처음이었기에 이렇게 하였다. 또한, 참고 자료들에서도 원하는 의존성을 얼마든지 넣어도 상관없다고 해서 문제가 없었을 줄 알았다. 하지만 테스트용 프로젝트를 별도로 만들고 거기서 테스트를 해보니 한 가지 문제점이 있었다. 

```
implementation 'org.springframework.boot:spring-boot-starter-security'
```

예시 1-1.

라이브러리에는 위와 같이 Spring Security 의존성을 넣었지만, 테스트용 프로젝트에는 아직 시큐리티 관련 작업을 할 게 아니라서 넣지 않은 상태였다. 그런데도 테스트용 프로젝트에서 웹 앱을 실행하고 swagger를 통해 확인하려고 하니 Spring Security에서 제공하는 기본 로그인 화면이 뜨는 것이었다. 이를 통해 설령 라이브러리를 사용할 테스트용 프로젝트 자체에는 spring security를 넣지 않았어도 라이브러리 내 gradle에 해당 의존성이 있기에 이의 영향을 받았음을 알 수 있었다. 그렇다고 해서 라이브러리에서 아예 해당 의존성을 뺄 수도 없었다. Security를 이용한 기능도 라이브러리로 만들 계획이었기 때문이다. 

다행히도 해결책은 간단했다. 라이브러리의 build.gradle에 다음과 같이 수정하면 되는 것이었다. 

```
compileOnly 'org.springframework.boot:spring-boot-starter-security'
```

예시 1-2

“compileOnly”라는 표현에서도 직관적으로 알 수 있듯, 이 키워드로 대체하면 컴파일 과정에서만 해당 의존성을 가져와 사용할 수 있게 된다. 이 키워드로 바꾸니 테스트용 프로젝트에서도 더 이상 시큐리티 로그인 창이 뜨지 않았다. 이렇게 라이브러리 개발 과정에서는 해당 의존성을 이용한 개발이 가능하면서도 사용자 입장에서는 특정 기능들만을 골라 사용할 수 있는 일석이조의 효과를 누릴 수 있게 된다. 만약 사용자가 해당 기능을 원한다면 자신의 프로젝트 내 build.gradle 파일에 예시 1-1과 같이 “implemetation” 키워드와 함께 해당 의존성을 명시하면 사용할 수 있게 된다. 원치 않는다면 아예 해당 의존성을 넣지 않으면 된다. 이 두 경우 모두 라이브러리의 특정 기능을 사용하고 싶어도 사용하지 못한다던가, 반대로 원치 않았던 기능을 강제로 사용해야 하는 문제 모두 해소되었음을 확인하였다. 

이 라이브러리의 사용자도 스프링부트로 프로젝트를 만드는 개발자라 가정하였기에 사용자가 원하는 기능에 대해서만 관련 의존성을 넣을 수 있도록 하는 이러한 방식이 나쁘지 않다고 생각하였다. 실제 필자가 개발한 라이브러리의 build.gradle을 보면 다음과 같이 구성되어 있다.

```
dependencies {
	compileOnly 'org.springframework.boot:spring-boot-starter'
	compileOnly 'org.springframework.boot:spring-boot-starter-security'
	compileOnly 'org.springframework.boot:spring-boot-starter-validation'
	compileOnly 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.springframework.boot:spring-boot-starter-data-jpa'
	compileOnly 'org.projectlombok:lombok'
	//developmentOnly 'org.springframework.boot:spring-boot-devtools'
	annotationProcessor 'org.springframework.boot:spring-boot-configuration-processor'
	annotationProcessor 'org.projectlombok:lombok'

	// JWT
	compileOnly 'io.jsonwebtoken:jjwt-api:0.12.6'
	runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.6'
	runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.6'
}
```

코드 1-1. 필자가 제작한 라이브러리 내 build.gradle

“implemtation”이 없고 대신 “compileOnly”가 많은 것을 알 수 있다. 필자가 미처 몰랐던 또 다른 사실은 웹 앱 개발 시에 도움을 주는 `developmentOnly 'org.springframework.boot:spring-boot-devtools'` 도 main 메서드를 포함한 application 클래스가 없는 라이브러리에서는 오히려 에러가 발생했고, 따라서 제외해야함을 알 수 있었다. 이 의존성도 라이브러리를 사용하는 개발자의 독립적인 앱을 만들기 위한 프로젝트에서만 필요한 것이었다.

만약 라이브러리의 규모가 크다면 이를 분리하여 개발, 배포하는 것도 한 방법일 수 있겠다. 이 경우 앞서 설명했던 것처럼 굳이 라이브러리 내 build.gradle 파일 내 의존성들을 “compileOnly”로 설정하지 않고 “implementation”으로 설정해도 무관할 것이다. 그러나 필자의 경우 쿠키, JPA, JWT등 분야는 다양해도 기능의 수는 적은 소규모인지라 따로 분리해서 별도의 여러 개의 라이브러리로 만들어야만 하는 것인지에 대해 모호한 상황이었고, 애초에 라이브러리 제작과 배포 자체도 처음이라 라이브러리 제작 및 배포의 방법을 배우는 게 우선이기도 하여 하나의 라이브러리로 합쳐서 제작하게 되었다. 만약 나중에 라이브러리의 규모가 커지거나 특정 분야에 대한 기능들이 많아지면 따로 분리하여 별도의 라이브러리들로 운영하는 것도 좋을 것 같다. 

## 일반적인 웹 앱과 라이브러리 제작의 차이점 2. 자동 구성 설정하기

```java
@Component
@RequiredArgsConstructor
public class CookieUtils {

    private final CookieConfigurer cookieConfigurer;

    public void addCookie(
        HttpServletResponse response,
        CookieRequest cookieRequest
    ) {
        //...
    }

    public void deleteCookies(
        HttpServletRequest request,
        HttpServletResponse response,
        String... cookieNames
    ) {
        //...
    }

    public String serialize(Object obj) {
        //...
    }

    public <T> T deserialize(Cookie cookie, Class<T> cls)
        throws IOException, ClassNotFoundException
    {
        //...
    }

}
```

코드 1-2.

위와 같이 쿠키 관련 기능들을 라이브러리에서 만들었다고 해보겠다. `@Component` 선언을 통해 해당 클래스는 Spring bean임을 명시하였다. 하지만 이 라이브러리 사용자의 프로젝트에서는 위 클래스가 component scan 대상에 포함되지 않는다. 이 상태로 배포하면 이 라이브러리 사용자의 프로젝트 내에서는 해당 Bean을 찾을 수 없다면서 에러가 뜬다. 다음은 그 예시이다.

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Parameter 0 of constructor in com.jerocaller.test.spoonsuits_local_test.spoonsuits_local_test.config.SecurityConfig required a bean of type 'com.jerocaller.libs.spoonsuits.web.jwt.DefaultJwtAuthenticationFilter' that could not be found.

Action:

Consider defining a bean of type 'com.jerocaller.libs.spoonsuits.web.jwt.DefaultJwtAuthenticationFilter' in your configuration.
```

코드 1-3. 여기서는 DefaultJwtAuthenticationFilter 클래스가 필자가 만든 라이브러리 내 기능 중 하나이다. 

라이브러리를 사용하는 프로젝트에서 앱 실행 시 위와 같이 라이브러리에서 제공하는 특정 bean을 찾을 수 없다는 에러 메시지가 뜬다. 

이로 인해 라이브러리에서 따로 해당 bean을 스캔 대상에 포함되도록 자동 구성(auto-configuration)을 설정해줘야 한다. 

필자의 경우, 라이브러리 구조는 대략 다음과 같았다.

```java
// src/main/java

config/
    AutoConfig.java

web/
   // 웹 관련 클래스들. 
```

예시 1-3.

```java
package com.jerocaller.libs.spoonsuits.config;

import org.springframework.boot.autoconfigure.AutoConfiguration;
import org.springframework.context.annotation.ComponentScan;

@AutoConfiguration
@ComponentScan("com.jerocaller.libs.spoonsuits")
public class AutoConfig {

}

```

코드 1-4. AutoConfig.java

web 폴더 내에는 사실상 라이브러리를 구성하는 기능들이 모여 있고, config 폴더 내에는 자동 구성을 위한 설정 클래스가 존재하는 구조로 만들었다. 필자는 실제 기능과 설정 클래스들을 분리하는 게 좋다고 생각하여 위와 같이 구조를 구성한 것이며, 꼭 필자처럼 하지 않아도 된다. 다만 위 코드 1-4처럼 `@AutoConfiguration` 어노테이션이 부여된 설정 클래스는 반드시 정의해야 한다. 이것이 앞서 말한 자동 구성을 설정할 수 있는 방법이기 때문이다. 위 설정 클래스에서 Spring bean으로 등록하고자 하는 POJO가 있으면 얼마든지 `@Bean` 어노테이션으로 등록해도 된다. 필자는 Spring bean으로 사용해야 하는 클래스에는 모두 `@Component` 를 따로 적용하였기에 생략한 것이다. 

> `@Configuration` 을 대신 적용해도 작동은 되는 것으로 확인되었다. 사실 `@AutoConfiguration` 어노테이션의 소스 코드를 보면 해당 어노테이션에도 `@Configuration` 어노테이션이 적용되어 있다.
> 

한 편, 필자의 경우 `@AutoConfiguration` 이 적용된 클래스가 최상위 패키지에 존재하지 않기에, `@ComponentScan` 을 통해 web 패키지 내에 정의된 Spring bean들을 스캔할 수 있도록 명시해야 한다. 해당 어노테이션을 사용하지 않으면 라이브러리를 사용하는 프로젝트에서 앞선 코드 1-3과 같이 여전히 해당 bean을 찾을 수 없다고 에러가 뜨기 때문이다. 이는 main 메서드가 포함된 `~application.java` 클래스에 적용되는 `@SpringBootApplication` 와 같은 원리이다. 만약 이 `@ComponentScan` 어노테이션을 적용하고 싶지 않다면 해당 설정 클래스를 최상위 패키지에 둬야 한다. 

여기서 끝이 아니다. 스프링부트에서 라이브러리 내에 위와 같이 설정한 자동 구성을 인식하도록 하려면 별도의 파일을 추가해야 한다. 

![사진 1-1.](/images/2025-04-10/spoon-suits-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%20%EA%B8%B0%EB%B0%98%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%A0%9C%EC%9E%91%20%ED%9B%84%20maven%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/1.png)

사진 1-1.

위와 같이 `src/main/resources` 폴더 내에 `META-INF/spring` 폴더를 생성한 뒤, 그 내부에 `org.springframework.boot.autuconfigure.AutoConfiguration.imports` 파일을 생성한다. 해당 파일 내부에는 앞서 `@AutoConfiguration` 을 적용한 설정 클래스의 이름을 그 패키지 주소까지 적으면 된다. 

```
com.jerocaller.libs.spoonsuits.config.AutoConfig
```

코드 1-5. `org.springframework.boot.autuconfigure.AutoConfiguration.imports`

만약 설정 클래스가 더 있다면 바로 다음 줄에 똑같은 형식으로 작성하면 된다. 이와 같이 작성한 파일이 존재하면 스프링부트에서 이 설정 파일을 읽어 들여 앞서 설정한 자동 구성을 읽을 수 있게 되고, 이로 인해 사용자의 프로젝트 내에서도 라이브러리에서 제공하는 Spring bean을 인식하여 사용할 수 있게 된다. 

## 일반적인 웹 앱과 라이브러리 제작의 차이점 3. main 메서드가 포함된 `~application.java` 의 존재 유무

라이브러리 제작이 일반적인 웹 앱 제작과 다른 또 다른 한 가지는, 라이브러리는 웹 앱과 달리 독자적으로 실행되는 “애플리케이션”이 아니기에 main 메서드가 포함된 `~application.java` 클래스를 뺴는 것이 좋다는 것이다. 만약 라이브러리에 해당 클래스가 있다면 사용자의 프로젝트에도 의도하지 않은 영향을 줄 수도 있기 때문이다. 

그런데 이 메인 클래스 하나를 빼니 몇 가지 문제가 발생했었다. 

1. 라이브러리이기에 테스트가 필수였는데, 라이브러리 자체 프로젝트의 테스트 패키지 내에서는 사실상 라이브러리 내 기능들을 테스트하기 어려웠다. 이로 인해 별도의 테스트용 프로젝트 폴더를 만들어 그곳에서 테스트를 진행해야 했다. 
2. jar 파일로의 빌드 과정에서 에러가 발생하여 배포 자체가 안되었다. 직접 jar 파일로 만드는 방식과 Maven local repository로 배포하는 과정 모두 진행이 되지 않았다. 이는 build.gradle 내에서 몇 가지 설정을 더 해줘야 해결됨을 알았다. 

1번의 문제를 발견하게 된 건 처음에 라이브러리 프로젝트 내 테스트 패키지에서 테스트를 작성하고 실행했을 때였다. (이에 대한 자세한 사항은 다음을 참조: [https://github.com/JeroCaller/Spoon-Suits/issues/16](https://github.com/JeroCaller/Spoon-Suits/issues/16))

```java
@Import({CookieConfig.class})
@SpringBootTest(classes = {MockCookieController.class})
@AutoConfigureMockMvc
@Slf4j
class CookieUtilsTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;

    // ...
    @Test
    @DisplayName("쿠키가 추가되는지 테스트")
    void addCookieTest() throws Exception {
        // ...
       final ResultActions resultActions = mockMvc.perform(
            get("/test/cookie")
                .content(objectMapper.writeValueAsString(mockCookie))
                .contentType(MediaType.APPLICATION_JSON)
        );
       //...
    }
}
```

코드 1-6.

라이브러리에서 만든 쿠키 기능을 테스트하고자 테스트 패키지 내에 mock controller를 만들었고, REST API로 테스트할 예정이었기에 ObjectMapper을 사용하였다. 그런데 다음의 에러가 발생하였다.

```java
org.springframework.beans.factory.UnsatisfiedDependencyException: Error creating bean with name 'com.jerocaller.libs.spoonsuits.web.cookie.CookieUtilsTest': Unsatisfied dependency expressed through field 'objectMapper': No qualifying bean of type 'com.fasterxml.jackson.databind.ObjectMapper' available: expected at least 1 bean which qualifies as autowire candidate. Dependency annotations: {@org.springframework.beans.factory.annotation.Autowired(required=true)}
```

코드 1-7.

이는 ObjectMapper라는 bean을 인식하지 못해서 생긴 일이었다. 이 당시 필자는 이를 이상하게 여겼다. 다른 프로젝트에서는 ObjectMapper를 자동으로 인식하여 이런 에러가 발생한 적이 없기 때문이었다. 그래서 위 코드에서는 `@SpringBootTest(classes = {MockCookieController.class, ObjectMapper.class})` 와 같이 ObjectMapper 클래스를 명시적으로 import하여 문제를 해결하였다. 하지만 나중에 알고보니 라이브러리에는 main 메서드가 있는 `~application.java` 가 없기에 발생한 문제였음을 파악하였다. 그래서 이후로는 “spoonsuits-local-tets”라는 별도의 테스트용 프로젝트를 만들어 메인 패키지에는 `~application.java` 클래스가 존재하도록 놔뒀고, 이 프로젝트 폴더의 테스트 패키지 내에서 테스트 코드를 작성, 실행했더니 이러한 문제를 해결할 수 있었다. 

2번의 문제는 다음과 같은 방법으로 해결하였다.

# Maven local repository로 배포하기 위한 build.gradle 설정

앞서 언급한 자동 구성 설정까지 완료해도 지금 당장은 배포할 수가 없다. 예를 들어 gradle을 이용하여 직접 jar 파일로 만들어본다고 가정해보자. intellij에서는 다음과 같은 절차를 따르면 된다.

![사진 2-1.](/images/2025-04-10/spoon-suits-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%20%EA%B8%B0%EB%B0%98%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%A0%9C%EC%9E%91%20%ED%9B%84%20maven%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/2.png)

사진 2-1.

intellij 창 우측의 Gradle 아이콘(코끼리 모양)을 클릭하면 위와 같은 메뉴가 뜨는데, Tasks → build → clean을 더블 클릭하여 build 폴더를 비움으로써 이전 빌드 결과물로 인해 잠재적으로 발생할 수도 있는 문제를 해소한다. 그 후, Tasks → build 폴더 내 build 항목을 더블 클릭하면 여태까지 만든 프로젝트를 jar 파일로 생성할 수 있게 된다. 해당 기능들은 다음의 명령어로 대체할 수 있다고 한다.

```java
macOS:  gradlew clean build
window: gradlew.bat clean build
```

그런데 이 과정을 거치면 다음의 에러가 뜨면서 빌드에 실패한다.

```java
Execution failed for task ':bootJar'.
> Error while evaluating property 'mainClass' of task ':bootJar'.
   > Failed to calculate the value of task ':bootJar' property 'mainClass'.
      > Main class name has not been configured and it could not be resolved from classpath ...
```

코드 2-1.

이는 main 메서드를 포함하는 클래스가 존재하지 않기 때문에 발생하는 일이다. 하지만 독립적으로 실행 가능한 웹 앱이 아닌 다른 프로젝트에서 사용할 라이브러리이기에 해당 클래스를 다시 생성할 수는 없다. 이를 해결하려면 build.gradle에 다음과 같은 설정들을 추가해야 한다.

```
plugins {
	//... 
	id 'org.springframework.boot' version '3.4.3' apply false    // (apply false 추가)
	//...
}

dependencyManagement {
	imports {
		mavenBom org.springframework.boot.gradle.plugin.SpringBootPlugin.BOM_COORDINATES
	}
}
```

코드 2-2. build.gradle

Gradle에서는 위와 같이 “plugins”를 이용하여 spring boot plugin을 사용할 수 있으며, 이를 통해 bootJar를 실행할 수 있다고 한다. 여기서 bootJar는 스프링부트로 제작한 프로젝트를 실행가능한 jar(Executable Jar) 파일로 빌드하는데 사용하는 task이다. 그런데 라이브러리 제작 시에는 앞서 언급했듯 독립적인 앱을 만드는 것이 아니기에 main 메서드가 포함된 application 클래스를 제거하였다고 했는데, 이는 라이브러리 자체만으로 실행 가능한 파일이 되면 안된다는 뜻이기도 하다. 따라서 위와 같이 `apply false` 를 통해 bootJar의 실행가능한 jar로 빌드하는 기능을 끈 것이다. 

하지만 이 경우 스프링부트에서 제공하는 의존성 라이브러리 버전 관리가 불가능해진다. 이를 다시 가능하게 하기 위해선 의존성 라이브러리 버전들이 명시된 pom.xml 형태의 파일인 bom 파일을 사용하기 위해 위와 같이 `BOM_COORDINATES` 를 명시해야한다. 

결과적으로 위와 같이 build.gradle에 추가 설정을 하면 jar 파일로 빌드할 수 있게 된다. 

![사진 2-2. 코드 2-2까지의 설정 후 gradle clean → build 후 build 폴더 내에 jar 파일이 생성된 모습](/images/2025-04-10/spoon-suits-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%20%EA%B8%B0%EB%B0%98%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%A0%9C%EC%9E%91%20%ED%9B%84%20maven%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/3.png)

사진 2-2. 코드 2-2까지의 설정 후 gradle clean → build 후 build 폴더 내에 jar 파일이 생성된 모습

그런데 이 방식은 라이브러리를 직접 jar 파일로 빌드하는 형식이라 사용자 입장에서는 해당 jar 파일을 직접 다운로드 받거나 하여 일일히 손수 자신의 프로젝트 폴더에 위치시켜야 한다. 하지만 필자가 원하는 궁극적인 목적은 이렇게 손수 jar 파일을 직접 넣는 방식이 아니라 build.gradle에 의존성을 명시하여 자동으로 해당 jar 파일이 다운로드되도록 하는 것이다. 로컬 기기 내부에서만 사용하는 것으로 한정하는 경우, maven local repository로 배포하는 방법이 있다. 이를 위해서는 build.gradle에 다음을 추가한다.

```
plugins {
	// ...
	id 'maven-publish'
}

publishing {
  publications {
    maven(MavenPublication) {
      // Publication only contains dependencies and/or constraints without a version. You should add minimal version information, publish resolved versions
      // 아래 versionMapping을 더해주지 않으면 위와 같은 에러 메시지가 뜸.
      versionMapping {
        usage('java-api') {
          fromResolutionOf('runtimeClasspath')
        }
        usage('java-runtime') {
          fromResolutionResult()
        }
      }

      groupId = 'com.jerocaller.libs.spoonsuits'
      artifactId = 'spoonsuits'
      version = "0.1.1"

      from components.java
    }
  }
}
```

코드 2-3.

위 코드에서 만약 groupId, artifactId, version을 별도로 명시하지 않으면 처음 프로젝트 생성 시 각각의 항목에 기입한 정보들로 설정된다고 한다. 

한 편, 위 코드의 주석으로도 써놨듯, `versionMapping` 부분을 추가하지 않으면 버전을 인식할 수 없다면서 에러가 뜨기 때문에 해당 부분도 추가해야 한다. 이 부분도 앞서 bootJar 기능을 disable 상태로 변경했기에 발생하는 문제로 보인다. 

위 설정들의 추가로 인한 필자의 build.gradle 전체 코드는 다음과 같이 되었다. 

```
plugins {
	id 'java'
	id 'org.springframework.boot' version '3.4.3' apply false
	id 'io.spring.dependency-management' version '1.1.7'
	id 'maven-publish'
}

group = 'com.jerocaller.libs'
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

dependencyManagement {
	imports {
		mavenBom org.springframework.boot.gradle.plugin.SpringBootPlugin.BOM_COORDINATES
	}
}

publishing {
	publications {
		maven(MavenPublication) {
			// Publication only contains dependencies and/or constraints without a version. You should add minimal version information, publish resolved versions
			// 아래 versionMapping을 더해주지 않으면 위와 같은 에러가 뜸.
			versionMapping {
				usage('java-api') {
					fromResolutionOf('runtimeClasspath')
				}
				usage('java-runtime') {
					fromResolutionResult()
				}
			}

			groupId = 'com.jerocaller.libs.spoonsuits'
			artifactId = 'spoonsuits'
			version = "0.1.1"

			from components.java
		}
	}
}

dependencies {
	compileOnly 'org.springframework.boot:spring-boot-starter'
	compileOnly 'org.springframework.boot:spring-boot-starter-security'
	compileOnly 'org.springframework.boot:spring-boot-starter-validation'
	compileOnly 'org.springframework.boot:spring-boot-starter-web'
	compileOnly 'org.springframework.boot:spring-boot-starter-data-jpa'
	compileOnly 'org.projectlombok:lombok'
	annotationProcessor 'org.springframework.boot:spring-boot-configuration-processor'
	annotationProcessor 'org.projectlombok:lombok'

	// JWT
	compileOnly 'io.jsonwebtoken:jjwt-api:0.12.6'
	runtimeOnly 'io.jsonwebtoken:jjwt-impl:0.12.6'
	runtimeOnly 'io.jsonwebtoken:jjwt-jackson:0.12.6'
}

tasks.named('test') {
	useJUnitPlatform()
}

```

코드 2-4. build.gradle

앞선 코드 2-3의 부분을 추가해야 intellij의 gradle 메뉴에서 “publishing” 항목이 뜬다. 여기서 “publishToMavenLocal” 항목을 클릭하면 jar 파일이 로컬 기기 내 `C:/Users/사용자 이름/.m2/repository` 폴더 내부에 생긴다. 

(잠재적인 에러를 방지하고자 한다면 build → clean을 먼저 한 다음 publishing → publishToMavenLocal을 실행한다)

![사진 2-3. gradle 메뉴에서 publishing 폴더가 생성되었다. 이 안에서 “publishToMavenLocal(publishing 폴더 내 맨 아래 항목)”을 클릭하면 maven local repository로 jar 파일이 배포된다.](/images/2025-04-10/spoon-suits-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%20%EA%B8%B0%EB%B0%98%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%A0%9C%EC%9E%91%20%ED%9B%84%20maven%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/4.png)

사진 2-3. gradle 메뉴에서 publishing 폴더가 생성되었다. 이 안에서 “publishToMavenLocal(publishing 폴더 내 맨 아래 항목)”을 클릭하면 maven local repository로 jar 파일이 배포된다.

필자의 경우, groupId, artifactId, version 정보가 추가적으로 폴더로 생성되어 결과적으로 `C:/Users/사용자 이름/.m2/repository/com/jerocaller/libs/spoonsuits/spoonsuits` 내에 `0.1.1` 이라는 폴더가 생성되었다. 

![사진 2-4. maven local repository에 배포된 라이브러리 폴더. `~/.m2/repository` 가 maven local repository라 보면 되겠다. 위 사진에서는 이전에 잠깐 배포해봤던 0.1 버전도 포함되어 있음을 볼 수 있다. ](/images/2025-04-10/spoon-suits-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%20%EA%B8%B0%EB%B0%98%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%A0%9C%EC%9E%91%20%ED%9B%84%20maven%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/5.png)

사진 2-4. maven local repository에 배포된 라이브러리 폴더. `~/.m2/repository` 가 maven local repository라 보면 되겠다. 위 사진에서는 이전에 잠깐 배포해봤던 0.1 버전도 포함되어 있음을 볼 수 있다. 

![사진 2-5. 0.1.1 버전 폴더의 내부 모습. jar 파일이 배포된 것을 볼 수 있다.](/images/2025-04-10/spoon-suits-%EC%8A%A4%ED%94%84%EB%A7%81%EB%B6%80%ED%8A%B8%20%EA%B8%B0%EB%B0%98%20%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC%20%EC%A0%9C%EC%9E%91%20%ED%9B%84%20maven%20%EB%A1%9C%EC%BB%AC%20%EC%A0%80%EC%9E%A5%EC%86%8C%EC%97%90%20%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0/6.png)

사진 2-5. 0.1.1 버전 폴더의 내부 모습. jar 파일이 배포된 것을 볼 수 있다.

# 로컬 기기의 다른 프로젝트 내에서 라이브러리 사용하기

방금 배포한 maven local repository 내 라이브러리를 다른 프로젝트에서 사용하려면 해당 프로젝트의 build.gradle에 다음과 같이 의존성을 명시하기만 하면 된다.

```
repositories {
	mavenLocal()  // 로컬 저장소에서 의존성을 탐색하도록 추가.
	//...
}

dependencies {
    // ...
    // 이전에 라이브러리 프로젝트 내 build.gradle에 명시한 groupId, artifactId, version을 
    // 순서대로 groupId:artifactId:version 형식으로 작성하면 된다. 
    implementation 'com.jerocaller.libs.spoonsuits:spoonsuits:0.1.1'
}
```

코드 3-1. 라이브러리를 사용하는 프로젝트 내 build.gradle

이렇게 하면 프로젝트에서 바로 해당 라이브러리를 사용할 수 있게 된다. 다만 필자가 만든 라이브러리의 경우 앞서 언급했듯 쿠키, JPA, JWT 등 여러 분야의 기능들이 있고, 사용자가 이들 중 몇몇 개만 선택하여 사용하는 것도 가능하게 하기 위해 관련 의존성들을 “compileOnly”로 처리했기에 사용자의 프로젝트에 내에서는 `implementation 'org.springframework.boot:spring-boot-starter-security'` 와 같이 별도로 명시해줘야 특정 기능을 사용할 수 있게 된다. 

실제로 필자는 “spoonsuits”라는 프로젝트 폴더 내 “spoonsuits”라는 라이브러리가 담긴 하위 프로젝트 폴더와 “spoonsuits-local-test”라는 테스트용 하위 프로젝트 폴더 이렇게 두 개의 별도 프로젝트로 나눠 운영하였다. “spoonsuits” 라이브러리에 새 기능을 추가하면 테스트 코드 작성을 위해 Gradle → Tasks → build → clean, Gradle → tasks → publishing → publishToMavenLocal을 클릭하여 매번 새로이 배포를 한 후, “spoonsuits-local-test” 폴더에서 테스트 코드를 작성하는 방식으로 테스트를 진행하였다. 이러한 프로젝트 폴더 구조는 Github repository에도 그대로 반영되어 있으니 참고.

[https://github.com/JeroCaller/Spoon-Suits](https://github.com/JeroCaller/Spoon-Suits)

필자는 편의를 위해 매번 새로운 기능을 추가할 때마다 버전을 달리 하지 않고 바로 publish하였다. 만약 버전 명시를 달리 하여 좀 더 명확한 환경에서 테스트를 하고자 한다면 기능을 추가할 때마다 version up하여 배포해도 될 것이다. 필자는 필자가 구상한 모든 기능을 다 구현한 후 온라인으로 처음 배포할 때 “v1.0.0” 이런 식으로 버전을 명시하고, 그 후 새 기능 추가 또는 버그 수정할 때마다 version up할 생각이기에 아직까지는 이 단계가 아니라서 버전을 달리 하지 않았다. 지금은 로컬에서 테스트하는 용도로만 사용하고 있기에 버전을 달리 하지 않은 것이다. 

# 글을 마치며…

로컬 기기 내에서의 라이브러리 배포까지의 과정을 이 글 하나로 축약했지만 사실 이 과정이 모두 처음이었기 때문에 생각보다 시간이 오래 걸렸다. 처음에 라이브러리를 위한 자동 구성 및 build.gradle 추가 설정을 몰라 하루에 9시간을 보내면서도 그 원인을 찾지 못해 처음 며칠 동안은 고생을 많이 했다. 이걸 해결하니 이번에는 테스트 코드를 작성하고 여기서 발견한 버그 및 에러들의 원인을 찾느라 또 시간을 허비할 수밖에 없었다. 그러다보니 라이브러리에 추가한 기능들 그 자체는 그와 관련된 개념만 알고 있다면(예를 들면 JWT 기능이라면 먼저 JWT에 대한 개념을 알고 있어야한다든가) 그렇게까지 복잡하고 어렵다고 보긴 힘든 것들이었음에도 내 예상보다 라이브러리 제작 기간이 길어져서 이에 대해선 다소 아쉬움으로 남는다. 하지만 그러한 힘들고 어려운 과정을 거쳤기에 앞으로는 라이브러리 제작에 큰 어려움은 없으리라 생각한다. 

물론 이 글을 작성하는 현재를 기준으로 아직 온라인 상으로의 배포는 하지 않았기에 이에 대해서도 진행할 것인데, 이 과정도 처음이라 아마 며칠 간은 고생할 것 같다. 조사해보니 보통 jitpack이라는 사이트를 이용하여 배포하는 게 간편하다고 해서 이를 이용하여 배포해볼 예정이다. 온라인 상에서의 배포까지 마치면 이에 대한 글로 작성하여 다음에 포스트하겠다. 

---

References

[1] [스프링부트 공유라이브러리 만들고 jitpack으로 배포하기 - 1편](https://ssdragon.tistory.com/167)

[2] [스프링부트 공유라이브러리 만들고 jitpack으로 배포하기 - 2편](https://ssdragon.tistory.com/168)

[3] [스프링 라이브러리 만들기 @AutoConfiguration](https://youseong.tistory.com/6)

[4] [Creating Your Own Auto-configuration :: Spring Boot](https://docs.spring.io/spring-boot/reference/features/developing-auto-configuration.html)

[5] [[springboot][Gradle] 자동 설정 만들기 #1 Starter와 Auto-Configure](https://yonghwankim-dev.tistory.com/508)

[6] bootJar

[[Spring Boot] jar 빌드하기 (feat. Gradle, bootJar)](https://velog.io/@jwkim/spring-boot-build-jar)

[7] bootJar

[Springboot Application 실행하기 (Manifest, Jar, BootJar 개념 짚기)](https://cookie-dev.tistory.com/28)

[8] 스프링 BOM

[스프링 BOM이란?](https://onestone-dev.tistory.com/70)

[9] maven local repository

[스프링부트 개발환경 구성하기 (4) 메이븐 Local Repository 설정](https://devpad.tistory.com/104)

[10] [[Gradle] 라이브러리 만들고 사용하기(Maven Local Repository)](https://velog.io/@haerong22/Gradle-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EB%A7%8C%EB%93%A4%EA%B3%A0-%EC%82%AC%EC%9A%A9%ED%95%98%EA%B8%B0Maven-Local-Repository)