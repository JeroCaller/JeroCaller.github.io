---
title: "CMD, PowerShell, WSL2에서 경로 숨기기"
category: "Tips"
tag: ["Shell", "CMD", "PowerShell", "WSL2", "Linux", "Bash"]
---

CMD, PowerShell, Ubuntu shell 등을 사용할 때 prompt에 항상 사용자 이름이나 호스트에 노트북 이름이 뜬다. 평소에는 괜찮지만 새로운 개념을 배웠을 때 이를 기록하고 기술블로그에 업로드하는 필자의 입장에서는 조금 난감하다. 사용자 이름이 내 실명이다보니 캡쳐본에서는 이를 그림판을 이용해 가리는 좀 귀찮은 작업이 추가되어야 했다. 이러한 귀찮은 작업을 피하기 위해 아예 프롬프트에 내 사용자명이 안 뜨도록 하는 방법을 찾아보았고, 이를 여기에 기록하고자 한다. 

# Windows에서 CMD(명령어 프롬프트 창)에서의 적용

CMD에서는 간단하게 `prompt $G` 를 입력하면 다음과 같이 프롬프트가 변경된다. 

<div class="single-image">
  <img width="50%" src="/images/2026-07-13/CMD-PowerShell-WSL2에서%20경로%20숨기기/1.png" alt="image">
</div>

다시 경로가 나오도록 프롬프트를 되돌리고자 한다면 `prompt $P$G` 를 입력하면 된다. 여기서 `$G` 는 `>` 기호를 뜻한다(Greater than). 그리고 `$P` 는 현재 경로를 의미한다. 따라서 `prompt $P$G` 의 경우에는 프롬프트에 먼저 현재 경로가 출력되게끔 한 후, 그 뒤에 `>` 기호가 출력되게끔 하는 것이다. 

그 외 다른 기호들이 있으니 좀 더 다양한 프롬프트로 꾸미고 싶다면 다음의 사이트를 참고하면 되겠다.

