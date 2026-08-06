---
title: "[Docker] Docker compose를 이용하여 여러 컨테이너들을 손쉽게 실행해보자!"
category: "Infra & Cloud"
tag: ["Docker", "Docker Compose", "env", "dotenv", "environment", "secrets", "DB", "Health check"]
---

# Docker compose 개요

지금까지의 Docker 관련 글들에서는 Docker의 기본을 정리하기 위해 대부분 단일 컨테이너에 대해 살펴보았었다. 그러나 실무에서는 여러 컨테이너들을 띄워 실행하여 서비스할 것이다. 웹 앱에 대해 흔히 알려진 web server - WAS(Web Application Server) - DB로 구성된 3-tier 아키텍처를 도커로 띄울 때가 그 예시가 되겠다. 같은 호스트 서버 내부라도 각각의 3 tier 계층들을 별도의 컨테이너로 구동시키기 때문이다. 

이렇게 여러 컨테이너들을 연동하며 여러 개 띄울 때에는 지금까지 배운 도커 명령어들을 사람이 일일히 입력하여 띄우기가 번거로울 것이다. 사람의 손으로 명령어를 치다보니 오타나 명령어 순서가 바뀌는 등의 실수가 일어나기도 쉽다. 도커 컨테이너를 실행시키기 위해 사람이 일일히 손으로 명령어를 입력하는 횟수를 확연히 줄일 방법은 없을까? 

이를 위해 Docker compose라는 기술이 존재한다. 이미지를 빌드하기 위해 `Dockerfile` 이라는 별도의 파일에서 스크립트를 작성했던 것처럼, 해당 기술도 여러 컨테이너들을 서로 연동하여 띄우는 것을 스크립트화할 수 있는 기술이다. 보통 `docker-compose.yml` 이름을 가지는 파일을 만들어 그 안에 여러 컨테이너들을 어떻게 띄울 것인지 스크립트로 작성하는 방식이다[^1]. Docker compose도 yaml 파일을 이용하여 어떻게 여러 컨테이너들을 일괄적으로 띄울 것인지를 결정하므로, 일종의 IoC(Infrastructure as Code)라 부를 수 있겠다. 여러 컨테이너들을 일괄적으로 어떻게 실행할 것인지 스크립트로 작성하는 기술이라고는 했지만 사실 이전에 살펴봤던 도커에서의 볼륨 및 네트워크도 만들고 컨테이너와 연동시킬 수 있다. 

Docker compose 사용 시 얻을 수 있는 이점에는 다음과 같이 있다.

- 여러 의존하는 컨테이너들과 이에 필요한 네트워크, 볼륨들을 일괄적으로 한꺼번에 생성, 실행, 중단, 삭제할 수 있다. 개발자가 도커 명령어를 일일이 입력할 필요가 없다.
- `docker-compose.yml` 파일 하나로 어느 환경에서든 동일한 멀티 컨테이너 생성 및 삭제가 가능하다.
- Docker compose 사용 시 컨테이너 생성에 사용되는 설정들을 캐싱한다. 이로 인해, 변경되지 않은 서비스 재시작 시 이미 존재하는 컨테이너를 재사용한다.
    - 반대로 설정이 변경된 컨테이너의 경우, 재시작 시 해당 컨테이너는 삭제되고 새로운 컨테이너가 생성된다. 이로 인해 컨테이너에 할당되는 IP 주소는 이전 것과 다르게 부여된다. 다만 컨테이너 이름은 이전과 동일하다.
    - Docker compose 사용에 의해 컨테이너에 이름이 부여되고, 정지 후 재시작 시에도 참조하므로 도커 엔진에서는 웬만해선 컨테이너 이름을 건드리지 않는 것이 좋다.

Docker compose는 현재 기준으로는 v5 버전대까지 출시되었다. 그 중 v1의 경우 파이썬으로 만들어졌다고 한다. 그래서 이 버전을 사용하기 위해선 파이썬 런타임 및 관련 의존성들을 설치해야했다고 한다. 다만 v2 이후부터는 Go라는 언어로 만들어졌다고 한다. 이 과정에서 Docker compose 설치 방법 및 명령어에도 변화가 생겼다. 

Docker compose를 사용하기 위해 설치하는 방법에는 크게 2가지가 있는데, 하나는 Docker Desktop을 설치하는 것이고, 또 하나는 리눅스에서 `sudo apt-get install docker-compose-plugin` 명령어를 이용하여 설치하는 방법이다. 전자의 경우, Docker compose가 이미 Docker Desktop 내부에 내장되어 있어 해당 프로그램만 설치하면 자동으로 설치된다고 한다. 후자의 경우, 주로 운영, 배포용으로 사용될 리눅스 서버에서 Docker engine 및 Docker CLI가 설치된 상태에서 plugin 형태로 설치하는 방식이라고 한다. 이 글에서는 전자의 방법을 이용할 것이다. 이미 이전 글에서 Docker Desktop 설치법을 살펴보았기에 Docker compose를 사용하기 위해 별도로 설치할 것은 없다. 

Docker compose가 엄연히 Docker와 다른 프로그램이라서 별도의 설치가 필요하다 하더라도 docker compose로 실행한 컨테이너나 볼륨 등은 여전히 도커 엔진 위에서 동작하기에 도커 엔진에서도 해당 컨테이너나 볼륨 등을 접근, 관리할 수 있다. 

<details id="ref-1">
<summary>
    <strong>
        <i>참고 - Docker compose 설치 방법의 변화</i>
    </strong>
    <a href="#ref-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
다른 분들의 예전 블로그 글들을 보면 간혹 Docker compose 설치 방법 중 하나로 다음과 같은 식의 명령어를 입력하여 설치하는 방법이 소개되곤 하는데,
    
```bash
curl -SL https://github.com/docker/compose/releases/download/v5.3.1/docker-compose-linux-x86_64 -o /usr/local/bin/docker-compose
```
    
Docker docs에 따르면 해당 방법은 옛날 방법이라 현재는 권고되지 않고 있다. 앞서 말한 Docker Desktop 또는 `docker-compose-plugin` 이 두 가지 방식이 권고되고 있다. 
</div>
</details>

# Docker compose 실습

Docker compose의 스크립트 문법을 일일이 먼저 정리하기보다는 실습을 통해 먼저 윤곽을 살펴보고 정리해보는 게 나을 것 같아서 실습 과정을 먼저 보이고자 한다. 

