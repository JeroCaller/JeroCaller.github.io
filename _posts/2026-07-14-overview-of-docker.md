---
title: "[Docker] Docker 개요"
category: "Infra & Cloud"
tag: ["Docker", "Container", "도커"]
---

참고하면 좋을 이전 글들

- [**[CI DB 구축 - 3] Github docker service container를 이용하여 runner에서 테스트용 DB 인프라 구축하는 법**](/ci%20&%20cd/CI-DB-%EA%B5%AC%EC%B6%95-3-Github-docker-service-container%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-runner%EC%97%90%EC%84%9C-%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%9A%A9-DB-%EC%9D%B8%ED%94%84%EB%9D%BC-%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94-%EB%B2%95/)
- [**[OS] 운영체제의 구조**](/infra%20&%20cloud/os-structure-of-os/)
- [**[OS] 리눅스 개요**](/infra%20&%20cloud/os-overview-of-Linux/)
- [**가상화 개요**](/infra%20&%20cloud/overview-of-virtualization/)

사실 이전 글인 “[**[CI DB 구축 - 3] Github docker service container를 이용하여 runner에서 테스트용 DB 인프라 구축하는 법**](/ci%20&%20cd/CI-DB-%EA%B5%AC%EC%B6%95-3-Github-docker-service-container%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-runner%EC%97%90%EC%84%9C-%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%9A%A9-DB-%EC%9D%B8%ED%94%84%EB%9D%BC-%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94-%EB%B2%95/)” 이란 글에서 CI/CD를 활용하기 위해 Docker라는 개념이 필요했었기에 해당 개념에 대해 서술한 적이 있다. 다만 해당 글은 Docker만을 다루거나 Docker를 제대로 다루는 글은 아니었기에, 이 글에서 Docker에 대한 개념 및 구성 요소들에 대해 다시 정리하고자 한다. 

이 글에서는 Docker에 대해 다루는 첫 번째 글이며, Docker의 개념 및 개요, 구성 요소들, 구조, 즉 주로 이론적인 측면에 대해서 다루고자 한다. 

# Docker 개요

Docker는 애플리케이션을 운영체제와 격리하여 독립적으로 실행시킬 수 있게 해주는 컨테이너 기술을 기반으로 하는 컨테이너 관리 플랫폼이다. 컨테이너 기술을 구현한 여러 회사들의 제품들 중에서 가장 유명한 것이 Docker이다. 이 Docker는 리눅스 운영체제를 기반으로 동작하기에 기본적으로는 그 외의 윈도우, macOS 등에서는 바로 사용하는 것이 불가능하다. 다만 WSL 2처럼 타 운영체제 위에서 리눅스를 같이 띄우는 기술이나 아예 가상화를 통해 리눅스 Guest OS를 가지는 VM을 이용한 방법 등을 통해 Docker를 사용할 수는 있다. 

Docker에서 제공하는 컨테이너 기술을 이용하면 애플리케이션을 운영체제로부터 독립시킬 수 있기에 호스트 PC에 해당 앱을 설치할 필요가 없다. 그렇기에 설치된 앱을 삭제할 때의 번거로운 작업도 없다. 그저 실행 중인 컨테이너만 중단, 삭제하면 끝이다. 이로 인해 앱 설치 과정에 의한 흔적을 Host에 남기지 않아 특히 Github Actions의 runner, 렌탈 서버 등 남에게 빌린 서버를 사용할 때 특히 좋다[^1]. 

“애플리케이션을 운영체제로부터 독립시킨다”라는 말을 조금 더 정확히 하자면, 애플리케이션과 이를 실행하기 위해 필요한 여러 환경 설정 파일, 바이너리, 라이브러리 등의 요소들이 포함된 사용자 공간(user space)과 운영체제의 커널을 분리하고, 사용자 공간에 해당하는 부분만 컨테이너 방식으로 가상화시킨다는 것이다. “커널은 제외한 채, 사용자 공간에 해당하는 영역만 가상화하여 외부 환경과 격리시킨다“라는 사실 하나로 인해 다음과 같은 여러 이점들이 생겨나게 된다. 

