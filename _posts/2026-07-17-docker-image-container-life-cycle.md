---
title: "[Docker] Docker image, container의 life cycle"
category: "Infra & Cloud"
tag: ["Docker", "Container", "도커", "Image", "Life cycle", "생명주기", "명령어", "WSL", "WSL 2", "WSL2", "Docker Desktop", "CLI", "GUI", "Linux"]
---

<div class="notice--info">
💡 이 글에서의 Docker 실습 부분에서는 Windows 11 환경에서 WSL 2, Ubuntu, Docker Desktop을 이용하였습니다. 리눅스 전용 컴퓨터 등의 다른 환경에서는 이 글의 실습 부분대로 잘 되지 않을 수도 있음을 참고바랍니다. 
</div>

이번 글에서는 특정 소프트웨어를 Docker의 container를 통해 실행해봄으로써, Docker image 및 container의 생명주기와 이를 다루는 각각의 명령어 및 실행 방법에 대해 살펴보도록 하겠다. 즉, Docker의 기초 부분이라 할 수 있는 image, container 실행, 중단, 삭제하는 방법에 대해 정리한다. 이 과정에서 먼저 Docker 명령어에 대한 사실과 더불어, Docker registry에서 image를 가져오는 방법에 대해서도 포괄적으로 다뤄보고자 한다. 

이 글에서 실습 부분은 WSL 2, Docker Desktop을 이용한다. Docker Desktop은 분명 GUI라서 이용하기 편하다는 장점이 있다. 하지만 여기서는 CLI 방식으로 명령어를 직접 입력하여 실행하는 방법 위주로 살펴보도록 할 것이다. 웹 배포 등 서버 용도로 사용하기 위해 컴퓨팅 자원을 조금이라도 아껴서 활용해야하는 입장에서는 GUI도 컴퓨터 성능에 부담이 될 수 있어 보통 CLI 형태로 많이 사용한다고 한다. 그래서 이러한 상황들을 대비하기 위해 GUI보단 CLI 위주로 살펴보고자 한다. 

# WSL 2 환경에서 Docker 실행해보기

사실 “[**[OS][Docker] WSL 2 - Windows에서의 Linux, Docker 설치 및 간단한 사용 방법**](/infra%20&%20cloud/wsl2-overview-and-how-to-use-with-docker/)”이라는 이전 글에서 WSL2, Docker Desktop 설치 및 사용 방법에 다루긴 했지만, 여기서 다시 한 번 간단하게 정리해보고자 한다. 설치 방법에 대해선 해당 글에 다뤘으니 여기서는 사용 방법에 대해서만 간단히 다뤄보겠다. 

1. 정석적인 방법
    1. 우선 Windows 바탕화면 아래의 작업 표시줄에 있는 검색창에 “powershell”이라 검색하면 다음과 같이 “Windows PowerShell”이 뜬다. “Run as Administrator”를 클릭하여 관리자로 실행한다. 
        
        <div class="single-image">
          <img width="70%" src="/images/2026-07-17/docker-image-container-life-cycle/1.png" alt="image">
          <p>사진 1-1.</p>
        </div>
        
    2. 그러면 다음과 같이 터미널 창이 뜰 것이다. `wsl` 이라고 치면 이미 설치된 리눅스 배포판이 실행되어 해당 터미널로 전환될 것이다. 필자의 경우 Ubuntu를 설치했기에 해당 배포판이 실행된다. 
        
        ![사진 1-2. ](/images/2026-07-17/docker-image-container-life-cycle/2.png)
        
        사진 1-2. 
        
    3. Docker Desktop을 실행시킨다. 해당 프로그램을 키기만 해도 Docker engine은 정상 작동할 것이다. 아래 사진처럼 좌측 하단에 “Engine running”이라 되어 있으면 docker engine이 실행 중이라는 뜻이다. 
        
        ![사진 1-3. ](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/16.png)
        
        사진 1-3. 
        
    4. 다시 터미널 창으로 돌아와서, `docker version` 명령어를 입력해보자. 만약 도커 엔진이 정상 작동한다면 Docker client 및 server 관련 정보들이 나올 것이고, 그렇지 않다면 docker 명령어 자체를 찾을 수 없다는 말이 나올 것이다. 아래 사진의 아랫 부분과 같이 docker client 및 server 정보가 정상적으로 출력된다면 docker를 사용할 준비를 마쳤다는 뜻이다. 한 편, 원활한 사용을 위해 현재 작업 디렉토리는 `cd ~` 명령어를 입력하여 사용자 홈 디렉토리에서 수행하는 것을 추천한다. 
        
        <div class="single-image">
          <img width="70%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/22.png" alt="image">
          <p>사진 1-4.</p>
        </div>
        
