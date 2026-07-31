---
title: "[Docker] Docker image 만드는 법"
category: "Infra & Cloud"
tag: ["Docker", "Image", "Copy-on-Write", "CoW", "Dockerfile", "commit", "Layer", "Layered architecture", "Image layer", "Image build", "Writable layer", "Cache", "Build cache"]
---

지금까지는 기존에 존재하는 docker image를 내려받고 이를 컨테이너로 생성 및 실행하는 과정을 바탕으로 한 개념들에 대해 다뤘었다. 

그런데 한 편, 때로는 개발자가 직접 docker image를 만들어야 할 때가 있을 것이다. 대표적인 예시가 바로 웹 앱을 만들었을 때이다. 웹 앱마다 필요로 하는 의존성, 환경 파일 등이 모두 다를 것이기 때문에 각 웹 앱에 맞는 이미지를 생성하는 것이 좋겠다. 물론 웹 앱 뿐만 아니라 어떤 프로그램을 만들고 이를 다른 기기에서도 손 쉽게 작동할 수 있도록 하기 위해서도 이를 이미지화할 수 있다. 

이번 글에서는 이러한 docker image를 만드는 방법에 대해 살펴보도록 하겠다. 원래는 이미지의 구조나 원리 등의 이론을 먼저 정리하고 그 다음에 실제로 사용하는 법을 정리하는 순서를 가졌지만, 이번 글에서는 거꾸로 이미지 만드는 방법 및 실습을 먼저 소개한 뒤에 관련 이론에 대해 정리하도록 하겠다. 이론 부분에서 이미지를 만드는 방법 중 하나인 Dockerfile을 이용한 설명들이 있기에 이를 먼저 알아두는 게 좋겠다는 판단에서다. 

# 이미지를 만드는 방법: `docker commit` 과 Dockerfile을 이용하는 방법

Docker에서 이미지를 만드는 방법에는 두 가지가 있다. 하나는 `docker commit` 명령어를 이용하여 현재 실행 중인 컨테이너를 이미지화하는 방법. 또 하나는 Dockerfile이라는 이름의 파일을 이용하여 이미지를 만드는 스크립트를 작성하고, 이를 빌드시켜 이미지화하는 방법이다. 이 글에서는 편의상 전자를 `docker commit` 방식이라 부르고, 후자는 Dockerfile 방식이라고 임의로 부르도록 하겠다. 

`docker commit` 방식은 컨테이너를 이미지화하는 방법인데, 왜 굳이 컨테이너를 이미지화해야할지 궁금할 것이다. 컨테이너 자체가 이미 기존에 존재하는 어떤 이미지로부터 생성된 것인데, 왜 굳이 다시 이미지화하는 걸까? 사실 이미지와 비교했을 때 컨테이너 내 변경사항이 없다면 이미지화할 필요가 없을 것이다. 다만 컨테이너 내에 변경사항이 생겼고, 이를 그대로 이미지화할 때 사용된다고 보면 되겠다. 예를 들어 새로운 파일을 만들었다든가 하는 상황이 그 예시일 것이다. 이 방식은 주로 디버깅 또는 실험에 사용된다고 한다. 컨테이너를 실행하는데 예기치 못한 에러 및 버그가 발생했을 때 내부 로그를 남기거나, 이를 해결한 최종 결과물을 다른 팀원과 공유하고자 할 때, 또는 기존 이미지로부터 생성한 컨테이너에 대해 이런 저런 실험을 하다가 마음에 드는 결과물이 나왔을 때 이를 이미지화하는 경우도 있다고 한다. 

하지만 이 방식의 문제점은, 외부에서 보았을 때 해당 이미지를 어떻게 만들었는지 재현을 할 수가 없다는 것이다. 즉, 이미지를 제작하는 과정이 보이지 않고 오로지 그 결과물만 보인다는 것이다. 이러한 정보의 부족으로 인해 `docker commit` 방식으로 생성된 이미지 사용 시 예상치 못한 에러, 버그가 또 발생했을 때의 디버깅이 어려울 수 있고, 컨테이너에서 이미지화하는 것을 반복할 때 변경사항을 추적하기가 어렵다는 문제점이 있다. 컨테이너로부터 이미지화되었을 때 외부에서 해당 이미지의 구조가 어떻게 되어있을지 한 눈에 파악하기 힘들어 커스텀하기에도 어려울 것이다. 

이러한 이유 때문인지, 실무에서는 거의 대부분 Dockerfile 방식을 대신 사용한다고 한다. Dockerfile 방식에서는 내가 어떻게 이미지로 만들 것인지 그 스크립트를 절차적으로 작성하기 때문에 이미지가 어떻게 구성되어있는지 알 수 있고 재현이 가능하며, 변경 이력을 추적하기 쉽다는 장점이 있다. 또한 `docker commit` 방식은 기존에 어떤 이미지가 존재하고 그 이미지로부터 생성된 컨테이너로부터 이미지화하는 방식이기에, 아예 처음부터 이미지를 만들기에는 부적절한데, Dockerfile을 이용하면 내 프로그램에 맞는 이미지를 만들 수 있다. 또한, `docker commit` 방식은 어쨌든 컨테이너를 작동시키는 상태에서 이미지화를 해야하는데에 반해, Dockerfile은 그런 과정 없이 그저 스크립트만 짜고 나중에 한꺼번에 이미지를 빌드하는 방식이기에 상대적으로 번거로운 과정이 없다는 것도 장점이겠다. 