- 컨테이너 자체가 경량화, 즉 가벼워진다. 컨테이너 자체는 그 자체로 커널을 보유할 필요가 없이 Host OS의 커널을 사용하면 그만이기 때문이다.
    - 이는 Guest OS라는 별도의 OS 및 커널을 소유해야하는 VM이 상대적으로 무겁다는 사실과 대비된다.
- 애플리케이션의 입장에서는 외부로부터의 독립성을 소유하기에 외부의 영향, 간섭을 받지 않게 된다. 예로, 만약 둘 이상의 앱들을 하나의 물리적 컴퓨터 위에 바로 실행시킨다고 해보자. 이는 즉, 여러 앱들이 같은 사용자 공간을 사용한다는 뜻이다. 이로 인해 의존성, 환경 설정 등을 위한 디렉토리 경로가 우연히 겹친다든지 한다면 자칫 서로 충돌이 나 제대로 작동하지 않을 수도 있고, A, B 두 앱 모두 필요한 의존성 파일에 대해 B 앱 업데이트로 인해 해당 의존성 파일도 업데이트하면 자칫 A 앱이 작동하지 않을 수도 있다. 반면, 각각의 앱들을 각자의 컨테이너로 격리시키고 실행하면 같은 Host OS의 커널을 사용한다고 하더라도 사용자 공간 자체는 서로 격리되어 있기에 앞서 말한 충돌, 의존성 문제가 나타나지 않는다. 앱 A의 의존성 파일을 수정한다 하더라도 앱 B에는 전혀 영향을 끼치지 않는다.
    - 이러한 특성 때문에, 필요한 경우, 버전만 다른 동일한 앱 여러 개를 동시에 띄워 실행시킬 수 있다. 이는 Docker를 사용하지 않는 일반 컴퓨터에서는 상상도 할 수 없는 일이다.
- 로컬 개발 환경과 테스트 환경,  CI/CD, 운영 환경 간의 일관성을 유지할 수 있다. 만약 Docker가 없었다면 로컬, 테스트, CI/CD, 운영 환경 모두에 동일한 앱, 의존성 설치, 환경 설정 등의 번거로운 작업도 했어야 했을 것이고, “제 컴퓨터에선 되는데 왜 여기선 안돼죠?”와 같은 환경 비일관성으로 인해 생기는 오작동 문제도 해결하기가 쉽지 않았을 것이다. 이는 달리 말하자면, Docker를 사용한다면 팀원들간의 협업에 있어 모든 팀원들이 모두 동일한 환경에서 개발하고 공유할 수 있게 된다는 말이기도 하다.
- 보통 Docker에서 컨테이너는 단 하나만 띄우는 것이 아닌 여러 개를 띄워 사용하곤 한다. 웹 사이트를 배포한다고 할 때에도 Docker를 사용한다면 웹 서버, WAS, DB 이렇게 최소 3개의 별개의 앱들을 각자의 컨테이너로 감싸 배포할 수 있다. 또는 아예 동일한 앱들을 여러 컨테이너들로 띄움으로서 여러 개를 동시에 실행시킬 수 있다. 이러한 특성을 이용하면, 사용자 수가 많아져 특정 컨테이너에 대해서만 요청 수가 급증하여 부담이 될 때 똑같은 컨테이너를 여러 개 띄워 수평 확장시킴으로서 요청을 분산시킬 수 있고, 이로 인해 더 많은 응답 처리, 응답 성능 향상, 가용성 확보 등의 이점도 얻을 수 있다. 다만 이러한 용도로 사용하는 경우에는 Docker만으로는 부족하고, 로드 밸런싱이 필요하기도 하고, 여러 컨테이너들을 함께 관리, 운영해야하기에 컨테이너 오케스트레이션 도구 중 하나인 쿠버네티스 등이 필요할 수도 있다.