다음 챕터에서부터는 이전 글인 “[**[Docker] 동일 호스트 내 여러 컨테이너들 간 통신을 위한 연동 - 여러 컨테이너들을 네트워크로 연동하기 실습**](/infra%20&%20cloud/docker-network-among-containers-in-the-same-host/#%EC%97%AC%EB%9F%AC-%EC%BB%A8%ED%85%8C%EC%9D%B4%EB%84%88%EB%93%A4%EC%9D%84-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC%EB%A1%9C-%EC%97%B0%EB%8F%99%ED%95%98%EA%B8%B0-%EC%8B%A4%EC%8A%B5)” 챕터에서 실행해보았던 워드프레스 + DB를 Docker compose로 실행시켜보는 실습을 가져볼 것이다. 여기서는 MariaDB를 대신 사용할 것이다. 

## 기본 실습

먼저 필자는 Windows에서 `C:\docker\compose_ex\basic` 폴더를 마련하였다. 폴더 경로는 사실 원하는 곳에 지정해도 상관없다. 해당 폴더 안에 다음과 같이 `compose.yaml` 파일을 만들었다. 

```yaml
# services: 여러 컨테이너들을 정의. 
services:
  db:
    image: mariadb:latest
    volumes:
      - dbvol:/var/lib/mysql  # Volume mount
    ports:
      - 3308:3306

    # 컨테이너 종료 후 재시작 옵션.
    # unless-stopped => 종료 코드 상관없이 재시작하나, 
    # 사용자가 컨테이너를 정지, 삭제한 경우에는 재시작하지 않음. 
    restart: unless-stopped  
    environment:
      MARIADB_ROOT_PASSWORD: jerorootforwp
      MARIADB_USER: jero123
      MARIADB_PASSWORD: jerowppass
      MARIADB_DATABASE: wordpressdb
  
  wordpress:
    # 특정 컨테이너가 시작되고 나서 이 컨테이너가 시작되도록 한다. 
    depends_on:
      - db
    image: wordpress
    volumes:
      - wordpressvol:/var/www/html
    restart: unless-stopped
    ports:
      - 8085:80
    environment:
      WORDPRESS_DB_HOST: db  # db 컨테이너 이름 명시
      WORDPRESS_DB_USER: jero123
      WORDPRESS_DB_PASSWORD: jerowppass
      WORDPRESS_DB_NAME: wordpressdb

volumes:
  dbvol:
  wordpressvol:

```

코드 1-1. `compose.yaml`

주요 항목을 정리하면 다음과 같다.

```yaml
services:
  ...
networks:
  ...
volumes:
  ...
```

코드 1-2. 

`services` 는 여러 컨테이너들을 정의할 수 있는 곳이다. 위 스크립트에서는 워드프레스를 실행시키기 위해 필요한 두 개의 컨테이너들을 각각 `db` , `wordpress` 라 하였다. `services.<service_name>` 구조에서 서비스 이름은 자유롭게 정할 수 있다. 

참고로 여기서 말하는 서비스라는 용어는 의미적으로는 컨테이너나 다를 바가 없다. 그래서 `services` 라 하면 여러 컨테이너들을 정의하는 곳이다. 다만, Docker compose로 실행시켜보면 실제 컨테이너 이름은 `compose.yaml` 에서 정의한 서비스 이름과 조금 다르며, `docker compose ps` 를 통해 실행되는 컨테이너들의 정보를 출력해봐도 컨테이너 이름과 서비스 이름을 구분한다. 이 글에서도 의미론적으로는 구분하지 않되, 기술적으로 구분해야할 경우에는 구분해서 부르도록 하겠다. 

`services` 내부의 각 컨테이너 항목들에 대해 `volumes` 속성이 있고, `<volume_name>:<container-path>` 와 같은 형식으로 볼륨 마운트를 하였다. 물론  `./:/var/lib/mysql` 처럼 호스트 경로를 상대 경로 또는 절대 경로로 지정하여 bind mount를 할 수도 있다. 볼륨 마운트의 경우, 해당 속성에 작성한 볼륨명을 하단의 `volumes` 속성에도 등록해야 한다. 

`restart` 속성은 컨테이너 종료 시 재시작 설정을 하는 속성이다. 다음과 같은 속성값들이 있다.

- `no` : 기본값. 어떤 상황에서도 컨테이너들을 재시작하지 않는다.
- `always` : 컨테이너가 삭제되지 않는 한 항상 재시작한다.
- `on-failure[:max-retries]` : 프로그램 종료 코드(exit code)가 0이 아닐 경우, 즉 정상적인 종료가 아닐 경우 재시작한다. 만약 뒤에 숫자를 붙이면 최대 그 숫자만큼만 재시작을 시도한다.
- `unless-stopped` : 종료 코드와는 상관없이 재시작하나, 사용자가 명시적으로 컨테이너를 중단 또는 삭제한 경우에는 재시작하지 않는다. 그 외 경우에는 항상 재시작한다. 실무에서 추천되는 옵션.

`wordpress` 컨테이너 항목에는 `depends_on` 항목이 있으며, 그 값으로 `db` 를 지정하였다. 이는 `db` 를 먼저 실행하고 나서 `wordpress` 컨테이너를 실행하겠다는 뜻으로, 컨테이너 실행 순서를 지정할 때 해당 옵션을 사용한다. 중단할 때에는 역순으로 `wordpress` 가 먼저 중단된 뒤에 `db` 가 그 다음에 중단된다. 

Docker compose를 이용하면 기본적인 네트워크가 생성되어 Docker compose 스크립트 내에 명시한 모든 컨테이너들이 이 네트워크에 연결되어 상호작용할 수 있다. 그래서 굳이 `network` 속성을 사용하여 사용자 정의 network를 생성할 필요는 없다. 다만, `compose.yaml` 스크립트에 작성한 컨테이너들 중 몇몇 컨테이너들끼리 서로 독립적인 네트워크로 연결하고자 할 경우에 활용하면 되겠다. `networks` 도 위 스크립트에서 보인 `volumes` 와 똑같은 방식으로 작성하면 된다.

```yaml
services:
  db:
    networks:
      - wordpressnet
  ...

  wordpress:
    networks:
      - wordpressnet
  ...
	
networks:
  - wordpressnet:

```

코드 1-3. `networks` 사용 시의 Docker compose 문법 형식. 

Docker compose 스크립트에서 별도로 네트워크를 지정하지 않아 기본으로 생성되는 네트워크의 경우, 프로젝트 폴더 이름을 따라 `<project_name>_default` 라는 이름의 네트워크가 `bridge` driver로 생성된다. 이렇게 Docker compose로 생성된 기본 네트워크는 컨테이너들을 지칭할 때 DNS을 자동으로 제공하여 IP 주소가 아닌 도메인 이름으로 지칭할 수 있다. 

Docker compose로 생성되는 기본 네트워크는 어찌되었든 자동으로 그 이름이 지어지기도 해서 default bridge network는 아니다. 그래서 default bridge의 단점인 DNS 미제공으로 인한 컨테이너를 IP 주소로만 참조 가능한 점, 원치 않은 컨테이너의 연결 등이 Docker compose로 생성한 네트워크에선 보이지 않는다. 

이제 위에서 작성한 `compose.yaml` 파일을 도커 엔진에서 실행해보자. 

Docker compose 실행 시 사용 가능한 명령어 형식은 다음과 같다. 

```bash
docker compose up <옵션>
# 예) docker compose up -d  // 컨테이너들을 백그라운드에서 실행.

docker compose -f <경로명> up <옵션>
# 예) docker compose -f /mnt/c/docker/compose.yaml up -d  // 특정 경로에 있는 docker compose yaml 파일을 실행. 
```

코드 1-4.

실행, 중단 뿐만 아니라 `docker compose` 명령어로 로깅 등의 추가적인 활동을 하고자 한다면 처음 실행하기 전 프로젝트 디렉터리로 먼저 이동한 후(`cd` 명령어 이용)에 실행하는 것을 추천한다. 그렇지 않고 `-f` 옵션을 통해 프로젝트 경로를 지정하여 실행한 경우, 로그 확인이나 프로세스 확인할 때에도 `docker compose -f <경로> logs` , `docker compose -f <경로> ps` 처럼 매번 `-f <경로>` 를 입력해야하기에 번거롭기 때문이다. 

이제 리눅스 쉘에서 도커 컴포즈를 실행해보자. 다음의 명령어를 순차적으로 입력하여 도커 컴포즈를 실행하였다. 

```bash
$ cd /mnt/c/docker/compose_ex/basic
$ docker compose up -d
```

코드 1-5.

위 코드에서 마지막 명령어 입력 시 대략 다음과 같은 출력 결과가 보일 것이다.

```bash
[+] up 5/5
 ✔ Network basic_default       Created                                                                                             0.0s
 ✔ Volume basic_wordpressvol   Created                                                                                             0.0s
 ✔ Volume basic_dbvol          Created                                                                                             0.0s
 ✔ Container basic-db-1        Started                                                                                             0.5s
 ✔ Container basic-wordpress-1 Started                                                                                             0.6s
```

코드 1-6. 

그 후, `docker compose ps` 또는 `docker compose ps -a` 명령어로 앞선 Docker compose를 통해 실행되고 있는 컨테이너들의 상태를 확인할 수 있다. 

앞서 wordpress의 포트 매핑을 8085로 하였는데, 웹 브라우저에서 `http://localhost:8085` 로 들어가보면 워드프레스 화면을 볼 수 있다. 

Docker compose로 생성한 볼륨, 컨테이너 등의 객체들은 모두 그 이름에 프로젝트 폴더명이 붙는다. 네트워크의 경우 기본으로 생성되는 네트워크는 `<project_name>_default` 로, 볼륨은 `<project_name>_<volume_name>` 의 형태로, 컨테이너의 경우 `<project_name>-<container_name>-1` 과 같은 형태로 붙는다. 위 실습의 경우, 프로젝트 폴더명이 `basic` 이었기에 `basic_wordpressvol` , `basic-db-1` 과 같이 이름이 생성된다. 

이제 Docker compose로 실행되는 여러 컨테이너들을 중단 및 삭제해보겠다. 다음의 명령어를 입력하면 된다.

```bash
docker compose down -v
```

코드 1-7.

기본적으로 `docker compose down` 은 컨테이너 및 네트워크를 자동으로 종료 및 삭제한다. 다만 볼륨만은 남아있는데, 이 볼륨까지도 자동으로 삭제하고자 뒤에 `-v` 옵션을 붙인 것이다. 

위 명령어를 실행하면 다음과 같은 형태의 출력 결과를 볼 수 있다.

```bash
[+] down 5/5
 ✔ Container basic-wordpress-1 Removed                                                                                             1.4s
 ✔ Container basic-db-1        Removed                                                                                             0.4s
 ✔ Volume basic_dbvol          Removed                                                                                             0.0s
 ✔ Network basic_default       Removed                                                                                             0.2s
 ✔ Volume basic_wordpressvol   Removed                                                                                             0.2s
```

코드 1-8.

docker 명령어를 통해 Docker compose로 생성된 컨테이너, 볼륨, 네트워크 등을 살펴보면 삭제되어 존재하지 않는 것을 확인할 수 있다. 

<details id="ref-2">
<summary>
    <strong>
        <i>참고 - 만약 Docker compose를 사용하지 않았다면.</i>
    </strong>
    <a href="#ref-2" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
위 실습에서 만약 Docker compose를 사용하지 않았다면 각각 컨테이너 생성, 실행 및 종료 과정은 docker 명령어로 다음과 같이 순차적으로 입력했어야 할 것이다. 
    
```bash
$ docker network create wordpress-net
$ docker volume create dbvol
$ docker volume create wordpressvol
$ docker run --name db -dit \
  -p 3308:3306 \
  --net=wordpress-net \
  -v dbvol:/var/lib/mysql \
  -e MARIADB_ROOT_PASSWORD=jerorootforwp \
  -e MARIADB_USER=jero123 \
  -e MARIADB_PASSWORD=jerowppass \
  -e MARIADB_DATABASE=wordpressdb \
  mariadb:latest
$ docker run --name wordpress -dit \
  -p 8085:80 \
  --net=wordpress-net \
  -v wordpressvol:/var/www/html \
  -e WORDPRESS_DB_HOST=db \
  -e WORDPRESS_DB_USER=jero123 \
  -e WORDPRESS_DB_PASSWORD=jerowppass \
  -e WORDPRESS_DB_NAME=wordpressdb \
  wordpress
```
    
코드 1-9. docker 명령어만을 이용하여 워드프레스 서비스를 구동하는 명령어들. 
    
```bash
$ docker stop wordpress
$ docker stop db
$ docker rm wordpress
$ docker rm db
$ docker network rm wordpress-net
$ docker volume rm dbvol
$ docker volume rm wordpressvol
```
    
코드 1-10. docker 명령어만을 이용하여 워드프레스 서비스를 종료, 삭제하는 명령어들.
</div>
</details>

### Docker compose 명령어 정리

Docker compose yml 스크립트에서 사용 가능한 문법들은 다음의 사이트를 참조.

- [https://docs.docker.com/reference/compose-file/](https://docs.docker.com/reference/compose-file/)

Docker CLI에서 사용 가능한 Docker compose 명령어들에 대해선 다음의 사이트를 참조.

- [https://docs.docker.com/reference/cli/docker/compose/](https://docs.docker.com/reference/cli/docker/compose/)
- [https://schoolofweb.net/ko/posts/docker-intermediate-3/#일상-명령군](https://schoolofweb.net/ko/posts/docker-intermediate-3/#%ec%9d%bc%ec%83%81-%eb%aa%85%eb%a0%b9%ea%b5%b0)

Docker CLI에서 사용 가능한 Docker compose 명령어들을 간단하게 정리하면 다음과 같다.

```bash
# up 관련 명령어
# up 명령어는 컨테이너와 그 외 필요한 네트워크, 볼륨 등을 생성, 실행하는 명령어. 

# docker compose에 있는 컨테이너 실행. 
# `yml` 파일에 변경 사항 발생 시 해당 사항을 반영하여 실행한다. 
# 설정 내용이 변경된 컨테이너의 경우, 이름은 똑같아도 이전의 것은 삭제되고 새로운 게 생성됨. 
docker compose up

# 컨테이너들을 백그라운드로 실행
docker compose up -d

# 경로 지정. `compose.yaml`, `docker-compose.yml` 등 지정된 파일명이 아닌 다른 파일명을 사용했을 때에도 이 옵션을 사용한다.
docker compose -f <경로> up

# 코드 변경 등의 이유로 이미지 빌드를 처음부터 다시 하고자 할 경우
docker compose up --build

# ===== down 관련 명령어 =====
# down 명령어는 실행 중인 컨테이너 및 네트워크를 자동으로 종료 및 삭제하는 명령어이다.
docker compose down

# 볼륨까지 삭제
docker compose down -v

# 실행 과정에서 빌드된 이미지까지 삭제하고자 할 경우
docker compose down --rmi local

# ===== ps 관련 명령어 =====
# docker compose로 실행 중인 프로세스(컨테이너) 상태를 확인한다.
docker compose ps

# 실행 중이지 않은 컨테이너까지 확인.
docker compose ps -a 

# ===== logs 관련 명령어 =====
# 현재 실행 중인 서비스들(컨테이너)의 log를 확인.
docker compose logs

# 실시간으로 출력되는 로그까지 계속 화면에 출력
docker compose logs -f

# 최근 몇 분 동안의 로그만 확인. 아래 명령어의 경우 최근 10분 로그만 확인. 
docker compose --since 10m

# ===== exec 관련 명령어 =====
# 실행 중인 특정 컨테이너 내부의 bash를 통해 진입.
docker compose exec <service_name> bash

# ===== 그 외 명령어들 =====

# 삭제되지 않고 중지된 컨테이너들을 다시 띄운다. `yml` 파일 변경 사항은 반영하지 않는다.
docker compose start

# 특정 컨테이너를 중단(stop) 후 재시작(start)한다. `yml` 설정 파일 변경 사항은 반영하지 않는다.
docker compose restart <service_name>

# 실행 중인 컨테이너를 중단한다. 단 삭제하지는 않는다.
docker compose stop
```

코드 2-1. docker CLI 환경에서의 docker compose 명령어들 정리. 

`compose.yaml` 파일 내용을 수시로 변경하면서 그 반영사항을 보고자 한다면 `up` 명령어를 사용한다. 

파이썬으로 작성되었던 v1 버전대의 경우, 명령어도 `docker-compose` 처럼 중간에 대시(`-`) 기호가 붙었던 반면 v2 버전대 이후부터는 `docker compose` 명령어로 바뀌었다. 

## 환경 변수(environment variables) 및 민감 정보(secrets) 별도로 관리하기

앞선 실습에서는 기본적인 Docker compose를 다뤄보았다. 그런데 앞서 작성한 `compose.yaml` 파일 내용을 자세히 보면 DB password 등의 민감한 정보가 그대로 작성되어 있는 것을 볼 수 있다. 이는 해당 파일을 Github repo에 올릴 때 그대로 유출되기에 민감한 정보들을 그대로 Docker compose 파일에 작성하면 안되고 별도로 관리해야한다. 

뿐만 아니라 포트 번호도 별도의 환경 변수 파일로 빼내어 다루면 좀 더 유연한 설정을 할 수 있다. 이 챕터에서는 Docker compose에서 환경 변수와 민감 정보(secrets)를 별도의 파일로 다루는 방법에 대해 살펴보도록 하겠다. 

앞선 실습에서 보았듯, 컨테이너에서 필요한 환경변수들은 `environment` 속성을 통해 하나씩 주입할 수 있음을 보았다. 다만, `compose.yaml` 파일 내용에는 거의 변경사항이 일어나지 않게끔 하면서도 그 안의 환경 변수 값들을 다르게 하고자 할 때에는 별도의 `.env` 파일로 분리하여 다룰 수 있다. 

이에 대한 실습을 위해 필자는 `C:\docker\compose_ex` 폴더 내부에 `env` 폴더를 따로 마련하였고, 그 안에 각각 `.env` 파일과 `compose.yaml` 파일을 마련하였다. 

```bash
DB_PORT=3308
WORDPRESS_PORT=8085
DB_ROOT_PASSWORD=jerorootforwp
DB_USER=jero123
DB_PASSWORD=jerowppass
DB_DATABASE=wordpressdb
```

코드 3-1. `.env` 파일 내용.

```yaml
# services: 여러 컨테이너들을 정의. 
services:
  db:
    image: mariadb:latest
    volumes:
      - dbvol:/var/lib/mysql  # Volume mount
    ports:
      - ${DB_PORT}:3306

    # 컨테이너 종료 후 재시작 옵션.
    # unless-stopped => 종료 코드 상관없이 재시작하나, 
    # 사용자가 컨테이너를 정지, 삭제한 경우에는 재시작하지 않음. 
    restart: unless-stopped  
    environment:
      MARIADB_ROOT_PASSWORD: ${DB_ROOT_PASSWORD}
      MARIADB_USER: ${DB_USER}
      MARIADB_PASSWORD: ${DB_PASSWORD}
      MARIADB_DATABASE: ${DB_DATABASE}
  
  wordpress:
    # 특정 컨테이너가 시작되고 나서 이 컨테이너가 시작되도록 한다. 
    depends_on:
      - db
    image: wordpress
    volumes:
      - wordpressvol:/var/www/html
    restart: unless-stopped
    ports:
      - ${WORDPRESS_PORT}:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: ${DB_USER}
      WORDPRESS_DB_PASSWORD: ${DB_PASSWORD}
      WORDPRESS_DB_NAME: ${DB_DATABASE}

volumes:
  dbvol:
  wordpressvol:

```

코드 3-2. `compose.yaml`

위와 같이 `yaml` 스크립트에서는 `${var}` 형태의 보간(interpolation) 문법을 이용하여 `.env` 파일에 있는 환경변수 값들을 가져올 수 있다. `.env` 파일과 `compose.yaml` 파일은 모두 프로젝트 폴더 루트에 같이 만드는 것이 좋다. 

이 상태로 `docker compose up -d` 명령어로 띄워보면 문제없이 동작하는 것을 확인할 수 있다. 정 `.env` 파일 내 환경변수값들이 제대로 `compose.yaml` 에 주입되었는지를 확인하고자 한다면 `docker compose logs` 명령어를 입력하여 출력된 로그들을 살펴보면 된다.

```bash
db-1         | 2026-08-05 08:13:29+00:00 [Note] [Entrypoint]: Temporary server started.
db-1         | 2026-08-05 08:13:30+00:00 [Note] [Entrypoint]: Creating database wordpressdb
db-1         | 2026-08-05 08:13:30+00:00 [Note] [Entrypoint]: Creating user jero123
db-1         | 2026-08-05 08:13:30+00:00 [Note] [Entrypoint]: Giving user jero123 access to schema wordpressdb
db-1         | 2026-08-05 08:13:30+00:00 [Note] [Entrypoint]: Securing system users (equivalent to running mysql_secure_installation)
```

코드 3-3. `docker compose logs` 명령어 입력으로 출력된 로그 중 일부. 앞서 작성한 `.env` 파일 내 DB 관련 정보들이 그대로 출력되는 것을 볼 수 있다. 

한 편, 보간법 대신 아예 파일 자체를 읽어오도록 할 수도 있다. `compose.yaml` 파일 내 `env_file` 속성을 이용하면 된다. 다음은 그 예시이다.

```yaml
services:
  db:
    env_file:
      - .env
```

코드 3-4. `compose.yaml` 에서 `env_file` 속성을 쓰는 예시.

그러면 `compose.yaml` 파일에서 `env_file` 속성으로 지정한 `.env` 파일 내 모든 환경변수 값들을 읽어올 수 있다. `env_file` 속성을 이용한 방식에서도 이왕이면 `compose.yaml` 과 `.env` 파일을 같은 프로젝트 폴더 루트에 위치시키는 것이 좋다. 

한 편, 같은 환경변수들이 여러 경로에 존재하는 경우의 우선순위는 다음과 같다고 한다. 1에 가까울수록 가장 높은 우선순위, 가장 아래에 위치할수록 낮은 우선순위를 갖는다. 

1. `docker compose -e` 처럼 명령어에서 `-e` 옵션으로 환경변수를 주입하는 경우.
2. `compose.yaml` 파일에서 `environment` 속성.
3. `compose.yaml` 파일에서 지정한 `env_file` 속성.
4. `.env` 파일과 보간법을 사용하는 방식. 
5. 이미지 빌드에 쓰이는 `Dockerfile` 내에서의 `ENV` 인자.

한 편, `.env` 방식의 문제점은 그 안에 있는 모든 값들이 여전히 노출되어 민감 정보까지 함께 사용하기에는 보안상 위험성이 있다는 것이다. 앞선 실습에서 `docker compose up -d` 명령어로 실행해본 뒤, 다음의 명령어들을 입력해보면 이를 알 수 있다.

```bash
$ docker compose config
name: env
services:
  db:
    environment:
      MARIADB_DATABASE: wordpressdb
      MARIADB_PASSWORD: jerowppass
      MARIADB_ROOT_PASSWORD: jerorootforwp
      MARIADB_USER: jero123
    image: mariadb:latest
# (생략...)
```

코드 3-5. `docker compose config` 명령어 실행 후의 출력 결과

```bash
$ docker inspect env-db-1
# (생략...)
"Config": {
# (생략...)
  "Env": [
    "MARIADB_DATABASE=wordpressdb",
    "MARIADB_ROOT_PASSWORD=jerorootforwp",
    "MARIADB_USER=jero123",
    "MARIADB_PASSWORD=jerowppass",
# (생략...)
```

코드 3-6. `docker inspect env-db-1` 명령어 실행 후의 출력 결과

이렇듯 Docker 내에서 특정 명령어들을 통해 살펴보면 민감 정보가 고스란히 출력되는 것을 볼 수 있다. 

이로 인해, Docker에서는 민감 정보를 위해선 secrets 기능을 대신 이용할 것을 권고하고 있다. 

이번에는 `C:\docker\compose_ex` 에 `using-secrets` 라는 폴더를 만들고, 그 안에 다음과 같은 파일 및 서브 디렉터리를 만들었다.

```bash
/using-secrets
|   .env
|   compose.yaml
|   
\---secrets
        db_database.txt
        db_password.txt
        db_root_password.txt
        db_user.txt
```

코드 3-7. `using-secrets` 폴더 내부 구조.

```yaml
# services: 여러 컨테이너들을 정의. 

services:
  db:
    image: mariadb:latest
    volumes:
      - dbvol:/var/lib/mysql  # Volume mount
    ports:
      - ${DB_PORT}:3306

    # 컨테이너 종료 후 재시작 옵션.
    # unless-stopped => 종료 코드 상관없이 재시작하나, 
    # 사용자가 컨테이너를 정지, 삭제한 경우에는 재시작하지 않음. 
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD_FILE: /run/secrets/db_root_password
      MARIADB_USER_FILE: /run/secrets/db_user
      MARIADB_PASSWORD_FILE: /run/secrets/db_password
      MARIADB_DATABASE_FILE: /run/secrets/db_database
    secrets:
      - db_root_password
      - db_user
      - db_password
      - db_database
  
  wordpress:
    # 특정 컨테이너가 시작되고 나서 이 컨테이너가 시작되도록 한다. 
    depends_on:
      - db
    image: wordpress
    volumes:
      - wordpressvol:/var/www/html
    restart: unless-stopped
    ports:
      - ${WORDPRESS_PORT}:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER_FILE: /run/secrets/db_user
      WORDPRESS_DB_PASSWORD_FILE: /run/secrets/db_password
      WORDPRESS_DB_NAME_FILE: /run/secrets/db_database
    secrets:
      - db_user
      - db_password
      - db_database

secrets:
  db_user:
    file: ./secrets/db_user.txt
  db_root_password:
    file: ./secrets/db_root_password.txt
  db_password:
    file: ./secrets/db_password.txt
  db_database:
    file: ./secrets/db_database.txt

volumes:
  dbvol:
  wordpressvol:
  
```

코드 3-8. `compose.yaml` 

```
# db_users.txt
jero123

# db_root_password.txt
jerorootforwp

# db_database.txt
wordpressdb

# db_password.txt
jerowppass
```

코드 3-9. 각각의 `db_~.txt` 파일 내용

```
DB_PORT=3308
WORDPRESS_PORT=8085
```

코드 3-10. `.env` 파일. 민감 정보만 제외하였다. 

`compose.yaml` 내용을 보면, `services` 항목 아래에 `secrets` 항목을 작성한 후, `compose.yaml` 파일 내에서 지칭할 `secret_name`을 `db_password` , `db_user` 와 같이 지정한다. 그리고 각 속성에 대해 `file: ...` 속성을 이용하여 각각 실제 secrets이 어디에 작성되어 있는지 그 파일 위치를 명시한다. 

그 후, 이 secrets을 필요로 하는 service마다 그 아래에 `secrets` 속성을 작성하여 제공한다. 여기에는 해당 서비스에서 사용할 `secret_name` 을 지정한다. 그리고 `environment` 속성 내에서 `_FILE` 접미사가 붙은 환경변수 속성값에 `/run/secrets/<secret_name>` 을 적으면 된다. 여기서 secret 파일들은 자동으로 `/run/secrets/...` 경로로 마운트되기에 이 점을 이용한 것이다. 

한 편, `_FILE` 접미사는 각각 wordpress, mariadb에서 지원하는 접미사여서 그대로 사용하였다. 어떤 image를 사용하느냐에 따라 이 점이 달라질 수 있으니 Docker hub 등에서 사용하고자 하는 이미지에 대해 `_FILE` 접미사를 지원하는지 여부를 확인하는 것이 좋겠다. 

이전에 실행하던 docker compose가 있다면 `docker compose down -v` 를 통해 볼륨까지 삭제한 후, 위 스크립트를 실행해보자.

```bash
$ cd /mnt/c/docker/compose_ex/using-secrets
$ docker compose up -d
```

코드 3-11. 

그리고 앞서 보았던 것처럼, 민감 정보가 화면에 출력되는지 확인해보자.

```bash
$ docker compose config
name: using-secrets
services:
  db:
    environment:
      MARIADB_DATABASE_FILE: /run/secrets/db_database
      MARIADB_PASSWORD_FILE: /run/secrets/db_password
      MARIADB_ROOT_PASSWORD_FILE: /run/secrets/db_root_password
      MARIADB_USER_FILE: /run/secrets/db_user
    image: mariadb:latest
    networks:
      default: null
    ports:
      - mode: ingress
        target: 3306
        published: "3308"
        protocol: tcp
    restart: unless-stopped
    secrets:
      - source: db_root_password
        target: /run/secrets/db_root_password
      - source: db_user
        target: /run/secrets/db_user
      - source: db_password
        target: /run/secrets/db_password
      - source: db_database
        target: /run/secrets/db_database
    volumes:
      - type: volume
        source: dbvol
        target: /var/lib/mysql
        volume: {}
  wordpress:
    depends_on:
      db:
        condition: service_started
        required: true
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_NAME_FILE: /run/secrets/db_database
      WORDPRESS_DB_PASSWORD_FILE: /run/secrets/db_password
      WORDPRESS_DB_USER_FILE: /run/secrets/db_user
    image: wordpress
    networks:
      default: null
    ports:
      - mode: ingress
        target: 80
        published: "8085"
        protocol: tcp
    restart: unless-stopped
    secrets:
      - source: db_user
        target: /run/secrets/db_user
      - source: db_password
        target: /run/secrets/db_password
      - source: db_database
        target: /run/secrets/db_database
    volumes:
      - type: volume
        source: wordpressvol
        target: /var/www/html
        volume: {}
networks:
  default:
    name: using-secrets_default
volumes:
  dbvol:
    name: using-secrets_dbvol
  wordpressvol:
    name: using-secrets_wordpressvol
secrets:
  db_database:
    name: using-secrets_db_database
    file: /mnt/c/docker/compose_ex/using-secrets/secrets/db_database.txt
  db_password:
    name: using-secrets_db_password
    file: /mnt/c/docker/compose_ex/using-secrets/secrets/db_password.txt
  db_root_password:
    name: using-secrets_db_root_password
    file: /mnt/c/docker/compose_ex/using-secrets/secrets/db_root_password.txt
  db_user:
    name: using-secrets_db_user
    file: /mnt/c/docker/compose_ex/using-secrets/secrets/db_user.txt
```

코드 3-12. `docker compose config` 명령어 실행 후 출력 결과

```bash
$ docker inspect using-secrets-db-1
# (생략...)
"Config": {
  "Env": [
    "MARIADB_USER_FILE=/run/secrets/db_user",
    "MARIADB_PASSWORD_FILE=/run/secrets/db_password",
    "MARIADB_DATABASE_FILE=/run/secrets/db_database",
    "MARIADB_ROOT_PASSWORD_FILE=/run/secrets/db_root_password",
# (생략...)
```

코드 3-13. `docker inspect ...` 명령어 실행 후 출력 결과.

보다시피, 단순 `.env` 방식에 비해 이번에는 민감 정보가 그대로 노출되지 않는 것을 볼 수 있다. 단, `docker compose logs db` 를 통해 MariaDB에 대한 로그를 보면 여전히 해당 DB 로그인 정보가 출력되기에 이에 대해선 별도로 주의가 필요하겠다. 

참고로 보안을 위해 `.env` 파일에 민감 정보를 넣었든, `/secrets/` 폴더 안에 민감 정보를 넣었든, Github repo에 실수로 업로드하는 것을 방지하기 위해 각각 `.gitignore` 에 추가하여 업로드되지 않도록 미리 방지하는 것을 잊지 말아야 한다. 

```
.env
secrets/
```

코드 3-14. 중요 정보들이 Github repo 등에 유출되지 않도록 작성한 `.gitignore` 예시. 

## DB health check

앞서 보았던 `compose.yaml` 파일에서, `depends_on` 을 통해 `db` 가 실행된 다음에 `web` 이 실행되도록 순서를 정하였다. 그런데 여기에는 한 가지 주의해야할 점이 있다. 단순히 `depends_on` 만 사용하면 실행 순서만 보장할 뿐, 이전 컨테이너가 제대로 동작하는지 확실히 확인한 상태에서 다음 컨테이너를 띄우는 방식이 아니라는 것이다. `db` 를 실행시키기만 하고 제대로 동작하는지는 확인하지 않은 채 바로 `web` 컨테이너를 띄우는 식이다. 즉, 컨테이너의 시작만 보장한다. 

이것이 생각보다 중요한 이유는, DB의 경우 처음 작동시킬 때 초기화 과정에서 시간이 걸리기 때문이다. DB가 초기화하여 아직 준비가 안된 사이에 백엔드 웹 앱 컨테이너가 이미 실행 중이고 DB와 연결을 시도하면 `connection refused` 로 연결이 실패해 백엔드 웹 앱도 예상치 못하게 종료된다[^2]. 

이 문제를 해결하기 위해선 DB 컨테이너가 정말 준비 완료되었는지 확인을 한 후에 백엔드 웹 앱 컨테이너를 띄우도록 하는 것이 좋다. 즉, DB에 대한 health check를 해야한다. 다행히도 Docker compose에서는 `healthcheck` 라는 속성을 제공한다. 

`compose.yaml` 에서 `healthcheck` 속성은 다음과 같은 형식으로 작성한다. 

```yaml
services:
  db:
    image: mariadb:latest
    # (생략...)
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 10s
      timeout: 10s
      retries: 5
      start_period: 10s
```

코드 4-1. 

- `test` : 어떤 명령어로 health check할 것인지 결정. 위 코드의 경우 `healthcheck.sh --connect --innodb_initialized` 라는 명령어를 띄어쓰기로 구분하여 넣은 것이다[^3]. 한 편 `test` 에 작성할 리스트의 첫 번째 인자에는 크게 3가지 중 하나를 선택하여 사용할 수 있다.
    - “CMD”: 직접 실행.
    - “CMD-SHELL”: Shell을 통해 실행. 여기서는 `$` 변수 사용이 가능하여 더 유연한 명령어 작성이 가능하다.
    - “NONE”: health check 비활성화할 때.
- `interval` : 몇 초마다 health check(검사) 할 것인지.
- `timeout` : 검사가 몇 초만에 끝나야하는지를 지정. 지정된 시간 안에 응답이 있어야 health check가 성공한 것으로 간주되고, 그렇지 않으면 실패한 것으로 간주됨.
- `retries` : 몇 번 연속으로 실패해야 정말로 실패한 것인지, 즉 unhealthy한 상태인지를 판별. 예를 들어 5라고 지정한 경우, 5번 연속 실패 시 해당 컨테이너는 최종적으로 unhealthy한 상태로 판정됨.
- `start_period` : 초기 시간 동안의 실패는 `retries` 로 카운팅하지 않고 무시함. 이 속성은 초기화 시간이 긴 컨테이너에 사용할 때 좋다. 예를 들어 10s으로 설정한 경우, 처음 컨테이너가 시작하고 10초 동안은 실패가 발생해도 이를 최종적인 실패로 간주하지 않고 무시한다.

이렇게 `healthcheck` 속성을 사용한다면, `depends_on` 에 의해 이에 의존하는 다른 컨테이너에서도 조금 더 엄밀한 설정이 가능하다. 

```yaml
wordpress: 
    depends_on:
      db:
        condition: service_healthy
```

코드 4-2. 

`depends_on.<service_name>.condition` 속성을 통해 의존 서비스의 health check가 healthy, 즉 서비스 준비 상태임을 확인했을 때 다음 서비스를 어떤 조건에서 실행시킬지를 좀 더 엄밀하게 정할 수 있다. 해당 속성에 사용 가능한 속성값들로는 다음과 같이 있다.

- `service_started` : 기본값. 의존하는 컨테이너가 시작되면 실행한다. `depoends_on` 만 사용하는 것과 같은 효과.
- `service_healthy` : 의존 컨테이너의 `healthcheck` 결과가 healthy, 즉 준비 상태라면 그 때 다음 컨테이너를 실행시킨다.
- `service_completed_successfully` : 의존 컨테이너가 정상적으로 종료되었을 때 다음 컨테이너를 실행. 주로 이전 컨테이너가 일회성일 때 사용한다.

위 코드의 경우, `db` 의 `healthcheck` 에 문제가 없는 것을 확인하면 그때서야 `wordpress` 를 실행시키겠다는 뜻이다. 이를 통해 좀 더 엄밀한 컨테이너 실행 순서를 보장할 수 있다. 

이전 실습에서 사용한 `compose.yaml` 에서 위 속성을 추가한 전체 코드는 다음과 같이 된다. 

```yaml
# services: 여러 컨테이너들을 정의. 

services:
  db:
    image: mariadb:latest
    volumes:
      - dbvol:/var/lib/mysql  # Volume mount
    ports:
      - ${DB_PORT}:3306

    # 컨테이너 종료 후 재시작 옵션.
    # unless-stopped => 종료 코드 상관없이 재시작하나, 
    # 사용자가 컨테이너를 정지, 삭제한 경우에는 재시작하지 않음. 
    restart: unless-stopped
    environment:
      MARIADB_ROOT_PASSWORD_FILE: /run/secrets/db_root_password
      MARIADB_USER_FILE: /run/secrets/db_user
      MARIADB_PASSWORD_FILE: /run/secrets/db_password
      MARIADB_DATABASE_FILE: /run/secrets/db_database
    secrets:
      - db_root_password
      - db_user
      - db_password
      - db_database
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 10s
      timeout: 10s
      retries: 5
      start_period: 10s
  
  wordpress:
    # 특정 컨테이너가 시작되고 나서 이 컨테이너가 시작되도록 한다. 
    depends_on:
      db:
        condition: service_healthy
    image: wordpress
    volumes:
      - wordpressvol:/var/www/html
    restart: unless-stopped
    ports:
      - ${WORDPRESS_PORT}:80
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER_FILE: /run/secrets/db_user
      WORDPRESS_DB_PASSWORD_FILE: /run/secrets/db_password
      WORDPRESS_DB_NAME_FILE: /run/secrets/db_database
    secrets:
      - db_user
      - db_password
      - db_database

secrets:
  db_user:
    file: ./secrets/db_user.txt
  db_root_password:
    file: ./secrets/db_root_password.txt
  db_password:
    file: ./secrets/db_password.txt
  db_database:
    file: ./secrets/db_database.txt

volumes:
  dbvol:
  wordpressvol:
  
```

코드 4-3. `compose.yaml`

위 스크립트를 토대로 Docker compose를 실행해보면 다음과 같은 결과를 얻는다.

```bash
$ docker compose up -d
[+] up 5/5
 ✔ Network health_check_default       Created                                                                       0.0s
 ✔ Volume health_check_wordpressvol   Created                                                                       0.0s
 ✔ Volume health_check_dbvol          Created                                                                       0.0s
 ✔ Container health_check-db-1        Healthy                                                                      11.2s
 ✔ Container health_check-wordpress-1 Started                                                                      11.3s
 
$ docker compose ps
NAME                       IMAGE            COMMAND                  SERVICE     CREATED          STATUS                    PORTS
health_check-db-1          mariadb:latest   "docker-entrypoint.s…"   db          17 seconds ago   Up 16 seconds (healthy)   0.0.0.0:3308->3306/tcp, [::]:3308->3306/tcp
health_check-wordpress-1   wordpress        "docker-entrypoint.s…"   wordpress   17 seconds ago   Up 5 seconds              0.0.0.0:8085->80/tcp, [::]:8085->80/tcp

$ docker inspect health_check-db-1
# (생략...)
"State": {
  # (생략...)
  "Health": {
	  "Status": "healthy",
	  "FailingStreak": 0,
	  "Log": [...
	# (생략...)
```

코드 4-4. 이전 실습과의 분리를 위해 필자는 `health_check` 라는 별도의 폴더에서 스크립트 작성 후 실행하였다. 

출력 결과를 자세히 보면 알겠지만, 이전에 `healthcheck` 를 사용하지 않을 때와 출력 결과가 미묘하게 다른 것을 확인할 수 있다. `docker compose up -d` 실행 시 `healthcheck` 대상인 컨테이너의 상태만 `Started` 가 아닌 `Healthy` 로 찍히며, `docker compose ps` 를 실행해봐도 DB 컨테이너의 `STATUS` 컬럼값 옆에 `(healthy)` 가 붙어있는 것을 볼 수 있다. 

---

References

[1] 지은이: 오가사와라 시게타카, 옮긴이: 심효섭, “그림과 실습으로 배우는 도커 & 쿠버네티스“, 위키북스

[2] 지은이: 이길섭, “Docker - 일경험 프로그램”, 위키독스

[Docker - 일경험 프로그램](https://wikidocs.net/book/18040)

[3] Docker docs - Docker compose

[Docker Compose](https://docs.docker.com/compose/)

[4] Docker docs - Docker compose quickstart - docker compose 사용 예시 및 `.dockerignore` 관련 내용 포함.

[Docker Compose Quickstart](https://docs.docker.com/compose/gettingstarted/)

[5] Docker docs - Environment variables in Compose

[Environment variables in Compose](https://docs.docker.com/compose/how-tos/environment-variables/)

[6] Docker docs - Compose file reference

[Compose file reference](https://docs.docker.com/reference/compose-file/)

[7] Docker docs - Overview of installing Docker Compose

[Overview of installing Docker Compose](https://docs.docker.com/compose/install/)

[8] `.dockerignore` 및 이미지 build context 관련 설명

[도커 기초 강좌 #6 .dockerignore와 빌드 컨텍스트 — 캐시 잘 쓰기 \| 스쿨오브웹](https://schoolofweb.net/ko/posts/docker-basics-6/)

[9] Docker compose depends_on, healthcheck 등.

[도커 중급 강좌 #4 compose 심화 — depends_on, healthcheck, profiles \| 스쿨오브웹](https://schoolofweb.net/ko/posts/docker-intermediate-4/)

[10] docker compose 기초 내용

[도커 중급 강좌 #3 docker compose 기초 — web + db 한 파일로 \| 스쿨오브웹](https://schoolofweb.net/ko/posts/docker-intermediate-3/)

[11] Docker docs - Why use Compose?

[Why use Compose?](https://docs.docker.com/compose/intro/features-uses/)

[12] Docker docs - Networking in Compose

[Networking in Compose](https://docs.docker.com/compose/how-tos/networking/)

[13] Docker compose 환경 변수 및 secrets 관리. 

[도커 중급 강좌 #5 환경변수와 secrets 관리 \| 스쿨오브웹](https://schoolofweb.net/ko/posts/docker-intermediate-5/)

---

[^1]: 해당 파일명은 Docker compose에서 기본적으로 인식하는 이름이라고 한다. 물론 다른 파일명으로 사용해도 된다. 그리고 Docker compose도 버전이 오르면서, 예전에는 `docker-compose.yml` 파일명이 기본이었지만 지금은 `compose.yml` 또는 `compose.yaml` 이 기본 이름이라고 한다. 물론 호환성을 위해 `docker-compose.yml` 으로 지어도 잘 작동은 한다고 한다. 필자가 참고하는 자료들이 대부분 예전 버전을 언급하고 있어 이 글에서는 편의상 `docker-compose.yml` 파일명을 기준으로 설명하려고 한다. 

[^2]: 앞서 살펴보았던 Wordpress + MariaDB 실습에서는 그저 운이 좋았기에 이에 대한 문제가 발생하지 않은 것이라고 봐야한다. MariaDB 초기화 작업이 다음 wordpress 컨테이너가 실행되고 DB에 연결 시도를 하기 전에 마쳤기 때문에 운 좋게 정상 작동할 수 있던 것이다. 실제로는 항상 다음 컨테이너가 DB 연결 시도하기 전에 DB 컨테이너의 초기화가 완료된다는 보장이 있는 것은 아니다. 

[^3]: 해당 명령어는 최신 MariaDB에서 사용 가능한 health check 명령어이다. 구버전은 또 다르며, DBMS마다 health check하는 명령어가 다르니 각 DBMS의 설명 및 문서를 참조해야한다.