---
title: "[OS][Docker] WSL 2 - Windows에서의 Linux, Docker 설치 및 간단한 사용 방법"
category: "Infra & Cloud"
tag: ["가상화", "Virtualization", "VM", "Virtual Machine", "VMM", "Hypervisor", "Container", "Docker", "WSL", "WSL 2", "WSL 1", "Linux", "Docker Desktop", "Windows", "OS"]
---

이 글에서는 윈도우 환경에서도 Linux 운영체제와 Docker를 사용할 수 있도록 WSL 2를 설치하는 방법에 대해 다뤄보겠다. Linux, Docker 등의 관련 지식, 이론 등의 설명은 이 글에서는 다루지 않고[^1] 대신 WSL2가 무엇인지, 그리고 “윈도우 환경에서 Linux 및 Docker를 설치 및 사용하는 방법”에 대해서만 다루겠다. 

# WSL 2 개요 및 구조

참고하면 좋을 이전 글들

- [**[OS] 운영체제의 구조**](/infra%20&%20cloud/os-structure-of-os/)
- [**가상화 개요**](/infra%20&%20cloud/overview-of-virtualization/)

WSL(Windows Subsystem for Linux)는 말 그대로 윈도우 운영체제에서 리눅스 환경을 실행할 수 있게 해주는 기술이다. “윈도우 운영체제에서 리눅스 바이너리를 네이티브로 실행시킬 수 있는 기술”이라고도 불린다. 이는 리눅스 운영체제에서만 동작할 수 있는 프로그램들을 윈도우에서도 돌릴 수 있다는 의미이다. 

이러한 기술 덕분에, 기존에는 리눅스 환경에서 앱을 테스트해봐야 할 때 별도의 리눅스 OS가 설치된 컴퓨터를 미리 구비해야하거나, 별도의 VM을 마련해야했지만, 이제는 윈도우 운영체제를 가진 컴퓨터 안에서도 빠르게 리눅스 환경을 마련할 수 있게 되었다. 

현재 WSL에는 WSL 1과 그 이후에 나온 WSL 2가 있다. 이 둘은 윈도우 운영체제에서 리눅스 환경을 실행, 동작시키는 구조가 다르다. 

기존의 WSL 1에서는, 리눅스 운영체제에서 호출하는 시스템 콜 API를 윈도우 시스템 콜 API에 맞게 번역하는 방식을 통해 리눅스를 구동시키는 방식이라고 한다. 이로 인해, 밑바닥에 있는 윈도우 운영체제와, 맨 위에 있는 리눅스 운영체제 사이에 번역가 역할을 해주는 레이어를 추가하여 이러한 구조를 구현하였다고 한다. 이 때, 리눅스에서는 리눅스 커널은 존재하지 않는 상태이며, 본래의 Host OS인 윈도우의 Windows NT kernel[^2]만이 있는 구조라고 한다. 

<div class="single-image">
  <img width="50%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/1.png" alt="image">
  <p>그림 1-1. WSL 1 아키텍처.</p>
</div>

이러한 WSL 1은 윈도우 운영체제 위에 리눅스 운영체제를 구현, 동작시키는 과정 속에서 그 어떤 가상화 기술도 들어가지 않았다. 이로 인해 서버 가상화 시 VM을 구동하는 데에 드는 시간과 하드웨어 I/O를 위한 상호작용 과정 속에서 오버헤드가 든다는 단점이 없어 가볍다는 것이 장점이라고 한다. 

사실 별도의 가상화 기술을 사용하지 않았음에도 하나의 운영체제 위에서 다른 운영체제를 구현, 동작시킨다는 것도 그 당시에 대단한 기술이었을 것이다. 다만, 현실적인 한계도 존재했다. 그것은 번역을 완벽하게 할 수 없다는 점이었다. 이로 인해 100% 실제 리눅스 환경을 모두 사용할 수는 없었다고 한다. 예로, 리눅스 환경에서만 돌아가는 Docker도 제대로 실행시킬 수 없었거나, 실행 가능하더라도 모든 기능을 100% 활용할 수는 없는 수준이었다고 한다. 