2. 조금 더 쉽게 실행하는 방법
    1. 사실 WSL2와 Ubuntu까지 설치 완료한다면 다음과 같이 Windows 작업표시줄의 검색창에 “Ubuntu”라고만 쳐도 해당 앱이 뜬다. 이를 실행시키면 WSL2에서 Ubuntu를 바로 실행시킬 수 있다. 이 상태에서 아까와 똑같이 Docker Desktop을 키면 바로 Docker를 사용할 준비를 마치게 된다. 쉬운 사용을 위해 아래 사진에서 “작업 표시줄에 고정”을 누르면 작업 표시줄에 해당 아이콘이 등록될 것이다. 해당 아이콘을 클릭하기만 해도 바로 WSL 2 기반의 리눅스 배포판 CLI 터미널 창이 바로 뜰 것이다. 
        
        ![사진 1-5.](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/9.png)
        
        사진 1-5.
        

# Docker의 기본적인 명령어와 그 구조

CLI 환경에서 Docker의 명령어 구조는 다음과 같다. 

```bash
docker 상위_커맨드 하위_커맨드 [옵션] 대상 [옵션 인자]
```

코드 1-1. docker 커맨드 구조

```bash
docker container run -dit some-container --mode 1
        상위     하위 옵션     대상         인자
```

코드 1-2. docker 명령어 예시

- 상위 커맨드: “무엇을”에 해당하는 커맨드. 주로 image, container 등 Docker의 요소들 중 하나를 입력한다.
- 하위 커맨드: 행동에 해당하는 커맨드. `run`, `ls` , `start` 등 상위 커맨드로 지정한 docker 요소에 대해 어떤 행동을 할지를 결정한다. `docker container run` 이라면 container라는 것을 실행하라는 뜻이다.
- 옵션: 생략 가능하다. 필요한 추가 기능이 있을 때 옵션을 사용하는 것이라 보면 된다. 위 코드 1-2에서는 `-d` , `-i` , `-t` 각각의 옵션들을 편의상 합친 것이다.
- 대상: 구체적으로 어떤 것에 대해 명령을 수행할 지 결정. 예를 들어 위 코드 1-2처럼 `container` 에 대해 수행할 경우, 구체적으로 어떤 container에 대해 수행할지 그 container 이름을 지정하는 것이다. 위 예에서는 `some-container` 라는 특정 컨테이너를 지정한 예시이다. `httpd` , `MySQL` 등 여러 container들이 있으니 그 중 하나를 택해 대상으로 지정하는 것이다.
- 인자: 바로 앞의 “대상”에 대한 추가적으로 필요한 인자가 있을 때 지정하는 것. 생략 가능하다. 예를 들어 MySQL과 같은 DBMS의 경우 username, password 등을 인자로 지정할 수 있다.
    - `--mode=1` 또는 `--name mycontainer` 와 같은 형식으로 `--옵션=값` 또는 `--옵션 값` 처럼 옵션에 각 인자값들을 지정하여 사용한다.

한 편, Docker의 image 및 container에 대해 자주 사용되는 명령어들로는 다음과 같다.

