---
title: "[CI DB 구축 - 3] Github docker service container를 이용하여 runner에서 테스트용 DB 인프라 구축하는 법"
category: "CI & CD"
tag: ["Database", "DB", "CI & CD", "CI", "Github", "Github Actions", "Docker", "Runner", "Testcontainer", "Container", "Image", "MariaDB"]
---

{% raw %}

이전 글 목록

- [[Spring Boot][CI DB 구축 - 1] SQL 스크립트를 이용한 데이터베이스 초기화](/spring/Spring-Boot-CI-DB-%EA%B5%AC%EC%B6%95-1-SQL-%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B2%A0%EC%9D%B4%EC%8A%A4-%EC%B4%88%EA%B8%B0%ED%99%94/)
- [[CI DB 구축 - 2] Github Actions 워크플로우 스크립트에 yml 설정 파일 주입하는 법](/ci%20&%20cd/CI-DB-%EA%B5%AC%EC%B6%95-2-Github-Actions-%EC%9B%8C%ED%81%AC%ED%94%8C%EB%A1%9C%EC%9A%B0-%EC%8A%A4%ED%81%AC%EB%A6%BD%ED%8A%B8%EC%97%90-yml-%EC%84%A4%EC%A0%95%ED%8C%8C%EC%9D%BC-%EC%A3%BC%EC%9E%85%ED%95%98%EB%8A%94-%EB%B2%95/)

필자가 스프링부트 기반 라이브러리를 제작하고 이에 대한 테스트 자동화를 구축하려고 했을 때 테스트용 애플리케이션이 DB와 상호작용하며 테스트를 진행해야만 했던 테스트 케이스가 있었다. 필자 개인 컴퓨터에선 이미 이전에 DBMS를 설치했기에 테스트 준비 및 실행 과정에 어려움이 없었지만, 문제는 Github Actions와 같은 곳에서 제공하는 가상 머신(runner)에서는 DB 인프라도 가상 머신 내에 별도로 구축해야 했다는 것이다. 필자는 이것을 세부적으로 어떻게 해야할지 몰라 한동안 CI 테스트 자동화 구축을 미뤄왔다. 그 후 조사와 학습 끝에 방법을 알아내 여기에 기록하고자 한다. 

필자가 조사한 결과, 다른 방법들도 있을 수 있겠지만 일단 2가지의 방법이 있음을 알게 되었다. 하나는 Github에서 자체 제공하는 docker service container를 이용하여 워크플로우 스크립트를 작성하여 이를 가져다 사용하는 방식이고, 또 하나는 Testcontainer라는 것을 사용하는 방법이다. 필자는 후자는 아직 사용해본 적이 없기도 하고, 실제 라이브러리에 대한 테스트 자동화 구축을 할 때에도 Github의 docker service container를 사용했으므로 이 글에서는 이에 대해서 집중적으로 다뤄보도록 하겠다. 

# 본격적인 DB 인프라 구축 전 알면 좋을 사전 지식 - Docker

그런데, Github에서 제공하는 docker service container를 이용하든 Testcotainer를 사용하든 이 둘의 공통점은 내부적으로 Docker라는 것을 사용한다는 점이다. 전자의 경우 필자가 실제로 이를 이용했을 때에는 Docker에 대한 지식 및 사용 방법을 몰라도 워크플로우 스크립트를 작성할 때 관련 문법만 알면 되었기에 문제 없이 구축할 수 있었다. 그럼에도 Docker에 대해 조금이라도 알아두면 좋을 것 같아 여기서 Docker에 대해 간단히 살펴보고자 한다. 

## Docker의 개념과 그 필요성

자바의 JDK를 자신의 컴퓨터에 설치하는 과정을 떠올리다보면 은근 번거롭다고 느껴진다. 일단 관련 사이트에서 JDK zip 파일을 받아 적당한 위치에 압축 해제하고, 시스템 환경 변수 설정도 일일이 해야 했다. 여기서 만약 어떤 프로젝트에서는 Java 8 버전만 썼다가 다른 프로젝트에서 Java 21 버전을 써야한다면 또 버전별로 설치해줘야 하며, 혹시라도 이 두 버전이 충돌하지 않게끔 신경쓰기도 해야한다. 한 편 내가 제작한 애플리케이션이 스프링부트를 기반으로 하였다면, 당연히 내 소스 코드가 동작하기 위해 스프링부트 관련 설정 및 준비들을 미리 해야한다. 그 과정에서 각 의존성들의 버전 호환도 고려해야할 것이다. 

