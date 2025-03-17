---
title: "[Spring Boot] 테스트 코드 작성"
category: "Spring"
tag: ["Spring", "Spring boot", "Test", "Unit Test", "Integration Test", "JUnit", "AssertJ", "Slice Test"]
---

# 테스트 코드 개요

필자는 테스트 코드에 대해 들어본 적은 있어도 자바 또는 스프링 부트 기반 테스트 코드에 대해서는 접해본 적이 여태껏 없었다. 그래서 그동안은 스프링 부트로 개발한 웹 애플리케이션 서버가 제대로 동작하는지 확인하기 위해 Swagger에서 각각의 API들에 대해 직접 입력값을 넣어보고 실행시켜 결과값을 보면서 내가 원하는 본래 결과와 똑같이 나왔는지 직접 확인해보는 과정을 거쳤었다. 뭐, 이렇게 해서도 내가 만든 서버에 대한 테스트를 할 수 있지만, 여러 가지 불편한 점들이 있었다. 

- 코드에 대해 추가 또는 변경되는 점들이 있을 때, 이러한 점들이 혹여나 기존의 기능들을 잘못 건드려 문제가 생기지 않는지 확인하기 위한 테스트를 할 때 매번 똑같은 입력값들을 직접 넣어줘야 하는 번거로움이 있다.
    - 앱을 만들다보면 항상 예상치 못한 버그를 만나서 이를 수정해야 하거나, 이전에는 예상치 못했던 새로운 기능 추가 또는 리팩토링을 해야할 때가 있는데, 이럴 때마다 매번 일일이 입력값을 만들어 넣어주면서 테스트를 하는 것이 생각보다 너무나도 번거로운 일이다. 즉, 반복적인 테스트가 사실상 힘들다.
- 실제 서비스할 목적의 웹 앱을 만드는 것이라면 미리 웹 환경에서 테스트 하여 문제점을 미리 파악하여 해결할 수는 없을까, 에 대한 개인적인 아쉬움이 있었다.
- Swagger를 이용하여 직접 테스트해보는 방법은 자신이 만든 API가 의도대로 동작하는지에 대해서만 확인해볼 수 있다. 서비스 계층의 특정 메서드가 제대로 작동하기에 정상적인 결과가 나오는 것인지 아니면 원래 취약점이 있는데 우연히 운이 좋아 잘 작동한 것인지 이러한 세부적인 것들에 대한 테스트를 할 수가 없다. 후자라면 사실상 모래로 성을 높이 쌓아올린 것처럼 불안한 상황이다. 작동만 되면 끝 아닌가 싶을텐데, 만약 이와 관련된 새로운 기능을 추가하거나 기존 기능을 변경해야 할 때, 또는 리팩토링할 때에도 이 기능이 이전처럼 정상적으로 동작한다는 보장이 없다. 그러한 상황에서 만약 갑자기 비정상적인 동작을 보인다면 디버깅이 더더욱 어려울 것이다. 그래서 API와 가장 가까운 계층인 컨트롤러 계층 뿐만 아니라 서비스 계층 등 API와 상대적으로 먼 계층의 세부적인 기능들에 대해서도 테스트가 필요한데, Swagger만으로는 이러한 세부적인 테스트를 할 수 없다.
- 기존에 만든 기능들에 대한 테스트 코드 같은 게 없어서 리팩토링에 대한 두려움이 있었다. 리팩토링 후에 갑자기 기존에는 작동하던 기능들이 제 기능을 못하고 버그 또는 에러를 발생시키면 이 원인을 잡는데에 시간이 걸릴 것이다. 하지만 만약 미리 기존에 모든 기능들에 대한 각각의 테스트 코드들이 마련되어 있다면 리팩토링 후 테스트 코드를 통해 문제가 있는지 더 간편하고 빠르게 확인할 수 있다.

그래서 이러한 이유들로 인해 결국 자바 또는 스프링 부트 기반의 테스트 코드에 대해 공부하게 되었고, 이 글에서는 이러한 내용들을 다룰 것이다. 

## 테스트 코드 개념 및 장단점

테스트 코드란, 개발자가 작성한 코드가 그 의도대로 잘 동작하는지, 예상치 못한 다른 문제점은 없는지 테스트하기 위해 작성되는 코드를 말한다. 

테스트 코드를 작성할 때의 장점들로는 다음이 있다. 이들은 장점이자 테스트 코드 작성의 이유가 되기도 한다. 

- 테스트 코드란 것 자체가 테스트를 코드로 작성하여 이를 언제든지 실행시켜도 그 결과가 바로 나올 수 있게끔 한다는 것인데, 즉, 테스트의 자동화가 이루어진다는 의미이기도 하다. 이로 인해 사람이 일일히 번거롭게 테스트를 진행하지 않아도 되며, 사람이 테스트를 수행하면 실수를 할 수 있지만, 테스트 코드로 작성해놓으면 실수 없이 테스트를 진행할 수 있다. 또한, 언제 실행을 하든간에 반복적이고 일관적인 테스트 결과를 확인할 수 있어 편리하고 빠르면서도 테스트에 대해 신뢰할 수 있다는 장점도 있다.
- 내가 테스트하고자 하는 기능별로 테스트할 수 있다. 컨트롤러 계층 뿐만 아니라 서비스 계층, 데이터 접근 계층에 대해서도 테스트할 수 있다. 이렇게 범위를 한정하여 테스트할 수도 있다는 사실은 미처 예상치 못한 문제점을 미리 발견할 수도 있다는 말도 된다.
- 실제 고객들을 위한 상품으로 출시하기 전에 개발 과정에서 미리 테스트를 진행함으로써 문제점들을 미리 발견할 수 있다. 테스트라는 게 없었다면 각종 에러 코드가 고객들의 눈에 보였을지도 모른다. 이 경우 고객들이 개인 또는 자사의 제품에 대한 신뢰를 잃게 되는 계기가 될 수도 있다. 즉, 테스트 코드의 작성은 그 자체로 애플리케이션의 안정성을 보장하는 역할도 한다.
- 리팩토링 시 예상치 못한 문제를 미리 파악할 수 있다. 리팩토링, 즉 코드를 변경한다는 것은 다른 코드에도 영향을 줄 수도 있는 행위이다. 이 때 테스트 코드를 미리 작성한 경우, 다른 코드에 영향을 끼치지 않았는지 또는 기능의 변경없이 정말 리팩토링을 문제없이 잘했는지 빠르게 확인해볼 수 있다.
- 그 자체로 버그 또는 에러 발생 시 추측할 수 있는 모든 원인들의 “소거법”이 된다. 만약 어떤 기능을 개발했는데 여기서 원인 모를 버그가 발생했다고 가정해보자. 그런데 만약 추측해볼 수 있는 원인들이 A, B, C, D가 있다고 할 때, 이미 A, B에 대한 테스트 코드가 작성되어 있으면 개발자는 자연스레 버그의 원인을 C, D 중 하나로 그 범위를 좁힐 수 있다. 이러한 특성을 이용하면, 개발자가 개발한 기능에 기존에는 알려지지 않았던 버그를 발견하여 이를 고친 뒤 이를 테스트 코드로도 작성해놓으면 그 자체로 “이러한 버그도 있었고, 우린 이런 식으로 해결했어요” 식의 일종의 트러블슈팅으로서의 기능으로도 활용할 수 있을 것이다.
- 명세 문서로서의 기능으로도 활용할 수 있다. 협업 시 보통 다른 개발자가 개발한 부분이 어떤 원리로 동작하는 것인지에 대해 한 눈에 이해하기 쉽진 않을 것이다. 이 때 테스트 코드가 이미 작성되어 있다면 이 테스트 코드와 비교하여 해당 기능이 어떻게 작동하는지에 대해 파악할 수 있을 것이다.

하지만 모든 것에는 항상 장점만 있을 순 없듯, 테스트 코드에도 단점이 있을 것이다. 

- 메인 애플리케이션 개발 외에도 테스트 코드도 별도로 작성해야 하니 그에 대한 시간이 걸리는 것도 사실이다. 조사해보니, 몇몇 IT 회사들에서는 데드라인을 맞추기 위해 테스트 코드를 작성할 별도의 시간이 별로 없어 이를 생략하는 경우도 더러 있는 것 같았다.
- 개발자 입장에서는 개발과 더불어 테스트까지 해야 하니 부담이 갈 것이다.

그럼에도, 앞서 언급한 장점들이 많기도 하고, 강력하기도 해서 필자 개인적으로는 될 수 있으면 테스트 코드도 작성하는 것이 더 좋을 거란 생각이 든다. 

## 테스트의 종류

테스트에도 여러 종류가 있는데, 이는 어떤 것을 기준으로 하느냐에 따라 달라진다. 

먼저 테스트의 자동화 여부에 따른 구분이다. 이는 크게 수동 테스트와 자동 테스트로 구분된다. 

