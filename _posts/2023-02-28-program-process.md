---
layout: single
title:  "[Python] 프로그램과 프로세스 (1)"
categories: ["Python"]
tag: ["Python", "program", "process", "system"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

# 개요

어떤 하나의 프로그램을 실행시키면, 운영 체제에서는 그에 해당하는 하나의 프로세스를 생성한다. 여기서 프로세스는 실행 중에 있는 프로그램을 의미하며, 한 프로그램으로부터 여러 개의 프로세스가 형성될 수 있다. 프로세스는 CPU, 메모리, 디스크 공간 등의 시스템 자원과 운영 체제의 커널(kernel, 파일, 네트워크 연결 등등)의 데이터 구조를 사용한다. 프로세스는 서로 독립적이라서 상대 프로세스에서 무슨 일이 일어나는지 알 수 없으며, 또한 간섭도 할 수 없다. 운영체제는 모든 실행중인 프로세스들을 추적한다. 그러면서 하나의 프로세스에 실행 시간을 줬다가 다른 프로세스에 주는 행위를 반복한다. 이를 통해 작업들을 공평하게 각각의 프로세스에 분배시켜서 유저의 요청에 반응할 수 있도록 되어 있다. 프로세스라는 것은 windows 운영체제를 가지는 컴퓨터의 경우, “작업 관리자”의 “프로세스” 탭을 눌러보면 리스트들이 뜨는데 그것이 모두 프로세스들이다. 만약 현재 크롬 브라우저를 킨 상태라면 해당 프로세스가 그 목록에 뜰 것이다. 

파이썬의 os모듈은 프로그램의 프로세스 안에 있는 데이터로의 접근을 할 수 있도록 해준다. 예를 들면 다음처럼 프로세스의 ID와 현재 작업 중인 디렉토리의 주소를 얻을 수 있다.

```python
import os

print(os.getpid())  # process ID
print(os.getcwd())  # 현재 작업중인 디렉토리
```

```python
19200
C:\python2\python_basic
```

거의 대부분의 운영체제에서 사용자는 운영체제와 명령 줄 인터페이스 (command line interface, CLI)를 통해 상호작용할 수 있다. 여기서 CLI은 텍스트 터미널을 통해 사용자가 컴퓨터와 상호 작용하는 방식을 의미한다. 윈도우의 cmd와 shell, Mac OS의 Terminal 창 등이 대표적 예이다. cmd 등을 통해서 컴퓨터에게 명령 줄을 전달하고 이를 실행하게 하면 2가지 결과를 얻게 된다. 

- 명령어 실행 상태 코드 (status code): 해당 명령어 실행이 성공했는지 아닌지를 알려줌
- 명령어 실행 결과물(output): 해당 명령어가 성공적으로 실행됐을 때 그 결과물.

파이썬에서도 CLI와 상호작용할 수 있는 기능을 제공한다. 초창기 파이썬 버전에서는 명령어를 os.system(), os.popen().read() 등의 함수들을 통해 실행시켰다. 그러나 파이썬 2.4 버전 이후로부터는 subprocess라고 하는 모듈 사용을 권고하기 시작했다. 

# subprocess 모듈

파이썬 표준 라이브러리 중 하나인 subprocess 모듈은 새로운 프로세스를 생성시킬 수 있고, 이 프로세스들의 input, output, error pipe에 연결할 수 있게 한다. 그 후 status code를 반환시킨다. 해당 모듈은 예전에 쓰이던 os.system()과 같은 오래된 함수들을 대체하기 위해 등장하였다. 

## subprocess 모듈에 존재하는 몇 가지 함수들

해당 모듈에 존재하는 몇 가지 함수들에 대해서 살펴보자.

- subprocess.run() : 파이썬 3.5에서 새로 나왔다. 사용자로부터 입력된 명령어를 실행시켜주고, 해당 명령어가 실행되고 나온 결과물이 포함된 CompletedProcess 라는 클래스의 인스턴스를 반환한다.
- subprocess.call() : 사용자로부터 명시된 명령어를 실행시키고 실행 상태 코드를 반환한다.
- subprocess.check_call() : 파이썬 2.5 버전에서 새로 나왔다. 명시된 명령어를 실행시키고, 실행이 성공적으로 이루어졌다면 상태 코드를 반환하고, 그렇지 않을 경우 에러를 반환한다. 이 기능은 subprocess.run(…, check=True)와 같다.
- subprocess.check_output() : 파이썬 2.7에서 추가됨. 명시된 명령어를 실행시킨다. 만약 실행 상태 코드가 0이면 실행 결과를, 그렇지 않으면 예외를 반환한다.
- subprocess.getoutput(cmd) : 문자열 포맷으로 명령어를 받아 실행시키고, 그 실행결과를 반환한다.
- subprocess.getstatusoutput(cmd) : cmd 명령어를 실행시키고 그 결과로 (명령어 실행 상태, 명령어 실행 결과물) 형태의 튜플을 반환시킨다.

파이썬 3.5 이상에서는 “그냥 다 필요없고, subprocess.run() 만 쓰세요”라고 권고하고 있다. 그 이전 버전에서는 subprocess.call()이 많이 쓰인다고 한다. 

위에 소개된 함수들은 모두 subprocess.Popen이라는 것을 토대로 작성되었다. 따라서 만일 위에서 소개된 함수들보다 더 복잡한 기능을 할 수 있는 함수를 원한다면 subprocess.Popen을 사용할 수 있다. 

## subprocess 함수들에 들어가는 매개변수들

이번에는 위에서 소개된 함수들에 들어가는 매개변수들에 대해서 살펴보자.

```python
subprocess.run(args, *, stdin=None, input=None, stdout=None, stderr=None, shell=False, timeout=None, check=False, universal_newlines=False)
 
subprocess.call(args, *, stdin=None, stdout=None, stderr=None, shell=False, timeout=None)
 
subprocess.check_call(args, *, stdin=None, stdout=None, stderr=None, shell=False, timeout=None)
 
subprocess.check_output(args, *, stdin=None, stderr=None, shell=False, universal_newlines=False, timeout=None)
 
subprocess.getstatusoutput(cmd)
 
subprocess.getoutput(cmd)
```

| Parameters | Explanation |
| --- | --- |
| args | 실행시킬 명령어. 보통 [’df’, ‘-Th’], (’df’, ‘-Th’)와 같이 문자열들의 시퀀스 형태로 쓰이며, ‘df - Th’처럼 문자열 자체로 쓸 수도 있다. 그러나 이 경우 쉘 매개변수의 값을 True로 지정해야 한다. |
| shell | 해당 매개변수에 True를 넣으면, 명령어가 쉘을 통해서 실행된다. 이는 쉘에서 제공하는 기능인 pipe, 와일드카드 등의 여러 기능을 사용할 수 있게 한다. |
| check | 해당 파라미터가 True로 설정되어있고, 만약 명령어를 실행하는 프로세스가 0이 아닌 상태 코드로 종료되었다면, 반환되는 에외 객체에 파라미터들과 종료 상태 코드, stdout, stderr 등을 포함시킨다. 한 마디로 “왜 에러가 발생했죠?”에 대한 대답을 얻을 수 있는 것이다.  |
| stdout, stderr | run() 함수는 기본적으로는 일반적인 output과 error output을 포착하지 않는다. 이러한 내용을 얻기 위해 subprocess.PIPE를 파라미터로 넘기면, 반환되는 CompletedProcess 클래스 인스턴스로부터 stdout, stderr 속성을 넘길 수 있거나 그에 해당하는 내용을 capture할 수 있다. call(), check_call() 함수는 해당 클래스 인스턴스를 반환하지 않기에 subprocess.PIPE를 쓸 수 없다. check_output()도 마찬가지이긴 하나, 만약 결과물에 에러에 관한 정보를 포함시키고 싶다면 stderr=subprocess.STDOUT을 넘기면 된다. |
| input | 해당 파라미터는 Popen.communicate()로 넘겨진다. 보통의 경우, 해당 파라미터 값은 바이트의 시퀀스이어야 하고, 만약 universal_newline=True일 경우, 문자열이어야 한다.  |
| universal_newlines | input, output 데이터 포맷에 영향을 준다. 해당 파라미터는 기본적으로 False로 설정되어 있는데, 이 경우 stdout, stderr의 output이 바이트 시퀀스이고, True일 경우 문자열 포맷으로 반환된다.  |

## subprocess.CompletedProcess 클래스

subprocess.run() 함수의 반환값은 앞서 말했듯 subprocess.CompletedProcess라 하는 클래스 인스턴스이다. 이는 run()이란 함수 자체가 파이썬 3.5 이후로 나온 것이라 해당 클래스도 3.5 이후에만 존재한다. 해당 클래스는 실행이 종료된 프로세스의 상태 정보를 포함하며, 다음의 속성들도 포함한다.

- args: 프로세스를 로드하기 위해 사용된 인자. list 또는 string 포맷이다.
- returncode: 하위 프로세스의 종료 상태 코드(exit status code). 보통 exit status code가 0이면 프로세스가 성공적으로 작동했다는 뜻이고, -N처럼 음수일 경우 하위 프로세스가 signal N에 의해 종료되었음을 의미한다.
- stdout: 하위 프로세스에서 캡쳐된 stdout을 의미. 보통 바이트 시퀀스로 표현되거나 만약 run() 함수에서 universal_newlines=True일 경우 문자열 포맷이다. run() 함수에 stderr=subprocess.STDOUT가 들어가면 stdout과 stderr가 하나의 속성으로 결합되고, stderr는 None이 된다.
- stderr: 하위 프로세서에서 캡쳐된 stderr. 역시 바이트 시퀀스이거나 문자열 형태이다. 만약 stderr가 캡쳐되었으면 해당 값은 None이 된다.
- check_returncode(): 만약 returncode가 0이 아닌 값이라면, 해당 메서드가 CalledProcessError 예외를 반환한다.

## subprocess 예제

windows 10 운영체제에서 “현재 디렉토리”를 의미하는 “dir” 명령어를 이용해보겠다. 리눅스와 같은 다른 운영체제에서는 해당 명령어가 ‘ls’이므로 windows에 해당 명령어를 쓰면 에러가 나니 주의하자. 그리고 만약 앞서 소개한 함수 실행 시 다음과 같은 에러가 뜬다면,

```python
FileNotFoundError
[WinError 2] 지정된 파일을 찾을 수 없습니다
```

shell=True 파라미터를 넣는다면 해당 에러가 나타나지 않음을 확인하였으니 참고바람.

- run()

```python
import subprocess
subprocess.run(["dir"], shell=True)
```

```python
C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: C237-F43B

 C:\python2\python_basic 디렉터리

2023-03-02  오후 07:00    <DIR>          .
2023-03-02  오후 07:00    <DIR>          ..
2022-11-13  오전 06:54    <DIR>          .vscode
2023-02-13  오후 02:39                 0 2022-12-31.txt
2023-02-24  오후 06:11               167 bottle1.py
2023-02-24  오후 06:43               337 bottle2.py
2023-02-24  오후 06:48               314 bottle_test.py
2023-03-02  오후 06:51             8,767 dataset.csv
2023-02-27  오전 09:03               387 flask1.py
2023-02-27  오전 09:13               297 flask2.py
(생략)
```

```python
import subprocess

subprocess.run("exit 1", shell=True, check=True)
```

```python
CalledProcessError
Command 'exit 1' returned non-zero exit status 1.
  File "C:\python2\python_basic\study.py", line 3, in <module>
    subprocess.run("exit 1", shell=True, check=True)
subprocess.CalledProcessError: Command 'exit 1' returned non-zero exit status 1.
```

위 예제에서는 run() 함수에 check=True를 설정해놓고 0이 아닌 상태 코드를 명령어로 입력했으므로 그에 대한 에러를 발생시킨 것이다. 

- call()

```python
import subprocess

subprocess.call(['dir'], shell=True)
```

```python
C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: C237-F43B

 C:\python2\python_basic 디렉터리

2023-03-02  오후 07:00    <DIR>          .
2023-03-02  오후 07:00    <DIR>          ..
2022-11-13  오전 06:54    <DIR>          .vscode
2023-02-13  오후 02:39                 0 2022-12-31.txt
2023-02-24  오후 06:11               167 bottle1.py
2023-02-24  오후 06:43               337 bottle2.py
2023-02-24  오후 06:48               314 bottle_test.py
2023-03-02  오후 06:51             8,767 dataset.csv
(생략)
```

run() 함수 실행 결과와 같음을 알 수 있다.

- check_output()

```python
import subprocess

result = subprocess.check_output(["dir"], shell=True)
print(result)
```

```python
b' C \xb5\xe5\xb6\xf3\xc0\xcc\xba\xea\xc0\xc7 \xba\xbc\xb7\xfd\xbf\xa1\xb4\xc2 \xc0\xcc\xb8\xa7\xc0\xcc \xbe\xf8\xbd\xc0\xb4\xcf\xb4\xd9.\r\n \xba\xbc\xb7\xfd \xc0\xcf\xb7\xc3 
\xb9\xf8\xc8\xa3: C237-F43B\r\n\r\n C:\\python2\\python_basic \xb5\xf0\xb7\xba\xc5\xcd\xb8\xae\r\n\r\n2023-03-02  \xbf\xc0\xc8\xc4 07:00    <DIR>          .\r\n2023-03-02  \xbf\xc0\xc8\xc4 07:00    <DIR>          ..\r\n2022-11-13  \xbf\xc0\xc0\xfc 06:54    <DIR>  
        .vscode\r\n2023-02-13  \xbf\xc0\xc8\xc4 02:39                 0 2022-12-31.txt\r\n2023-02-24  \xbf\xc0\xc8\xc4 06:11               167 bottle1.py\r\n2023-02-24  \xbf\xc0\xc8\xc4 06:43               337 bottle2.py\r\n2023-02-24  \xbf\xc0\xc8\xc4 06:48      
         314 bottle_test.py\r\n2023-03-02  \xbf\xc0\xc8\xc4 06:51             8,767 dataset.csv\r\n2023-02-27  \xbf\xc0\xc0\xfc 09:03               387 flask1.py\r\n2023-02-27 
 \xbf\xc0\xc0\xfc 09:13               297 flask2.py\r\n2023-02-27  \xbf\xc0\xc0\xfc 09:23               349 flask3a.py\r\n2023-02-27  \xbf\xc0\xc0\xfc 09:30               409 flask3b.py\r\n2023-02-27  \xbf\xc0\xc0\xfc 09:37               418 flask3c.py\r\n2023-02-21  \xbf\xc0\xc8\xc4 09:16             1,618 jsonplaceholder_test.py\r\n2023-02-07  \xbf\xc0\xc8\xc4 06:46                 0 json_fake_test.json\r\n2023-02-27  \xbf\xc0\xc8\xc4 06:57               475 links.py\r\n2022-12-27  \xbf\xc0\xc8\xc4 06:37               365 lotto_generator.py\r\n2022-12-27  \xbf\xc0\xc8\xc4 07:42    <DIR>          our_phone_web\r\n2023-01-21  \xbf\xc0\xc8\xc4 03:41               119 receipt.csv\r\n2022-12-27  \xbf\xc0\xc8\xc4 06:57               113 reporter.py\r\n2023-02-28  \xbf\xc0\xc8\xc4 06:15                53 some_link.txt\r\n2023-01-19  \xbf\xc0\xc8\xc4 07:12               270 some_sayings.txt\r\n2023-03-04  \xbf\xc0\xc0\xfc 04:00                89 study.py\r\n2023-02-28  \xbf\xc0\xc8\xc4 05:54    <SYMLINK>      symbolic_link.txt [file_text.txt]\r\n2023-02-27  \xbf\xc0\xc0\xfc 09:20    <DIR>          templates\r\n2022-12-27  \xbf\xc0\xc8\xc4 05:57                41 test1.py\r\n2022-12-30  \xbf\xc0\xc8\xc4 01:38    <DIR>  
        test_folder\r\n2023-02-07  \xbf\xc0\xc8\xc4 01:42               380 user_data.json\r\n2023-02-27  \xbf\xc0\xc0\xfc 08:59    <DIR>          __pycache__\r\n
22\xb0\xb3 \xc6\xc4\xc0\xcf              14,968 \xb9\xd9\xc0\xcc\xc6\xae\r\n
   7\xb0\xb3 \xb5\xf0\xb7\xba\xc5\xcd\xb8\xae  61,107,982,336 \xb9\xd9\xc0\xcc\xc6\xae \xb3\xb2\xc0\xbd\r\n'
```

```python
import subprocess

result = subprocess.check_output(["dir"], shell=True, universal_newlines=True)
print(result)
```

```python
C 드라이브의 볼륨에는 이름이 없습니다.
 볼륨 일련 번호: C237-F43B

 C:\python2\python_basic 디렉터리

2023-03-02  오후 07:00    <DIR>          .
2023-03-02  오후 07:00    <DIR>          ..
2022-11-13  오전 06:54    <DIR>          .vscode
2023-02-13  오후 02:39                 0 2022-12-31.txt
2023-02-24  오후 06:11               167 bottle1.py
2023-02-24  오후 06:43               337 bottle2.py
(생략)
```

## subprocess.Popen

해당 클래스는 새 프로세스에서 subroutine을 실행시킬 때 쓰인다(하위 프로세스를 실행시킨다는 뜻인 것 같다). 위에서 소개한 함수들만으로는 어떤 특정 기능을 수행할 수 없을 때 해당 클래스를 이용하면 된다. 

해당 클래스의 구조는 다음과 같이 생겼다.

```python
class subprocess.Popen(args, bufsize=-1, executable=None, stdin=None, stdout=None, stderr=None, 
    preexec_fn=None, close_fds=True, shell=False, cwd=None, env=None, universal_newlines=False,
    startup_info=None, creationflags=0, restore_signals=True, start_new_session=False, pass_fds=())
```

각 매개변수들의 설명은 다음과 같다.

- args: 실행할 명령어. 문자열 포맷도 될 수 있고, 명령어 파라미터들의 시퀀스일 수도 있다. 문자열일 경우 해당 명령어 해석은 플랫폼에 의존하므로 일반적으로는 시퀀스 형태를 추천한다.
- bufsize: cache 전략을 명시함. 0은 buffering이 없는 것, 1은 line buffering, 1보다 큰 수는 buffer size, 음수는 시스템의 기본 buffer strategy를 쓰겠다는 의미.
- stdin, stdout, stderr : 프로그램의 표준 입출력와 에러를 표시함.
- shell: 실행할 프로그램으로 쉘을 쓸 것인지 알려줄 때 사용. True일 경우 args 파라미터는 시퀀스 형태보단 문자열 형태로 쓰는 것을 추천.
- cwd: None이 아닐 경우, subprocess를 실행하기 전에 현재 작업중인 디렉토리를 바꾼다.

이 외 다른 매개변수들에 대한 설명은 References의 [4] 참고. 

- subprocess.Popen 클래스의 메서드들

| Method | 설명 |
| --- | --- |
| Popen.poll() | 하위 프로세스 (명령어)가 실행되었는지를 확인할 때 쓰인다. 실행되지 않았다면 None을 반환하고, 프로세스 종료 후 상태 코드를 반환한다. |
| Popen.wait(timeout=None) | 하위 프로세스가 종료될 때까지 기다리고, 상태 코드를 반환한다. timeout에 명시된 시간 이후로도 해당 프로세스가 종료되지 않을 경우, TimeoutExpired 예외가 발생됨 |
| Popen.communicate(input=None, timeout=None) | 프로세스와 상호작용할 때 쓰임. 예를 들어 stdin으로 데이터를 보낸다든가, stdout, stderr로부터 데이터를 읽어들인다든가. |
| Popen.send_signal(signal) | 하위 프로세스에 특정 signal을 보낸다 |
| Popen.terminate() | 하위 프로세스를 중단한다. |
| Popen.kill() | 하위 프로세스를 종료시킨다. |

- communicate() 메서드에 대한 보충 설명
1. input 파라미터는 하위 프로세스에 보낼 데이터여야 한다. 만약 보낼 데이터가 없다면 None으로 설정한다. 해당 파라미터의 데이터 자료형은 byte string이어야 하나, universal_newline = True일 경우 문자열이어야 한다.
2. (stdout_data, stderr_data)의 튜플로 반환한다. 해당 자료형은 universal_newline에 따라 바이트 또는 문자열이다.
3. 프로세스가 timeout에 명시된 시간 이후에도 종료되지 않는다면 TimeoutExpired 예외를 받으며, 이를 이용해 output 데이터를 잃지 않으면서 다시 communication을 시도할 수 있다. 그러나 이 때 timeout 이후에도 하위 프로세스는 종료되지 않는다. 따라서 이 경우 명시적으로 하위 프로세스를 종료시키는 것이 좋다. (Popen.kill()을 통해서)
4. 보낼 데이터가 너무 크거나 무한일 경우, 해당 메서드를 사용할 수 없다. 

subprocess.Popen 클래스에 대한 예제는 Reference의 [4] 참고

# 보충 설명

## 파이프(pipe)

앞서 Popen을 설명했는데, 사실 Pipe Open의 줄임말이다. 프로세스를 실행할 때 파이프를 뚫어서 그 사이로 데이터가 드나들도록 해서 데이터의 입출력을 조절할 수 있다. 

파이썬 프로그램이 동작하기 위해서는 이를 위한 스크립트를 실행시키고, 그에 따른 결과값을 사용자가 보는 쉘에 표시한다. 이 때 스크립트를 실행시키면 한 개 또는 그 이상의 프로세스가 동작한다. 예를 들어 스크립트를 실행 시킬 때 프로세스 A, B 두 개가 실행된다면, 일반적인 상황에서는 프로세스 A로부터 발생된 데이터 결과값들은 바로 쉘로 넘어가 사용자 화면에 표시된다. 그러나 두 프로세스 간에 Pipe를 뚫으면 프로세스 A에서 발생한 데이터를 쉘에 넘기지 않고 프로세스 B로 넘김으로서 두 프로세스가 서로 데이터 통신을 할 수 있는 것이다. 즉, pipe를 뚫음으로서 stdout, stderr 값을 redirect 시키는 것이다. 앞서 설명한 Popen의 communication() 메서드가 특정 프로세스와 하위 프로세스 간에 데이터 통신을 할 수 있도록 두 프로세스 사이에 pipe를 형성시키는 대표적 예이다. 

communication()을 이용한 예제는 References의 [5] 참고.

## stdin, stdout, stderr

- 표준입력 (standard input): 프로그램에 들어가는 데이터 (주로 텍스트)를 말함. 프로그램이 read 기능 사용을 이용한 데이터 입력을 통해 데이터 전송을 요구할 수 있으나, 모든 프로그램이 입력을 요구하는 것은 아니다. redirect 되지 않는 한, 입력은 키보드로부터 입력받는다.
- 표준출력 (standard output): 프로그램이 출력 데이터를 쓰는 스트림(stream)을 의미. 프로그램은 write 기능을 통해 데이터 전송을 요청할 수 있다. 그러나 모든 프로그램이 출력을 발생시키지는 않는다. redirect되지 않는 한, 프로그램을 작동시킨 텍스트 터미널에 표시된다.
- 표준에러 (standard error): 에러 메시지나 진단 데이터를 출력하기 위한 출력 스트림으로, 표준 출력과는 독립적이며, 따라서 표준 출력과 표준 에러는 서로 개별적으로 redirected 될 수 있다. 일반적으로 표준에러가 출력되는 곳은 프로그램을 실행시킨 터미널이다. 예를 들어, 파이프라인에 있는 프로그램의 출력은 다음 프로그램의 입력으로 redirect 될 수 있으나, 각각의 프로그램들의 에러들은 터미널창으로 바로 전달될 수 있다.

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [[운영체제] 프로세스란? (스케줄링, 메모리구조, 상태변화)](https://blockdmask.tistory.com/22)

[3] [[CLI] CLI 기본 개념 및 사용법](https://medium.com/@psychet_learn/cli-cli-기본-개념-및-사용법-c8d000ebc162)

[4] [Python system interaction (subprocess) tutorial](https://pyzone.dev/python-system-interaction-subprocess-tutorial/)

[5] [[Python subprocess - 3] 파이썬에서 외부 프로세스 실행 및 입출력 제어 (심화개념설명)](https://blog.naver.com/sagala_soske/222131573917)

[6] [Strandard Input / Output / Error 정의와 의미](https://srzero.tistory.com/entry/Strandard-Input-Output-Error-정의와-의미)