- [https://learn.microsoft.com/ko-kr/windows-server/administration/windows-commands/prompt](https://learn.microsoft.com/ko-kr/windows-server/administration/windows-commands/prompt)

다만 이 방식은 현재 터미널 창에서만 유지되며, 새로운 터미널을 키면 원래 프롬프트 문자열로 보인다. 

# PowerShell에서의 적용

PowerShell에서는 다음과 같은 명령어를 입력하면 된다. 

```powershell
function prompt { "PS (여기에 원하는 프롬프트 문자열 입력) > " }
```

![powershell 프롬프트 변경.png](/images/2026-07-13/CMD-PowerShell-WSL2에서%20경로%20숨기기/2.png)

다만 이 방법은 새로운 PowerShell을 키면 해당 창에서는 적용되지 않는 일회용 방법이라 볼 수 있다. 만약 새 PowerShell을 켜도 자동으로 해당 프롬프트가 적용되길 원한다면 별도의 설정 파일을 이용해야한다. 

PowerShell에서의 설정 파일은 `$PROFILE` 에 있다. `$PROFILE` 명령어를 입력하면 현재 `$PROFILE` 이라는 설정 파일의 위치를 볼 수 있고, `notepad $PROFILE` 이라 하면 해당 설정 파일을 메모장에서 열어볼 수 있다. 다만, 맨 처음 설정 파일을 건드리지 않았다면 `$PROFILE` 명령어를 통해 그 위치는 뜰지언정 `notepad $PROFILE` 명령어를 실행하면 해당 파일이 없어 열 수 없다는 식의 메시지가 뜰 수 있다. 이럴 때는 다음의 명령어를 통해 새로운 프로필 파일을 만든다.

```powershell
New-Item -ItemType file -Path $PROFILE -Force
```

이 후 다시 `notepad $PROFILE` 을 입력하면 메모장이 뜰 것이다. 

필자의 경우 해당 텍스트 파일에 다음과 같은 명령어를 넣었다. 

```powershell
# 1. 블로그 캡처용 프롬프트 함수
function Set-BlogPrompt {
    function global:prompt { "PS [workspace] > " }
}

# 2. 원래 상태(기본 경로)로 되돌리는 함수
function Set-WorkPrompt {
    function global:prompt {
        # 윈도우 기본 PowerShell 프롬프트 모양을 재현
        "PS $($executionContext.SessionState.Path.CurrentLocation)$('>' * ($nestedPromptLevel + 1)) "
   }
}

# 3. 리눅스처럼 간단하게 부를 수 있는 '별칭(Alias)' 등록
Set-Alias -Name blog -Value Set-BlogPrompt
Set-Alias -Name work -Value Set-WorkPrompt

# [선택 사항] 터미널을 처음 켰을 때 기본으로 work 상태로 시작하고 싶다면 아래 줄 주석 해제
# Set-WorkPrompt
```

필자의 경우에는 PowerShell을 캡처해야할 때와, 현재 경로를 언제든 보고 싶을 때를 구분하여서 위와 같이 구성해보았다. PowerShell을 킨 처음부터 적용되게끔 하고 싶다면 앞서 본 `function prompt { "PS (여기에 원하는 프롬프트 문자열 입력) > " }` 만 입력해도 될 것이다. 메모장을 저장하고 닫는다.

그 후 `. $PROFILE` 을 입력하여 앞서 새로 설정한 설정값들이 현재 터미널 창에도 적용되도록 한다. 

<div class="single-image">
  <img width="60%" src="/images/2026-07-13/CMD-PowerShell-WSL2에서%20경로%20숨기기/3.png" alt="image">
</div>

필자의 경우, 위 사진과 같이 `blog` 라는 커스텀 명령어를 입력하면 현재 경로 위치가 안보이도록 변경되고, 다시 `work` 라는 커스텀 명령어를 입력하면 원래대로 복원되도록 설정하였다. 이제 프롬프트 문자열을 변경하는 긴 명령어를 일일이 매번 치지 않아도 간단한 커스텀 명령어만 치면 되어서 편리해진다. 

# WSL2 Linux shell에서의 적용

Linux shell에서는 홈 디렉토리 위치에 `.bashrc` 라는 설정 파일이 있다. 이 설정 파일을 이용하여 프롬프트 모양을 설정할 수 있다. 

먼저 `nano ~/.bashrc` 명령어를 입력한다. 이는 홈 디렉토리(`~/`)에 있는 `.bashrc` 파일을 `nano` 라는 텍스트 에디터로 여는 명령어이다. 그러면 다음과 같은 화면으로 전환될 것이다. 

![linux shell nano bashrc 첫 모습.png](/images/2026-07-13/CMD-PowerShell-WSL2에서%20경로%20숨기기/4.png)

키보드 화살표 아래키를 쭉 눌러 커서를 맨 아래로 둔다. 기존에 있는 내용들은 건드리지 않고 새로운 명령어만 추가하기 위함이다. 그 후 필자는 다음과 같은 명령어를 입력해두었다.

```bash
# ===== My Custom Commands =====
# Basic prompt shape definition.
alias work='PS1="\[\e[1;32m\]\u@\h\[\e[0m\]:\[\e[1;34m\]\w\[\e[0m\]\$ "'

# When you need to take a picture of this shell but want to hide your username or hostname, use this to hide it.
alias blog='PS1="\[\e[32m\][dev-server]\[\e[m\] \[\e[34m\]\w\[\e[m\] \$ "'
```

필자는 평상시에는 원래 프롬프트가 나오도록 하기 위해 `work` 라는 명령어 별칭을 등록하였고, `blog` 라는 별칭을 별도로 마련하여 쉘에서 `blog` 를 입력하면 `[dev-server] >` 와 같은 형식의 프롬프트가 나오도록 설정한 명령어이다. 별칭이나 프롬프트 문자열은 원하는 거로 바꿔도 무방하다. 

만약 상황에 따라 프롬프트를 변경하는게 아니라 처음부터 끝까지 내가 원하는 커스텀 프롬프트가 나오도록 하고자 하는 경우에는 `alias` 부분은 넣지 말고 `PS1=...` 부분만 넣으면 된다(이때 양쪽의 작은 따옴표 `'` 는 생략한다).

위 명령어에서 나온 각각의 의미를 풀어보면 다음과 같다. 

- `PS1` : Prompt String의 약자라고 한다. 프롬프트 문자열을 커스텀할 때 쓰인다. 맨 처음 쉘을 볼 때 뜨는 프롬프트를 설정한다. `PS2` 라는 것도 있는데, 이건 명령어를 여러 행에 걸쳐 입력할 때 나타나는 프롬프트를 설정하는 변수라고 한다. 아래에 소개할 문자들은 모두 `PS` 변수에 사용될 수 있는 문자들이다.
- `\e` : ASCII escape 문자 (033)
- `\u` : 현재 사용자명
- `\h` : 호스트명
- `\w` : 현재 위치의 절대경로
- `\W` : 현재 위치의 상대 경로
- `[1;32m]` : `;` 기호를 기준으로 왼쪽은 0 또는 1을 입력할 수 있으며, 1은 색을 굵게, 0은 색을 굵게 하지 않을 때 사용한다. `;` 기호의 오른쪽에는 색상 코드로, `32m` 은 초록색을 의미한다. 그 외 다른 색상들에 대해선 이 글 맨 아래의 `References` 에서 다른 글들을 참고.
- `\$` : 현재 사용자가 root면 `#` , 아니면 `$` 로 표시된다.

작성이 끝났으면 `Ctrl` + `O` 를 눌러 저장한다. 그러면 아래 사진과 같이 밑에 하얀줄이 생기고 파일명이 보일 것이다. 여기선 건드리지 않고 엔터키를 누른다. 

![nano 저장 시 파일명.png](/images/2026-07-13/CMD-PowerShell-WSL2에서%20경로%20숨기기/5.png)

그 후, `Ctrl + X` 키를 눌러 빠져나온다. 

앞서 변경된 사항을 shell에 바로 반영하기 위해선 `source ~/.bashrc`  명령어를 입력한다. 그러면 설정이 완료된다. 

이제 쉘에 각각 `work` 와 `blog` 를 번갈아가며 입력해보면 각 명령어에 따라 프롬프트가 바뀌는 것을 볼 수 있다. 

<div class="single-image">
  <img width="60%" src="/images/2026-07-13/CMD-PowerShell-WSL2에서%20경로%20숨기기/6.png" alt="image">
</div>

---

References

[1] [Linux Shell Prompt 글자속성 변경 및 색상지정 - Honey Blog](https://blessu1201.github.io/2020/07/21/shell_prompt.html)

[2] [네이버 블로그 - 리눅스 쉘 스크립트 color codes](https://blog.naver.com/kunks3/114737528)

[3] [ANSI코드를 이용한 Bash Prompt 색상 변경 방법](https://tech-linux.tistory.com/7)

[4] [Microsoft - prompt](https://learn.microsoft.com/ko-kr/windows-server/administration/windows-commands/prompt)

[5] [Linux, PS1쉘변수](https://m.blog.naver.com/dudwo567890/130153102817)

[6] [[Windows] Powershell Profile 사용법](https://cloudest.oopy.io/posting/061)

[7] [[PowerShell 기초] 6. Powershell 프로필 설정](https://ddochea.tistory.com/184)
