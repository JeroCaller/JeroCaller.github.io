---
title: "[Spoon Suits] Github Actions를 이용한 Github tag & release 자동화"
category: "Personal Project"
tag: ["Personal Project", "Library", "Spring Boot", "Spoon Suits", "Jitpack", "Github", "Tag", "Release", "Github Actions", "CI/CD"]
---

{% raw %}

저번 글 “[[Spoon Suits] Jitpack을 이용한 라이브러리 온라인 배포](/personal%20project/spoon-suits-jitpack%EC%9D%84-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%9D%BC%EC%9D%B4%EB%B8%8C%EB%9F%AC%EB%A6%AC-%EC%98%A8%EB%9D%BC%EC%9D%B8-%EB%B0%B0%ED%8F%AC/)”에서는 필자가 만든 스프링부트 기반 라이브러리를 온라인에 배포하기 위해 Jitpack을 사용한 과정에 대해 설명하였다. Jitpack을 이용하여 배포할 시에는 Github의 release를 이용하기에 배포 이후 버그 수정 및 새 기능 추가 등 유지보수 관점에서도 Github release는 장기간 사용할 수밖에 없는 기능이다. 그런데 매번 새 버전을 개발자가 일일히 만들어서 릴리즈하고 거기에 기존 버전과의 차이점 또는 새 버전에서 어떤 점이 바뀌었는지를 설명하는 change log도 일일히 작성해야 하는 점은 사실 번거로운 일일 것이다. 그래서 이 글에서는 Github Actions를 이용하여 이러한 과정도 자동화한 것에 대해 설명하고자 한다. 또한 이 기능에는 CI, CD와 같은 개념과 더불어 Github Actions에서 사용되는 기본적인 개념들도 관련되어 있기에 이에 대해서도 정리하겠다. 

# CI / CD

애플리케이션을 개발하는 것에 그치지 않고 이를 사용자가 실제로 사용할 수 있도록 배포까지 해야 함을 생각해보면, 개발만 한다고 다가 아니란 것을 알 수 있을 것이다. 이 과정을 전부 거치기까지 다음의 단계들을 순차적으로 거친다.

1. 애플리케이션 빌드(빌드란 소스 코드를 실행가능한 파일로 변환하는 과정을 의미한다)
2. 빌드된 애플리케이션에 대한 테스트
3. 테스트 통과 시 이를 공유 리포지토리에 통합(merge)하고 release한다.  
4. 성공적으로 통합된 이후 배포 준비가 된 공유 리포지토리의 전체 소스 코드를 production 환경(AWS와 같은 클라우드 등이 그 예가 되겠다)에 배포하여 사용자가 실제로 이 애플리케이션을 사용할 수 있게 한다. 

그런데 이러한 과정들을 모두 개발자가 일일히 수동으로 거쳐야한다면 굉장히 번거로운 일이 될 것이다. 예를 들어, Git과 같은 형상관리 툴을 이용하는 경우 애플리케이션을 이루는 수많은 기능들을 하나의 기능 단위로 쪼개 각각의 브랜치에서 작업을 한 후, 이를 공용 리포지토리에 push하여 병합하는 과정을 거칠 것이다. 이 과정에서 병합하기 전에 먼저 자신이 개발한 기능이 공유 리포지토리의 코드들과 병합될 때 문제가 없는지 미리 확인하기 위해 빌드 및 테스트를 거쳐야할 것이다. 보통 프로젝트 규모가 커질수록 애플리케이션을 구성하는 기능들도 굉장히 많을 것인데, 이렇게 많은 기능들을 매번 일일히 테스트하는 것은 쉽지 않을 것이다. 이는 배포도 마찬가지인데, 배포 후 버그 및 에러를 발견하였거나 빠른 주기로 새 기능을 추가해야하는 경우 이럴 때마다 일일히 수동으로 배포하는 것도 만만치 않은 작업일 것이다. 따라서 이러한 일련의 과정들은 자동화되는 것이 중요할 것이다. CI, CD는 이러한 끊임없는 빌드, 테스트, 통합, 배포의 과정을 자동화하는 것을 목표로 한다. 

CI는 Continuous Integration의 약자로, 지속적 통합을 의미한다. 이 지속적 통합은 빌드 및 테스트, 그리고 공유 리포지토리의 통합까지 자동화하는 것을 의미한다. 이러한 CI를 구축하면 개발자의 입장에서 빌드, 테스트, 통합의 일련의 과정들을 일일히 수동으로 하는 번거로움을 없앨 뿐만 아니라, 코드에 오류가 있으면 테스트의 자동화로 인해 빠르게 파악할 수 있고, 이로 인해 병합 시의 충돌을 사전에 방지할 수 있다. 이 과정의 자동화로 인해 결과적으로 배포하기까지의 시간을 단축할 수 있다는 장점도 있다고 할 수 있겠다. 

