---
title: "CI & CD에 테스트 자동화 및 코드 커버리지 추가하기 (with Github Actions, Java, Jacoco)"
category: "CI & CD"
tag: ["CI & CD", "Github Actions", "Codecov", "Code Coverage", "Test", "Java", "Jacoco", "Gradle"]
---

{% raw %}

이 글을 이해하는데에 좋을 이전 글들

- [https://jerocaller.github.io/spring/spring-boot-테스트-코드-작성/](/spring/spring-boot-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%BD%94%EB%93%9C-%EC%9E%91%EC%84%B1/)
- [https://jerocaller.github.io/personal project/spoon-suits-Github-Actions를-이용한-Github-tag-&-release-자동화/](/personal%20project/spoon-suits-Github-Actions%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-Github-tag-&-release-%EC%9E%90%EB%8F%99%ED%99%94/)
- [https://jerocaller.github.io/spring/code-coverage-with-jacoco/](/spring/code-coverage-with-jacoco/)

테스트 코드 작성 후 테스트 통과 여부를 보기 위해서 1차적으로는 자신의 로컬 기기에서 먼저 테스트를 돌려볼 것이다. 만약 모든 테스트 케이스를 통과한다면 이를 원격 저장소에 push할 것이다. 이렇게만 들으면 문제가 없는 것처럼 보인다. 그런데 협업을 한다든가 아니면 개인 프로젝트라도 라이브러리처럼 남들에게 공유하는 성격의 프로젝트라면 문제점이 하나 발생한다. 

내가 내 로컬 기기에서 테스트 코드를 작성하고, 이를 내 로컬 기기에서 돌려보니 테스트가 모두 통과되었다고 해보자. 그리고 이걸 원격 저장소에 업로드하였다. 협업하는 상황이고, 다른 팀원이라면 내가 작성한 프로덕션 코드와 테스트 코드에 문제가 없는지 어떻게 확인해야할까? 아마 팀원은 자신의 로컬 기기로 내가 작성한 코드를 끌어와서 로컬에서 돌려봐야할 것이다. 언뜻보면 문제가 없어보이지만, 팀원, 팀장도 자신이 맡은 작업을 수행하느라 바쁠텐데 다른 사람의 테스트 코드 통과 여부까지 일일히 로컬 기기에서 확인해봐야한다는 건 꽤 번거로운 일이 될 것이다. 다른 사람의 코드를 내 로컬 기기로 끌어와 일일이 실행하지 않고서도 어디선가 그 테스트 코드를 자동으로 실행하고 그 결과를 팀원들 누구나 다 볼 수 있게끔 자동으로 보고하는 기능이 있다면 훨씬 편하지 않을까? 만약 이게 가능하다면 모든 팀원들이 내 코드의 테스트 통과 여부에 대해 바로 확인할 수 있어 시간도 절약되고 다른 팀원들의 상황을 파악하는데에도 도움이 될 수 있을 것이다. 

여기서 더 나아가, 코드 품질을 일정 수준으로 유지시키기 위해 모든 팀원들에게 일정 수준 이상의 코드 커버리지를 지키도록 하고 싶다고 해보자. 그래서 기준치를 넘기지 못하면 중앙 원격 저장소에 애초에 merge하지 못하도록 하고 싶다고 해보자. 단순히 다른 사람의 코드를 가져와 직접 실행해보고 그로부터 나온 코드 커버리지 수치를 보고 판단하는 방식으로는 모든 팀원들에 대해 수행해야해서 번거로울 뿐만 아니라, 누군가 실수로 기준치를 만족시키지 못한 코드를 원격 저장소에 merge해버리는 것을 강제로 막을 방도도 없다는 것이 문제이다. 

협업이 아닌 개인 프로젝트라도 마찬가지일 것이다. 라이브러리처럼 내가 만든 어떤 프로젝트를 누구나 사용할 수 있도록 공개할 때, 사용자의 입장에서는 당신의 코드가 안전하다는 걸 어떻게 확신할 수 있을까? 매번 새 기능을 추가할 때마다 테스트 결과를 어떤 방식으로든 사용자에게 보일 수 있다면 좋지 않을까? 게다가 그 테스트 실행 및 사용자에게 그 결과를 보여주는 방식도 자동화한다면 더더욱 좋을 것이다. 

이 모든 문제점을 해결할 수 있는 것이 바로 CI/CD 과정 중의 하나로 포함되는 테스트 자동화이다. 내가 코드를 원격 저장소에 업로드할 때마다 여태까지 작성된 테스트 코드를 자동으로 실행시켜 테스트 결과 및 코드 커버리지 결과를 보여줄 수 있는 방법이다. 

이 글에서는 Java, Gradle 기반 프로젝트에 대해 Github Actions를 이용하여 테스트 자동화를 구축하는 과정에 대해 살펴보고자 한다. 여기에는 다음의 과정들이 포함될 것이다. 

- 개발자가 작성한 테스트 코드를 Github Actions에서 자동으로 실행하도록 하여 테스트 통과 여부 및 코드 커버리지가 기준치를 넘어가는지 여부를 확인하는 과정.
    - 테스트 코드가 실패했다면 정확히 어떤 테스트 케이스에서 실패했는지 여부도 자동으로 보여주는 기능 구축.
- 자동화된 테스트 실행으로 나온 테스트 결과 및 코드 커버리지 수치를 PR(Pull Request) comment로 확인하는 방법.
- 현재 테스트 결과 및 코드 커버리지 수치를 배지로도 표현하여 다른 사람들도 쉽게 이를 확인할 수 있도록 하는 방법.

이 글에서는 Java, Gradle을 기준으로 설명하지만 다른 언어 및 도구를 사용하더라도 비슷하게 구축 가능할 것이다. 

<p class="notice--info">
💡

이 글에서 보일 코드들의 전체 소스 코드는 <a href="https://github.com/JeroCaller/gradle-ci-test">https://github.com/JeroCaller/gradle-ci-test</a> 여기를 참고. 
</p>


# 사전 지식 - 테스트 결과 및 코드 커버리지 보고서를 로컬에서 보는 방법

Java, Gradle 기준으로 테스트 코드는 JUnit을 사용할 것이다. 그리고 이 글에서는 코드 커버리지 계측 도구를 Jacoco를 기준으로 하여 설명하겠다. Jacoco 사용 시 현재 테스트 코드에 대한 코드 커버리지 결과를 보기 위해 `build.gradle` 에 `jacocoTestReport` task를 작성할 것이고, 만약 코드 커버리지가 특정 기준치를 넘기도록 강제하고자 한다면 여기에 `jacocoTestCoverageVerification` task도 추가하여 사용할 것이다. 이 상태에서 테스트 코드를 실행하고 나면 사실 프로젝트 어딘가에 각각 테스트 결과 및 Jacoco 결과 보고서를 HTML 형태로 볼 수 있다. 물론 Jacoco의 경우 미리 HTML 형태로도 결과 보고서를 볼 수 있도록 `build.gradle` 에 설정을 해줘야 한다. 

테스트 코드를 성공적으로 실행하고 나면 프로젝트 폴더의 `build` 폴더 내에 아마 다음과 같은 하위 폴더 구조가 형성될 것이다. 

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
  <img width="60%" src="/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/1.png" alt="image">
  <p>사진 1-1. <code>build</code> 폴더 내 테스트 및 Jacoco 코드 커버리지 결과 보고서 위치.</p>
</div>

위 사진을 참고해보면 각각 다음의 위치에 테스트 및 코드 커버리지 결과 보고서가 생성된다.

- 테스트 결과 보고서
    - HTML: `build/reports/tests/test/index.html`
    - XML: `build/test-results/test/TEST-{...}.xml`
- Jacoco 코드 커버리지 결과 보고서
    - HTML: `build/reports/jacoco/test/html/index.html`
    - XML: `build/reports/jacoco/test/jacocoTestReport.xml`
        - 둘 다 각각 `build.gradle` 에 설정을 해야 각 포맷 형태로 생성 가능.

![사진 1-2. `build/reports/tests/test/index.html` 화면. 전테 테스트 실행 결과를 확인할 수 있다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/2.png)

사진 1-2. `build/reports/tests/test/index.html` 화면. 전테 테스트 실행 결과를 확인할 수 있다. 

![사진 1-3. `build/reports/jacoco/test/html/index.html` 화면. Jacoco를 이용한 코드 커버리지 결과를 볼 수 있다.  필자가 이전에 작성한 글인 [https://jerocaller.github.io/spring/code-coverage-with-jacoco/](https://jerocaller.github.io/spring/code-coverage-with-jacoco/) 의 “사진 1-1”을 참고함. ](/images/2025-11-22/code-coverage-with-jacoco/1.png)

사진 1-3. `build/reports/jacoco/test/html/index.html` 화면. Jacoco를 이용한 코드 커버리지 결과를 볼 수 있다.  필자가 이전에 작성한 글인 [https://jerocaller.github.io/spring/code-coverage-with-jacoco/](/spring/code-coverage-with-jacoco/) 의 “사진 1-1”을 참고함. 

테스트에 대해 알기 전까지는 기능 구현 및 개발에만 몰두하여 이런 것들도 프로젝트 폴더에서 자동으로 생성된다는 것을 미처 몰랐을 수도 있을 것이다. 테스트 및 코드 커버리지 결과를 로컬에서 보고 싶을 때 참고하면 좋을 것이다. 또한 이 결과 보고서들의 위치는 개발자가 따로 커스텀하지 않는 이상 Github Actions를 이용하여 테스트 자동화를 구축할 때에도 서버 내에서 이 위치로 찾아가 결과 보고서로부터 데이터를 추출할 수 있게끔 하니 알아두면 좋다. 이러한 결과 보고서는 PR 댓글과 같은 형태로 가공하여 다른 사람들도 보기 쉽게 출력할 때 활용된다. 

HTML 파일의 경우 사람이 한 눈에 보기 쉬운 형태로 테스트 및 코드 커버리지 결과를 볼 수 있도록 하는 용도이며, XML 파일의 경우 쉽게 말해 기계가 읽기 쉽게 하기 위한 용도로 활용할 때 사용한다. 후에 기술할 Github Actions를 이용한 테스트 자동화 구축에는 XML 파일을 필요로 한다. 

# Github Actions를 이용한 테스트 자동화 구축

코드 커버리지는 잠시 놔두고, 지금은 테스트 자동화를 먼저 구축해보자. 여기서 구축하고자 하는 테스트 자동화의 구체적인 목적은 다음과 같다. 

- 누군가가 원격 저장소에 `feature/*` 브랜치로 PR 후 `main` 브랜치로 merge를 시도할 때, merge 전에 자동화된 테스트가 먼저 실행된 뒤, 테스트가 모두 통과된 후에만 merge가 가능하도록 하는 기능 구현.
- PR 및 자동화 테스트가 진행된 뒤, 테스트 결과를 PR 화면의 댓글로 자동으로 기록해주는 기능 구현.
- 테스트 실패 시 정확히 어떤 테스트 케이스에서 실패했는지 확인할 수 있도록 하는 기능 구현.

사실 Github Actions에는 이러한 기능을 쉽게 구현해주는 다른 이들이 만든 actions가 이미 있다. 따라서 여기서는 그걸 가져와 이용하는 방향으로 다루고자 한다. 

먼저 여기서는 Git 브랜치 전략을 `main` 과 `feature/*` 단 두 개의 브랜치만 사용하는 Github Flow라고 가정하였다. 그리고 여기서 언급할 작업들은 모두 `feature` 로 시작하는 새 브랜치를 생성하여 거기서 작업하도록 하였다. 

프로젝트 폴더 루트에 `.github/workflows` 폴더가 없다면 생성한 뒤, 그 폴더 안에 Github Actions의 워크플로우를 위한  `.yml` 확장자의 파일을 생성한다. 필자는 `ci.yml` 이라고 지어봤다. 그리고 다음과 같이 작성하였다. 

```yaml
name: CI based on Gradle and Java

on:
  pull_request:
    branches:
      - main

jobs:
  build_and_test:
    runs-on: ubuntu-latest
    permissions:
      checks: write
      pull-requests: write

    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up JDK version
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
          cache: 'gradle'

      - name: Grant execute permission for gradlew
        run: chmod +x gradlew

      # Gradle로 build 시, build.gradle에 설정한 내용에 의해
      # 'test', 'jacocoTestReport', 'jacocoTestCoverageVerification' task들이 순차적으로 자동 실행된다.
      - name: Build with Gradle to get a result of test
        run: ./gradlew build

      # 테스트 결과를 PR comment로 출력
      - name: Publish test results
        uses: EnricoMi/publish-unit-test-result-action@v2
        # 이전 task에서 테스트 실패 시 task도 실패하는데, 이 task의 성공 여부에 상관없이 무조건 이 작업을 실행.
        if: always()
        with:
          files: |
              build/test-results/test/TEST-*.xml
```

예제 1-1. `ci.yml`

위 코드를 하나씩 살펴보겠다. 

```yaml
name: CI based on Gradle and Java
```

예제 1-2. `ci.yml` 의 일부

먼저 위와 같이 현재 워크플로우를 구분할 수 있는 이름을 지었다. 이름은 원하는대로 지어도 된다. 

```yaml
on:
  pull_request:
    branches:
      - main
```

예제 1-3. `ci.yml` 일부

여기서는 Git branch 전략을 main과 feature 단 두 개의 브랜치만 사용하는 Github Flow라 가정하였기에,  `main` 브랜치에 PR이 들어올 때 해당 워크플로우를 실행시키도록 조건을 부여한 것이다. Git Flow 등 다른 브랜치 전략을 가지고 있다면 그에 맞게 `develop` 등 다른 브랜치들도 위 조건에 추가할 수 있다. 

```yaml
jobs:
  build_and_test:
    runs-on: ubuntu-latest
    permissions:
      checks: write
      pull-requests: write
```

예제 1-4. `ci.yml` 일부

위 코드에서는 `build_and_test` 라는 하나의 job을 선언하였다. 이 워크플로우를 실행하게될 서버인 runner에서의 운영 체제를 최신 버전의 ubuntu로 설정하였다. 그리고 이 워크플로우에 의해 자동으로 실행될 테스트의 결과물을 PR 화면에서 댓글로 보이기 위해선 PR에 대한 쓰기 권한이 runner에게 필요하므로 `permissions` 부분에 `write` 권한을 부여한 것이다. 

```yaml
steps:
  - name: Checkout repository
    uses: actions/checkout@v4
```

예제 1-5. `ci.yml` 일부

여기서부터는 앞서 정의한 job 내에 여러 step들을 정할 부분이다. 먼저 첫 번째로 위 코드처럼 `actions/checkout@v4` 라는 이미 다른 사람이 만든 action을 가져와 사용하도록 하고 있다. 이 액션은 깃허브 repo에 있는 소스 코드를 runner라는 서버 컴퓨터로 옮기는 역할을 한다. runner라는 서버 컴퓨터에서 테스트 자동화의 전 과정이 일어나므로 이를 위해 필요한 소스 코드도 서버 컴퓨터로 당연히 옮겨줘야 하는 것이다. 

```yaml
- name: Set up JDK version
  uses: actions/setup-java@v4
  with:
    java-version: '21'
    distribution: 'temurin'
    cache: 'gradle'
```

예제 1-6. `ci.yml` 일부

필자는 앞서 언급했듯, 자바 기반 프로젝트를 다루고 있는데, 이 자바를 runner에서 사용할 수 있도록 먼저 세팅하는 과정을 위 코드에서 실행하고 있다. 

```yaml
- name: Grant execute permission for gradlew
  run: chmod +x gradlew

# Gradle로 build 시, build.gradle에 설정한 내용에 의해
# 'test', 'jacocoTestReport', 'jacocoTestCoverageVerification' task들이 순차적으로 자동 실행된다.
- name: Build with Gradle to get a result of test
  run: ./gradlew build
```

예제 1-7. `ci.yml` 일부

위 코드에서는 runner에게 Gradle의 실행 권한을 부여하고, 그 후 Gradle로 소스 코드를 build하도록 하고 있다. Gradle build시 `build.gradle` 에 설정한 테스트 관련 task들도 작동할 것이다. 그래서 결국 이 단계에서 테스트가 runner 안에서 실행된다고 보면 되겠다. 

```yaml
# 테스트 결과를 PR comment로 출력
- name: Publish test results
  uses: EnricoMi/publish-unit-test-result-action@v2
# 이전 task에서 테스트 실패 시 task도 실패하는데, 이 task의 성공 여부에 상관없이 무조건 이 작업을 실행.
  if: always()
  with:
    files: |
      build/test-results/test/TEST-*.xml
```

예제 1-8. `ci.yml` 일부 

예제 1-7에 의해 runner 내부에서 실행된 테스트 결과를 PR 댓글 등 Github repo 어딘가에 출력할 필요가 있는데, 이를 위 코드에서 수행하도록 하였다. 테스트 결과를 깃허브의 PR 댓글 등으로 출력해주는 action들도 이미 여러 개 있는데, 그 중 필자는 “[EnricoMi/publish-unit-test-result-action](https://github.com/EnricoMi/publish-unit-test-result-action)”이라는 action을 택하였다. 원래 앞선 예제 1-7의 코드 단계에서 만약 테스트가 단 하나라도 실패하면 Gradle build에 실패하고, 따라서 워크플로우의 해당 step도 실패하게 되어 다음 작업을 이어나갈 수 없게 된다. 그러나 여기서는 테스트 실패 결과도 보고 받을 수 있도록 해야하기 때문에 테스트 성공, 실패 여부 상관없이 이를 출력할 수 있도록 `if: always()` 조건문을 추가하였다. 그리고 앞선 예제 1-7에서 Gradle build에 의해 생성된 XML 형태의 테스트 결과 보고서 위치를 `with: files: ...` 구문에서 알려주고 있다. 만약 파일이 여러 개인 경우 여러 줄에 거쳐서 작성해도 된다. 

이제 이를 로컬에 commit하고 Github repo에도 push 및 main으로의 PR을 생성하도록 해보자. main 브랜치로의 PR을 생성하면 다음과 같은 화면이 보일 것이다. 

![사진 2-1. main 브랜치로의 새 PR을 생성한 화면. 위 사진을 보면 `CI based on Gradle and Java ...` 라는 워크플로우가 실행 중인 것을 볼 수 있다. 이는 앞서 설정한 워크플로우 스크립트에 따라 runner에서 테스트가 자동으로 진행되고 있다는 뜻이다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/3.png)

사진 2-1. main 브랜치로의 새 PR을 생성한 화면. 위 사진을 보면 `CI based on Gradle and Java ...` 라는 워크플로우가 실행 중인 것을 볼 수 있다. 이는 앞서 설정한 워크플로우 스크립트에 따라 runner에서 테스트가 자동으로 진행되고 있다는 뜻이다. 

만약 위 상태에서 모든 테스트 케이스가 성공하면 다음과 같은 화면이 뜰 것이다. 

![사진 2-2. 자동화된 테스트 성공 후의 모습. PR 화면에서 위와 같이 `github-actions bot` 에 의해 테스트 결과를 보여주는 댓글이 자동으로 달린 것을 볼 수 있다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/4.png)

사진 2-2. 자동화된 테스트 성공 후의 모습. PR 화면에서 위와 같이 `github-actions bot` 에 의해 테스트 결과를 보여주는 댓글이 자동으로 달린 것을 볼 수 있다. 

해당 Github repo의 “Actions” 탭에 가보면 목록에서 해당 워크플로우 정보를 볼 수 있고, 거기서도 다음과 같이 테스트 결과를 볼 수 있게 된다. 

![사진 2-3. Github repo에서 “Actions” 탭에서 해당 워크플로우 정보에 들어간 모습. 사진 아래에 테스트 결과가 뜬 것을 볼 수 있다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/5.png)

사진 2-3. Github repo에서 “Actions” 탭에서 해당 워크플로우 정보에 들어간 모습. 사진 아래에 테스트 결과가 뜬 것을 볼 수 있다. 

만약 테스트에 실패한다면 PR comment에도 정확히 몇 개의 테스트 케이스가 통과하고 실패하였는지 보고되고, 정확히 어디서 실패했는지도 확인할 수 있다. 

![사진 2-4. 테스트 실패 시의 PR 화면. 사진 아래에는 `github-actions bot` 이 작성한 테스트 결과 보고서가 있는데, 여기서 4개의 테스트 중 3개만 통과했다는 것을 볼 수 있다. 한 편, `Checks` 탭 클릭 시 구체적으로 어떤 테스트가 실패했는지를 볼 수 있다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/6.png)

사진 2-4. 테스트 실패 시의 PR 화면. 사진 아래에는 `github-actions bot` 이 작성한 테스트 결과 보고서가 있는데, 여기서 4개의 테스트 중 3개만 통과했다는 것을 볼 수 있다. 한 편, `Checks` 탭 클릭 시 구체적으로 어떤 테스트가 실패했는지를 볼 수 있다. 

![사진 2-5. 앞선 사진 2-4에서 “Checks” 탭을 클릭하여 이동된 모습. 위 사진에서는 아래에 정확히 어떤 테스트에서 실패했는지를 “annotations”라는 형태로 보여주고 있다. 이를 클릭하면 실패한 테스트의 코드까지 보여준다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/7.png)

사진 2-5. 앞선 사진 2-4에서 “Checks” 탭을 클릭하여 이동된 모습. 위 사진에서는 아래에 정확히 어떤 테스트에서 실패했는지를 “annotations”라는 형태로 보여주고 있다. 이를 클릭하면 실패한 테스트의 코드까지 보여준다. 

## 테스트 결과를 마크다운 배지로도 볼 수 있도록 하기

이렇게 Github Actions를 이용하여 구축한 테스트 자동화 파이프라인에 대해, 최근 테스트가 통과되었는지 유무를 README 파일에도 명시할 수 있으면 더더욱 좋을 것이다. 보통 Github repo에 직접 참여자가 아닌 이상 제 3자는 Github repo에서 README 파일을 가장 먼저 접하기도 해서 여기에 테스트 통과 여부를 첨부한다면 제 3자에게 테스트 결과 현황 확인을 가능하게 하여 궁극적으로 이 프로젝트의 소스 코드에 대한 신뢰도 줄 수 있을 것이다. 

이를 위해 마크다운 파일에 첨부 가능한 뱃지를 가져와 보자. 먼저 앞선 Github repo에서 “Actions”탭에 들어간다. 

![사진 3-1. “Actions” 탭에서 앞서 작성한 워크플로우에 들어가 배지를 획득하려는 모습. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/8.png)

사진 3-1. “Actions” 탭에서 앞서 작성한 워크플로우에 들어가 배지를 획득하려는 모습. 

그러면 위 사진처럼 화면이 뜰 것이다. 왼쪽 사이드바에서 앞서 `ci.yml` 파일을 통해 작성한 `CI based on Gradle and Java` 라는 워크플로우를 선택한다. 그 후 우측 화면의 검색창 오른쪽에 있는 점 3개 버튼을 누르면 `Create status badge` 라는 버튼이 보이는데 이를 클릭한다. 

<div class="single-image">
  <img width="60%" src="/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/9.png" alt="image">
  <p>사진 3-2.  </p>
</div>

그러면 위와 같은 창이 뜰 것이다. 다른 건 그대로 놔두고 맨 아래 `Copy status badge Markdown` 초록색 버튼을 누르면 해당 배지의 마크다운 내용이 복사된다. 이를 README 마크다운 파일에 붙여넣으면 된다. 

![사진 3-3. 앞서 생성한 배지를 REAEME 마크다운 파일에 추가한 모습. 여기서는 테스트가 통과된 상태라 `CI based on Gradle and Java` 배지 옆에 초록색 바탕으로 `passing` 이라 떠 있다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/10.png)

사진 3-3. 앞서 생성한 배지를 REAEME 마크다운 파일에 추가한 모습. 여기서는 테스트가 통과된 상태라 `CI based on Gradle and Java` 배지 옆에 초록색 바탕으로 `passing` 이라 떠 있다. 

![사진 3-4. 테스트 실패 시의 배지 모습. 앞선 사진 3-3과 달리 이번에는 빨간색 배경의 `failing` 이라 뜨게 된다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/11.png)

사진 3-4. 테스트 실패 시의 배지 모습. 앞선 사진 3-3과 달리 이번에는 빨간색 배경의 `failing` 이라 뜨게 된다. 

이제 최신의 PR 테스트가 실행될 때마다 위 사진들처럼 README.md 파일에 테스트 통과 여부가 배지 형태로 보이게 될 것이다. 

## PR 단계에서 테스트 도중 또는 실패 시 merge 강제로 막기

![사진 4-1. PR 단계에서 아직 테스트가 실행 중임에도 “Merge pull request” 버튼이 활성화된 모습. 아직 테스트가 끝나지 않은 상태임에도 merge를 진행할 수 있는 셈이다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/12.png)

사진 4-1. PR 단계에서 아직 테스트가 실행 중임에도 “Merge pull request” 버튼이 활성화된 모습. 아직 테스트가 끝나지 않은 상태임에도 merge를 진행할 수 있는 셈이다. 

![사진 4-2. PR 단계에서 테스트에 실패했음에도 “Merge pull request” 버튼은 여전히 활성화되어 있어 merge가 가능한 상황.](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/13.png)

사진 4-2. PR 단계에서 테스트에 실패했음에도 “Merge pull request” 버튼은 여전히 활성화되어 있어 merge가 가능한 상황.

위 두 사진에서 볼 수 있듯, 지금까지는 테스트 진행 도중이나 실패했음에도 여전히 merge가 가능한 상황이다. 이를 막고자 한다면 다음의 과정을 거쳐야 한다. 

1. 먼저 현재 repo의 “Settings’ 탭에 들어간 후, 왼쪽 사이드바에 있는 “Branches” 버튼을 클릭한다. 그 후 오른쪽에 보이는 화면에서 “Add branch ruleset” 버튼을 클릭한다. 
    
    ![사진 4-3. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/14.png)
    
    사진 4-3. 
    
2. 그러면 Ruleset을 설정할 수 있는 화면으로 넘어가게 되는데, 먼저 생성하고자 하는 Ruleset 이름 설정 뒤, 그 아래의 드롭박스에서 “Active”를 선택한다.  

    <div class="single-image">
      <img width="60%" src="/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/15.png" alt="image">
      <p>사진 4-4. </p>
    </div>
    
3. 아래로 내리다보면 다음 사진처럼 “Target branches” 부분이 나타난다. 박스의 오른쪽에 있는 “Add target” 버튼 클릭 후 나오는 여러 메뉴들 중 “Include by pattern”을 클릭한다. 그러면 다음 사진처럼 창이 하나 뜰 것이다. 여기서는 `main` 브랜치로의 merge에 관한 룰을 세팅하고 있는 것이기에 `main` 이라 적는다. 
    
    ![사진 4-5. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/16.png)
    
    사진 4-5. 
    
4. 아래로 내리다보면 여러 체크박스들 중 “Require status checks to pass” 체크박스가 보일 것이다. 해당 체크박스에 체크를 한다. 그러면 그 아래에 세부 설정 화면이 뜰 것이다. 거기서 “Require branches to be up to date before merging”에 체크한다. 그 후 그 아래에 보이는 박스 우측의 “Add checks” 버튼 클릭 시 텍스트 입력창이 뜬다. 앞서 Github repo의 “Actions” 탭에서 “CI based on Gradle and Java”란 이름으로 설정한 워크플로우에 들어가보면 “Jobs” 항목에 필자의 경우 “build_and_test”와 “Test Results”가 뜨는데 이 job들을 입력하여 등록하면 된다(사실 “build_and_test” job이 실질적으로 테스트를 실행하고 통과 여부를 결정짓는 것이기에 해당 job만 추가해도 충분할 것이다). 그러면 해당 Job들이 모두 통과되어야만 merge할 수 있게 되는 것이다. 
    
    ![사진 4-6. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/17.png)
    
    사진 4-6. 
    
5. 맨 아래로 내려 “Create”라는 초록색 버튼을 클릭하면 앞서 설정한 Ruleset이 새로 생성된다. 생성된 ruleset 목록을 확인하고자 한다면 “Settings” → “Rules” → “Rulesets”에 가보면 확인할 수 있다. 
    
    ![사진 4-7.](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/18.png)
    
    사진 4-7.
    

이로써 모든 설정이 완료되었다. 이제 새 PR을 생성해보면 CI에 의해 자동으로 수행되는 테스트가 실행되는 도중 또는 실패 이후에도 “Merge pull request” 버튼이 비활성화되어 merge를 할 수 없게 된다. 

![사진 4-8. PR 화면에서 테스트가 진행되고 있는 모습. “Merge pull request” 버튼이 비활성화되어 있는 것을 볼 수 있다. 따라서 테스트 도중에 실수로 merge하는 것을 강제로 방지할 수 있게 되었다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/19.png)

사진 4-8. PR 화면에서 테스트가 진행되고 있는 모습. “Merge pull request” 버튼이 비활성화되어 있는 것을 볼 수 있다. 따라서 테스트 도중에 실수로 merge하는 것을 강제로 방지할 수 있게 되었다. 

![사진 4-9. PR 화면에서 테스트 실패 시의 모습. 역시 “Merge pull request” 버튼이 비활성화되어 있어 merge를 할 수 없게 되었다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/20.png)

사진 4-9. PR 화면에서 테스트 실패 시의 모습. 역시 “Merge pull request” 버튼이 비활성화되어 있어 merge를 할 수 없게 되었다. 

# Code Coverage 계측 자동화 구축

이제 CI 자동화 테스트에 코드 커버리지 부분도 추가해보도록 하겠다. 앞선 자동화 테스트와 마찬가지로 코드 커버리지에서도 PR 단계에서 자동 실행되어 그 결과를 PR 댓글로 볼 수 있는 방식이다. Jacoco 기준, PR 댓글로 볼 수 있도록 하는 action도 여러 개 있는데, 여기서는 2개를 소개해보고자 한다. 

- “[madrapps/jacoco-report](https://github.com/marketplace/actions/jacoco-report)”
    - 코드 커버리지 결과를 PR 댓글 형태로 볼 수 있도록 해주는 action
- “[codecov/codecov-action](https://github.com/codecov/codecov-action)”
    - 코드 커버리지 결과를 Codecov라는 별도의 사이트에서 볼 수 있도록 해주는 action
    - 앞선 action과의 차이점은, [codecov](https://about.codecov.io/) 라는 별도의 공식 홈페이지가 있어, 그곳에서 코드 커버리지 수치를 표현한 배지를 획득할 수 있어 마크다운 파일에 달 수 있으며, 모든 Github repo를 모아 하나의 창에서 각 repo들에 대한 코드 커버리지 관리를 할 수 있다는 특징이 있다.
    - 물론 Codecov를 이용한 방법에서도 PR 댓글 형태로 코드 커버리지 결과를 자동으로 달 수 있도록 하는 기능도 사용할 수 있다.

따라서 만약 코드 커버리지 결과를 단순히 PR 댓글로 보는 기능만을 원하거나 별도의 사이트를 가입, 이용하기에 부담스럽다면 `madrapps/jacoco-report` 액션을, 반면 앞선 자동화 테스트 결과를 마크다운 파일에서 배지로 볼 수 있었듯이, 코드 커버리지 수치를 똑같이 배지로 보고 싶다든가, 하나의 사이트에서 내 Github repo들의 코드 커버리지를 조금 더 체계적으로 관리하고자 한다면 `codecov/codecov-action` 액션을 사용해보는 것도 좋을 것이다. 

이 두 액션은 똑같이 PR 댓글 형태로 코드 커버리지 결과를 알려주기에 두 액션을 동시에 쓸 필요는 아마 없을 것으로 보인다. 다만 이 글에서는 이 각각의 액션 사용법을 살펴보기 위해 일부러 두 액션을 모두 사용해보도록 하겠다. 

참고로 이 두 액션은 앞서 작성한 `ci.yml` 파일에 이어서 작성하도록 하겠다. 코드 커버리지 결과도 결국 Github Actions의 runner로 내 repo 소스 코드를 checkout 해야하고, Gradle로 앱 빌드 과정을 거쳐야하기에 이 과정을 이미 수행하도록 한 `ci.yml` 을 재활용하기 위함이다. 

## `madrapps/jacoco-report` 액션 이용해보기

앞서 작성한 `ci.yml` 파일 내 맨 아래에 다음의 스크립트를 추가한다. 

```yaml
 # Jacoco report PR comment에 추가하는 기능
 - name: Add coverage to PR comment
   id: jacoco
   uses: madrapps/jacoco-report@v1.7.2
   with:
     paths: |
         ${{ github.workspace }}/**/build/reports/jacoco/test/jacocoTestReport.xml
     token: ${{ secrets.GITHUB_TOKEN }}
     min-coverage-overall: 30
     min-coverage-changed-files: 30
```

예제 2-1. `ci.yml` 에 새로 추가한 부분

`paths` 값에는 XML 형태의 Jacoco 보고서가 생성되는 위치를 기입하였다. `${{ github.workspace }}` 및 `${{ secrets.GITHUB_TOKEN }}` 에 대해선 별도로 지정할 것이 없으니 넘어가도 좋다. 

`min-coverage-overall` 속성값을 30으로 잡아 프로젝트 전체 수준에서 요구되는 최소한의 코드 커버리지 수치를 30%으로 잡았다. 기본값은 80이다. 또한, `min-coverage-changed-files` 속성값도 30으로 잡아 변경된 코드들에 대한 최소 코드 커버리지 요구 수치도 30%으로 잡았다. 이 속성의 기본값도 역시 80이다. 

이 외에도 해당 액션에 대한 여러 방식으로 커스터마이징이 가능하니 해당 액션의 공식 Github 문서를 참고 바란다. 

아까와 동일하게 `feature` 브랜치에서 내 Github repo로 push 후 PR을 생성하면 잠시 뒤 테스트 실행 후 다음과 같은 코드 커버리지 결과가 PR 댓글 형태로 추가된다. 

![사진 5-1. 해당 코드 커버리지 액션을 최초로 추가한 뒤의 PR 모습. 위에 `github-actions bot` 댓글을 통해 코드 커버리지 결과가 추가된 것을 볼 수 있다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/21.png)

사진 5-1. 해당 코드 커버리지 액션을 최초로 추가한 뒤의 PR 모습. 위에 `github-actions bot` 댓글을 통해 코드 커버리지 결과가 추가된 것을 볼 수 있다.  

<div class="single-image">
  <img width="60%" src="/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/22.png" alt="image">
  <p>사진 5-2. 새 기능 추가 후 PR 시의 코드 커버리지 결과. 바로 이전에 merge된 소스 코드와 비교했을 때 현재 PR로 제출된 소스 코드의 코드 커버리지를 비교하는 모습. <code>-17.32%</code> 부분은 이전 PR에 비해 해당 수치만큼이나 코드 커버리지가 떨어졌다는 의미이다. </p>
</div>

## `codecov/codecov-action` 액션 이용해보기

앞선 액션보다는 현재 다룰 액션을 사용하는 것이 더 복잡할 것이다. 이 액션은 앞서 언급했듯 Codecov라는 별도의 사이트가 있으며, 특정 repo의 코드 커버리지 badge 획득을 위해서라도 해당 홈페이지에 로그인할 필요가 있기 떄문이다. 그럼에도 코드 커버리지 수치를 badge로 표현할 수 있다는 점과, 해당 사이트에서 내 Github repo들을 한 화면에 모아 각 repo들의 코드 커버리지를 모니터링 및 관리할 수 있다는 것도 큰 장점이 될 수 있으니 적어도 시도는 해볼만한 액션이라 생각된다. 

먼저, 검색창에 “[codecov](https://about.codecov.io/)”라 치면 해당 사이트를 볼 수 있다. 

![사진 6-1. codecov 사이트 첫 화면.](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/23.png)

사진 6-1. codecov 사이트 첫 화면.

현재 필자가 알아본 바로는 무료 플랜에서는 1명의 사용자(즉, 나 자신)만 사용 가능하며, public repo든 private repo든 제한없이 사용할 수 있다고 한다. 참고로 말하자면, 사실 공식 홈페이지에서는 public repo에서만 적용 가능한 대신 팀원 제한없이 사용할 수 있는 무료 플랜도 있다고 나오지만, 필자가 현재 로그인 헀을 때에는 전자의 플랜이 적용되어 있고, 이를 바꿀 수 있는 방법을 찾지 못한 상황이다. 

어찌되었건, 사용을 위해 위 사진에서 보이는 화면 우측 상단의 “Login”을 클릭하고, “Github” 계정으로 로그인한다. Codecov에 처음으로 가입하는 것이라면 다음의 창이 뜰 것이다. 

<div class="single-image">
  <img width="60%" src="/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/24.png" alt="image">
  <p>사진 6-2. </p>
</div>

아래의 “Authorize Codecov”를 클릭하여 다음으로 넘어간다. 다음 창에서는 Codecov 사이트에서 사용할 이름과 이메일을 입력하면 된다. 이렇게 로그인에 성공하면 다음과 같이 나의 Github repo들을 목록 형태로 볼 수 있게 된다. 

![사진 6-3. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/25.png)

사진 6-3. 

이 목록에서 Codecov를 적용하고자 하는 repo를 목록에서 찾은 뒤, 오른쪽의 “Configure” 버튼을 클릭한다. 그러면 다음과 같은 창으로 넘어갈 것이다. 해당 화면에서는 Codecov를 Github Actions CI에 적용하는 방법을 차례대로 설명해주고 있다. 

![사진 6-4. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/26.png)

사진 6-4. 

 

![사진 6-5. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/27.png)

사진 6-5. 

위 사진들을 보면 알 수 있듯, 필자는 Java, Gradle을 기반으로 하고 있는 반면, 설명에서는 npm을 기준으로 하고 있어 이를 무시하였다. 어차피 앞서 소개한 codecov 액션 Github 문서에서 CI 설정 방법이 나오니 이를 참고해도 된다. 

다만 위 사진에서 나온 설명들 중 반드시 해야하는 한가지가 있는데, 그것은 바로 `CODECOV_TOKEN` 을 내 해당 Github repo에 추가해야한다는 점이다. 이를 위해 Github에서 해당 repo의 “Settings” 탭에 들어가 “Secrets and variables” 탭의 “Actions” 탭을 클릭한다. 

![사진 6-6. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/28.png)

사진 6-6. 

위 사진에서 우측의 “New repository secret” 초록색 버튼을 클릭하면 secret의 이름(Name)과 그 값을 넣을 수 있는 칸(Secrets)이 각각 나온다. Name에는 앞서 Codecov 설명에 나온대로 `CODECOV_TOKEN` 을 입력하고, Secrets 칸에는 Codecov 화면에 나온 토큰값을 입력하면 된다. 해당 토큰값은 Codecov 인증을 위한 값이니 절대 외부에 노출해선 안된다. 

![사진 6-7.](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/29.png)

사진 6-7.

아래의 “Add secret” 버튼을 클릭하면 다음과 같이 “Repository secrets”에 새 secret이 하나 추가된 것을 볼 수 있다. 

![사진 6-8. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/30.png)

사진 6-8. 

이렇게 Codecov에서 제공하는 시크릿 키를 Github repo에 등록하는 과정을 거치지 않으면 Codecov를 이용한 CI에 실패할 수 있으니 반드시 해주도록 하자. 

그 후, `ci.yml` 에 다음과 같이 스크립트를 추가하였다. 

```yaml
 # codecov를 이용한 jacoco report
 - name: Upload coverage reports to Codecov
   uses: codecov/codecov-action@v5
   with:
     token: ${{ secrets.CODECOV_TOKEN }}
```

예제 3-1. `ci.yml` 파일 맨 아래에 추가함.

역시 `codecov/codecov-action` 공식 Github 문서를 통해 여러 방식으로 커스텀할 수 있지만 여기서는 아주 간단하게 작성해보았다. `with: token:` 부분에는 앞서 설정한 토큰값이 입력되도록 `${{ secrets.CODECOV_TOKEN }}` 이라고 설정하였다. 

사실 위 예제 3-1에서 사용된 Codecov action은 runner에서 계측된 코드 커버리지 결과 데이터를 Codecov 사이트에 전송하여 해당 사이트에서 코드 커버리지 수치를 보거나 분석하는 용도로 사용되는 것이다. PR 댓글에 자동으로 코드 커버리지 결과도 올리도록 하려면 Codecov의 Github App도 설치해야한다. 다음 사진과 같이 Codecov 사이트 자체에 해당 앱을 설치할 수 있는 안내문이 뜨는 경우가 있어 그 경로를 통해 설치해도 되고, 아니면 “[https://github.com/apps/codecov](https://github.com/apps/codecov)” 사이트에 직접 접속하여 설치해도 된다.

![사진 6-9. Codecov Github App 설치 안내문](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/31.png)

사진 6-9. Codecov Github App 설치 안내문

해당 페이지에 접속하면 다음과 같은 페이지가 보일 것이다. 

![사진 6-10. Codecov Github App 설치 화면](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/32.png)

사진 6-10. Codecov Github App 설치 화면

사실 필자는 이미 해당 앱을 설치했기에 우측 상단에 “Configure”라는 버튼이 뜨는데, 설치하지 않은 상태라면 초록색의 “Install” 버튼이 대신 있을 것이다. 해당 버튼 클릭 시 다른 화면이 뜨며, 해당 앱을 모든 내 Github repo에 적용할 것인지, 아니면 특정 repo들에만 적용할 것인지 선택하는 부분이 있는데, 이 부분은 원하는대로 하면 되겠다. 필자의 경우, 나중에 다른 repo에서도 사용할 가능성이 있을 것 같아 모든 repo에 적용하도록 설정하였다. 설정을 마쳤으면 아래의 “Install” 버튼을 클릭한다. 그러면 설치가 완료된다. 

현재 repo에 Codecov Github app이 적용되었는지 확인하기 위해 해당 repo의 “Settings” ⇒ “Github Apps”을 클릭해보면 된다. 목록에 “Codecov”가 존재한다면 해당 repo에 잘 적용된 것이다. 

![사진 6-11. Codecov Gitub App이 repo에 적용된 모습.](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/33.png)

사진 6-11. Codecov Gitub App이 repo에 적용된 모습.

이제 앞서 작성한 `ci.yml` 을 Github repo에 push, PR을 해보자. 그러면 PR에서 CI 테스트가 실행된 뒤 잠시 기다리면 다음과 같이 Codecov에 의한 PR 댓글이 자동으로 달리게 된다. 

![사진 6-12. Codecov를 처음 적용할 떄의 PR 댓글. 다음 PR 때 Codecov의 코드 커버리지 결과가 보인다고 한다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/34.png)

사진 6-12. Codecov를 처음 적용할 떄의 PR 댓글. 다음 PR 때 Codecov의 코드 커버리지 결과가 보인다고 한다. 

![사진 6-13. 소스 코드 변경사항에 대한 코드 커버리지가 모두 되었을 때의 Codecov PR 댓글 모습.](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/35.png)

사진 6-13. 소스 코드 변경사항에 대한 코드 커버리지가 모두 되었을 때의 Codecov PR 댓글 모습.

![사진 6-14. 새로 작성한 코드에 대한 테스트 코드를 전혀 작성하지 않았을 때의 Codecov PR 댓글 모습](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/36.png)

사진 6-14. 새로 작성한 코드에 대한 테스트 코드를 전혀 작성하지 않았을 때의 Codecov PR 댓글 모습

또한 이러한 코드 커버리지 결과는 Codecov 사이트에서 좀 더 세부적으로 확인할 수 있다. 

![사진 6-15. Codecov 사이트에서 특정 Github repo에서의 코드 커버리지 계측 결과 및 이력을 확인할 수 있다. 자세히 보면 알겠지만 commit별, PR별로도 코드 커버리지 결과를 볼 수 있다. ](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/37.png)

사진 6-15. Codecov 사이트에서 특정 Github repo에서의 코드 커버리지 계측 결과 및 이력을 확인할 수 있다. 자세히 보면 알겠지만 commit별, PR별로도 코드 커버리지 결과를 볼 수 있다. 

한 편, 현재 repo의 코드 커버리지 지수를 배지로 보고자 한다면 Codecov 사이트에서 해당 repo의 “Configuration” 탭 클릭 후 왼쪽 사이드바의 “Badges & Graphs”를 클릭한다. 그러면 아래 사진처럼 마크다운에서 사용 가능한 배지 링크가 뜬다. 이를 복사하여 README.md에 붙여넣으면 되겠다. 

![사진 6-16. Codecov에서의 특정 repo에 대한 배지 링크.](/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/38.png)

사진 6-16. Codecov에서의 특정 repo에 대한 배지 링크.

<div class="single-image">
  <img width="60%" src="/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/39.png" alt="image">
  <p>사진 6-17. Codecov 제공 코드 커버리지 배지를 README.md 파일에 추가한 모습</p>
</div>

{% endraw %}

---

References

[1] [카카오웹툰은 GitHub Actions를 어떻게 사용하고 있을까? \| 카카오엔터테인먼트 테크블로그](https://tech.kakaoent.com/front-end/2022/220106-github-actions/)

[2] [[CI/CD] GitHub Actions를 이용한 자동 테스트](https://velog.io/@milkskfk5677/CICD-GitHub-Actions%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EC%9E%90%EB%8F%99-%ED%85%8C%EC%8A%A4%ED%8A%B8)

[3] [Github Actions 를 이용한 CI 테스트 자동화](https://creampuffy.tistory.com/190)

[4] [[GitHub Actions] JUnit 테스트 결과 확인](https://jtechtalk.tistory.com/42)

[5] junit 테스트 자동화 action

[JUnit Report Action - GitHub Marketplace](https://github.com/marketplace/actions/junit-report-action)

[6] 테스트 결과를 PR comment에 추가하는 action

[https://github.com/EnricoMi/publish-unit-test-result-action](https://github.com/EnricoMi/publish-unit-test-result-action)

[7] jacoco report action

[JaCoCo Report - GitHub Marketplace](https://github.com/marketplace/actions/jacoco-report)

[8] [Github Actions로 PR 시 테스트를 돌려보자](https://devs0n.tistory.com/25)

[9] 코드 커버리지 자동화

[Github Action으로 코드 커버리지 뱃지 생성하기](https://shanepark.tistory.com/457)

[10] 코드 커버리지 자동화

[Codecov로 code coverage 확인해보기](https://jane514.tistory.com/12)

[11] codecov/codecov-action

[https://github.com/codecov/codecov-action](https://github.com/codecov/codecov-action)

[12] Codecov 공식 홈페이지

[Codecov: Code Coverage Testing & Insights Solution](https://about.codecov.io/)