내 컴퓨터에서야 번거롭더라도 그러한 환경들을 한 번만 세팅하면 그 후에는 추가적인 설정 필요없이 바로 앱을 실행할 수 있다. 하지만 내 앱을 AWS와 같은 클라우드에 업로드하여 서비스하거나 CI 파이프라인 구축할 때에는 내 로컬 컴퓨터가 아닌 다른 컴퓨터를 사용해야할 때가 있는데, 여기서도 내 컴퓨터 환경과 똑같이 구축해야할 때를 생각하면 여간 번거로운 일이 아니겠다. MariaDB와 같은 DBMS도 설치해야하고, JDK도 설치해야하고, 스프링부트에서 사용할 여러 의존성들도 각 버전 간 호환 여부를 생각하면서 챙겨야하고, 신경써야 할 것들이 많다보니 중간에 1, 2개는 설치하는 걸 까먹을 수도 있다. 

테스트와 같이 잠깐의 무언가를 위해 필요한 프로그램을 해당 컴퓨터에 “설치”한다면, 이후 해당 프로그램은 필요 없어지고 다른 프로그램을 써야 할 때에도 이미 해당 프로그램이 저장 용량, 메모리 등 해당 컴퓨터의 자원을 일부 차지, 소모하고 있을 수도 있다. 따라서 컴퓨터에 무언가를 “설치”한다는 것도 마냥 좋은 방법은 아닐 수도 있다. 이와 비슷하게, CI 테스트 자동화를 구축할 때에도 Github Actions 사용 시 Github에서 제공하는 가상 머신 runner는 테스트를 한 번 할 때마다 임의의 것으로 제공하기에 사실상 일회용 머신을 사용하는 것과 마찬가지이다. 따라서 여기에다가 무언가를 설치하기보다는 딱 일회용으로 쓰고 말끔히 버릴 수 있는 기술을 사용하는 편이 더 좋을 것이다. 

한 편, 이러한 상황도 있을 것이다. 

> A: 앱(또는 테스트)이  동작하질 않아요.
> 
> 
> B: 제 컴퓨터에서는 잘 되는데요?
> 
> A: 그렇다고 당신의 컴퓨터를 제가 차지하고 사용할 수는 없잖아요.
> 

이렇듯 각 컴퓨터 간의 결과의 차이는 각 컴퓨터에 설정된 환경의 미세한 차이로부터 비롯될 수도 있다. 내 개인 컴퓨터에서는 잘 동작하던 테스트가 CI 테스트 과정에서도 잘 동작하도록 하려면 일관적이고 동일한 환경이 필요하다. 

이렇듯 환경 설정 과정의 복잡함, 번거로움과 그에 드는 시간 비용, 일관적이고 동일한 환경 제공, 일회용 프로그램 사용 목적 등의 문제들을 모두 한꺼번에 해결할 수 있는 것이 바로 Docker이다. 

Docker는 애플리케이션의 작동, 배포, 제공을 편리하게 하기 위해 애플리케이션에 필요한 환경들을 컨테이너(Container)라고 하는 격리된 공간에 담아 관리하고 사용할 수 있게 해주는 플랫폼이다. 

비유를 들자면, 만약 전국 어디서든 동일한 맛의 음식을 현지 고객들에게 제공하고자 한다면 이를 어떻게 해야할까? 전국마다 공간을 임대하고 그 공간에 매번 근처에서 동일한 브랜드의 식기구들을 구매하고 챙겨와 환경을 조성해야할지도 모른다. 맛에 민감한 요리사라면 어쩌면 똑같은 가스레인지, 똑같은 냉장고, 똑같은 오븐도 새로 구매하여 챙겨야 하는데, 이 모두 무거운 것들이라 운반하기도 쉽지 않을 것이다. 하지만 아예 “푸드트럭” 방식으로 운영하면 어떨까? 전국 어딜 가든 하나의 트럭 안에 완전히 동일한 식기구와 가스레인지, 설거지 도구 등을 담아 사용할 수 있고, 심지어는 공간도 동일해서 요리할 때마다 움직여야 하는 동선조차 똑같아 매번 다른 공간에서 다른 동선들을 짜야할 필요성도 없어진다. 따라서 어디서든 동일한 맛의 음식을 고객들에게 제공할 수 있지 않을까? 이렇게 어디서든 동일한 식기구와 환경을 사용할 수 있도록 한 공간에 담아둔 “푸드 트럭”이 Docker의 container라는 개념과 유사하다고 볼 수 있다. 

## Docker와 관련된 개념들

Docker에는 여러 개념들이 있는데, 그 중 기본적인 개념들만 살펴보자.  