이러한 CI 작업이 완료되면 그 다음으로 CD 작업이 진행된다. CD에는 두 가지 뜻을 내포하고 있다. 

- Continuous Delivery, 지속적 제공
    - 빌드, 테스트, 통합 등 CI 과정을 거치고 난 후의 공유 리포지토리의 소스 코드가 배포 준비가 된 상태라 판단될 경우 여기에 태그를 달아 release 상태로 만든다. 이는 이전 글에서 Github repo에서 tag를 생성하고 release하는 과정에서 다뤘다. 이렇게 현재 소스 코드가 배포 준비되어 release하는 과정을 자동화하는 것을 지속적 제공이라 한다. 이 단계에서는 release라는 배포 준비 단계까지만 자동화하며, 실제 배포는 개발자가 수동으로 한다는 특징이 있어 이 특징이 지속적 배포와의 차이를 만든다.
- Continuous Deployment, 지속적 배포
    - 지속적 배포는 지속적 제공에서 더 나아가, AWS와 같은 실제 프로덕션 환경으로 소스 코드를 전송하여 그 곳에서 애플리케이션을 빌드하고 이를 배포하는 과정까지 자동화하는 것을 의미한다.

즉, 지속적 제공은 repository로의 자동 release로 “배포 준비 단계”까지의 자동화를, 지속적 배포는 실제 프로덕션으로의 배포까지 자동화하는 것을 의미한다. 

참고로 이러한 일련의 자동화 과정을 구축하는 것을 파이프라인(pipeline)이라고도 부른다. 

이러한 CI, CD 툴에는 여러 가지가 있다. Jenkins, Travis CI 등이 있는데, 이 글에서 소개할 Github Actions도 이에 포함된다. 

# Github Actions

Github 공식 문서에 따르면 Github Actions는 앞서 말한 CI/CD를 가능하게 해주는 플랫폼 중 하나이다. 사실 Github Actions를 이용하면 CI/CD만 가능한 게 아니라, Github Issues에 올라온 issue에 자동으로 어울리는 label을 달아준다든가, 하루에 특정 시각이 되면 자동으로 다른 사이트의 특정 내용을 크롤링해와 기록하고 보여주는 기능 등 여러 가지의 기능들을 자동화할 수 있다고 한다. 그래서 어찌보면 CI/CD보다 조금 더 범용적인 자동화를 제공해준다고도 볼 수 있겠다. 

이러한 Github Actions에는 몇 가지 개념들이 있어 이를 정리하는 것이 좋겠다. 

## 핵심 개념