<div class="single-image">
  <img width="70%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/2.png" alt="image">
  <p markdown="1">참고사진 1-1. WSL 2 아키텍처. 출처: [https://www.youtube.com/watch?v=lwhMThePdIo](https://www.youtube.com/watch?v=lwhMThePdIo)</p>
</div>

나중에 나온 WSL 2의 경우, 아예 기존 WSL 1과의 아키텍처부터 다르게 나왔다. 여기서는 Microsoft 사의 하이퍼바이저 가상화 기술인 Hyper-V를 이용하여 그 위에 Windows 운영체제 파티션과, 리눅스 운영체제 파티션으로 나눠 병렬적인 구조로 실행되도록 하였으며[^3], 아예 리눅스 커널 자체를 가져다 올려놓은 구조이다. 리눅스 운영체제의 경우, Hyper-V에서 제공하는 컴퓨팅 자원을 덜 소모하는 경량 VM(lightweight VM)을 통해 리눅스 운영체제를 띄움으로서 일반적인 VM보다 더 가볍고 성능이 빠르다고 한다. 

실제로 필자가 윈도우 운영체제에서 WSL 2를 사용할 때와, 예전에 VMWare를 이용한 별도의 가상화 기술로 VM을 띄워 실행해봤을 때에도 부팅 속도 자체가 다른 게 체감이 갔었다. VM이라 하면 부팅 속도 자체도 느릴 거라 생각했는데, WSL 2에서는 그렇게 느리다는 걸 체감하진 못했다. 

리눅스 커널의 경우, 리눅스 커널 공식 사이트인 [kernel.org](http://kernel.org) 에서의 커널을 기반으로 하여 Microsoft 사에서 WSL 2에 맞게끔 커스텀하여 WSL 2 위에 올라간 것이다. WSL 2 기반 Linux kernel도 오픈소스여서 “[WSL2-Linux-Kernel](https://github.com/microsoft/WSL2-Linux-Kernel)”에서 확인해볼 수 있다. 

이러한 WSL 2는 그 구조 상 다음과 같은 장점들이 있다고 한다. 

- 경량 VM을 사용하기에 일반적인 VM에 비해 속도가 빠르다.
- WSL 1에서 시스템 콜 번역 과정이 필요 없어져 그에 따른 성능 하락을 마주치지 않아도 되며, 아예 리눅스 커널을 올려놓은 구조기에 거의 동일한 리눅스 환경을 제공할 수 있다.
    - 실제로 Hyper-V 기반의 WSL 2에서는 Docker를 동작시킬 수 있다.

# Windows에서 WSL 2 설치하는 방법

<div class="notice--info">
💡 Windows 11 (25H2)을 기준으로 설명합니다. 
</div>

1. 바탕화면 밑의 “검색창”에서 “Windows 기능 켜기/끄기”를 직접 검색하거나, 아니면 제어판 → 프로그램 → “Windows 기능 켜기/끄기”를 클릭한다. 그러면 다음과 같은 창이 뜨는데, 목록 중 “Linux용 Windows 하위 시스템”과 “가상 머신 플랫폼”을 체크 표시하고 “확인” 버튼을 누른다. 그 후 컴퓨터 리부팅 과정을 거칠 것이다. [^4] 

    <div class="single-image">
      <img width="60%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/3.png" alt="image">
    </div>

2. 그 후, 윈도우 검색창에서 “PowerShell”을 검색하고, 이를 관리자 권한으로 실행한다.
    
    ![20260513_20240213.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/4.png)
    
3. 먼저 해당 PowerShell에 다음과 같은 명령어를 입력한다. 그러면 wsl과 Linux 배포판 중 하나인 Ubuntu가 설치될 것이다. 
    
    ```powershell
    wsl --install
    ```
    
4. Ubuntu 운영체제에서 사용할 user account를 생성한다. 원하는 username과 password를 입력한다. 
    
    ![wsl install 명령어로 설치 및 우분투 계정 만들기.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/5.png)
    
5. 이후, 리눅스 CLI로 넘어갈 것이다. `lsb_release -a` 명령어를 입력하면 현재 설치된 리눅스 배포판 정보를 간단하게 알 수 있다. 한 편, `exit` 명령어를 입력하면 현재 실행중인 리눅스 배포판에서 벗어나 다시 PowerShell로 돌아올 수 있다. 
    
    ![wsl ubuntu 설치 후 현재 os 정보 확인.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/6.png)
    

여기까지 하면 WSL 2 및 Ubuntu가 설치 완료된다. 그 외 참고하면 좋을 wsl 관련 명령어들은 다음과 같다.

- `wsl --list --verbose` : WSL에 설치된 모든 Linux 배포판 정보들을 확인할 수 있다.
    
    ![wsl 설치 후 그 외 설정.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/7.png)
    
- `wsl --set-default-version <1|2>` : WSL에는 1과 2 버전이 있는데, 기본 버전을 설정할 수 있다. 필자의 경우, `wsl --set-default-version 2` 라고 설정하여 WSL 2를 기본으로 사용하게끔 설정하였다.
- `wsl --update` : WSL을 최신 버전으로 업데이트한다.
- `wsl --list --online` : WSL에 설치 가능한 Linux 배포판 목록들을 확인한다. 여기서 설치하고픈 배포판의 이름과 함께 `wsl --install <Distro>` 형식으로 입력하면 해당 배포판을 설치할 수 있다. 예) `wsl --install Debian`
    
    <div class="single-image">
      <img width="70%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/8.png" alt="image">
    </div>
    
- `wsl` : 이 명령어만 입력하는 경우, 기본으로 설치, 설정된 Linux 배포판을 실행하게 된다. 앞서 기본으로 설치된 Ubuntu를 기본으로 설정한 경우, Ubuntu cli로 변환될 것이다.

WSL 설치 시 Ubuntu도 앱 형태로 설치되는데, 이를 실행해도 Ubuntu를 바로 사용할 수 있다. 

<div class="single-image">
  <img width="80%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/9.png" alt="image">
</div>

<details id="ref-1">
<summary>
    <strong>
        <i>참고) 리눅스 현재 작업 디렉토리</i>
    </strong>
    <a href="#ref-1" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
