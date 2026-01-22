---
title: "[CI DB 구축 - 2] Github Actions 워크플로우 스크립트에 yml 설정 파일 주입하는 법"
category: "CI & CD"
tag: ["Database", "DB", "CI & CD", "CI", "Github", "Secrets", "환경설정", "Workflow"]
---

{% raw %}

지난 시간에 작성한 “[[Spring Boot][CI DB 구축 - 1] SQL 스크립트를 이용한 데이터베이스 초기화](/spring/Spring-Boot-CI-DB-%EA%B5%AC%EC%B6%95-1-SQL-%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%B4%88%EA%B8%B0%ED%99%94/)” 글에 이어 이번 시간에는 CI을 구축하기 위해 워크플로우 스크립트에 필요한 값들을 주입하는 방법에 대해 다뤄보고자 한다. 

필자는 CI 테스트 자동화를 구축할 때 테스트용 DB도 같이 사용해야 했기에 스프링부트 쪽에서의 `application.yml` 파일 내부의 DB 관련 설정이 필요했다. 해당 파일 뿐만 아니라, 필자는 각 상황별 환경 변수를 다르게 해서 각각 `application-local.yml` , `application-secret.yml` 등으로 세분화한 추가 설정 파일들이 존재하는 상태였다. 이 파일들에는 비록 테스트이긴 하지만 그래도 외부에 노출되기 꺼려지는 민감 정보들이 들어 있어 Github repo에서는 이들을 `.gitignore` 에 추가하였기에 업로드되지 않은 상태였다. 

이러한 이유로, `.gitignore` 로 인해 업로드 되지 않은 `application-secret.yml` 파일 내 환경설정 값들을 워크플로우에 어떻게 삽입해야할지 몰랐었다. 민감 정보들이 외부에 노출되지 않으면서도 워크플로우에 잘 삽입되어 문제없이 CI 테스트가 동작하길 원했다. 이에 대해 조사하면서 알게 된 방법을 이 글에서 작성해보고자 한다. 

# Github Secrets을 이용하여 `application.yml` 환경 설정 파일을 통째로 주입하기

Github의 repo에 들어가보면 “Settings” 탭이 있다. 이를 클릭 후, 왼쪽 사이드바에서 “Security” ⇒ “Secrets and variables” 드롭박스 클릭 후 “Actions”를 클릭하면 다음의 화면을 볼 수 있다. 

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
  <img width="70%" src="https://jerocaller.github.io/images/2025-12-04/CI-CD%EC%97%90%20%ED%85%8C%EC%8A%A4%ED%8A%B8%20%EC%9E%90%EB%8F%99%ED%99%94%20%EB%B0%8F%20%EC%BD%94%EB%93%9C%20%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80%20%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0%20(with%20Github%20Actions%2C%20Java%2C%20Jacoco)/28.png" alt="image">
  <p>사진 1-1. 필자가 이전에 작성한 글인 <a href="https://jerocaller.github.io/ci%20&%20cd/CI-CD%EC%97%90-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94-%EB%B0%8F-%EC%BD%94%EB%93%9C-%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0-(with-Github-Actions,-Java,-Jacoco)/">“[CI & CD에 테스트 자동화 및 코드 커버리지 추가하기 (with Github Actions, Java, Jacoco)]"</a>의 “사진 6-6”을 참고함.</p>
</div>

오른쪽에 보이는 “New repository secret” 버튼을 클릭하여 repository 수준의 secret을 만든다. 그러면 secret의 이름과 그 값을 입력할 수 있는 화면이 뜬다. 필자는 각각 `application.yml` 파일과 `application-secret.yml` 파일의 내용이 필요한데, 각각 `APPLICATION` , `APPLICATION_SECRET` 이란 secret을 만들고 각 파일 내용을 그대로 옮겨 넣었다.