- `docker image 하위_커맨드 옵션`   

| 하위 커맨드 | 설명 | 축약형 |
| --- | --- | --- |
| pull | docker registry에서 이미지를 다운로드한다.  |  |
| rm | docker image 삭제 | `docker rmi ...` |
| ls | 현재 기기에서 보유한 docker image들을 목록으로 출력 | `docker images` |
| build | docker image 생성(빌드) |  |

- `docker container 하위_커맨드 옵션`   

| 하위 커맨드 | 설명 | 축약형 |
| --- | --- | --- |
| start | container 실행 |  |
| stop | container 실행 중단, 정지 | `docker stop ...` |
| create | docker image로부터 container 생성 |  |
| run | docker container를 실행한다. 만약 그에 해당하는 image가 없다면 자동으로 해당 이미지를 registry로부터 다운로드 받는다. `docker image pull` 과 같은 결과. 아직 image로부터 container가 생성되지 않은 경우 자동 생성된다. `docker container create` 와 같은 효과. 즉, 컨테이너로 만들 이미지 자체가 없는 경우, `docker image pull`, `docker container create`, `docker container start` 이 세 명령어가 순차적으로 실행되는 것과 같다. | `docker run ...` |
| rm | 정지된 container를 삭제한다. | `docker rm ...` |
| exec | 실행 중인 container 내에서 프로그램 실행 |  |
| ls | 현재 보유한 container들의 목록을 출력. | `docker ps` |
| cp | container, host 간 파일 복사 명령어. |  |
| commit | container를 image로 변환.  |  |
| restart | 중단된 상태의 container를 재실행.  |  |

docker 구성 요소들인 image, volume 등과 달리 container의 경우 특이하게 `docker container stop` 과 같은 명령어에서 `container` 를 제외하고 `docker stop` 과 같이 축약할 수도 있다. image나 그 외 요소들에는 적용되지 않고 container에서만 적용되는 예외이니 주의. 

한 편 자주 쓰이는 옵션에는 다음과 같이 존재한다. 

| 옵션 | 설명 | 비고 |
| --- | --- | --- |
| `--name 컨테이너명` | 컨테이너 이름 지정 |  |
| `-p 호스트_포트번호:컨테이너_포트번호` | 호스트, 컨테이너 간 통신을 위한 포트 번호 지정. |  |
| `-v 호스트_디스크:컨테이너_디렉토리` | 볼륨을 컨테이너에 마운트 |  |
| `--net=네트워크명` | 컨테이너를 네트워크에 연결 |  |
| `-e 환경변수명=값` | 환경변수 설정 |  |
| `-d` | 컨테이너를 백그라운드로 실행 | 컨테이너의 프로그램을 백그라운드에 실행시키면서 동시에 다른 docker 작업을 하고자 할 때 사용. 이를 사용하지 않으면 컨테이너의 프로그램이 실행 종료될 때까지 터미널로 아무런 작업을 할 수 없음. 짧은 시간 내에 한 번만 실행하고 마는 컨테이너의 경우에는 해당 옵션이 불필요함.  |
| `-i`  | 컨테이너에 터미널을 연결. 직접 해당 컨테이너에 접속하여 작업을 하고자 할 때 사용.  | `interactive` 의 약자. |
| `-t` | 특수 키 사용 가능하게 함. 이 옵션을 `-i` 와 함께 사용해야 명령 입출력 결과가 shell을 통해 출력될 수 있다.  |  |
| `-help`  | 사용 방법을 보고자 할 때.  |  |

# 컨테이너의 생명주기

처음 이미지를 다운로드받고 그 이미지로부터 컨테이너를 실행하는 모든 과정에서의 컨테이너의 생명주기는 다음과 같이 흐른다. 

![그림 2-1. docker container의 생명주기와 그와 관련된 명령어들.](/images/2026-07-17/docker-image-container-life-cycle/3.png)