위에서 처음 WSL을 설치하고 Ubuntu를 실행했을 때 현재 작업 디렉토리가 필자의 경우, `/mnt/c/WINDOWS/system32` 이었다. 이는 PowerShell을 관리자 권한으로 실행했을 경우에 이렇게 나타난다. 여기에는 각종 윈도우 관련 파일들이 있기에, 이와는 상관없는 전혀 다른 작업들을 하고자 하는 경우에는 다른 경로로 옮겨서 진행하는 것이 나을 것으로 보인다. 이 때 `cd ~` 를 이용하여 홈 디렉토리로 옮기면 되겠다. `~` 는 홈 디렉토리임을 의미하는 문자이다. 이 홈 디렉토리로 이동 후 `pwd` 라는 명령어를 입력하면 `/home/<username>` 형태의 경로가 출력될 것이다. 
    
아니면 앞서 보인 사진에서처럼, WSL 설치 시 Ubuntu가 앱 형태로도 설치되는데, 이를 실행해도 바로 홈 디렉토리에서 시작된다. 
</div>
</details>

# WSL 2 기반 Docker Desktop 설치 방법

1. [https://docs.docker.com/desktop/setup/install/windows-install/](https://docs.docker.com/desktop/setup/install/windows-install/)  - 이 사이트에 들어가서 “Docker Desktop for Windows”를 클릭한다. 그러면 해당 설치 파일이 다운로드될 것이다. 필자의 경우 “Docker Desktop for Windows - x86_64”를 클릭하여 다운로드 받았다.
    
    <div class="single-image">
      <img width="70%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/10.png" alt="image">
    </div>
    
2. 아래 사진과 같이 다운로드 받은 “Docker Desktop Installer”를 두 번 클릭하면 아래 사진처럼 새 창이 뜬다. 그대로 둔 채로 “OK”를 누른다. 
    
    <div class="single-image">
      <img width="70%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/11.png" alt="image">
    </div>
    
3. 그러면 설치가 시작되는데, 잠시 기다리면 설치가 완료되면서 다음과 같은 화면이 뜬다. “Close and restart”를 누른다. 그러면 컴퓨터가 자동으로 재시작한다. 
    
    <div class="single-image">
      <img width="70%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/12.png" alt="image">
    </div>
    
4. 재시작하고 나면 바탕화면에 다음과 같은 아이콘이 생길 것이다. 더블클릭하여 실행한다. 만약 후에 실행에 문제가 생긴다면 대신 아이콘 위에 마우스 커서를 두고 우클릭 후 “관리자 권한으로 실행”해보길 바란다. 
    
    <div class="single-image">
      <img width="10%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/13.png" alt="image">
    </div>
    
5. 다음과 같은 화면이 뜬다면 우측 아래에 “Accept”를 클릭한다. 
    
    <div class="single-image">
      <img width="70%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/14.png" alt="image">
    </div>

1. 그러면 아래와 같은 창이 뜰 것이다. 아래 화면은 로그인 화면인데, 필자는 “Personal”을 택한 후, 아래의 깃허브 아이콘을 클릭하여 깃허브 계정 연동으로 로그인해보았다. 
    
    ![docker desktop 첫 화면 및 로그인.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/15.png)

1. 다음과 같은 화면으로 전환된다면 성공한 것이다. 화면 좌측 아래에 초록색 글씨로 “Engine running”이라고 나오면 현재 docker engine이 실행 중에 있다는 뜻이다. 
    
    ![docker desktop 무한로딩 해결 후 화면.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/16.png)

참고로, WSL 상에서 `wsl -l -v` 명령어를 입력해봐도 docker-desktop의 현재 동작 여부를 확인할 수 있다. 

<div class="single-image">
  <img width="80%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/17.png" alt="image">
</div>

단, 예를 들어 우분투 터미널 창을 킨 상태에서 `exit` 명령어로 끈 후, 이미 기존에 있던 다른 터미널 창에서 `wsl -l -v` 을 입력하는 경우, ubuntu가 “Running” 상태 그대로 있을 수 있다. 이 때는 해당 터미널 창을 닫고 새로운 창을 열고 명령어를 입력해야 최신 상태를 반영하여 출력해준다. 이는 마치 환경 변수 설정 후 새 터미널 창을 열어야 새로운 명령어를 입력할 수 있는 것과 마찬가지라고 보면 되겠다. 

터미널 창에서 `docker --version` 또는 `docker version` 을 입력하면 현재 설치된 docker 정보들을 살펴볼 수 있다. 전자의 경우 간단한 정보만 나온다면 후자의 경우 보다 상세한 정보들이 나온다. 

만약 현재 실행 중인 docker engine을 종료하고자 한다면 아래 사진과 같이 Docker Desktop 창 좌측 아래에 “Engine running”이라는 초록색 문구 오른쪽의 점 세 개 짜리 아이콘을 클릭한 후, “Quit Docker Desktop”을 클릭하면 Docker Engine이 꺼진 후 Docker Desktop 창도 자동으로 꺼진다. 

<div class="single-image">
  <img width="80%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/18.png" alt="image">
</div>

<details id="ref-2">
<summary>
    <strong>
        <i>참고) Docker Desktop 무한로딩 이슈</i>
    </strong>
    <a href="#ref-2" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">

