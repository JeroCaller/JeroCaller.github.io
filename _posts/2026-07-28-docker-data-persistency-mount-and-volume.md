---
title: "[Docker] 데이터 영구 저장하기 - Mount와 Volume"
category: "Infra & Cloud"
tag: ["Docker", "Volume", "Mount", "Bind", "Volume mount", "Bind mount", "Storage", "Data", "Data persistency", "Backup"]
---

Docker에서 컨테이너는 여러 가지 이유로 자주 삭제되는 경우가 많다고 한다. 개발 및 테스트 환경, 운영 중 이상이 발생하여 새 컨테이너를 생성해야햐는 경우 등이 그 예시가 되겠다. 이로 인해, 컨테이너가 삭제될 때 그 안에 있던 파일이나 데이터들도 같이 삭제된다. 이미지 자체에 해당 파일이나 데이터들도 같이 포함되어 있지 않은 한, 컨테이너 삭제로 인해 같이 삭제된 파일 및 데이터들도 복구할 길이 없을 것이다. 

따라서 이러한 일을 방지하기 위해, 즉 컨테이너의 삭제와는 상관없이 데이터를 영구적으로 보존하여 data persistency를 확보하고자 한다면 데이터는 별도의 storage 영역에 저장하는 방식이 필요할 것이다. 이러한 방식에서는 기존 컨테이너를 삭제하더라도 데이터를 영구적으로 보존할 수 있고, 이렇게 보존된 데이터를 새 컨테이너와 연결하여 그대로 사용할 수 있을 것이다. 

이번 글에서는 Docker container의 삭제와는 별도로 데이터를 영구적으로 보존하고 이를 컨테이너에 연결하는 방법에 대해 서술하겠다. 

# 데이터를 영구적으로 저장하는 방법 - Bind mount VS Volume mount

Docker에서는 데이터를 영구적으로 보존하는 방식으로 bind mount 방식과 volume mount 방식이 있다. 

우선 여기서 말하는 mount라는 것은, 외부의 저장 장치 및 파일 시스템을 기존 파일 시스템의 특정 경로(directory)에 연결하는 것을 의미한다. 노트북에 USB 저장 장치를 연결하여 작업할 수 있도록 하는 것도 mount라 볼 수 있다. `connect` 라는 단어는 주로 통신에서 많이 쓰인다면 `mount` 는 주로 외부 저장장치, 파일 시스템 간 연결에 많이 쓰이는 것으로 보인다. 

Volume mount의 경우, volume이라고 하는 Docker에서 생성, 관리하는 별도의 격리된 데이터 저장 공간을 만들고, 이를 컨테이너와 연결하여 사용하는 방식이다. Volume mount의 특징으로는 다음과 같다.

1. Volume이라는 격리된 데이터 저장소도 어찌되었든 host 기기에 저장되는 것은 맞지만, volume 외부에서 직접적으로 volume에 접근할 수는 없고, 오로지 컨테이너를 이용해서만 volume 내부에 접근 가능하다.
2. Docker를 통해서만 volume의 생성, 삭제, 관리 등의 작업을 수행할 수 있다. 
3. Container와는 별개의 존재이므로, container가 삭제되더라도 volume 안에 저장된 데이터들은 영구적으로 보존되어서 새 container로 mount하여 지속적인 작업을 할 수 있게 된다. 
4. 컨테이너에 직접 데이터를 저장하고 읽어오는 것보다 volume에 데이터를 저장하는 것이 파일 입출력 속도 면에서 더 낫다고 한다[^1]. 
5. 빈 volume에 특정 컨테이너를 처음 연결하는 경우, 기본적으로 해당 컨테이너와 마운트된 경로(파일 및 디렉토리)가 그대로 volume 내부에 복사된다. 예를 들어 apache 웹 서버인 `httpd` 에서  `index.html` 파일을 비롯한 정적 웹 페이지들이 저장되어 있는 `/usr/local/apache2/htdocs/` 경로를 빈 volume에 마운트한 경우, 해당 경로 및 그 안의 파일들이 고스란히 volume에 복사되어 저장된다. 
    1. 빈 volume에 마운트된 컨테이너 내 특정 경로 및 파일들이 해당 volume에 기본적으로 복사되지 않도록 하고자 한다면 docker 명령어 작성을 통해 volume 마운트 시 명령어에 `volume-nocopy` 옵션을 추가하면 된다고 한다. 
6. 이미 파일 및 디렉토리가 저장된 volume에 특정 컨테이너를 연결하는 경우, 컨테이너 안에 있던 같은 경로의 파일들은 접근할 수 없게 되고, volume에 있던 경로의 파일들만 접근할 수 있게 된다. 그렇다고 해서 volume에 있던 파일들이 컨테이너 내부의 파일에 덮어씌워진 것은 아니지만, 컨테이너 내부의 파일에 접근하고자 한다면 그 어떤 volume에도 마운트되지 않은 새 컨테이너로 새로 만드는 수밖에 없다. 

Bind mount는 volume mount와 달리 volume과 같은 도커에서 제공하는 저장 공간을 사용하지 않고, 호스트 기기의 파일 및 디렉토리를 바로 컨테이너에 마운트하는 개념이다. 이 방식의 특징은 다음과 같다.