- 수동 테스트(Manual Test) : 사람이 일일이 수행하는 테스트. 필자가 앞서 말한 “Swagger를 통한 테스트”가 그 예일 것이다. 수동 테스트 중 탐색적 테스트(Exploratory Test)가 있는데, 이는 제작한 앱을 실행시켜 여러 기능들을 일일히 실행함으로써 문제점이 없는지 테스트하는 방법이라고 한다. 이러한 수동 테스트는 그 어떠한 도구를 사용하지 않고 사람 손으로 일일히 수행하는 방식이다. 이로 인해 일부 테스트를 놓쳐 테스트의 정확성이 떨어질 수 있고, 특히 앱의 규모가 클수록 혼자서 테스트를 수행하기 힘들 것이다. 설령 테스터들을 모집한다 해도 그 자체만으로도 큰 비용이 들 것이다.
- 자동 테스트(Automated Test) : 테스트 자동화 도구를 이용하여 테스트를 하는 방법. 이 글에서도 주로 다룰 방식이다. 테스트 자동화 도구들의 도움으로 테스트 코드를 작성하고 실행하는 방식이다. 이로 인해 빠른 테스트가 가능하고, 수동 테스트에 비해 테스트에 대한 정확성이 높다.

또 다른 기준으로, 테스트 대상 범위에 대한 분류가 있다. 크게 단위 테스트와 통합 테스트가 있다. 

- 단위 테스트 (Unit Test) : 애플리케이션을 구성하는 개별 모듈을 독립적으로 테스트하는 방식. 즉, 테스트 대상의 범위라는 측면에서 가장 작은 단위에 대해 수행하는 테스트이다. 일반적으로는 메서드 단위로 테스트를 수행하며 해당 메서드가 원래 의도한 대로 그 결과값이 나오는지 테스트하는 방식이다. 테스트 대상의 범위가 작다보니 테스트 비용도 적게 들고, 빠른 피드백을 할 수 있다. 단위 테스트 특성 상 외부의 모든 의존성들을 제외하여 독립적인 환경에서 테스트를 수행한다. 여기서의 의존성이라 함은 다른 외부 모듈 뿐만 아니라 외부의 데이터베이스, 네트워크 등도 포함된다. 이로 인해 이후 여러 모듈들을 연결하여 전체적으로 테스트할 때 어떤 모듈에서 문제가 발생하는지도 미리 파악할 수 있다.
- 통합 테스트 (Intergration Test) : 애플리케이션을 구성하는 여러 모듈들을 결합하여 실행했을 때 애플리케이션의 전체적인 흐름에서 의도대로 동작하는지 테스트하는 방식. 단위 테스트만으로는 둘 이상의 모듈들을 결합했을 때 발생할 수 있는 모든 문제점들을 파악하긴 힘들다. 각 모듈 자체에는 문제가 없을지라도 이 모듈들을 결합했을 때 문제가 발생할 수 있다. 따라서 통합 테스트에서는 여러 모듈들이 잘 연결되어 의도대로 동작하는지 테스트한다. 뿐만 아니라 단위 테스트와는 달리 외부의 데이터베이스, 네트워크 등의 여러 외부 요인들과도 연동하여 테스트를 진행한다. 따라서 애플리케이션 자체가 잘 작동하는지에 대해서도 테스트하는 것이나 다름없다. 단위 테스트에 비해선 다른 모듈들도 동작해야 하기에 상대적으로 테스트 비용(금전적, 시간, 인력 등)이 크다는 단점도 있다.

이 외에도 수많은 테스트 방식이 있다고 한다. 

## 좋은 테스트 코드 작성하기

좋은 테스트 코드를 작성하는 방법에도 여러 가지가 있다고 한다. 그 중 Given-When-Then 패턴과 FIRST 규칙에 대해 살펴보겠다. 

### Given-When-Then 패턴

이 패턴은 Given, When, Then이라는 각각의 단계를 거쳐 테스트 코드를 작성하는 방식이다. 

- Given: 테스트 수행 전 테스트에 필요한 환경 설정 및 데이터를 생성하는 단계. “만약 이러이러한 상황이 주어졌을 때”라는 문구와 어울리는 단계이다.
- When: 테스트의 목적을 보여주는 단계이며, 실제 테스트하고자 하는 코드가 포함된 단계이다. 테스트 코드로 인한 결과값을 얻는 단계이다. “만약 이러하다면”이라는 문구와 어울리는 단계이다.
- Then: When 단계에서 얻은 테스트 결과값을 검증하는 단계이다. 테스트 결과값 외에도 검증할 부분들이 있다면 이 단계에서 수행할 수 있다. “이러할 것이다.”라는 문구와 어울리는 단계이다.

한편 이 패턴은 제법 코드가 길어지기에 인수 테스트(Acceptance Test)와 같이 많은 환경들이 있는 테스트에 적합하다고 한다. 단위 테스트의 경우에는 코드가 불필요하게 길어질 수 있으나 좀 더 체계적이고 명확한 테스트 코드 작성을 할 수 있어 단위 테스트에 적용해도 좋다는 의견이 있다[1]. 

### F.I.R.S.T 규칙

F.I.R.S.T 규칙은 좋은 테스트 코드를 작성하기 위한 규칙 중 하나로, 주로 단위 테스트에 적용 가능한 규칙이다. 

- Fast (빠르게) : 테스트는 빠르게 수행되어야 한다. 테스트가 느려지면 문제를 파악하고 해결하는 과정도 덩달아 느려지기 때문이다. 빠른 테스트를 위해선…
    - 목적을 단순하게 설정한다.
    - 외부 환경을 사용하지 않는다.
- Isolated (독립적으로) : 하나의 테스트 코드는 하나의 목적, 대상에 대해서만 수행해야 한다. 다른 테스트 코드 또는 외부 환경과도 상호작용했을 때 외부 환경이 달라지면 테스트 코드의 결과도 달라져 일관성도 지킬 수 없을 것이다.
- Repeatable (반복 가능한) : 테스트는 어떤 환경에서도 반복 가능해야 한다. “Isolated”처럼 외부 환경이 어떻든 간에 상관없이 테스트 수행이 반복 가능해야 한다.
- Self-Validating (자가 검증) : 테스트는 그 자체만으로 테스트 검증이 완료되어야 한다. 테스트의 결과값과 기댓값의 비교도 개발자가 직접 하는 것이 아닌 테스트 코드 그 자체에서 수행하도록 하여 테스트 성공 여부도 확인 가능해야 한다.
- Timely (적시에) : 주로 TDD(Test Driven Develepment, 테스트 주도 개발) 방식의 개발에서 적용되는 규칙으로, 테스트 코드는 애플리케이션 코드 구현 전에 작성해야한다는 규칙이다. 테스트 코드 작성을 늦게 한다는 것은 테스트 코드를 통해 문제점들을 발견하고 이를 해결하는 과정 자체도 늦어진다는 의미로, 문제점을 발견하고 해결하는 시간적 비용도 그만큼 들게 된다. 다만 이 규칙은 TDD 방식으로 프로젝트를 진행하지 않는다면 제외해도 된다고 한다.

# 테스트 코드 작성

스프링 부트에서도 테스트 자동화 도구들을 제공한다. 다음의 의존성을 build.gradle에 추가하면 테스트 자동화 도구들을 사용할 수 있게 된다.

```java
testImplementation 'org.springframework.boot:spring-boot-starter-test'
```

