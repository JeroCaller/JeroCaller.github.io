---
title: "PMD와 CPD - 소스 코드 품질 정적 분석 도구"
category: "Tools"
tag: ["CPD", "PMD", "Static code analysis", "정적 코드 분석", "코드 분석"]
---

# 개요

문법적인 오류나 프로그램 실행 단계에서 발생할 수 있는 런타임 에외들은 발생 시 이를 메시지 형태로 알려주기에 어디서 문제가 발생했는지 원인을 파악할 수 있다. 한 편 테스트 코드를 작성하면 각 기능들이 원래 의도대로 동작하는지 쉽게 확인할 수 있다. 이 모두 공통점은 프로그래밍 단계에서 발생할 수 있는 문제점들을 시스템적으로 파악할 수 있다는 것이다. 그런데 사용되지 않는 변수 선언 또는 객체 생성, 빈 catch block 등의 잘못된 코드 스타일, 중복 코드 등의 결점이 있는지를 확인하기 위해선 지금까지는 수동으로 코드를 일일히 읽어나가면서 분석했어야 했을 것이다. 이들은 오류가 발생하지도 않고, 테스트 코드 작성으로 발견하기도 쉽지 않기 때문이다. 하지만 이조차도 간단하게 자동화하여 파악할 수 있다. 이를 수행할 수 있는 도구 중 하나로 PMD라는 도구가 존재한다. 

PMD는 정적 소스 코드 분석 도구로, 소스 코드를 실행하지 않고 분석하여 앞서 말한 프로그래밍적 결점들을 찾아준다. 에러가 나기 쉬운 잠재적인 결점들, 보안적 허점들, 코드 스타일 등을 탐지할 수 있다. 이러한 도구를 통해 소스 코드의 품질을 분석하고 향상시킬 수 있다. 오픈 소스이며, 무료로 사용 가능하다. 

뿐만 아니라 PMD에는 CPD(Copy - Paste Detector)라는 도구도 제공한다. 이 도구는 소스 코드 내에서 중복되는 코드를 탐지해주는 도구이다. 이를 이용하면 리팩토링 작업 시 리팩토링 대상인 중복 코드를 찾아내는데에 한층 더 수월할 것이다. 

PMD는 주로 자바 언어를 고려하여 만들어졌다고는 하지만 그렇다고 해서 자바 언어에서만 사용할 수 있는 것은 아니다. HTML, XML, JSP, Kotlin, Javascript 등 여러 다양한 프로그래밍 언어들에 대해서도 사용할 수 있다고 한다. 

이번 글에서는 이러한 PMD와 CPD라는 소스 코드 품질 분석 도구에 대해 살펴보도록 하겠다. 

## 배경 지식

소스 코드 품질 분석 도구는 소스 코드의 코딩 스타일, 표준 등을 지키고 있는지 확인하고, 코드 복잡도, 메모리 누수 현상, 스레드 결함 및 그 외 잠재적으로 발생할 수 있는 결함들을 탐지할 때 사용하는 분석 도구이다. 이러한 소스 코드 품질 분석 도구에는 크게 정적 분석 도구와 동적 분석 도구로 나뉜다. 

- 정적 분석 도구 : 소스 코드를 실행시키지 않고 분석하는 형태로 결함을 파악하는 방식이다. 주로 앱 개발 초기와 완료 시점에서 사용한다고 한다. 앱 개발 초기 시점에서는 결함을 미리 찾는데에 사용되며, 개발 완료 시점에서는 소스 코드 품질을 검증하고자 할 때 사용된다고 한다.
    - 분석 도구들 : PMD, cppcheck, SonarQube, checkstyle, ccm, cobertuna 등이 있다고 한다.
- 동적 분석 도구 : 소스 코드를 실행하여 분석하는 도구로, 주로 메모리 누수, 스레드 결함 등을 파악할 때 사용한다.
    - 분석 도구들 : Avalanche, Valgrind 등이 있다고 한다.

## TMI

