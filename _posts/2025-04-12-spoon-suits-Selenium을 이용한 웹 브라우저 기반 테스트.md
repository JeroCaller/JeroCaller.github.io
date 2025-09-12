---
title: "[Spoon Suits] Selenium을 이용한 웹 브라우저 기반 테스트"
category: "Personal Project"
tag: ["Personal Project", "Library", "Spring Boot", "Spoon Suits", "Selenium", "Test", "Browser Authmation", "Framework"]
---

필자가 만들고 있는 Spoon Suits 라이브러리는 스프링부트 프로젝트에서 사용할 목적을 가지고 있다. 웹 애플리케이션 개발 용도로만 그 목적을 제한한 건 아니지만, 아무래도 웹 개발을 집중적으로 공부해서인지 현재 모든 기능들이 웹과 관련이 있는 기능들이었다. Cookie나 JWT와 관련된 기능들도 포함되어 있는 것이 그 예이다. 그래서인지 테스트 코드를 작성하다보니 기존의 spring boot starter test에서 제공해주는 툴만으로는 원하는 테스트를 하기가 어려워지는 지점이 생기게 되었다. 예를 들면 쿠키 삭제 기능을 테스트하기 위해 여러 개의 쿠키들을 먼저 생성하는 REST API를 호출한 뒤, 그 중 특정 쿠키를 삭제하는 또 다른 REST API를 호출하여 특정 쿠키 삭제 기능에 대한 테스트가 필요했는데, 기존의 spring boot starter test로는 첫 번째 API 호출 후 두 번째 API를 호출하려고 할 때 이전에 생성된 쿠키들이 사라져서 테스트를 할 수 없었던 문제가 발생했다. 이러한 문제를 해결하기 위해 조사를 해본 결과, 테스트용 웹 브라우저를 띄워 실제 웹 사용 환경과 똑같이 구성하여 테스트를 할 수 있게 해주는 Selenium이라는 프레임워크를 접하게 되었다. 이 글에서는 Selenium에 대한 설명과 더불어, 필자가 라이브러리의 특정 기능들을 테스트하기 위해 이 프레임워크를 어떻게 사용했는지를 기술하고자 한다. 

# Selenium

Selenium은 브라우저 자동화 (automating browser)를 위한 프레임워크로, 주로 웹 브라우저 기반의 자동화된 테스트 코드 작성을 위한 프레임워크라 한다. 하지만 공식 홈페이지에 따르면, 테스트 용도로만 한정하지는 않는다고 한다. 프로그래밍적으로 웹 브라우저를 띄우고 그 곳에서 URL을 통해 특정 사이트로 이동할 수도 있고, 여기서 더 나아가 현재 사이트를 구성하고 있는 HTML Element들도 프로그래밍적으로 추출할 수 있다는 특징을 가지고 있다. 그로 인해 이 프레임워크는 테스트 뿐만 아니라 크롤링에서도 사용된다고 한다. 또한 방금 언급했듯 HTML Element들도 Java, Python과 같은 언어로 가져올 수 있기 때문에 사이트의 특정 버튼을 누르면 그에 대한 결과로 의도대로 UI 요소들이 바뀌었는지 등을 테스트할 수 있는 UI test로도 사용된다고 한다. 

Selenium은 특정 프로그래밍 언어에만 종속된 것이 아니고 Java, Python, Javascript 등 여러 언어들에서 사용 가능하다. 또한 브라우저를 띄워서 작업하는 방식이라서 크롬, 파이어폭스 등 다양한 브라우저들의 WebDriver들도 제공하기에 이 중 원하는 driver만 골라 특정 브라우저 환경을 마련할 수도 있다. 

> 브라우저 자동화라는 것은 쉽게 말해 사람이 직접 마우스, 키보드 등을 이용하여 직접 웹 브라우저와 상호작용하는 것이 아니라 프로그래밍적으로 웹 브라우저와 상호작용하는 방식이며, 이러한 특성 때문에 “자동화”라는 용어가 붙은 것으로 보인다.
> 

Selenium 공식 사이트