Docker container는 애플리케이션 동작에 필요한 여러 라이브러리, 의존성, 환경들을 외부와 격리하여 보관하는 공간이다. 도커 컨테이너를 사용하는 컴퓨터를 보통 호스트(host)라고 하는데, 도커 컨테이너는 호스트에서 실행 중인 독립적인 프로세스이다. 독립성을 가지기에 다른 컨테이너의 영향을 받지 않는다고 하며, 심지어는 호스트 OS와도 격리되어 있어 어느 OS이건 그 컴퓨터에 Docker만 설치되어 있다면 바로 사용이 가능하다. docker container는 그 자체로도 실행 가능해야하기에 이미 내부에 구동에 필요한 의존성 라이브러리들이나 설정 파일들이 들어 있다. 따라서 실행 시에는 별도의 추가적인 의존성 설치가 불필요하다. 호스트와도 격리되어 있기에 호스트 PC에 무언가를 설치하는 과정이 없으며, 따라서 해당 container가 더 이상 쓸모가 없어 삭제하더라도 해당 PC에 남는 관련 데이터, 환경 변수 등이 존재하지 않는다. 즉, 깔끔한 삭제가 가능하다는 것이다. 

Docker image는 container를 생성하는데에 필요한 이진 파일, 라이브러리, 설정 파일, 그리고 이들을 모아 container로 만드는데 필요한 명령어들이 들어 있는 템플릿 또는 패키지이다. 예를 들어 스프링부트로 제작한 앱이라면 이 앱이 실행될 런타임 환경, 앱을 구성하는 소스 코드 및 의존성, 라이브러리들이 포함되어 있다고 보면 되겠다. 어떻게 보면 docker image는 붕어빵 틀, container는 그로부터 생산된 붕어빵이라 봐도 될 듯 싶다(클래스와 객체의 관계와 같다고 보면 되겠다). 이러한 특성에 의해 단 하나의 image만 있어도 어떤 컴퓨터에서든 설치되어 있는 Docker 엔진을 통해 동일한 container를 생성하여 호스트 컴퓨터 내에서 실행시킬 수 있다. 