![그림 1-1. Docker를 사용하지 않을 때와 사용할 때의 차이점을 묘사한 그림. 둘 다 각자 물리적 컴퓨터를 사용하고 있으나 왼쪽은 Docker를 사용하지 않고, 오른쪽은 Docker를 사용할 때의 구조이다. 핵심만을 설명하기 위해 오른쪽에는 host kernel과 container 사이의 Docker engine은 생략하였다. 물리적 서버에서 Docker 없이 순수하게 사용하는 경우, 둘 이상의 앱들은 서로 같은 사용자 공간(user space)을 사용하게 된다. 따라서 만약 두 앱이 동일한 의존성을 사용한다고 하면, 한쪽 앱만을 위해 의존성에 변경을 주는 경우, 이를 사용하던 다른 앱에 악영향을 끼칠 수 있다. 반면 오른쪽 그림과 같이 Docker container를 사용하게 되면 서로 독립적인 각자의 사용자 공간을 보유하기 때문에 같은 의존성이라도 각자의 사용자 공간에서만 사용하기에 다른 컨테이너에 있는 앱, 의존성 파일에 영향을 끼치지 않는다. 이는 곧 컨테이너 내용물을 수정할 때에도 다른 컨테이너에 영향을 끼치지 않기에 수정이 수월하다는 장점으로도 이어진다. ](/images/2026-07-14/overview-of-docker/1.png)

그림 1-1. Docker를 사용하지 않을 때와 사용할 때의 차이점을 묘사한 그림. 둘 다 각자 물리적 컴퓨터를 사용하고 있으나 왼쪽은 Docker를 사용하지 않고, 오른쪽은 Docker를 사용할 때의 구조이다. 핵심만을 설명하기 위해 오른쪽에는 host kernel과 container 사이의 Docker engine은 생략하였다. 물리적 서버에서 Docker 없이 순수하게 사용하는 경우, 둘 이상의 앱들은 서로 같은 사용자 공간(user space)을 사용하게 된다. 따라서 만약 두 앱이 동일한 의존성을 사용한다고 하면, 한쪽 앱만을 위해 의존성에 변경을 주는 경우, 이를 사용하던 다른 앱에 악영향을 끼칠 수 있다. 반면 오른쪽 그림과 같이 Docker container를 사용하게 되면 서로 독립적인 각자의 사용자 공간을 보유하기 때문에 같은 의존성이라도 각자의 사용자 공간에서만 사용하기에 다른 컨테이너에 있는 앱, 의존성 파일에 영향을 끼치지 않는다. 이는 곧 컨테이너 내용물을 수정할 때에도 다른 컨테이너에 영향을 끼치지 않기에 수정이 수월하다는 장점으로도 이어진다. 

물론 Docker에도 단점이 있다. 

- 리눅스 운영체제에서만 Docker가 사용될 수 있기에 애플리케이션도 리눅스에서 사용 가능한 애플리케이션만 사용 가능하다.
- 여러 컨테이너들이 동일한 Host kernel을 사용하기에, 물리 서버 자체에 이상이 생길 경우 그 위에 올라가 있는 모든 컨테이너들에게도 영향이 간다.

# Docker 구성 요소

Docker에는 여러 구성 요소들이 있는데, 여기서는 간단히 정리해보겠다. 