“[spring initializr](https://start.spring.io/)” 등을 이용하여 스프링 부트 프로젝트 생성 시에는 해당 의존성이 기본으로 추가될 것이다. 

이 의존성에는 여러 가지 테스트 관련 도구들을 제공한다고 한다. 

| 라이브러리 | 설명 |
| --- | --- |
| JUnit | 자바용 단위 테스트 프레임워크. 테스를 위한 각종 어노테이션 지원 및 통합 테스트도 지원. |
| Spring Test & Spring Boot Test | 스프링 부트 애플리케이션용 유틸리티 및 통합 테스트 지원 |
| AssertJ | 검증문(assert) 관련 라이브러리 |
| Hamcrest | Matcher 라이브러리. 표현식을 이해하기 쉽게 하기 위해 사용됨 |
| Mockito | 테스트 용도로 사용할 가짜(Mock) 객체를 생성, 관리, 검증할 수 있도록 지원하는 테스트 프레임워크 |
| JSONassert | JSON용 assert 라이브러리 |
| JsonPath | JSON 데이터에서 특정 데이터 조회, 검색을 위한 라이브러리 |

여기서는 자바 언어로 작성한 애플리케이션 테스트를 위해 사용되는 기본적인 JUnit 및 AssertJ에 대해 먼저 알아본 뒤, 그 후에 본격적으로 스프링 부트 애플리케이션에 대한 테스트 코드를 작성하는 것에 대해 살펴보도록 하겠다.

## JUnit & AsssertJ를 이용한 간단한 단위 테스트 작성해보기

JUnit은 자바 진영에서 제공하는 단위 테스트 프레임워크이다. 꼭 스프링 부트를 사용하지 않아도 자바 언어로 작성한 모든 애플리케이션에 이 프레임워크를 사용하여 테스트 코드를 작성할 수 있다고 한다. 

다음은 가장 기초적인 JUnit을 사용한 테스트 코드이다.

```java
package com.jerocaller.TestStudy.simple;

import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;

public class JUnitTest {

    @Test
    @DisplayName("덧셈 테스트")
    public void addTest() {

        int a = 1;
        int b = 2;
        int sum = 3;

        Assertions.assertEquals(sum, a + b);

    }

    /*
    @Test
    @DisplayName("1 + 1 = 3이라고 하면 테스트 실패하는지 확인.")
    public void addFailedTest() {

        int a = 1;
        int b = 1;
        int sum = 3;

        Assertions.assertEquals(sum, a + b);
    }

     */
}

```

코드 1-1. JUnitTest.java

메서드에 테스트할 코드를 작성하는 방식이며, 해당 메서드에는 테스트 코드임을 알리는 `@Test` 어노테이션을 적용한다. `@DisplaynaName("...")` 어노테이션은 해당 테스트의 목적을 설명하는 내용을 기술하기 위한 용도로 사용된다. 

위 코드에서는 단순히 두 숫자를 더한 결과가 예상한 결과인지를 확인하기 위한 테스트 코드를 작성하였다. 예상값과 실제값이 같은지 검증하기 위해 여기서는 `Assertions.assertEquals()` 를 이용하여 assert문을 작성하였다. 

이 코드를 실행해보겠다. 현재 필자는 IntelliJ Community Edition이란 IDE를 사용하여 코드를 작성하고 실행하므로 이 IDE를 기준으로 하겠다. 

> 앞으로도 별 다른 문제가 없으면 앞으로 작성할 다른 글들에서도 IntelliJ Community Edition을 기준으로 글을 작성할 예정이다.
> 

테스트 코드는 src/test/java 폴더 내에 생성된 루트 디렉토리 내부에 작성한다. 

![사진 1-1. IntelliJ에서 테스트 코드 실행 방법](/images/2025-03-17/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1/1.png)

사진 1-1. IntelliJ에서 테스트 코드 실행 방법

위 사진처럼 프로젝트 구조를 보여주는 왼쪽 사이드바에서 테스트 코드가 작성된 파일을 마우스 우클릭 한 후, “(파일명) 실행” 버튼을 클릭하면 된다. 그러면 화면 아래에 콘솔창이 뜨면서 테스트가 실행될 것이며, 잠시 뒤 그 결과가 나온다.

![사진 1-2. 테스트 실행 결과. 테스트를 통과하였다. ](/images/2025-03-17/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1/2.png)

사진 1-2. 테스트 실행 결과. 테스트를 통과하였다. 

참고로 위 사진에서 체크 표시(🚫 아이콘 왼쪽에 있는 것)를 눌러 활성화시키면 세부적인 테스트 상황을 볼 수 있다. 

위 사진에서는 테스트 실행 후 모든 테스트가 통과되었다.

이번에는 앞선 코드 1-1에서 주석 처리된 다른 테스트 코드를 주석 해제한 후 다시 실행해보자. 해당 메서드에는 테스트 실패 시 어떤 일이 벌어지는지 확인하기 위해 일부러 테스트에 실패하도록 코드를 작성한 상태이다. 

![사진 1-3. 테스트에 실패한 경우의 콘솔창 모습. ](/images/2025-03-17/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1/3.png)

사진 1-3. 테스트에 실패한 경우의 콘솔창 모습. 

위 사진과 같이 테스트에 실패한 특정 메서드들에 대해서만 테스트 실패 표시가 뜨고, 실패한 테스트의 내역을 살펴보면 어디서 무엇 때문에 테스트에 실패했는지까지 파악할 수 있다. 

한 편, AssertJ에서는 보다 다양하고 가독성 좋은 assert문을 지원한다. 앞서 JUnit만 사용했을 때 assert문을 다음과 같이 사용하였는데,

```java
Assertions.assertEquals(sum, a + b);
```

이 경우 어느 인자가 예측값이고 실제값인지 구분하기 어렵다. 하지만 AssertJ를 사용하면 다음과 같이 두 값을 좀 더 구분할 수 있게 된다. 

```java
assertThat(a + b).isEqualTo(sum);
```

AssertJ에서 제공하는 대표적인 Assert문에는 다음과 같이 존재한다. 

| 메서드 | 설명 |
| --- | --- |
| isEqualTo(X) | X와 그 값이 같은가 |
| isNotEqualTo(X) | X 값과 다른가 |
| contains(X) | X가 포함되어 있는가 |
| doesNotContain(X) | X가 포함되지 않았는가 |
| startsWith(X) | 문자열의 시작이 X로 시작하는가 |
| endsWith(X) | 문자열의 끝이 X로 끝나는가 |
| isEmpty() | 빈 값 검증 |
| isNotEmpty() | 빈 값이 아닌지 검증 |
| isPositive() | 검증하고자 하는 값이 양수인지 검증 |
| isNegative() | 검증하고자 하는 값이 음수인지 검증 |
| assertThat(X).isGreaterThan(Y) | X > Y인지 검증 |
| assertThat(X).isLessThan(Y) | X < Y인지 검증 |

다음은 위 assert문들을 각각 사용해본 예시 코드이다.

```java
package com.jerocaller.TestStudy.study;

import org.junit.jupiter.api.Test;

import java.util.ArrayList;
import java.util.Arrays;
import java.util.List;

import static org.assertj.core.api.Assertions.*;
//import org.assertj.core.api.Assertions;

/**
 * AssertJ 라이브러리에서 제공해주는 메서드 확인
 */
public class JUnitAssertJ {

    @Test
    public void isEqualTo() {
        int a = 1;
        int b = 2;
        int sum = 3;

        //Assertions.assertThat()
        assertThat(a + b).isEqualTo(sum);
    }

    @Test
    public void isNotEqualTo() {
        int a = 1;
        int b = 1;
        int sum = 3;

        assertThat(a + b).isNotEqualTo(sum);
    }

    @Test
    public void containsMethod() {
        int target = 1;
        int[] array = {1, 2, 3};

        assertThat(array).contains(target);
    }

    @Test
    public void doesNotContainMethod() {
        int target = 4;
        int[] array = {1, 2, 3};

        assertThat(array).doesNotContain(target);
    }

    @Test
    public void startsWithMethod() {
        String source = "Bearer abc123";
        String target = "Bearer";

        assertThat(source).startsWith(target);
    }

    @Test
    public void endsWithMethod() {
        String source = "안녕하십니까?";
        String target = "?";

        assertThat(source).endsWith(target);
    }

    @Test
    public void isEmptyMethod() {
        int[] array = {};

        assertThat(array).isEmpty();
    }

    @Test
    public void isNotEmptyMethod() {
        List<Integer> original = new ArrayList<>();
        List<Integer> other = new ArrayList<>();
        other.add(1);
        original = other; // 객체의 참조값 대입. 두 변수는 같은 객체를 참조하게 됨.

        assertThat(original).isNotEmpty();
        assertThat(original).isEqualTo(other); // 두 변수는 같은 참조값을 가지는가?
    }

    @Test
    public void isPositiveMethod() {
        int a = 3;

        assertThat(a).isPositive();
    }

    @Test
    public void isNegativeMethod() {
        int a = -1;

        assertThat(a).isNegative();
    }

    @Test
    public void isGreaterThanMethod() {
        int[] source = {3, 2, 1};
        int[] expected = {1, 2, 3};

        int[] actual = bubbleSortAsc(source);
        printIntArray(actual);

        assertThat(actual).isEqualTo(expected);
        assertThat(actual[1]).isGreaterThan(actual[0]);
    }

    @Test
    public void isLessThenMethod() {
        int[] source = {3, 2, 1};
        int[] expected = {1, 2, 3};

        int[] actual = bubbleSortAsc(source);
        printIntArray(actual);

        assertThat(actual).isEqualTo(expected);
        assertThat(actual[1]).isLessThan(actual[2]);
    }

    private int[] bubbleSortAsc(int[] array) {

        int[] copiedArray = Arrays.copyOf(array, array.length);
        for (int i = 0; i < copiedArray.length; i++) {
            int endIndex = copiedArray.length - i;
            for (int j = 0; j < endIndex - 1; j++) {
                if (copiedArray[j] > copiedArray[j+1]) {
                    int temp = copiedArray[j+1];
                    copiedArray[j+1] = copiedArray[j];
                    copiedArray[j] = temp;
                }
            }
        }

        return copiedArray;
    }

    private void printIntArray(int[] array) {

        StringBuilder stringBuilder = new StringBuilder("[");

        for (int i = 0; i < array.length; i++) {
            stringBuilder.append(array[i]);
            if (i != array.length - 1) {
                stringBuilder.append(", ");
            }
        }
        stringBuilder.append("]");

        System.out.println(stringBuilder.toString());

    }

}

```

코드 1-2. JUnitAssertJ.java

참고로 IntelliJ에서 간편하게 “import static”을 이용하여 특정 클래스를 import하려면 다음의 절차를 거치면 된다. 

1. 사용하고자 하는 메서드를 괄호까지 작성해준다. 그 후 커서를 메서드명과 열린 괄호 사이에 위치시킨다. 
    
    ![사진 1-4. ](/images/2025-03-17/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1/4.png)
    
    사진 1-4. 
    
2. 그 후 키보드에서 alt + enter 키를 누른다. 그러면 다음과 같이 메뉴가 뜨는데, “static 메서드 가져오기…”를 클릭한다. 
    
    ![사진 1-5. ](/images/2025-03-17/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1/5.png)
    
    사진 1-5. 
    

1. 만약 여러 개의 패키지들이 존재할 경우 그 중 자신이 사용하고자 하는 것을 클릭한다. 여기서는 "org.assertj.core.api.Assertions”을 사용하므로 이 패키지를 클릭한다. 
    
    ![사진 1-6.](/images/2025-03-17/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1/6.png)
    
    사진 1-6.
    

1. 그러면 다음과 같이 필요한 패키지를 import 함으로써 해당 메서드를 사용할 수 있게 된다. 
    
    ![사진 1-7.](/images/2025-03-17/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%BD%94%EB%93%9C%20%EC%9E%91%EC%84%B1/7.png)
    
    사진 1-7.
    

### JUnit의 생명주기와 관련된 어노테이션들

한 편, JUnit은 생명주기를 가지고 있으며, 이와 관련된 어노테이션들이 존재한다. 먼저 다음의 코드를 보겠다 .

```java
package com.jerocaller.TestStudy.study;

import org.junit.jupiter.api.*;

public class JUnitLifeCycle {

    /**
     * 전체 테스트를 실행하기 전 딱 한 번만 실행된다.
     * DB와 같은 외부 시스템과의 연동과 같은 테스트 준비에 사용된다.
     * static으로 선언해야 함.
     */
    @BeforeAll
    static void beforeAll() {
        System.out.println("=== beforeAll 호출 됨 ===");
    }

    /**
     * 각 테스트 케이스(메서드) 실행 전마다 실행됨.
     * 각 테스트 메서드에서 사용될 객체 초기화, 테스트에 필요한 값 주입 등의
     * 테스트 전에 실행되어야 할 기능들을 여기서 설정할 수 있다.
     */
    @BeforeEach
    public void beforeEach() {
        System.out.println("=== beforeEach 호출 됨 ===");
    }

    /**
     * 모든 테스트 종료 후 딱 한 번 실행됨.
     * DB 연결 종료, 공통적으로 사용하는 자원 해제 등의 마무리 작업에 사용.
     * static으로 선언해야 함.
     */
    @AfterAll
    static void afterAll() {
        System.out.println("=== afterAll 호출 됨 ===");
    }

    /**
     * 각 테스트 케이스가 종료될 때마다 실행됨 .
     * 테스트 후 특정 데이터 삭제 등과 같은 각 테스트 케이스마다의 마무리 작업에 사용.
     */
    @AfterEach
    public void afterEach() {
        System.out.println("=== afterEach 호출 됨 ===");
    }

    @Test
    public void test1() {
        System.out.println("----- Test 1 -----");
    }

    @Test
    public void test2() {
        System.out.println("----- Test 2 -----");
    }

    @Test
    public void test3() {
        System.out.println("----- Test 3 -----");
    }

}

```

코드 2-1. JUnitLifeCycle.java

```java
=== beforeAll 호출 됨 ===
=== beforeEach 호출 됨 ===
----- Test 1 -----
=== afterEach 호출 됨 ===
=== beforeEach 호출 됨 ===
----- Test 2 -----
=== afterEach 호출 됨 ===
=== beforeEach 호출 됨 ===
----- Test 3 -----
=== afterEach 호출 됨 ===
=== afterAll 호출 됨 ===
```

코드 2-2. 코드 2-1 실행 결과

해당 어노테이션들에 대해 간략히 설명하자면 다음과 같다.

| 어노테이션 | 설명 |
| --- | --- |
| `@BeforeAll` | 전체 테스트 시작 전에 이 어노테이션이 적용된 메서드를 호출된다.  |
| `@BeforeEach` | `@Test` 가 적용된 각각의 테스트 메서드들이 실행되기 전에 `@BeforeEach` 어노테이션이 적용된 메서드가 먼저 실행된다.  |
| `@AfterEach` | `@Test` 가 적용된 각각의 테스트 메서드들이 실행된 후 `@AfterEach` 어노테이션이 적용된 메서드가 실행된다.  |
| `@AterAll` | 모든 테스트들이 끝난 후에 해당 어노테이션이 적용된 메서드를 실행시킨다. |

각각의 어노테이션들의 실제 활용 용도는 위 코드 2-2에서 주석으로 달아놨으니 참고하면 되겠다.

## 스프링 부트로 제작한 애플리케이션에 대한 테스트 코드 작성하기

지금까지 자바 언어에서 사용할 수 있는 테스트 도구들에 대해 살펴보았다. 지금부터는 본격적으로 스프링 부트로 제작한 애플리케이션에 대한 테스트 코드 작성에 대해 살펴보겠다. 여기서는 컨트롤러, 서비스, 리포지토리(Spring Data JPA를 사용한다고 가정) 계층 각각에 대해 나눠서 어떻게 테스트 코드를 작성할 수 있는지 살펴보도록 하겠다. 참고로 이와 같이 각 계층(layer)별로 나눠서 계층을 단위로 하여 테스트하는 것을 슬라이스(slice) 테스트라 한다. 

 참고로 여기서 보일 코드에 대한 전체 소스 코드는 다음의 깃허브 리포지토리 사이트에서 확인할 수 있다. 

[spring-boot-study-with-intellij/TestStudy/src at master · JeroCaller/spring-boot-study-with-intellij](https://github.com/JeroCaller/spring-boot-study-with-intellij/tree/master/TestStudy/src)

### 컨트롤러 계층에 대한 테스트

컨트롤러 계층은 클라이언트와의 요청과 응답을 주고받는 웹에 가장 가까운 계층이라 볼 수 있다. 그렇기에 웹과 완전히 독립적인 테스트 환경을 꾸려 테스트를 진행한다는 것은 사실상 불가능할 것이다. 그렇다면 웹 환경에서의 테스트를 위해 애플리케이션을 실제로 서버에 배포해야하는가에 대한 의문이 생길텐데, 스프링부트 측에서 제공하는 테스트 관련 도구들을 이용하면 가짜 웹 환경을 꾸려 테스트를 할 수 있기에 배포 전에 웹과 비슷한 환경에서도 미리 그 기능을 테스트해볼 수 있다.  

여기서는 두 가지 방식에 대해 각각 소개하겠다. 먼저 첫 번째 방식이다. src/main/java 폴더 쪽에 미리 UserController라는 컨트롤러를 만들어 둔 상태에서 작성한 테스트 코드이다. 

```java
// 생략...

import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.AutoConfigureMockMvc;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.http.MediaType;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;
import org.springframework.web.context.WebApplicationContext;

import java.time.LocalDate;
import java.util.ArrayList;
import java.util.List;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

/**
 *
 * <p>
 *     참고 자료) 신선영, “스프링 부트 3 백엔드 개발자 되기 - 자바 편, 2판”, (골든래빗)
 * </p>
 *
 * <p>
 *     메인 패키지에서 제작한 컨트롤러를 테스트하기 위한 코드
 * </p>
 * 
 * <p>에너테이션 설명</p>
 * <p>
 * <code>@SpringBootTest</code> : 메인 애플리케이션 클래스에 부여된
 * <code>@SpringBootApplication</code> 애너테이션을 통해 해당 클래스를 검색하고,
 * 해당 클래스에 포함된 bean 조회 후 이를 토대로 테스트용 애플리케이션 컨텍스트를 생성함. 
 * </p>
 *
 * <p>
 *     <code>@AutoConfigureMockMvc</code> : MockMvc 객체를 자동 생성 및 구성.
 *     MockMvc는 애플리케이션을 실제 서버로 배포하지 않아도 테스트용 MVC 환경을 구성하여
 *     요청, 응답 전송 기능을 제공하는 유틸리티 클래스. 주로 웹과 가장 가까운 영역인
 *     컨트롤러 계층의 클래스를 테스트할 때 사용됨.
 * </p>
 * 
 */
@SpringBootTest
@AutoConfigureMockMvc
class UserControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Autowired
    private WebApplicationContext context;

    @Autowired
    private SiteUsersRepository siteUsersRepository;

    @Autowired
    private ClassTypeRepository classTypeRepository;

    // DB에 저장된 테스트용 데이터들을 기억했다가 삭제하기 위한 용도.
    private final List<SiteUsers> usersForTest = new ArrayList<>();
    private final List<ClassType> classTypesForTest = new ArrayList<>();

    @BeforeEach
    public void mockMvcSetUp() {
        this.mockMvc = MockMvcBuilders.webAppContextSetup(context)
            .build();
    }

    @AfterEach
    public void clean() {
        // 테스트 케이스에 사용된 테스트용 데이터들을 DB로부터 삭제한다.
        usersForTest.forEach(siteUsersRepository::delete);
        classTypesForTest.forEach(classTypeRepository::delete);

        // 기록용 리스트들의 내부를 비운다.
        usersForTest.clear();
        classTypesForTest.clear();
    }

    @DisplayName("getAllUsers : 모든 사용자 조회에 성공한다.")
    @Test
    public void getAllUsers() throws Exception {

        // Given
        // 테스트용 URL 및 사용자 객체를 생성한 후, 해당 사용자 객체를 DB에 저장.
        final String url = "/api/users";
        
        ClassType classType = classTypeRepository.saveAndFlush(
            ClassType.builder()
                .classNumber(1)
                .className("VVIP")
                .build()
        );
        classTypesForTest.add(classType);

        SiteUsers newUser = SiteUsers.builder()
            .memberId(100)
            .signUpDate(LocalDate.now())
            .mileage(100)
            .username("kimquel")
            .averPurchase(120.5)
            .classType(classType)
            .build();
        SiteUsers savedUser = siteUsersRepository.save(newUser);
        usersForTest.add(savedUser);

        // When
        // 주어진 URL을 통해 API 호출하여 회원 조회 시도.
        // mockMvc.perform : 요청을 전송하는 역할.
        // 이 결과로 ResultActions 객체가 반환되는데, 이 객체는 이후
        // 결과값 검증 및 확인용의 andExpect() 메서드를 제공함.
        // 한 편, accept()는 응답 타입을 결정하는데에 사용되는 메서드로,
        // 여기서는 JSON 타입으로 받는 것으로 설정함.
        final ResultActions result = mockMvc.perform(get(url)
            .accept(MediaType.APPLICATION_JSON));

        // Then
        // 응답 코드가 OK(200)이고,
        // JSON 형태의 결과값 중 마지막 요소의 id, username의 값이 앞서
        // 저장한 사용자 객체의 것과 같은지 비교.

        // status().isOk() : 응답의 HTTP Status가 OK (200)인지 검증.
        // isCreated() 등 여러 다른 Status를 위한 메서드들도 존재함.
        // jsonPath("$") : JSON 형태의 응답 값을 받아올 때 사용.
        // 여기서 $는 받은 JSON 값 자체를 의미하는 듯 하다.
        // $.data.[-1].memberId )
        // {
        //      "httpStatus": "OK",
        //      "message": ...,
        //      "data": {
        //          ...,
        //          { // 마지막 데이터 ([-1])
        //              "memberId": 100,  -> $.data.[-1].memberId
        //              "username": "kimquel"  -> $.data.[-1].username
        //          }
        //      }
        // }
        //
        result
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.data.[-1].memberId")
                .value(savedUser.getMemberId())
            )
            .andExpect(jsonPath("$.data.[-1].username")
                .value(savedUser.getUsername()));
    }

}
```

코드 3-1. UserControllerTest.java

우선 위 코드에서 처음 보는 어노테이션들에 대해 설명하자면 다음과 같다.

- `@SpringBootTest` - 메인 패키지 내에서 `@SpringBootApplication` 어노테이션이 적용된 클래스를 찾은 후, 이를 토대로 스프링 Bean들을 모두 초기화하여 가져오는 역할을 한다. 이를 토대로 테스트용 애플리케이션 context를 형성한다. Spring Bean들을 모두 스캔하여 가져오기 때문에 의존성 주입에 대한 고민을 덜어주지만, 이로 인해 테스트 속도가 느려진다는 단점도 존재한다.
- `@AutoConfigureMockMvc`  - MockMvc 객체를 자동으로 생성 및 구성하는 어노테이션. MockMvc 객체는 테스트용 MVC 환경, 즉 테스트를 위한 웹 환경을 조성함으로써 실제 애플리케이션을 서버에 배포하지 않고도 테스트할 수 있도록 해주는 역할을 한다.

그 밖에도 MockMvc 객체 타입 변수 선언 및 WebApplicationContext 객체 의존성 주입을 통해 테스트를 위한 웹 애플리케이션 context 구성, 외부 DB 서버와의 데이터 전송을 위한 JpaRepository들도 의존성 주입하였다. 만약 테스트용이 아닌 실제 데이터들이 존재하는 DB에서 테스트를 진행한다면 테스트를 위해 생성된 테스트용 데이터들을 다시 DB에서 삭제하기 위해 `@AfterEach` 를 활용하였다. 

`mockMvc.perform()` 메서드는 웹 상에서 요청을 전송하는 역할로서, 실제로 테스트를 위해 테스트 대상인 URL에 요청을 전송하여 컨트롤러가 제 기능을 하는지 테스트하기 위한 용도이다. 이 메서드 안에는 `get()` 메서드가 사용되었는데, 이는 HTTP 요청 method를 GET으로 하겠다는 의미이다. 이 외에도 `post()`, `put()`, `delete()` 등이 있어 상황에 맞는 요청 method를 골라 사용할 수 있다. 

한 편 `/members?id=1` 과 같이 요청 파라미터를 붙이고자 한다면 다음과 같이 `param()` 메서드를 이용하여 작성하면 되며,

```java
mockMvc.perform(get(url)
    .param("id", "1")
    ...
```

`/members/{id}` 와 같이 요청 경로에 데이터를 추가하고자 한다면 다음과 같이 작성할 수 있다. 

```java
mockMvc.perform(get("/api/users/{id}", 100))
	... 
```

JSON 형태의 데이터를 요청 Body에 싣고자 한다면 자바 객체를 JSON으로 직렬화해주는 ObjectMapper와 결합하여 다음과 같이 작성 가능하다. 

```java
final ResultActions result = mockMvc.perform(post(url)
    .content(objectMapper.writeValueAsString(javaObj))
    .contentType(MediaType.APPLICATION_JSON)
);
```

이 외에 나머지 부분들에 대해서는 코드 내 주석으로 작성하였기에 참고하면 되겠다. 

위 테스트 코드(3-1)에서 보이는 주요 특징들로는 다음과 같다. 

- `@SpringBootTest` 어노테이션을 통해 Spring Bean 및 관련 설정들을 모두 가져와 테스트 준비를 한다. 앞서 말했듯, 이는 개발자가 테스트 코드 작성 시 의존성 주입에 대한 것을 상관하지 않아도 된다는 점이 있지만, 테스트 실행 속도가 느려진다는 단점도 있다.
- 외부 DB와 연결하여 테스트 대상인 컨트롤러 객체가 제 기능을 하는지 테스트하도록 구성되어 있다.

다음의 방식은 위와는 조금 다른 방식이다. 

```java
package com.jerocaller.TestStudy.controller;

import com.jerocaller.TestStudy.business.UserService;
import com.jerocaller.TestStudy.common.ResponseMessages;
import com.jerocaller.TestStudy.data.entity.SiteUsers;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.context.bean.override.mockito.MockitoBean;
import org.springframework.test.web.servlet.MockMvc;

import java.time.LocalDate;

import static org.mockito.BDDMockito.given;
import static org.mockito.Mockito.verify;
import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultHandlers.print;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.*;

/**
 * 컨트롤러 계층에 속한 Bean의 테스트
 *
 * <p>
 *     참고자료: 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”,
 *     (위키북스, 2024)
 * </p>
 *
 * <p>어노테이션 설명</p>
 * <p>
 *     <code>@WebMvcTest</code>
 *     웹에서 사용되는 요청 및 응답에 대한 테스트 수행에 사용됨.
 *     대상 클래스만 load하여 테스트를 수행한다. 만약 아무런 대상 클래스를 추가하지 않으면,
 *     <code>@Controller</code>, <code>@RestController</code>,
 *     <code>@ControllerAdvice</code> 등의 컨트롤러 관련 Bean 객체 모두 load됨.
 *     <code>@SpringBootTest</code>보다 가볍게 테스트할 때 사용.
 * </p>
 * <p>
 *     <code>@MockitoBean</code>
 *     원래는 <code>@MockBean</code>이었으나 현재 deprecated되어 대신 사용되고 있는
 *     어노테이션으로, 실제 Bean 객체가 아닌 Mock(가짜) 객체를 생성, 주입한다.
 *     실제 객체가 아니므로 실제 메인 애플리케이션 쪽에 작성한대로 수행되지 않음.
 *     따라서 Mockito 제공 given() 메서드를 통해 그 동작을 정의해야 함.
 *     테스트 대상 클래스에 실제 주입되는 외부 의존성으로부터 독립적인 테스트를 하고자 할 때
 *     사용.
 * </p>
 */
@WebMvcTest(UserController.class)
public class UserControllerTest2 {

    @Autowired
    private MockMvc mockMvc;

    @MockitoBean
    private UserService userService;

    @DisplayName("MockMvc를 이용하여 사용자 데이터 조회 테스트")
    @Test
    public void userTest() throws Exception {

        final Integer targetId = 100;

        given(userService.getOneUserById(100)).willReturn(
            SiteUsers.builder()
                .memberId(targetId)
                .signUpDate(LocalDate.now())
                .mileage(1000)
                .username("kimquel")
                .averPurchase(123.40)
                .build()
        );

        final String url = "/api/users/{id}";

        mockMvc.perform(get(url, targetId))  // "/api/users/100"
            .andExpect(status().isOk())
            .andExpect(jsonPath("$.message")
                .value(ResponseMessages.READ_OK)
            )
            .andExpect(jsonPath("$.data.memberId").value(targetId))
            .andExpect(jsonPath("$.data.username").value("kimquel"))
            .andExpect(jsonPath("$.data.recommBy").doesNotExist())
            .andDo(print());  // 이 테스트에서 사용된 mock Http 요청 및 응답 세부 내용 출력

        // mock 객체에 대해, 지정된 메서드의 실행 여부를 검증.
        // 여기서는 given()에 정의된 동작이 실제로 실행되었는지 여부를 검증한다. 
        verify(userService).getOneUserById(100);
        
    }

}

```

코드 3-2. UserControllerTest2.java

위 코드에서 보이는 새로운 어노테이션들을 정리하면 다음과 같다. 

- `@WebMvcTest` - 웹에서 사용되는 요청 및 응답에 대한 테스트 수행에 사용됨. 대상 클래스만 load하여 테스트를 수행한다. 만약 아무런 대상 클래스를 추가하지 않으면, `@Controller`, `@RestController`, `@ControllerAdvice` 등의 컨트롤러 관련 Bean 객체 모두 load된다. `@SpringBootTest`보다 가볍게 테스트할 때 사용. 테스트 대상 컨트롤러 객체에만 범위를 한정하여 테스트 할 수 있어 테스트 실행 속도도 비교적 빠른 편이다.
- `@MockitoBean` 원래는 `@MockBean`이었으나 현재 deprecated되어 대신 사용되고 있는 어노테이션으로, 실제 Bean 객체가 아닌 Mock(가짜) 객체를 생성, 주입한다. 실제 객체가 아니므로 실제 메인 애플리케이션 쪽에 작성한대로 수행되지 않는다. 따라서 Mockito 제공 `given()` 또는 `when()` 메서드를 통해 그 동작을 정의해야 한다. 테스트 대상 클래스에 실제 주입되는 외부 의존성으로부터 독립적인 테스트를 하고자 할 때 사용.

앞서 본 첫 번째 방식과 비교하여 이번 방식의 차이점 및 특징은 다음과 같다. 

- 테스트 대상 범위를 하나로만 한정하였다. 이로 인해 테스트 실행 속도가 비교적 빨라진다.
- JpaRepository를 이용하여 실제 외부 DB와 연동하였던 첫 번째 방식에 비해 이번 방식은 외부 DB와는 연동하지 않고, 대신 원래 컨트롤러 객체에 의존성 주입되던 서비스 객체를 mock 객체로 만들어 테스트에 사용하고 있다. 이를 통해 서비스 계층으로부터 독립적인 테스트를 갖출 수 있게 된다.
    - 첫 번째 방식은 외부 환경인 서비스 계층, 그리고 외부 DB와의 연동까지 하고 있기에 전체적인 애플리케이션의 작동을 테스트하는 통합 테스트의 성격에 가까운 반면, 두 번째 방식은 웹을 제외한 외부 DB 및 서비스 계층을 제외한다는 점에서 슬라이스 테스트에 더 가깝다고 할 수 있겠다.

### 서비스 계층에 대한 테스트

서비스 계층의 경우, 웹과 연관되어 있는 컨트롤러 계층과 달리 웹과 연결되어 있지 않은 계층이다. 따라서 서비스 계층에 대한 테스트 코드 작성 시 웹이라는 외부 환경을 배제하고 테스트할 수 있다. 또한 데이터 접근 계층에 속한 JpaRepository에 대해서도 mock 객체를 만들어 사용할 수 있기에 서비스 계층은 단위 테스트라고도 부를 수 있을 만큼 완전히 독립적인 환경에서의 테스트가 가능하다. 다음은 필자가 예시로 작성해본 서비스 계층 객체에 대한 테스트 코드이다. 

```java
// 생략...
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.mockito.Mockito;

import java.time.LocalDate;
import java.util.*;

import static org.assertj.core.api.AssertionsForClassTypes.assertThat;
import static org.mockito.ArgumentMatchers.*;
import static org.mockito.Mockito.*;

/**
 * Service 계층에 존재하는 Bean에 대한 테스트.
 *
 * 여기서는 컨트롤러와 달리 웹 통신을 통한 테스트가 필요하지 않고 오로지
 * 각 메서드에 대한 기능적 테스트만 필요하다고 가정. 이 경우 단위 테스트로 진행됨.
 * 이 경우 독립적인 테스트를 통한 정확한 테스트 결과 도출을 위해,
 * Service 객체에는 외부 의존성이 들어가지 않게 하고 대신 가짜 의존성을 넣도록 하여
 * 외부 의존성과 독립적으로 테스트하도록 설계.
 */
@Slf4j
class UserServiceTest {

    private final SiteUsersRepository siteUsersRepository =
        Mockito.mock(SiteUsersRepository.class);
    private final ClassTypeRepository classTypeRepository =
        Mockito.mock(ClassTypeRepository.class);
    private UserService userService;

    /**
     * 각 테스트 케이스들 간의 독립성을 위해 매 테스트 케이스 때마다
     * mock 객체를 주입.
     */
    @BeforeEach
    public void setUp() {

        userService = new UserService(
            classTypeRepository, siteUsersRepository
        );

    }

    @Test
    @DisplayName("UserService#getOneUserById() 테스트")
    void getOneUserByIdTest() {

        // Given
        final int memberId = 100;

        ClassType classType = ClassType.builder()
            .classNumber(1)
            .className("VIP")
            .bonus(1000)
            .build();

        SiteUsers user = SiteUsers.builder()
            .memberId(memberId)
            .signUpDate(LocalDate.now())
            .mileage(1000)
            .username("kimquel")
            .averPurchase(123.5)
            .classType(classType)
            .build();

        // siteUsersRepository는 여기서는 mock(가짜) 객체기에
        // 아무 것도 설정하지 않으면 아무런 기능을 하지 않는다.
        // 따라서 여기서는 테스트를 위해 특정 입력값을 토대로 특정 메서드를 호출하면 (when)
        // 특정 결과(thenReturn)가 나오도록 설정함.
        // given().willReturn()과 동일.
        Mockito.when(siteUsersRepository.findById(memberId))
            .thenReturn(Optional.of(user));

        // When
        SiteUsers actualUser = userService.getOneUserById(memberId);

        // Then
        assertThat(actualUser.getMemberId()).isEqualTo(user.getMemberId());
        assertThat(actualUser.getUsername()).isEqualTo(user.getUsername());
        assertThat(actualUser.getMileage()).isLessThan(2000);
        assertThat(actualUser.getClassType().getClassName())
            .isEqualTo("VIP");

        // siteUsersRepository#findById()가 실제로 호출되었는지 검증 시도함으로써
        // 이 테스트의 신뢰도를 높인다.
        verify(siteUsersRepository).findById(memberId);

        // 이 테스트 케이스 내에서 한 번도 호출된 적 없으므로
        // 다음의 코드는 테스트 실패로 이어짐.
        //verify(classTypeRepository).findById(any());
    }
    // 생략...
}
```

코드 4-1. UserServiceTest.java

실제 작성한 코드는 너무 길어서 위에선 일부 생략하였다. 위 클래스의 전체 코드는 다음의 사이트에서 확인 가능하다.

[https://github.com/JeroCaller/spring-boot-study-with-intellij/blob/master/TestStudy/src/test/java/com/jerocaller/TestStudy/business/UserServiceTest.java](https://github.com/JeroCaller/spring-boot-study-with-intellij/blob/master/TestStudy/src/test/java/com/jerocaller/TestStudy/business/UserServiceTest.java)

위 테스트 코드는 웹과 관련이 없는 서비스 계층에서의 테스트 코드이기에 앞서 컨트롤러 계층에 대한 테스트 코드에서 봤던 `@SpringBootTest` 등의 어노테이션 및 MockMvc 등 웹 관련 설정들이 사용되지 않았음을 볼 수 있다. 

위 코드에서는 서비스 계층 객체에 보통 의존성 주입되어 사용되는 repository 객체들을 mock 객체로 만들었다. 이 때, `Mockito.mock()` 메서드를 사용하였는데, 기존의 `@MockitoBean` 어노테이션과의 차이점은, 해당 어노테이션을 사용하면 mock 객체는 스프링 컨테이너에 등록되고, 이 등록된 객체를 주입받아 사용하는 방식인 반면, `Mockito.mock()` 메서드는 스프링 컨테이너를 사용하지 않고 직접 mock 객체를 생성하여 사용하는 방식이다. 만약 `@MockitoBean` 과 같이 스프링 컨테이너를 활용한 테스트 코드를 작성하고자 한다면 다음의 방식도 고려해볼만할 것이다.

```java
@ExtendWith(SpringExtension.class)
@Import({UserService.class})
public class UserServiceTest2 {

    @MockitoBean
    private SiteUsersRepository siteUsersRepository;

    @MockitoBean
    private ClassTypeRepository classTypeRepository;

    @Autowired
    private UserService userService;
// 생략...
```

코드 4-2. UserServiceTest2.java

위 코드의 나머지 부분들은 코드 4-1과 동일하다. 

- `@ExtendWith(SpringExtension.class)` - 스프링에서 객체를 주입받기 위한 어노테이션. SpringExtension 클래스는 JUnit의 Jupiter 테스트에 스프링 테스트 컨텍스트 프레임워크(Spring TestContext Framework)를 통합하는 역할을 수행함.
- `@Import({Some.class})` - `@Autowired` 어노테이션을 통해 의존성 주입할 클래스를 import하여 가져오고 실제로 의존성 주입이 가능하게 하는 어노테이션. 여기서는 테스트 대상인 서비스 객체를 가져오는 용도로 사용됨.

### 리포지토리에 대한 테스트

리포지토리 계층은 DB와 가장 가까운 계층으로, 데이터의 CRUD가 일어나는 곳이다. 그렇기에 DB라는 외부 요소를 완전히 제외하고 테스트하는 것은 불가능하다고 볼 수 있을 것이다. 대신 실제 사용할 DB를 테스트용으로 사용할 것이냐, 아니면 테스트용 DB를 따로 마련하여 거기서 테스트할 것이냐를 결정할 수 있을 것이다. 테스트용 DB로는 보통 H2와 같은 Embedded DB를 사용하여 진행하는 경우도 있다고 한다. Embedded DB는 모바일 기기나 가전 제품 등의 전자 제품에 소프트웨어(애플리케이션)를 내장시킬 때 데이터 저장을 위해 사용되는 DB로, 일반적인 DB와 달리 애플리케이션과 아주 가깝게 위치하여 연동되는 특성을 지닌다. 저장될 데이터가 다른 기기나 사용자와 공유할 목적이 아니고 그 시스템 내부에서만 사용하고자 할 때 주로 사용된다고 한다. 

하지만 테스트용 DBMS와 실제 DBMS를 다르게 하였을 때 우려되는 문제점도 있다. 테스트용 DBMS와 실제 사용할 DBMS 간에는 시스템 자체의 간극이 있어, 이는 테스트 시 호환성 문제로 생각대로 테스트가 잘 진행되지 않을 수 있고, 설령 테스트에서 성공했다고 해서 실제 DB와의 상호작용에도 문제가 없다고 단언하기 힘들 것이다. 

여기서는 H2와 같은 별도의 테스트용 DB를 사용하지 않고 기존의 MariaDB를 사용하는 대신, 실제 테이블과 똑같은 테스트용 테이블을 마련하여 그곳에서 테스트를 진행하였다. 

다음은 테스트 대상인 리포지토리 객체의 코드이다.

```java
public interface SiteUsersRepository extends JpaRepository<SiteUsers, Integer> {

    List<SiteUsers> findByClassType(ClassType classType);

    /**
     * 가장 많은 추천을 받은 유저 한명 조회
     * 
     * @return
     */
    @Query("""
        SELECT su1
        FROM SiteUsers su1
        JOIN SiteUsers su2
        ON su2.recommBy = su1.username
        WHERE
            su2 = (SELECT su3
                FROM SiteUsers su3
                WHERE su3.recommBy IS NOT NULL
                GROUP BY su3.recommBy
                HAVING COUNT(su3) = (
                    SELECT MAX(sub.recommendationCount)
                    FROM (
                        SELECT COUNT(su4) AS recommendationCount
                        FROM SiteUsers su4
                        WHERE su4.recommBy IS NOT NULL
                        GROUP BY su4.recommBy
                    ) sub
                )
            )
    """)
    Optional<SiteUsers> findByRecommByMax();
}

```

코드 5-1. SiteUsersRepository.java

그리고 다음은 위 코드에 대한 테스트 코드이다.

```java
// 생략...
import lombok.extern.slf4j.Slf4j;
import org.junit.jupiter.api.AfterEach;
import org.junit.jupiter.api.DisplayName;
import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.jdbc.AutoConfigureTestDatabase;
import org.springframework.boot.test.autoconfigure.orm.jpa.DataJpaTest;

import java.util.List;

import static org.assertj.core.api.Assertions.assertThat;

/**
 * <p>DB와 가장 가까운 repository에 대한 테스트</p>
 * <p>어노테이션 설명</p>
 * <p>
 *     <code>@DataJpaTest</code> - 다음의 기능들을 수행한다.
 *     <ol>
 *     <li>JPA 관련된 설정들만 로드하여 테스트 진행함.</li>
 *     <li>기본적으로 <code>@Transactional</code> 어노테이션을 포함하고 있어
 *     테스트 코드 종료 시 자동으로 DB의 Rollback이 진행됨.
 *     => 이로 인해 테스트 진행 과정에서 테스트용 데이터를 생성하여 save() 메서드 등을
 *     호출하면 테스트 종료 후 롤백되기에 결과적으로는 DB에 테스트 데이터가 남지 않는다.
 *     따라서 별도로 테스트 데이터를 지우는 기능을 정의하지 않아도 된다.
 *     </li>
 *     <li>기본값으로는 H2와 같은 Embedded DB를 사용한다. 다른 DB 사용 시 아래 소개할
 *     어노테이션을 통해 별도 설정을 해야 한다.
 *     </li>
 *     </ol>
 * </p>
 *
 * <p>
 *     <code>@AutoConfigureTestDatabase(replace = Replace.NONE)</code> -
 *     어떤 종류의 데이터베이스를 사용할 것인지 설정하기 위한 어노테이션
 *     <ul>
 *         <li>
 *             ANY : H2와 같은 Embedded memory DB 사용 시
 *         </li>
 *         <li>
 *             NONE : 애플리케이션에서 실제 사용하는 DB로 테스트. 여기선
 *             MariaDB를 사용하므로 이를 테스트에 사용하게끔 함.
 *         </li>
 *     </ul>
 * </p>
 *
 */
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Slf4j
class SiteUsersRepositoryTest {

    @Autowired
    private SiteUsersRepository siteUsersRepository;

    @Autowired
    private ClassTypeRepository classTypeRepository;

    @Test
    @DisplayName("findByClassType: 특정 클래스 타입의 유저만 조회하는지 테스트")
    public void findByClassTypeTest() {

        ClassType givenTypeOne = classTypeRepository.saveAndFlush(
            ClassType.builder()
                .classNumber(50)
                .className("VIP")
                .build()
        );
        ClassType givenTypeTwo = classTypeRepository.saveAndFlush(
            ClassType.builder()
                .classNumber(60)
                .className("GOLD")
                .build()
        );

        log.info("givenTypeOne: {}", givenTypeOne);
        log.info("givenTypeTwo: {}", givenTypeTwo);

        SiteUsers givenTypeOneUser = siteUsersRepository.saveAndFlush(
            SiteUsers.builder()
                .memberId(100)
                .username("kimquel")
                .classType(givenTypeOne)
                .build()
        );
        SiteUsers givenTypeTwoUser = siteUsersRepository.saveAndFlush(
            SiteUsers.builder()
                .memberId(200)
                .username("jeongdb")
                .classType(givenTypeTwo)
                .build()
        );

        log.info("givenTypeOneUser: {}", givenTypeOneUser);
        log.info("givenTypeTwoUser: {}", givenTypeTwoUser);

        List<SiteUsers> resultUsers =
            siteUsersRepository.findByClassType(givenTypeOne);
        log.info("size : {}", resultUsers.size());
        logList(resultUsers);

        assertThat(resultUsers.getFirst().getClassType().getClassNumber())
            .isEqualTo(50);
        assertThat(resultUsers.getFirst().getClassType().getClassName())
            .isEqualTo("VIP");
        assertThat(resultUsers.getFirst().getMemberId()).isEqualTo(100);
        assertThat(resultUsers.getFirst().getUsername())
            .isEqualTo("kimquel");
        assertThat("jeongDb").isNotIn(resultUsers);

    }

    @Test
    @DisplayName("findByRecommByMax: 최다 추천받은 사용자 조회 기능 테스트")
    public void findBYRecommByMaxTest() {

        SiteUsers givenUserOne = siteUsersRepository.saveAndFlush(
            SiteUsers.builder()
                .memberId(100)
                .username("kimquel")
                .recommBy("jeongdb")
                .build()
        );
        SiteUsers givenUserTwo = siteUsersRepository.saveAndFlush(
            SiteUsers.builder()
                .memberId(101)
                .username("jeongdb")
                .recommBy("kimquel")
                .build()
        );
        SiteUsers givenUserThree = siteUsersRepository.saveAndFlush(
            SiteUsers.builder()
                .memberId(102)
                .username("javas")
                .recommBy("kimquel")
                .build()
        );

        SiteUsers resultUser = siteUsersRepository.findByRecommByMax()
            .orElseGet(null);

        assertThat(resultUser).isNotNull();
        assertThat(resultUser.getUsername()).isEqualTo("kimquel");

    }

    private <E> void logList(List<E> list) {

        log.info("=== List Logging Start ===");

        if (list.isEmpty()) {
            log.info("Nothing Here!!!");
        } else {
            for (int i = 0; i < list.size(); i++) {
                log.info("{} : {}", i, list.get(i));
            }
        }

        log.info("=== List Logging End ===");

    }

}
```

코드 5-2. SiteUsersRepositoryTest.java

- `@DataJpaTest` - 다음의 기능들을 수행한다.
    - JPA 관련된 설정들만 로드하여 테스트 진행함.
    - 기본적으로 `@Transactional` 어노테이션을 포함하고 있어 테스트 코드 종료 시 자동으로 DB의 Rollback이 진행됨. => 이로 인해 테스트 진행 과정에서 테스트용 데이터를 생성하여 `save()` 메서드 등을 호출하면 테스트 종료 후 롤백되기에 결과적으로는 DB에 테스트 데이터가 남지 않는다. 따라서 별도로 테스트 데이터를 지우는 기능을 정의하지 않아도 된다.
    - 기본값으로는 H2와 같은 Embedded DB를 사용한다. 다른 DB 사용 시 아래 소개할 어노테이션을 통해 별도 설정을 해야 한다.
- `@AutoConfigureTestDatabase(replace = Replace.NONE)` - 어떤 종류의 데이터베이스를 사용할 것인지 설정하기 위한 어노테이션
    - ANY : H2와 같은 Embedded memory DB 사용 시.
    - NONE : 애플리케이션에서 실제 사용하는 DB로 테스트. 여기선 MariaDB를 사용하므로 이를 테스트에 사용하게끔 함.

# 그 외 정보들

- src/test/java 내 작성된 모든 테스트 코드들을 한꺼번에 실행하고자 할 때에는 IntelliJ에서 모든 테스트 코드가 들어있는 src/test/java 폴더 우클릭 후 “실행” 버튼을 누르면 된다.
- 테스트 코드에서 lombok을 적용하고자 한다면 다음과 같이 build.gradle에 추가적으로 의존성을 기입해야 한다.

```java
// 테스트에서 lombok 사용 시 아래와 같은 의존성들을 추가.
	testCompileOnly 'org.projectlombok:lombok'
	testAnnotationProcessor 'org.projectlombok:lombok'
	testImplementation 'org.projectlombok:lombok'
```

- 한 편, 테스트 코드에서는 Lombok의 `@RequiredArgsConstructor` 와 필드의 final 선언만으로는 생성자를 이용한 의존성 주입이 진행되지 않는다. 만약 이렇게 진행한다면 `org.junit.jupiter.api.extension.ParameterResolutionException: No ParameterResolver registered for parameter` 라는 예외가 발생할 것이다. 이는 의존성 주입의 주체가 다르기 때문에 발생한 원인으로, 메인 쪽에서는 스프링 컨테이너가 의존성 주입을 하기에 `@Autowired` 어노테이션을 적용하지 않아도 자동으로 의존성 주입을 해준다고 한다. 그러나 테스트의 경우, JUnit의 Jupiter가 이를 담당하는데, Jupiter는 스프링 컨테이너와 달리 자동으로 `@Autowired` 가 적용된 코드를 판별하는 기능을 제공하지 않기에 `@Autowired` 를 사용해야 하는 것이다. 그래서 테스트 코드 작성 시 생성자를 이용한 의존성 주입을 하고자 할 경우, 생성자 메서드에 `@Autowired` 를 적용해야 한다. 필자는 주입해야할 의존성들이 많아질수록 생성자 메서드의 코드가 길어지는 것이 불편하다 느껴 위에서 소개한 코드에서는 필드에 의한 의존성 주입을 사용하였다.
- IntelliJ에서 메인 패키지 내에 있는 테스트 하고자 하는 클래스의 테스트 클래스 파일을 빠르게 생성하는 방법이 있다. 메인 패키지 내에 있는 테스트 대상 클래스 이름 끝 부분에 깜빡이는 커서를 둔 뒤, Alt + Enter 키를 누른다. 그러면 뜨는 메뉴에서 “테스트 생성”을 클릭하면 src/test/java 내에 똑같은 패키지 구조를 가지는 테스트 파일이 생성된다.

# 글을 마치며…

그 어려운 인증을 공부한 뒤에 맞이한 개념이라 적어도 인증보다는 쉽고 빨리 공부할 수 있을거라고 생각했었다. 마냥 쉬운 건 아니었지만 그래도 스프링 시큐리티, OAuth에 비해선 쉬웠다고 느꼈다. 하지만 처음보는 어노테이션들과 메서드들의 항연으로 인해 생각보다 글로 정리하는데에 시간이 많이 걸렸다(그리고 사실 이 때부터 기존에 사용하던 이클립스가 아닌 인텔리제이로 넘어왔기에 처음 보는 것들이 많아 시간이 걸린 것도 있다). 사실 검색해보니 위 글에서 소개한 어노테이션들 외에도 처음보는 어노테이션들이 되게 많았다. 이들에 대해서는 처음부터 모든 걸 다 알아가는 건 힘들기에 실제 테스트 진행 시 필요한 일이 생기면 그때그때 검색하여 추가적으로 알아보려고 한다. 이러한 의미에서, 필자가 이 글에서 정리하지는 않았지만 추가적인 개념들도 있었다. 

- TDD (Test Driven Development)
- 코드 커버리지와 Jacoco

이러한 것들도 나중에 필요하다 생각될 때 공부하려고 한다. 

이렇게 생각보다는 그렇게까지 쉽지는 않았고, 시간도 생각보다 오래 걸리긴 했지만, 자바, 스프링부트로 제작한 코드에 대한 테스트 코드를 작성하는 기초적인 방법을 터득하게 되어서 오히려 든든해지는 측면도 있었다. 그전까지만 해도 내가 작성한 코드가 예상치 못한 버그나 예외없이 잘 작동하는지 보장해줄 수단이 없었기 때문이다. 

필자 개인적으로도 코드를 작성하다보면 “이건 라이브러리로 따로 만들어서 사용하는 게 더 편리할 것 같은데..”라는 생각이 들 때가 종종 있었다. 그래서 스프릥 부트 환경에서 제작한 라이브러리 제작 시 웹 환경에서도 내가 의도한 대로 동작하는지 확인하기 위한 좋은 수단이 될 것 같다. 

이러한 이유로, 생각보다는 글로 정리하는 데까지는 오래 걸렸지만 이를 공부한 것에 대해선 후회되진 않는다. 

---

References

[1] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, ch. 7, (위키북스, 2024)

[2] 신선영, “스프링 부트 3 백엔드 개발자 되기 - 자바 편, 2판”, (골든래빗)

[3] AssertJ import 방법

[JUnit 5와 AssertJ로 테스트코드 작성하기](https://velog.io/@woonyumnyum/%EC%9A%B0%EC%BD%94%ED%85%8C-JUnit-5%EC%99%80-AssertJ%EB%A1%9C-%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1%ED%95%98%EA%B8%B0)

[4] [2]의 전체 소스 코드

[springboot-developer-2rd/chapter4 at main · shinsunyoung/springboot-developer-2rd](https://github.com/shinsunyoung/springboot-developer-2rd/tree/main/chapter4)

[5] 테스트 코드에 lombok 적용법

[[JUnit] Junit test code에서 lombok @Slf4j 동작하지 않음 ( Cannot resolve symbol 'Slf4j' ), gradle](https://kangyb.tistory.com/30)

[6] 테스트 코드에 lombok 적용법

[[Gradle] lombok - test 환경에서 사용하기](https://jundragon.tistory.com/9)

[7] `org.junit.jupiter.api.extension.ParameterResolutionException: No ParameterResolver registered for parameter` 예외 해결법

[[JUnit5] DB호출 테스트 중 발생한 No ParameterResolver registered for parameter in constructor 에러 해결](https://colabear754.tistory.com/95)

[8] verify()에 대해.

[[Mockito] verify()로 행위 검증하기](https://amaran-th.github.io/Java/%5BMockito%5D%20verify()%EB%A1%9C%20%ED%96%89%EC%9C%84%20%EA%B2%80%EC%A6%9D%ED%95%98%EA%B8%B0/)

[9] IntellJ에서 “import static” 자동 완성 방법. 메서드의 괄호까지 작성한 후 메서드명과 괄호 사이에 커서를 둔 후 alt + enter 키를 입력하면 뜨는 목록에서 import를 원하는 경로를 자동 입력할 수 있다. 

[[IntelliJ] import static 단축키 사용(설정)하는 법](https://zorba91.tistory.com/240)

[10] Repository 테스트에 임베디드 DB를 쓰지 말아야 하는 이유 - DBMS 간의 SQL 문법 및 시스템이 다르기 때문에 테스트 환경 구성에 실패할 가능성이 높음. 

[테스트에 임베디드 DB 쓰지 말라니까](https://keepseeking.tistory.com/20)

[11] 임베디드 DB에 관한 설명

[지식덤프](http://www.jidum.com/jidums/view.do?jidumId=261)

[12] 수동 테스트와 자동 테스트. 내가 예전에 쓴 글

[[Python] 코드 테스트 개요](https://jerocaller.github.io/python/test/test-code-in-python/)

[13] 스프링부트 테스트에 대한 더 많은 정보들

[Spring Boot Test 종합 정리 ( 테스트종류, JUnit5 )](https://happyer16.tistory.com/entry/Spring-Boot-Test-%EC%A2%85%ED%95%A9-%EC%A0%95%EB%A6%AC)

[14] 임베디드 DB

[내장된 데이터베이스를 사용하고 싶다면? \| GDSC UOS](https://gdsc-university-of-seoul.github.io/embedded-database/)

[15] 임베디드 DB

[임베디드 데이터베이스](https://ko.wikipedia.org/wiki/%EC%9E%84%EB%B2%A0%EB%94%94%EB%93%9C_%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4)