Docker registry는 이러한 Docker image들을 저장해놓는 중앙집중적 저장소이다. 잘 알려진 Docker registry로 “[Docker hub](https://hub.docker.com/)”라는 사이트가 있다. 마치 소스 코드를 저장하는 유명한 원격 저장소 중 하나인 Github와 비슷한 맥락이라 보면 되겠다. 여기에는 MariaDB 등 유명한 프로그램들의 image들도 있어 얼마든지 가져다 사용할 수도 있고, 또는 반대로 개발자가 docker image를 제작하고 여기에 업로드할 수도 있다. 

만약 누군가 만들어놓은 docker image를 가져다 쓰고자 한다면 다음의 과정을 거친다. 

1. Docker hub와 같은 Docker registry로부터 원하는 docker image를 호스트 PC에 내려 받는다. 이 과정을 `docker pull`  또는 `docker run` 이라는 명령어로 수행할 수 있다. 
2. docker image를 받은 호스트 PC에서 해당 image를 토대로 새로운 docker container 인스턴스를 생성한다. 이 과정을 명령어로 수행한다면 `docker run` 을 입력하면 되겠다. 
    1. `docker run` 이라는 명령어에는 만약 호스트 PC 내에 해당 docker image가 없다면 자동으로 docker registry로부터 해당 image를 다운로드를 받는 기능이 포함되어 있다. 

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
  <img width="80%" src="/images/2026-01-24/%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%203%5D%20Github%20docker%20service%20container%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20runner%EC%97%90%EC%84%9C%20%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%9A%A9%20DB%20%EC%9D%B8%ED%94%84%EB%9D%BC%20%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94%20%EB%B2%95/1.png" alt="image">
  <p>그림 1-1. 이미 존재하는 docker image를 내려받아 호스트 PC에 실행하는 과정을 묘사한 그림</p>
</div>

# Github에서 제공하는 Docker service container를 사용하여 DB를 구축하는 방법

Github Actions에서는 Docker service container라는 것을 제공하는데, 이는 사실 docker container와 같다고 보면 된다. 단지 원래 Docker를 사용할 때에는 Docker 설치 과정을 거치고 그 후 관련 명령어를 사용자가 입력해줘야 docker container 생성 및 실행을 할 수 있는데, Github Actions에서는 이 과정을 모두 자동으로 처리해주기에 사용자가 이에 대해 신경쓰지 않아도 된다. 사용자는 어떤 docker image를 사용할 것인지 워크플로우 스크립트에 `services` 속성을 통해 지정해주기만 하면 된다. 

단, Docker 자체는 리눅스 기반 기술이기에 이를 사용하는 호스트 PC의 OS도 리눅스 또는 우분투와 같은 리눅스 기반 OS여야 한다. 따라서 Github Actions 사용 시 제공되는 runner의 OS는 반드시 Linux 또는 Ubuntu를 사용해야 한다. 이는 워크플로우 스크립트에서 `runs-on: ubuntu-latest` 와 같이 지정하면 된다. 

<div class="single-image">
  <img width="90%" src="/images/2026-01-24/%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%203%5D%20Github%20docker%20service%20container%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20runner%EC%97%90%EC%84%9C%20%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%9A%A9%20DB%20%EC%9D%B8%ED%94%84%EB%9D%BC%20%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94%20%EB%B2%95/2.png" alt="image">
  <p>그림 2-1. Github Actions를 이용하여 CI 테스트 자동화 구축 시 필요한 DB를 docker를 이용하여 구축하고 테스트용 앱과 연동하여 테스트를 진행하는 과정을 묘사한 그림. 파란색으로 칠한 docker 관련 작업들은 Github Actions에 의해 자동으로 수행된다.</p>
</div>

워크플로우 스크립트의 `services` 속성값을 지정함으로써 원하는 docker image를 자동으로 runner(host)로 가져와 container로 만들어 실행시킨다. 필자의 경우 MariaDB가 필요했기에 MariaDB라는 docker container가 runner 내에서 실행될 것이다. 그 후, Github에 저장되어 있는 repo의 소스 코드를 runner로 가져와 이를 빌드하고, 앞서 생성한 MariaDB container와 DB 연동을 한다. 그 후 테스트를 실행하면 DB와 연동하여 진행되어야 하는 테스트도 문제없이 동작한다. 

위와 같은 모든 과정은 단 하나의 워크플로우 스크립트 작성으로 수행할 수 있다. 필자는 다음과 같이 `build_and_test` 라는 하나의 job을 정의하였고, 그 안에 `services` 속성을 이용하여 MariaDB의 특정 버전의 docker container를 사용하고자 설정하였다. 

```yaml
# 생략...

jobs:
  build_and_test:
    runs-on: ubuntu-latest
    permissions:
      checks: write
      pull-requests: write

    # DB 환경 테스트를 위한 MariaDB 환경 설정
    services:
      mariadb:
        image: mariadb:11.4
        env:
          MARIADB_DATABASE: test_spoonsuits
          MARIADB_ROOT_PASSWORD: ${{ secrets.DB_ROOT_PASSWORD }}
          MARIADB_USER: ${{ secrets.DB_USERNAME }}
          MARIADB_PASSWORD: ${{ secrets.DB_PASSWORD }}
        ports:
          # runner : docker container (MariaDB)
          - 3306:3306
        options: >-
          --health-cmd "healthcheck.sh --connect --innodb_initialized"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 3
     
    # 생략...
```

코드 1-1. `ci-test.yml` 중 MariaDB를 구축하는 스크립트 일부.

한 가지 주의할 것은, Github Actions 내에서의 docker container는 하나의 job 내에서만 사용 가능하다. Github Actions에서는 하나의 job을 하나의 runner에서 동작시킨다. 그래서 여러 개의 job들은 각자의 runner에서 동작한다. 따라서 A라는 runner에 MariaDB라는 docker container를 사용하고 있다면 이를 B라는 전혀 다른 runner에서 접근하여 사용할 수 없다. 이러한 특성에 의해 docker container를 사용하기 위한 `services` 속성값은 반드시 특정 job 내부에서 설정해야 한다. 

한 편 위 코드에서는 MariaDB를 사용할 것이기에 `mariadb` 라는 속성명을 입력하였다. image는 필자가 개인 컴퓨터에서 사용하는 버전과 최대한 동일한 버전을 사용하도록 `mariadb:11.4` 와 같이 설정하였다. 만약 언제든지 가장 최신 버전을 사용하고자 한다면 대신 `mariadb:latest` 와 같이 작성하면 된다. 참고로 MariaDB docker image 버전은 “[mariadb docker hub](https://hub.docker.com/_/mariadb)” 사이트에서 참고하였다. 

`env` 속성에는 MariaDB에서 사용할 데이터베이스 이름, root 계정의 비밀번호 및 root 계정 하에 있는 사용자 계정을 설정하고 있다. 원래 MariaDB에서는 새로운 데이터베이스를 생성하려면 `CREATE DATABASE ...` 구문을 사용하고, 새로운 사용자 및 그에 맞는 권한을 주고자 한다면 `GRANT ...` 라는 DCL 언어를 토대로 쿼리문을 사용자가 직접 작성, 실행했어야 했을 것이다. 하지만 Github Acitons 내에서는 그저 root 계정의 패스워드, root 계정 하에 생성할 새로운 사용자 계정의 이름 및 패스워드만 지정해줘도 이를 토대로 root 계정 및 사용자 계정을 자동으로 생성해줘서 매우 편리하다. 

MariaDB docker image에서 사용 가능한 `env` 속성들에는 이 외에도 여러 가지가 있는데, 이에 대한 자세한 사항은 MariaDB 공식 사이트 내 페이지인 “[MariaDB Server Docker Official Image Environment Variables](https://mariadb.com/docs/server/server-management/automated-mariadb-deployment-and-administration/docker-and-mariadb/mariadb-server-docker-official-image-environment-variables)”이라는 문서를 참고하면 되겠다. 

`ports` 속성에는 runner의 port와 MariaDB docker container의 port를 지정해 서로 연결시킬 때 사용하는 속성이다. `3306:3306` 과 같이 입력되어 있는데, 왼쪽 값은 runner의 port값이고, 오른쪽 값은 MariaDB docker container의 port값이다. MariaDB의 경우 기본 포트 값은 `3306` 으로 되어 있어 이를 사용하였다. 

여기까지는 MariaDB라는 docker container를 가져와 설정하는 과정이었는데, 설령 이렇게 runner 안에 MariaDB를 구축했다고 하더라도 정말로 이 MariaDB가 잘 작동하고있는지 별도로 확인하는 것, 즉 health check를 하는 것이 좋을 것이다. 만에 하나 MariaDB가 잘 작동하지 않는데 테스트라는 다음 단계로 넘어가면 당연히 해당 작업은 실패할 것이기에 이 과정을 거치도록 해보자. `options` 속성의 첫 번째 줄 `--health-cmd "healthcheck.sh --connect --innodb_initialized"` 이라는 명령어를 통해 MariaDB에 대한 health check를 수행한다. MariaDB docker container 내에는 `healthcheck.sh` 라는 파일이 내장되어 있어 이 파일을 실행시키면 자동으로 헬스 체크를 해준다고 한다. 사실 이는 MariaDB 11 버전부터 생겨난 것으로, 그 이전에는 `mysqladmin ping` 이라는 명령어가 잘 알려져 있었다. 필자가 참고한 자료들에는 `mysqladmin ping` 으로 헬스 체크를 하는 자료들이 많아서 이를 그대로 사용했다가 에러를 겪은 적이 있었다. 이 에러를 해결해나가는 과정에 대한 자세한 사항은 필자가 직접 작성한 “[[Troubleshooting][CI Test] CI 테스트를 위한 MariaDB 도입 관련 문제](https://github.com/JeroCaller/Spoon-Suits/issues/93#top)” 페이지를 참고하길 바란다. 

`--health-interval 10s` 에서는 이 health check를 몇 초 간격으로 수행할 것인지를 지정한다. 여기서는 10초마다 체크하도록 하였다. 그러면 10초마다 앞선 `--health-cmd "healthcheck.sh --connect --innodb_initialized"` 명령어를 자동으로 실행하여 health check를 수행할 것이다. 

`--health-timeout 5s` 에서는 health check를 위해 MariaDB에게 요청을 보낸 후 몇 초 이내에 응답을 하지 않으면 실패로 간주할 것인지를 설정하는 명령어다. 여기서는 5초로 지정하여 5초 이내로 응답이 오지 않는다면 MariaDB 서버의 상태가 비정상적인 것으로 간주한다. 

`--health-retries 3` 에서는 MariaDB에 대한 health check를 실패했을 때 재시도할 횟수를 의미한다. 여기서는 3으로 지정하였는데, health check가 3번 연속 실패한다면 그제서야 MariaDB 서버가 비정상적인 상태라 보고 실패라고 결정짓는다. 이렇게 MariaDB가 결과적으로 비정상적 상태로 판명되면 그 다음 과정인 테스트를 진행할 수 없게 된다. 

이러한 `options` 속성값을 주면 최종적으로 테스트가 진행되기 전에 MariaDB가 초기화되는 시간을 벌어주며, 초기화에 성공하여 MariaDB의 상태가 정상적임을 확인하면 자동으로 다음 작업으로 넘어갈 수 있도록 해준다. 

이 다음에는 `steps` 속성을 통해 테스트 과정을 기술하면 된다. 필자의 경우 앞선 코드 1-1을 포함하는 전체 워크플로우 스크립트는 다음과 같다.

```yaml
name: ci-test

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

    # DB 환경 테스트를 위한 MariaDB 환경 설정
    services:
      mariadb:
        image: mariadb:11.4
        env:
          MARIADB_DATABASE: test_spoonsuits
          MARIADB_ROOT_PASSWORD: ${{ secrets.DB_ROOT_PASSWORD }}
          MARIADB_USER: ${{ secrets.DB_USERNAME }}
          MARIADB_PASSWORD: ${{ secrets.DB_PASSWORD }}
        ports:
          # runner : docker container (MariaDB)
          - 3306:3306
        options: >-
          --health-cmd "healthcheck.sh --connect --innodb_initialized"
          --health-interval 10s
          --health-timeout 5s
          --health-retries 3

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

      # Gradle로 build 시, build.gradle에 설정한 내용에 의해
      # 'test', 'jacocoTestReport', 'jacocoTestCoverageVerification' task들이 순차적으로 자동 실행된다.
      - name: Build with Gradle to get a result of test
        run: ./spoonsuits-local-test/gradlew build -PenableLocalTest=true

      # 테스트 결과를 PR comment로 출력
      - name: Publish test results
        uses: EnricoMi/publish-unit-test-result-action@v2
        # 이전 task에서 테스트 실패 시 task도 실패하는데, 이 task의 성공 여부에 상관없이 무조건 이 작업을 실행.
        if: always()
        with:
          files: |
            spoonsuits-local-test/build/test-results/test/TEST-*.xml

      - name: Upload coverage reports to Codecov
        uses: codecov/codecov-action@v5
        with:
          token: ${{ secrets.CODECOV_TOKEN }}
```

코드 1-2. `ci-test.yml` 전체 내용

위 스크립트에서는 `services` 를 통해 먼저 MariaDB 서버를 runner 내에 구축한 뒤, `steps` 를 통해 소스 코드 체크아웃 및 필요한 JDK 설정 → 필요한 환경설정 파일 생성 및 환경설정 값 주입 → 테스트 진행 → 테스트 결과 및 코드 커버리지 결과를 Pull Request(PR) 댓글에 자동으로 표시해주는 작업들을 순차적으로 정의하여 CI 테스트 자동화 파이프라인을 구축하였다. 

위 스크립트를 Github repo에 업로드하면 그 이후부터 모든 PR에 대해 자동화된 테스트가 진행될 것이다. 그러면 테스트를 모두 통과한 PR에 대해서만 merge가 가능하도록 제어하는 기초가 되며, 원한다면 특정 code coverage를 넘겨야만 merge할 수 있도록 강제할 수도 있다. 이에 대한 자세한 사항은 필자가 이전에 작성한 글인 “[CI & CD에 테스트 자동화 및 코드 커버리지 추가하기 (with Github Actions, Java, Jacoco)](/ci%20&%20cd/CI-CD%EC%97%90-%ED%85%8C%EC%8A%A4%ED%8A%B8-%EC%9E%90%EB%8F%99%ED%99%94-%EB%B0%8F-%EC%BD%94%EB%93%9C-%EC%BB%A4%EB%B2%84%EB%A6%AC%EC%A7%80-%EC%B6%94%EA%B0%80%ED%95%98%EA%B8%B0-(with-Github-Actions,-Java,-Jacoco)/)” 글을 참고하면 되겠다. 

`services` 을 통해 runner 내부로 가져와 실행하는 docker container는 해당 job이 끝나면 자동으로 삭제된다. 따라서 사용자가 별도로 container를 삭제할 필요도 없고, runner에도 container 인스턴스가 남아있지 않아 깨끗한 상태로 되돌릴 수 있다. 

# 그 외 CI 테스트 자동화 과정에서 DB를 구축하는 방법

## Testcontainer

앞서 서두에서 언급했듯, 이 글에서 주로 소개한 Github Actions의 Docker service container 방식 말고도 CI 파이프라인에서 테스트용 DB를 구축하는 또다른 방법으로 [Testcontainer](https://testcontainers.com/)라는 외부 라이브러리라는 것도 존재한다. 이에 대해 간단히 소개하겠다. 

Testcontainer는 테스트 실행 시 사전에 필요한 DB와 같은 서비스들을 docker container를 이용하여 미리 띄운 뒤 이를 토대로 테스트를 진행할 수 있게끔 하는 라이브러리이다. 다만 기존에 이 글에서 설명한 Github Actions의 방식과 다른 점은, 테스트에 필요한 DB docker container를 프로그래밍 언어의 코드로 설정하고 띄운다는 점이다. 

```java
GenericContainer container = new GenericContainer("postgres:15")
    .withExposedPorts(5432)
    .waitingFor(new LogMessageWaitStrategy()
        .withRegEx(".*database system is ready to accept connections.*\\s")
        .withTimes(2)
        .withStartupTimeout(Duration.of(60, ChronoUnit.SECONDS)));
container.start();
var username = "test";
var password = "test";
var jdbcUrl = "jdbc:postgresql://" + container.getHost() + ":" + container.getMappedPort(5432) + "/test";
//perform db operations
container.stop();

```

코드 2-1. Testcontainer 공식 문서에서 발췌한 예시 코드. Java 코드로 필요한 container를 설정하고 있다.

Testcontainer에는 어떤 프로그램의 docker image를 사용할지 그에 매핑되는 자바 API가 이미 마련되어 있어 개발자는 이를 이용하여 테스트 코드 단에서 어떤 docker container를 사용할지 선언하기만 하면 된다. 위 예제에서는 postgreSQL라는 DBMS docker container를 사용하도록 API를 사용한 상태이다. 

이렇게 설정해놓으면 Testcontainer API 호출에 의해 먼저 docker container가 실행된다. 그 후에 테스트가 실행된다. Testcontainer에서는 자체적으로 대기 전략들을 사용하기에 테스트가 실행되기 전에 container가 먼저 초기화되는 것을 보장한다. 테스트가 끝나면 자동으로 Testcontainer에 의해 생성된 container 등의 리소스들을 자동으로 삭제해준다. 이는 테스트가 오류로 인해 중간에 예기치 않게 중단되어도 문제없이 수행된다. 

Testcontainer도 docker를 사용하므로, 호스트 PC에서는 docker 엔진을 필요로 한다. 다만 테스트에 필요한 DB 등의 인프라 구축을 위해 개발자가 수동으로 직접 Docker 명령어를 실행하는 방식에 비하면 더 추상화되어 있고 더 사용하기 쉬운 기술이다. 개발자가 수동으로 Docker를 사용하면 DB 초기화 이전에 테스트가 실행되는 실수를 하거나, 테스트가 끝난 후 DB를 제대로 삭제하지 않아 다음 테스트에도 영향을 줘 비일관적인 테스트 결과가 나오기 쉽상이다. Testcontainer는 필요할 때만 관련 container를 실행하면서도, 초기화 후 테스트 실행 및 깔끔한 리소스 삭제 등의 기능들이 자동화되어 있다는 것이 차이점이자 장점이라 할 수 있다. 

앞서 소개한 Github Actions의 docker service container 방식과 비슷하게 Testcontainer도 Docker 기반이기에 DB와 같은 인프라 구축 과정의 대부분을 자동화해줘 편리하다는 장점이 있다. 그러면서도 Testcontainer는 테스트에 필요한 Docker container를 코드 단계에서 작성하는 방식이기에 로컬 환경이든 CI 환경이든 어디서든 별도의 추가 설정 없이 바로 사용할 수 있으며, Github Actions, Jenkins 등 CI 도구와도 독립적이기에 이식성이 뛰어나다는 장점도 있다. 즉, 어떤 CI 도구를 사용하든 문제없이 테스트를 실행할 수 있다는 것이다. 

Testcontainer는 자바 뿐만 아니라 Node.js, Python 등 여러 프로그래밍 언어에서도 지원하기에 자바가 아닌 다른 언어를 사용하고 있더라도 충분히 고려할만한 라이브러리라 할 수 있겠다. 

다만, 테스트가 실행될 때마다 관련 Docker container가 실행되고 삭제되는 방식이기에, 이러한 테스트들이 많으면 많을수록 그만큼 Docker container들의 생성 및 삭제 빈도수도 많아지기에 컴퓨터 리소스에 부담을 줄 수 있다는 단점이 있다. 따라서 같은 DB 환경을 사용하는 테스트들이 많다면 이에 맞게 같은 DB container를 공유하도록 하는 것이 좋겠다. 물론 이 과정에서 이전 테스트에서 생겨난 데이터가 제대로 삭제되지 않아 다음 테스트에 영향을 줄 수 있어 테스트 간 독립성도 해치지 않는 선에서 잘 조절해야할 것이다. 

또한, 기존 로컬 기기에 이미 MariaDB 등 테스트 환경이 이미 구축되어 있는 경우와 비교하면, Testcontainer를 사용한 방식은 테스트를 실행할 때마다 매번 docker가 실행되기에 테스트를 실행하기까지의 시간이 상대적으로 더 걸린다고 한다. 그리고 윈도우 OS에서도 잘 돌아가던 테스트에 비해, Testcontainer는 Docker를 필요로 하므로 별도의 리눅스 OS를 가지는 가상 머신 또는 윈도우의 경우 WSL를 이용해 리눅스 환경을 미리 조성해줘야한다는 것도 단점이라면 단점이라고 할 수도 있겠다. 

이미 테스트 코드를 Testcontainer를 고려하지 않고 작성한 경우, 기존에 테스트 코드를 작성한 상태에서 Testcontainer로 마이그레이션한다면 기존의 테스트 코드를 건드려야한다. 하지만 Github Actions를 사용할 것이라면 docker service container를 대신 이용하면 테스트 코드를 건드리지 않고 오로지 워크플로우 스크립트 단계에서 다 해결할 수 있기에 이와 대비된다. 기존에 작성한 테스트 코드의 양이 너무 방대하거나 하여 리팩토링하기 힘든 경우에도 사실 부담스러운 선택지가 될 수도 있다. 필자도 실제로 이미 테스트 코드를 작성한 상태라 테스트 코드를 건드리기보다는 워크플로우 스크립트 단에서 DB 인프라를 구성하는 게 더 간편하기도 하고, 테스트 코드를 잘못 건드리는 실수를 애초에 방지할 수도 있다는 생각을 해서 Github Actions의 docker service container를 사용하였다. 

이러한 사실들을 토대로 프로젝트마다 어떤 테스트 환경을 사용할 것인지, 즉 Github Actions 사용 시 docker service container를 이용할지 아니면 Testcontainer를 이용할 것인지 판단하는데에 도움이 되길 바란다. 

{% endraw %}

---

References

[1] Testcontainers 공식 홈페이지

[Testcontainers](https://testcontainers.com/)

[2] Testcontainers spring boot

[Testcontainers :: Spring Boot](https://docs.spring.io/spring-boot/reference/testing/testcontainers.html)

[3] [Spring Boot에서 Testcontainers로 통합 테스트 환경 구축하기](https://dami97.tistory.com/73)

[4] [Github Actions Database Test 도입하기](https://velog.io/@dev_hyun/GithubActions-Database-Test-%EB%8F%84%EC%9E%85)

[5] [Using the MySQL Service with Github Actions](https://firefart.at/post/using-mysql-service-with-github-actions/)

[6] [Github Actions로 데이터베이스(MySQL) 연동하여 빌드 및 테스트 자동화(CI)](https://shnowball.tistory.com/58)

[7] Github 공식 문서 - Docker service container

[Docker 서비스 컨테이너와 통신 - GitHub 문서](https://docs.github.com/ko/actions/tutorials/use-containerized-services/use-docker-service-containers)

[8] mariadb docker

[mariadb - Official Image \| Docker Hub](https://hub.docker.com/_/mariadb)

[9] mariadb docker container 실행 실패 문제에 대한 가능한 해결책

[https://github.com/MariaDB/mariadb-docker/issues/497](https://github.com/MariaDB/mariadb-docker/issues/497)

[10] mariadb docker container 실행 실패 문제에 대한 가능한 해결책

[MariaDB Server Docker Official Images Healthcheck without mysqladmin](https://mariadb.org/mariadb-server-docker-official-images-healthcheck-without-mysqladmin/)

[11] docker 관련 설명

[M1-2 Docker의 개념](https://wikidocs.net/289828)

[12] docker 관련 설명

[[Docker] Docker란 무엇일까요? / Docker 개념 및 설명](https://junesker.tistory.com/88)

[13] docker 공식 사이트

[Docker: Accelerated Container Application Development](https://www.docker.com/)

[14] dockerdocs - What is Docker?

[What is Docker?](https://docs.docker.com/get-started/docker-overview/)

[15] Daemon 개념

[👩‍💻 프로세스 / 데몬 / 서비스 차이 한방 정리](https://inpa.tistory.com/entry/%ED%94%84%EB%A1%9C%EC%84%B8%EC%8A%A4-%EB%8D%B0%EB%AA%AC-%EC%84%9C%EB%B9%84%EC%8A%A4-%EC%A0%95%EB%A6%AC)

[16] 내 글 - “[Spoon Suits] Github Actions를 이용한 Github tag & release 자동화”

[[Spoon Suits] Github Actions를 이용한 Github tag & release 자동화](/personal%20project/spoon-suits-Github-Actions%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%9C-Github-tag-&-release-%EC%9E%90%EB%8F%99%ED%99%94/)