![docker desktop 무한 로딩 문제.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/19.png)
    
Docker Desktop 실행 후 위 화면과 같이 계속 무한로딩에 걸리는 이슈를 겪었다. 
    
해결 시도 1. ✅
    
시스템 트레이에서도 Docker Desktop이 꺼지질 않아 필자는 어쩔 수 없이 작업 관리자를 이용하여 강제로 끌 수밖에 없었다. 그 후, PowerShell에 들어가 `wsl --list --verbose` [^5] 를 입력하여 현재 동작중인 리소스들을 확인해보았다. 

<div class="single-image">
  <img width="80%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/20.png" alt="image">
</div>
    
`wsl --shutdown` 을 통해 WSL 내에서 실행 중인 모든 프로세스를 중단하려고 해도 중단되지 않아 `wsl --update` 후 다시 해당 명령어를 입력해보았더니 그 때는 종료가 되었다. 
    
이렇게 Ubuntu 및 docker-desktop을 모두 종료한 상태에서, 필자는 혹시 몰라 컴퓨터를 껐다 켜보았다. 그 후, Docker desktop을 “관리자 권한으로 실행”해보았다. 그랬더니 다행히 무한 로딩 문제 없이 첫 화면이 떴다. 
    
그 후 나중에 Docker desktop을 관리자 권한으로 실행하지 않고 아이콘 더블클릭으로 실행해도 잘 되었다. 정확한 원인은 모르겠지만, 아무래도 `wsl --update` 를 통해 WSL을 최신 버전으로 업데이트하지 않아서 발생한 문제인 것 같기도 하다. 
</div>
</details>