IaC(Infrastructure as code), 즉 코드형 인프라라는 개념은 말 그대로 인프라를 코드로 관리한다는 것인데,  Dockerfile도 이에 해당한다고 볼 수 있다. 오로지 코드만으로 이미지를 빌드, 생성, 관리할 수 있기 때문이다. 보통 이러한 Dockerfile은 Github repo에서도 심심치 않게 볼 수 있는데, 이를 통해 다른 사람의 소스 코드를 내 컴퓨터에서 손쉽게 구동시켜볼 수 있다. 

## `docker commit`을 이용한 이미지 만들기 실습

이 챕터에서는 `docker commit` 명령어를 이용하여 기존에 실행하고 있는 컨테이너에 변경사항이 생겼다고 가정하고 이를 이미지화하는 방법에 대해 살펴볼 것이다. 

여기서는 Apache 웹 서버인 `httpd` 를 이용할 것이다. 해당 웹 서버에 필자가 만든 `index.html` 을 주입하면 이 컨테이너 내부의 파일 시스템에 변화가 생긴 셈이다. 이를 이미지화해보고, 이 이미지가 정말로 필자가 만든 `index.html` 을 반영하고 있는지 확인하기 위해 이를 컨테이너로 실행해보는 과정을 거쳐볼 것이다. 

이 글에서도 저번 글과 마찬가지로 Windows 11 운영체제에서 WSL 2, Ubuntu, Docker Desktop을 이용한다. 

먼저, Windows에서 미리 웹 서버에 이식할 `index.html` 파일을 준비한다. 필자의 경우, `C:\docker\study` 폴더에 다음과 같은 `index.html` 파일을 준비하였다. 사실 `docker cp` 명령어를 이용하여 파일을 컨테이너에 전달할 것이기에 파일 위치는 그리 중요하진 않다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>First Page In Docker</title>
</head>
<body>
  <div>
    <h1>Docker에서의 첫 웹페이지</h1>
    <p>반갑습니다!</p>
    <div>
      <p>Docker 개념들</p>
      <ul>
        <li>Image</li>
        <li>Container</li>
        <li>Registry</li>
        <li>Docker daemon</li>
      </ul>
    </div>
  </div>
</body>
</html>
```

코드 1-1. `index.html` 

그 후, 리눅스 쉘에서 다음과 같이 `httpd` 웹 서버 컨테이너를 작동시킨다.

```bash
docker run --name my-web-server -d -p 8080:80 httpd
```

코드 1-2. 

웹 브라우저에서 `http://localhost:8080` 으로 접속해본다. 아직까지는 필자가 만든 `index.html` 파일을 컨테이너에 주입하지 않았기에 `It works!` 와 같은 기본 웹 페이지가 보일 것이다. 

이제 다음의 명령어를 통해 앞서 만든 HTML 파일을 컨테이너에 복사한다.

```bash
docker cp /mnt/c/docker/study/index.html my-web-server:/usr/local/apache2/htdocs/
```

코드 1-3. 

위 명령어 실행 후 앞선 웹 브라우저에서 새로고침하면 다음과 같은 화면으로 바뀔 것이다. 

<div class="single-image">
  <img width="50%" src="/images/2026-07-31/docker-how-to-build-image/1.png" alt="image">
  <p>사진 1-1. </p>
</div>

위와 같은 화면이 나온다면 성공적으로 HTML 파일을 컨테이너에 전달한 것이다. 

이제 이 상태에서의 컨테이너를 이미지화할 것이다. 리눅스 쉘에서 다음의 명령어를 입력한다.

```bash
docker commit my-web-server my-ws-im:1.0.0
```

코드 1-4. 

앞서 생성하고 실행 중인 `my-web-server` 컨테이너로부터 `my-ws-im:1.0.0` 이라는 이미지를 생성하는 명령어이다. 이미지 이름과 버전은 원하는 대로 작성해도 되며, `docker commit` 명령어의 형식을 정리하면 다음과 같다.

```bash
docker commit <컨테이너명> <이미지이름>[:태그]
```

코드 1-5.

필자의 경우, `1.0.0` 이라는 버전을 태그로 부여해보았다. 태그 부여 시 이미지 이름 뒤에 콜론(`:`) 기호 뒤에 작성하면 된다. 한 편 이와 같이 태그, 즉 버전을 부여하지 않을 수도 있는데, 이 경우 자동으로 `latest` 가 붙는다. 즉, 가장 최신의 이미지라는 뜻으로 붙여진다. 

해당 이미지가 잘 생성되었는지 다음을 통해 확인할 수 있다.

```bash
$ docker images
                                                                                                    i Info →   U  In Use
IMAGE            ID             DISK USAGE   CONTENT SIZE   EXTRA
alpine:latest    28bd5fe8b56d         13MB         3.93MB
httpd:latest     305fd8326a27        177MB         47.6MB    U
my-ws-im:1.0.0   d0cb6da9000c        175MB         45.2MB
```

코드 1-6. 

도커 이미지 목록에 앞서 만들었던 `my-ws-im:1.0.0` 이미지가 잘 생성되었음을 확인할 수 있다. 이제 이 이미지로부터 컨테이너를 생성해보겠다. 

```bash
docker run --name my-ws-ct -d -p 8085:80 my-ws-im:1.0.0
```

코드 1-7.

웹 브라우저에서 `http://localhost:8085` 로 접속해보면 다음과 같이 `httpd` 기본 화면이 아닌 앞서 만든 커스텀 `index.html` 화면이 보이는 것을 확인할 수 있다.

<div class="single-image">
  <img width="60%" src="/images/2026-07-31/docker-how-to-build-image/2.png" alt="image">
  <p markdown="1">사진 1-2. 앞서 `docker commit` 명령어를 통해 만든 `my-ws-im:1.0.0` 이미지로부터 컨테이너를 생성하여 웹 브라우저에서 접속한 모습. 순수 `httpd` 이미지로 실행한 컨테이너는 `8080` 포트 번호로 접속했었는데, `my-ws-im:1.0.0` 이미지로 생성한 컨테이너는 `8085` 로 매핑하였다. 위 사진은 해당 포트 번호로 접속했을 때의 화면이다. </p>