그림 2-1. docker container의 생명주기와 그와 관련된 명령어들.

1. 특정 소프트웨어를 docker에서 실행하고자 할 때 관련 image가 로컬 기기에 없는 경우, DockerHub와 같은 docker registry로부터 해당 image를 다운로드 받는다.
    1. `docker image pull` 명령어가 사용된다. 
2. 다운로드받은 image로부터 container를 생성한다. 
    1. `docker container create` 명령어가 사용된다.
3. 생성된 container를 실행시킨다.
    1. `docker container start` 명령어가 사용된다.
    2. 위 3가지 명령어를 `docker container run` 하나로 합쳐 사용할 수도 있다. 
    3. 서버에서 웹 배포와 같은 행위를 할 때, 배포를 중단한다든가 그 외 예외 상황들이 벌어지지 않는 한 실행되고 있는 container는 계속 실행중인 채로 놔둔다. 
4. 여기서부터는 실행 중인 container에 대해 더 이상 필요가 없어졌다든지 하는 여러 이유로 container를 폐기하고자 할 때 거치는 과정들이다. 실행 중인 container를 중단한다. 
    1. `docker container stop` 명령어를 사용한다. 
    2. container가 실행 중인 상황에서는 바로 해당 container를 삭제할 수 없다. 따라서 실행 중인 container를 먼저 중단하는 것이 필수다. 
5. 실행 중단된 container를 삭제한다.
    1. `docker container rm` 명령어를 이용한다. 
6. 더 이상 image를 사용하지 않는다면 image도 삭제할 수 있다. 
    1. `docker image rm` 명령어를 이용한다. 

Docker Desktop과 같은 GUI 환경에서 작업하는 경우, 현재 image, container들의 목록 및 상태들을 한 눈에 확인할 수 있지만, CLI의 경우 이를 바로 알 수는 없기에 컨테이너의 경우에는 `docker ps` (`docker container ls` 의 축약형) 명령어를, 이미지의 경우에는 `docker image ls`  명령어를 이용하여 각각 컨테이너, 이미지 목록과 상태를 확인하면서 특정 컨테이너, 이미지를 생성, 중단, 삭제할지를 결정한다. 

## 실습해보기

이 챕터에서는 앞서 말한 컨테이너 생명주기에 대해 간단하게 CLI 환경에서 실습해보고자 한다. 

먼저 Docker Desktop 및 리눅스(필자의 경우 우분투) CLI를 실행한다. 

여기서는 이미지, 컨테이너의 생명주기에 대해서 다룰 것이기에 필요한 인자가 덜하기도 하고 비교적 간단한 프로그램을 대상으로 실습해보고자 한다. 여기서는 Apache 웹 서버인 `httpd` 를 대상으로 실습해보겠다. 