1. 호스트에 있는 파일 및 디렉터리를 바로 컨테이너에 마운트하는 방식이기에 볼륨과는 달리 도커가 이 저장 공간을 생성, 관리하지 않는다.
2. 마운트되는 파일 및 디렉터리는 볼륨 마운트에서는 호스트에서 직접 접근 및 조작이 불가능했지만, bind mount에서는 호스트에서 직접 접근 및 조작이 가능하다. 
3. Bind mount는 엄연히 말하면 Docker daemon이 있는 host(server)에서 가능하고, client에서는 불가능하다. 따라서 Docker daemon이 설치된 서버를 원격으로 조종하는 경우, 클라이언트에 있는 파일을 서버에 bind mount할 수는 없다. 
4. Docker Desktop을 이용하는 경우(여기서는 윈도우즈 운영체제에서 WSL 2를 통해 가동한다고 가정), 윈도우즈 운영체제의 파일 시스템과 Linux VM에 자동으로 마운트되는 시스템이 갖춰져 있다. 윈도우즈의 C, D 드라이브를 Linux VM에서는 `/mnt/c` , `/mnt/d` 와 같은 경로로 접근 가능한 것처럼. 따라서 이 경우에 bind mount도 가능하다. 
5. Volume mount와 마찬가지로, 마운트하고자 하는 경로가 컨테이너 내부에도 존재할 경우, 컨테이너 내부에 존재하던 하위 디렉토리 및 파일에는 접근하지 못하고 bind mount된 호스트의 해당 경로 및 파일들에만 접근 가능하다. 만약 컨테이너 내부의 해당 경로에 있는 하위 디렉토리 및 파일들에 접근하고자 한다면 mount가 되지 않은 새 컨테이너를 생성해서 다루는 방법 밖에 없다. 

<div class="single-image">
  <img width="60%" src="/images/2026-07-28/docker-data-persistency-mount-and-volume/1.png" alt="image">
  <p>그림 1-1. Volume mount와 bind mount의 차이점을 묘사한 그림.</p>
</div>

Volume mount와 bind mount를 구별하는 핵심은, 저장 공간을 Docker에서 생성, 관리하고, 컨테이너로만 접근할 수 있는지 아니면 호스트에서 바로 접근할 수 있느냐로 보면 되겠다. 

이러한 volume과 bind mount의 각각의 특징들로 인해, 각각 언제 어떤 마운트 방식을 써야 할지도 달라진다. 

다음은 volume mount를 사용하면 좋은 경우들이다.

- Bind mount의 경우, 마운트된 디렉토리 및 파일들을 Docker container뿐만 아니라 호스트에서도 접근할 수 있기에 저장 공간 측면에서는 Docker container - 호스트 간 완전한 격리가 되지 않는다고 볼 수 있고, 이로 인해 예기치 않은 데이터 오염 및 보안 문제가 발생할 수도 있다. 또한, bind mount를 사용하는 경우 마운트되는 호스트의 디렉터리 구조에 의존하므로, 다른 호스트 기기에서 다른 디렉터리 구조를 가지면 마운트가 제대로 되지 않아 호스트 환경에 독립적으로 서비스를 할 수 없게 된다. 따라서 데이터 오염 방지, 보안, 호스트 환경과의 독립성 유지가 필요한 경우에는 Volume mount를 사용하는 것이 좋다.
    - 컨테이너로부터 저장 공간에 접근하여 데이터를 조작하는 것을 원치 않으나, 어떠한 이유로 인해 bind mount를 꼭 써야 하는 경우, 컨테이너 생성 시 `readonly`  로 설정하여 데이터를 오로지 읽기 전용으로만 접근하도록 하는 방법도 있다. 이 경우 컨테이너 생성 시 `docker run -v <호스트 경로>:<컨테이너 내 경로>:readonly` 와 같이 `readonly` 또는 줄여서 `ro` 옵션을 주면 된다.
- 컨테이너에 직접 데이터를 저장하는 것보다 더 빠른 디스크 I/O 속도를 원할 경우.
- Volume의 경우 리눅스와 윈도우즈 컨테이너 모두에 동작 가능하므로, 서로 다른 운영체제가 설치된 컨테이너들도 동작 가능하도록 하고자 할 경우.
- 저장되는 파일 및 디렉터리 위치 관리를 개발자가 일일히 신경쓰지 않고 도커에게 맡기고자 할 경우.
- 서버와 클라이언트가 물리적으로 분리되어 원격으로 조종해야하는 경우, 클라이언트에 있는 파일을 서버에 bind mount할 수는 없다. 따라서 이 상황에서는 데이터 영속성을 위해 서버 자체에서 volume mount를 사용하면 된다.

다음은 bind mount를 사용하면 좋은 경우들이다.

- 호스트에서 컨테이너로, 또는 그 반대 방향으로 빠르게 파일들을 주고 받아야할 때. 예를 들면, 소스 코드 또는 그로부터 빌드된 바이너리를 호스트에서 컨테이너로 직접 전달하여 빠른 테스트를 하고자 하는 경우.
- Volume의 경우 저장된 파일, 데이터들에 접근하려면 오로지 컨테이너를 통해서만 접근해야해서 조금 번거롭고 불편하다. 따라서 백업이 빠르고 원활하게 되어야 하는 경우, 또는 특정 파일 및 데이터를 컨테이너 뿐만 아니라 호스트에서도 접근, 조작이 필요한 경우에 bind mount를 쓰는 것이 더 좋을 것이다.

한 편, Volume 사용 시 유념해두면 좋을 유의사항으로는 다음과 같이 있을 수 있겠다. 