</div>

이로써 `docker commit` 명령어를 이용하여 컨테이너로부터 새 이미지를 생성하고, 이를 컨테이너로 실행해보는 과정을 성공적으로 마쳤다. 

## Dockerfile을 이용한 이미지 만들기 실습

바로 이전 챕터에서 실습해보았던 이미지 생성을 이번에는 Dockerfile을 이용하여 진행해볼 것이다. 

Dockerfile은 해당 파일 안에 스크립트를 작성하기 때문에 편의성을 위해 VScode 에디터에서 작성하였다. 이 때 Dockerfile의 작성 문법을 알려주는 도구 중 하나로 Microsoft 사의 “Docker”라는 VSCode 확장 프로그램을 설치해두면 좋다. 해당 도구는 문법을 알려줄 뿐만 아니라 VSCode에서도 쉽게 도커를 운용할 수 있는 확장 프로그램이라고 한다. 여기서는 Dockerfile 문법 도움 및 확인을 위해 활용하였다. 

먼저, `index.html` 파일을 만들어 둘 폴더 위치를 선정해둔다. 필자의 경우 `C:\docker\study` 폴더를 마련하였다. 그 후, `index.html` 파일을 다음과 같이 작성하였다.

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>First Page In Docker</title>
</head>
<body>
  <div>
    <h1>Docker에서의 첫 웹페이지</h1>
    <p>반갑습니다!</p>
    <p>Dockerfile을 이용하였습니다. </p>
  </div>
</body>
</html>
```

코드 2-1. `index.html` 

그 후, 같은 폴더에서 `Dockerfile` 이란 이름의 파일을 하나 생성한다. 해당 파일은 다른 파일들과 달리 별도의 확장자를 붙이지 않는 것에 주의한다. 해당 파일에 다음과 같은 스크립트를 작성하였다.

```docker
FROM httpd
COPY index.html /usr/local/apache2/htdocs
```

코드 2-2. `Dockerfile` 

이 실습에서는 기존 `httpd` 이미지로부터 확장하여 새 이미지를 생성하는 것이므로 `FROM httpd` 를 입력하여 base image를 `httpd` 로 하도록 선언하였다. 그 후, `COPY` 명령어를 통해 `index.html` 파일을 `/usr/local/apache2/htdocs` 폴더에 복사하도록 하였다. 

이제 리눅스 쉘에서 이 Dockerfile을 토대로 이미지를 빌드해볼 것이다. 리눅스 쉘에서 다음의 명령어를 입력한다.

```bash
docker build -t my-ws-df-im:1.0.0 /mnt/c/docker/study
```

코드 2-3. 

위 명령어를 실행하면 다음과 같은 텍스트들이 출력된다.

```bash
[+] Building 0.7s (7/7) FINISHED                                                                         docker:default
 => [internal] load build definition from Dockerfile                                                               0.0s
 => => transferring dockerfile: 90B                                                                                0.0s
 => [internal] load metadata for docker.io/library/httpd:latest                                                    0.1s
 => [internal] load .dockerignore                                                                                  0.0s
 => => transferring context: 2B                                                                                    0.0s
 => [internal] load build context                                                                                  0.0s
 => => transferring context: 399B                                                                                  0.0s
 => [1/2] FROM docker.io/library/httpd:latest@sha256:305fd8326a27a137fedb8900f26375699ab233cc37793f35cf5d7c65671f  0.1s
 => => resolve docker.io/library/httpd:latest@sha256:305fd8326a27a137fedb8900f26375699ab233cc37793f35cf5d7c65671f  0.0s
 => [2/2] COPY index.html /usr/local/apache2/htdocs                                                                0.0s
 => exporting to image                                                                                             0.3s
 => => exporting layers                                                                                            0.1s
 => => exporting manifest sha256:21ee8f54620fdfcf8b05b5d3e98686067c57e3aa205f18481bdeafb9df2beb74                  0.0s
 => => exporting config sha256:39b5b842617830889f765f1c91dc74d0018d48dcfbeffe5c38bf9bb98863bd8d                    0.0s
 => => exporting attestation manifest sha256:d88297bf00d81f6fa2555619cee5f24e5c38f7fc562d888b9e449dc8c9231da1      0.0s
 => => exporting manifest list sha256:0575b28651824029727303749f7e38bca4d318a001e4b90136392b25d4fedc31             0.0s
 => => naming to docker.io/library/my-ws-df-im:1.0.0                                                               0.0s
 => => unpacking to docker.io/library/my-ws-df-im:1.0.0                                                            0.1s
```

코드 2-4. 코드 2-3 명령어 실행 결과.

Dockerfile로부터 이미지를 빌드하는 명령어 구조를 정리하면 다음과 같다.

```bash
docker build -t <이미지 이름>:[태그] <Dockerfile이 들어있는 이미지로 만들 디렉터리 경로>
```

코드 2-5. 

여기서 `-t <이미지 이름>[:태그]` 의 `-t` 옵션은 빌드될 이미지에 사람이 읽기 쉬운 이미지 이름과 태그를 부여하기 위한 옵션이다. 해당 옵션 바로 뒤에 원하는 이미지 이름과 태그를 입력하면 된다. 사실 이 옵션을 사용하지 않는다고 해서 이미지 빌드가 안되는 건 아니지만 이 경우 무작위 해시값인 `IMAGE ID` 로만 해당 이미지를 지칭할 수 있어 가독성, 편의성을 위해선 사실상 이 옵션을 사용하는 것이 좋겠다. 

`docker images` 명령어를 통해 해당 이미지가 생성되었는지 확인한다.

```bash
$ docker images
                                                                                                    i Info →   U  In Use