<div class="single-image">
  <img width="60%" src="/images/2026-01-23/%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%202%5D%20Github%20Actions%20%EC%9B%8C%ED%81%AC%ED%94%8C%EB%A1%9C%EC%9A%B0%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%20yml%20%EC%84%A4%EC%A0%95%ED%8C%8C%EC%9D%BC%20%EC%A3%BC%EC%9E%85%ED%95%98%EB%8A%94%20%EB%B2%95/1.png" alt="image">
  <p>사진 1-2. <code>application.yml</code> 내용 일부를 수정한 후 그 내용을 그대로 action secret 값으로 넣은 모습.</p>
</div>

그 후 “Add secret” 버튼을 클릭하면 해당 secret가 생성되고, 목록에도 보인다. 위 사진의 경우, `application-secret.yml` 의 내용도 필요하므로, 따로 secret을 또 만들어 똑같은 과정을 거쳤다. 

![사진 1-3. `application.yml` 에 해당하는 내용을 넣은 secret 하나를 생성한 후의 목록 모습.](/images/2026-01-23/%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%202%5D%20Github%20Actions%20%EC%9B%8C%ED%81%AC%ED%94%8C%EB%A1%9C%EC%9A%B0%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%20yml%20%EC%84%A4%EC%A0%95%ED%8C%8C%EC%9D%BC%20%EC%A3%BC%EC%9E%85%ED%95%98%EB%8A%94%20%EB%B2%95/2.png)

사진 1-3. `application.yml` 에 해당하는 내용을 넣은 secret 하나를 생성한 후의 목록 모습.

![사진 1-4. 필요한 설정 내용들을 모두 넣은 후의 모습. 이와 같이 필요한 만큼 secret으로 생성하여 관리할 수 있다. ](/images/2026-01-23/%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%202%5D%20Github%20Actions%20%EC%9B%8C%ED%81%AC%ED%94%8C%EB%A1%9C%EC%9A%B0%20%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90%20yml%20%EC%84%A4%EC%A0%95%ED%8C%8C%EC%9D%BC%20%EC%A3%BC%EC%9E%85%ED%95%98%EB%8A%94%20%EB%B2%95/3.png)

사진 1-4. 필요한 설정 내용들을 모두 넣은 후의 모습. 이와 같이 필요한 만큼 secret으로 생성하여 관리할 수 있다. 

그 후, 워크플로우 스크립트에서는 repository를 runner 내부로 checkout하고 JDK를 설정하는 스텝과, Gradle을 빌드하는 스텝 사이에 다음과 같은 스텝을 추가하였다. 

```yaml
# 생략
jobs:
  build_and_test:
    runs-on: ubuntu-latest
   
    # 생략...
    steps:
      - name: Checkout repository
        uses: actions/checkout@v4

      - name: Set up JDK version
        uses: actions/setup-java@v4
        with:
          java-version: '21'
          distribution: 'temurin'
          cache: 'gradle'

      - name: create application.yml
        # touch : 파일이 없을 경우 새 파일을 생성하는 리눅스 명령어
        # ehco ... > ... : 값을 오른쪽에 명시된 파일 내부로 복사
        run: |
          cd ./spoonsuits-local-test/src/main/resources
          touch ./application.yml
          touch ./application-secret.yml
          echo "${{ secrets.APPLICATION }}" > ./application.yml
          echo "${{ secrets.APPLICATION_SECRET }}" > ./application-secret.yml

      - name: Grant execute permission for gradlew
        run: chmod +x ./spoonsuits-local-test/gradlew
        
      # 생략...
```

코드 1-1. github 워크플로우 스크립트

위 코드의 `name: create application.yml` step에서 필요한 위치에 각각 `application.yml` , `application-secret.yml` 파일을 생성한 후, 그 파일에 앞서 설정한 Github secret 값을 그대로 주입하는 작업을 하고 있다. `touch` 명령어를 통해 파일을 새로 생성한 후,  `echo A > B` 명령어는 `A` 에 있는 값을 `B`에 복사하는 명령어인데 이를 이용하여 생성된 파일에 Github secrets 값을 주입시킨 것이다. 이렇게 하면 로컬에서처럼 내용까지 갖춰진 `application.yml` 파일을 runner 내에서도 구축할 수 있게 된다. 