<details id="ref-3">
<summary>
    <strong>
        <i>참고) Docker Desktop 실행 후에도 리눅스에서 docker 명령어가 듣지 않는 현상.</i>
    </strong>
    <a href="#ref-3" class="material-symbols-outlined">link</a>
</summary>
<div markdown="1">
Docker Desktop을 키고 도커 엔진을 실행시키고 있는 상태에서, 리눅스에서 아래와 같이 docker 명령어를 정상적으로 입력했음에도 에러 메시지를 내뿜는다면, 
    
```bash
failed to connect to the docker API at unix:///var/run/docker.sock; check if the path is correct and if the daemon is running: dial unix /var/run/docker.sock: connect: no such file or directory
```
    
아래와 같은 과정을 시도해보자. 
    
1. Docker Desktop 프로그램에서 맨 위 오른쪽의 톱니바퀴 모양의 “설정” 버튼 클릭 후 등장하는 화면에서 왼쪽 사이드바에서 “Resources” 클릭 ⇒ 중앙 화면에서 “WSL integration” 클릭 후, “Enable integration with my default WSL distro”와 “Enable integration with additional distros” 항목에 체크 표시들이 되어 있지 않다면 이를 체크 표시한다. 그 후 화면 우하단에 있는 “Apply & restart”를 클릭하여 변경된 설정을 적용한다. 
        
    ![wsl2에서 docker 명령어 실행이 안될 때의 해결책 1.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/21.png)

2. 설정 변경 적용 후 Docker Destkop이 자동으로 껐다가 다시 켜졌을 것이다. 도커 엔진이 실행 중인지 확인한 후, 리눅스 터미널 창을 다시 확인한다. 혹시 모르니 기존에 켜져 있던 리눅스 터미널 창이 있다면 끄고 새로운 창을 켜서 시도해보길 바란다. 

3. 다음과 같이 도커 명령어들이 정상적으로 작동한다면 문제 해결.
        
    <div class="single-image">
      <img width="80%" src="/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/22.png" alt="image">
    </div>
        
    ![docker desktop 설정 변경 후 docker 명령어가 잘 듣는다 2.png](/images/2026-07-12/wsl2-overview-and-how-to-use-with-docker/23.png)
</div>
</details>

---

References