IMAGE               ID             DISK USAGE   CONTENT SIZE   EXTRA
alpine:latest       28bd5fe8b56d         13MB         3.93MB
httpd:latest        305fd8326a27        177MB         47.6MB
my-ws-df-im:1.0.0   0575b2865182        175MB         45.2MB
my-ws-im:1.0.0      d0cb6da9000c        175MB         45.2MB
```

코드 2-6. 

앞서 빌드 시도한 이미지가 목록에 들어온 것을 확인할 수 있다. 이제 이 이미지로 컨테이너를 생성, 실행하여 잘 작동하는지 확인해보겠다. 

```bash
docker run --name my-ws-df-ct -d -p 8090:80 my-ws-df-im:1.0.0
```

코드 2-7. 

`http://localhost:8090` 으로 웹 브라우저에서 보면 다음과 같은 화면이 뜬다. 

<div class="single-image">
  <img width="60%" src="/images/2026-07-31/docker-how-to-build-image/3.png" alt="image">
  <p>사진 2-1.</p>
</div>

이로서 Dockerfile을 이용하여 이미지를 빌드, 생성하고 컨테이너로 실행까지 해보는 실습을 거쳐보았다. 

실제로 앱을 구성하는 소스 코드, 환경 설정 파일, 의존성 파일 등을 하나의 프로젝트 폴더로 관리하며 Github repo에도 업로드할텐데, 이 repo에 들어 있는 앱을 구성할 각종 파일들로부터 바로 이미지화하기 위해 해당 프로젝트 폴더에 Dockerfile을 같이 두는 방식을 취한다. 

한 편, Dockerfile에서 주로 사용하는 명령어로는 다음과 같이 존재한다.

| 명령어 | 설명 |
| --- | --- |
| FROM | 토대가 될 base image 지정 |
| COPY | 이미지에 파일 또는 디렉터리를 복사 |
| ADD | 이미지에 파일 또는 디렉터리를 추가 |
| RUN | 빌드 명령어를 실행. 이미지 빌드 시 실행할 명령어를 지정.  |
| CMD | 컨테이너 실행 시 실행할 명령어를 지정.  |
| ENTRYPOINT | 컨테이너 실행 시 실행할 명령어 강제 지정. 실행될 컨테이너를 실행 가능한(executable) 프로그램으로 실행.  |
| EXPOSE | 통신에 사용할 포트 번호 지정.  |
| WORKDIR | 명령어를 실행할 작업 디렉터리 지정.  |

