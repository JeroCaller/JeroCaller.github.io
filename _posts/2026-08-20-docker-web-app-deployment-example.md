---
title: "[Docker] 웹 앱을 로컬에서 도커로 띄워보기 예시"
category: "Infra & Cloud"
tag: ["Docker", "Docker Compose", "Secrets", "Docker secrets", "WSL", "WSL 2", "Docker Desktop", "Spring Boot", "Spring", "React", "Dockerfile", "image", "Docker image", "3 tier architecture", "Web", "Web app", "nginx"]
---

참고하면 좋을 이전 글들

- “[**[JDBC][Server] Server and DB architecture - Web server VS WAS(Web Application Server)**](/servlet%20&%20jsp/jdbc-server-server-and-db-architecture/#web-server-vs-wasweb-application-server)”
- “[**[Docker] Docker compose를 이용하여 여러 컨테이너들을 손쉽게 실행해보자!**](/infra%20&%20cloud/docker-compose/)”

지금까지는 Docker의 기본적인 개념, 사용법 등에 대해 여러 글에 거쳐 정리해왔다. 중간에 실습도 진행함으로써 도커 사용법과 개념에 대해 머릿속에 정리가 될 수 있게끔 글을 구성하려고 노력하였다. 하지만 정말 도커 개념 이해 및 사용법 숙지를 위한 실습이라 체감상 그다지 실무적인 도움이 되지 않을 수도 있을 것이다. 그래서 이번 시간에는 조금이나마 도커 사용에 실질적으로 도움이 될지도 모를 실습 내용을 이 글에서 정리해보고자 한다. “도움이 될지도 모를”이라고 하는 이유는 필자가 실제로 이 글에서 소개할 실습을 진행할 당시 어디 실제로 클라우드 서비스나 홈 서버에 배포한 것도 아니라서 완전히 실무적인 실습 예시라고는 하기 어려운 것 같다고 판단하기 때문이다. 그럼에도 3 tier 아키텍처로 구성된 웹 앱을 로컬에서나마 도커로 띄워보는 것 자체는 실제로 웹 앱이 동작하기도 하기에 도커 기본 내용에서 다룬 정말 기본적인 실습들보다는 조금이나마 실무에 도움이 되지 않을까 싶어 이렇게 글을 작성한다[^1]. 나중에 기회가 된다면 홈 서버 또는 클라우드 배포 시의 도커 사용 예시에 대해 다뤄보고자 한다. 

# 실습 계획

Docker는 보통 단 한 개의 컨테이너만 동작시키기보다는 여러 컨테이너들을 동작시켜 하나의 서비스를 제공하곤 한다. 대표적인 예시가 3 tier architecture로 구성된 웹 앱이다. 웹 앱의 경우 web server + WAS + DB 이렇게 3 계층으로 구성되어 있어 적어도 3개의 컨테이너들을 동시에 띄우고 서로 통신이 가능하게끔 해야해서 멀티 컨테이너 환경 실습에 적합하다고 생각한다. 따라서 이 글에서도 해당 아키텍처를 기반으로 하는 웹 앱을 도커로 띄워 실제로 웹 사이트 방문이 되는지 확인하는 시간을 갖고자 한다. 

이 글에서 진행할 실습은 하나의 로컬 PC 내에서 각각 web server, WAS, DB에 해당하는 프로그램들을 각각 컨테이너로 띄워 웹 앱을 로컬에서 테스트해보는 식으로 진행할 것이다. 

조금 더 구체적인 실습 계획은 다음과 같다. 프로젝트 폴더 내부에 미리 준비한 프론트엔드, 백엔드 소스 코드를 각 폴더로 구분하여 넣을 것이다. 이 때 각 부분에 대해 Docker image 빌드가 필요하기에 각 폴더 내부에 `Dockerfile` 을 배치할 것이다. 그리고 루트 폴더 내부에 `compose.yaml` 파일도 넣는다. 

여기서는 WSL 2, Ubuntu, Docker Desktop을 이용하여 하나의 PC 내에서 web server, 백엔드 애플리케이션, DB 각각을 컨테이너로 띄워 실습하는 구조로 진행할 것이다. 

실습 대상이 되는 웹 앱은 회원가입, 로그인 및 이미지 파일을 업로드, 다운로드하는 간단한 이미지 앨범 앱이다. 필자가 예전에 관련 연습을 할 때 만들었던 예시 프로젝트들을 백엔드와 프론트엔드 모두 하나의 프로젝트 폴더로 모아 Docker compose를 이용한 웹 앱 배포 연습용으로 만들었다. 해당 Github repo는 다음의 페이지에서 확인 가능하다. 

- [https://github.com/JeroCaller/local-docker-web-app-example](https://github.com/JeroCaller/local-docker-web-app-example)

다음은 해당 웹 앱을 실행해보는 시연 영상이다. 아래 시연 영상들은 개발 환경에서 테스트한 것인데, Docker 배포 환경에서도 동일하게 실행된다. 

![](https://raw.githubusercontent.com/JeroCaller/local-docker-web-app-example/main/readme-resources/image-album-signin.gif)

![](https://raw.githubusercontent.com/JeroCaller/local-docker-web-app-example/main/readme-resources/image-album-upload-and-download-image.gif)

이 웹 앱 프로젝트에 사용한 기술들은 다음과 같다.

- Frontend
    - React (CRA)
    - Nginx (web server)
- Backend
    - Spring Boot 3.4.1
    - Spring Data JPA
    - Gradle
    - MariaDB
    - REST API
- Deployment
    - WSL 2
    - Ubuntu 26.04 LTS
    - Docker Desktop 4.81.0
    - Docker Compose v5.2.0

<details id="ref-1">
<summary>
    <strong>
        <i>참고 - 3 tier architecture</i>
    </strong>
    <a href="#ref-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
3 tier architecture는 애플리케이션을 3개의 논리적 또는 물리적 컴퓨팅 티어로 나눠 개발하는 소프트웨어 아키텍처 중 하나이다. 각 티어들은 다음과 같다.
    
- 프레젠테이션 티어: 웹 개발에서는 웹 서버가 위치하는 영역이다. 사용자가 애플리케이션과 상호작용할 때 직접적으로 상호작용하는 티어이며, 사용자 인터페이스를 제공한다. 웹 개발의 경우, 정적 파일인 HTML, CSS, JS 파일들을 통해 UI를 제공한다. 3 tier architecture에서는 인접한 티어끼리만 상호작용이 가능하기에 이 티어에서 바로 DB가 있는 데이터 티어로 접근할 수 없고, 인접한 애플리케이션 티어를 통해 데이터를 요청해야한다.
- 애플리케이션 티어: WAS, 즉 백엔드 애플리케이션이 위치한 영역. 여기서 사용자 요청을 처리하는 비즈니스 로직이 포함된다. 이 티어의 양 옆으로 각각 프레젠테이션 티어, 데이터 티어와 맞닿아 있기에 중간 매개체 역할을 해서 미들 티어라고도 불린다. 이 티어를 통해 프레젠테이션 티어를 거쳐 들어오는 사용자 요청을 처리하기 위해 데이터 티어와 상호작용한다. 이 과정에서 필요한 데이터를 가져오거나 새 데이터를 삽입하는 등의 CRUD 작업을 데이터 티어에 요청하고, 그로부터 얻은 결과를 다시 프레젠테이션 티어에 넘긴다. 이를 통해 프레젠테이션 티어에서 사용자에게 응답하는 구조이다.
- 데이터 티어: 데이터베이스 서버가 존재하는 영역. 데이터를 저장, 관리하고 이에 대한 CRUD 작업이 가능한 공간이다.
    
이렇게 애플리케이션을 구성하는 요소들이 크게 3개의 티어로 나뉘며, 각 인접한 티어끼리만 상호작용이 가능하다는 측면에서는 layered architecture와 많이 닮았다. 
    
엄밀히 구분하자면 tier와 layer는 의미가 조금 다르다고 한다. layer는 하나의 소프트웨어 안에서 기능적으로 구분된 구역을 의미하지만, tier는 별도의 독립적인 인프라에서 동작하는 구역을 의미한다. 예를 들어 스프링부트로 웹 앱 제작 시 흔히 사용하는 controller - business - repository로 구분된 각 영역들은 layer라고 부르지만, 이렇게 만들어진 소프트웨어 여러 개가 각자의 호스트 서버 또는 VM에서 동작할 때에는 tier라고 부르는 것이다. 
    
다만, tier라는 용어는 각 소프트웨어가 반드시 서로 다른 물리적 서버에서 돌아가야만 성립하는 것은 아니다. 같은 물리적 서버라도 그 내부에서 VM 여러 개를 띄워 논리적으로 서로 격리된 환경의 서버들을 생성하여 운영할 수도 있는 것이다. 
    
이러한 의미에서 보았을 때, web server, WAS, DB 서버들을 하나의 동일한 호스트 서버에서 실행한다 하더라도 Docker container를 이용하여 운영한다면 각자 운영체제의 사용자 공간(user space)을 별도로 가지고 서로 통신하는 방식으로 운영되므로 이 경우에도 “3 tier architecture”라고 부를 수 있다고 생각한다. 
    
다만 현실에서 가상 이상적인 모습은 아마 각 서버들을 서로 독립된 VM 또는 물리적 서버로 분산하여 운영하는 것일지도 모른다. 만약 똑같은 단일 서버 내에 web server, WAS, DB 소프트웨어들을 같이 운영할 경우, 해당 서버에 문제가 생기거나 셋 중 하나에만 문제가 생기더라도 전체에 영향을 끼칠 수 있다. 하지만 서버를 분리시켜 운영하면, 특히 서로 다른 물리 서버 3개로 운영한다면, 설령 그 중 한 곳에서 문제가 발생한다 하더라도 다른 티어에 악영향을 끼치지 않아 정상 작동하기에 서버 장애에 조금이나마 덜 영향을 받을 것이다. 이는 같은 물리 서버 내에서 3개의 독립적인 VM으로 운영하더라도 마찬가지일 것이다. 다만 하나의 물리 서버, 3개의 VM 구조에서는 물리 서버 자체에 문제가 생기면 VM 모두에도 영향을 끼칠 것이다. 그래서 서버 장애로 인한 피해를 최소화하기에는 3개의 서로 다른 물리 서버로 운영하는 것이 베스트일 것이다. 
    
하지만 이는 현실적으로 금전적 비용 문제가 클 것이다. 실제로 각각의 물리적 컴퓨터 3개를 사서 운영하든 AWS와 같은 클라우드 서비스를 이용하여 3개의 서버를 띄우든 상관없이 금전적 비용이 더 크게 발생할 것이다. 그래서 서버 장애로 인한 잠재적인 피해를 감수하더라도 소규모 서비스, 사이드 프로젝트, 배포 연습 등의 목적이라면 하나의 물리 서버 위에서 3개의 VM으로 운영하는 것도 좋을 것이다. 그보다 더 가볍게 운영하고자 한다면 아예 이 글에서 소개할 실습처럼 하나의 물리 서버 위에서 3개의 Docker container로 운영하는 것도 좋을 것이다. 
</div>
</details>

# 본격 실습

프로젝트 폴더 구조는 대략 다음과 같다.

```
/project
  /frontend
    /src
    package.json
    Dockerfile
    nginx.conf
    ...
  /backend
    /src
    build.gradle
    Dockerfile
    ...
  /secrets  # docker secrets
    db-root-password.txt
    db-name.txt
    spring-datasource-password.txt
    spring-datasource-url.txt
    spring-datasource-user.txt
  .env
  .gitignore
  compose.yaml
```

코드 1-1. 이번 실습에 이용할 프로젝트 폴더 구조

하나의 프로젝트 폴더 안에 각각 `frontend`와 `backend` 폴더가 있고, 그 안에 각각의 소스 코드 및 Dockerfile들이 위치해있다. 그리고 프로젝트 최상단에는 `/secrets` , `compose.yaml` 등 Docker Compose로 배포하기 위한 설정 파일들이 위치해 있다. 

## Docker image 빌드를 위한 설정들

### `.dockerignore` 와 build context

Dockerfile을 이용하여 이미지로 빌드할 때 Docker 명령어로는 `Docker build -t image_name:tag <dockerfile이 든 이미지로 빌드할 디렉터리 경로>` 와 같은 형식으로 입력하여 이미지를 빌드할 것이다. 이 때 해당 디렉터리 전체가 Docker daemon에 전송되고, 그 곳에서 이미지가 빌드된다. 이 때, Docker daemon으로 보내지는 디렉터리 자체를 build context라고 부른다. “이미지 빌드에 필요한 맥락”이라고 해석하면 되겠다. 

![그림 1-1. 호스트에 있는 프로젝트 디렉터리를 기반으로 Dockerfile을 통해 이미지를 빌드할 때의 과정. 호스트에 있는 Dockerfile이 포함된 디렉터리가 Docker daemon에 전송되는 과정에서 `.dockerignore` 에 명시한 파일 및 하위 디렉터리들은 제외되어 전송된다. 그 후, Docker daemon 내의 build context에 있는 파일 및 하위 디렉터리들은 Dockerfile에 명시된 `COPY` , `ADD` 등의 명령어를 통해 또 한 번 필터링되어 필요한 파일 및 하위 디렉터리들만 이미지 빌드에 사용된다. 위 그림에서는 도커 이미지 빌드 과정에서 특정 파일 및 하위 디렉터리들이 필터링되어 이미지에서 제외되는 것을 묘사하고자 필터 그림을 추가하여 시각화하였다. 위 그림을 보면 Docker image에 들어가는 파일 및 하위 디렉터리들은 총 두 단계의 필터링을 거친다고 보면 되겠다. ](/images/2026-08-20/docker-web-app-deployment-example/1.png)

그림 1-1. 호스트에 있는 프로젝트 디렉터리를 기반으로 Dockerfile을 통해 이미지를 빌드할 때의 과정. 호스트에 있는 Dockerfile이 포함된 디렉터리가 Docker daemon에 전송되는 과정에서 `.dockerignore` 에 명시한 파일 및 하위 디렉터리들은 제외되어 전송된다. 그 후, Docker daemon 내의 build context에 있는 파일 및 하위 디렉터리들은 Dockerfile에 명시된 `COPY` , `ADD` 등의 명령어를 통해 또 한 번 필터링되어 필요한 파일 및 하위 디렉터리들만 이미지 빌드에 사용된다. 위 그림에서는 도커 이미지 빌드 과정에서 특정 파일 및 하위 디렉터리들이 필터링되어 이미지에서 제외되는 것을 묘사하고자 필터 그림을 추가하여 시각화하였다. 위 그림을 보면 Docker image에 들어가는 파일 및 하위 디렉터리들은 총 두 단계의 필터링을 거친다고 보면 되겠다. 

이렇게 호스트에 있는 디렉터리를 Docker daemon의 build context로 전달할 때 몇 가지 주의 사항이 있다. 

첫 번째는 Docker image로 빌드할 때 불필요한 파일들까지 build context에 넘어갈 수 있다는 것이다. 디렉터리 내부에 있는 모든 파일들이 Docker image로 빌드할 때 필수 요소들인건 아니다. 오히려 빌드된 이미지의 용량만 차지하여 이미지가 무거워질 뿐이다. 

두 번째는 보안상 민감한 정보까지 도커 이미지에 박제될 수 있다는 것이다. 디렉터리 전체를 파일 하나도 빠짐없이 build context에 보낼 경우 `.env` , `application-secrets.yml` 등에 있는 민감 정보들도 build context에 보내져서 빌드된 이미지의 레이어에 박제된다. 이는 `.gitignore` 와는 상관이 없다. 애초에 git과 docker는 서로 관련 없는 기술이기에 민감 정보를 git에서 제외했다고 해서 Docker의 build context에서까지 자동으로 제외되는 것은 아니기에 안심할 수는 없다는 것이다. 

이 두 문제를 해결하기 위해, 불필요하거나 민감한 정보가 들어있는 파일들은 build context에 넘기지 않도록 제외시켜야한다. 이를 수행할 수 있는 것이 바로 `.dockerignore` 라는 파일을 이용하는 것이다. 해당 파일을 만들고 그 안에 제외시킬 디렉터리 및 파일명들을 기입하면 된다. 사용법과 문법 자체는 거의 `.gitignore` 와 동일해서 사용하기 어렵지는 않을 것이다. 이 `.dockerignore` 파일은 `Dockerfile` 과 동일한 곳(프로젝트 폴더 루트)에 위치시키면 되겠다. 

프론트엔드의 경우, 다음과 같이 제외시킬 수 있겠다.

```
build/
dist/
README.md
.gitignore
.git
Dockerfile
.dockerignore
.env
**/node_modules/

jsconfig.json
tsconfig.*.json
tsconfig.json
```

코드 2-1. 프론트엔드(리액트)에서의 `.dockerignore` 예시

백엔드, 즉 스프링부트의 경우 다음과 같이 제외시킬 수 있겠다.

```
.env
application-*.yml
README.md
HELP.md
build/
.git
.gitignore
.gitattributes
.idea/
Dockerfile
.dockerignore

# 만약 같은 프로젝트 폴더 안에 compose.yaml이 있을 경우 이도 제외시키는 것이 좋음.
```

코드 2-2. 백엔드(스프링부트)에서의 `.dockerignore` 예시

참고로 Docker image를 빌드할 때에는 `.dockerignore` 및 `Dockerfile` 이란 파일 자체도 불필요하기에 제외시켰다. 이 파일들을 제외시키지 않으면 파일 및 그 내용 자체가 똑같이 이미지 레이어에 박제되는데, 이럴 필요까진 없기 때문이다. 

위 예시에서는 이미지 빌드 시 프론트엔드와 백엔드 각각의 artifact들도 같이 빌드를 한다고 가정하고 작성한 것이다. 만약 로컬에서 미리 빌드시키고(리액트에서는 `/build` 또는 `/dist` 폴더로 HTML, CSS, JS 등의 정적 파일로 빌드, 스프링부트에서는 `.jar` 파일로 빌드) 이를 이미지에 포함시키는 경우에는 빌드 산출물을 `.dockerignore` 에 넣지 않도록 한다. 

이렇게만 하더라도 불필요한 파일들까지 이미지로 넘어가서 이미지가 불필요하게 비대해지는 것을 방지할 수 있고, 보안적으로 민감한 정보들이 이미지 레이어로 노출되는 것도 막을 수 있다. 

한 편, 이러한 `.dockerignore` 를 좀 더 쉽게 작성해주는 템플릿을 제공하는 사이트도 있다. “[**.dockerignore**](https://dockerignore.com/)”라는 사이트에 방문하면 Spring Boot, React 등의 프레임워크별로 `.dockerignore` 템플릿을 제공해주니 사용해보면 좋다. 

### Multi-stage build를 이용하여 이미지 빌드하기

프론트엔드 및 백엔드에서 각자 제작한 웹 앱을 Docker image로 빌드할 때, 크게 두 가지 방법이 있다. 하나는 로컬 개발 환경에서 미리 빌드 산출물을 제작한 다음, 이를 Docker image의 build context로 넘겨서 빌드하는 방법이 있고, 또 하나는 아예 앱 빌드도 Docker image 빌드 과정 속에 포함시켜 실행시키는 방법이 있다. 이 방법들 모두 각자 장단점이 있을 것이다. 

전자의 경우, 미리 빌드한 앱 산출물을 이미지 내에 넣기만 하면 되기에 Dockerfile에 작성할 스크립트의 라인 수가 상대적으로 줄어들 것이다. 빌드된 Docker image의 레이어 개수도 별로 없어 이미지 용량을 최소화할 수 있다는 장점이 있다. 한 편, 이를 서버에 배포하기 위해선 빌드 산출물들을 `.gitignore` 및 `.dockerignore`에 추가하지 않고 같이 전달해야할 것이다. 만약 Github repository에 이 빌드 산출물들이 git 업로드에 제외되어 있으면 서버에서 `git clone` 을 통해 소스 코드를 받고 artifact를 별도로 빌드해야한다. 이 때 해당 서버에는 빌드 시 필요한 도구인 JDK, Nodejs, npm 등이 설치되어 있지 않다면 이들을 설치해야해서 꽤 번거로울 것이다. 또한, 빌드 환경 자체도 각 개발자마다 서로 다를 수 있어 “내 컴퓨터에서는 빌드가 되는데 다른 컴퓨터에서는 빌드가 안된다”와 같은 문제가 발생할 수도 있다. 

후자의 경우, 빌드 환경과 실행 환경이 모두 하나의 Dockerfile 안에 들어가므로 상대적으로 Dockerfile의 내용이 길어지고 복잡해질 수 있다. 하지만 하나의 Dockerfile만으로 앱 빌드 환경과 실행 환경을 코드로 동시에 제어할 수 있어 어떤 컴퓨터에서든 동일한 앱 빌드 및 실행 환경을 동시에 제공할 수 있다는 것이 큰 장점일 것이다. 

후자의 방식을 이용하려면 Docker의 Multi-stage build 방식을 이용하면 된다. 다음은 백엔드에서의 Dockerfile 코드다.

```docker
# === Build stage ===
FROM eclipse-temurin:21-jdk as builder

# image 내 working directory를 '/app'으로 설정. 
WORKDIR /app

# 프로젝트 내 현재 디렉토리를 image의 workdir인 `.` 현재 디렉토리에 복사. 
COPY . .

# gradle을 이용하여 앱 빌드
RUN chmod +x ./gradlew && ./gradlew clean build -x test

# === Runtime stage ===
# 런타임 환경에서는 빌드가 필요없기에 런타임 환경인 jre만 사용.
FROM eclipse-temurin:21-jre

WORKDIR /app

# 이전 스테이지인 builder 스테이지에서 빌드된 결과물을 복사.
COPY --from=builder /app/build/libs/*.jar app.jar

EXPOSE 8080

# 빌드된 앱 실행
ENTRYPOINT ["java", "-jar", "app.jar"]
```

코드 3-1. `backend` 폴더 내 `Dockerfile`

위 코드에서는 multi-stage build 방식을 사용하였는데, 둘 이상의 `FROM` 키워드가 나오는 것이 특징이다. 첫 번째 `FROM` 에서는 특정 jdk base image를 통해 앱을 빌드하는 단계를 정의하고 있다. 해당 스테이지를 다음 스테이지에서 편하게 지정할 수 있도록 `as` 키워드를 이용하여 `builder` 라고 이름을 지었다. 그러면 다음 스테이지에서 `--from=builder` 와 같이 이전 스테이지를 쉽게 지칭할 수 있다. 

두 번째 `FROM` 부터가 다음 스테이지이며, 실행 환경을 정의하는 스테이지이다. 여기서는 앞선 빌드 스테이지에서 빌드된 `jar` 파일을 특정 위치에 복사하고 특정 명령어를 통해 실행이 될 수 있게끔 정의되어 있다. 

이러한 multi-stage build의 장점은 이전 스테이지의 이미지 레이어 및 빌드 도구들이 최종 이미지에는 남지 않고, 마지막 스테이지의 것만 최종 이미지에 반영된다는 점이다. 실행 환경에서는 불필요한 각종 빌드 도구들도 사실상 삭제하는 효과를 가져 이미지의 가벼움을 유지할 수 있다는 장점이 있다. 

실제로 `docker image history <image_name>` 명령어를 이용해보면 최종 스테이지의 레이어만 남아있는 것을 볼 수 있다. 다음은 앞서 위에서 보았던 Dockerfile을 빌드하고 나온 이미지의 레이어가 출력된 결과이다.

```bash
$ docker image history image-album-study-backend
IMAGE          CREATED        CREATED BY                                      SIZE      COMMENT
66745d6e24a1   25 hours ago   ENTRYPOINT ["java" "-jar" "app.jar"]            0B        buildkit.dockerfile.v0
<missing>      25 hours ago   EXPOSE [8080/tcp]                               0B        buildkit.dockerfile.v0
<missing>      25 hours ago   COPY /app/build/libs/*.jar app.jar # buildkit   65.8MB    buildkit.dockerfile.v0
<missing>      6 days ago     WORKDIR /app                                    8.19kB    buildkit.dockerfile.v0
<missing>      2 weeks ago    ENTRYPOINT ["/__cacert_entrypoint.sh"]          0B        buildkit.dockerfile.v0
<missing>      2 weeks ago    COPY --chmod=755 entrypoint.sh /__cacert_ent…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago    RUN /bin/sh -c set -eux;     echo "Verifying…   12.3kB    buildkit.dockerfile.v0
<missing>      2 weeks ago    RUN /bin/sh -c set -eux;     ARCH="$(dpkg --…   166MB     buildkit.dockerfile.v0
<missing>      2 weeks ago    ENV JAVA_VERSION=jdk-21.0.11+10                 0B        buildkit.dockerfile.v0
<missing>      2 weeks ago    RUN /bin/sh -c set -eux;     apt-get update;…   82.9MB    buildkit.dockerfile.v0
<missing>      2 weeks ago    ENV LANG=en_US.UTF-8 LANGUAGE=en_US:en LC_AL…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago    ENV PATH=/opt/java/openjdk/bin:/usr/local/sb…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago    ENV JAVA_HOME=/opt/java/openjdk                 0B        buildkit.dockerfile.v0
<missing>      3 weeks ago    umoci raw add-layer --image /home/buildd/roc…   12.3kB    Add rock control metadata
<missing>      3 weeks ago    umoci config --image /home/buildd/rockcraft-…   0B        Set annotations
<missing>      3 weeks ago    umoci config --image /home/buildd/rockcraft-…   0B        Set labels
<missing>      3 weeks ago    umoci config --image /home/buildd/rockcraft-…   0B        Set default PATH for bare-based rock
<missing>      3 weeks ago    umoci config --image /home/buildd/rockcraft-…   0B        Set default commands
<missing>      3 weeks ago    umoci config --image /home/buildd/rockcraft-…   0B        Set entrypoint
<missing>      3 weeks ago    umoci raw add-layer --image /home/buildd/roc…   115MB
```

코드 3-2. 

첫 번쨰 줄부터 위에서 네 번째 줄까지가 필자가 Dockerfile을 통해 정의한 이미지 레이어들이다. 그 외 아래에 있는 나머지 레이어들은 `FROM` 키워드로 명시된 base image 자체 레이어들이다. 첫 위의 4개의 라인을 보면 알곘지만, 빌드 스테이지에서의 이미지 레이어들은 존재하지 않고 오로지 최종 스테이지의 레이어들만 존재하는 것을 볼 수 있다. 

<details id="ref-2">
<summary>
    <strong>
        <i>참고 - 여러 명령어들을 한 줄로 작성하여 이미지 레이어 개수 최소화하기</i>
    </strong>
    <a href="#ref-2" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
위 Dockerfile에서 `RUN chmod +x ./gradlew && ./gradlew clean build -x test` 부분을 보면 두 명령어를 한 줄로 작성했음을 볼 수 있다. 둘 이상의 명령어들이 모두 Docker image의 파일 시스템에 변화를 줘 이미지 레이어를 생성한다면, 이 명령어들을 `&&` 을 이용하여 하나의 라인으로 작성하는 것이 좋다. 이렇게 할 경우, 여러 줄에 걸쳐 각 명령어들을 작성하는 방식에 비해 image layer가 덜 생성되어 최종 이미지의 용량을 상대적으로 줄일 수 있기 때문이다. 
</div>
</details>

프론트엔드에서 필자는 다음과 같은 Dockerfile을 작성하였다. 역시 multi-stage build 방식을 취하였다.

```docker
# === Build stage ===
FROM node:22 AS builder
WORKDIR /app

# 소스 코드는 자주 변경되고, 의존성 파일은 잘 안 바뀌므로 
# build cache를 위해 이들을 분리하여 작업 진행. 
# 원래는 package-lock.json까지 복사하여 `npm ci` 명령을 대신 실행하면 
# 더 깔끔하고 재현 가능한 설치를 구현할 수 있다. 
# 다만 여기서는 해당 파일을 `.gitignore`에 추가하였기에 
# 처음 소스 코드를 받은 상태라면 로컬에서 먼저 `npm i`를 진행하여 `package-lock.json`
# 파일을 먼저 만들고 진행해야 하는데, 실수로 이 과정을 까먹을 수도 있어
# 여기서는 `npm install` 로 대신함.
COPY package.json .
RUN npm install
COPY . .
RUN npm run build

# === Runtime stage ===
FROM nginx:alpine

# 앞서 build stage에서 build되어 나온 html, js 등의 정적 파일들을 
# nginx 웹 서버 내 특정 경로로 주입하여 웹 서버가 정적 파일 서빙이 가능하도록 한다. 
COPY --from=builder /app/build /usr/share/nginx/html
```

코드 3-3.

`package.json` 을 이용하여 `npm install` 을 통해 `node_module` 등의 의존성들을 먼저 설치하도록 하였다. 그리고 그 의존성들을 바탕으로 `npm run build` 를 통해 본격적으로 앱을 빌드하도록 하였다. 그 후 이 빌드 산출물을 다음 스테이지로 넘겨 nginx 서버 내부로 넘겨 서비스하도록 설정하였다. 

<details id="ref-3">
<summary>
    <strong>
        <i>참고 - 로컬에서 앱을 빌드할 때의 Dockerfile</i>
    </strong>
    <a href="#ref-3" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
앞서 언급했듯, 앱을 도커 이미지로 빌드하는 방법에는 multi-stage build 방법 뿐만 아니라, 로컬 개발 환경에서 미리 앱을 빌드하고 그 산출물을 도커 이미지에 복사하는 방법도 있다고 하였다. 이 경우의 Dockerfile 스크립트는 보통 다음과 같이 작성된다. 
    
스프링부트에서의 Dockerfile은 다음과 같다.
    
```docker
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY build/libs/*.jar app.jar
ENTRYPOINT ["java", "-jar", "app.jar"]
```
    
코드 3-4. Spring boot Dockerfile 예시.
    
필자가 별도로 조사해본 결과, 스프링부트와는 달리 리액트에서는 로컬 개발 환경에서 미리 앱을 빌드하지 않고 Dockerfile에 앱 빌드 과정까지 넣는 것이 더 흔했다. 그럼에도 만약 로컬 개발 환경에서 미리 정적 파일들을 빌드하여 nginx 서버에 복사한다고 가정한다면 다음과 같이 작성할 것이다.
    
```docker
FROM nginx:alpine
COPY build/ /usr/share/nginx/html
```
    
코드 3-5. React Dockerfile 예시.
    
앱 빌드 도구가 무엇이냐에 따라 빌드 결과물이 `build/` 에 담길 수도, `dist/` 에 담길 수도 있으니 빌드 도구에 따라 해당 경로를 다르게 하면 되겠다. 
    
한 편, 앞선 코드 3-3에서 보았듯, `npm install` 보다 조금 더 깔끔하고 재현 가능한 설치를 한다면 다음과 같이 작성할 수 있다.
    
```docker
# === build stage ===
FROM node:22 AS builder
WORKDIR /app
    
# package-lock.json도 필요.
COPY package*.json .
RUN npm ci
COPY . .
RUN npm run build
    
# === runtime stage ===
FROM nginx:alpine
    
# 앞서 build stage에서 build되어 나온 html, js 등의 정적 파일들을 
# nginx 웹 서버 내 특정 경로로 주입하여 웹 서버가 정적 파일 서빙이 가능하도록 한다. 
COPY --from=builder /app/build /usr/share/nginx/html
```
    
코드 3-6. `npm ci` 사용 시의 React Dockerfile 예시.
    
앞선 코드 3-3과 다른 점은 `COPY package*.json .` 과 같이 작성하여 `package-lock.json`도 포함시켜야한다는 것이고, 그 다음에 `RUN npm ci` 명령어로 대체한다는 것이다. 이 과정에서는 `package-lock.json` 도 필요하기에 해당 파일이 `.dockerignore` 에 포함되지 않도록 주의한다. 
</div>
</details>

## NGINX 서버 설정

이제 Dockerfile은 모두 작성하였다. Docker Compose 스크립트 작성 전에 nginx 설정을 하는 것이 좋다. 필자의 경우, `frontend` 폴더 루트에 다음과 같이 `nginx.conf` 파일을 추가하였다.

```nginx
server {
  listen 80; # nginx port 설정

  location / {
    # `/`으로 접속 시 응답할 정적 파일들이 있는 root 디렉토리 설정
    root /usr/share/nginx/html;

    # 요청된 uri 경로가 root에 존재하면 해당 경로에 있는 정적 파일을 제공. 
    # 그렇지 않다면 `/index.html`을 대신 응답한다. 
    # React로 구현한 웹 페이지는 SPA(Single Page Application)이므로 단 하나의 index.html만 활용하고, 
    # URI 경로는 react router를 이용하여 하나의 페이지 안에서 다른 화면을 보여주는 구조. 
    # 예를 들어 `/product` 라는 URI로 요청이 올 경우, 해당 경로로 응답할 정적 파일이 없으므로
    # 404 에러가 발생하는데, 이를 방지하기 위해 현재 사용자가 보고 있는 index.html을 재전송한다. 
    try_files $uri $uri/ /index.html;
  }

  location /api/ {
    # docker compose를 이용할 것이므로 백엔드 서비스명이 될 "backend"를 호스트로 입력함. 
    # `/api/`로 시작되는 URI로 요청이 올 경우, 백엔드, 즉 API 서버로 요청을 전달한다. 
    # 리액트에서 `axios.get("/api/...")` 코드가 실행될 때 이 설정이 적용되어 API 서버로 요청이 전달됨. 
    proxy_pass http://backend:8080;
  }
}
```

코드 4-1. `/frontend/nginx.conf` 

위 파일에서는 nginx 서버의 포트 번호, React를 이용한 SPA(Single Page Application)일 경우의 설정, 프론트에서 백엔드 서버로 API 요청 시 해당 요청을 백엔드 서버로 전달하는 설정값들이 포함되어 있다. 

필자의 경우, 해당 파일은 Dockerfile에 포함시키기보다는 Docker Compose 실행 시 nginx 컨테이너와 마운트하는 방식을 취하였다. nginx 관련 설정을 여러 번 변경할 것 같아 이를 Dockerfile에 포함 시 항상 이미지의 일부분을 다시 빌드해야 하므로 시간이 걸리기 때문이었다. 그래서 `.dockerignore` 에 `nginx.conf` 을 추가하기도 했으며, Docker compose 스크립트에 해당 파일을 `frontend` 이미지의 컨테이너에 마운트하는 코드를 추가하기도 했다. 

위 코드에서 주석으로 설명하기도 했지만, 한 가지 주의할 점은 백엔드 서버로 프록시 설정할 때, 도메인의 호스트 부분을 `compose.yaml` 스크립트에서 작성할 백엔드 서비스명으로 그대로 기입해야한다는 것이다. 

## Docker compose 설정

`compose.yaml` 은 다음과 같이 작성하였다.

```yaml
services:
  frontend:
    build: ./frontend
    ports:
      - ${FRONTEND_PORT}:80
    networks:
      - frontend-net
    restart: unless-stopped
    volumes:
      # ro: read-only
      - ./frontend/nginx.conf:/etc/nginx/conf.d/default.conf:ro
    depends_on:
      - backend
      - db-server
  
  backend:
    build: ./backend
    ports: 
      - ${BACKEND_PORT}:8080
    networks:
      - backend-net
      - frontend-net
    volumes:
      # Dockerfile에서의 디렉터리 구조를 참고. 
      - image-vol:/app/files
    restart: unless-stopped
    depends_on:
      db-server:
        condition: service_healthy
    secrets:
      - db-url
      - db-user
      - db-password
  
  db-server:
    image: mariadb:latest
    volumes:
      - db-vol:/var/lib/mysql
    networks:
      - backend-net
    ports:
      - ${DB_PORT}:3306
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD_FILE: /run/secrets/db-root-password
      MARIADB_USER_FILE: /run/secrets/db-user
      MARIADB_PASSWORD_FILE: /run/secrets/db-password
      MARIADB_DATABASE_FILE: /run/secrets/db-name
    secrets:
      - db-root-password
      - db-user
      - db-password
      - db-name
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 10s
      timeout: 10s
      retries: 5
      start_period: 10s

secrets:
  db-url:
    file: ./secrets/spring-datasource-url.txt
  db-user:
    file: ./secrets/spring-datasource-user.txt
  db-password:
    file: ./secrets/spring-datasource-password.txt
  db-root-password:
    file: ./secrets/db-root-password.txt
  db-name:
    file: ./secrets/db-name.txt

volumes:
  db-vol:
  image-vol:

networks:
  backend-net:
  frontend-net:

```

코드 5-1. `compose.yaml`

프로젝트 폴더에 있는 Dockerfile을 Docker Compose를 이용하여 빌드하고자 할 경우에는 위와 같이 `build` 속성을 이용한다. 해당 속성값에는 Dockerfile이 위치한 경로를 지정한다. 그러면 Docker Compose 실행 시 먼저 해당 위치를 찾아 존재하는 Dockerfile로부터 이미지를 먼저 빌드한다. 

Docker Compose 스크립트 작성 시 기본적으로는 여러 서비스들이 하나의 네트워크를 공유하게 된다. 그런데 3 tier 아키텍처를 가지는 웹 앱의 경우, 웹 서버가 DB와 직접적으로 통신해서는 안될 것이다. 따라서 프론트엔드와 백엔드, 백엔드와 DB 서버끼리만 통신할 수 있도록 일부러 위와 같이 별도의 네트워크들을 정의하고 각 서비스들을 연결하여 각자 독립적인 망을 구성하였다. 

실습에 사용할 웹 앱에서는 DB 데이터 뿐만 아니라 사용자가 입력한 이미지 파일들도 저장해야하기에 백엔드에서 별도의 볼륨을 만들고 마운트하여 이미지 파일들만 볼륨에 저장하도록 설정하였다. 

위 파일에서는 같은 위치에 `.env` 파일을 위치시켜 해당 파일에 있는 환경변수들을 가져와 사용한다. 또한 Docker secrets을 사용하기에, Docker Compose를 띄우기 전에 `/secrets` 폴더를 만들어 각각에 해당하는 정보들을 txt 파일로 작성하였다. 

위 코드에서 사용된 Docker secrets, DB healthcheck 등에 대한 자세한 사항은 이전 글인 “[**[Docker] Docker compose를 이용하여 여러 컨테이너들을 손쉽게 실행해보자!**](/infra%20&%20cloud/docker-compose/)”에 자세히 기술하였으니 참고. 

위 파일에서는 Docker secrets 중 스프링부트의 `application.properties` 에 들어갈 속성값 중 하나인 `spring.datasource.url` 을 작성하였는데, 이 때 주의할 점은, DB 연결을 위한 URL의 호스트 부분에는 `compose.yaml` 에 작성한 DB 서비스명을 그대로 입력해야한다는 것이다. 위 코드에서는 `db-server` 라는 서비스명을 정의하였는데, 그러면 `/secrets` 폴더의 `spring-datasource-url.txt` 파일에는 다음과 같이 작성해야한다.

```yaml
jdbc:mariadb://db-server:3306/study
```

코드 5-2. `/secrets/spring-datasource-url.txt` 

즉, `jdbc:mariadb://<compose-service-name>:<port>/<db-name>` 과 같은 형식으로 작성해야한다는 것이다. 

### Docker secrets을 `application.properties` 에 주입하기.

이전 글인 “[**[Docker] Docker compose를 이용하여 여러 컨테이너들을 손쉽게 실행해보자! - 환경 변수(environment variables) 및 민감 정보(secrets) 별도로 관리하기**](/infra%20&%20cloud/docker-compose/#%ED%99%98%EA%B2%BD-%EB%B3%80%EC%88%98environment-variables-%EB%B0%8F-%EB%AF%BC%EA%B0%90-%EC%A0%95%EB%B3%B4secrets-%EB%B3%84%EB%8F%84%EB%A1%9C-%EA%B4%80%EB%A6%AC%ED%95%98%EA%B8%B0)“ 에서는 Docker secrets를 사용하여 민감 정보를 `compose.yaml` 및 Docker 상에서 노출시키지 않도록 하는 방법에 대해 다뤘었다. 이 때는 MariaDB 등의 공식 이미지에서 제공하는 `_FILE` 접미사를 이용하여 Docker secrets을 주입할 수 있었음을 보았었다. 그런데 스프링부트에서는 이러한 접미사를 제공하지 않는다. 따라서 스프링부트의 `application.properties` 에 Docker secrets을 주입하기 위해선 조금 다른 방법을 사용해야한다. 

`application.properties` 에 다음과 같이 작성한다. 필자의 경우, 도커 배포뿐만 아니라 로컬 개발 환경에서도 실습 웹 앱을 띄워서 테스트하기도 해서 `application-docker.properties` 에 대신 작성하였다. 

```
spring.config.import=optional:configtree:/run/secrets/
spring.datasource.url=${db-url}
spring.datasource.username=${db-user}
spring.datasource.password=${db-password}

# 실제 운영 시에는 이 값은 부적절하겠으나, 여기서는 실습 상의 편의를 위해 다음과 같이 설정함.
spring.jpa.hibernate.ddl-auto=update
```

코드 6-1. `application-docker.properties`

`spring.config.import=optional:configtree:/run/secrets/` 속성을 통해 Docker secrets 값이 든 텍스트 파일을 `/run/secrets/` 경로로 마운트함으로써 스프링부트에서도 해당 값을 사용할 수 있게 된다. 그 아래 줄부터는 `compose.yaml` 에 작성한 `secrets` 속성값에 정의한 시크릿 이름들을 `${...}` 방식으로 작성하여 주입시킬 수 있다. 

`compose.yaml` 에서는 스프링부트에 해당하는 `backend` 서비스 항목에도 `secrets` 속성값을 정의하여 어떤 시크릿을 사용해야할지 명시하는 것은 변하지 않음에 주의한다. 

## Docker Compose 실행하기

이제 Docker Compose를 이용하여 웹 앱을 띄워보기만 하면 된다. `compose.yaml` 가 있는 실습 프로젝트 폴더로 이동한 후, 다음의 명령어를 이용하면 된다. 

```bash
docker compose up -d
```

코드 7-1. 

처음에는 아직 관련 도커 이미지가 존재하지 않는 상태이므로 처음부터 프론트엔드 및 백엔드 도커 이미지가 빌드될 것이다. 빌드 시간은 컴퓨터 사양에 따라 달라진다. 

만약 이미지에 포함될 소스 코드 일부를 변경한 경우, `docker compose up -d --build` 명령어를 통해 이미지를 재빌드하여 실행한다. 

이미지 빌드가 끝나면 MariaDB, backend app server, nginx server 컨테이너들이 초기화 및 실행될 것이다. 웹 브라우저에서 `http://localhost:80` 과 같이 입력하면 해당 웹 페이지에 들어가볼 수 있다. 

---

References

[1] IBM - “3계층 아키텍처란 무엇인가요?”

[3티어 아키텍처란 무엇인가요? \| IBM](https://www.ibm.com/kr-ko/think/topics/three-tier-architecture)

[2] IBM docs - “Three-tier architectures”

[Three-tier architectures](https://www.ibm.com/docs/en/was-nd/9.0.5?topic=overview-three-tier-architectures)

[3] Docker docs - Docker compose Quickstart - `.dockerignore` 및 build context 관련 설명 포함되어 있음.

[Docker Compose Quickstart](https://docs.docker.com/compose/gettingstarted/)

[4] [도커 기초 강좌 #6 .dockerignore와 빌드 컨텍스트 — 캐시 잘 쓰기 \| 스쿨오브웹](https://schoolofweb.net/ko/posts/docker-basics-6/)

[5] 언어, 도구, 프레임워크별 `.dockerignore` 템플릿 사이트. 

[.dockerignore Templates for Frameworks, Languages & Tools](https://dockerignore.com/)

[6] Spring boot dockerfile 예시 참고용

[[Docker] Dockerfile을 이용한 Spring Boot App 환경 구성 및 실행방법](https://adjh54.tistory.com/420)

[7] Docker docs - Multi-stage builds

[Multi-stage builds](https://docs.docker.com/get-started/docker-concepts/building-images/multi-stage-builds/)

[8] Docker docs - Multi-stage builds

[Multi-stage builds](https://docs.docker.com/build/building/multi-stage/)

[9] [프론트엔드 개발자를 위한 Docker로 React 개발 및 배포하기](https://velog.io/@oneook/Docker%EB%A1%9C-React-%EA%B0%9C%EB%B0%9C-%EB%B0%8F-%EB%B0%B0%ED%8F%AC%ED%95%98%EA%B8%B0)

[10] Docker docs - React.js language-specific guide

[React.js language-specific guide](https://docs.docker.com/guides/reactjs/)

[11] nginx react 배포 시 reverse-proxy 설정법

[[Nginx] React 배포시 Reverse-Proxy 설정 방법](https://citronbanana.tistory.com/105)

[12] nginx react 배포 시 reverse-proxy 설정법

[[Nginx] Nginx를 이용한 React 배포 및 Reverse Proxy 서버 구축하기 (EC2 + Nginx + express)](https://blog.naver.com/dlaxodud2388/223161852880)

[13] nginx react 배포 시 reverse-proxy 설정법

[Linux : Nginx Reverse Proxy 설정 방법, 예제, 명령어](https://jjeongil.tistory.com/1490)

[14] nginx react 배포 시 reverse-proxy 설정법

[nginx) react(spa) reverse-proxy를 이용해 cors에러 해결하기](https://tomhoon.tistory.com/564)

[15] [[Spring Boot] Context Path 설정](https://frogand.tistory.com/147)

[16] dockerhub - nginx

[nginx - Official Image \| Docker Hub](https://hub.docker.com/_/nginx)

[17] Spring boot dockerfile 예시 참고. 

[[번역] Spring Topical Guides - Spring Boot Docker](https://octoping.tistory.com/58)

[18] Docker multi stage build 관련 설명. 

[도커 중급 강좌 #1 멀티스테이지 빌드와 이미지 슬리밍 \| 스쿨오브웹](https://schoolofweb.net/ko/posts/docker-intermediate-1/)

[19] how to inject docker secrets into spring application.properties 

[How to handle Docker-Secrets in application.properties files](https://stackoverflow.com/questions/70007676/how-to-handle-docker-secrets-in-application-properties-files)

[20] Spring docs - application.properties 안에 docker secrets 사용하는 방법

[Externalized Configuration :: Spring Boot](https://docs.spring.io/spring-boot/reference/features/external-config.html#features.external-config.files.configtree)

[21] nginx `proxy_set_header Host` 에 관한 설정 설명. 

[Module ngx_http_proxy_module](https://nginx.org/en/docs/http/ngx_http_proxy_module.html?&_ga=2.154176760.1941564344.1786971474-756007900.1786971474#proxy_set_header)

[22] [[Nginx] Reverse Proxy 설정](https://engineer-diarybook.tistory.com/entry/Nginx-Reverse-Proxy-%EC%84%A4%EC%A0%95-1)

[23] Spring boot dockerfile example

[[Docker] Spring Boot 프로젝트를 Docker 이미지로 만들기](https://rebugs.tistory.com/741)

---

[^1]: 그리고 이 글에서는 React 및 Spring Boot로 제작한 웹 앱을 Docker image로 만들기 위한 Dockerfile 스크립트 작성까지 보이니 추후 웹 앱 배포 시에도 도움이 될 것이다.