이 방법의 장점은 환경설정 파일에 있던 내용을 그대로 Github secret으로 붙여넣기만 해도 되어서 설정이 편리하다는 점이다. 또한 Github secret에 설정된 값은 나중에 워크플로우가 실행되어 로그가 출력될 때 `*` 문자로 처리되기에 보안도 챙길 수 있다는 점이다. 

다만 Github secret은 한 번 설정한 값은 나중에 다시 살펴볼 수 없다. 따라서 값의 일부만 수정해야할 때에도 아예 새로운 secret 값으로 업데이트해줘야하는 것이 단점이라고 볼 수 있겠다. 

# 참고) `step.env` 변수를 이용하는 방법

이 방법은 아직 필자가 실제로 해보진 않았지만 이러한 방법도 있다는 것을 기록하기 위해 소개하고자 한다. 

Github Actions의 워크플로우 문법 중에는 각 스텝에 `env` 변수를 사용하여 필요한 환경변수들을 여러 개 작성할 수 있는 문법이 존재한다. Gradle build를 통해 테스트를 진행할 때 필요한 환경변수들을 `env` 변수를 통해 삽입하는 방식으로 활용할 수 있다. 예를 들면 다음과 같이 활용할 수 있다. 

```yaml
 - name: Run Gradle Tests
     # 이 단계에서 필요한 Spring Boot 설정들을 환경 변수로 주입!
   run: ./gradlew test
   env:
     # Spring Boot Datasource 설정 (환경 변수가 yml 파일을 덮어씀!)
     SPRING_DATASOURCE_URL: jdbc:mariadb://127.0.0.1:3306/your_test_db 
     SPRING_DATASOURCE_USERNAME: ${{ secrets.MARIADB_USER_CI }}
     SPRING_DATASOURCE_PASSWORD: ${{ secrets.MARIADB_PASSWORD_CI }}
     SPRING_JPA_HIBERNATE_DDL_AUTO: create-drop
```

코드 2-1. 

민감한 환경변수값은 여전히 Github secrets을 이용하여 관리해야한다. 다만 앞서 소개한 방식과는 다르게 하나의 secret에는 단 하나의 값만 들어가게 된다. 

이 때 스프링부트의 `application.yml` 과 같은 환경 설정 파일에서 사용되는 환경변수명은 리눅스 등 다른 운영체제에서 사용하는 변수 명명 규칙과 호환하기 위해 다른 방식으로 환경변수 이름이 바뀐다. 이러한 규칙을 relaxed binding이라고 하는데, 스프링부트의 환경변수명을 다른 운영체제에서도 사용할 수 있도록 하려면 다음의 규칙에 따라 환경변수명을 변환해야한다. 

- 점 (`.`)은 언더스코어(`_`)로 변환시킨다.
- 대시(`-`)가 있는 경우 삭제한다.
    - 예) `spring.jpa.defer-datasource-initialization` ⇒ `SPRING_JPA_DEFERDATASOURCEINITIALIZATION`
- 영소문자는 모두 대문자로 변환시킨다.

이로 인해 앞선 코드 2-1의 `env` 내 환경변수명들이 `application.yml` 파일에서 보던 것과 다른 이유이다. 

위 방식의 장점은 다음과 같다.

- 어떤 환경변수들이 사용되었는지를 파악할 수 있다. 앞선 `application.yml` 파일 직접 생성하는 방법에서는 Github secret의 특성상 한 번 그 값을 설정하면 다시는 그 내용을 볼 수도 없기에 어떤 환경 변수들이 설정되어 있는지 확인할 길이 없다. 하지만 이 방식에서는 그 값은 확인할 수 없더라도 어떤 환경변수가 사용되었는지는 확인할 수 있다.
- Github secret과 함께 사용할 수 있어 여전히 보안성을 지킬 수 있다.

