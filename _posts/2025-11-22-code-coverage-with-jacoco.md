---
title: "Code coverage 측정 with Jacoco"
category: "Spring"
tag: ["Code Coverage", "Test", "Java", "Jacoco", "Gradle", "Spring"]
---

# Code coverage

코드 커버리지(Code coverage)는 테스트 코드가 소스 코드를 얼마나 실행 및 테스트하는지를 측정하는 지표이다. 

만약 소스 코드를 작성하여 총 5개의 기능을 구현했는데, 정작 테스트 코드는 이 중 3개의 기능에 대해서만 다루고 있다면 나머지 2개의 기능에서는 어떤 버그가 발생할지 모르는 일이다. 더군다나 프로젝트의 규모가 커 소스 코드의 길이가 길거나 모듈이 아주 많다면 테스트 코드는 어디서부터 어디까지의 소스코드를 다루고 있는지조차 수동으로 파악하기가 힘들 것이다. 이러한 측면에서 볼 때 테스트 케이스 자체의 통과 여부뿐만 아니라 소스 코드의 얼마나 많은 부분을 테스트 코드가 커버하고 있는지 보는 것도 중요하겠다. 

일반적으로 코드 커버리지를 측정하는 기준은 다음과 같이 여러 가지가 존재하는 것으로 알려져 있다. 

- Statement coverage : 프로그램에서 얼마나 많은 명령문(문장)들이 테스트를 통해 수행되었는지. 실행된 문장 수를 전체 문장 수로 나누면 얻을 수 있다.
- Line coverage : 얼마나 많은 소스코드의 라인들이 테스트되었는지. 실행된 소스코드 라인 수에서 전체 소스코드 라인 수를 나눠 얻을 수 있다.
- Branch(Edge) coverage : if~else, switch와 같은 조건문들에서 얼마나 많은 분기들이 테스트되었는지. 실행된 분기 수를 전체 분기 수로 나누면 얻을 수 있다.
- Function coverage : 전체 함수들 중 얼마나 많은 함수들이 테스트에 의해 실행되었는지. 실행된 함수들의 수를 전체 함수들의 수로 나누면 얻을 수 있다.

이 외에도 여러 가지 존재한다. 이러한 코드 커버리지를 높이면 높일수록, 즉 소스 코드의 많은 부분을 테스트 코드 작성으로 커버할수록 소스 코드의 많은 부분에서 버그, 에러가 날 가능성을 테스트 단계에서 검증한다는 뜻이고, 이는 실제 프로덕션 배포 시 예기치 않은 버그 및 에러를 만날 가능성을 줄여준다는 의미이기도 하다. 따라서 코드 커버리지 지표를 도입하고 이 수치를 높이는 것이 중요하다고 볼 수 있다. 

그러면 이러한 코드 커버리지 수치를 100% 달성하면 그만 아닐까? 물론 그러면 가장 좋겠지만, 현실적으로는 힘든 작업이다. 마감기한이 얼마 남지 않아 모든 테스트를 다 작성하기 힘든 시간적 한계가 있을 수도 있고, 무엇보다도 코드 커버리지 지수가 높은 것이 항상 코드 품질이 좋다는 것을 보장하지는 않기 때문이다. 

높은 코드 커버리지가 항상 코드 품질 보장을 하지 않는 이유

- 코드 커버리지는 말 그대로 소스 코드의 얼마나 많은 부분이 테스트 코드로 커버되고 있는지를 정량적으로 나타내는 지표이지, 코드의 품질을 무조건 보장해주는 지표는 아니다. 코드 커버리지에만 매몰될 경우, 코드 커버리지를 올리기 위해 불필요하거나 의미없는 테스트 코드를 작성할 가능성도 없지 않아 있다. 또한 코드 커버리지가 소스 코드의 가독성 및 유지보수성까지 대표한다고 보긴 어렵다. 코드 커버리지가 좋아도 막상 소스코드를 보면 가독성, 유지보수성이 안 좋을 수도 있는 것이다.
- 테스트에도 유닛테스트뿐만 아니라 통합 테스트 등 여러 가지 종류의 테스트가 있으므로, 코드 커버리지에만 집착하면 유닛테스트만 작성하여 코드 커버리지를 높이는 것에만 집중할 수 있으며 이는 높은 코드 커버리지를 달성하더라도 통합 테스트에서 발견할 수 있었을 버그 및 에러를 발견하지 못할 가능성으로 이어질 수 있는 것이다.
- 애초에 구현한 기능 자체가 기존의 기획서에 명시된 모든 요구사항을 다 구현하지 못한 경우에도 이를 코드 커버리지만으로는 알 수가 없을 것이다.
- 코드 커버리지는 테스트 자체의 성공 여부를 알려주지는 않는다. 테스트 성공 여부는 별도로 파악해야 한다.

즉, 코드 커버리지는 코드 품질을 지킬 수 있는 좋은 도구이지만 만능은 아니다. 따라서 어느 정도의 코드 커버리지는 향상 또는 유지가 필요하겠지만, 과도하게 코드 커버리지에만 몰두하지 않도록 주의하는 것도 필요하겠다. 