PMD 공식 소개 글에서 PMD의 약자가 무엇인지 나와있지 않았고, 몇몇 다른 글들에서는 이를 “Programming Mistake Detector”로 표현하길래 그런 줄 알고 있었다. 그런데 후에 [공식 사이트](https://docs.pmd-code.org/latest/pmd_projectdocs_trivia_meaning.html)를 조금 더 찾아보니 사실 그냥 발음이 좋아보여서 지은거지, 무언가의 약자로 지은 것이 아니라고 한다. 오히려 공식에서는 “PMD”의 의미를 찾으려 하고 있다고 한다. 그러면서 “Pretty Much Done”, “Project Mess Detector” 등의 여러 후보들을 나열하면서 “Programming Mistake Detector”라는 말도 그 중 하나로 나열되어 있었다. 아무래도 이 말이 좀 더 PMD의 특성과 잘 어울려서 대중적으로는 이 말로 알려진 것 같다. 

# PMD 설치 및 사용법

PMD를 이용하여 코드를 분석하고자 할 때 이를 실행하는 방법에는 여러가지가 있다. 

- CLI 환경에서 실행.
- Intellij에서 plugin으로 실행.
- Maven, Gradle 등의 빌드 도구를 통해 실행.

여기서는 위 각각의 실행을 위해 PMD를 설치하고 사용하는 방법에 대해 설명하겠다. 

## Intellij에서 plugin으로 설치하는 방법

1. Intellij에서 “파일” ⇒ “설정” ⇒ “플러그인”으로 들어간 후, 검색창에서 “pmd”라고 검색한다. 
2. 그러면 다음과 같이 검색 결과가 나올 것이다. 필자의 경우 저자명이 “Amit Dev”인 플러그인을 설치하였다.
    
    ![사진 1-1. 인텔리제이에서의 PMD 플러그인](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/1.png)
    
    사진 1-1. 인텔리제이에서의 PMD 플러그인
    
3. 위와 같이 하면 플러그인 설치는 끝났다. 이후 다음 사진과 같이, PMD를 실행하여 코드 분석을 적용하고자 하는 패키지를 우클릭한 후, “Run PMD” ⇒ “Pre Defined - Java”에서 원하는 목록을 클릭한다. 필자의 경우 “All”을 클릭하였다. 
    
    ![사진 1-2. 인텔리제이 플러그인 설치 후 PMD 실행 방법.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/2.png)
    
    사진 1-2. 인텔리제이 플러그인 설치 후 PMD 실행 방법.
    
4. IDE 창 왼쪽 아래에 PMD 아이콘이 생기는데, 아래에 다음 사진처럼 PMD 분석 결과가 뜬다. 항목을 하나씩 클릭해서 보면 어떤 파일, 어떤 코드에서 코드 작성 규칙을 어겼고, 어떻게 작성해야 좋은지도 확인할 수 있게 된다.
    
    ![사진 1-3. PMD 실행 후 결과.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/3.png)
    
    사진 1-3. PMD 실행 후 결과.
    

## binary zip 파일 형태로 설치하는 방법

1. (Windows 기준) [https://pmd.github.io/](https://pmd.github.io/)  이 사이트에서 “Download” 버튼을 클릭하여 zip 파일을 다운로드 받는다. 
    
    ![사진 2-1. PMD 공식 문서. 오른쪽의 “Download” 버튼을 눌러 zip 파일을 다운로드 받는다. ](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/4.png)
    
    사진 2-1. PMD 공식 문서. 오른쪽의 “Download” 버튼을 눌러 zip 파일을 다운로드 받는다. 
    
2. 다운로드 받은 zip 파일을 원하는 곳에 압축 해제한다. 필자의 경우, `C://pmd` 라는 폴더를 만든 후 그 안에 압축 해제하였다. 
3. 압축 해제한 폴더 내부에는 bin이라는 이름의 폴더가 존재한다. 이 경로를 환경 변수에 등록한다. 윈도우 검색창에 “시스템 환경 변수 편집”을 검색하여 클릭하면 창이 하나 뜬다. 거기서 “환경 변수” 클릭 ⇒ 리스트에서 “Path” 목록을 찾아 더블클릭 ⇒ pmd 압축해제한 폴더 내 bin 폴더 경로를 붙여넣는다. 
    
    ![사진 2-2. 환경변수 설정 모습.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/5.png)
    
    사진 2-2. 환경변수 설정 모습.
    
    ![사진 2-3. 환경변수 설정 모습.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/6.png)
    
    사진 2-3. 환경변수 설정 모습.
    
4. 그러면 이후 어디서든 pmd 명령어를 통해 pmd를 실행할 수 있게 된다.

만약 특정 프로젝트 폴더에 위치한 상태에서, CLI 환경에서 다음의 명령어를 입력하면,

```powershell
pmd.bat check -f text -R rulesets/java/quickstart.xml -d ./src/main/java -r ./pmd-report.txt
```

코드 1-1. PMD 명령어 예시

현재 위치의 `pmd-report.txt` 가 없었다면 해당 파일이 새로 생성되어 그 안에 특정 룰을 적용하여 PMD를 실행한 결과가 텍스트 형태로 저장된다. 

위 명령어에 쓰인 `-f`, `-R` 등의 옵션에 대한 설명은 다음의 사이트를 참고하면 되겠다.

- [https://docs.pmd-code.org/latest/pmd_userdocs_installation.html#running-pmd-via-command-line](https://docs.pmd-code.org/latest/pmd_userdocs_installation.html#running-pmd-via-command-line)
- [https://docs.pmd-code.org/latest/pmd_userdocs_cli_reference.html](https://docs.pmd-code.org/latest/pmd_userdocs_cli_reference.html)

## Gradle를 이용하는 방법

프로젝트 내에서 Maven, Gradle 등의 빌드 도구를 이용할 경우 이를 통해서도 PMD를 실행해볼 수 있다. 여기서는 Gradle을 기준으로 설명하겠다. 

1. `build.gradle` 에서 다음과 같이 작성한다.
    
    ```groovy
    plugins {
      // 생략
      id 'pmd'  // 추가
    }
    
    pmd {
      toolVersion = "7.22.0"  // 사용하고자 하는 pmd 버전을 명시.
      ignoreFailures = true  // pmd 규칙에 위배되는 코드가 있더라도 프로젝트 빌드를 가능하도록 설정.
    
      // 커스텀 룰셋을 적용하고자 하는 경우 다음의 코드를 적용
      // ruleSetFiles = files("${project.rootDir}/config/pmd/ruleset.xml")  // 커스텀 룰셋 파일 위치를 지정.
      // ruleSets = []
    }
    ```
    
    코드 2-1. `build.gradle`
    
2. 터미널 창에서 `./gradlew pmdMain` , `./gradlew pmdTest` 명령어를 입력하거나, 인텔리제이의 경우 다음 사진과 같이 화면 우측에 “Gradle 아이콘 (코끼리 모양)” 클릭 ⇒ “Tasks” ⇒ “other” ⇒ “pmdMain” 또는 “pmdTest”를 더블클릭하면 pmd가 실행된다. 
    
    ![사진 3-1.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/7.png)
    
    사진 3-1.
    
3. 실행된 후 결과는 `build/reports/pmd` 폴더 안에 `main.html` 또는 `test.html` 파일로 생성된다. 이를 브라우저에서 보면된다. 
    
    ![사진 3-2. PMD 실행 HTML 결과](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/8.png)
    
    사진 3-2. PMD 실행 HTML 결과
    

이 방법을 이용할 경우 원한다면 CI/CD 파이프라인에도 이를 추가하여 모든 코드가 프로젝트에서 정의한 룰을 지키도록 강제할 수도 있겠다. 

## 개인적인 PMD 사용 후기

위에서 소개한 3가지 방법으로 각각 PMD를 실행하여 코드 분석을 수행해본 결과, 실행 과정이 가장 간단하고 결과도 눈에 띄게 잘 보이도록 하는 방법은 Intellij의 plugin을 이용하는 방법이었다. 다른 방법들의 경우, 실행 과정도 상대적으로 간단하지 않았고, 코드 분석 결과도 한 눈에 알아보기 어렵거나, 특정 정보들이 누락된 경우가 있어 사용하기 불편하였다. 다만, CI/CD 파이프라인에 적용한다든가 특수한 상황에서 사용해야할 경우에는 어쩔 수 없이 다른 방법으로 사용해야할 수도 있기에 이러한 방법들이 있다는 것을 알아두면 좋을 것 같다. 

# PMD 내장 규칙

PMD에는 여러 내장 규칙들을 제공하고 있어 프로젝트에 이 규칙들을 적용하여 어떤 코드가 어떤 규칙을 어기고 있는지를 확인할 수 있다. PMD에서 제공하는 내장 규칙의 수들은 매우 많은데, 각각 어떤 규칙들이 있는지는 다음의 사이트를 참고하면 되겠다. 다음 사이트는 자바 언어를 기준으로 한다. 

- [https://docs.pmd-code.org/latest/pmd_rules_java.html](https://docs.pmd-code.org/latest/pmd_rules_java.html)

<style>
  .single-image {
    text-align: center;
    margin: 1em;
  }
  .single-image > p {
    margin-top: 0.5em;
  }
</style>
<div class="single-image">
  <img width="60%" src="/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/9.png" alt="image">
  <p>사진 4-1.  PMD 내장 규칙의 한 예. 출처: <a href="https://docs.pmd-code.org/latest/pmd_rules_java_codestyle.html#longvariable" target="_black">https://docs.pmd-code.org/latest/pmd_rules_java_codestyle.html#longvariable</a></p>
</div>

예를 들어, 위 사진과 같이 PMD에서는 “LongVariable”이라는 내장 규칙이 존재하는데, 이는 말 그대로 필드, 지역 변수, 매개변수 등의 변수의 이름이 너무 길면 안된다는 규칙이다. 위 사진에 따르면 변수의 길이가 17로 기본으로 설정되어 있는데, 이는 코드 내 변수명의 길이가 17을 넘겨선 안된다는 것이다. 이 규칙을 어기면 어떤 코드에서 이 규칙을 어겼는지 결과창에 뜰 것이다. 물론 이러한 수치를 직접 조정할 수 있는데, 이에 대한 자세한 방법은 후술할 “커스텀 Rule 작성 및 적용” 챕터에서 다루겠다. 

한 편 이러한 규칙들은 다음의 카테고리로 나뉘어져 있다. 

| 이름 | 설명 |
| --- | --- |
| Best Practices | 일반적으로 모범 사례(best practice)로 알려져 있는 규칙들의 모임. |
| Code Style | 코드 스타일 규칙들의 모임. 네이밍 컨벤션 규칙, 사용하지 않는 코드 삭제 등이 있다.  |
| Design | 디자인 이슈를 발견하는 규칙들의 모임. 추상 메서드가 없는 추상 클래스 방지, 널 포인터 예외 처리 방지 등이 있다고 한다. |
| Documentation | 코드 문서에 관한 규칙들의 모임. 주석 및 javadoc에 적용될 규칙들이 있다.  |
| Error Prone | 에러가 발생하기 쉬운 코드를 감지하는 규칙들의 모임. |
| Multithreading | 멀티 스레드 실행 시 발생할 수 있는 이슈들을 감지하는 규칙들의 모임. |
| Performance | 최적화되지 않은 코드들을 감지하는 규칙들의 모임. |
| Security | 잠재적인 보안적 허점을 가질 수 있는 코드를 감지하는 규칙들의 모임 |

또한 각 규칙에는 다음과 같은 우선순위가 존재한다.

| 우선순위 | 이름 |
| --- | --- |
| 1 | High |
| 2 | Medium_High |
| 3 | Medium |
| 4 | Medium_Low |
| 5 | Low |

우선순위가 1에 가까워질수록 가장 중요하게 해결해야할 규칙임을 뜻한다. 

앞선 “PMD 설치 및 사용법” 챕터에서 PMD를 설치하고 특정 버튼 또는 명령어를 입력하자마자 바로 코드 분석 결과가 나올 수 있었던 것은 모두 이러한 PMD 내장 규칙들이 이미 존재하여 이들을 적용했기 때문이다. 

# 커스텀 Rule 작성 및 적용

PMD 내장 규칙들이 존재하는 한 편, 이러한 PMD 내장 규칙들을 커스텀하거나, 아니면 처음부터 새로 규칙을 만들어 적용할 수도 있다. 

## 기존 내장 규칙을 커스텀하여 사용하는 방식

여기서는 인텔리제이 플러그인을 이용한 방식을 중심으로 설명하겠다. 

특정 프로젝트에 한하여 커스텀 룰셋을 만들어 적용하고자 하는 경우, 프로젝트 폴더 루트에 `ruleset.xml` 파일을 만들어 커스텀하면 되겠다. 필자의 경우, 프로젝트 루트에 `config/pmd/ruleset.xml` 경로를 만들었다. 해당 파일 내부에는 다음과 같은 형태를 띈다.

```xml
<?xml version="1.0"?>
<ruleset name="Custom Ruleset for auth token security study project"
         xmlns="http://pmd.sourceforge.net/ruleset/2.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://pmd.sourceforge.net/ruleset/2.0.0
         http://pmd.sourceforge.net/ruleset_2_0_0.xsd">

    <description>커스텀 규칙 모음</description>

    <rule ref="category/java/design.xml/CyclomaticComplexity">
        <properties>
            <property name="methodReportLevel" value="7" />
        </properties>
    </rule>

    <rule ref="category/java/codestyle.xml/LongVariable">
        <properties>
            <property name="minimum" value="10" />
        </properties>
        <priority>4</priority>
    </rule>

    <rule ref="category/java/bestpractices.xml/SystemPrintln">
        <priority>1</priority>
    </rule>

</ruleset>
```

코드 3-1. 커스텀 룰셋 작성 예시

`<ruleset>` 이라는 태그가 root tag가 되며, 그 안에는 해당 룰셋에 대한 설명을 적어놓는 `<description>` 과, 특정 룰을 기술하는 `<rule>` 태그들로 구성될 수 있다. 위 코드는 필자가 예시로 만들어본 코드이다. 주로 PMD 내장 규칙들을 가져와 커스텀하는 방식으로 구성되어 있다. 

앞선 사진으로 살펴본 `LongVariable` 과 같은 내장 규칙들은 `<rule ref="category/java/{rule-category}/{rule-name}">` 과 같은 형식으로 참조할 수 있다. 그리고 해당 규칙에서 제공하는 속성들을 커스텀하고자 하는 경우 `<rule>` 태그 안의 `<properties>` 내부에 `<property>` 태그를 통해 값을 조정할 수 있다. 위 코드에서는 `LongVariable` 내장 규칙에 대해, 허용가능한 변수명 길이를 조절하는 `minimum` 속성의 값을 원래 기본값인 17에서 10으로 조정해보았다. 

그리고 `<rule>` 내부에는 `<priority>` 태그를 통해 해당 규칙의 우선순위를 내 마음대로 조정할 수도 있다. 

위와 같이 커스텀 룰셋을 모두 작성하였다면 이제 이를 등록, 사용해볼 차례이다. 앞서 소개한 intelliJ pmd plugin이 설치되어있다는 전제하에, “파일” ⇒ “설정” ⇒ “도구” ⇒ “PMD”로 차례대로 들어간다. 그러면 다음 사진과 같은 화면이 보일 것이다. 

![사진 5-1. 인텔리제이에서의 커스텀 룰셋 적용 과정](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/10.png)

사진 5-1. 인텔리제이에서의 커스텀 룰셋 적용 과정

위 사진과 같이 “RuleSets”라 되어있고, 그 아래에 플러스(`+`) 버튼이 있는데, 이를 클릭한다. 그러면 새로운 창이 뜨는데, “Browse” 클릭 후 뜨는 창에서 앞서 만든 커스텀 룰셋 파일을 지정한다. 

![사진 5-2. 인텔리제이에서의 커스텀 룰셋 적용 과정](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/11.png)

사진 5-2. 인텔리제이에서의 커스텀 룰셋 적용 과정

그 후 “적용”, “확인” 버튼을 누른다. 혹시라도 해당 커스텀 룰셋이 적용되지 않았다면 IntelliJ IDE 창을 껐다가 켜보자. 

만약 에러 메시지가 뜨거나 하여 해당 룰셋을 적용할 수 없는 경우, 룰셋 XML 파일 내부에 문법 오류가 있는지 확인해본다. 필자의 경우에도 처음에는 에러 메시지가 뜨면서 적용이 되질 않았는데, 알고 보니 xml 파일 내에 문법을 틀리게 작성하거나, 오타가 발생하여 의도치 않게 존재하지 않는 내장 규칙을 참조하려고 해서 벌어진 일이었다. 

그 후 앞서 Intellij 플러그인 설치 방법에서 보였듯, PMD를 적용하고자 하는 패키지를 우클릭 후 “Run PMD” 항목에 마우스를 가져다 대면 아까와 달리 “Custom” 항목이 활성된다. 해당 항목에 마우스를 가져다 대면 앞서 등록한 커스텀 룰셋 파일명이 항목으로 보인다. 이를 클릭하면 해당 커스텀 룰셋으로 PMD 코드 분석이 실행된다. 

![사진 5-3. 커스텀 룰셋 실행 방법.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/12.png)

사진 5-3. 커스텀 룰셋 실행 방법.

잠시 기다리면 다음과 같이 PMD 분석 결과를 얻을 수 있다. 

![사진 5-4. 커스텀 룰셋 실행 결과](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/13.png)

사진 5-4. 커스텀 룰셋 실행 결과

## 새로운 규칙을 만들어 적용하는 방식

바로 이전 챕터에서는 PMD에서 기본으로 제공하는 내장 규칙들을 이용하여 이를 커스텀하는 방법에 대해 알아보았다. 반면, 개발자가 처음부터 직접 새로운 규칙을 작성하여 적용할 수 있다고 한다. 이를 위해서는 먼저 PMD가 어떻게 소스 코드와 룰셋이 담긴 xml 파일을 이용하여 규칙을 위배하는 소스 코드를 찾아낼 수 있는지 그 원리부터 간단히 알아가는 것이 좋겠다. 그 다음, 실제로 규칙을 만들 때 최대한 편리하게 만들 수 있도록 하기 위해 PMD에서 제공하는 “Rule Designer”라는 도구를 이용하여 규칙을 만들어가는 방법에 대해 살펴보고자 한다. 

### PMD 규칙이 자바 언어에 적용되는 원리: AST(Abstract Syntax Tree)

개발자가 어떠한 앱을 만들기 위해 소스 코드를 작성할 때, 개발자 입장에서는 소스 코드는 전부 텍스트로 구성되어 있다. 하지만 컴파일러와 같은 컴퓨터의 입장에서는 이 텍스트로만 구성되어 있는 소스 코드를 분석할 줄 알아야 개발자가 의도한 앱으로 빌드할 수 있을 것이다. 따라서 컴퓨터의 입장에서는 소스 코드의 구조적인 의미를 파악해야 한다. 이를 위해 컴퓨터는 소스 코드에 존재하는 각종 문법들을 추출하여 이들을 트리 구조로 구성하는 과정을 거친다고 한다. 이 과정에서 사용되는 자료구조가 바로 AST(Abstract Syntax Tree, 추상 구문 트리)라고 한다. 예를 들어, 클래스가 정의되어 있고, 그 안에 메서드가 정의되어 있다면, 이를 트리 구조로 나타내본다면 각각 클래스와 메서드를 노드로 하고, 클래스 노드를 부모 노드로 하고 그 아래에 메서드를 자식 노드로 하는 트리 구조를 만들 수 있다. 

추상 구문(Abstract Syntax)과 연관된 개념으로 Concrete syntax가 있는데 이 둘의 차이는 추상 구문에서는 프로그래밍 언어에서 자주 볼 수 있는 괄호, 세미콜론 등의 기호들을 생략함으로써 의미만을 남기도록 표현하고 concrete syntax에서는 이들을 포함한다는 것이다. Concrete syntax로 트리 구조를 구성한 것을 Parse tree라고 부른다. 

<div class="single-image">
  <img width="50%" src="/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/14.png" alt="image">
  <p>참고 사진 1-1. 코드가 주어졌을 때 코드를 구성하는 문법들을 AST로 구성한 예시. 출처: <a href="https://ko.wikipedia.org/wiki/%EC%B6%94%EC%83%81_%EA%B5%AC%EB%AC%B8_%ED%8A%B8%EB%A6%AC" target="_black">https://ko.wikipedia.org/wiki/추상_구문_트리</a> , By Dcoetzee - 자작, CC0, <a href="https://commons.wikimedia.org/w/index.php?curid=14676451" target="_black">https://commons.wikimedia.org/w/index.php?curid=14676451</a></p>
</div>

PMD에서도 마찬가지로, 소스 코드를 AST로 구성한 후, 마치 정규표현식을 이용하여 특정 패턴과 일치하는 문자열을 찾듯이, 규칙에 해당하는 노드들을 AST에서 찾는 방식으로 동작한다고 한다. 

### 새로운 규칙을 만드는 방법들

PMD에서는 새로운 규칙을 만들 때 사용하는 방법으로 2가지 방법을 제시한다. 

1. XPath query를 이용하는 방식.
2. Java visitor를 이용하는 방식.

XPath는 XML 문서에서 특정 요소, 속성 등을 경로 표현식을 이용하여 지정할 때 사용되는 언어이다. Markup 언어에 적용할 수 있어서 HTML에도 적용할 수 있다고 한다. XPath는 XML 문서 내 특정 요소 또는 속성을 찾을 때 DOM tree를 순회하는 방식으로 찾는다고 하며, 이는 앞서 설명한 AST를 이용한 PMD의 원리와도 거의 똑같다고 볼 수 있겠다. 

예를 들어 다음과 같은 HTML 코드가 있다고 할 때,

```html
<div id="language">
  <h4>Language</h6>
  <ul>
    <li><a href="index.html">KR</a><img src="images/korean-flag.png" width="30"></li>
    <li><a href="main_eng.html">US</a><img src="images/american-flag.png" width="30"></li>
  </ul>
</div>
```

코드 4-1. HTML 코드 예시.

XPath를 이용하여 특정 요소들을 찾는 예시는 다음과 같다. 

- 문서 내 모든 `<li>` 요소들을 찾고자 할 경우의 XPath query: `//li`
- 또는 `<div>` 요소를 root 노드로 할 때 해당 노드 내부에 있는 모든 `<ul>`  내 `<a>` 요소들을 찾고자 할 때의 XPath query: `div/ul/li/a`
- 속성(attribute) 이름이 “width”인 `<img>` 요소들을 문서 내에서 모두 찾고자 할 경우의 XPath query: `//img[@width]`
- 속성(attribute) 이름이 “width”이며, 그 값이 “30”인 `<img>` 요소들을 문서 내에서 모두 찾고자 할 경우의 XPath query: `//img[@width="30"]`

PMD에서는 이와 똑같은 XPath 문법을 이용하여 룰에 해당하는 코드를 찾는 방법을 제공하는 것이다. PMD에서는 XPath를 이용할 때 AST를 XML의 DOM tree로 취급하여 순회한다고 한다. 즉, 모든 AST 내 노드들이 XPath 방법론에서는 XML element로 취급하는 것이다. 

한 편, PMD에서는 자바 언어로 규칙을 작성하는 방법도 제공한다. 이를 위해 PMD에서 여러 자바 API를 제공하기에 이를 이용하여 작성하기만 하면 된다고 한다. 

다만 PMD 공식에 따르면, XPath를 이용한 방식이 가장 쉽다고 한다. Java 언어로 규칙을 작성하는 방식은 XPath 방법으로는 불가능한 규칙을 만들 때에만 사용하는 것을 권고하고 있다. 

다음 챕터에서는 자바 언어를 이용하는 방식보다는 XPath를 이용하여 새로운 규칙을 작성하는 방법에 대해 소개할 것이다. 다만, 아무런 도구도 없이 XPath 쿼리문을 작성하는 것은 매우 힘들기에 Rule Designer라는 GUI 도구와 함께 사용하는 방법에 대해 살펴보도록 하겠다. 

### Rule Designer를 이용하여 좀 더 쉽게 규칙 만들기

Rule Designer는 XPath 기반 규칙을 생성하는데에 도움을 주는 GUI 도구이다. 

이 도구를 사용하려면 먼저 JavaFX라는 도구가 설치되어야 한다. JavaFX는 간단히 말해, Java 진영에서의 데스크톱 앱 제작 프레임워크라 보면 되겠다. Python의 PyQt, PySide, Tkinter와 JS 진영의 Electron과 비슷하다고 보면 되겠다. 

1. [https://gluonhq.com/products/javafx/](https://gluonhq.com/products/javafx/) 이 사이트에 방문하여 자신의 기기에 설치되어 있는 JDK 버전, OS 등에 맞는 JavaFX를 선택하여 zip 파일 형태의 JavaFX를 다운로드 받는다. 
    
    ![사진 6-1. JavaFX zip 파일 다운로드 받는 곳.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/15.png)
    
    사진 6-1. JavaFX zip 파일 다운로드 받는 곳.
    
2. 다운로드받은 zip 파일을 원하는 곳에 압축해제한다. 폴더명은 보통 `{...}/javafx-sdk-21.0.10` 과 같은 형태로 끝맺을 것이다. 
3. 해당 폴더에 대해 환경변수를 등록한다. 먼저 다음 사진처럼 “Path” 환경변수에 해당 폴더 경로를 등록한다. 단, 다른 환경변수들과는 달리 JavaFX에 대해선 bin 폴더가 아닌 루트 폴더 그 자체 (`{…}/javafx-sdk-21.0.10`)를 환경변수를 넣는다. 
    
    ![사진 6-2. JavaFX를 “Path” 환경변수에 추가하였다.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/16.png)
    
    사진 6-2. JavaFX를 “Path” 환경변수에 추가하였다.
    
4. 그 후, “JAVAFX_HOME” 환경변수를 새로 만들어 거기에도 앞선 폴더 경로를 입력한다. 단, 여기서 주의할 점이 있는데, 만약 폴더 경로에 띄어쓰기와 같은 공백이 있는 경우, 해당 경로 자체를 아래 사진처럼 따옴표(`” “`)로 감싸줘야 한다. 그렇지 않으면 폴더 경로를 제대로 인식하지 못한다. 
    
    ![사진 6-3. JAVAFX_HOME이라는 환경변수를 등록.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/17.png)
    
    사진 6-3. JAVAFX_HOME이라는 환경변수를 등록.
    
5. 모두 완료하고, 새 터미널창을 킨 다음 `pmd.bat designer` 라고 입력한다. 그러면 터미널창에는 다음과 같은 형식의 메시지가 뜰 것이고,
    
    ```bash
    > pmd.bat designer
    PMD Rule Designer 7.19.2 (with PMD 7.22.0) initializing...
    done in 1697ms.
    Run with --verbose parameter to enable error output.
    ```
    
    코드 5-1. 
    
    그와 동시에 다음과 같은 GUI 툴이 뜨게 된다. 
    
    ![사진 6-4. Rule Designer 첫 화면](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/18.png)
    
    사진 6-4. Rule Designer 첫 화면
    

참고로, 앞선 `JAVAFX_HOME` 환경변수 등록 시 폴더 경로에 공백이 있는 경우 이를 큰 따옴표로 묶지 않고 그냥 대입하면 나중에 터미널 창에서 `pmd.bat designer` 를 입력할 때 다음과 같은 에러 메시지가 뜨면서 desiger GUI 툴이 뜨지 않게 되니 주의해야한다.

```bash
> pmd.bat designer
WARNING: Unknown module: javafx.graphics specified to --add-opens
WARNING: Unknown module: javafx.fxml specified to --add-opens
WARNING: Unknown module: javafx.graphics specified to --add-opens
WARNING: Unknown module: javafx.base specified to --add-opens
WARNING: Unknown module: javafx.graphics specified to --add-opens
WARNING: Unknown module: javafx.controls specified to --add-opens
WARNING: Unknown module: javafx.graphics specified to --add-opens
WARNING: Unknown module: javafx.base specified to --add-opens
WARNING: Unknown module: javafx.graphics specified to --add-opens
WARNING: Unknown module: javafx.graphics specified to --add-opens
Error: Could not find or load main class Files
Caused by: java.lang.ClassNotFoundException: Files
```

코드 5-2. `JAVAFX_HOME` 환경변수에 대입할 경로에 공백이 있고, 해당 경로를 큰 따옴표로 묶지 않은 상태로 등록한 후 터미널 창에서 designer 툴을 킬려고 할 때 발생하는 에러 메시지. 필자의 경우 폴더 경로에 “C:\Program Files\…”와 같이 공백이 존재하는데, JavaFX에서는 공백을 기준으로 나눈 뒤 뒤에 나온 “Files”라는 경로를 자바 클래스 파일로 착각하여 위와 같은 에러 메시지가 나온 것으로 보인다. 

설치를 완료했으니 이 designer 툴을 이용하여 새로운 규칙을 정의하는 예시를 살펴보도록 하겠다. 

예를 들어, controller 계층에서 직접 entity 객체를 생성 또는 사용하면 안된다는 규칙을 정의해보도록 하겠다. 이를 위해 우선 이 규칙에 위배되는 예시 코드를 자바 언어로 다음과 같이 준비한다.

```java
package com.jerocaller.controller;

import com.jerocaller.entity.User; // Entity를 직접 임포트함
import org.springframework.web.bind.annotation.*;

@RestController // 1. 컨트롤러임을 확인
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) { // 위반: 반환 타입이 Entity(User)
        return null;
    }

    @PostMapping("/save")
    public void saveUser(@RequestBody User user) { // 위반: 파라미터 타입이 Entity(User)
        User otherUser = new User("hi");  // 위반: 엔티티 객체 사용.
        // logic
   }
}
```

코드 5-3. 새 규칙 정의를 위해 특정 규칙 위배 코드 예시.

그 후 designer 툴에서 가운데에 위치한 “Source code”에 붙여넣는다. 그러면 다음과 같은 모습이 될 것이다.

![사진 6-5. 사용자 정의 규칙을 정의하기 위해 designer 툴을 사용하는 모습.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/19.png)

사진 6-5. 사용자 정의 규칙을 정의하기 위해 designer 툴을 사용하는 모습.

사진 오른편에는 자바 코드에 대한 AST 트리가 보여진다. 이 AST 트리에서 찾고자 하는 대상을 찾으면 된다. 이 예시의 경우에는 다음의 조건들이 모두 갖춰져야 하는 구조였다.

1. `import` 경로에 `.entity.` 경로가 포함되어 있으며,
2. 해당 클래스에 `~Controller` 로 끝나는 어노테이션이 붙어있어야 하며,
3. `.entity.` 경로 뒤에 오는 클래스 타입이 해당 클래스 내부에서 작성되어야 해당 룰 위배 조건에 충족된다. 

이 조건들을 토대로 AST 트리를 찾아보며 완성한 XPath 쿼리문은 다음과 같았다.

```xml
//ClassType[
  @SimpleName = //ImportDeclaration[contains(@ImportedName, '.entity.')]/substring-after(@ImportedName, '.entity.')
  and
  ancestor::ClassDeclaration/ModifierList/Annotation[ends-with(@SimpleName, 'Controller')]
]
```

코드 5-4. XPath 쿼리문 도출 결과

`ClassType` , 즉 위 자바 코드에서 작성된 클래스 타입들 중 

1. 그 타입의 이름이 `import` 구문에 `.entity.` 경로가 포함될 때 해당 경로의 뒤에 붙여진 클래스명과 동일하며(즉, Entity 객체이며) ⇒ `@SimpleName = //@ImportDeclaration[...]`
2. 해당 클래스 타입을 감싸고 있는 클래스가 `~Controller` 로 끝나는 어노테이션을 가질 경우

위 조건을 모두 만족하는 클래스 타입은 Entity 객체로 판정하겠다는 뜻이다. 이렇게 XPath 쿼리문을 도출하는데에 참고할만한 자료들은 다음과 같이 있으니 참고하면 되겠다.

- [https://www.w3schools.com/xml/xpath_syntax.asp](https://www.w3schools.com/xml/xpath_syntax.asp)  ⇒ XPath 기본 문법
- [https://www.saxonica.com/documentation12/index.html#!functions](https://www.saxonica.com/documentation12/index.html#!functions) ⇒ XPath에서 사용가능한 함수들의 목록. 위 쿼리문에서 사용된 `ends-with(...)`, `contains(...)` 등이 이에 해당한다.

이렇게 XPath 쿼리문을 완성하였으면 이전에 만들어뒀던 `ruleset.xml` 파일에 적용할 때이다. 다음과 같은 형태로 룰을 작성하여 기존 xml 파일에 추가하면 되겠다. 

```xml
<rule name="DontUseEntityInController"
  message="컨트롤러 레이어에서 엔티티 객체를 사용하지 말 것."
  class="net.sourceforge.pmd.lang.rule.xpath.XPathRule"
  language="java"
>
  <description>
    Don't use or create entity instances directly in controller layers.
  </description>
  <priority>2</priority>
  <example>
<![CDATA[
package com.jerocaller.controller;

import com.jerocaller.entity.User; // Entity를 직접 임포트함
import org.springframework.web.bind.annotation.*;

@RestController // 1. 컨트롤러임을 확인
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable Long id) { // 위반: 반환 타입이 Entity(User)
      return null;
    }

    @PostMapping("/save")
    public void saveUser(@RequestBody User user) { // 위반: 파라미터 타입이 Entity(User)
      User otherUser = new User("hi");  // 위반: 엔티티 객체 사용.
      // logic
    }
}
]]>
  </example>
  <properties>
    <property name="xpath">
      <value>
<![CDATA[
    //ClassType[
        @SimpleName = //ImportDeclaration[contains(@ImportedName, '.entity.')]/substring-after(@ImportedName, '.entity.')
        and
        ancestor::ClassDeclaration/ModifierList/Annotation[ends-with(@SimpleName, 'Controller')]
    ]
]]>
      </value>
    </property>
  </properties>
</rule>
```

코드 5-5. 

그 후, 인텔리제이에서 PMD를 실행해보면 위에 작성한 규칙에 위배되는 코드가 발견되면 다음과 같은 결과를 볼 수 있게 된다. 

![사진 6-6. XPath를 이용한 새 규칙 작성 후 PMD를 돌린 결과](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/20.png)

사진 6-6. XPath를 이용한 새 규칙 작성 후 PMD를 돌린 결과

즉, 앞서 작성한 자바 예시 코드는 xml 파일 내에서 `<example>` 태그 내에 작성하면 되고, 앞선 Rule Designer를 이용하여 추출한 Xpath query문은 `<property name="xpath">` 태그 내의 `<value>` 태그 내에 삽입하면 된다. 

<details id="ref-1">
<summary>
    <strong>
        <i>참고 - ArchUnit으로 아키텍처 검사하기</i>
    </strong>
    <a href="#ref-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
위에서는 PMD로 어디까지 규칙을 작성할 수 있는지를 확인해보기 위해 일부러 레이어드 아키텍처 위배 여부를 사례로 적용해보았다. 그런데 사실 아키텍처 위반 여부는 이 PMD보다는 [ArchUnit](https://www.archunit.org/)이라고 하는 라이브러리를 이용하는 것이 더 수월해보일 것으로 보인다. 이 라이브러리는 프로젝트에서 사용되고 있는 아키텍처에 대해 개발자가 이 아키텍처를 준수하면서 코딩을 하고 있는지를 검사할 수 있는 라이브러리라고 한다. 이 검사는 unit test 형태의 테스트 코드를 통해 수행할 수 있어 이미 JUnit 등의 자바 진영의 테스트 라이브러리들에 익숙한 사람이라면 쉽게 아키텍처 검사 테스트 로직을 작성할 수 있다고 한다. 
</div>
</details>
    

# CPD(Copy - Paste Detector)

한 편, PMD에서는 지금까지 소개한 코드 스타일 규칙 검사 기능 뿐만 아니라, 프로젝트 전반에서 코드를 복사 및 붙여넣기한 곳(즉, 중복되는 코드)이 있는지도 검사해주는 CPD(Copy - Paste Detector) 기능도 별도로 제공하고 있다. 기존에는 프로젝트 소스코드의 품질을 높이려는 목적으로 중복되는 코드가 있는지 확인하기 위해 모든 소스 코드를 다 뒤져봐야했다면, 이 CPD라는 도구를 이용하면 자동으로 중복되는 위치를 탐지해주기에 매우 편리하다고 볼 수 있는 도구이다. 이 기능도 역시 CLI와 GUI 버전 모두 사용할 수 있다. 

먼저 CLI 버전으로 살펴보겠다. 윈도우 기준이니 참고. 명령 프롬프트 창을 열고, 검사하고자 하는 프로젝트 폴더 위치로 이동한다. 그 후, `pmd.bat cpd` 로 시작하는 명령어를 이용하여 코드가 중복되는 곳들을 검사할 수 있다. 

상황에 따라 명령어에 쓰일 수 있는 옵션들이 다양하므로 우선 각각의 예시 명령어들을 실행한 결과부터 살펴보도록 하겠다. 

- `pmd.bat cpd --minimum-tokens 60 --dir src/main/java --dir src/test/java` ⇒ 현재 폴더 기준으로 `src/main/java` 및 `src/test/java` 에 해당하는 폴더들을 찾아 그 내부에 있는 소스 코드들을 검사한다.
    
    ![사진 7-1. 코드가 중복되는 곳들이 있으면 위 사진과 같이 콘솔창에 출력된다. 정확히 어떤 파일에서 어떤 코드의 위치에서 중복이 발생하는지를 알려준다. 만약 중복되는 곳이 없다면 아무것도 출력되지 않는다. ](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/21.png)
    
    사진 7-1. 코드가 중복되는 곳들이 있으면 위 사진과 같이 콘솔창에 출력된다. 정확히 어떤 파일에서 어떤 코드의 위치에서 중복이 발생하는지를 알려준다. 만약 중복되는 곳이 없다면 아무것도 출력되지 않는다. 
    
    - `--minimum-tokens` : 중복되는 코드의 길이(토큰)를 설정한다[^1]. 반드시 명령어에 넣어야 하는 옵션으로, 적어도 이 토큰 길이와 같거나 긴 중복 코드들을 탐색한다. 이 토큰 길이를 짧게 할수록 더 많은 중복 코드들을 잡아낼 수 있지만, 너무 짧게 잡으면 언어 자체에서 사용하는 문법 키워드들(예: `public class ...` , `public static void ...` 등)도 중복으로 오탐지할 수 있기에 적당한 크기로 설정하는 것이 좋다. 보통은 100을 기준으로 하고, 여기서 더 엄격하게 할 것인지 느슨하게 할 것인지에 따라 토큰 길이를 증감시켜가면서 적용하면 되겠다.
    - `--dir` : 검사 대상이 될 폴더를 지정한다. 검사 대상이 될 폴더가 두 군데 이상일 경우 나란히 나열하여 작성하면 된다.
- `pmd.bat cpd --minimum-tokens 60 --dir src/main/java --dir src/test/java --report-file ./pmd.txt` ⇒ `src/main/java` 및 `src/test/java` 폴더 대상으로 검사한 결과를 현재 폴더의 `pmd.txt` 파일에 저장.
    - `--report-file` : 검사 결과를 저장할 파일명을 지정한다. 만약 해당 경로에 해당 파일이 없다면 새로 하나 만들어서 기록하고, 이미 존재한다면 그 파일 내용을 덮어쓴다. 이 옵션은 PMD 7.14.0 버전부터 사용할 수 있으니 주의.
- `pmd.bat cpd --minimum-tokens 60 --dir src/main/java --dir src/test/java --report-file ./pmd.md --format markdown` ⇒ 두 폴더 내부의 소스 코드들을 대상으로 중복 검사한 결과를 현재 폴더의 `pmd.md` 파일에 마크다운 포맷으로 저장.
    - `--format` : 검사 결과를 저장할 파일 포맷을 지정한다. 지정하지 않으면 `text` 가 기본이다. 지원하는 포맷 목록은 [https://docs.pmd-code.org/latest/pmd_userdocs_cpd_report_formats.html](https://docs.pmd-code.org/latest/pmd_userdocs_cpd_report_formats.html) 사이트를 참고.
- `pmd.bat cpd --minimum-tokens 60 --dir src/main/java --dir src/test/java --report-file ./pmd-detail.md --format markdown --ignore-literals --ignore-identifiers --ignore-annotations` ⇒ 검사 시 변수와 같은 식별자의 이름, 상수, 어노테이션 이름은 무시하여 중복 검사를 실행하고, 그 결과를 마크다운 파일로 저장한다.
    - `--ignore-literals` : 숫자, 문자열과 같은 리터럴 값들을 무시하고 중복 검사를 실행한다. 예를 들어 두 코드에 각각 `int a = 10` 과 `int a = 20` 이 있다면 변수 `a` 의 값이 무엇이든 무시하고 검사한다.
    - `--ignore-identifiers` : 클래스, 메서드, 변수, 상수 등에 쓰이는 식별자 이름들을 무시하고 중복 검사를 진행한다. 예를 들어 두 코드에 각각 `int a = 10`, `int b = 10` 이 있다면 변수 이름인 `a` , `b` 는 서로 같다고 가정하고 중복 검사를 실행한다.
    - `--ignore-annotations` : 자바에서 사용되는 `@` 로 시작되는 어노테이션의 이름을 무시하고 중복 검사를 실행한다. 예를 들어 두 코드에 각각 `@AnnoOne` , `@AnnoTwo` 라는 어노테이션이 쓰여 그 이름이 서로 다르다고 하더라도, 코드 구조가 똑같다면 이 두 코드를 중복이라고 판별한다.
    - 이 `--ignore` 로 시작하는 옵션들은 특정 언어들에서만 지원하는데, 위 3개의 옵션들은 Java 뿐만 아니라 C, C#, C++ 과 같은 몇몇 언어에서도 지원한다. 검사 대상 언어에 따라 적용할 수 있는 옵션이 다르니 이에 대한 자세한 사항은 [https://docs.pmd-code.org/latest/pmd_userdocs_cpd.html](https://docs.pmd-code.org/latest/pmd_userdocs_cpd.html) 이 문서를 참고.
    - 또한 `--ignore` 로 시작하는 옵션들을 사용하지 않으면 코드 구조 자체는 서로 중복되어도 그 안에 있는 변수, 상수등의 이름만 다르다는 이유로 중복으로 판별되지 않을 수 있는데, 이 옵션을 적용하면 이 이름들도 무시하기에 좀 더 코드 구조적인 측면에서의 중복을 잡아내기에 좋다.

한 편, 이 CPD 기능을 똑같이 GUI 환경에서도 사용할 수 있다. 이를 사용하려면 먼저 앞서 “Rule Designer” 도구를 소개했을 때처럼, JavaFX가 미리 설치되어 있어야 한다. 윈도우 기준으로 명령어 창에 `pmd.bat cpd-gui` 명령어를 친다. 그러면 다음과 같은 GUI 프로그램이 자동으로 뜬다. 

![사진 7-2. CPD GUI 프로그램 화면.](/images/2026-04-28/tools-pmd-cpd-소스%20코드%20분석%20도구/22.png)

사진 7-2. CPD GUI 프로그램 화면.

“Root source directory”에서 코드 중복 검사를 진행할 폴더 위치를 선정한다. 그 아래에 여러 옵션들을 설정한 후, 화면 우상단에 있는 “Go” 버튼을 클릭하면 화면 하단에 코드가 중복되는 파일들과 그 위치, 코드가 화면에 보이게 된다. 이러한 방식으로 쉽게 코드 중복 검사를 실행할 수 있게 된다. 

<div class="notice--primary" markdown="1">
💡이 글에서 사용된 예제 프로젝트 소스 코드는 다음의 사이트를 참고하시길 바랍니다. 

[https://github.com/JeroCaller/spring-boot-study-with-intellij/tree/master/AuthTokenSecurity](https://github.com/JeroCaller/spring-boot-study-with-intellij/tree/master/AuthTokenSecurity)

</div>

---

References

[1] pmd 공식 문서

[PMD](https://pmd.github.io/)

[2] [Floating Chat Icon](https://chamomile.lotteinnovate.com/guides/web-develop/)

[3] pmd github

[https://github.com/pmd/pmd](https://github.com/pmd/pmd)

[4] [[IT기사]개발자가 권장하는 Java 코드 품질 도구](https://velog.io/@been/IT%EA%B8%B0%EC%82%AC%EA%B0%9C%EB%B0%9C%EC%9E%90%EA%B0%80-%EA%B6%8C%EC%9E%A5%ED%95%98%EB%8A%94-Java-%EC%BD%94%EB%93%9C-%ED%92%88%EC%A7%88-%EB%8F%84%EA%B5%AC#pmd)

[5] [[JAVA] 프로젝트에서 PMD를 이용한 소스 품질 검사](https://tae-hui.tistory.com/entry/JAVA-%ED%94%84%EB%A1%9C%EC%A0%9D%ED%8A%B8%EC%97%90%EC%84%9C-PMD%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%86%8C%EC%8A%A4-%ED%92%88%EC%A7%88-%EA%B2%80%EC%82%AC)

[6] 사용자 정의 룰 작성

[egovframework:dev2:imp:inspection:customrule    [eGovFrame]](https://www.egovframe.go.kr/wiki/doku.php?id=egovframework:dev2:imp:inspection:customrule)

[7] Xpath 개념 및 문법

[W3Schools.com](https://www.w3schools.com/xml/xpath_syntax.asp)

[8] ArchUnit - 자바 아키텍처 검사 라이브러리 소개

[NAVER D2](https://d2.naver.com/helloworld/9222129)

[9] ArchUnit 공식 홈페이지

[Unit test your Java architecture](https://www.archunit.org/)

[10] IntelliJ pmd plugin에서 커스텀 룰셋 추가 방법

[IntelliJ PMDPlugin custom ruleset](https://stackoverflow.com/questions/36222643/intellij-pmdplugin-custom-ruleset)

[11] PMD rule designer GUI 툴 설치 과정 설명.

[The rule designer \| PMD Source Code Analyzer](https://docs.pmd-code.org/latest/pmd_userdocs_extending_designer_reference.html)

[12] XPath 쿼리문에 사용 가능한 함수 목록.

[Saxon 12 Documentation](https://www.saxonica.com/documentation12/index.html#!functions)

[13] AST에 관한 설명.

[추상 구문 트리(AST)란?](https://velog.io/@hbsps/%EC%B6%94%EC%83%81-%EA%B5%AC%EB%AC%B8-%ED%8A%B8%EB%A6%ACAST%EB%9E%80)

[14] AST에 관한 설명.

[[CS: 운영체제] 컴파일러](https://29223.tistory.com/113)

[15] AST에 관한 설명.

[구문 트리 - 나무위키](https://namu.wiki/w/%EA%B5%AC%EB%AC%B8%20%ED%8A%B8%EB%A6%AC)

[16] Xpath

[XPath](https://ko.wikipedia.org/wiki/XPath)

[17] Xpath

[XPath의 이해 (초급편)](https://hahahax5.tistory.com/2)

[18] 길벗알앤디, “2026 시나공 기출문제집 정보처리기사 필기”, p44 ~ 45

---

[^1]: 여기서의 토큰은 단순히 문자 한 글자가 아닌 문법적으로 어떠한 의미를 가질 수 있는 최소한의 문자열이라고 보면 되겠다. 예를 들어 `public class {int a}` 라는 코드가 있다면 이를 각각 `public`, `class` , `{` , `int` , `a` , `}` 와 같은 토큰으로 나눌 수 있겠다.