다만 다음과 같은 단점들도 있겠다.

- 환경설정 파일의 내용 자체가 길고 그 구조가 복잡한 경우 위와 같이 `env` 를 이용하는 방식으로는 환경설정을 일일이 하기가 번거롭다.
- 나중에 새로운 환경변수를 추가하고자 할 때 아예 워크플로우 스크립트를 건드려야 한다는 것이다. 로컬에서 수정한 워크플로우 스크립트를 커밋, Github에 push까지 해야한다. 앞선 파일 방식에서는 중간에 새로운 환경변수가 추가되거나 심지어는 그 구조를 새롭게 바꿔야할 때에도 Github secret의 값만 바꾸면 되기에 워크플로우 스크립트는 전혀 건드리지 않아도 된다. 이 방식과 비교할 때 불필요한 커밋을 남기게 되는 것과 마찬가지이다.

따라서 필요한 환경변수가 적을 때, 또는 Github secret에 환경설정 파일 내용 전부를 넣는 방식으로는 나중에 환경변수 구조를 파악할 수 없어 어려움을 겪을 때 등에 사용할 때 좋을 것 같다. 

# 글을 마치며

이번 시간에는 Github Actions를 이용한 워크플로우 스크립트 작성 시 repo에는 보안상의 이유로 인해 git ignore되어 있어 업로드 되지 못한 환경 설정 값들을 주입하는 방법에 대해 살펴보았다. 필자의 경우 CI 테스트 자동화 구축 과정에서 테스트용 DB도 같이 구축해야 헀기에 runner 내부에서 테스트용 앱과 DB가 연동이 되었어야 했고, 이로 인해 `application.yml` 에 작성하는 DB 연동 정보를 외부에서 주입해야할 때 이 글에서 소개한 방법을 통해 원활하게 처리할 수 있었다. 비록 테스트용이라 사실 엄밀히 말하자면 민감한 정보라 하기에도 애매하지만, 혹시 모를 일을 위해 민감 정보로 취급하고 repo에도 업로드하지 않았었다. 이로 인해 CI 테스트 자동화 구축을 위해선 이 글에서 소개한 방법이 더더욱 유용할 수밖에 없었다. 

이전 글에서도 언급한 부분이지만, 이번 글은 Github Actions를 이용한 CI 테스트 자동화 구축 과정에서 필요한 DB 구축하기에 관한 내용 2편이다. 사실 이번 글은 어떤 의미에서는 “CI DB 구축”이라는 것과는 다소 거리가 멀어보인다. 하지만 그 과정에서는 꼭 필요한 내용이기도 했다. 그렇다고 다른 내용과 합쳐서 설명하기에는 글이 다소 장황해지고 나중에 별개의 개념만 찾아보기도 힘들것 같아 이렇게 일부러 별개의 내용으로 분리하여 다뤄보았다. 

다음 시간에는 본격적으로 runner라는 가상 머신 내에서 테스트용 DB를 미리 구축하여 테스트 자동화를 문제없이 구축하는 방법에 대해 설명하고자 한다. 

{% endraw %}
---

References

[1] [[AWS] 배포 시 Github Secret으로 민감 정보 yml 파일 관리하기](https://m42-orion.tistory.com/142)

[2] [[CICD] github actions 변수 사용하기 (env 파일, secrets and variables)](https://aandi.tistory.com/79)

[3] 환경설정 값 주입을 위한 `steps.env` 문법 참고

[Workflow syntax for GitHub Actions - GitHub Docs](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax#jobsjob_idstepsenv)

[4] Linux - touch 명령어

[Linux 명령어 - touch 명령어 사용법 알아보기(파일 생성 및 시간 정보 변경)](https://server-talk.tistory.com/392)

[5] spring 공식 - Binding from environment variables

[Externalized Configuration :: Spring Boot](https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.typesafe-configuration-properties.relaxed-binding.environment-variables)