[1] [2.2. WSL 설치와 실행](https://wikidocs.net/219899)

[2] [[Linux] Windows 11에서 WSL2 설치하고 VSCode 연동하기](https://cuffyluv.tistory.com/245)

[3] [WSL 설치](https://learn.microsoft.com/ko-kr/windows/wsl/install)

[4] [[WSL2] Windows 11에서 WSL2 설치하기](https://velog.io/@gynchoi17/WSL2-Windows-11%EC%97%90%EC%84%9C-WSL2-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0)

[5] [[ Windows ] docker desktop 설치하기 (WSL2 Ubuntu 활용)](https://goddaehee.tistory.com/313)

[6] [Windows에서 Docker Desktop 설치하기](https://loui3.tistory.com/278)

[7] docker desktop 무한로딩 문제

[[아카이브] Windows11에서 도커 테스크탑 실행 무한대기 문제 발생 시 필수 의례사항](https://khr2033.tistory.com/274)

[8] WSL 개념 및 구조

[2.1. WSL 2의 구조](https://wikidocs.net/221054)

[9] WSL 개념 및 구조

[WSL2는 정말 Windows 커널 옆에서 실행될까? 절반만 맞는 이야기 🧐](https://gasbugs.tistory.com/614)

[10] Microsoft 공식. WSL 관련 설명. 

[Linux용 Windows 하위 시스템란?](https://learn.microsoft.com/ko-kr/windows/wsl/about)

[11] [WSL 2 의 일반 사용](https://medium.com/@cratios48/wsl-2-%EC%9D%98-%EC%9D%BC%EB%B0%98-%EC%82%AC%EC%9A%A9-9e8dfc75efae)

[12] [나무위키 - WSL](https://namu.wiki/w/Unix/Microsoft%20Windows?from=WSL%28Windows%29#s-6)

[13] Microsoft 공식 유튜브 영상. WSL 아키텍처 그림도 포함되어 있다. 

[The new Windows subsystem for Linux architecture: a deep dive - BRK3068](https://www.youtube.com/watch?v=lwhMThePdIo)

[14] [나무위키 - Hyper-V](https://namu.wiki/w/Hyper-V)

[15] Microsoft 공식. Hyper-v 아키텍처. 

[Hyper-v 아키텍처](https://learn.microsoft.com/ko-kr/windows-server/virtualization/hyper-v/architecture)

[16] hyper-v 설명. 

[Hyper-V에 대하여](https://benstagram.tistory.com/675)

[17] 참고) Mircrosoft 공식 - WSL 관련 FAQ

[FAQ's about Windows Subsystem for Linux](https://learn.microsoft.com/en-us/windows/wsl/faq)

---

[^1]: Linux, Docker에 대해서는 관련 이론, 지식, 실습 등은 다른 글들에서 다룰 예정. 다만 WSL 자체는 무엇인지는 알아야하기에 해당 개념은 이 글에서 소개할 예정. 

[^2]: Windows 운영체제의 커널 정식 명칭이 “Windows NT kernel”이라 불린다. 그냥 Windows 운영체제의 커널이라 보면 되겠다. 

[^3]: 즉, 하이퍼바이저 위에 윈도우 운영체제와 리눅스 운영체제가 올라가 있는 구조라 사실상 Host OS는 없고 모두 Guest OS라고 봐야 한다. 그리고 실제로 Hyper-V는 1형 하이퍼바이저로 분류된다. 

[^4]: 사실 필자가 참고한 몇몇 자료들 중 어떤 곳에서는 “Hyper-V 체크박스에 체크하여 활성화시켜야한다”라고 말하는 곳도 있고, 또 어떤 곳은 아예 해당 설정 자체를 언급하지 않는 곳도 있었다. 필자의 경우, 몇몇 참고자료들의 설명대로 해당 체크박스는 건드리지 않고 다른 설정들을 따라 했었다. 그러나 WSL 2를 다 설치하고 나중에 다시 해당 설정창에 가보니 이미 해당 체크박스에 체크가 되어있었다. 이를 미루어볼 때, WSL 2 사용을 위해선 Hyper-V 활성화가 필수라고 볼 수 있다. 다만 WSL 2 설치 과정에서 사용자가 직접 해당 설정을 하지 않아도 자동으로 설정되는 것으로 보인다. 

[^5]: `wsl -l -v` 로 해도 동작한다.