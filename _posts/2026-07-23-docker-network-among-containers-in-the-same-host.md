---
title: "[Docker] 동일 호스트 내 여러 컨테이너들 간 통신을 위한 연동"
category: "Infra & Cloud"
tag: ["Docker", "Network", "Container", "Bridge", "Bridge network"]
---

이번 글에서는 동일 호스트 내에서 여러 컨테이너들을 도커에서 제공하는 가상 네트워크로 연동하여 운용하는 방법에 대해 살펴보도록 하겠다. 

# 개요

웹 앱 제작, 운용 시에는 웹 서버, WAS, DB 서버 이렇게 최소 3가지 요소들이 필요하다. 이와 비슷하게, Apache, PHP, MySQL, Linux로 대표되는 LAMP[^1] 기술 스택들을 사용하여 웹 앱, 웹 서버를 배포하는 경우도 있다. 이렇듯, 어떠한 서비스를 만들기 위해서는 둘 이상의 여러 소프트웨어들이 필요하다. 그리고 이 소프트웨어들은 서로 상호작용하면서 작동한다. 

이를 도커에서 운영한다고 하면, 일단 관리 편의성을 위해 하나의 컨테이너에 하나의 소프트웨어만을 담고, 각종 컨테이너들을 띄워 운용할 것이다. 이 때에도 각 컨테이너 간 연동이 중요할 것이다. 이를 위해, 도커에서 제공하는 네트워크를 사용한다. 

사실 도커에서 제공하는 네트워크에는 여러 종류가 있다. 다음은 그 중 일부를 간략하게 설명한 것이다.

- bridge: 기본(default) network driver이다. 사용자가 별도로 명시하지 않는 한 bridge network로 자동 생성된다. 하나의 동일한 호스트 내에서 여러 컨테이너들 간의 통신이 필요할 때 사용된다.
- host: 호스트와 컨테이너 간 네트워크 격리를 없애고, 호스트의 네트워크를 직접 사용하는 방식.
- overlay: 보통 여러 대의 물리적 서버들과 그 집합을 논할 때 물리적 서버를 노드(node)라고도 부르는데[^2], overlay network는 여러 노드들에 있는 컨테이너들 간의 네트워킹을 지원한다. 즉, 여러 물리적 서버에 각자 설치되어 있는 Docker daemon들을 네트워크로 연결하여 여러 컨테이너들끼리의 통신을 돕는다. 컨테이너 오케스트레이션 도구 중 하나인 Docker Swarm에서 사용된다.

이 글에서는 동일한 호스트 내에서의 여러 컨테이너들 간의 통신에 대해 다루며, 아직까지는 호스트와 컨테이너 간 네트워크 격리가 필요한 경우를 느끼지 못하고 있기도 하고, 이전까지의 글들과 현재 글에서는 도커의 기본을 다루고 있는 셈이라 이 글에서는 사실상 bridge 네트워크에 대해서만 다룰 것이다. 

# Docker network 관련 명령어

동일 호스트 내에서 여러 컨테이너들 간의 통신을 위해 필요한 network에 관련된 도커 명령어에는 다음과 같이 존재한다. 여기서는 주요 명령어들만 정리하였다. 

- 기본 형태: `docker network <하위 명령어>`

| 하위 커맨드 | 설명 | 사용 예 |
| --- | --- | --- |
| connect | 네트워크에 컨테이너를 접속시킬 때 |  |
| disconnect | 네트워크에서 컨테이너 접속 중단시킬 때 |  |
| create | 네트워크를 새로 생성 |  |
| inspect | 특정 네트워크의 상세 정보 확인 | `docker network inspect <특정 네트워크명>` |
| ls | 네트워크 목록 확인 |  |
| prune | 현재 아무 컨테이너도 접속하지 않은 네트워크를 모두 삭제 |  |
| rm | 특정 네트워크 삭제 | `docker network rm <특정 네트워크명>` |

# 여러 컨테이너들을 네트워크로 연동하기 실습

이 챕터에서는 여러 컨테이너들의 통신 실습을 위해 워드프레스와 MySQL를 조합하여 도커에서 띄워볼 것이다[^3]. 여기서 워드프레스(WordPress)는 오픈소스로 공개된 설치형 블로그이다. 즉, 블로그에 작성할 텍스트 등의 데이터들을 데이터베이스에 저장을 해야 언제든지 블로그 컨텐츠를 워드프레스를 통해 볼 수 있기에 MySQL과 같은 DB도 필수인 것이다. 