- 예를 들어 `httpd` 컨테이너와 빈 volume을 mount하여 해당 volume에 `/usr/local/apache2/htdocs/` 폴더에 `index.html` 이 복사되었다고 해보자. 그리고 해당 HTML 파일에는 “음식”에 관한 내용이 들어있다고 가정해보겠다. 이제 같은 `httpd` 이미지로부터 생성된 새 컨테이너를 해당 volume에 mount한다고 해보자. 그러면 해당 컨테이너에서도 “음식” 내용이 든 웹 페이지가 브라우저에 보이게 된다. 즉, 컨테이너에 있는 파일, 디렉터리보다 볼륨에 있는 파일, 디렉터리가 우선적으로 보인다는 점, 그리고 둘 이상의 컨테이너가 같은 볼륨에 연결되어 있다는 점 때문에 두 웹 서버에서 동일한 웹 페이지가 보이게 된다. 볼륨에 저장된 파일, 데이터가 공유된다는 것이다. 여기서 컨테이너를 통해 어떻게든 볼륨 내 파일을 조작한다면 그 내용이 두 웹 서버의 화면에 똑같이 반영될 것이다. 따라서 만약 두 웹 서버가 서로 독립적으로 운영되도록 하고자 한다면 각자의 볼륨을 따로 생성해서 마운트시키는 게 더 좋을 것이다.
- `httpd` 와 `MySQL` 처럼 서로 다른 이미지로부터 생성된 컨테이너들이라 할지라도, 그래서 같은 볼륨에 마운트 시켜도 마운트 경로가 겹치지 않아 문제가 생기지 않는다 하더라도 되도록 특별한 이유가 없는 한, 각자의 컨테이너에 대해 서로 독립적인 볼륨들을 생성, 마운트하는 것이 더 좋을 것이다. 이는 컨테이너에 둘 이상의 프로그램을 넣지 않는 이유와 비슷하다고 보면 되겠다. 각 컨테이너 별로 독립적인 데이터 운용 및 관리를 위해서다.

## 관련 명령어

Bind mount의 경우 컨테이너 생성 후 마운트하기 전에 미리 마운트할 디렉터리 또는 파일들을 마련한다. Volume mount의 경우, 컨테이너를 생성하는 명령어를 작성할 때 볼륨 마운트 옵션도 같이 명시하면 비록 해당 볼륨이 존재하지 않더라도 자동으로 생성된다. 다만 이러한 경우는 컨테이너 내 특정 경로의 파일 및 디렉터리들을 미리 빈 볼륨에 복사하여 세팅하고자 할 때를 제외하면 웬만해서는 볼륨을 먼저 생성한 후에 컨테이너를 마운트시키는 게 권장되고 있다. 

Volume mount의 경우 `docker volume` 으로 시작하는 명령어를 통해 volume을 생성, 삭제할 수 있다. 관련 주요 하위 명령어로는 다음과 같이 존재한다.

| 하위 명령어 | 설명 | 사용 예시 |
| --- | --- | --- |
| create | 새 볼륨 생성 | `docker volume create <볼륨이름>` |
| inspect | 특정 볼륨의 세부 정보 확인 | `docker volume inspect <볼륨 이름>` |
| ls | 현재 볼륨의 목록 확인 | `docker volume ls` |
| prune | 마운트되지 않은 모든 볼륨 삭제 | `docker volume prune` |
| rm | 특정 볼륨 삭제 | `docker volume rm <볼륨 이름>` |

컨테이너를 생성, 실행하면서 동시에 storage를 마운트하는 명령어 구조는 다음과 같다. 

```bash
# bind mount
docker run -v <host-path>:<container-mount-path>[:option]

# volume mount
docker run -v <volume-name>:<container-mount-path>[:option]
```

코드 1-1.

Bind mount는 Volume mount와는 다르지만 명령어에서는 똑같이 `-v` 옵션을 사용하여 컨테이너와 마운트한다. 

위와 같이 콜론 기호(`:`)를 기준으로 왼쪽에는 실제 마운트할 볼륨 또는 bind mount할 호스트 경로를, 오른쪽에는 컨테이너의 마운트 경로를 지정하면 된다. 

`[:option]` 의 경우 생략 가능하며, 필요한 경우 마운트 경로 지정한 뒤 그 뒤에 덧붙여서 사용할 수 있다. 앞서 소개했듯 컨테이너가 storage에 읽기 전용으로만 접근시키고자 할 경우 `docker run -v <host-path>:<container-mount-path>:ro` 와 같이 지정할 수 있다. 

한 편, 위 명령어에서는 `-v` 즉, `--volume`  옵션을 이용한 마운트 방법을 소개하였는데, `--mount` 옵션을 이용해서도 마운트할 수 있다. 

```bash
# bind mount
docker run --mount type=bind,src=<host-path>,dst=<container-mount-path>[,<key>=<value>]

# bind mount 명령어 예시. 아래 명령어는 호스트에서 현재 경로를 컨테이너의 `/app` 경로와 마운트 시키고, 읽기 전용으로만 데이터에 접근하도록 한다.
ex) docker run --mount type=bind,src=.,dst=/app,ro

# volume mount
docker run --mount type=volume,src=<volume-name>,dst=<container-mount-path>[,<key>=<value>]
```

코드 1-2. 

`--mount` 를 이용한 옵션에서는 volume인지 bind인지, source는 어디고 마운트할 경로는 어딘지 등을 좀 더 명확하게 지정할 수 있다. 또한 `-v` 옵션에 비해 `--mount` 옵션을 사용하면 조금 더 다양한 옵션들을 사용할 수 있어, 상세한 옵션 지정이 필요할 경우에 사용하면 되겠다. 다만 상세한 옵션 지정이 필요하지 않는 경우, 명령어를 좀 더 간단하게 쓰고자 하는 경우에는 `-v` 옵션을 그대로 사용해도 될 것이다. `--mount` 와 `-v` 옵션에서 각각 사용 가능한 세부 옵션들은 다음의 docker docs 공식 사이트를 참고.