- Container: 애플리케이션과 이 앱의 동작을 위해 필요한 각종 환경 설정 파일, 라이브러리, 의존성, 바이너리들을 같은 공간에 넣어 격리시킨 공간. 실제로 Host OS 위에 있는 Docker Engine 위에서 동작하는 실체이다. 같은 image로부터 여러 개의 서로 독립적인 컨테이너들을 생성, 운용할 수도 있다. 반대로 하나의 물리적 서버 내에서 서로 다른 앱들의 컨테이너들을 여러 대 띄워서 서로 상호작용하게끔 구성할 수도 있다.
- Image: Container를 생성하는 일종의 설계도. 여기에는 앱 실행에 필요한 이진 파일, 라이브러리, 그리고 컨테이너로 만들고 실행시키는데 필요한 명령어들이 포함되어 있다. 일종의 템플릿이자 패키지라 보면 되겠다. 객체지향 프로그래밍에서 클래스와 객체 관계처럼 Image와 Container의 관계도 그와 비슷하다고 보면 된다. 하나의 이미지만 있어도 서로 다른 물리적 서버에서 동일한 컨테이너들을 생성, 운용할 수 있다. 이미 누군가가 만든 이미지를 사용할 수도, 아니면 본인이 직접 만들어 사용할 수도 있다.
- Registry: Docker image들을 저장해놓는 저장소이다. [Dockerhub](https://hub.docker.com/)처럼 온라인에서 누구나 사용 가능한 공개 저장소도 있고[^2], 반대로 사내, 또는 개인적으로만 사용 가능한 private registry도 있다. 심지어는 Docker registry 자체도 Docker image화된 경우도 있어, 이 이미지를 이용하여 사내, 또는 개인 Docker registry를 구축할 수도 있다고 한다.
- Volume: container는 본래 일회용으로 사용된다. container를 삭제해도 image는 별도로 삭제하지 않는 한 계속 존재하기도 하고, image로부터 container를 만드는데에 별 다른 비용이 드는 것도 아니기 때문이다. 다만, 한 번 컨테이너를 지우면 그 안에 있던 파일이나 데이터들도 같이 사라진다. 특히 DB 데이터 등 절대 지워지면 안되는 파일, 데이터가 있는 경우에는 container 내부에 두기만 해선 안될 것이다. 이 경우, Volume이라는 것을 사용하면, 컨테이너와 독립적으로 존재하는 스토리지 영역이기에 컨테이너가 사라져도 데이터를 영구적으로 보존시킬 수 있다.

한 편, 이 개념들은 서로 별개의 개념들이 아니라 서로 유기적으로 상호작용하며 동작한다. 다음은 특정 앱을 사용하기 위해 특정 Docker container를 실행시키기까지의 과정을 묘사하였다. 

1. Docker hub와 같은 Docker registry로부터 원하는 docker image를 호스트 PC에 내려 받는다. 이 과정을 `docker pull` 또는 `docker run` 이라는 명령어로 수행할 수 있다.
2. docker image를 받은 호스트 PC에서 해당 image를 토대로 새로운 docker container 인스턴스를 생성한다. 이 과정을 명령어로 수행한다면 `docker run` 을 입력하면 되겠다.
    1. `docker run` 이라는 명령어에는 만약 호스트 PC 내에 해당 docker image가 없다면 자동으로 docker registry로부터 해당 image를 다운로드를 받는 기능이 포함되어 있다.


<div class="single-image">
  <img width="70%" src="/images/2026-01-24/%5BCI%20DB%20%EA%B5%AC%EC%B6%95%20-%203%5D%20Github%20docker%20service%20container%EB%A5%BC%20%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC%20runner%EC%97%90%EC%84%9C%20%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%9A%A9%20DB%20%EC%9D%B8%ED%94%84%EB%9D%BC%20%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94%20%EB%B2%95/1.png" alt="image">
  <p markdown="1">
    그림 2-1. 이미 존재하는 Docker image를 내려받아 호스트 PC에 실행하는 과정을 묘사한 그림. 출처: [https://jerocaller.github.io/ci & cd/CI-DB-구축-3-Github-docker-service-container를-이용하여-runner에서-테스트용-DB-인프라-구축하는-법/#docker와-관련된-개념들](/ci%20&%20cd/CI-DB-%EA%B5%AC%EC%B6%95-3-Github-docker-service-container%EB%A5%BC-%EC%9D%B4%EC%9A%A9%ED%95%98%EC%97%AC-runner%EC%97%90%EC%84%9C-%ED%85%8C%EC%8A%A4%ED%8A%B8%EC%9A%A9-DB-%EC%9D%B8%ED%94%84%EB%9D%BC-%EA%B5%AC%EC%B6%95%ED%95%98%EB%8A%94-%EB%B2%95/#docker%EC%99%80-%EA%B4%80%EB%A0%A8%EB%90%9C-%EA%B0%9C%EB%85%90%EB%93%A4)
  </p>
</div>

# Docker Architecture

다음은 도커 공식 문서에서 설명하는 도커 아키텍처이다. 

![참고 사진 1-1. Docker에서 공식으로 설명하는 docker 아키텍처 도식도. 출처: [https://docs.docker.com/get-started/docker-overview/#docker-architecture](https://docs.docker.com/get-started/docker-overview/#docker-architecture)](/images/2026-07-14/overview-of-docker/2.png)

참고 사진 1-1. Docker에서 공식으로 설명하는 docker 아키텍처 도식도. 출처: [https://docs.docker.com/get-started/docker-overview/#docker-architecture](https://docs.docker.com/get-started/docker-overview/#docker-architecture)

Docker에서는 클라이언트-서버 아키텍처를 사용한다. 서버 쪽에서는 Docker daemon(줄여서 `dockerd` 라고도 표현한다)이라는 프로그램이 존재한다. 이 daemon은 다음과 같은 역할을 한다.

- 클라이언트로부터 Docker와 관련된 요청을 받고 이를 처리한다.
- container, image, volume, network 등 Docker를 구성하는 요소들을 관리, 실행시킨다.

클라이언트에서는 `docker run` 과 같이 명령어를 입력하면, UNIX socket 또는 network interface를 통해 REST API 형태로 요청을 하고, 이 요청을 서버 측의 docker daemon이 처리하여 응답하는 구조이다. 

클라이언트-서버 구조이기에, 클라이언트와 서버를 하나의 동일한 컴퓨터 내에서 사용할 수도 있고, 물리적으로 떨어져 있는 두 컴퓨터에 대해 원격으로 Docker 관련 작업을 실행하기 위해 서버 쪽 컴퓨터에는 docker daemon만, 클라이언트 쪽 컴퓨터에서는 CLI 기반의 클라이언트 프로그램만 설치하여 소통하는 것도 가능하다. 

한 편, Docker Desktop이란 프로그램에는 Docker daemon과 Docker client가 같이 포함되어있다. GUI 인터페이스를 가지며, 설치도 간편한 편이므로 주로 개발 과정에서 테스트가 필요하거나, Docker에 대해 공부할 때 좋은 프로그램이다. 

# 글을 마치며…

이번 시간에는 Docker에 대한 개요, 개념, 아키텍처들에 대해 정리해보았다. 전체적인 개요를 다루는 것이라 세부 개념들에 대해서는 다른 글들에서 하나씩 정리해 갈 예정이다. 

Windows 운영체제에서 Docker Desktop을 이용하여 Docker를 설치하고 사용하는 과정에 대해선 이전 글인 “[**[OS][Docker] WSL 2 - Windows에서의 Linux, Docker 설치 및 간단한 사용 방법**](/infra%20&%20cloud/wsl2-overview-and-how-to-use-with-docker/)”을 참고. 필자도 앞으로의 글에서는 주로 Windows 운영체제 위에서 WSL2와 Docker desktop을 이용하여 Docker를 다뤄볼 것이다. 

다음 글들에서는 Docker를 사용하기 위한 기본적인 명령어들과 container, image의 life cycle 등 각각의 개념들에 대해 좀 더 자세히 살펴보도록 하겠다. 

---

References

[1] 지은이: 오가사와라 시게타카, 옮긴이: 심효섭, “그림과 실습으로 배우는 도커 & 쿠버네티스“, 위키북스

[2] [Docker - 일경험 프로그램](https://wikidocs.net/book/18040)

[3] [dockerdocs - Home](https://docs.docker.com/)

[4] [Docker 베이스 이미지 이해하기: 컨테이너 속 Ubuntu는 진짜 Ubuntu가 아님 \| GeekNews](https://news.hada.io/topic?id=25997)

[5] [[Docker] Docker Image 이해하기 (도커 이미지란, 이미지의 종류, Docker Hub, 도커 이미지의 특징)](https://sxxb-in.tistory.com/17)

---

[^1]: 물론 Docker 실행에 필요한 Docker engine은 어쩔 수 없이 설치해야해서 이건 흔적이 남긴 한다. 

[^2]: Docker와 Dockerhub은 마치 Git과 Github의 관계와도 같다고 볼 수 있다.