“[Google Testing Blog](https://testing.googleblog.com/2020/08/code-coverage-best-practices.html)”에 따르면, 모든 프로젝트에 천편일률적으로 적용하는 코드 커버리지 기준치는 없으므로 주로 비즈니스적 관점에서의 영향력 및 중요성, 코드의 유지 기간 및 복잡성 등의 여러 요소들을 기초로 판단하는 것이 좋다고 한다. 해당 글에서 구글 테스트 팀은 보통 코드 커버리지가 60% 이상이면 적절한 수준, 75% 이상이면 칭찬할만한 수준, 90%는 모범적인 수준임을 제시하였다. 필자가 따로 다른 자료들을 살펴봤을 때에도 보통 70~80%를 기준치로 잡는 것을 보았다. 

이러한 코드 커버리지는 사람이 수동으로 확인 및 계산하는 건 매우 어렵고, 이를 직접 자동화하는 것도 만만치는 않을 것이기에 이미 만들어진 외부 코드 커버리지 툴을 사용하여 측정한다. Java의 경우 대표적으로 Jacoco가 알려져 있는데, 이 글에서는 이 Jacoco에 대해 살펴보도록 하겠다. 

# Jacoco 개요

Jacoco는 Java Code Coverage의 약자로, 말 그대로 Java 진영에서 코드 커버리지 측정 도구로 사용될 수 있는 라이브러리이다. JVM 환경에서의 코드 커버리지 분석 도구이므로 자바 뿐만 아니라 JVM 기반 언어인 코틀린 등의 다른 언어에서도 사용 가능하다고 한다. 참고로 기존에 존재하던 자바 진영의 다른 코드 커버리지 도구들이 모두 명령어 창, 특정 IDE의 플러그인 등 특정 도구에만 특화되어 있기만 할 뿐 통합되어 있는 툴이 없어 Jacoco가 탄생되었다고 한다. 그래서 Jacoco는 다른 도구들과 통합하여 사용하기 쉽다고 한다. 

Jacoco의 기능 및 특징들은 다음과 같다.

- 자바 바이트 코드 명령어(Instruction), 분기별(branch), 라인, 메서드, 클래스 등 여러 기준에서의 코드 커버리지 측정 가능.
- 자바 바이트코드(bytecode)를 기반으로 작동한다. 따라서 계측을 위해 기존 소스 코드에 수정을 가하지 않아도 되며, 아예 소스 코드가 없어도(바이트코드가 기록된 `*.class` 확장자의 파일들만 있어도) 계측 가능하다.
- 어떤 프레임워크나 WAS를 사용하더라도 그에 상관없이 JVM 기반이면 코드 커버리지를 측정할 수 있다.
- 코드 커버리지 결과를 사람이 보기에 좋은 HTML 파일뿐만 아니라 분석에 용이한 XML, CSV 파일 형태로도 결과 보고서를 생성해준다.
- 런타임 환경에서 테스트 케이스를 실행 후 커버리지를 검사하는 방식으로 진행된다.
- 개발자가 원하면 코드 커버리지 통과 기준치를 설정할 수 있으며, 개발자가 설정한 기준치에 미달이면 빌드 자체가 안되도록 할 수 있다.
    - 이를 Github Actions와 같은 CI/CD 도구와 통합하여 사용하면 팀 협업 시 누군가가 코드 커버리지 기준치에 미달된 소스 코드를 push하더라도 실수로 main branch에 merge되지 않도록 방지하는 도구로 활용할 수 있다.

# Jacoco 사용해보기

이 챕터에서 보일 코드의 전체 소스 코드는 다음을 참고.

[https://github.com/JeroCaller/spring-boot-study-with-intellij/tree/master/JacocoStudy](https://github.com/JeroCaller/spring-boot-study-with-intellij/tree/master/JacocoStudy)

이 글에서는 gradle을 기준으로 한다. 

Jacoco 사용을 위해선 build.gradle 내부에 다음과 같이 jacoco 플러그인을 추가한다. 

```groovy
plugins {
    id 'java'
    id 'org.springframework.boot' version '3.5.7'
    id 'io.spring.dependency-management' version '1.1.7'
    id 'jacoco' // 추가
}
```

예제 1-1. build.gradle

그리고 아래에 `jacocoTestReport` 라는 task를 다음과 같이 정의한다. 

```groovy
tasks.named('test') {
    useJUnitPlatform()

    // finalizedBy : 현재 task 실행 후 다음에 실행할 task 지정.
    // 현재 task 실행의 성공 여부와 상관없이 무조건 다음 task 실행.\
    finalizedBy jacocoTestReport
}

jacocoTestReport {
    // dependsOn : 현재 task 실행 전 수행되어야 할 task 지정.
    // 이전 task가 성공해야 현재 task가 실행됨.
    dependsOn test

    reports {
        html.required = true;
    }

    // glob 패턴 정리.
    // '*/' -> 0개 이상의 글자와 부합. 파일명에 주로 사용.
    // '**/' -> 0개 이상의 글자와 부합하는 디렉토리와 매칭.
    getClassDirectories().setFrom(files(classDirectories.files.collect {
        fileTree(
            dir: it,  // 정확히 이렇게 작성한다. 없으면 jacoco 관련 task가 실행 안됨.
            excludes: [
                '**/*Application.class',
                '**/dto/*.class',
                '**/common/*.class'
            ]
        )
    }))
}
```

예제 1-2. build.gradle

위 코드에서는 build.gradle에 `jacocoTestReport` 라는 task를 정의하였다. 내부에는 우선 `test` 라는 이름의 task가 먼저 성공적으로 실행된 뒤에 현재 task가 그 후에 실행되도록 `dependsOn` 키워드를 통해 설정하였다. 그 후 `reports` 부분에서는 코드 커버리지 결과물을 어떤 형식으로 출력할 것인지 정한다. 여기서는 HTML 파일로 그 결과를 볼 수 있도록 설정하였다. 

그 다음 `getClassDirectories()` 로 시작하는 부분은 테스트 대상 소스 코드 중 코드 커버리지 계산에 제외시킬 클래스나 패키지를 지정하기 위해 사용되었다. 만약 코드 커버리지 대상에 제외하고자 하는 모듈 또는 패키지가 없다면 생략해도 무방하다. 위 코드에서는 보통 코드 커버리지로 산정할 필요가 없는 `~Application.java` 또는 DTO 클래스들을 제외한 것이다. 

Jacoco 사용을 위해 필자는 다음과 같이 임의의 코드를 작성하였다. 

```java
package com.jerocaller.JacocoStudy.service;

import com.jerocaller.JacocoStudy.common.UserGrade;
import com.jerocaller.JacocoStudy.dto.UserDto;
import org.springframework.stereotype.Service;

import java.util.List;

@Service
public class UserService {
    public UserDto addBonusByUserGrade(UserDto userDto) {
        if (userDto.getUserGrade() == null) {
            return userDto;
        }

        switch (userDto.getUserGrade()) {
            case UserGrade.BRONZE:
                userDto.addMoney(100);
                break;
            case UserGrade.SILVER:
                userDto.addMoney(200);
                break;
            case UserGrade.GOLD:
                userDto.addMoney(300);
                break;
        }

        return userDto;
    }

    public UserDto decideUserGrade(UserDto userDto) {
        if (userDto.getUserGrade() != null) {
            return userDto;
        }

        UserGrade userGrade;

        if (userDto.getExperiencePoint() <= 100) {
            userGrade = UserGrade.BRONZE;
        } else if (userDto.getExperiencePoint() <= 200) {
            userGrade = UserGrade.SILVER;
        } else {
            userGrade = UserGrade.GOLD;
        }

        return UserDto.builder()
            .username(userDto.getUsername())
            .email(userDto.getEmail())
            .experiencePoint(userDto.getExperiencePoint())
            .money(userDto.getMoney())
            .userGrade(userGrade)
            .build();
    }

    public int getTotalAllUsersMoney(List<UserDto> users) {
        int total = 0;

        for (UserDto userDto : users) {
            total += userDto.getMoney();
        }

        return total;
    }

    public int getTotalAllUsersExp(List<UserDto> users) {
        int total = 0;

        for (UserDto userDto : users) {
            total += userDto.getExperiencePoint();
        }

        return total;
    }
}

```

에제 1-3.

앞선 예제 1-3에 대한 테스트 코드를 다음과 같이 작성하였다. 

```java
package com.jerocaller.JacocoStudy.service;

import com.jerocaller.JacocoStudy.common.UserGrade;
import com.jerocaller.JacocoStudy.dto.UserDto;
import org.junit.jupiter.api.BeforeEach;
import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.context.annotation.Import;
import org.springframework.test.context.junit.jupiter.SpringExtension;

import static org.assertj.core.api.Assertions.assertThat;
import static org.junit.jupiter.api.Assertions.*;

@ExtendWith(SpringExtension.class)
@Import({UserService.class})
class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    void addBonusByUserGradeTest() {
        UserDto userDto = UserDto.builder()
            .username("kimquel")
            .email("kimquel@email.com")
            .build();

        userDto = userService.addBonusByUserGrade(userDto);
        assertThat(userDto.getMoney()).isEqualTo(0);
    }
}
```

예제 1-4.

위 테스트 코드를 실행하면 프로젝트 루트 폴더에서 `/build/reports/jacoco/test/html` 폴더에 `index.html` 파일이 생성될 것이다. 해당 파일을 열면 다음과 같은 결과를 볼 수 있다. 

![사진 1-1. 예제 1-4에 따른 jacoco HTML 첫 화면. instructions 기준 7%, branches 기준 6%의 코드 커버리지 달성](/images/2025-11-22/code-coverage-with-jacoco/1.png)

사진 1-1. 예제 1-4에 따른 jacoco HTML 첫 화면. instructions 기준 7%, branches 기준 6%의 코드 커버리지 달성

![사진 1-2. 사진 1-2에서 ‘Element’의 ‘JacocoStudy.service’ 클릭 시 나오는 화면. ‘JacocoStudy.service’라는 패키지 내 모든 클래스들에 대한 커버리지 결과를 보여주고 있다. ](/images/2025-11-22/code-coverage-with-jacoco/2.png)

사진 1-2. 사진 1-2에서 ‘Element’의 ‘JacocoStudy.service’ 클릭 시 나오는 화면. ‘JacocoStudy.service’라는 패키지 내 모든 클래스들에 대한 커버리지 결과를 보여주고 있다. 

![사진 1-3. 사진 1-2 화면에서 UserService 클래스 클릭 시 나오는 화면. 해당 클래스 내 모든 메서드들에 대한 코드 커버리지 결과가 나온다. ](/images/2025-11-22/code-coverage-with-jacoco/3.png)

사진 1-3. 사진 1-2 화면에서 UserService 클래스 클릭 시 나오는 화면. 해당 클래스 내 모든 메서드들에 대한 코드 커버리지 결과가 나온다. 

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
  <img width="60%" src="/images/2025-11-22/code-coverage-with-jacoco/4.png" alt="사진 1-4. 사진 1-3에서 특정 메서드 클릭 시 나오는 화면. 테스트 대상 코드 중 어느 부분이 테스트되었고 안되는지 여부를 파악할 수 있다. ">
  <p>사진 1-4. 사진 1-3에서 특정 메서드 클릭 시 나오는 화면. 테스트 대상 코드 중 어느 부분이 테스트되었고 안되는지 여부를 파악할 수 있다. </p>
</div>

위 사진에서 초록색 줄은 테스트로 커버되고 있는 코드, 빨간색 줄은 아직 테스트로 커버되지 않은 코드를 의미한다. 노란색 줄은 주로 조건문에서 색칠되며, 조건문에 의해 발생하는 모든 가능한 경우의 수를 테스트 코드로 커버하고 있다면 초록색, 부분만 커버되고 있는 경우에 노란색 줄이 칠해지는 것이다. 

```java
@ExtendWith(SpringExtension.class)
@Import({UserService.class})
class UserServiceTest {

    @Autowired
    private UserService userService;

    @Test
    void addBonusByUserGradeTest() {
        UserDto userDto = UserDto.builder()
            .username("kimquel")
            .email("kimquel@email.com")
            .build();

        userDto = userService.addBonusByUserGrade(userDto);
        assertThat(userDto.getMoney()).isEqualTo(0);

        userDto.setUserGrade(UserGrade.BRONZE);
        userDto = userService.addBonusByUserGrade(userDto);
        assertThat(userDto.getMoney()).isEqualTo(100);

        userDto.setUserGrade(UserGrade.SILVER);
        userDto = userService.addBonusByUserGrade(userDto);
        assertThat(userDto.getMoney()).isEqualTo(300);

        userDto.setUserGrade(UserGrade.GOLD);
        userDto = userService.addBonusByUserGrade(userDto);
        assertThat(userDto.getMoney()).isEqualTo(600);
    }
}
```

예제 1-5. 테스트 대상 메서드 내 모든 분기들에 대한 테스트 코드 작성

![사진 1-5. 예제 1-5에 따른 jacoco 결과. 사진 1-1과 비교해볼 때 instruction, branches 커버리지 비율이 모두 올랐다. ](/images/2025-11-22/code-coverage-with-jacoco/5.png)

사진 1-5. 예제 1-5에 따른 jacoco 결과. 사진 1-1과 비교해볼 때 instruction, branches 커버리지 비율이 모두 올랐다. 

<div class="single-image">
  <img width="50%" src="/images/2025-11-22/code-coverage-with-jacoco/6.png" alt="사진 1-6. 예제 1-5에 따른 특정 메서드 내 line에 따른 테스트 여부 결과. 사진 1-4에 비해 초록색 부분이 많아졌다. 이는 테스트된 코드의 범위가 늘어났다는 뜻이다. ">
  <p>사진 1-6. 예제 1-5에 따른 특정 메서드 내 line에 따른 테스트 여부 결과. 사진 1-4에 비해 초록색 부분이 많아졌다. 이는 테스트된 코드의 범위가 늘어났다는 뜻이다. </p>
</div>

## 코드 커버리지 통과 기준치 설정 (옵션)

`jacocoTestCoverageVerification` 라는 task를 추가하여 코드 커버리지 통과 기준치를 설정함으로써, 현재 프로젝트에서 작성된 테스트 코드들이 특정 코드 커버리지 기준치를 만족하는지 확인할 수 있다. 이 task는 필수로 작성해야하는 것은 아니다. 이러한 코드 커버리지 기준치 설정을 위해 다음과 같이 build.gradle에 `jacocoTestCoverageVerification` 이라는 task를 추가한다. 

```groovy
tasks.named('test') {
    useJUnitPlatform()

    // finalizedBy : 현재 task 실행 후 다음에 실행할 task 지정.
    // 현재 task 실행의 성공 여부와 상관없이 무조건 다음 task 실행.\
    finalizedBy jacocoTestReport
}

jacocoTestReport {
    // dependsOn : 현재 task 실행 전 수행되어야 할 task 지정.
    // 이전 task가 성공해야 현재 task가 실행됨.
    dependsOn test

    reports {
        html.required = true;
    }

    // glob 패턴 정리.
    // '*/' -> 0개 이상의 글자와 부합. 파일명에 주로 사용.
    // '**/' -> 0개 이상의 글자와 부합하는 디렉토리와 매칭.
    getClassDirectories().setFrom(files(classDirectories.files.collect {
        fileTree(
            dir: it,  // 정확히 이렇게 작성한다. 없으면 jacoco 관련 task가 실행 안됨.
            excludes: [
                '**/*Application.class',
                '**/dto/*.class',
                '**/common/*.class'
            ]
        )
    }))

    finalizedBy jacocoTestCoverageVerification
}

// 추가
jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = 0.30
            }
        }

        rule {
            enabled = true
            element = 'CLASS'

            limit {
                counter = 'LINE'
                value = 'COVEREDRATIO'
                minimum = 0.75
            }

            limit {
                counter = 'BRANCH'
                value = 'COVEREDRATIO'
                minimum = 0.75
            }
        }
    }
}
```

예제 2-1. build.gradle. 커버리지 기준치 설정 및 커버리지 검증에는 아무런 제외 대상을 설정하지 않은 경우.

```bash
Execution failed for task ':jacocoTestCoverageVerification'.
> A failure occurred while executing org.gradle.internal.jacoco.JacocoCoverageAction
   > Rule violated for bundle JacocoStudy: instructions covered ratio is 0.05, but expected minimum is 0.30
     Rule violated for class com.jerocaller.JacocoStudy.common.UserGrade: lines covered ratio is 0.00, but expected minimum is 0.75
     Rule violated for class com.jerocaller.JacocoStudy.JacocoStudyApplication: lines covered ratio is 0.00, but expected minimum is 0.75
     Rule violated for class com.jerocaller.JacocoStudy.dto.UserDto: lines covered ratio is 0.00, but expected minimum is 0.75
     Rule violated for class com.jerocaller.JacocoStudy.service.UserService: lines covered ratio is 0.08, but expected minimum is 0.75
     Rule violated for class com.jerocaller.JacocoStudy.service.UserService: branches covered ratio is 0.06, but expected minimum is 0.75

```

예제 2-1 작성 후 테스트 실행 후의 콘솔창. 

`jacocoTestReport` task에는 `~Application.class` , `dto` 패키지, `common` 패키지를 jacoco 제외 대상에 포함시켰으나 `jacocoTestCoverageVerification` 에는 제외 대상에 포함시키지 않았는데, 그 결과 커버리지 검증 기준에 해당 패키지 및 클래스들까지도 포함되어 위와 같이 에러 메시지에 포함되었다. 

해당 제외 대상들이 `jacocoTestCoverageVerification` 에도 포함시키도록 하려면 다음과 같이 해당 task에도 `excludes` 를 포함시켜야 한다.

```groovy
jacocoTestCoverageVerification {
    violationRules {
        rule {
            limit {
                minimum = 0.30
            }
        }

        rule {
            enabled = true  // 이와 같이 쉽게 해당 rule을 on, off 할 수 있다.
            element = 'CLASS'

            // 추가 부분.
            // 여기서는 경로(/)가 아닌 패키지 및 클래스명으로 작성해야함.
            // 즉 / 대신 마침표(.)를 사용해야함.
            excludes = [
                '**.*Application',
                '**.dto.*',
                '**.common.*'
            ]

            limit {
                counter = 'LINE'
                value = 'COVEREDRATIO'
                minimum = 0.75
            }

            limit {
                counter = 'BRANCH'
                value = 'COVEREDRATIO'
                minimum = 0.75
            }
        }
    }
}
```

예제 2-2. build.gradle. 

위 코드를 추가하고 다시 gradle을 빌드 및 테스트를 실행하면 다음과 같이 에러 메시지가 일부 변경된다. 

```bash
Execution failed for task ':jacocoTestCoverageVerification'.
> A failure occurred while executing org.gradle.internal.jacoco.JacocoCoverageAction
   > Rule violated for bundle JacocoStudy: instructions covered ratio is 0.05, but expected minimum is 0.30
     Rule violated for class com.jerocaller.JacocoStudy.service.UserService: lines covered ratio is 0.08, but expected minimum is 0.75
     Rule violated for class com.jerocaller.JacocoStudy.service.UserService: branches covered ratio is 0.06, but expected minimum is 0.75

```

예제 2-2 적용 후 실행 결과

위 에러 메시지에 `dto` , `common` 등의 패키지가 언급되지 않는 것을 보아, 커버리지 기준치 적용 대상에 잘 제외되었다는 것을 확인할 수 있다. 

한 편, 지금은 이전에 지정한 커버리지 기준치를 넘기지 못한 상황이다. 이로 인해 Gradle build에 실패한다. 이는 앞서 보인 콘솔 결과창 메시지들을 보면 확인할 수 있다. 만약 충분한 테스트 코드를 작성한다면 기존에 정한 코드 커버리지 기준치를 넘겨 Gradle 빌드에 성공한다. 

## `jacocoTestCoverageVerification` task 내에서 지정 가능한 코드 커버리지 기준들

`jacocoTestCoverageVerification` task 내 사용 가능한 속성들과 그 각각의 속성들에 사용 가능한 값들은 다음과 같다(앞선 예제 2-1, 2-2 참고). 

- `element` : 코드 커버리지 검사를 위해 필요한 범위 기준 설정
    - BUNDLE(Default) : 패키지 번들 (프로젝트 내 모든 파일)
    - PACKAGE : 패키지
    - CLASS : 클래스
    - GROUP : 논리적 번들 그룹
    - SOURCEFILE : 소스 파일
    - METHOD : 메서드
- `counter` : 코드 커버리지 측정 기준
    - LINE : 빈 줄을 제외한 실제 코드의 라인 수 (전체 소스 코드 중 테스트 코드가 몇 줄의 소스 코드를 커버하고 있는지)
    - BRANCH : 조건문 등의 분기 수
        - Branch 커버리지가 100%인 경우, 모든 조건문들이 실행된 것이고, 이는 모든 코드 라인들을 수행할 수밖에 없으므로, Line 커버리지도 100%가 된다. 그러나 그 역은 성립하지 않는다.
    - CLASS : 클래스 수 (소스 코드 내 작성된 전체 클래스들 중 몇 개의 클래스들이 테스트 코드로 커버되고 있는지)
    - METHOD : 메서드 수
    - INSTRUCTION(Default) : 자바의 바이트코드 명령 수
    - COMPLEXITY : 복잡도. McCabe의 순환 복잡도(Cyclomatic Complexity) 정의를 따른다.
- `value` : 커버리지 지표. 코드 커버리지 결과를 어떻게 표현할 것인지 결정
    - TOTALCOUNT : 전체 개수
    - MISSEDCOUNT : 커버되지 않은 개수
    - COVEREDCOUNT : 커버된 개수
    - MISSEDRATIO : 커버되지 않은 비율. 0% ~ 100% 범위로 표현.
    - COVEREDRATIO(Default) : 커버된 비율. 0% ~ 100% 범위로 표현.

위 내용을 토대로 앞선 예제 2-2의 내용을 해석해보면 다음과 같다. 우선 #1로 표시된 `rule` 블록에서는 `element` 가 별도로 지정되지 않았으므로 `BUNDLE` 로 기본 지정되어 프로젝트 전체를 대상으로 한다. 그리고 그 내부의 `limit` 블록에서는 `counter` , `value` 모두 별도로 지정되지 않았으므로 각각 `INSTRUCTION` 을 기준으로 커버리지 값을 계산하여 `COOVERDRATIO` 형식으로 결과를 낸다. 그 결과가 `minimum` 으로 지정된 30%을 넘어야만 코드 커버리지 기준치를 넘게 된다고 설정한 것이다. 

그 다음 `rule` 에서처럼 여러 rule들을 생성할 수 있다. 예제 2-2에 작성된 두 번째 `rule` 에서는 `CLASS` 를 단위로 해당 룰을 적용하며, 각각의 `limit` 에서는 각각 `LINE` 및 `BRANCH` 기준 `COVEREDRATIO` 의 값이 각각 0.75를 넘겨야 앱 빌드가 되도록 설정된 것이다. 

### 순환 복잡도 (Cyclomatic Complexity)

순환 복잡도(Cyclomatic Complexity)는 메서드, 함수와 같은 코드 블록 단위 내에서 얼마나 많은 서로 독립적인 실행 경로가 있는지를 수치로 나타내는 지표이다. [jacoco 공식 문서](https://www.eclemma.org/jacoco/trunk/doc/counters.html)에 따르면, jacoco에서는 MaCabe의 정의에 따라 순환 복잡도 v(G)를 다음과 같이 정의한다. 

$$
v(G) = E - N + 2
$$

순환 복잡도에서는 메서드, 함수와 같은 하나의 코드 단위에서 더 이상 분할할 수 없는 명령어들의 집합을 그래프의 Node로 상정하고, 어떤 명령어가 실행되어야만 다음 명령어가 실행될 수 있는 관계를 Edge로 선을 이어서 표현한다. 위 수식에서 E는 Edge의 수, N은 Node의 수를 의미한다. 

하지만 이 상태로는 순환 복잡도를 그래프로 그려 파악하기 쉽지 않을 것이다. 따라서, 이와 똑같은 결과를 내지만 다른 관점으로 바라본 다음의 수식도 고려해볼 수 있다. 

$$
v(G) = B - D + 1
$$

여기서 B는 D로 대표되는 결정점(decision points)에 의해 생성되는 모든 가능한 실행 경로의 수를 의미하고, D는 if ~ else, switch 등의 조건문 및 for, while 등의 반복문과 같은 제어문들의 수(decision points)를 의미한다. 이해를 쉽게 하기 위해 다음의 예제를 보자.

```java
String areAllPositive(int a, int b) {
    String message;
    
    if (a > 0) {  // decision point 1
        message = "a는 양수입니다. ";
    } else {
        message = "a는 양수가 아닙니다. ";
    }
    
    if (b > 0) { // decision point 2
        message += "b는 양수입니다. ";
    } else {
        message += "b는 양수가 아닙니다.";
    }
    
    return message;
}
```

예제 3-1. 

위 예제를 보면, 다음에 실행할 코드를 결정하는 조건식이 2개가 존재한다. 이들은 결정 포인트로서, D의 개수에 포함된다. 한 편 이러한 조건문들에 의해 가능한 모든 실행 경로들의 수는 2 * 2 = 4이다. 조건 A는 true이거나 false, 조건 B에 대한 검사 결과도 true이거나 false 이 두 가지의 결과만 내기 때문이다. 따라서 위 메서드의 순환 복잡도를 계산해보면 v = 4 - 2 + 1 = 3이 된다. 

참고로 \\(v(G) = D + 1\\) 로 계산하여도 동일한 결과를 얻는다. 

이러한 순환 복잡도의 수치는 jacoco HTML 결과물에서 `cxty` 컬럼의 형태로 표시된다. jacoco에서는 이 순환 복잡도에 대해서도, 모든 가능한 실행 경로들 중 얼마나 테스트에 의해 실행되었고 그렇지 않은지를 표시해준다. `cxty` 컬럼 바로 왼쪽에 존재하는 `Missed` 컬럼이 순환 복잡도 기준으로 실행되지 않은 경로의 수를 표시해주는 것이다. 

McCabe에 따르면 순환 복잡도의 구간마다 다음의 해석을 제안하였다. 

- 1 ~ 10 : 코드가 간단한 편. 예상치 못한 버그 및 오류 발생 가능성 또는 유지보수적 관점에서 봤을 때 그 위험도가 가장 적은 편.
- 11 ~ 20 : 1 ~ 10보다는 비교적 코드가 복잡한 편. 적정 수준의 위험도.
- 21 ~ 50 : 코드가 복잡함. 높은 수준의 위험도.
- 51 이상 : 테스트조차 불가능한 수준. 매우 높은 수준의 위험도.

어떤 메서드 또는 함수의 순환 복잡도가 N이라면 이 모두를 커버하기 위해 작성해야할 테스트 케이스의 수도 N개에 달한다는 뜻이다. 즉, 순환 복잡도가 높을수록 필요한 테스트 케이스의 수도 그만큼 높아 사실상 테스트로 커버하기 힘든 상황이라는 뜻이다. 이러한 테스트 관점뿐만 아니라, 순환 복잡도가 높다는 것은 곧 하나의 함수가 너무 많은 분기점들을 가지고 있다는 뜻이고, 이는 곧 SRP, 즉 단일 책임 원칙을 위반하고 있을 가능성이 높다는 뜻이다. 따라서 유지보수성과 테스트 용이성을 위해서라도 순환 복잡도가 높은 메서드는 되도록 리팩토링하여 기능을 분리하도록 하는 것이 낫겠다. 

한 편, 이러한 순환 복잡도는 Branch 기준 코드 커버리지와 언뜻 보면 똑같은 개념으로 보인다. Jacoco에서는 더군다나 순환 복잡도 기준으로도 테스트 실행 여부를 보여주기에 더더욱 차이점을 알기 힘들다. 하지만 이 둘은 그 목적 자체에서 엄연한 차이를 보인다. Branch 기준 코드 커버리지는 말 그대로 분기점을 기준으로 각각의 분기들이 테스트 되었는지 여부를 보여주는 용도인 반면, 순환 복잡도는 애초에 함수, 메서드와 같은 코드 단위에서 코드의 복잡도가 어떠한지를 보여주는 지표로, 원래는 코드 커버리지와는 상관없는 개념이다(이걸 Jacoco에서는 순환 복잡도를 기준으로도 코드 커버리지를 결과로 보여주는 것일 뿐이다). 즉, 순환 복잡도를 통해 개발자가 구현한 코드가 유지보수성이 좋고 나쁜지, 테스트하기에 너무 어려운 구조는 아닌지, 리팩토링을 해야할지 여부를 판단할 때 필요한 지표라 보면 되겠다. 

# 라이브러리 대상 Jacoco 적용하기

필자는 이전에 스프링부트 기반의 라이브러리를 제작하였을 때, 라이브러리 자체는 독립적으로 실행 가능한 앱을 제작할 때의 의존성으로서의 역할을 하는 것이지, 라이브러리 자체가 독립적으로 실행가능한 파일로서의 역할을 하진 않았기에, 웹 앱 기반 테스트 코드를 라이브러리 자체 내에서 작성하는 것이 아닌 별도의 프로젝트로 만들어 작성했어야만 했다. 그러다보니 Jacoco를 적용할 때에도 라이브러리 프로젝트 폴더가 아닌, 이 라이브러리를 가져와 테스트하는 별도의 프로젝트 내에서 적용해야만 했다. 그러다보니 생긴 문제점은, Jacoco가 라이브러리를 대상으로 하는 것이 아니라 테스트용 프로젝트 내 `src/main/java` 패키지 내에 작성한 소스 코드를 대상으로 코드 커버리지 결과를 내는 것이었다. 그래서 HTML 결과를 보더라도 라이브러리 내에 작성한 클래스, 메서드명은 전혀 보이지 않았고, 테스트용 프로젝트 내에 작성한 소스 코드에서의 클래스, 메서드 이름만이 보일 뿐이었다. 

프로젝트 폴더의 구조는 다음과 같았다. 

```
/root
  /spoon-suits   ==> 라이브러리
    /src/main/java --> 각종 기능들 정의
  /spoon-suits-local-test  --> 라이브러리를 테스트할 별도의 테스트용 프로젝트 폴더
    /src/main/java  --> 라이브러리를 테스트하기 위해 가짜 웹 앱 코드 작성
      /...
        /controller
        /data
        /service
        /...
    /src/test/java  --> src/main/java 내 소스 코드를 테스트. 사실상 라이브러리 테스트용
```

예제 4-1. 필자가 언급한 라이브러리의 전체 프로젝트 폴더 구조

루트 프로젝트 폴더 안에 크게 2개의 하위 프로젝트 폴더들이 존재하는 구조였다. 하나는 라이브러리이고, 또 하나는 이 라이브러리를 테스트하기 위한 폴더였다. 

아무튼, 라이브러리가 아닌 라이브러리를 테스트하기 위해 작성한 소스 코드만이 코드 커버리지의 대상이 되어 결과물로 나타나는 문제가 있었다. 조사 끝에 이를 해결하는 방법은 생각보다 간단하였음을 발견하였다. 테스트용 프로젝트 폴더에 있는 build.gradle에 정의한 `jacocoTestReport` task 내에 몇 줄 정도의 코드만 추가해도 되었었다. 

```groovy
tasks.named('jacocoTestReport') {
  dependsOn tasks.named('test')

  reports {
    xml.required = true // Codecov 같은 외부 서비스를 위해
    html.required = true  // 시각적으로 편하게 보기 위함.
  }

  def spoonsuitsProject = project(':spoonsuits')  // #1

  // 커버리지 결과에 spoonsuits-local-test/src/main/java가 아닌
  // spoonsuits 라이브러리 내 작성한 코드들이 대상이 되어 결과에 나오도록 설정.
  // 커버리지 측정 대상을 'spoonsuits' 라이브러리로 지정
  sourceDirectories.setFrom(files(spoonsuitsProject.sourceSets.main.allSource.srcDirs))  // #2
  classDirectories.from(spoonsuitsProject.sourceSets.main.output.classesDirs)  // #3

  // 실행 데이터는 현재 프로젝트(spoonsuits-local-test)의 테스트 결과를 사용
  executionData.from = files("${buildDir}/jacoco/test.exec")
}
```

예제 4-2. `spoon-suits-local-test` 프로젝트 폴더 내 build.gradle

먼저 #1에서처럼 코드 커버리지 대상으로 삼을 라이브러리 프로젝트 객체 변수를 정의한다. 그 후 #2에서는 Jacoco로 보고서 생성 시 기준으로 삼을 소스 코드의 위치를 선정한다. `projectName.sourceSets.main.` 으로 지정함으로써 라이브러리 프로젝트인 `spoonsuits` 의 `src/main/java` 내부를 가리키게 된다. 그 후 `classDirectories` 에서도 코드 커버리지 분석 대상이 될 바이트 코드가 담긴 `.class` 파일들의 위치를 라이브러리 프로젝트 쪽으로 지정하였다. 이렇게 지정하면 테스트 폴더에서 테스트 코드를 작성했어도 결과적으로는 라이브러리 자체를 기준으로 Jacoco 보고서가 생성된다. 

# 글을 마치며…

이 글에서는 소스 코드의 품질 향상 및 유지를 위한 방법 중 하나로 코드 커버리지라는 개념에 대해 살펴보았다. 단순히 테스트 코드를 작성하는 것뿐만 아니라, 전체 소스 코드의 얼마만큼을 테스트 코드가 커버하고 있는지 살펴보는 것도 코드 품질에 중요하다는 것을 살펴보았다. 그리고 Java 진영에서 코드 커버리지 자동 계측을 위한 도구 중 하나인 Jacoco에 대해서 개념부터 사용방법까지 살펴보았다. 

앞서 살펴본 Jacoco의 기능 중 하나인 개발자가 직접 코드 커버리지 통과 기준치를 설정하는 기능은 특히 Github Actions와 같은 도구를 이용하여 CI/CD를 구축할 때 활용할 수 있다고 한다. 협업 시 모든 인원들이 새로 작성한 코드를 원격 저장소에 push하여 이를 merge하기 전에 반드시 일정 수준 이상의 코드 커버리지를 충족하고 있는지, 즉 충분한 양의 테스트 코드를 작성했는지를 먼저 검사할 수 있고, 기준치를 넘겼을 때에만 merge할 수 있도록 활용하는 방법이 있다고 한다. 이를 통해 협업 시에도 다른 누군가가 일일히 번거롭게 다른 이가 제출한 코드의 커버리지를 확인할 필요없이 소스 코드의 품질을 유지시킬 수 있는 것이다. 이 모든 과정을 CI/CD를 통해 자동화할 수 있다고 한다. 다음 시간에는 이 Jacoco를 CI/CD와 함께 어떻게 사용할 수 있는지에 대해 살펴보고자 한다. 

---

References

[1] 장정우, “스프링 부트 핵심 가이드 - 스프링 부트를 활용한 애플리케이션 개발 실무”, (위키북스, 2024)

[2] [HMG Developers](https://developers.hyundaimotorgroup.com/blog/146)

[3] [[Java] JaCoCo를 활용하여 code coverage 측정하기](https://chanho0912.tistory.com/99)

[4] [[테스트] JaCoCo란 무엇인가?](https://digitalbourgeois.tistory.com/233)

[5] [[Gradle] fianlizedBy, dependsOn 이용해서 task 자동 실행시키기](https://jinseobbae.github.io/gradle/2022/01/11/gradle-call-task.html)

[6] [Github Action으로 코드 커버리지 뱃지 생성하기](https://shanepark.tistory.com/457)

[7] [Code Coverage](https://techblog.uplus.co.kr/code-coverage-c252e271df60)

[8] [Gradle 프로젝트에 JaCoCo 설정하기 \| 우아한형제들 기술블로그](https://techblog.woowahan.com/2661/)

[9] gradle 공식 홈페이지 - jacoco plugin 사용법

[The JaCoCo Plugin](https://docs.gradle.org/current/userguide/jacoco_plugin.html)

[10] jacoco에서 제외할 파일 및 디렉토리 지정에 쓰이는 glob 문법

[Globs (Glob Patterns) 문법 정리](https://www.daleseo.com/glob-patterns/)

[11] [Code Coverage Best Practices](https://testing.googleblog.com/2020/08/code-coverage-best-practices.html)

[12] cyclomatic complexity, cxty(Complexity by Type)

[Cyclomatic complexity](https://en.wikipedia.org/wiki/Cyclomatic_complexity)

[13] bytecode(바이트코드)

[바이트코드](https://namu.wiki/w/%EB%B0%94%EC%9D%B4%ED%8A%B8%EC%BD%94%EB%93%9C)

[14] 자바 바이트코드

[자바 바이트코드](https://ko.wikipedia.org/wiki/%EC%9E%90%EB%B0%94_%EB%B0%94%EC%9D%B4%ED%8A%B8%EC%BD%94%EB%93%9C)

[15] Jacoco 공식 사이트

[EclEmma - JaCoCo Java Code Coverage Library](https://www.jacoco.org/jacoco/)