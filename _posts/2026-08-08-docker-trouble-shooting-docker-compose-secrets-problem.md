---
title: "[Docker][Trouble shooting] Docker compose secrets 문제"
category: "Infra & Cloud"
tag: ["Docker", "Docker Compose", "Secrets", "Mount", "WSL", "WSL 2", "Docker Desktop"]
---

“[**[Docker] Docker compose를 이용하여 여러 컨테이너들을 손쉽게 실행해보자!**](/infra%20&%20cloud/docker-compose/)” 글의 “**환경 변수(environment variables) 및 민감 정보(secrets) 별도로 관리하기**” 챕터에 나오는 secrets 실습을 하다가 오류를 발견한 적이 있어 이 글에 작성하고자 한다. 해당 글에 같이 쓰기엔 이미 글 내용이 길어진 상태이기도 하고, 이번 트러블 슈팅 글 자체도 길게 느껴질 수 있어 이렇게 별도의 글로 작성한다. 

# 문제

secrets 실습을 진행하기 위해 리눅스 쉘에서 Wordpress + MariaDB로 구성된 Docker compose를 실행하였다. 실행 자체에는 문제가 없었으나 웹 브라우저에서는 데이터베이스 연결에 오류가 있다는 메시지가 떴다. `docker compose ps` 로 살펴보니 MariaDB에서만 “restart…”라는 상태값이 떴었다. `docker compose logs db` 를 통해 해당 DB 컨테이너에 대해서만 로그를 출력해보니 다음과 같은 메시지를 발견했다. 

```bash
Database is uninitialized and password option is not specified
You need to specify one of MARIADB_ROOT_PASSWORD,
MARIADB_ROOT_PASSWORD_HASH, MARIADB_ALLOW_EMPTY_ROOT_PASSWORD
and MARIADB_RANDOM_ROOT_PASSWORD
```

코드 1-1. 

이 당시 프로젝트 폴더 구조는 다음과 같았으며

```
/using-secrets  (프로젝트 폴더)
|   .env
|   compose.yaml
|   
\---secrets
        db_database.txt
        db_password.txt
        db_root_password.txt
        db_user.txt
```

코드 1-2. 

`compose.yaml` 파일 내용은 다음과 같았다.

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

코드 1-3. `compose.yaml` 

이 secrete 실습 이전에는 `.env` 파일을 만들고 그 안에 작성한 환경변수 값들이 `compose.yaml` 파일에 주입이 되는지를 실습했었는데, 이 당시에는 Docker compose가 문제 없이 동작하여 웹 브라우저에서도 워드프레스에 쉽게 접속할 수 있었다. 그래서 secrets에 문제의 원인이 있겠다는 걸 짐작하긴 했었다. 

# 배경

그 당시 필자가 사용하던 여러 기술들의 정보는 다음과 같다.

- Host OS: Windows 11
- WSL 2 및 Docker Desktop을 이용하여 Docker 사용
- Docker
    - Docker client version: 29.6.1
    - Docker server(Docker Desktop 4.81.0) - Engine version: 29.6.1
- Linux distro: Ubuntu 26.04 LTS
- Docker compose version: v5.2.0

# 해결 과정

이 문제의 경우, 처음에는 구글링을 통해 문제의 원인과 해결책을 찾으려고 했지만, 생각보다 관련 정보가 없어서 어려움을 겪었다. 그래서 어쩔 수 없이 AI에게 질문하며 해결책을 찾으려고 하였다. 

처음에는 `compose.yaml` 파일 자체에 어떠한 오타, 경로 주소에 오타, 잘못된 값 사용 등의 문제가 아닐까 싶었지만 AI도 이에 대해선 문제가 없다고 하였다. 

ChatGPT에 의하면 먼저 다음의 명령어를 수행한 결과를 자신에게 보여달라고 하였다. 이 명령어는 DB를 실행시키진 않고 DB 컨테이너에 secrets 파일이 전달될 수 있는지만 확인하는 명령어라고 한다.

```bash
docker compose run --rm --no-deps --entrypoint sh db -c 'set -eu; echo "FILE=$MARIADB_ROOT_PASSWORD_FILE"; test -s "$MARIADB_ROOT_PASSWORD_FILE"; echo SECRET_MOUNT_OK'
```

코드 2-1.

만약 정상적으로 동작한다면 다음과 같은 출력 결과가 나와야 한다고 하였다. 

```bash
FILE=/run/secrets/db_root_password
SECRET_MOUNT_OK
```

코드 2-2.

그런데 이 명령어를 필자가 직접 실행해보니 전혀 다른 결과가 나왔다.

```bash
Error response from daemon: mounting /mnt/c/docker/compose_ex/using-secrets/secrets/db_root_password.txt to /mnt/wsl/docker-desktop-bind-mounts/Ubuntu/aa89dab328a6685978d784c176dc3e983afaef5acfdd69f3f2dc1021644f57f9: not a directory
```

코드 2-3. 

이 메시지를 보고 ChatGPT는 이 문제가 애초에 Windows쪽 파일이 WSL의 Ubuntu로 마운트하는 과정에서 실패가 났기에 secrets 파일도 제대로 DB 컨테이너에 전달되지 않아서 발생한 문제라고 분석하였다. 그래서 해당 AI는 `wsl --update` 명령어를 통해 WSL 자체를 업데이트 할 것을 권고하였다. 

필자는 이에 따라 우선 Docker desktop과 실행 중이던 WSL를 종료하고, 해당 명령어를 통해 업데이트하였다. 그 후 다시 Docker destkop, WSL을 실행시킨 후, 다시 Docker compose를 실행시켜보았다. 그 결과, 이번에는 DB 컨테이너가 정상 작동하면서 워드프레스와 잘 연결이 되었다. 

사실 이전의 `.env` 파일을 가지고 하는 실습에서도 그렇고, 그 동안의 bind mount 기반 실습에서 단 한 번도 이러한 문제가 발생한 적이 없었다. 그런데 유독 secrets 기반 실습에서만 이러한 문제가 나타났다. Docker secrets 사용을 위한 텍스트 파일들은 컨테이너 내부의 `/run/secrets/...` 경로로 자동 마운트된다고 하는데, 그 전 과정에서 WSL의 Windows 파일 시스템과의 마운트 과정에서 어떠한 문제가 발생한 것이 원인이 된 것 같다. 따라서 앞으로 Docker secrets 관련 작업을 할 때에는 안정적인 작업을 위해 처음부터 `wsl --update` 를 하든가, 아니면 Windows에 있는 프로젝트 폴더를 아예 리눅스 사용자 공간에 별도로 복사하여 마운트 문제 자체를 피하든가 하는 방식을 취해야 할지도 모르겠다.