[Selenium](https://www.selenium.dev/)

Selenium 공식 document

[The Selenium Browser Automation Project](https://www.selenium.dev/documentation/)

공식 사이트 내에 document도 있기에 Java, Python 등 특정 언어 내에서 어떻게 사용할지 그 API에 대한 설명들도 있어 이를 적극적으로 참고하면 되겠다. 

Gradle에서는 다음과 같은 의존성을 넣으면 된다.

```
// 웹 브라우저 환경에서의 자동화 테스트를 위한 프레임워크
// https://mvnrepository.com/artifact/org.seleniumhq.selenium/selenium-java
implementation 'org.seleniumhq.selenium:selenium-java:4.30.0'
```

코드 1-1. build.gradle

원래 Selenium을 사용하려면 앞서 말헀듯 WebDriver라는 게 필요하다. 마치 MySQL, Oracle 등 여러 DBMS 회사들이 각자의 driver를 제공하여 이를 자바의 JDBC를 통해 원하는 driver를 선택, 사용할 수 있는 것과 같은 이치라 보면 되겠다. 크롬, 파이어폭스 등 브라우저 회사마다 존재하는데, 이 역시도 Selenium 사이트에서 다운받아 원하는 WebDriver를 jar 파일 형식으로 가져와 자신의 프로젝트 내에 위치시키면 된다. [https://www.selenium.dev/downloads/](https://www.selenium.dev/downloads/)

하지만 위 코드 1-1과 같이 gradle을 이용하면 이러한 WebDriver마저도 자동으로 프로젝트 내에 위치하기에 따로 다운 받아 설치할 필요는 없다. 

# Selenium을 테스트용으로 활용하기

필자는 Selenium을 이용하여 테스트 코드를 다음과 같은 형식으로 작성하였다.

```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)  // #1
@Slf4j
class CookieControllerOnBrowserTest {

    private WebDriver webDriver;  // #2

    @LocalServerPort
    private int port;  // #3
    
    private String requestDomainUrl;
    private String requestMockCookiesCreationUrl;
    private String requestDeleteCookiesUrl;
    
    // 생략...
    
    @BeforeEach
    void setUp() {
        webDriver = new ChromeDriver(SeleniumOptionConfigUtils.getChromeOption()); // #4
        requestDomainUrl = String.format("http://127.0.0.1:%d/test/cookie", port);  // #4-1
        requestMockCookiesCreationUrl = UriComponentsBuilder
            .fromUriString(requestDomainUrl)
            .path("/many")
            .build()
            .toUriString();
        requestDeleteCookiesUrl = UriComponentsBuilder
            .fromUriString(requestDomainUrl)
            .path("/selenium/delete")
            .build()
            .toUriString();
        // 생략...
    }

    @AfterEach
    void clean() {
        webDriver.quit();  // #5
    }
    
    // 생략...
    
    @Test
    @DisplayName("여러 개의 테스트용 쿠키 생성 여부 확인 테스트")
    void doesSeveralCookiesCreatedTest() {

        webDriver.get(requestMockCookiesCreationUrl);  // #6

        assertThat(requestMockCookiesCreationUrl)
            .isEqualTo(webDriver.getCurrentUrl());
        log.info("실제 브라우저 URL: {}", webDriver.getCurrentUrl());
        log.info("브라우저 탭 제목: {}", webDriver.getTitle());

        assertThat(webDriver.manage().getCookieNamed("TEST-COOKIE-1"))  // #7
            .isNotNull();
        Set<Cookie> actualCookies = webDriver.manage().getCookies();  // #8

        int sameCount = howManySameNamesAreContainedInCookie(
            expectedMockCookieNames,
            actualCookies
        );
        log.info("{} is null?", webDriver.manage()
            .getCookieNamed("TEST-COOKIE-1"));
        assertThat(actualCookies.size()).isEqualTo(3);
        assertThat(sameCount).isEqualTo(3);

    }
    
    @Test
    @DisplayName("""
        3개의 테스트용 쿠키들이 브라우저에 저장된 상태에서
        존재하지 않는 쿠키 삭제 시도 시 이미 저장된 쿠키들은 그대로 있는지 테스트.
    """)
    void dontDeleteInnocentCookies() {

        // 테스트용 쿠키 여러 개 생성.
        webDriver.get(requestMockCookiesCreationUrl);

        final String targetCookieName = "NO-COOKIE";
        final String deleteCookieUri = UriComponentsBuilder
            .fromUriString(requestDeleteCookiesUrl)
            .queryParam("name", targetCookieName)
            .build()
            .toUriString();

        // 대상 쿠키 삭제
        webDriver.get(deleteCookieUri);
        Set<Cookie> actualCookies = webDriver.manage().getCookies();
        int sameCount = howManySameNamesAreContainedInCookie(
            expectedMockCookieNames,
            actualCookies
        );
        assertThat(actualCookies.size()).isEqualTo(3);
        assertThat(sameCount).isEqualTo(3);

    }
    
    // 생략...
```

코드 2-1. CookieControllerOnBrowserTest.java

먼저 Selenium을 이용하여 웹 브라우저 기반 테스트를 하기 위해 #2과 같이 webDriver 필드를 선언하였다. 필자는 크롬 브라우저에서 테스트하길 원했기에 #4와 같이 `ChromeDriver` 객체를 생성하여 사용하도록 하였다. 정확한 테스트를 위해 `@BeforeEach`, `@AfterEach` 를 이용하여 각 테스트 케이스를 실행할 때마다 크롬 드라이버 객체를 생성, 소멸시키도록 하였다. webDriver 객체를 소멸시켜(쉽게 말하면 화면에 뜬 브라우저를 끄는 것) 사용을 종료하는 코드는 `webDriver.quit()` 을 이용하면 된다. 

#6과 같이 `get(url)` 메서드에 띄우고자 하는 사이트의 URL을 인자로 대입하면 해당 웹 사이트를 띄울 수 있다. 사실 필자의 경우 테스트용 웹 앱을 REST API로 작성했기에 API를 호출하는 용도로 사용하였다. 메서드 이름에서도 볼 수 있듯, GET 방식으로만 HTTP 요청을 할 수 있다. 

#7, #8와 같은 코드로 현재 테스트용 웹 브라우저에 저장된 특정 쿠키만을 가져오거나 아니면 모든 쿠키를 가져올 수 있다. 프로그래밍적으로 가져온 쿠키들을 통해 테스트를 할 수 있는 것이다. 

한 편, Selenium을 제대로 이용하려면 Selenium을 통해 띄운 웹 브라우저에서 현재 호스팅되고 있는 사이트로 이동하도록 해야 한다. 즉 여기서는 REST API를 제공하는 서버가 필요하다는 것인데, 여기서는 `@SpringBootTest` 를 통해 `src/main/java` 에 작성한 Spring bean들을 테스트용 Spring Context로 가져와 테스트 환경에서 구동시킬 것이다. 문제는 테스트를 위한 REST API 도메인의 포트 번호를 현재 코드 상에서는 알 수 없다는 사실이다. `src/main/java` 내 `~Application.java` 를 실행했을 때는 `application.yml`의 `server.port` 에 따로 포트 번호를 명시하지 않는 한 기본적으로 8080 포트번호로 실행되지만 테스트 환경에서는 랜덤으로 포트 번호가 부여된다. 이로 인해 테스트 코드 내에서 8080 포트번호로 하드코딩한 상태에서 테스트를 진행하면 네트워크 오류가 뜬다. 따라서 테스트 코드에서 HTTP 요청할 도메인 주소의 포트번호를 8080처럼 하드코딩해서는 안된다. 테스트 환경에서 실행할 웹 앱의 포트 번호를 알기 위해 위 코드의 #1에서처럼 `webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT` 속성값을 넣는다. 그 후 #3과 같이 `@LocalServerPort` 어노테이션을 통해 랜덤으로 부여된 포트 번호를 필드에 주입받는다. 이 필드 값을 토대로 #4-1과 같이 HTTP 요청할 도메인의 포트 번호를 동적으로 구성해야 한다. 

한 편, 위 코드의 #4 코드를 보면 `ChromeDriver` 객체 생성자에 무언가를 주입하고 있는데, 해당 코드는 다음과 같다. 

```java
package com.jerocaller.test.spoonsuits_local_test.spoonsuits_local_test.config;

import lombok.AccessLevel;
import lombok.NoArgsConstructor;
import org.openqa.selenium.chrome.ChromeOptions;

@NoArgsConstructor(access = AccessLevel.PRIVATE)
public class SeleniumOptionConfigUtils {

    /**
     * <p>
     *     Selenium을 이용한 모든 테스트에 적용할 Chrome option.
     * </p>
     * <p>
     *     여기서는 GUI를 지원하지 않는 리눅스와 같은 OS 환경에서도 Selenium 테스트가 진행될 수 있도록
     *     브라우저 GUI 창 띄우기를 방지하도록 설정함.
     * </p>
     *
     * @return
     */
    public static ChromeOptions getChromeOption() {
        ChromeOptions chromeOptions = new ChromeOptions();

        // 헤드리스 모드 활성화. 셀레니움 테스트 실행 시 브라우저 화면을 화면에 띄우지 않고,
        // 메모리 상에서만 실행하는 방식. 특히 GUI를 지원하지 않는 리눅스 등의 OS 환경에서 사용.
        chromeOptions.addArguments("--headless");
        chromeOptions.addArguments("--no-sandbox");

        // 리눅스와 같은 OS 환경에서 공유 메모리(`/dev/shm`) 공간 사용 시
        // 해당 공간이 매우 적게 할당되어 Chrome이 차지하는 메모리 용량이 이를 넘길 경우 문제가 발생할 수 있음.
        // 따라서 해당 공유 메모리 사용을 하지 않고 일반 파일 시스템(`/tmp`)을 사용하도록 함.
        chromeOptions.addArguments("--disable-dev-shm-usage");

        return chromeOptions;
    }
}

```

코드 2-2.

`ChromeDriver` 객체에는 `ChromeOptions` 객체를 주입하여 크롬 드라이버에 대한 옵션을 설정해줄 수 있다. 이 역할을 위 코드 2-2에서 수행하고 있다. 위에서는 3가지의 옵션들이 사용되었는데 각각에 대한 설명은 다음과 같다.

- `--headless` : 보통 Selenium 실행 시 화면에 웹 브라우저 창이 떴다가 작업이 끝나면 사라진다. 대신 Selenium을 실행시켜도 웹 브라우저 창이 뜨지 않도록 설정할 수 있는데, 이를 헤드리스 모드라 한다. 이는 터미널 창에서 `./gradlew` 를 실행하거나 리눅스와 같이 GUI를 지원하지 않는 OS에서 Selenium을 실행해야 할 때(Github Actions의 CI/CD라든가) 헤드리스 모드를 설정하지 않으면 자칫 에러가 날 수 있다. 따라서 이를 방지하여 selenium을 실행하더라도 웹 브라우저 창이 뜨지 않고 메모리 상에서만 실행되도록 하는 옵션이다.
- `--no-sandbox` : 샌드박스 기능을 비활성화하는 옵션. 샌드박스는 외부 프로그램으로부터 격리된 공간을 형성하는 보안적 기능이다. Selenium에서는 각 웹 페이지 간 격리를 통해 보안성을 높인다. 그러나 이러한 기능은 오히려 의도한 테스트 코드가 작동하지 않고 예기치 않은 에러를 발생시킬 수 있다. 따라서 여기서는 샌드박스 기능을 비활성화한다.
- `--disable-dev-shm-usage` : 크롬에서는 `/dev/shm` 이라는 공유 메모리 공간을 사용한다. 그러나 해당 공간은 매우 작아 실행 중 메모리 부족 현상이 나타날 수 있다. 따라서 여기서는 해당 공간 대신 일반적인 파일 시스템을 사용하여 메모리 부족 현상을 방지한다.

이 옵션 모두 나중에 GUI를 지원하지 않는 리눅스 또는 CI/CD 환경에서도 테스트 코드가 원활하게 작동하기 위한 설정이다. 

이제 테스트 코드를 실행하면 selenium을 활용한 쿠키 테스트가 문제없이 진행될 것이다. 

<style>
  details {
    background-color: #434555;
    margin-bottom: 1em;
  }
  details > summary {
    cursor: pointer;
    margin: 0.5em;
  }
  details a {
    text-decoration: none;
    margin: 0 0.5em;
  }
  details > div {
    padding: 0.5em;
  }
</style>
<details id="참고-1">
<summary>
    <strong>
        <i>참고) Selenium CDP version 경고 해결 방법</i>
    </strong>
    <a href="#참고-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">

```java
Unable to find CDP implementation matching 139
Unable to find version of CDP to use for 139.0.7258.155. You may need to include a dependency on a specific version of the CDP using something similar to `org.seleniumhq.selenium:selenium-devtools-v86:4.25.0` where the version ("v86") matches the version of the chromium-based browser you're using and the version number of the artifact is the same as Selenium's.
```
코드 2-3. 콘솔창 결과
    
필자가 Selenium을 이용한 테스트를 진행했을 때 콘솔창에서 위와 같은 경고가 떴다. 위 경고는 현재 프로젝트에서 사용하는 Selenium 라이브러리 버전과, 이를 실행시키는 디바이스에 설치된 Chromium 기반 크롬 브라우저의 버전이 서로 호환되지 않아서 발생하는 경고이다. Selenium에서는 CDP(Chrome Devtools Protocol)이라는 특정 프로토콜을 이용하여 크롬 브라우저와 통신하고 제어한다고 한다. 이 때 브라우저와의 원활한 통신을 위해 브라우저 버전과 호환되는 devtools 모듈을 사용하는데, 이 devtools 버전과 크롬 브라우저 간 호환이 되지 않아 위와 같은 경고가 뜬 것이다. 
    
이를 해결하려면 build.gradle 파일에 다음과 같은 의존성을 추가한다. 
    
```java
dependencies {
    // 시스템에 설정된 크롬 브라우저 버전과 셀레니움 라이브러리 버전 간 호환을 위함.
    // 'org.seleniumhq.selenium:selenium-devtools-v<BROWSER_MAJOR_VERSION>:<SELENIUM_VERSION>' 형식
    testImplementation 'org.seleniumhq.selenium:selenium-devtools-v139:4.35.0'
}
```
코드 2-4. build.gradle
    
`<BROWSER_MAJOR_VERSION>` 에는 경고 메시지로 뜬 `Unable to find version of CDP to use for 139.0.7258.155.` 에서 맨 앞 숫자인 `139` 와 같은 숫자를 대입하면 된다. 그리고 그 뒤에는 해당 브라우저 버전에 맞는 Selenium 버전을 명시하면 된다. 이는 [maven repository](https://mvnrepository.com/search?q=selenium+devtools) 사이트에서 `selenium devtools` 라 검색하면 나오는 무수히 많은 브라우저 버전 중에서 찾으면 나오는 내용이니 참고. 
</div>
</details>

# 개인적으로 느낀 Selenium 사용의 어려운 점

그런데, REST API를 구성할 때에는 그 HTTP Method가 항상 GET만 있는 것은 아니다. POST, DELETE등으로 해야할 때도 있다. 그런데 Selenium에서는 기본적으로는 GET만 지원하며, POST 등의 다른 방식들도 지원하기는 하지만 조금 다른 방법으로 사용해야 한다. POST 등 GET 이외의 HTTP Method를 이용하여 Selenium의 웹 브라우저에서 HTTP 요청을 하고 그 응답을 받고자 한다면 `org.openqa.selenium.remote.http` 패키지에 있는 클래스들을 이용해야 한다. 주로 다음이 쓰일 수 있다. 

- HttpClient
- HttpMethod
- HttpRequest
- HttpResponse

HTTP 요청을 위한 객체 생성을 다음과 같은 방식으로 진행한다고 한다.

```java
HttpRequest request = new HttpRequest(<HTTP_method>,<API>);
```

코드 3-1. References [4]에서 발췌

여기서 첫 번째 인자(HTTP_method)에는 같은 패키지에 있는 HttpMethod 클래스의 상수인 GET, POST, DELETE 들의 상수들 중 하나를 이용하면 된다. 두 번째 인자에는 문자열 타입으로 호출하고자 하는 URL을 넣으면 된다. 이렇게 생성된 request 객체의 `setAttribute()`, `setContent()` , `setHeader()` 등의 메서드들을 이용하여 요청 헤더, 요청 바디 등에 데이터를 추가로 넣을 수 있다. 

그 후, HTTP 요청을 하기 위해 필요한 HttpClient 객체를 다음과 같이 생성한다.

```java
import java.net.URL;
// ...

URL url = new URL(baseUrl);
HttpClient client = HttpClient.Factory.createDefault().createClient(url);
```

코드 3-2. References [4]에서 발췌

위와 같이 생성한 HttpClient 객체를 이용하여 다음과 같이 HTTP 요청 후 응답을 받을 수 있다.

```java
HttpResponse response = client.execute(request);
```

코드 3-3. References [4]에서 발췌

이제 이 응답 객체를 이용하여 테스트 검증에 사용할 수 있게 된다. 

이와 같이 Selenium을 이용하여 GET 이외의 다른 HTTP Method를 사용하려면 `webDriver.get()` 방식에 비해 조금은 복잡한 방식을 이용해야 한다. 필자도 사실 위와 같은 방식을 이용하여 GET 이외의 HTTP Method를 가지는 API에 대해서도 테스트를 하려고 했다. 그런데 문제는, 예를 들어 POST로 요청을 하고, 응답으로 쿠키까지 응답하는 API에 대해서는 그 쿠키까지 추출할 수 없었다는 것이다. 앞서 본 `webDriver.manage().getCookies()` 로도 되질 않는 것이었다. 그래서 위에서 보인 객체들에 쿠키와 관련된 메서드들은 없는지 찾아봤으나 발견하지 못했다. 게다가 사실 위 방법도 한 블로거 덕분에 발견한 것이지, 응답으로부터 쿠키를 추출하는 기능에 대한 내용을 Selenium 공식 문서나 Javadoc에서 찾을 수 없었다. 물론 필자가 못 찾은 것이지 문서 어딘가에 있을지도 모른다. 하지만 의외로 Selenium에서 제공하는 document나 Javadoc이 생각만큼 그리 친절하진 않은 것 같다는 느낌을 받았다(Javadoc은 훨씬 심한 상태였던게, 대부분의 메서드들에 주석 자체가 안 달린 경우가 많아 해당 기능이 뭘 하는 건지, 처음보는 타입의 인자에는 어떤 값을 어떻게 넣어야 하는지를 파악하기가 매우 어려웠다. 이는 References [5]를 참조하면 알 수 있을 것이다). 그래서 어떤 기능을 찾으려고 해도 다른 라이브러리 또는 프레임워크들에 비해서 찾기 어려웠다. 

아무튼 끝끝내 위와 같은 방식으로부터 쿠키를 얻는 방법을 알아내지 못해 위 방법은 쓸 수 없었다. IDE에서 제공하는 자동 완성 기능을 이용하여 코드를 이리저리 만져봐도 알아내지 못했다. 그래서 어쩔 수 없이 컨트롤러의 Method를 GET 방식으로 바꿔 테스트를 진행했었다. 

문제는 또 있었다. JWT의 경우, HTTP 응답 객체의 body 또는 쿠키에 있는 JWT값을 추출하고 이를 parsing했어야 했는데, 이를 위해선 HTTP 응답 객체에 담긴 정보들을 추출하는 방법을 알아야 했다. 그러나 애초에 기존의 spring boot starter test이든, selenium이든 둘 모두 이를 가능하게 하는 방법을 몰라 곤란에 처해지기도 하였다. 다행히도 Spring boot starter test 내에 그러한 기능이 있다는 것을 뒤늦게 알게 되어 Selenium 없이도 해당 테스트가 가능해졌다. 

# 번외: Spring boot test tool을 이용하여 HTTP 응답 객체 내 정보들을 추출하는 법

여태까지 필자는 몰랐지만, 사실 MockMvc 객체의 `andReturn()` 메서드를 이용하면 그로부터 MvcResult 객체를 반환받을 수 있는데, 이를 통해 HTTP Response 정보를 추출할 수 있었다는 것이다. MvcResult 객체의 `getResponse()` 메서드 호출 시 MockHttpServletResponse 객체를 얻을 수 있다. 이로부터 응답 객체에 포함된 쿠키, body 내용 등을 추출할 수 있었다. 

필자가 라이브러리에 구현한 JWT 기능에 대한 테스트가 필요했었다. 테스트용 프로젝트 내 메인 패키지 쪽에서는 REST API의 응답 body에 access 및 refresh token을 담아 응답하도록 구성하였다. 필자가 구현한 JWT 기능을 테스트하려면 이 Response body로부터 token들을 추출한 후, 이를 파싱하여 JWT 안에 담긴 내용들을 확인해야 했다. 이럴 때 `andReturn()` 을 이용하여 응답 객체 내 문자열 형태의 토큰들을 얻을 수 있었다. 

```java
@SpringBootTest
@AutoConfigureMockMvc
@Slf4j
class JwtAuthControllerTest {
    
    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private ObjectMapper objectMapper;
    
    // 생략...
    
    @Test
    @DisplayName("""
       POST /test/auth로 로그인 요청 성공 시 Access & Refresh Token을 응답하는지 테스트.
    """)
    void getTokensByLoginTest() throws Exception {

        final String uri = "/test/auth";
        MemberLogin mockMemberLogin = MemberLogin.builder()
            .username("Kimquel")
            .password("kimquel1234")
            .build();

        MvcResult mvcResult = mockMvc.perform(post(uri)
            .content(objectMapper.writeValueAsString(mockMemberLogin))
            .contentType(MediaType.APPLICATION_JSON)
        )
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.message")
                .value("로그인 성공"))
            // 그 외 결과값 검증 코드들...
            .andReturn();

        // JWT 토큰 여부 및 토큰 세부 정보 검증.
        // Access Token 검증.
        String accessToken = JsonPath.read(
            mvcResult.getResponse().getContentAsString(),
            "$.data.tokenDto.accessToken"
        );
        tokenTest(accessToken, mockMemberLogin);

        // Refresh Token 검증
        String refreshToken = JsonPath.read(
            mvcResult.getResponse().getContentAsString(),
            "$.data.tokenDto.refreshToken"
        );
        tokenTest(refreshToken, mockMemberLogin);

        assertThat(accessToken).isNotEqualTo(refreshToken);

    }

    private void tokenTest(String token, MemberLogin mockMemberLogin) {

        Claims claimsFromToken = jwtProvider.extractClaims(token);
        Map<String, Object> jwtHeader = getJwtHeader(token);
        List<String> roles = (List<String>) claimsFromToken.get("roles");

        log.info(claimsFromToken.keySet().toString());
        log.info(jwtHeader.toString());
        log.info(roles.toString());

        assertThat(claimsFromToken.getIssuer())
            .isEqualTo(jwtProperties.getIssuer());
        assertThat(jwtHeader.get("typ"))
            .isEqualTo("JWT");
        assertThat(claimsFromToken.getSubject())
            .isEqualTo(mockMemberLogin.getUsername());
        assertThat(roles.getFirst().startsWith("ROLE_")).isTrue();

    }

    private Map<String, Object> getJwtHeader(String token) {

        SecretKey secretKey = Keys.hmacShaKeyFor(jwtProperties.getSecretKey()
            .getBytes(StandardCharsets.UTF_8)
        );

        Jws<Claims> jws = Jwts.parser()
            .verifyWith(secretKey)
            .build()
            .parseSignedClaims(token);
        return jws.getHeader();
    }

}
```

코드 4-1. JwtAuthControllerTest.java

응답 바디 내 전체 내용들을 `mvcResult.getResponse().getContentAsString()` 메서드 호출로 문자열 형태로 가져온다. 그런데 응답 바디는 JSON 형식으로 구조화되어 있기에 그 중 특정 데이터를 추출하기 위해 `com.jayway.jsonpath.JsonPath` 를 이용하였다. 이 역시 spring boot starter test 의존성 내에 포함되어 있는 기능이다. `JsonPath.read()` 메서드의 첫 번째 인자로 `mvcResult.getResponse().getContentAsString()` 이라는 JSON 형태의 파싱 대상을 넣고, 두 번째 인자에는 첫 번째 인자로 들어온 응답 바디 중 추출하고자 하는 내용을 Json Path(JSON의 최상위 요소를 나타내는 “$” 등의 문법들을 이용하여 JSON 내 특정 프로퍼티를 지칭하기 위한 표현식을 JSON Path라 한다)로 넣으면 된다. 그러면 응답 바디 내 원하는 특정 데이터만을 가져올 수 있게 된다. 

필자는 응답 바디에 있는 문자열 형태의 JWT를 추출한 뒤, 이를 jjwt 라이브러리의 기능들을 이용하여 유저 네임, role 등 여러 정보들을 추출하여 의도대로 JWT가 encoding 되었는지를 테스트하는 용도로 사용하였다. 

# 글을 마치며…

사실 Selenium을 테스트, 그 중에서도 제한적으로만 사용했기에 이 글에서도 거의 표면적인 것만 다뤘다. 그럼에도 Selenium을 통해 다양한 프로그래밍 언어에서 UI test를 비롯한 웹 브라우저 기반의 테스트도 할 수 있고, 크롤링에도 활용할 수 있는 등, 다양하게 활용할 수 있다는 점에서 잠재적으로 유용한 프레임워크라 생각되었다. 이러한 범용성 때문에 앞으로도 필요하면 사용할 수도 있을 것 같아 기록을 위해 이렇게 글로 작성해보았다. 

---

References

[1] [테스트 자동화 도구 비교 : Selenium, Appium, Cypress, JUnit](https://iam-joy.tistory.com/33)

[2] [Selenium을 이용하여 스프링(Spring) Test하기](https://nesoy.github.io/blog/Selenium)

[3] Selenium 공식 홈페이지

[The Selenium Browser Automation Project](https://www.selenium.dev/documentation/)

[4] Selenium에서도 POST 등 GET 이외의 HTTP Method 사용할 수 있다고 함.

[Invoking HTTP Requests from Selenium using HttpClient](https://medium.com/@dkscodes/invoking-http-requests-from-selenium-using-httpclient-85c5e3a5a16f)

[5] Selenium Javadoc

[org.openqa.selenium.remote.http package summary - selenium-http 4.28.1 javadoc](https://www.javadoc.io/doc/org.seleniumhq.selenium/selenium-http/latest/org/openqa/selenium/remote/http/package-summary.html)

[6] Selenium, Wikipedia

[Selenium (software)](https://en.wikipedia.org/wiki/Selenium_(software))

[7] 참고) Selenium의 특징, 단점 및 Selenium과 비슷한 Playwright에 대한 글

[02화 Playwright 시작하기](https://brunch.co.kr/@jameshjkang/82)

[8] 참고) 브라우저 기반 테스트 자동화 도구 Selenium VS Playwright

[Playwright vs Selenium in 2025: Performance & Features Compared](https://research.aimultiple.com/playwright-vs-selenium/)

[9] [https://github.com/SeleniumHQ/selenium/issues/3845](https://github.com/SeleniumHQ/selenium/issues/3845)

[10] seleinum headless 및 chrome options

[셀레니움 최적화를 위한 chrome_options](https://coding-shop.tistory.com/297)

[11] seleinum headless 및 chrome options

[Selenium을 위한 Chrome 브라우저 설정 옵션 완벽 가이드](https://with-kwang.tistory.com/82)

[12] seleinum headless 및 chrome options

[나만의 웹 크롤러 만들기(7): 창없는 크롬으로 크롤링하기 - Beomi's Tech blog](https://beomi.github.io/2017/09/28/HowToMakeWebCrawler-Headless-Chrome/)

[13] 샌드박스

[샌드박스 (컴퓨터 보안)](https://ko.wikipedia.org/wiki/%EC%83%8C%EB%93%9C%EB%B0%95%EC%8A%A4_(%EC%BB%B4%ED%93%A8%ED%84%B0_%EB%B3%B4%EC%95%88))