- [https://docs.docker.com/engine/storage/volumes/#options-for---mount](https://docs.docker.com/engine/storage/volumes/#options-for---mount)
- [https://docs.docker.com/engine/storage/bind-mounts/#options-for---mount](https://docs.docker.com/engine/storage/bind-mounts/#options-for---mount)

한 편, 옵션 사용 시 각 속성 구분은 쉼표(`,`)로만 하며, 그 뒤에 띄어쓰기를 하지 않도록 주의. 

# 웹 서버를 이용한 마운트 실습

이 챕터에서는 구체적으로 도커에서 데이터 영속성을 위해 바인드 마운트 및 볼륨 마운트를 하는 방법에 대해 살펴보도록 하겠다. 여기서는 Apache 웹 서버인 `httpd` 를 상대로 진행하며, Windows 11 운영체제에서 WSL 2, Ubuntu, Docker Desktop을 이용하여 실습하였다. 

이 챕터에서는 웹 서버에 커스텀한 HTML 파일을 마운트 시켜 웹 화면에 띄울 수 있도록 하는 것을 목표로 한다. 

## 바인드 마운트 방식

일단 혹시라도 실행 중인 컨테이너가 있다면 원활한 실습을 위해 중단 및 삭제한다. 

필자의 경우, Windows에서 `C:\docker\bind` 라는 폴더를 미리 마련하였고, 이를 다음과 같이 bind mount하였다. 

```bash
docker run --name my-web-server -d \
  -p 8080:80 \
  -v /mnt/c/docker/bind:/usr/local/apache2/htdocs httpd
```

코드 2-1. 

웹 브라우저 URL에 `http://localhost:8080` 을 입력한다. 

<div class="single-image">
  <img width="40%" src="/images/2026-07-28/docker-data-persistency-mount-and-volume/2.png" alt="image">
  <p>사진 2-1. httpd를 빈 폴더와 bind mount한 후 웹 브라우저의 화면</p>
</div>

현재 bind mount된 폴더에는 아무것도 넣지 않아서 위와 같은 화면이 뜬다. 이제 해당 폴더에 다음과 같은 간단한 `index.html` 파일을 만들었다. 

```html
<!DOCTYPE html>
<html lang="ko">
<head>
  <meta charset="UTF-8">
  <title>IT/소프트웨어 정보 사이트</title>
</head>
<body>
  <h2>IT/소프트웨어 정보 사이트에 오신 것을 환영합니다.</h2>
</body>
</html>
```

코드 2-2. `index.html` 

그리고 아까 켜 둔 웹 브라우저를 새로고침하면 다음과 같이 화면이 바뀐다.

<div class="single-image">
  <img width="60%" src="/images/2026-07-28/docker-data-persistency-mount-and-volume/3.png" alt="image">
  <p>사진 2-2. </p>
</div>

`httpd` 의 경우, 만약 mount도 되지 않고 아무것도 연결되지 않은 컨테이너 상태에서 웹 화면을 본다면 “it works!”라는 화면만 보인다. 이를 통해 알 수 있는 사실은, 위 실습에서 bind mount가 잘 되었음을 확인할 수 있으며, 기존 컨테이너의 `/usr/local/apache2/htdocs` 디렉토리 내에 있는 파일은 접근할 수 없게 되고, bind된 폴더 내 파일에만 접근할 수 있는 상태가 되었기에 위와 같이 우리가 커스텀한 파일 내용이 보여지게 된다는 점이다. 

## 볼륨 마운트 방식

역시나 이전에 작동하고 있는 컨테이너가 있다면 중단, 삭제하고 다음과 같은 절차로 진행한다. 

볼륨에 접근하는 방법은 컨테이너를 통해 접근하는 방법밖에 없다. 호스트에서 직접 바로 접근할 수는 없다. 그래서 일단 생성한 볼륨을 컨테이너와 연결한 후, 컨테이너에 대해 호스트에서 만든 `index.html` 파일을 `docker cp` 명령어를 통해 전달한다. 그러면 볼륨에도 똑같은 파일이 저장될 것이다. 이후, 해당 컨테이너는 중단시키고 새 컨테이너를 해당 볼륨과 마운트시킨다. 이 때에도 호스트에서 전달한 `index.html` 가 웹 화면에 보이면 볼륨 마운트 및 파일 저장이 원활하게 된다고 볼 수 있다. 이 점을 확인해보고자 한다. 

먼저 볼륨을 하나 생성한다. 다음과 같이 생성하였다. 

```bash
docker volume create my-web-server-vol
```

코드 3-1.

앞서 생성 시도한 볼륨이 정말 잘 생성되었는지 확인하려면 `docker volume ls` 를 이용한다.

```bash
$ docker volume ls
DRIVER    VOLUME NAME
local     2a7551cd54b3888f63e26bcd9bf9e52eab620aa79de41cd12d5a181c0b56e0c8
local     51c3fff2745b2deb9e874d0f79a8f24b5db63aab0bf21064daf11b0f5c5c3ee7
local     b480b3960789d0ec9e6bf9b7dade3d9c6ccecaf240f52f12b8e54b9d4912d7c3
local     my-web-server-vol
```

코드 3-2.

볼륨의 상세 정보를 보고자 한다면 `docker volume inspect <볼륨명>` 명령어를 이용한다. 

```bash
$ docker volume inspect my-web-server-vol
[
    {
        "CreatedAt": "2026-07-27T10:15:51Z",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/my-web-server-vol/_data",
        "Name": "my-web-server-vol",
        "Options": null,
        "Scope": "local"
    }
]
```

코드 3-3.

해당 볼륨이 잘 생성되었다면 이제 바로 `httpd` 컨테이너를 생성과 동시에 해당 볼륨에 마운트 시킨다. 

```bash
docker run --name my-web-server -d \
  -p 8080:80 \
  -v my-web-server-vol:/usr/local/apache2/htdocs \
  httpd
```

코드 3-4.

웹 브라우저에서 확인해보면 기본 화면인 `It works!" 가 보일 것이다. 

이제 이 상태에서 앞서 만든 `index.html` 파일을 호스트에서 컨테이너로 건넬 것이다. 아까 “바인드 마운트 방식” 챕터에서 만들어본 `C:\docker\bind\index.html` 파일을 그대로 이용해볼 것이다. 

```bash
docker cp /mnt/c/docker/bind/index.html my-web-server:/usr/local/apache2/htdocs/
```

코드 3-5. 

위 명령어를 입력한 후 앞선 웹 화면을 새로고침하면 앞서 사진 2-2에서 보았던 화면을 똑같이 볼 수 있을 것이다. 

이제 해당 컨테이너를 중단, 삭제한다. 

```bash
docker stop my-web-server
docker rm my-web-server
```

코드 3-6.

이제 다음과 같은 새 컨테이너를 생성해보겠다. 역시 똑같은 볼륨에 마운트하나, 구별을 위해 컨테이너 이름과 포트 번호는 조금 다르게 하였다.

```bash
docker run --name apache-server -d \
  -p 8085:80 \
  -v my-web-server-vol:/usr/local/apache2/htdocs \
  httpd
```

코드 3-7.

이제 `http://localhost:8085` 로 접속해보면 앞선 사진 2-2에서 보았던 것과 동일한 화면이 보이는 것을 확인할 수 있다. 이를 통해, 컨테이너에 저장된 데이터 및 파일들이 자동으로 마운트된 볼륨에도 복사가 되어 저장되고, 이는 컨테이너가 삭제되어도 그대로 유지된다는 것을 확인할 수 있다. 

실습이 끝났으면, 실행 중인 컨테이너를 중단 및 삭제한다. 그리고 더 이상 불필요해진 해당 볼륨도 삭제한다. `docker volume rm my-web-server-vol` 명령어를 통해 삭제하면 된다.

# 마운트 실습 - 데이터베이스 활용

이번에는 데이터베이스를 이용하여 볼륨에 데이터를 저장하고 영속성이 유지되는지 테스트해보고자 한다. 아무래도 도커에서 데이터 영속성을 지켜야하는 상황을 대표하는 것 중 하나는 DB 데이터를 보존하는 것이 아닐까 싶어서이다. 여기서는 `MariaDB` 를 이용하여 볼륨에 마운트하여 데이터를 저장해본 후, 해당 컨테이너를 삭제 후 새 컨테이너로 마운트해도 기존 데이터가 잘 보존되어 있는지를 확인해보도록 하겠다[^2]. 

먼저 다음과 같이 볼륨을 먼저 만들었다.

```bash
docker volume create db-vol
```

코드 4-1. 

그 후, 다음과 같이 `mariadb` 이미지를 이용하여 해당 볼륨에 마운트하는 새 컨테이너를 생성하였다. 

```bash
docker run --name maria-test -d \
  -p 3307:3306 \
  -v db-vol:/var/lib/mysql \
  -e MARIADB_ROOT_PASSWORD=maria-root-test \
  -e MARIADB_DATABASE=testdb \
  mariadb
```

코드 4-2. MariaDB 컨테이너 실행 명령어. 필자의 경우, 호스트에서 이미 3306 포트 번호를 사용 중이라 부득이하게 호스트 번호를 3307로 매핑하였다. 

`mariadb` 이미지 관련 사용 가능한 환경 변수들은 다음의 사이트에서 확인 가능하다.

- [https://hub.docker.com/\_/mariadb](https://hub.docker.com/_/mariadb)
- [https://mariadb.com/docs/server/server-management/automated-mariadb-deployment-and-administration/docker-and-mariadb/mariadb-server-docker-official-image-environment-variables](https://mariadb.com/docs/server/server-management/automated-mariadb-deployment-and-administration/docker-and-mariadb/mariadb-server-docker-official-image-environment-variables)

해당 컨테이너가 작동 중이라면 이번에는 해당 컨테이너의 쉘에 접속해볼 것이다. 다음과 같은 명령어를 차례대로 입력한다. 참고로 아래 명령어에서 `$` 는 현재 호스트, `#` 은 앞서 만든 `maria-test` 컨테이너 내부 쉘에 들어왔음을 의미한다. `$` , `#` 기호는 빼고 입력한다. 

```bash
$ docker exec -it maria-test bash
# mariadb -uroot -pmaria-root-test
```

코드 4-3. 

다음과 같은 텍스트들이 출력되면 MariaDB에 무사히 로그인, 접속했다는 뜻이다. 

```bash
Welcome to the MariaDB monitor.  Commands end with ; or \g.
Your MariaDB connection id is 3
Server version: 12.3.2-MariaDB-ubu2404 mariadb.org binary distribution

Copyright (c) 2000, 2018, Oracle, MariaDB Corporation Ab and others.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

MariaDB [(none)]>
```

코드 4-4.

이제 해당 쉘에서 앞서 만든 `testdb` database가 있는지 확인한다.

```bash
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testdb             |
+--------------------+
5 rows in set (0.001 sec)

MariaDB [(none)]> use testdb;
Database changed
MariaDB [testdb]> show tables;
Empty set (0.000 sec)

MariaDB [testdb]>
```

코드 4-5. 

위와 같이 해당 database가 존재하는 것을 확인하였다. 다만 그 외에는 해당 데이터베이스에 아무것도 만들지 않았기에 `show tables` 입력 시 아무런 테이블도 조회되지 않음을 알 수 있다. 

여기서 간단한 테이블을 생성하고 몇몇 데이터들을 입력해보도록 하겠다. 해당 쉘에서 다음과 같은 SQL을 입력하였다.

```sql
CREATE TABLE products (
  id INT PRIMARY KEY AUTO_INCREMENT,
  name VARCHAR(50) UNIQUE KEY,
  price INT
);

INSERT INTO products(name, price) VALUES
('양상추', 3000),
('바나나', 2000),
('사과', 1000),
('초콜릿', 1500);
```

코드 4-6. 

그리고 다음의 SQL를 통해 데이터들이 잘 입력되었는지 확인한다.

```sql
MariaDB [testdb]> SELECT * FROM products;
+----+-----------+-------+
| id | name      | price |
+----+-----------+-------+
|  1 | 양상추    |  3000 |
|  2 | 바나나    |  2000 |
|  3 | 사과      |  1000 |
|  4 | 초콜릿    |  1500 |
+----+-----------+-------+
4 rows in set (0.000 sec)
```

코드 4-7.

확인하였으면 이제 해당 컨테이너 자체를 빠져나간다. `exit` 명령어를 입력한다. MariaDB 안에서 한 번, `maria-test` 라는 컨테이너 쉘 자체에서도 한 번 더 입력하여 다시 도커(호스트 쉘)로 돌아온다. 

그 다음, 현재 실행중인 `maria-test` 컨테이너를 중단, 삭제한다. 

```bash
docker stop maria-test
docker rm maria-test
```

코드 4-8.

그 후, 이번에는 같은 `mariadb` 이미지로부터 새로운 컨테이너를 다음과 같이 생성한다.

```bash
docker run --name new-maria -d \
  -p 3300:3306 \
  -v db-vol:/var/lib/mysql \
  -e MARIADB_ROOT_PASSWORD=maria-root-test \
  mariadb
```

코드 4-9.

그 다음 다시 아까처럼 해당 컨테이너의 쉘에 접속한다.

```bash
$ docker exec -it new-maria bash
# mariadb -uroot -pmaria-root-test
```

코드 4-10. 

그 후 다음과 같은 SQL 명령어들을 차례대로 입력한다.

```sql
MariaDB [(none)]> show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| testdb             |
+--------------------+
5 rows in set (0.004 sec)

MariaDB [(none)]> use testdb;

MariaDB [testdb]> show tables;
+------------------+
| Tables_in_testdb |
+------------------+
| products         |
+------------------+
1 row in set (0.000 sec)

MariaDB [testdb]> desc products;
+-------+-------------+------+-----+---------+----------------+
| Field | Type        | Null | Key | Default | Extra          |
+-------+-------------+------+-----+---------+----------------+
| id    | int(11)     | NO   | PRI | NULL    | auto_increment |
| name  | varchar(50) | YES  | UNI | NULL    |                |
| price | int(11)     | YES  |     | NULL    |                |
+-------+-------------+------+-----+---------+----------------+
3 rows in set (0.001 sec)

MariaDB [testdb]> SELECT * FROM products;
+----+-----------+-------+
| id | name      | price |
+----+-----------+-------+
|  1 | 양상추    |  3000 |
|  2 | 바나나    |  2000 |
|  3 | 사과      |  1000 |
|  4 | 초콜릿    |  1500 |
+----+-----------+-------+
4 rows in set (0.016 sec)
```

코드 4-11. 

앞서 다른 컨테이너로 만든 테이블 및 데이터가 볼륨에 그대로 보존되어 있어 새 컨테이너로 접속해도 같은 데이터들을 볼 수 있음을 확인할 수 있다. 이를 통해 데이터베이스에서의 데이터들도 컨테이너의 생명주기와 관계없이 볼륨에 영구적으로 저장할 수 있음을 확인할 수 있었다. 

<details id="ref-1">
<summary>
    <strong>
        <i>참고 - 주의사항</i>
    </strong>
    <a href="#ref-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
필자가 위 과정대로 실습하던 중, 맨 처음 `maria-test` 컨테이너를 생성했을 때 `-e MARIADB_DATABASE=testdb` 라는 옵션값을 깜빡하고 주지 않은 채로 생성했었다. 그래서 이후 해당 컨테이너를 삭제하고 해당 옵션을 준 채로 새 컨테이너를 생성했었다. 그러나 MariaDB에 접속했을 때 `testdb` 데이터베이스는 존재하지 않았다. 이는 이미 맨 처음에 해당 옵션을 주지 않은 상태에서 빈 볼륨이 초기화되어서 발생한 문제로 보인다. 그래서 컨테이너 및 볼륨 자체를 삭제하고 아예 처음부터 빈 볼륨을 새로 생성, `-e MARIADB_DATABASE=testdb` 옵션값을 까먹지 않고 넣어 처음부터 다시 시작하니 해당 db가 생성되었다. 
    
이처럼 처음 초기화가 중요하므로 이에 주의해야겠다. 
</div>
</details>

# 볼륨 내 데이터 백업 및 복원 방법

볼륨 내 데이터들을 호스트로 백업하거나, 반대로 호스트에서 볼륨으로 데이터를 주입, 복원하는 일도 필요할 것이다. 이 챕터에서는 볼륨 내 데이터를 백업 및 복원하는 방법에 대해 다룬다. 

실습을 위해, 앞서 살펴본 `httpd` 이미지를 토대로 실습하겠다. 앞선 “웹 서버를 이용한 마운트 실습”에서의 “볼륨 마운트 방식”챕터와 동일한 절차로 준비한다. 즉, 맨 처음 볼륨을 컨테이너와 마운트 한 후, 호스트에서 커스텀한 `index.html` 파일을 `docker cp` 를 통해 컨테이너에 주입, 그 후 해당 컨테이너를 종료시켜 해당 볼륨만 남기는 것까지 동일하게 진행한다. 

그 후, 다음의 명령어를 입력하면 볼륨 내 데이터를 호스트로 백업할 수 있게 된다.

```bash
docker run --rm \
  -v my-web-server-vol:/source \
  -v /mnt/c/docker/backup:/target \
  alpine \
  tar czvf /target/web-server-backup.tar.gz -C /source .
```

코드 5-1.

위 코드에서는 `my-web-server-vol` 이라는 볼륨 안에 있던 `index.html` 파일을 Windows의 `C:\docker\backup` 이라는 폴더에 `web-server-backup.tar.gz` 이라는 이름의 파일로 백업하는 명령어이다. 위 명령어가 성공적으로 실행되면 실제로 Windows의 해당 폴더 경로에 압축된 파일이 생성될 것이다. 

위 명령어에 대해 하나씩 살펴보면 다음과 같다.

- `--rm` : 이 컨테이너를 단 한 번만 실행하고 바로 삭제한다. 백업 과정처럼 단 한 번만 작업을 진행하고 그 이후에는 굳이 컨테이너를 지속적으로 실행할 필요가 없는 상황에서 이 옵션을 사용하면 자동으로 컨테이너가 삭제 되어서 편리하다.
- `-v my-web-server-vol:/source` : 백업을 위해 현재 `alpine` 이라는 경량 리눅스 컨테이너를 사용한다. 이 때 `alpine` 컨테이너 내부의 `/source` 라는 경로와 `my-web-server-vol` 이라는 볼륨을 마운트한다. 여기서는 volume mount가 사용되었다. `/source` 경로 대신 원하는 경로로 해도 상관은 없다.
- `-v /mnt/c/docker/backup:/target` : 백업을 위해 `alpine` 컨테이너 내부의 `/target` 이라는 경로와 `/mnt/c/docker/backup` 이라는 호스트에 존재하는 경로와 마운트한다. 여기서는 bind mount가 쓰였다. `/target` 경로 대신 원하는 경로로 해도 상관은 없다.
    - 이렇듯 볼륨 백업을 위해선 하나의 컨테이너가 볼륨과도 마운트해야하고, 백업 파일이 저장될 호스트 경로도 마운트해야한다.
- `alpine` : 백업을 위해 사용될 이미지. `alpine` 은 경량 리눅스 배포판으로, 말 그대로 가벼워서 간단한 작업에 쓰기 좋다. `ubuntu` 와 같은 여타 다른 리눅스 배포판을 써도 되지만, 백업을 위해 사용하기엔 무거워서 보통 `alpine` , `busybox` 와 같은 경량 리눅스 배포판을 사용한다.
- `tar czvf /target/web-server-backup.tar.gz -C /source .` : 본격적으로 파일을 백업하는 명령어이다. `tar` 라는 도구를 이용하며, `/source` 폴더에 있는 현재 디렉터리(`.`)에 있는 파일들을 하나로 묶어 `/target` 디렉터리 아래에 `web-server-backup.tar.gz` 라는 파일로 백업하라는 의미이다. `/source` 는 앞선 `my-web-server-vol` 과 마운트되어 있고, `/target` 경로는 호스트 내부의 `/mnt/c/docker/backup` 경로와 마운트되어 있기에 사실상 볼륨에서 호스트 경로로 백업하는 것이다.
    - `-C` : 이 옵션 뒤에 오는 경로에 있는 파일들을 대상으로 한다.
    - `czvf` 의 `c` : `create` 의 약자로, `tar archive` 를 새로 생성하라는 옵션. `tar` 는 여러 파일들을 하나의 archive(아카이브)라는 단일 파일로 묶는 도구이다. 즉, 여기서는 여러 파일들을 하나로 묶겠다는 의미이다.
    - `z` : gzip 압축 옵션을 적용. 사실 `tar` 자체는 여러 파일들을 하나로 묶는 역할만 할 뿐, 파일을 더 적은 용량으로 압축하는 것은 `gzip` 이 별도로 수행하는 구조이다. 이는 Windows에서 흔히 보는 파일 여러 개로 묶고 압축하는 `zip` 과는 달라 보이는 점이다.
    - `v` : verbose, 명령어 실행 과정을 자세하게 출력한다.
    - `f` : 생성하고자 하는 `tar` 아카이브 파일 이름을 지정한다. 이 옵션 뒤에 파일명을 지정해야하기에 보통 `czvf` 처럼 `f` 옵션을 맨 뒤에 배치한다.

![그림 3-1. volume을 host로 백업하는 과정을 묘사한 그림. volume 내부는 container로만 접근 가능하기 때문에 위와 같이 임시 컨테이너를 이용하여 volume 내 데이터들을 host로 백업한다. ](/images/2026-07-28/docker-data-persistency-mount-and-volume/4.png)

그림 3-1. volume을 host로 백업하는 과정을 묘사한 그림. volume 내부는 container로만 접근 가능하기 때문에 위와 같이 임시 컨테이너를 이용하여 volume 내 데이터들을 host로 백업한다. 

반대로 기존 호스트에 있는 파일 및 데이터들을 도커 볼륨으로 복원하고자 할 때에는 다음과 같은 명령어를 사용한다.

```bash
docker run --rm \
  -v my-web-server-restored-vol:/source \
  -v /mnt/c/docker/backup:/target \
  alpine \
  tar xzvf /target/web-server-backup.tar.gz -C /source
```

코드 5-2. 구분을 위해 일부러 기존 `my-web-server-vol` 과는 다른 새로운 `my-web-server-restored-vol` 볼륨을 사용하였다. 

볼륨 백업 명령어와 거의 큰 차이가 없다. 다만 다음의 점들을 주의해야 한다.

- `-C /source` 뒤에 `.` 이 붙지 않는다.
- `czvf` 의 `c` 대신 `x` 옵션을 붙여 `xzvf` 옵션으로 해야한다. 여기서 `x` 옵션은 `extract` 의 의미로, 기존 tar 아카이브 파일로부터 파일들을 추출하는 옵션이다. 즉, 압축된 파일들을 풀 때 사용하는 옵션이다.

이제 복원된 볼륨을 다음과 같이 마운트하여 웹 브라우저에서 확인해보자. 

```bash
docker run --name my-web-server-restored -d -p 8085:80 \
  -v my-web-server-restored-vol:/usr/local/apache2/htdocs \
  httpd
```

코드 5-3.

그러면 앞선 사진 2-2와 같이 직접 만들었던 `index.html` 의 화면이 고스란히 보일 것이다. 이로서 docker volume 백업과 복원에 성공하였다. 

---

References

[1] 지은이: 오가사와라 시게타카, 옮긴이: 심효섭, “그림과 실습으로 배우는 도커 & 쿠버네티스“, 위키북스

[2] Docker docs

[Home](https://docs.docker.com/)

[3] Docker docs - storage

[Storage](https://docs.docker.com/engine/storage/)

[4] Docker docs - database tutorial

[Use containerized databases](https://docs.docker.com/guides/databases/)

[5] Docker docs - Storage drivers - container보다 Volume에 데이터 쓰기/읽기 작업이 더 빠른 이유 참고. 

[Storage drivers](https://docs.docker.com/engine/storage/drivers/)

[6] [[Docker] 도커 볼륨과 마운트](https://joyerim.tistory.com/107)

[7] [Docker Volume, 제대로 이해하기](https://gngsn.tistory.com/291)

[8] [mariadb - Official Image \| Docker Hub](https://hub.docker.com/_/mariadb)

[9] 참고 - MariaDB에서의 백업 방법

[[Linux] mariadb-backup 활용 가이드](https://servermon.tistory.com/951)

[10] 참고 - cold backup vs hot backup

[Cold Backup vs Hot Backup: Which One Is Best for Your System](https://www.info2soft.com/blogs/cold-backup-vs-hot-backup.html)

[11] [[Docker] 볼륨, 볼륨 마운트, 볼륨 백업](https://velog.io/@bami/Docker-%EB%B3%BC%EB%A5%A8-%EB%A7%88%EC%9A%B4%ED%8A%B8#%EB%B3%BC%EB%A5%A8-%EB%B0%B1%EC%97%85)

[12] [Docker 백업 완벽 가이드: 4가지 핵심 항목 정리 ⋆ Blog * JackerLab](https://blog.jackerlab.com/docker-backup-guide-4-essential-items/)

[13] tar, gz 관련 설명

[리눅스 tar와 tar.gz 차이 (압축, 해제 명령어)](https://change-words.tistory.com/entry/linux-tar-targz)

[14] tar 관련 설명

[[Linux] tar 와 tar.gz 차이 압축, 압축 해제](https://eunchankim-dev.tistory.com/60)

---

[^1]: 왜 컨테이너보다 volume에 데이터를 쓰고 읽는 속도가 더 빠른지 그 원리는 추후에 기회가 된다면 별도의 글에서 다뤄볼 예정이다. 여기서 언급하기엔 생각보다 복잡하고 너무 길어 이 글의 주제에서 벗어날 수도 있고, 그 원리 중에는 Dockerfile 등을 이용한 이미지 빌드, 이미지 레이어에 대한 언급도 나오는데, 아직 이에 대해선 다루지 않아 지금 다루기엔 너무 이른 감이 있기도 하다. 

[^2]: 이전 글에도 몇 번 언급했지만, 지금은 docker의 기본적인 것을 다루고 있어, 실제 서비스 가능한 예시로 들기에는 그 과정도 복잡하고, docker 기본 개념을 익히는 본질과 멀어질 수 있어 이렇게 간단한 예시로 들고 있다. 나중에 별도의 글에서 웹 서버, WAS, DB를 서로 연동하여 웹 서비스가 가능한지를 살펴보는 조금 더 실질적인 활용이 가능한 실습을 해볼 예정이다.