각각의 docker image들은 다음과 같이 Docker Hub에서 확인할 수 있다.

![사진 1-1. Docker Hub에 있는 wordpress image. ](/images/2026-07-23/docker-network-among-containers-in-the-same-host/1.png)

사진 1-1. Docker Hub에 있는 wordpress image. [https://hub.docker.com/\_/wordpress](https://hub.docker.com/_/wordpress) 에서 자세한 사항 확인 가능.


![사진 1-2. Docker Hub에 있는 mysql image. ](/images/2026-07-23/docker-network-among-containers-in-the-same-host/2.png)

사진 1-2. Docker Hub에 있는 mysql image. [https://hub.docker.com/\_/mysql](https://hub.docker.com/_/mysql) 에서 자세한 사항 확인 가능. 

먼저 가상 네트워크를 만들겠다. 다음의 명령어를 입력하였다.

```bash
$ docker network create wordpress-net
95e13946d4eec25ab3656682ee1168fcc4c65ccad385e7352377a45e22ac36cf
```

코드 1-1.

여기서는 `wordpress-net` 이라는 이름을 부여하였다. 원하는 이름으로 바꿔도 된다. 해당 네트워크가 잘 생성되었는지 확인하고자 한다면 다음의 명령어를 통해 확인할 수 있다. 

```bash
$ docker network ls
NETWORK ID     NAME            DRIVER    SCOPE
b2a33672fa7b   bridge          bridge    local
3452b1ee2d05   host            host      local
c8c557e6489f   none            null      local
95e13946d4ee   wordpress-net   bridge    local
```

코드 1-2.

잘 보면 `NAME` 속성에 맨 아래 레코드에 앞서 만든 `wordpress-net` 네트워크가 존재함을 볼 수 있다. 

그 다음에는 mysql image를 pull해와 컨테이너를 생성 및 실행해보도록 하겠다. 사실 db에서의 username, password, db name이 필수이기도 하고, wordpress와 연동하기 위해 똑같은 정보를 wordpress 컨테이너 생성 시에도 입력하기 위해 필요하기 때문에 미리 정해두는 것이 좋다. 필자의 경우, 연습이기에 다음과 같이 간단하게 정해보았다. 본인이 원하는 걸로 지어도 된다. 다만 여기서는 연습이기에 간단하게 설정하였지만, 실제 실무에서는 보안을 위해 신경써서 짓고, 보안상 민감한 정보들이므로 외부에 노출되지 않도록 하는 것이 좋겠다. 

| USER | jerowp |
| --- | --- |
| ROOT_PASSWORD | jerorootforwp |
| DATABASE | wordpressdb |
| PASSWORD | jerowppass |

이 정보들을 토대로 mysql 컨테이너를 생성, 실행해보도록 하겠다. 다음과 같은 도커 명령어를 입력하였다.

```bash
docker run --name wp-mysql -dit \
  --net=wordpress-net \
  -e MYSQL_ROOT_PASSWORD=jerorootforwp \
  -e MYSQL_PASSWORD=jerowppass \
  -e MYSQL_USER=jerowp \
  -e MYSQL_DATABASE=wordpressdb \
  mysql \
  --character-set-server=utf8mb4 \
  --collation-server=utf8mb4_unicode_ci
  
```

코드 1-3. 

위 명령어에 대해 각각 정리하면 다음과 같다.

- `\` : 위 명령어는 그 자체로 엄청 길다. 따라서 가독성을 위해 `\` 기호를 통해 개행할 것임을 알리는 것이다. 이렇게 작성하면 긴 명령어도 쉽게 그 구조를 알아볼 수 있도록 할 수 있다.
- `-e` : 환경 변수를 입력할 때 사용하는 옵션. `MYSQL_ROOT_PASSWORD` 등 MySQL에 어떤 환경 변수를 적용할 수 있는지는 Docker Hub의 해당 이미지가 있는 페이지에서 참고할 수 있다.
- `--net` : 앞서 생성한 네트워크를 이 컨테이너와 연결시킨다. 앞서 생성한 네트워크 이름을 입력하면 된다.
- `--character-set-server` : 문자 인코딩 항목으로, 여기서는 `utf8` 을 사용하도록 하였다.
- `--collation-server` : `collation` 은 DB 내 문자열들을 어떻게 비교, 정렬할지에 대한 규칙을 의미한다. 여기서는 정렬 순서를 `utf8` 을 따르도록 하였다.

해당 컨테이너가 잘 생성되어 실행 중인지 확인하려면 역시 `docker ps` 로 확인한다. 

MySQL의 경우, 도커 명령어에 오타를 입력하였다든가 잘못된 인수를 넣었다든가 하는 이유로 실행이 되지 않을 때도 있다. 이 경우, 해당 컨테이너가 생성은 되더라도 실행은 되지 않아 `docker ps -a` 를 입력해보면 `STATUS` 에 `exited` 라고 뜬다. 이 경우 왜 실행이 되지 않았는가를 확인해야할 것인데, 이는 `docker logs <mysql 컨테이너 이름>` 명령어를 통해 확인할 수 있다. 다음은 MySQL 컨테이너가 생성은 되었으나 실행이 되지 않아 그 원인을 파악하기 위해 해당 명령어를 입력한 모습이다.

![사진 1-3. MySQL 컨테이너의 로그를 확인. ](/images/2026-07-23/docker-network-among-containers-in-the-same-host/3.png)

사진 1-3. MySQL 컨테이너의 로그를 확인. 

위 사진의 경우, 옵션 하나에 오타를 내서 제대로 실행되지 않았음을 로그 내용을 통해 확인할 수 있다. 

MySQL 컨테이너가 정상적으로 동작하는 것을 확인하였다면 이번에는 wordpress 컨테이너를 동작시킬 차례이다. 다음과 같은 명령어를 입력하였다.

```bash
docker run --name my-wordpress -dit \
  --net=wordpress-net \
  -p 8080:80 \
  -e WORDPRESS_DB_HOST=wp-mysql \
  -e WORDPRESS_DB_USER=jerowp \
  -e WORDPRESS_DB_PASSWORD=jerowppass \
  -e WORDPRESS_DB_NAME=wordpressdb \
  wordpress
```

코드 1-4. 

워드프레스 컨테이너도 마찬가지로 `--net` 옵션을 통해 앞서 만들었던 네트워크 이름을 명시하여 MySQL과 같은 네트워크에 연결되도록 하였다. `-e` 로 시작되는 워드프레스 환경 변수들 또한 해당 Docker Hub 페이지의 설명을 참조하여 입력하였다. `WORDPRESS_DB_HOST` 의 경우, 앞서 만들었던 MySQL의 컨테이너 이름을 입력하면 된다. 

위 명령어 입력 후 `docker ps` 를 통해 해당 워드프레스 컨테이너도 잘 동작하는지 확인한다. 그 후, 위 명령어에서는 포트 번호 `8080` 으로 설정하였으므로, 웹 브라우저에서 `http://localhost:8080` 으로 들어가본다. 연동이 잘 되었다면 다음과 같은 화면이 뜰 것이다. 

![사진 1-4. 웹 브라우저에서 워드프레스 첫 화면. 연동 성공 시 위와 같은 화면이 보인다.](/images/2026-07-23/docker-network-among-containers-in-the-same-host/4.png)

사진 1-4. 웹 브라우저에서 워드프레스 첫 화면. 연동 성공 시 위와 같은 화면이 보인다.

![사진 1-5. 앞선 사진 1-4에서 언어를 선택하면 위와 같이 정보 설정 화면으로 넘어간다. ](/images/2026-07-23/docker-network-among-containers-in-the-same-host/5.png)

사진 1-5. 앞선 사진 1-4에서 언어를 선택하면 위와 같이 정보 설정 화면으로 넘어간다. 

그렇다면 만약 워드프레스와 MySQL의 연동이 잘 되지 않았다면 어떤 모습이었을까? 앞선 실습대로 해왔다면, 이번에는 일부러 MySQL 컨테이너를 동작 중단해보자. `docker stop wp-mysql` 처럼 말이다. 그러면 웹 브라우저를 새로고침하고 나면 다음과 같은 화면이 뜰 것이다. 

![사진 1-6. 워드프레스 - MySQL 연동 실패 시 웹 화면. ](/images/2026-07-23/docker-network-among-containers-in-the-same-host/6.png)

사진 1-6. 워드프레스 - MySQL 연동 실패 시 웹 화면. 

그러면 위와 같이 데이터베이스 연동에 실패했다는 에러 메시지가 뜬다. 앞서 MySQL 컨테이너 중단 전까지는 위 화면이 보이지 않았다는 것은 두 컨테이너 연동에 성공했다는 뜻이다. 

위와 같은 실습을 마쳤으면 두 컨테이너에 대해 `docker stop` , `docker rm` 명령어를 통해 중단, 삭제하고, 네트워크도 `docker network rm ...` 명령어를 통해 삭제하면 된다. 

# 번외: default bridge network VS user-defined bridge network

앞서 언급했듯, 동일 호스트 내에서 여러 컨테이너들을 연동하여 통신이 가능하게끔 하기 위해 생성, 사용한 network는 bridge라는 가상 네트워크이다. 그런데 이 bridge network에도 두 가지로 나뉜다. 하나는 default bridge network(기본), 또 하나는 user-defined bridge network(사용자 정의)이다. 

default bridge network의 경우 Docker를 실행하자마자 자동으로 생성된다고 한다. 또한, 새로 시작되는 컨테이너는 별도의 네트워크를 지정하지 않는 한 자동으로 default bridge network에 연결된다고 한다. 

user-defined bridge network의 경우 앞선 실습을 통해 생성해봤던 네트워크가 바로 이에 해당한다. `docker network create ...` 를 통해 개발자가 직접 네트워크를 만들면 그것이 user-defined bridge network가 되는 것이다. 

![사진 2-1. 현재 네트워크 목록 확인. 앞선 실습으로 인해 생성된 `wordpress-net` 네트워크도 포함되어 있다. 해당 네트워크의 `DRIVER` 값도 `bridge` 로 되어 있는 것을 보아, 사용자 정의 네트워크는 기본적으로 `bridge` 로 생성됨을 확인할 수 있다. 한 편, 위 사진의 첫 번째 줄에 있는 `NAME` 이 `bridge` 인 네트워크는 기본으로 생성되는 default bridge network이다. ](/images/2026-07-23/docker-network-among-containers-in-the-same-host/7.png)

사진 2-1. 현재 네트워크 목록 확인. 앞선 실습으로 인해 생성된 `wordpress-net` 네트워크도 포함되어 있다. 해당 네트워크의 `DRIVER` 값도 `bridge` 로 되어 있는 것을 보아, 사용자 정의 네트워크는 기본적으로 `bridge` 로 생성됨을 확인할 수 있다. 한 편, 위 사진의 첫 번째 줄에 있는 `NAME` 이 `bridge` 인 네트워크는 기본으로 생성되는 default bridge network이다. 

`docker network prune` 명령어는 현재 그 어떤 컨테이너에도 연결되어 있지 않은 네트워크들을 모두 삭제하는 기능을 가진다. 위 사진 2-1 상태에서 해당 명령어를 입력해보면 다음과 같은 결과를 얻는다.

![사진 2-2. `docker network prune` 명령어를 통해 사용되지 않는 네트워크들을 모두 삭제한 모습.](/images/2026-07-23/docker-network-among-containers-in-the-same-host/8.png)

사진 2-2. `docker network prune` 명령어를 통해 사용되지 않는 네트워크들을 모두 삭제한 모습.

위 사진을 보면 알 수 있듯, 아무런 컨테이너도 작동하지 않은 상태에서 해당 명령어를 입력했을 때, 이전에 만들었던 `wordpress-net` 이란 이름의 네트워크만 삭제되는 것을 볼 수 있다. `bridge` 라는 이름의 네트워크는 앞선 `wordpress-net` 과 같은 종류의 네트워크임에도 삭제되지 않았다. 이를 미루어보았을 때, `docker network prune` 은 user-defined bridge에 대해서만 삭제를 수행하고, default bridge에 대해선 삭제를 진행하지 않는 명령어인 것으로 보인다. 

Docker 공식 문서에 따르면, default bridge보다는 되도록 user-defined bridge network를 사용하는 것을 권고하고 있다. 그 이유는 다음과 같다.

- User-defined bridge의 경우, 자동으로 컨테이너 간 DNS를 제공한다. 여기서 DNS란 Domain Name System의 약자로, `127.0.0.1` 과 같이 숫자로 표현되는 IP 주소를 `http://myserver.com` 과 같이 사람이 쉽게 인식할 수 있는 domain name으로 변환하거나 또는 그 반대로 변환하는 것을 말한다. 여기서 말하는 DNS는 쉽게 말해, 컨테이너에도 각각 IP 주소가 부여되어 있는데, DNS를 통해 특정 컨테이너를 IP 주소로 특정하지 않고 이름으로 특정할 수 있다는 뜻이다. Default bridge의 경우 여기에 연결된 컨테이너들은 서로를 지칭할 때 IP 주소로밖에 지정할 수 없다. 따라서 사람의 입장에서는 숫자로만 구성된 IP로만 특정 컨테이너를 지칭하기가 어려울 것이다. 그러나 user-defined bridge에서는 이 네트워크에 연결된 컨테이너들을 미리 지정한 이름(`--name` 으로 지정한 컨테이너 이름)으로 지칭할 수 있어 사람 입장에서 더 편리하다.
- Default bridge의 경우, 새로 생성되는 컨테이너가 `--net` 등을 통해 별도의 네트워크에 연결하도록 지정되지 않았다면 자동으로 default bridge에 연결되는데, 이로 인해 원치 않은 컨테이너들끼리 네트워크로 연동된다. 반면 user-defined bridge를 이용하면 원하는 컨테이너들로만 네트워크로 연동시킬 수 있어 컨테이너 간 네트워크 격리에 용이하다.
- User-defined bridge를 사용하면 컨테이너가 실행 중인 상태에서도 특정 네트워크에 연결 또는 연결 해제시킬 수도 있다. 반면 default bridge의 경우, 여기에 연결된 컨테이너를 연결 해제하거나 반대로 컨테이너를 해당 네트워크에 연결하고자 할 경우, 먼저 해당 컨테이너를 작동 중단 시킨 다음에 연결해야한다는 번거로움이 있다.

앞선 실습을 통해 보았듯이, user-defined bridge network를 생성하고 컨테이너들을 연결하는게 그리 어려운 일은 아니기에 되도록 네트워크를 직접 생성하여 컨테이너들을 연동하는 것이 좋겠다. 

---

References 

[1] 지은이: 오가사와라 시게타카, 옮긴이: 심효섭, “그림과 실습으로 배우는 도커 & 쿠버네티스“, 위키북스

[2] Docker docs - CLI references

[docker](https://docs.docker.com/reference/cli/docker/)

[3] Docker docs - network drivers

[Network drivers](https://docs.docker.com/engine/network/drivers/)

[4] Docker docs - bridge network

[Bridge network driver](https://docs.docker.com/engine/network/drivers/bridge/#differences-between-user-defined-bridges-and-the-default-bridge)

[5] [나무위키 - 워드프레스](https://namu.wiki/w/%EC%9B%8C%EB%93%9C%ED%94%84%EB%A0%88%EC%8A%A4)

[6] 워드프레스 공식 홈페이지

[마음대로 만들어나가는 WordPress](https://wordpress.com/ko/)

[7] DNS 개념

[🌐 DNS 개념 & 동작 ★ 알기 쉽게 정리](https://inpa.tistory.com/entry/WEB-%F0%9F%8C%90-DNS-%EA%B0%9C%EB%85%90-%EB%8F%99%EC%9E%91-%EC%99%84%EB%B2%BD-%EC%9D%B4%ED%95%B4-%E2%98%85-%EC%95%8C%EA%B8%B0-%EC%89%BD%EA%B2%8C-%EC%A0%95%EB%A6%AC)

---

[^1]: 각 기술들의 앞 글자를 딴 이름. 필자가 임의대로 이렇게 부르는게 아니라, 실제로 이렇게 불린다. 다만 웹서버, WAS 및 이를 구현하기 위한 프로그래밍 언어, 운영체제라는 큰 틀에서는 Apache 대신 NginX를, PHP 대신 자바, 파이썬 등으로 교체해도 똑같이 동작할 것이다. 이렇게 되면 LAMP는 아니게 되겠지만. 

[^2]: 여러 물리적 서버들을 하나의 집합으로 묶은 서버 클러스터에서 여러 물리적 서버들이 네트워크로 연결되어 있는 모습이 그래프 이론의 그래프와 동일해서 이런 이름이 붙여진 것이 아닐까 생각된다. 물리적 서버가 노드, 이 서버들을 잇는 네트워크가 간선이 되는 셈이다. 

[^3]: 여기서는 두 컨테이너들이 network로 연결되어 통신이 가능한 상태까지만 만들어보도록 하겠다. 나중에 예제로 만든 프론트엔드 프로젝트, 백엔드 프로젝트를 이용하여 웹서버, WAS, DB 이 세 개를 동시에 띄워 실제로 잘 작동하여 컨테이너들 간의 통신에 문제가 없는지까지 살펴볼 예정이다.