이외에도 여러 명령어들이 있으며, 자세한 사항은 “[Docker docs - Dockerfile reference](https://docs.docker.com/reference/dockerfile/)” 페이지를 참고. 

## Docker image를 로컬에서 백업 및 복원하는 방법

보통 docker image는 편의성, 유지보수성을 위해 Docker Hub와 같은 Docker registry에 업로드하고, 필요할 때 다운로드받아 사용하는 방식을 취한다. 다만, 예외적으로 이미지를 로컬에서 백업 또는 이동시켜야할 경우가 있을지도 모른다. 보안적인 요소로 인해 Docker registry를 사용할 수 없는 경우, 이미지 파일 자체가 너무 커서 registry에 업로드 또는 다운로드하기에는 네트워크에 부담이 될 경우 등이 있겠다. 

이 경우, 이미지를 tar 파일로 만들어 도커 엔진 관리 영역 밖인 호스트 사용자 공간으로 빼내고, 반대로 호스트 사용자 공간에 있는 이미지 파일을 도커 엔진 안으로 불러와 이미지로 복원시키는 방법을 이용하면 된다. 

먼저 도커 이미지를 tar 파일로 만드는 명령어는 다음과 같다.

```bash
docker save -o <file_name.tar> <image-name>
```

코드 3-1. 

그리고 tar 파일로 만든 이미지를 도커에서 불러와 다시 이미지화하는 명령어는 다음과 같다. 

```bash
docker load -i <file_name.tar>
```

코드 3-2. 

이전 챕터에서 만든 `my-ws-df-im:1.0.0` 이미지를 대상으로 실습해보겠다. 혹시 모르니 해당 이미지로부터 생성, 실행되고 있는 컨테이너들은 중단, 삭제해둔다. 필자의 경우, Windows 영역의 `C:\docker\images` 폴더에 `my-ws-df-im-1.0.0.tar` 파일로 추출하고자 한다. 그래서 다음과 같은 명령어를 입력하였다.

```bash
docker save -o /mnt/c/docker/images/my-ws-df-im-1.0.0.tar my-ws-df-im:1.0.0
```

코드 3-3. 

위 명령어 실행 후 Windows에서 파일 탐색기로 해당 경로를 보면 다음과 같이 tar 파일이 생성된 것을 확인할 수 있다. 

<div class="single-image">
  <img width="60%" src="/images/2026-07-31/docker-how-to-build-image/4.png" alt="image">
  <p>사진 3-1. </p>
</div>

명확한 실습을 위해 도커에 남아있는 `my-ws-df-im:1.0.0` 이미지는 `docker rmi my-ws-df-im:1.0.0` 명령어로 삭제한다. 

이번에는 해당 tar 파일을 도커로 불러와 이미지로 복원해보겠다. 다음의 명령어를 입력한다.

```bash
docker load -i /mnt/c/docker/images/my-ws-df-im-1.0.0.tar
```

코드 3-4. 

해당 명령어 입력 후의 출력 결과는 다음과 같다. 

```bash
$ docker load -i /mnt/c/docker/images/my-ws-df-im-1.0.0.tar
Loaded image: my-ws-df-im:1.0.0
$ docker images
                                                                                                    i Info →   U  In Use
IMAGE               ID             DISK USAGE   CONTENT SIZE   EXTRA
alpine:latest       28bd5fe8b56d         13MB         3.93MB
httpd:latest        305fd8326a27        177MB         47.6MB
my-ws-df-im:1.0.0   0575b2865182        175MB         45.2MB
my-ws-im:1.0.0      d0cb6da9000c        175MB         45.2MB
```

코드 3-5. 

해당 이미지가 도커 내부로 로드해온 것을 볼 수 있다. 해당 이미지로부터 `httpd` 컨테이너를 만들 때의 명령어와 비슷한 명령어로 컨테이너를 생성, 실행해보면 웹 브라우저에서 해당 웹 페이지를 볼 수 있다. 이로서 이미지를 tar 파일로 저장하고 불러오는 실습이 문제없이 진행되었다. 

# Docker image의 구조

Docker image는 여러 개의 layer로 구성되어 있는 layered architecture를 가진다. 이 각각의 레이어들은 파일의 추가, 수정, 삭제, 복사 등 파일 시스템에 변화를 줄 때마다 생성되어 이전 레이어 위에 stack처럼 계속 쌓이는 구조이다. 

```docker
FROM ubuntu:22.04
LABEL org.opencontainers.image.authors="org@example.com"
COPY . /app
RUN make /app
RUN rm -r $HOME/.cache
CMD python /app/app.py
```

코드 4-1. Dockerfile 예제. 출처: [https://docs.docker.com/engine/storage/drivers/#images-and-layers](https://docs.docker.com/engine/storage/drivers/#images-and-layers)

위 예시 코드는 파이썬으로 작성한 앱을 빌드하고 실행하는 Dockerfile 스크립트라 보면 되겠다. 여기서 이미지 레이어를 생성하는 코드는 각각 `FROM ~`, `COPY ~` , `RUN ~` 으로 총 4개의 레이어가 생성된다. 

- `FROM ~` 에서는 토대가 될 base image를 선정하는 단계이다. 여기서 토대가 되는 파일, 디렉토리 구조가 형성되기에 첫 번째 레이어를 생성한다.
- `COPY ~` 에서는 현재 디렉토리에 있는 파일들을 `/app` 디렉터리에 복사, 추가하므로 두 번째 레이어를 생성한다.
- `RUN make /app` 에서는 `make /app` 명령어에 의해 빌드된 앱이 파일 형태로 생성되기에 세 번째 레이어를 생성한다.
- `RUN rm -r $HOME/.cache` 에서는 캐시 디렉터리를 삭제하므로 네 번째 레이어를 생성한다.

그 외 나머지 줄에서는 파일 시스템의 변화를 주는 명령어들이 아니고, 오로지 이미지의 metadata에만 변화를 주기에 별도의 레이어를 생성하진 않는다. 

이러한 각각의 이미지 레이어들은 immutable, 즉 한 번 레이어가 생성되면 그 후에 변경할 수 없다. read-only, 즉 읽기 전용 레이어이다. 마찬가지로 이미지 자체도 immutable, read-only 속성을 지닌다. 그래서 이미지 또는 이를 구성하는 레이어에 변화를 주고자 한다면 아예 새로운 이미지로 생성하는 수밖엔 없다. 언뜻 보면 불편하겠지만, 만약 컨테이너에서 파일 시스템에 변화를 준다고 해서 그것이 이미지에까지 변화를 준다면 일관적인 이미지 관리와 변경 내역 추적이 사실상 불가능할 것이다. 

<div class="single-image">
  <img width="60%" src="/images/2026-07-31/docker-how-to-build-image/5.png" alt="image">
  <p markdown="1">참고 사진 1-1. image 및 container를 구성하는 레이어 구조도 예시. 출처: [https://docs.docker.com/engine/storage/drivers/#images-and-layers](https://docs.docker.com/engine/storage/drivers/#images-and-layers)</p>
</div>

참고로 특정 이미지의 자세한 레이어들을 보고 싶다면 각각 Docker Desktop과 CLI에서는 다음과 같은 과정을 거치면 된다. 먼저 Docker Desktop에서는 아래 사진처럼 좌측 사이드바에서 “images” 클릭 후 나오는 여러 이미지 목록 중에서 특정 이미지를 클릭하면 다음과 같이 레이어들을 볼 수 있다. 

<div class="single-image">
  <img width="70%" src="/images/2026-07-31/docker-how-to-build-image/6.png" alt="image">
  <p markdown="1">사진 4-1. Docker desktop에서 특정 이미지의 layer 목록 보기.</p>
</div>

CLI 환경에서는 `docker image history <이미지명>` 명령어를 통해 특정 이미지를 구성하는 모든 레이어들을 살펴볼 수 있다. 

```bash
$ docker image history my-ws-df-im:1.0.0
IMAGE          CREATED       CREATED BY                                      SIZE      COMMENT
0575b2865182   4 hours ago   COPY index.html /usr/local/apache2/htdocs # …   24.6kB    buildkit.dockerfile.v0
<missing>      2 weeks ago   CMD ["httpd-foreground"]                        0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   EXPOSE map[80/tcp:{}]                           0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   COPY httpd-foreground /usr/local/bin/ # buil…   20.5kB    buildkit.dockerfile.v0
<missing>      2 weeks ago   STOPSIGNAL SIGWINCH                             0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   RUN /bin/sh -c set -eux;   savedAptMark="$(a…   34.9MB    buildkit.dockerfile.v0
<missing>      2 weeks ago   ENV HTTPD_PATCHES=                              0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   ENV HTTPD_SHA256=68c74d4df38c26bed4dfbdb8f3b…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   ENV HTTPD_VERSION=2.4.68                        0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   RUN /bin/sh -c set -eux;  apt-get install --…   7.01MB    buildkit.dockerfile.v0
<missing>      2 weeks ago   WORKDIR /usr/local/apache2                      4.1kB     buildkit.dockerfile.v0
<missing>      2 weeks ago   RUN /bin/sh -c mkdir -p "$HTTPD_PREFIX"  && …   16.4kB    buildkit.dockerfile.v0
<missing>      2 weeks ago   ENV PATH=/usr/local/apache2/bin:/usr/local/s…   0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   ENV HTTPD_PREFIX=/usr/local/apache2             0B        buildkit.dockerfile.v0
<missing>      2 weeks ago   # debian.sh --arch 'amd64' out/ 'trixie' '@1…   87.4MB    debuerreotype 0.17
```

코드 4-2. 

파일 시스템에 변화를 주는 명령어에 대해서만 레이어가 생성되지만, Docker Desktop이나 CLI에서의 `docker image history` 명령어를 통해 보이는 레이어에는 파일 시스템에 변화를 주지 않는 명령어까지 포함되어 출력된다. 또한 Docker Desktop에서의 레이어 출력 순서와 CLI에서의 출력 순서가 정반대인 것에 유의해야겠다. 

이렇게 다른 개발자가 만든 이미지에서도 레이어가 어떻게 구성되어 있는지를 Dockerfile 문법으로 확인할 수 있어 이미지가 어떻게 구성되어 있고, 어떻게 재현할지를 알 수 있다는 것이 장점이다. 

## Image layer 방식으로 인해 파생되는 개념들

이렇게 image의 layer 방식으로 인해 생기는 여러 개념들에 대해서 살펴보도록 하겠다. 

### 재사용 가능한 base image와 image layer

이미지가 여러 레이어들의 stack 형식으로 구성되어 있기에 좋은 장점 중 하나는 바로 여러 이미지들에서 공통으로 사용하는 레이어들을 따로 모아 이미지화하고, 이를 base image로 삼아 다른 이미지들이 공유하여 사용할 수 있다는 점이다. 예를 들어, 여러 웹 앱들이 있고, 이들을 각각 이미지화한다고 하였을 때, 해당 앱들이 대부분 자바, 스프링부트의 특정 버전을 사용하고 있다면 이들을 별도로 모아 하나로 이미지화해둘 수 있다. 그리고 각 웹 앱의 Dockerfile에서는 이 이미지를 `FROM ...` 에 사용하여 base image로 삼을 수 있다. 이는 마치 소스 코드에서의 모듈 재활용성을 떠올리면 되겠다. 만약 어떤 코드 뭉치가 여러 군데에서 중복적으로 사용된다면, 이들을 따로 모아 하나의 모듈로 만들고, 이를 필요로 하는 모듈들에서 `import` 만 하면 된다. 

한 예로, 도커 엔진에 `ubuntu` 라는 이미지가 있고, 이를 토대로 `FROM ubuntu` 로 시작하는 Dockerfile을 만들어 나만의 커스텀 이미지(`my-custom-image` 라 하겠다)를 빌드, 생성해보았다고 해보자. 이 때, `my-custom-image` 이미지를 빌드할 때 별도로 Docker registry에서 ubuntu를 pull해올 필요가 전혀 없다. 이미 로컬 기기에 해당 이미지가 별도로 존재하기 때문이다. 따라서 도커 엔진에서는 해당 `ubuntu` 이미지 레이어들을 가져와 `my-custom-image` 이미지를 빌드하는 방식이다. 이는 마치 객체지향 프로그래밍에서의 클래스 상속과도 비슷하다고 볼 수 있다. 상속의 이점 중 하나는 무엇인가? 코드의 재사용성 확보다. 중복되는 코드를 또 작성하지 않아도 된다. 마찬가지로 이미지 빌드 시 이미 로컬 기기에 base image가 존재한다면 별도로 네트워크를 통해 pull해오지 않고도 로컬에서 바로 빌드할 수 있다. 실제로도 `docker image history` 명령어를 통해 이미지 레이어를 보면 기존 base image의 레이어가 그대로 기록되어 있으면서도 그 위에 새로운 이미지 레이어들이 쌓이는 구조임을 확인할 수 있다. 

한마디로 정리하자면, 이미지의 레이어 구조로 인해 이미지의 재사용성을 확보할 수 있다. 

### Build cache

```docker
FROM ubuntu:latest
RUN apt-get update && apt-get install -y build-essentials
COPY main.c Makefile /src/
WORKDIR /src/
RUN make build
```

코드 5-1. Dockerfile 예시. 출처: [https://docs.docker.com/build/cache/](https://docs.docker.com/build/cache/)

<div class="single-image">
  <img width="70%" src="/images/2026-07-31/docker-how-to-build-image/7.png" alt="image">
  <p markdown="1">참고 사진 2-1. docker image build cache에 대한 설명을 위한 그림. 위 코드 5-1을 레이어로 표현한 그림. 출처: [https://docs.docker.com/build/cache/](https://docs.docker.com/build/cache/)</p>
</div>

만약 위 예시와 같은 Dockerfile이 있을 때, `main.c` 소스 코드 내용이 자주 변경되면, 이는 같은 이미지를 생성할 때마다 일부 파일 시스템에 변경사항이 생긴다는 것이다. 그래서 해당 레이어부터 그 아래 끝까지 있는 모든 레이어들이 전부 처음부터 다시 빌드된다. 단, 파일 시스템 변경 사항이 없는 상대적으로 base image에 가까운 레이어들은 처음부터 재빌드되지 않고 cache에 의해 그대로 보존된다. 

즉, 이미지 레이어들 중 특정 레이어에서 변경 사항이 생긴다면, 이 이미지를 다시 빌드할 때 변경 사항이 포착된 레이어부터 말단 레이어(상대적으로 나중에 쌓인 레이어)까지 모두 다시 빌드된다. 다만 그 이전 레이어들, 즉 `FROM ...` 처럼 base image에 가까운 레이어들은 다시 빌드되지 않고 캐싱된다. 이를 통해 다음의 사실들을 알 수 있다.

- 특정 일부 파일 시스템에 변화가 생겨도 이미지의 모든 레이어들을 다시 빌드할 필요 없이 필요한 부분의 레이어들만 빌드하면 되기에, 이미지의 모든 레이어들이 다시 빌드되어야 하는 방식보다는 빌드 타임을 절약할 수 있다.
- 파일 시스템 일부 변화로 인한 이미지 레이어 재빌드에 관해, 최대한 많은 레이어들이 빌드 캐시되도록 하려면 Dockerfile 작성 시 파일 시스템에 변화가 많은 레이어들을 가장 아래 쪽에 작성하도록 하는 것이 좋다. 예를 들어, 위 예제 및 참고 사진에 따르면, `RUN apt-get ...` 스크립트를 만약 `COPY main.c` 보다 아래에 위치하도록 하였다면 `RUN apt-get` 에 해당하는 레이어는 캐싱되지 않고 이미지를 다시 빌드할 때마다 처음부터 다시 빌드될 것이다. 이러면 상대적으로 빌드 타임이 좀 더 오래 걸려 비효율적이다.

### Container의 writable layer

이미 한 번 생성된 이미지와 그 레이어들은 변경 불가능하고 읽기 전용으로만 접근할 수 있다. 그런데 만약 컨테이너에서 이미지 레이어에 존재하는 파일 시스템에 변화를 준다면 어떻게 될까? 아예 해당 파일에 변화를 줄 수 없는 걸까? 

이미지로부터 컨테이너 생성 시 이미지 레이어와는 별도로 컨테이너를 위한 쓰기 가능한 레이어(writable layer)가 별도로 생성된다. 이 레이어는 이미지 레이어들 중 가장 맨 끝에 쌓인다. 이에 대한 구조는 앞선 참고 사진 1-1을 참고하면 되겠다. 그리고 만약 컨테이너 실행 도중 이미지를 구성하는 특정 레이어에 해당하는 파일 시스템에 접근 및 변경해야하는 경우, 해당 이미지 레이어에 변경 작업을 할 수는 없다. 앞서 말했듯 이미지 레이어는 읽기 전용이기 때문이다. 따라서 해당 레이어에 있는 파일이 컨테이너의 writable layer에 복사된 뒤에 그 곳에서 파일 변경 사항이 일어난다. 이렇게 이미지 레이어의 파일 시스템에 변경 작업을 수행하기 위해 더 높은 곳에 위치한 컨테이너의 writable layer에 복사를 한 다음에 그 복사본에 파일 변경 작업을 수행하는 전략을 copy-on-write(CoW)라고 한다. 이러한 전략 덕분에, 기존에 존재하는 이미지 레이어에는 아무런 변형이 일어나지 않으면서도 파일 시스템에 변화를 줄 수 있는 것이다. 

컨테이너 내부에서 일어나는 데이터 및 파일의 저장, 삭제, 변경 등의 작업들은 모두 컨테이너의 writable layer에서만 일어난다. 그리고 해당 레이어는 컨테이너의 생명주기와 같아 컨테이너가 삭제되면 해당 레이어도 삭제된다. 이로 인해 컨테이너에 저장한 데이터들이 컨테이너 삭제 시 같이 삭제되는 이유이기도 하며, 별도의 데이터 보존을 위해서는 Volume 또는 bind mount를, 컨테이너에서 일어난 변경 작업을 저장하여 이미지화하고자 할 때에는 `docker commit` 명령어를 별도로 사용해야 하는 것이다. 다만 컨테이너를 삭제한 상태에서 이미지를 별도로 삭제하지 않으면 이미지 레이어들은 컨테이너와는 별개로 계속 보존된다. 

하나의 이미지로부터 여러 개의 컨테이너를 띄우면, 각 컨테이너마다 똑같은 이미지들이 복사되어 실행되는 것이 아니라, 하나의 동일한 이미지 및 그 이미지의 레이어들을 공유하여 사용한다. 어차피 이미지 레이어는 immutable, read-only이기 때문에 여러 컨테이너들이 동시에 접근해도 이미지 내용이 변경되지 않기에 가능한 구조인 것이다. 따라서 각 컨테이너마다 똑같은 이미지를 필요한 만큼 여러 개 복사하여 사용하는 방식에 비해 용량적으로도 훨씬 더 효율적인 구조이다. 

![참고 사진 3-1. 하나의 이미지 레이어를 여러 컨테이너들이 공유하는 구조. 각 컨테이너들에서 발생하는 파일 변경 사항은 각 컨테이너들이 독립적으로 가지고 있는 writable layer에 저장되기에 기존 이미지 레이어에 영향을 끼치지 않는다. 출처: [https://docs.docker.com/engine/storage/drivers/#container-and-layers](https://docs.docker.com/engine/storage/drivers/#container-and-layers)](/images/2026-07-31/docker-how-to-build-image/8.png)

참고 사진 3-1. 하나의 이미지 레이어를 여러 컨테이너들이 공유하는 구조. 각 컨테이너들에서 발생하는 파일 변경 사항은 각 컨테이너들이 독립적으로 가지고 있는 writable layer에 저장되기에 기존 이미지 레이어에 영향을 끼치지 않는다. 출처: [https://docs.docker.com/engine/storage/drivers/#container-and-layers](https://docs.docker.com/engine/storage/drivers/#container-and-layers)

컨테이너 생성 후 실행 시키고 있는 도중이라면, 관련 이미지가 더 이상 쓸모없다고 생각하고 삭제해선 안되는 이유가 바로 여기에 있다. 실행 중인 1개 이상의 컨테이너들이 관련 이미지의 레이어를 계속 참조하고 있기 때문이다. 

<details id="ref-1">
<summary>
    <strong>
        <i>참고 - Volume에 데이터를 저장하는 것보다 컨테이너에 직접 저장하는 것이 디스크 I/O 속도가 더 느린 이유.</i>
    </strong>
    <a href="#ref-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
이미 생성된 레이어는 변경 불가능하고 읽기 전용으로만 접근할 수 밖에 없는 이미지의 특성을 생각했을 때, 컨테이너마다 별도의 writable layer를 두고 CoW 전략을 통해 writable layer에서만 파일 변경 사항을 반영하는 전략은 꽤 스마트하고 효율적인 전략으로 보인다. 그러나 이 전략에 항상 장점만 있는 것은 아니다. 볼륨에 데이터를 저장하는 것에 비해 컨테이너에 파일 입출력 시 속도가 상대적으로 더딘 이유가 되기도 한다. 
    
Copy-on-write 전략의 실행 과정을 순차적으로 정리하자면 다음과 같다.
    
1. 변경될 파일이 container의 writable layer 또는 이미지 레이어에 이미 존재하는 파일인지를 확인하기 위해 레이어를 가장 위 레이어부터 가장 아래에 있는 base image layer까지 순차 탐색.
2. 해당 파일이 container의 writable layer에 없다면 발견한 이미지 레이어의 파일로부터 복사.
3. writable layer에서 파일 변경 사항 반영.
    
이라고 볼 수 있는데, 이렇게 상대적으로 긴 과정을 거치므로 파일 입출력 속도도 그만큼 느릴 수밖에 없다. 따라서 쓰기 작업이 빈번하게, 또는 대용량의 데이터에 대한 쓰기 작업이 필요한 때에는 비록 데이터의 영속성이 불필요하더라도 속도를 감안해서 볼륨에 마운트하여 그곳에 데이터 쓰기 작업을 하도록 하는 것이 더 좋겠다. 볼륨 마운트 시 데이터 입출력은 컨테이너의 writable layer가 아닌 볼륨에 직접 수행되어 CoW와 같은 긴 과정이 필요없기 때문이다. 데이터 입출력 속도를 위해 볼륨을 사용해야할 대표적인 예시는 데이터 쓰기 작업이 빈번하게 있는 DB를 사용할 때가 되겠다. 
</div>
</details>

---

References

[1] 지은이: 오가사와라 시게타카, 옮긴이: 심효섭, “그림과 실습으로 배우는 도커 & 쿠버네티스“, 위키북스

[2] [지은이: 이길섭, Docker - 일경험 프로그램, 위키독스, M6-1 Dockerfile](https://wikidocs.net/289898)

[3] [Docker docs - Home](https://docs.docker.com/)

[4] [[Docker] Docker Image 이해하기 (도커 이미지란, 이미지의 종류, Docker Hub, 도커 이미지의 특징)](https://sxxb-in.tistory.com/17)

[5] docker docs 공식 - base image 개념

[Base images](https://docs.docker.com/build/building/base-images/)

[6] Docker docs - image layers

[Understanding the image layers](https://docs.docker.com/get-started/docker-concepts/building-images/understanding-image-layers/)

[7] 참고 - Docker hub를 대체할 Github Container Registry

[[Docker] GitHub Container Registry에 도커 이미지 올려 사용하기](https://woo-chang.tistory.com/81)

[8] Docker docs - Dockerfile

[Dockerfile overview](https://docs.docker.com/build/concepts/dockerfile/)

[9] Docker docs - Docker build cache

[Docker build cache](https://docs.docker.com/build/cache/)

[10] Docker docs - image

[What is an image?](https://docs.docker.com/get-started/docker-concepts/the-basics/what-is-an-image/)

[11] Docker docs - Storage drivers - image layer에 대한 설명이 있음. 

[Storage drivers](https://docs.docker.com/engine/storage/drivers/)

[12] Docker docs - Dockerfile reference

[Dockerfile reference](https://docs.docker.com/reference/dockerfile/)

[13] Docker docs - Multi-stage builds

[Multi-stage builds](https://docs.docker.com/build/building/multi-stage/)

[14] [[Docker] Multi-stage Build](https://nolzaheo.tistory.com/106)

[15] [[Docker] Multi-stage build](https://jaeseo0519.tistory.com/223)

[16] [[Docker] Dockerfile - Multi-stage build(멀티스테이지 빌드)](https://kimjingo.tistory.com/63)

[17] Docker docs - docker container commit

[docker container commit](https://docs.docker.com/reference/cli/docker/container/commit/)

[18] [[Docker] 이미지 커밋와 이미지 빌드](https://server-technology.tistory.com/237)

[19] docker image를 tar 파일로 저장 및 로드하는 방법에 대한 글

[docker image를 tar 파일로 저장 (export / import / save / load)](https://www.leafcats.com/240)

[20] docker image를 tar 파일로 저장 및 로드하는 방법에 대한 글

[Docker image를 tar 파일로 저장 및 로드(save & load, export & import)](https://m.blog.naver.com/qbxlvnf11/222439207002)