참고로, MySQL 등 유명한 소프트웨어들의 docker image들을 확인, 검색해보고자 한다면 “[DockerHub](https://hub.docker.com/)”라는 docker registry에서 확인 가능하다. 

![사진 3-1. DockerHub의 메인 페이지](/images/2026-07-17/docker-image-container-life-cycle/4.png)

사진 3-1. DockerHub의 메인 페이지

![사진 3-2. DockerHub에서 httpd를 검색한 결과. 여기서 httpd의 사용법, 버전, 그 외 정보들을 확인할 수 있다. ](/images/2026-07-17/docker-image-container-life-cycle/5.png)

사진 3-2. DockerHub에서 httpd를 검색한 결과. 여기서 httpd의 사용법, 버전, 그 외 정보들을 확인할 수 있다. 

먼저 bash 창에서 다음과 같이 입력한다.

```bash
$ docker run --name my-apache-1 -d httpd
Unable to find image 'httpd:latest' locally
latest: Pulling from library/httpd
010f7d7e175f: Pull complete
ebd81352b960: Pull complete
4f4fb700ef54: Pull complete
062e450697fa: Pull complete
69b5b831a361: Pull complete
39d55e966aeb: Pull complete
507729664175: Download complete
c72742279dc1: Download complete
Digest: sha256:305fd8326a27a137fedb8900f26375699ab233cc37793f35cf5d7c65671fb252
Status: Downloaded newer image for httpd:latest
cf265bec25a87109acb66d2efce74453bd7e14fa3d8271cefa73f0a4cecfc1da
```

코드 2-1.

`docker run --name my-apache-1 -d httpd` 와 같이 명령어를 입력하였다. 

- `--name my-apache-1` : 이 컨테이너의 이름을 `my-apache-1` 로 지정한다. 이름은 해당 컨테이너가 어떤 것이고 어떤 작업을 하는지에 관련 있게 지으면 되겠다.
- `-d` : 해당 컨테이너를 백그라운드로 동작시킨다.
- `httpd` : 앞서 언급한 Apache 웹 서버인 `httpd` 를 대상으로 실행한다.

앞서 언급했듯, 해당 명령어는 로컬 기기에 해당 image가 없다면 DockerHub에서 자동으로 이를 다운로드 받는다. 그리고 그 이미지로부터 컨테이너를 생성, 실행까지 자동화해준다. 위 코드 2-1에서도 로컬 기기에 해당 이미지가 없어 이를 다운로드받는 과정이 나타나 있다. 

`docker image ls` 를 통해 현재 저장된 이미지 목록을 확인할 수 있다.

```bash
$ docker image ls
                                                                                                    i Info →   U  In Use
IMAGE          ID             DISK USAGE   CONTENT SIZE   EXTRA
httpd:latest   305fd8326a27        177MB         47.6MB    U
```

코드 2-2.

그리고 `docker ps` 를 통해 현재 컨테이너 정보도 목록으로 확인할 수 있다. 

```bash
$ docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED              STATUS              PORTS     NAMES
cf265bec25a8   httpd     "httpd-foreground"   About a minute ago   Up About a minute   80/tcp    my-apache-1
```

코드 2-3.

| 항목 | 내용 |
| --- | --- |
| CONTAINER ID | 컨테이너 식별자. 컨테이너 식별자는 영어, 숫자의 랜덤한 글자이므로, 사람의 입장에서 식별하기 어려울 때에는 앞서 `--name my-apache-1` 처럼 컨테이너에 이름을 부여해주면 된다.  |
| IMAGE | 해당 컨테이너를 생성할 때 사용한 이미지명 |
| CREATED | 컨테이너 생성 후 경과 시간 |
| STATUS | 컨테이너의 현재 상태로, 실행 중이라면 `Up` , 종료된 상태라면 `Exited` 로 표시됨.  |
| PORTS | 컨테이너에 할당된 포트 번호. `호스트 -> 컨테이너` 형식으로 포트 번호가 출력되며, 두 포트 번호가 동일할 경우 화살표 뒷부분은 출력되지 않는다.  |
| NAMES | 컨테이너 이름. 컨테이너 생성 시 `--name` 옵션을 사용했다면 해당 옵션 값으로 표시될 것이다.  |

`docker ps` 의 경우에는 현재 실행되고 있는 컨테이너만을 출력하며, `docker ps -a` 명령어를 사용하면 정지된 상태를 포함한 모든 컨테이너들을 보여준다. 

위 터미널 창에서 보듯, `docker ps` 로 출력한 컨테이너 목록에서 `STATUS` 부분이 “Up”으로 되어 있다면 현재 해당 컨테이너가 실행되고 있다는 뜻이다. 

이제 컨테이너의 쓸모가 다해 실행 중인 컨테이너를 삭제해본다고 해보겠다. 여기서 주의할 점은, 실행 중인 컨테이너에 대해 무작정 삭제 명령어를 주지 않고 그 전에 먼저 정지시켜야한다는 것이다. 

```bash
$ docker stop my-apache-1
my-apache-1
$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED          STATUS                     PORTS     NAMES
cf265bec25a8   httpd     "httpd-foreground"   11 minutes ago   Exited (0) 6 seconds ago             my-apache-1
```

코드 2-4. 

위와 같이 앞서 지정한 컨테이너 이름인 `my-apache-1` 을 이용하여 `docker stop ...` 명령어를 통해 해당 컨테이너를 중단시켰다. `docker stop ...` 명령어를 입력한 이후 해당 컨테이너명이 그대로 출력된다면 해당 명령어가 잘 수행되었다는 뜻이다. 그 결과 현재 실행 중인 컨테이너만을 출력하는 `docker ps` 결과물에 해당 컨테이너 정보가 사라졌고, `docker ps -a` 를 통해 살펴보았을 때 해당 컨테이너의 “STATUS”가 “Exited”로 중단된 것을 확인할 수 있다. 

이제 해당 컨테이너가 중단되었으니 삭제할 수 있게 되었다. 해당 컨테이너를 삭제해보겠다. 

```bash
$ docker rm my-apache-1
my-apache-1
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```

코드 2-5.

아까처럼 같은 컨테이너 이름을 대상으로 하여 `docker rm`  명령어를 입력하였다. 그 후 `docker ps -a` 로 확인해봤을 때 해당 컨테이너가 아예 삭제되었음을 확인할 수 있다. 

이렇듯, 컨테이너의 생명주기에 따른 생성, 실행, 중단, 삭제 작업을 수행하면서 수시로 `docker ps` 또는 `docker ps -a` 명령어를 입력하여 현재 컨테이너가 어떤 상태인지를 파악할 수 있다. CLI 환경에서는 현재 컨테이너 및 이미지의 상태가 어떤지 바로 확인할 수 없으니 이러한 명령어를 통해 확인하는 것이다. 

한 편 GUI 환경인 Docker Desktop에서는 다음과 같이 이미지 및 컨테이너 현황을 쉽게 살펴볼 수 있다. 

![사진 3-3. Docker Desktop에서 현재 이미지 목록을 확인. ](/images/2026-07-17/docker-image-container-life-cycle/6.png)

사진 3-3. Docker Desktop에서 현재 이미지 목록을 확인. 

![사진 3-4. Docker Desktop에서 현재 컨테이너 목록 및 상태를 확인할 수 있다. 아예 컨테이너가 없는 경우 화면에 아무것도 뜨지 않는다. ](/images/2026-07-17/docker-image-container-life-cycle/7.png)

사진 3-4. Docker Desktop에서 현재 컨테이너 목록 및 상태를 확인할 수 있다. 아예 컨테이너가 없는 경우 화면에 아무것도 뜨지 않는다. 

만약 컨테이너를 중단시킨 상태에서 해당 컨테이너를 아직 삭제하지 않았을 때, 다시 실행시키고자 하는 경우, `docker restart ...` 명령어를 이용하면 된다. 

```bash
$ docker ps -a
CONTAINER ID   IMAGE     COMMAND              CREATED              STATUS                     PORTS     NAMES
1823bf8045fa   httpd     "httpd-foreground"   About a minute ago   Exited (0) 2 seconds ago             my-apache-1
$ docker restart my-apache-1
my-apache-1
$ docker ps
CONTAINER ID   IMAGE     COMMAND              CREATED         STATUS         PORTS     NAMES
1823bf8045fa   httpd     "httpd-foreground"   2 minutes ago   Up 4 seconds   80/tcp    my-apache-1
```

코드 2-6.

컨테이너를 종료시킨 상태에서 이미지도 삭제하고자 하는 경우 다음과 같은 과정을 거치면 된다. 

우선 현재 컨테이너가 정말로 종료, 삭제되었는지 `docker ps -a` 를 통해 확인한다. 해당 컨테이너가 삭제됨을 확인하면 우선 `docker image ls` 를 통해 현재 이미지의 상태 및 이름을 확인한다.

```bash
$ docker image ls
                                                                                                    i Info →   U  In Use
IMAGE          ID             DISK USAGE   CONTENT SIZE   EXTRA
httpd:latest   305fd8326a27        177MB         47.6MB
```

코드 2-7.

그 후, 해당 이미지의 이름을 대상으로 하여 다음과 같이 명령어를 입력하여 삭제한다.

```bash
$ docker image rm httpd
Untagged: httpd:latest
Deleted: sha256:305fd8326a27a137fedb8900f26375699ab233cc37793f35cf5d7c65671fb252
$ docker image ls
                                                                                                    i Info →   U  In Use
IMAGE   ID             DISK USAGE   CONTENT SIZE   EXTRA
```

코드 2-8.

`docker image ls` 를 통해 다시 확인해보니 해당 이미지가 잘 삭제되었음을 확인할 수 있다. 

image의 경우 나중에 다시 활용할 기회가 있다면 굳이 삭제하지 않아도 된다. 삭제할 경우 나중에 다시 활용할 때 DockerHub에서 다시 다운로드 받아야하는 과정이 생기기에 이 과정에서 사실상 불필요한 요청을 DockerHub에 하는 것과 마찬가지이기 때문이다. 정말로 나중에 다시 사용할 기회가 없다고 판단되거나, 아니면 지금 당장 저장 용량 확보가 시급한 경우 등에 이미지를 삭제하면 되겠다. 

앞선 코드 2-7에서는 해당 이미지의 이름이 정확히는 `httpd:latest` 라 써져 있는데, 콜론(`:`) 뒤에 `latest` 라고 써져 있는 경우에는 `httpd` 만 입력해도 해당 이미지를 삭제할 수 있다. 다만 `httpd:2.4` 와 같이 뒤에 특정 버전명이 붙어 있는 경우에는 해당 버전명까지 명시하여 `docker image rm httpd:2.4` 와 같이 명시하여 삭제해야한다. 

한 편, `cf265bec25a8` 와 같이 무작위로 결정되는 container ID에 대해, 맨 앞의 `cf` 처럼 생략하고 입력해도 해당 컨테이너를 대상으로 명령 수행이 가능하다. 또는 container ID 모두 입력해도 된다. 

- `docker stop cf`
- `docker stop cf265bec25a8`

# 글을 마치며

이번 글에서는 CLI 환경에서 docker image를 내려받고 그로부터 컨테이너를 생성, 실행, 중단, 삭제해보는 작업까지 컨테이너의 생명주기에 대해 살펴보았다. 사실 이 글에서는 docker의 기초적인 사용법, 컨테이너의 생명주기에 초점을 맞추었기에 핵심만을 설명하다보니, 이 글에서 다룬 것만으로는 아마 실무적인 활용을 하기는 어려울 것이다. 그래서 이 글에서 다룬 명령어 사용 및 컨테이너 생명주기에 어느 정도 숙지되었다면 아마 다음과 같은 질문이 떠오를 것이다. 

- 웹 브라우저에서 웹 서버에 접속하려면 어떻게 해야하지?
- 웹 서버, WAS, DB 등 실제 세계에서는 여러 프로그램들을 서로 연계하여 서비스하는데, Docker에서는 이 컨테이너들을 어떻게 서로 연동하지?
- 데이터는 어디에 저장하지? 컨테이너 내부에 저장하면 컨테이너 삭제 시 같이 삭제될텐데…

이러한 점들은 앞으로 다른 글들에서 하나씩 다뤄볼 예정이다. 

---

References

[1] 지은이: 오가사와라 시게타카, 옮긴이: 심효섭, “그림과 실습으로 배우는 도커 & 쿠버네티스“, 위키북스

[2] [Docker - 일경험 프로그램](https://wikidocs.net/book/18040)

[3] [docker docs - Home](https://docs.docker.com/)