![참고 사진 1-1. Github Actions의 핵심 개념 및 작동 과정. [https://docs.github.com/en/actions/about-github-actions/understanding-github-actions#the-components-of-github-actions](https://docs.github.com/en/actions/about-github-actions/understanding-github-actions#the-components-of-github-actions) 에서 발췌함.](https://docs.github.com/assets/cb-25535/mw-1440/images/help/actions/overview-actions-simple.webp)

참고 사진 1-1. Github Actions의 핵심 개념 및 작동 과정. [https://docs.github.com/en/actions/about-github-actions/understanding-github-actions#the-components-of-github-actions](https://docs.github.com/en/actions/about-github-actions/understanding-github-actions#the-components-of-github-actions) 에서 발췌함.

- workflow : 1개 이상의 job를 실행하는 자동화된 프로세스. 가장 최상위 개념이다. “작업 흐름”이라는 그 뜻대로, 자동화할 파이프라인 전체를 의미한다고 볼 수 있다. 예를 들어 Github Actions를 이용하여 빌드-테스트-통합-배포 준비-배포의 모든 과정을 한꺼번에 하나의 과정으로서 자동화한다면 이 일련의 과정들이 하나로 묶여 하나의 workflow라 할 수 있다.
    - Github Actions에서는 하나의 workflow를 `.yml` 또는 `.yaml` 확장자 파일 하나로 정의할 수 있으며, repository의 루트에 `.github/workflows` 폴더를 생성한 후 이 안에 해당 파일을 저장하면 Github에서 이를 자동으로 인식하고 yaml 파일을 자동 실행하여 workflow가 동작하는 원리이다.
    - workflow는 repository에 종속된 개념이다. Actions 탭 자체가 각각의 repo마다 존재한다.
- event : workflow를 트리거(trigger)할 수 있는 리포지토리 내 특정 활동을 의미한다. 여기에는 리포지토리로의 PR, issue 오픈, 커밋 push 등이 있다. 사실 이뿐만 아니라 특정 시간마다 workflow를 트리거할 수 있는 schedule 기능도 있고, 개발자가 손수 workflow를 직접 트리거할 수도 있다.
- runner : workflow를 실행할 수 있는 서버로, Github에서 제공하는 가상 머신이라 보면 된다. Github에서는 Ubuntu Linux, Windows, macOS 운영체제를 지원하며, 어느 운영체제에서 workflow를 돌릴지 yaml 파일에 지정할 수도 있다. 또한 개발자가 직접 runner를 호스팅하여 사용할 수도 있다고 한다.
- job : job은 여러 개의 step으로 구성되어 있으며, 하나의 job은 똑같은 runner에서 실행된다. 만약 여러 개의 job들을 정의한 경우 각각의 job들은 각각의 runner에서 실행된다. job들이 여러 개일 경우 기본적으로는 병렬 실행, 즉 동시에 실행되며, 설정에 따라 하나의 job이 끝나야 다음 job이 순차적으로 실행되도록 설정할 수도 있다.
- step :  가장 작은 단위의 작업(task). 하나의 job에는 1개 이상의 step들로 구성될 수 있는데, 순차적으로 실행되며, 이 step들은 모두 하나의 공통된 job에 포함되어 있고, 이 하나의 job은 하나의 runner에서 실행되기에 여러 step들은 데이터를 서로 공유할 수 있다. 한 편 step에는 bash와 같은 shell script 실행이 가능하고, 또는 action을 실행시키도록 할 수도 있다.
- action : 복잡하고 자주 반복되는 작업을 실행할 수 있도록 하는 커스텀된 애플리케이션(사실상 미리 정의된 명령어들의 집합이라 보면 되겠다)이며, Github Actions 플랫폼에 특화되어 있는 개념이다. 개발자가 직접 action을 작성할 수 있고, 아니면 [Github Marketplace](https://github.com/marketplace)라는 곳에서 다른 유저들이 만들어놓은 action을 가져와 사용할 수도 있다.

## 핵심 문법

Github Actions에서 사용할 모든 워크플로우들은 모두 yaml 파일로 작성하며, 지정된 문법에 따라 작성한다. 다음에 소개할 문법들의 예시 코드들은 모두 [Github Actions docs](https://docs.github.com/en/actions) 에서 참고하였다. 

워크플로우가 트리거될 이벤트를 정의하고자 한다면 `on` 키워드를 이용한다.

```yaml
on: push
```

코드 1-1.

위 코드는 repo에 branch에 상관없이 push가 들어온 경우 해당 워크플로우를 실행하겠다는 이벤트 조건이다. 다음과 같이 여러 이벤트들을 조건으로 달 수 있다. 

```yaml
on: [push, fork]
```

코드 1-2. 

위 코드는 push 또는 다른 사람이 나의 특정 repo를 fork해갈 경우 해당 워크플로우를 실행하겠다는 뜻이다. 즉, 둘 중 하나의 이벤트만 발생해도 해당 워크플로우는 실행된다. 만약 push와 fork가 동시에 총 두 번 일어나면 그만큼의 워크플로우가 트리거되어 실행된다. 

```yaml
on:
  label:
    types:
      - created
  push:
    branches:
      - main
  page_build:
      # 빈 값 가능
```

코드 1-3. 

트리거 조건을 더 세부적으로 작성하기 위해 `created` , `main` 과 같은 activity type 또는 filter값을 사용하는 경우에는 위와 같이 on 키워드 다음에 각각 독립적으로 그 조건을 작성한다. 위 예시는 issue에 부여되는 label이 생성되거나, repo의 main 브랜치로 push가 들어오거나, Github Pages가 실행가능한 브랜치로의 push가 들어오거나 셋 중 하나의 조건을 만족할 때 워크플로우가 동작한다. activity type, filter 값들은 github docs에서 다 나와있어 이를 참고하는 것이 좋겠다. 

한 편, `jobs` 키워드를 통해 1개 이상의 job들을 정의할 수 있다. 

```yaml
jobs:
  my_first_job:
    name: My first job
  my_second_job:
    name: My second job
```

코드 1-4. 

위 코드에서는 “my_first_job”과 “my_second_job”이라는 두 가지 job이 정의된 것이다. job의 이름은 그 작업이 무엇을 하는지 설명할 수 있는 이름이라면 어떤 것이든 상관없이 지어도 된다. 

한 편 위 코드 1-4는 두 job이 병렬 실행되는데, 순차적으로 실행시키고자 하는 경우 `needs` 키워드를 사용한다.

```yaml
jobs:
  job1:
  job2:
    needs: job1
  job3:
    if: ${{ always() }}
    needs: [job1, job2]
```

코드 1-5.

위 코드는 `job2` 는 `job1` 의 작업이 끝난 후에 실행되고, `job3` 는 `job1`, `job2` 모두 작업이 끝난 후에야 실행되는 구조를 띈다. 참고로 `if` 문을 사용하여 특정 job이 특정 조건에서만 실행되도록 할 수 있는데, 위 코드의 경우 `job1`, `job2` 의 작업이 실패하더라도 무조건 실행하도록 구성되어 있다. 이러한 조건문이 없는 상태에서 이전 작업들 중 하나라도 실패하면 이 작업들을 필요로하는 작업도 스킵된다고 한다. 

job 내 step은 다음과 같은 형태로 작성할 수 있다.

```yaml
jobs:
  my_first_job:
    steps:
      - name: My first step
        # Uses the default branch of a public repository
        uses: actions/heroku@main
      - name: My second step
        # Uses a specific version tag of a public repository
        uses: actions/aws@v2.0.1
```

코드 1-6.

위 코드에서는 “my_first_job”이라는 하나의 job 내부에 총 두 개의 step이 `steps` 프로퍼티에 정의되어 있다. 각각의 스텝은 `-` 을 통해 구분할 수 있다. 위 코드에서는 각각의 step에 `name` 을 통해 이름을 부여하고, 어떤 action을 사용할지 `uses` 프로퍼티를 통해 표시한 것을 볼 수 있다. 

한 편, 워크플로우 트리거 이벤트 중 하나로 schedule을 사용할 수 있는데, 이는 특정 시각 또는 일, 월, 요일마다 특정 작업을 반복할 수 있도록 해준다. 

```yaml
on:
  schedule:
    # * is a special character in YAML so you have to quote this string
    - cron:  '30 5,17 * * *'
```

코드 1-7.

위 코드를 해석하면, 매일 오전 5:30, 오후 5:30마다 워크플로우를 실행하겠다는 의미이다. Github docs에 따르면 cron 문법은 다음과 같다.

```yaml
┌───────────── minute (0 - 59)
│ ┌───────────── hour (0 - 23)
│ │ ┌───────────── day of the month (1 - 31)
│ │ │ ┌───────────── month (1 - 12 or JAN-DEC)
│ │ │ │ ┌───────────── day of the week (0 - 6 or SUN-SAT)
│ │ │ │ │
│ │ │ │ │
│ │ │ │ │
* * * * *
```

코드 1-8.

| 연산자 | 뜻 | 예시 |
| --- | --- | --- |
| `*` | 아무 값 | `30 * * * *` 는 매일 매시각의 30분일 때마다 실행. 1시 30분, 2시 30분, 3시 30분, … 등에 워크플로우를 실행 |
| `,` | 값 열거를 위한 구분자 | `30 5,17 * * *` 은 매일 오전 5시 30분 및 오후 5시 30분에 워크플로우를 실행 |
| `-` | 값 범위 지정 | `30 5-17 * * *` 은 매일 오전 5시 30분부터 1시간 간격으로 오후 5시 30분까지 워크플로우를 실행. 오전 5시 30분, 6시 30분, 7시 30분, … , 오후 4시 30분, 오후 5시 30분에 각각 실행됨. |
| `/` | step 값 | `20/15 * * * *` 은 20분부터 59분까지 15분 간격으로 워크플로우를 실행. 매 시간의 20분, 35분, 50분에 실행. |

표 1-1. cron 표현식에 사용되는 연산자들

실제로 위 schedule을 이용하여 repo에 먼저 특정 사이트의 특정 페이지 내용을 크롤링해오는 코드를 작성, 저장한 후, 매일 특정한 매 시각마다 이 크롤링 코드를 워크플로우에서 실행시켜 오늘의 신간 도서 목록을 가져와 Github Issue에 기록하는 사례도 있었다. 이러한 기능을 개발자가 만든 웹 애플리케이션과 결합하면 자신의 웹 사이트에 오늘 하루 날씨를 갱신하여 표시하는 기능과 같은 여러 가지 일들에도 응용할 수 있을 것이다. 

위에서 소개한 문법들은 핵심만 간략하게 설명한 것이다. 전체 문법은 다음의 사이트를 참고하면 된다.

[Workflow syntax for GitHub Actions - GitHub Docs](https://docs.github.com/en/actions/writing-workflows/workflow-syntax-for-github-actions)

# Github Actions를 이용하여 Github Tag & Release 자동화하기

이제 필자가 새 버전을 출시할 때마다 Github Actions를 이용하여 태그 및 릴리즈를 자동화하도록 구성한 과정에 대해 설명해보고자 한다. 

필자는 몇몇 글들을 통해 맨 처음 Github Actions 사용을 위한 워크플로우 작성법을 알게 되었다. 그에 따라 필자는 프로젝트 폴더 최상단에 `.github/workflows` 폴더를 생성한 뒤, 거기에 `.yml` 파일을 생성하였다. 이런 폴더 경로를 구성해야 깃허브에서 이를 인식하여 그 안에 있는 yaml 파일을 실행시켜 CI/CD를 비롯한 자동화 과정이 실행된다. 

그리고 필자는 최종적으로 다음과 같이 yaml 파일 내 코드를 작성하였다. 

```yaml
name: Release versioning  # workflow 이름

# 이 workflow가 트리거될 조건 명시.
# 여기서는 main 브랜치에 push가 들어오는 경우를 조건으로 삼았다. 
on:
  push:
    branches:
      - main

jobs:
  # "build"라는 이름의 job 정의
  build:
    name: Automation of Github release
    runs-on: ubuntu-latest  # 최신 ubuntu OS 환경에서 실행
    
    # 쓰기/읽기 등의 권한 허락 여부 설정
    # 결과적으로 Github repo에 release하려면 이 워크플로우에 접근 권한을 부여해야함.
    permissions: 
      contents: write
      id-token: write
    
    # 작업 정의
    steps:
      # repository 내 코드를 가상 머신으로 체크아웃해주는 액션 사용
      - uses: actions/checkout@v4
      
      # mathieudutour/github-tag-action이라는 액션을 이용하여 
      # 자동으로 다음 릴리즈를 위한 태그 버전 계산하여 부여.
      - name: Bump version and push tag
        id: tag_version
        uses: mathieudutour/github-tag-action@v6.2
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          default_bump: false
          custom_release_rules: 'BREAKING CHANGE:major,feat:minor,fix:patch,docs:patch,refactor:patch,test:patch,style:patch'
          
      # 앞의 tag_version step을 통해 계산된 새로운 태그 버전 값을 
      # 토대로 실제 repo에 release하는 step
      - name: Create a GitHub release
        uses: ncipollo/release-action@v1
        with:
          tag: ${{ steps.tag_version.outputs.new_tag }}
          name: Release ${{ steps.tag_version.outputs.new_tag }}
          body: ${{ steps.tag_version.outputs.changelog }}
      
      # 앞선 태그 자동화 액션을 통해 얻은 계산값들 로그하는 용도
      - name: Log created release info
        run: |
          echo "Release Type : ${{ steps.tag_version.outputs.release_type \}\}"
          echo "Previous Tag : ${{ steps.tag_version.outputs.previous_tag }}"
          echo "New Tag: ${{ steps.tag_version.outputs.new_tag }}"
```


코드 2-1. tag-and-release-versioning.yml

위 코드에서는 다음의 외부 액션들을 사용하였다.

- [actions/checkout](https://github.com/marketplace/actions/checkout)
- [mathieudutour/github-tag-action](https://github.com/mathieudutour/github-tag-action)
    - [ncipollo/release-action](https://github.com/marketplace/actions/create-release)

위 코드에서는 하나의 job 내부에 여러 step들에 거친 여러 작업들을 정의하였다. `steps` 프로퍼티 내부에 정의된 step들은 위에서부터 순차적으로 실행된다. `actions/checkout` 액션을 이용하여 먼저 repo에 있는 코드들을 가상 머신으로 복사해와 워크플로우 작업 준비를 한다. 그 후, `mathieudutour/github-tag-action` 액션을 이용하여 기존의 Github repo에 있는 tag, release 정보를 토대로 다음 release 버전 번호를 자동으로 계산한다. 여기서 버전 계산 방식은 해당 액션에서 내부적으로 [Semantic Versioning](https://semver.org/) 을 따른다고 한다. 줄여서 “SemVer”라고도 하는 이 방식은 간단하게 말하면, `major.minor.patch` 이렇게 세 부분의 버전 번호로 나눠 다음의 규칙들을 적용하는 방식이다. 

- 세 부분 모두 0과 양의 정수만을 범위로 가진다.
- 코드에 변화가 발생한 경우 반드시 다음 새로운 버전으로 릴리즈해야 한다. 기존 버전의 코드를 수정하고 버전 번호까지 유지하면 안된다.
- major : 이전 버전과 호환되지 않는 API의 변화를 만든 경우. 쉽게 말하면 사용자가 느끼기에 큰 변화가 일어날 경우 이 버전 번호를 증가시킨다.
    - 이 버전 번호가 0일 경우, 이는 초기 개발용이라고 한다. 쉽게 말하면 공식 릴리즈는 아니고 아직 개발 및 테스트 단계일 경우 사용한다. 그래서 major 번호가 0일 경우 이 버전에 해당하는 API는 public API는 아니다. 사용자 입장에서 이 버전은 안정적인 버전은 아니라고 인식한다.
    - 버전 1부터는 public API로 본다. 즉, 공식 릴리즈라 보면 되겠다.
- minor : 이전 버전과 호환 가능한 새 기능 추가 시, 또는 기존에 있던 특정 기능을 deprecated하겠다고 표시하는 경우 이 버전 번호를 1씩 증가시킨다. private code 내 부수적인 새 기능 또는 개선 사항이 도입된 경우 또는 patch 레벨이 변화할 경우에도 이 버전 번호를 올리는 것을 고려할 수 있다.
    - 만약 이 버전 번호를 올리면 patch 버전은 0으로 리셋되어야 한다.
    - major가 사용자가 느끼기에 큰 변화라 한다면, minor는 기존 체계에서 특정 기능들이 새로 들어오거나 수정되는 것이기에 사용자 입장에선 작은 변화로 느낄 수 있다.
- patch : 기존 버전과 호환 가능한 상태에서 기존 버전의 버그를 고친 경우 1씩 올린다.

이 외에도 여러 세부 규정들이 있으니 해당 공식 문서를 참조.

이러한 각각의 특성들을 보면 patch 버전이 가장 빈번하게 증가할 것이고, 그 다음 minor, 그 다음으로 major가 상대적으로 가장 드물게 변화할 것이다. 

다시 코드 2-1의 두 번째 step으로 돌아와서, `custom_release_rules` 에는 어떤 commit type에 어떤 release type을 부여할 것인지 설정하였다. 여기서 필자는 공식 문서 및 그 외 소스로부터 정보를 얻어 commit type이 `chore` 를 제외한 나머지 commit type에 대해서는 무조건 major, minor, patch 중 하나의 버전 번호가 증가하도록 구성하였다. 예를 들어 commit type이 “feat”인 경우 minor 번호가 증가하는 방식이다. 

그런데 이 설정만 하면 commit type이 chore인 경우 해당 step에선 이 타입을 버전 자동화 대상으로 인식되지는 않지만, 그럼에도 patch 번호가 증가한다. 이 액션은 인식되지 않은 commit type에 대해서는 기본적으로 patch 번호가 증가하도록 되어 있다. 그래서 `default_bump` 를 false로 두어서 `custom_release_rules` 에 지정되지 않은 commit type에 대해서는 버전 번호를 증가시키지 않도록 하였다. 참고로 원래 `default_bump` 값은 `patch`로 기본 설정되어 있다고 한다. 

한 편, `custom_release_rules` 필드에는 공식 문서([mathieudutour/github-tag-action](https://github.com/mathieudutour/github-tag-action) 의 README)에 따르면 엄연히 commit type이라고 적혀있지 않고 `keyword` 라 적혀 있었다. 그래서 커밋 로그 내에 지정된 키워드만 있으면 이를 인식하는 것인지, 아니면 commit type으로만 인식하는 것인지 이해하지 못했다. 그래서 커밋 메시지에 “BREAK CHANGE”라는 키워드가 있다면 major 번호가 상승하도록 하였는데, 아직 실제로 major 번호를 올릴 정도로 라이브러리 내 코드가 크게 바뀐 게 아니라서 이에 대해선 테스트하지 못한 상태이다. 

갑자기 “BREAK CHANGE”란 말이 왜 나왔냐하면, 해당 액션은 기본적으로 git commit 로그를 기반으로 다음 릴리즈의 버전 번호를 결정하기 때문이다. 공식 문서에 따르면 다음의 규칙을 따른다고 한다.

| commit message | Release type |
| --- | --- |
| fix(pencil): stop graphite breaking when too much pressure applied | Patch(Fix) Release |
| feat(pencil): add 'graphiteWidth' option | Minor(Feature) Release |
| `perf(pencil): remove graphiteWidth option`<br/><br/>`BREAKING CHANGE: The graphiteWidth option has been removed`<br/>`The default graphite width of 10mm is always used for performance reasons.` | ~~Major~~ Breaking Release <br/> (Note that the `BREAKING CHANGE:`  token must be in the footer of the commit) |

표 2-1. [mathieudutour/github-tag-action](https://github.com/mathieudutour/github-tag-action) 및 [semantic-release](https://github.com/semantic-release/semantic-release) 문서에서 발췌

위 표만 보면 commit type이 fix인 경우 patch 번호가, feat인 경우 minor 번호가 증가한다고 한다. 그런데 major의 경우, commit footer에 `BREAKING CHANGE:`  토큰이 있어야만 이를 인식한다고 적혀있다. 이것 때문에 해당 문구를 언급한 것이다. 그런데 위 설명과는 다르게, chore 등의 다른 commit type을 사용해도 patch 번호가 증가하였는데, 이는 위 표에 아무것도 해당되지 않는 commit type이라 기본 설정인 patch 번호 자동 증가가 적용된 결과로 보인다. 

그러나 필자의 경우, `custom_release_rules` 를 통해 각 커밋 내 키워드별로 릴리즈 타입을 부여했기에 위 표 2-1대로 적용된다는 보장이 없다. 그래서 혹시 몰라 `BREAKING CHANGE:major` 로 설정함으로써 major 번호 증가 조건은 위 표 2-1과 동일하게 유지하려고 한 것이다. 하지만 앞서 말했듯 이 설정이 유효한지는 이 글을 쓰고 있는 현재 시점에서 확인하지 못했다. 나중에 실제로 major 버전을 올릴 일이 있을 때 가서야 확인할 수 있을 것 같다. 

어쨌거나 이 step을 완료하고 나면 그 다음 step에서는 `ncipollo/release-action` 액션을 통해 앞서 생성된 버전 값들을 토대로 실제 release를 Github repo에 생성하는 작업을 거친다. 이전 step의 id값을 토대로 `steps.tag_version.outputs.new_tag` 와 같이 이전 step에서 도출된 값을 그대로 가져와 사용할 수 있음을 확인할 수 있다. 참고로 이전 step에서 어떠한 결과값(output)들이 나오는지도 해당 액션 공식 문서를 참고하였다. 

그 다음 `Log created release info` 이라는 step에서 필자는 정말로 생성된 태그 버전값이 예측대로 잘 생성되었는지 확인하기 위해 bash 문법을 이용하여 이를 출력하도록 하였다. 

이제 이 yml 파일을 Github repo에 push하여 저장하면 해당 워크플로우가 Github Actions에 등록된다. 그러면 해당 워크플로우에 명시된 특정 이벤트가 발생하면 이 워크플로우가 자동으로 트리거되어 결과적으로 Github에 tag 및 release를 자동으로 생성해준다. 

![사진 1-1. 필자의 라이브러리 repo 내 Actions 탭을 누른 후의 화면. 앞서 코드 2-1로 정의한 “Release versioning”이란 워크플로우가 왼쪽 사이드바 메뉴의 “All workflows”에 잘 등록된 것을 볼 수 있다. 화면 중앙에는 여태까지 실행된 워크플로우 내역이다. ](/images/2025-04-22/spoon-suits-Github%20Actions%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20Github%20tag%20%26%20release%20%EC%9E%90%EB%8F%99%ED%99%94/1.png)

사진 1-1. 필자의 라이브러리 repo 내 Actions 탭을 누른 후의 화면. 앞서 코드 2-1로 정의한 “Release versioning”이란 워크플로우가 왼쪽 사이드바 메뉴의 “All workflows”에 잘 등록된 것을 볼 수 있다. 화면 중앙에는 여태까지 실행된 워크플로우 내역이다. 

이후 chore 타입으로 커밋을 생성하고 이 Github repo에 push하면 앞선 코드 2-1로 정의한 워크플로우가 실행되기는 하지만 앞서 설정한 릴리즈 타입의 대상이 아니기에 결과적으로 워크플로우가 에러를 내면서 중단된다. 이로 인해 릴리즈가 되지 않는데, 이는 의도된 것이다. 반면 fix, test, refactor 등의 타입으로 커밋, push하면 워크플로우가 성공적으로 수행되어 새로운 버전으로 릴리즈된다. 

![사진 1-2. “test”라는 commit type으로 커밋 후 원격 저장소에 push, PR, merge 후의 모습. ](/images/2025-04-22/spoon-suits-Github%20Actions%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20Github%20tag%20%26%20release%20%EC%9E%90%EB%8F%99%ED%99%94/2.png)

사진 1-2. “test”라는 commit type으로 커밋 후 원격 저장소에 push, PR, merge 후의 모습. 

![사진 1-3. 사진 1-2에서의 커밋을 push한 후 “Release versioning”이란 워크플로우에 의해 자동으로 release가 된 결과. 사진 1-2의 커밋 내역과 비교했을 때 커밋 제목의 내용이 release의 change log로써 자동으로 기록되었다. 이를 통해 자동으로 다음 버전을 계산해서 릴리즈를 만들어줄 뿐만 아니라, change log도 자동으로 기록해줘 개발자의 수고로움을 덜어준다. ](/images/2025-04-22/spoon-suits-Github%20Actions%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%9C%20Github%20tag%20%26%20release%20%EC%9E%90%EB%8F%99%ED%99%94/3.png)

사진 1-3. 사진 1-2에서의 커밋을 push한 후 “Release versioning”이란 워크플로우에 의해 자동으로 release가 된 결과. 사진 1-2의 커밋 내역과 비교했을 때 커밋 제목의 내용이 release의 change log로써 자동으로 기록되었다. 이를 통해 자동으로 다음 버전을 계산해서 릴리즈를 만들어줄 뿐만 아니라, change log도 자동으로 기록해줘 개발자의 수고로움을 덜어준다. 

# 글을 마치며…

앞서 선보인 Github tag & release 자동화는 이미 커밋된 코드가 원격 저장소에 병합된 이후로 배포 준비 단계를 자동화한 것이기에 continuous delivery에 해당한다고 볼 수 있다. 그리고 이전 글에서 Jitpack을 이용하여 라이브러리를 배포하였다고 했는데, 이 과정은 continuous deployment에 해당한다고 볼 수 있겠다. 새 버전 릴리즈 후에는 Jitpack 사이트로 이동하여 해당 버전을 배포하는 버튼을 눌러야 해서 완전 자동화라고 보긴 조금 애매하긴 하지만, 그래도 이를 제외하면 배포 과정까지 모두 자동으로 수행해주기에 지속적 배포의 단계로 볼 수 있겠다. 

아무튼 이번에 spoon suits라는 라이브러리 제작을 통해 Github Actions과 Jitpack을 이용한 CI/CD까지 경험할 수 있었다(사실 CI 부분은 없지만…). 다시 한 번 느끼는 거지만, 실제 사소한 프로젝트라도 직접 만들어보면 그냥 공부할 때보다 더 많은 걸 얻어가는 것 같다. 

---

References

[1] [Github Action 사용법 정리](https://zzsza.github.io/development/2020/06/06/github-action/)

[2] 깃허브 공식 Github Actions 설명서

[GitHub Actions 설명서 - GitHub Docs](https://docs.github.com/ko/actions)

[3] CI, CD 및 Github Actions에 대한 설명

[GitHub Actions으로 배포 자동화해 보기(a.k.a CI/CD) - 1화 - 골든래빗](https://goldenrabbit.co.kr/2023/07/05/github-actions%eb%a1%9c-%eb%b0%b0%ed%8f%ac-%ec%9e%90%eb%8f%99%ed%99%94%ed%95%b4-%eb%b3%b4%ea%b8%b0a-k-a-ci-cd-1%ed%8e%b8/)

[4] Github Actions 또는 jitpack.yml에 공통적으로 쓰이는 bash shell script 문법

[Bash 입문자를 위한 핵심 요약 정리 (Shell Script)](https://blog.gaerae.com/2015/01/bash-hello-world.html)

[5] `${{ secrets.GITHUB_TOKEN }}` 에 대한 정보

[GITHUB_TOKEN으로 인증하기 \| TechWell](https://techwell.wooritech.com/docs/github-action/config-wf/token/)

[6] `${{ secrets.GITHUB_TOKEN }}` 에 대한 정보

[자동 토큰 인증 - GitHub Docs](https://docs.github.com/ko/actions/security-for-github-actions/security-guides/automatic-token-authentication#about-the-github_token-secret)

[7] jobs, with, steps 등 workflow에 사용되는 구문

[GitHub Actions에 대한 워크플로 구문 - GitHub Docs](https://docs.github.com/ko/actions/writing-workflows/workflow-syntax-for-github-actions)

[8] mathieudutour/github-tag-action에 관한 설명

[[Github Actions] 태그 생성 및 릴리즈 자동화 후 배포 파이프라인 작동](https://jaeseo0519.tistory.com/378)

[9] mathieudutour/github-tag-action에 관한 설명

[How to Automatically Bump and Tag Versions with GitHub Tag Action](https://cicube.io/workflow-hub/mathieudutour-github-tag-action/)

[10] mathieudutour/github-tag-action 공식 repo

[https://github.com/mathieudutour/github-tag-action](https://github.com/mathieudutour/github-tag-action)

[11] [스프링부트 공유라이브러리 만들고 jitpack으로 배포하기 - 1편](https://ssdragon.tistory.com/167)

[12] [Jitpack & github releases를 사용한 라이브러리 배포](https://swmobenz.tistory.com/42)

[13] CI/CD 개념

[CI/CD란 무엇인가 (Feat. DevOps 엔지니어)](https://artist-developer.tistory.com/24)

[14] CI/CD 개념

[[DevOps] CI/CD 파이프라인이란](https://llshl.tistory.com/50)

[15] 애플리케이션 빌드, 배포 관련 개념

[애플리케이션 배포환경 구성(애플리케이션배포)](https://code-nen.tistory.com/54)

[16] CI/CD 개념

[https://www.servicenow.com/kr/products/devops/what-is-cicd-pipeline.html](https://www.servicenow.com/kr/products/devops/what-is-cicd-pipeline.html)

[17] Github docs - schedule (cron) - 특정 시각마다 워크플로 실행 가능

[Events that trigger workflows - GitHub Docs](https://docs.github.com/en/actions/writing-workflows/choosing-when-your-workflow-runs/events-that-trigger-workflows#schedule)

[18] `actions/checkout` 관련 설명

[GitHub Actions의 체크아웃(Checkout) 액션으로 코드 내려받기](https://www.daleseo.com/github-actions-checkout/)

[19] `actions/checkout` 공식 repo

[Checkout - GitHub Marketplace](https://github.com/marketplace/actions/checkout)

[20] Semantic Versioning (SemVer)

[Semantic Versioning 2.0.0](https://semver.org/)

[21] sematic-release

[https://github.com/semantic-release/semantic-release](https://github.com/semantic-release/semantic-release)

{% endraw %}