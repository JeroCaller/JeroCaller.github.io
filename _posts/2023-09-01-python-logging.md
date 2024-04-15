---
layout: single
title:  "[Python] logging"
categories: ["Python", "logging", "log"]
tag: ["Python", "logging", "log"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
프로그램 실행 도중 에러가 발생하거나 또는 실행 도중 데이터들의 변화를 알고 싶을 때, 또는 그저 사용자가 프로그램 내 어떤 작업을 했는지 추적할 필요가 있을 때 등등의 경우에 이러한 정보들을 따로 파일을 만들어 그곳에 기록하는 방법인 로그(log)가 있다. 이렇게 프로그램 실행 중의 정보들을 기록하는 행위를 로깅(logging)이라 하고, 이 기록 자체를 로그(log)라 한다. 다른 말로 하면, 소프트웨어 실행 시 발생하는 이벤트(데이터의 변화, 사용자로부터의 입력에 의해 발생하는 동작 등)들을 기록하는 것이라 보면 되겠다. 

파이썬에서는 코딩 후 프로그램 실행 시 발생하는 에러의 원인을 파악하기 위해 특정 부분에 print() 코드를 삽입하여 코드의 특정 부분에 어떤 일이 발생하는 지 파악하는 경우가 있을 것이다. 그런데 이러한 경우, 문제를 파악하고 해결한 후라면 이 print() 코드를 지워야할 것이다. 프로그램의 규모가 커지고 복잡해지면 이러한 과정이 꽤 번거로울 수 있다. 하지만 이를 아예 로그 파일에 기록하게 하면 이러한 번거로운 작업 없이 프로그램의 에러 파악 및 해결, 유지 보수 등의 작업에 큰 도움이 될 것이다. 

이 문서에서는 파이썬에서 로그를 작성할 수 있게해주는 내장 모듈인 logging 모듈에 대해 살펴보겠다. 

# 중요도

파이썬 logging 모듈에서는 프로그램 실행 도중 발생하는 이벤트들을 로그에 기록할 때 이벤트에 중요도를 부여한다. 이 중요도는 로깅 수준(level), 심각도(severity)라고도 불리며, 다음의 중요도 계급이 존재한다. 

| level | 용도 | 부여된 숫자 값 |
| --- | --- | --- |
| DEBUG | 이벤트에 대한 상세 정보. 보통은 문제 진단 시에만 필요 | 10 |
| INFO | 프로그램이 예상대로 작동하는지에 대한 확인, 추적하는 용도. | 20 |
| WARNING | 예상치 못한 일이 발생했거나 미래에 발생할 문제를 표시한다. 소프트웨어는 계속 작동하는 상태이다.  | 30 |
| ERROR | 심각한 문제로 인해 소프트웨어가 일부 기능을 수행하지 못한 경우. | 40 |
| CRITICAL | 심각한 에러로, 프로그램 자체가 실행되지 않을 수 있는 경우. | 50 |

위 표는 아래로 갈수록 그 중요도가 높아진다. debug가 가장 낮은 중요도를 가지고, critical이 가장 높은 중요도를 가진다. logging 모듈에서는 특정 수준 이상의 이벤트만 로그에 기록하도록 한다. 기본적으로는 WARNING 수준으로 설정되어 있고, 이 경우, DEBUG, INFO 이벤트는 로그에 기록되지 않으며, WARNING, ERROR, CRITICAL 수준의 이벤트만 로그에 기록된다. 물론 로그에 기록될 최소 수준을 개발자가 직접 설정할 수도 있다. 다음은 그 예를 보여주는 코드이다.

```python
import logging

logging.debug("hello, this is debug level.")
logging.info("this is info.")
logging.warning("조심하세요.")
logging.error("에러 발생. Error occured!")
logging.critical("이거 문제가 너무 심각한데요?")
```
*예제 1-1*
```python
WARNING:root:조심하세요.
ERROR:root:에러 발생. Error occured!
CRITICAL:root:이거 문제가 너무 심각한데요?
```
*예제 1-1 실행결과*

예제 1-1에서 로그에 기록할 최소 중요도를 따로 명시하지 않았으므로, 기본적으로 warning 수준으로 설정된다. 그렇기에 출력결과에서 warning 이상의 중요도 이벤트만 출력됨을 알 수 있다. 

# 파일에 로깅하기

앞서 “개요” 부분에서 언급했듯, 보통은 파일을 만들어 거기에 로그를 저장하는 식으로 활용한다. logging 모듈에서는 basicConfig() 함수를 이용하여 파일에 로깅할 수 있다. 

```python
import logging

logging.basicConfig(
    filename="example.log", 
    encoding='utf-8', 
    level=logging.DEBUG
    )
logging.debug("hello, this is debug level.")
logging.info("this is info.")
logging.warning("조심하세요.")
logging.error("에러 발생. Error occured!")
logging.critical("이거 문제가 너무 심각한데요?")
```
*예제 2-1*

위 예제에서는 basicConfig() 함수에 `filename="example.log"` 라는 키워드 인자를 작성함으로써 `"example.log"` 파일을 만들어 여기에 로그들을 기록하도록 설정할 수 있다. 또다른 키워드 인자로 level=에는 로깅할 최소 중요도를 설정할 수 있다. 여기서는 DEBUG 수준으로 설정하였다. 위 코드를 실행하면 로그들이 터미널창에 출력되지 않고 대신  `"example.log"` 파일이 형성되는데, 해당 파일을 열면 다음처럼 기록되어 있을 것이다. (.log 파일은 사실상 텍스트 파일이라서 윈도우에서 기본적으로 제공하는 메모장 프로그램으로도 열람할 수 있다)

```python
DEBUG:root:hello, this is debug level.
INFO:root:this is info.
WARNING:root:조심하세요.
ERROR:root:에러 발생. Error occured!
CRITICAL:root:이거 문제가 너무 심각한데요?
```
*example.log*

basicConfig() 함수는 기본적으로 파일에 이어서 쓰는 방식으로 설정되어 있다. 따라서 만약 기존 로그 파일을 지우고 새로 쓰고자 한다면 `filemode=’w’`를 키워드 인자로 넘기면 된다. (`filemode=’a’`는 이어쓰기 모드이다)

참고로, 변수 데이터도 메시지 문자열 안에 포맷하여 로깅하는 것도 가능하다. 

```python
import logging

logging.basicConfig(
    filename="example.log", 
    encoding='utf-8', 
    filemode='w', 
    level=logging.DEBUG
    )
some_int_var = 2
some_str_var = 'hi'
some_float_var = 3.141592
logging.debug(f"{some_int_var}, {some_str_var}, {some_float_var}")
```
*예제 2-2*
```python
DEBUG:root:2, hi, 3.141592
```
*example.log*

# 메시지 표시 포맷 변경

메시지가 표시되는 포맷을 변경하려면 basicConfig의 format 키워드 인자를 이용하면 된다. 

```python
import logging

logging.basicConfig(
    filename="example.log", 
    encoding='utf-8', 
    filemode='w', 
    level=logging.DEBUG,
    format='%(levelname)s: %(message)s'  # 추가됨.
    )
logging.debug("hello, this is debug level.")
logging.info("this is info.")
logging.warning("조심하세요.")
logging.error("에러 발생. Error occured!")
logging.critical("이거 문제가 너무 심각한데요?")
```
*예제 3-1*
```python
DEBUG: hello, this is debug level.
INFO: this is info.
WARNING: 조심하세요.
ERROR: 에러 발생. Error occured!
CRITICAL: 이거 문제가 너무 심각한데요?
```
*example.log*

format 인자를 통해 위 실행결과와 같이 자신이 원하는 메시지 표시 방식으로 바꿔서 로깅할 수 있다. 참고로, format 인자에 사용될 수 있는 속성(attribute)에는 대략적으로 다음과 같이 존재한다. 

| 속성 이름 | 포맷 방법 | 설명 |
| --- | --- | --- |
| asctime | %(asctime)s | 사람이 읽을 수 있는 LogRecord가 생성된 시간. 기본적으로는 “2023-09-02 12:48:12,112”의 형태를 가진다. 여기서 쉼표 뒤의 숫자는 밀리 초이다. |
| created | %(created)f | LogRecord가 생성된 시간. |
| filename | %(filename)s | pathname의 파일명 부분. pathname은 로깅을 호출한 소스 파일의 절대 경로 문자열이다. |
| funcName | %(funcName)s | 로깅 호출을 포함하는 함수명. |
| levelname | %(levelname)s | 메시지의 로깅 수준을 텍스트로 나타냄. 예) ‘DEBUG’ |
| levelno | %(levelno)s | 메시지의 로깅 수준을 숫자로 나타냄. 예) DEBUG → 10 |
| lineno | %(lineno)d | 로깅 호출이 일어난 소스 행 번호. (사용 가능한 경우에만) |
| args | (직접 포맷 불필요) | message 생성을 위해 msg에 병합될 튜플 또는 딕셔너리 인자. |
| msg | (직접 포맷 불필요) | 원래 로깅 호출에서 전달된 포맷 문자열. args와 병합하여 message를 만듦.  |
| message | %(message)s | 로그 된 메시지로, msg % args로 계산됨.  |
| module | %(module)s | 모듈명 (filename의 이름 부분) |
| name | %(name)s | 로깅 호출에 사용된 로거(logger)의 이름. |
| pathname | %(pathname)s | 로깅 호출이 일어난 소스 파일의 전체 경로명. (사용 가능한 경우에만) |
| process | %(process)d | 프로세스 ID (사용 가능한 경우에만) |
| processName | %(processName)s | 프로세스 이름 (사용 가능한 경우에만) |

*table 1*

이 외에도 여러 속성이 있는데 자세한 내용은 References [7]의 “LogRecord 어트리뷰트” 참고. 

> LogRecord 객체는 로깅될 때마다 Logger 객체에 의해 자동으로 생성되며, 로그 되는 이벤트와 관련된 정보들을 담는다. 여기에는 이벤트 수준, 로깅 호출한 소스 파일의 전체 경로명, 메시지 등이 포함된다. 위 표에 나온 속성들도 사실 LogRecord 객체의 속성을 의미한다.
> 

## 메시지에 날짜 및 시간 표시

로깅시 메시지에 이벤트가 발생한 날짜 및 시간를 표시하고자 한다면 위 표의 ‘%(asctime)s’을 이용하면 된다. 해당 속성은 날짜 및 시간을 정해진 기본 형식으로 표시하는데, 만약 이 표시 방법을 바꾸고자 한다면 basicConfig() 함수의 datefmt 키워드 인자를 이용하면 된다. 해당 키워드는 time 모듈의 strftime() 함수에서 쓰이는 포맷 형식을 똑같이 사용한다. 

다음은 앞서 살펴본 속성 표의 일부를 활용한 예제 코드이다.

```python
import logging

logging.basicConfig(
    filename="example.log", 
    encoding='utf-8', 
    filemode='w', 
    level=logging.DEBUG,
    format='%(asctime)s %(levelname)s: from module %(module)s: \n%(message)s',
    datefmt="%Y년 %m월 %d일 %H시 %M분 %S초"
    )

logging.debug("hello, this is debug level.")
logging.info("this is info.")
logging.warning("조심하세요.")
logging.error("에러 발생. Error occured!")
logging.critical("이거 문제가 너무 심각한데요?")
```
*예제 3-2*
```python
2023년 09월 02일 01시 20분 04초 DEBUG: from module study: 
hello, this is debug level.
2023년 09월 02일 01시 20분 04초 INFO: from module study: 
this is info.
2023년 09월 02일 01시 20분 04초 WARNING: from module study: 
조심하세요.
2023년 09월 02일 01시 20분 04초 ERROR: from module study: 
에러 발생. Error occured!
2023년 09월 02일 01시 20분 04초 CRITICAL: from module study: 
이거 문제가 너무 심각한데요?
```
*example.log*

# logging 라이브러리를 구성하는 클래스

logging 라이브러리에는 크게 4개의 클래스를 가지고 있다. 

- Logger(로거): 프로그램의 코드에서 접근 가능한 로깅 관련 인터페이스를 제공. 즉 이를 통해 개발자가 로깅 관련 코딩을 작성 및 실행할 수 있다.
- Handler(처리기): 로거에 의해 만들어진 로그 기록을 파일이나 전자 메일 등으로 보내는 역할.
- Filter(필터): 여러 이벤트 중 로깅될 이벤트를 결정하는 데에 더 정밀한 기능을 제공. 즉, 앞서 살펴본 DEBUG, ERROR 등의 수준보다 더 정밀한 기준으로 로그에 기록될 이벤트들을 골라내도록 하는데에 도움을 줌.
- Fomatter(포매터): 로그 최종 출력에서 로그 기록의 배치를 지정.

로그 이벤트 정보는 LogRecord 객체를 통해 위 네 객체 사이에서 전달될 수 있다. 

## Logger

### Logger 클래스의 인스턴스화와 Logger 객체 이름 짓기

로깅을 위해선 우선 Logger 클래스의 인스턴스를 생성하고, 해당 인스턴스의 메서드 호출을 통해 로깅을 수행할 수 있다. Logger 객체는 각각 이름이 있다. Logger 객체 생성과 해당 객체에 이름을 부여하는 코드는 다음과 같다.

```python
logger = logging.getLogger(__name__)
```
*예제 4-1*

위 코드의 getLogger() 함수는 이름을 가진 Logger 객체를 반환한다. Logger 객체의 이름을 지을 때는 다음을 고려한다. 

- 로깅을 사용하는 모듈의 이름으로 로거 이름을 짓는다. 예를 들어 로깅을 사용하는 모듈의 이름이 a.py라면 a를 로거 이름으로 짓는다. 이것은 강제사항은 아니고, 개발자의 선택의 문제이지만 관례적으로는 이렇게 짓는다고 한다.
- 이름에 점(마침표)을 구분기호로 사용하여 로거 계층 구조를 명확하게 표현한다. 예를 들어 어떤 디렉토리에 main.py라는 파일이 있고 해당 파일에서 로깅을 한다면 해당 파일에서의 로거 이름은 main이라 지을 수 있고, 하위 디렉토리에 sublib.py라는 모듈과 그 안에서도 로깅을 사용한다면 해당 파일에서의 로거 이름은 main.sublib라고 짓는다. 이렇게 함으로써 이벤트가 어디서 로깅이 되는지를 명확히 알 수 있게 하고, 부모 로거 객체와 자식 로거 객체를 구분해준다. (main이 부모 로거, main.sublib는 자식 로거)

모듈 이름을 로거 이름으로 사용하기 위해 예제 4-1처럼 __name__을 사용할 수도 있다. 그런데 만약 외부 모듈에서 해당 모듈의 로거를 사용하는 것이 아니라 자체 모듈에서 사용한다면 로거 이름이 “\_\_main\_\_”으로 찍힐 것이다. 이와 같이 로깅을 사용할 모듈과 로거가 작성된 모듈이 같을 경우 모듈 이름을 로거 이름으로 짓기 위해 다음의 코드처럼 작성하는 것을 고려해볼 수도 있겠다.

```python
# study.py
import logging
import os

logger = logging.getLogger(os.path.basename(__file__).split('.')[0])
print(logger.name)
```
*예제 4-2*
```python
study
```
*예제 4-2 출력결과*

Logger 객체의 name 속성을 통해 해당 객체의 이름을 호출할 수도 있다. 

참고로 getLogger() 함수에 아무런 이름도 넘기지 않으면 자동으로 “root”라는 이름으로 설정된다. 또한, 로거 계층의 뿌리를 루트 로거 (root logger)라 한다. 앞선 예에서 “main.sublib”의 “main”이 루트 로거라 볼 수 있겠다. 

### 최소 중요도 설정과 메시지 로깅 메서드

Logger 객체에는 자체적으로 로거가 처리할 최소 중요도의 로그 메시지를 지정할 수 있는데, 이는 Logger.setLevel(logging.DEBUG)와 같이 지정하면 된다. 

또한, 앞서 logging.debug(”msg”)와 같이 Logger 객체 자체에서도 Logger.debug(), Logger.info(), Logger.warning(), Logger.error(), Logger.critical() 메서드를 제공하며, 괄호 안에 메시지를 대입하면 로깅할 수 있다. 모듈 자체에서 제공하는 메서드와 로거 객체에서 제공하는 메서드의 차이는, 모듈 자체에서 제공하는 메서드를 사용하면 이름이 “root”인 로거에서만 로깅을 할수 있으나(예제 1-1 코드와 그 출력결과를 참조), 로거 객체에서 제공하는 메서드를 사용하면 패키지 내 모듈 계층에 따라 로거 계층을 구성하여 각 로거 객체를 통해 로깅 처리를 할 수 있다는 점이다. 

사실, logging 모듈과 Logger 객체 모두 exception() 이라는 메서드를 제공하는데, 이는 error()와 같은 수준을 가지는 점에서 비슷하나, exception() 메서드는 자세한 오류 내용을 담고 있는 traceback을 로깅할 수 있는 것이 차이점이다. 따라서 해당 메서드는 주로 try-except문의 except문 안에서 쓰일 수 있다. 

```python
import logging

logger = logging.getLogger('a')
logger.setLevel(logging.DEBUG)

try:
    some_var = int('hi')
except ValueError as e:
    logger.error(e)
    print("="*40)
    logger.exception(e)
    print("="*40)
    logging.exception(e)
```
*예제 4-3*
```python
invalid literal for int() with base 10: 'hi'
========================================
invalid literal for int() with base 10: 'hi'
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 7, in <module>
    some_var = int('hi')
ValueError: invalid literal for int() with base 10: 'hi'
========================================
ERROR:root:invalid literal for int() with base 10: 'hi'
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 7, in <module>
    some_var = int('hi')
ValueError: invalid literal for int() with base 10: 'hi'
```
*예제 4-3 출력결과*

현재 로거에서의 수준이 setLevel()을 통해 명확히 정해지지 않았다면 부모 로거에서 설정된 수준을 가져와 이를 사용하는데 이 때 부모 로거에서 가져와서 사용하는 수준을 effective level(실효 수준)이라 한다. 만약 부모 로거에서도 수준이 정해지지 않았다면 그의 부모 로거를 조사하며, 이는 명시적으로 설정된 수준을 가지는 조상 로거가 발견될 때까지 계속 검색한다. 최상위 로거는 루트 로거이며, 해당 로거는 개발자가 따로 수준을 명시하지 않으면 WARNING으로 기본 설정된다. 

```python
import logging

logger = logging.getLogger('a')
logger.setLevel(logging.INFO)

logger2 = logging.getLogger('a.b')
logger2.setLevel(logging.NOTSET)  # logging.NOTSET: 최소 수준을 설정하지 않는다.
print(logging.getLevelName(logger2.getEffectiveLevel()))
# Logger().getEffectiveLevel(): 해당 로거 객체의 
# 실효 수준을 숫자값으로 반환. 
# logging.getLevelName(): 수준에 대응되는 숫자값을 대입할 경우 그에 
# 대응되는 수준 이름을 출력. 예) 10 -> DEBUG.
```
*예제 4-4*
```python
INFO
```
*예제 4-4 출력결과*

> 예제 4-4에서처럼 basicConfig()를 사용하지 않거나 또는 로거 객체에 따로 handler를 추가하지 않는다면 기본적으로 로그 메시지들을 콘솔창에 출력한다. handler라는 개념은 다음에 나올 “Handler” 챕터 참고.
> 

## Handler

Handler 객체는 로그 메시지 심각도를 기반으로 로그 메시지를 handler의 지정된 대상으로 전달하는 역할을 한다. 예를 들면 모든 로그 메시지들은 콘솔창에 출력하게 하고, 동시에 warning 이상의 수준의 로그 메시지들은 파일로 기록하게 하고, critical 수준 메시지만 개발자의 이메일로 보내게끔 구성할 수 있다. 로그 메시지를 보낼 대상들에는 그에 대응되는 Handler 객체를 가지며, 이러한 객체를 Logger 객체의 addHandler() 메서드의 인자로 대입하여 등록할 수 있다. 

로그 메시지를 콘솔창에 보내는 처리기는 StreamHandler가 있고, 파일로 보내는 처리기는 FileHandler 클래스가 존재한다. 이외에도 여러 대상에 대한 처리기들이 존재하며, 자세한 사항은 References의 [1]의 “유용한 처리기” 부분 참조. 

다음은 모든 로그 메시지는 콘솔창에 출력하게 하면서 동시에 WARNING 이상 수준의 로그 메시지는 ‘example.log’ 파일에 저장하게 하는 예제 코드이다.

```python
import logging

logger = logging.getLogger("study")
logger.setLevel(logging.DEBUG)

console_handler = logging.StreamHandler()    #1
console_handler.setLevel(logging.DEBUG)    #2
logger.addHandler(console_handler)    #3

file_handler = logging.FileHandler(
    filename="example.log",
    mode='w',
    encoding='utf-8'
)    #4
file_handler.setLevel(logging.WARNING)    #5
file_formatter = logging.Formatter(
    "%(asctime)s from module %(module)s %(levelname)s:\n%(message)s"
)    #6
file_handler.setFormatter(file_formatter)    #7
logger.addHandler(file_handler)    #8

logger.info("테스트를 시작합니다.")
try:
    some_var = int("hi")
except ValueError as e:
    logger.exception(e)
```
*예제 5-1*
```python
테스트를 시작합니다.
invalid literal for int() with base 10: 'hi'
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 25, in <module>
    some_var = int("hi")
ValueError: invalid literal for int() with base 10: 'hi'
```
예제 5-1 콘솔창 출력결과
```python
2023-09-03 06:13:01,844 from module study ERROR:
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 25, in <module>
    some_var = int("hi")
ValueError: invalid literal for int() with base 10: 'hi'
```
*example.log*

예제 5-1의 #1 부분에서 콘솔창에 로그 메시지를 출력하기 위한 StreamHandler() 객체를 생성하였다. Handler 객체는 #2, #5처럼 각각의 최소 수준을 설정할 수 있다. 이를 이용하면 같은 로그 메시지를 수준에 따라 각기 다른 대상으로 전달할 수 있다. #3, #8에서처럼 Logger 객체의 addHandler() 메서드에  앞서 생성한 각각의 Handler 객체를 대입하여 Logger 객체에 추가하면 된다. 

FileHandler의 경우, #4처럼 해당 클래스 생성자에 파일 이름, 파일 모드 등을 따로 지정할 수 있다. 또한 로그 메시지 출력 시의 포맷은 #6처럼 Formatter() 클래스에 포맷 문자열을 작성한 후, 해당 객체를 Handler 객체의 setFormatter() 메서드에 대입하면 (#7) 해당 포맷으로 설정할 수 있다. 

> logging 모듈에는 Handler라는 클래스가 따로 있는데, 해당 클래스를 직접 인스턴스화해서 사용하면 안된다. 대신, 사용자 정의 Handler 클래스를 만들고자 할 때 Handler 클래스를 상속하여 사용할 수는 있다. Handler 클래스는 모든 handler가 가지는 인터페이스를 정의해주는 모든 Handler 클래스들의 베이스 클래스이기 때문이다.
> 

로거 계층에서 자식 로거는 부모 로거에 연결된 handler로 로그 메시지를 전달한다. 이를 전파(propagate)라고 부르기도 한다. 따라서 일단 부모 로거에 addHandler()를 통해 특정 handler를 추가한 상태라면, 자식 로거에 따로 해당 handler를 추가하지 않아도 된다. 만약 자식 로거에서의 로그 메시지가 부모 로거로 전파되는 것을 원치 않는다면, 자식 로거 객체의 propagate 속성을 False로 설정하면 전파가 꺼진다. 

## Fomatter

Formatter() 클래스는 생성자에 포멧 문자열을 대입함으로써 로그 메시지가 출력될 때의 배치, 순서, 구조 등을 결정한다. 해당 클래스 자체를 인스턴스화하여 해당 인스턴스를 각 핸들러의 포맷으로 설정하여 사용한다(대상에 따른 Handler 객체의 setFormatter() 메서드 인자로 Formatter 객체를 대입하면 된다). 이 과정은 예제 5-1에서 자세히 보였다. 

Formatter 클래스의 생성자에 존재하는 여러 인자들 중 다음의 인자들을 소개하겠다.

`Formatter(fmt=None, datefmt=None)` 

fmt: 포맷 문자열. table 1을 참조하여 여러 속성을 사용할 수 있다. 

datefmt: fmt 포맷 문자열 내에 %(asctime)s을 사용한 경우, 시간 표시 방식을 정할 수 있다. time 모듈의 strftime() 메서드에 사용하는 포맷 방식을 똑같이 사용한다. 

(이 밖에도 여러 인자가 있지만 위의 두 인자가 자주, 그리고 유용하게 쓰일 것 같아서 두 인자만 소개한다)

## Filter

앞서 Logger 객체 형성 시 모듈 수준의 이름을 사용하여 이름을 가진 Logger 객체를 형성할 수 있다고 하였다. 그리고 마침표를 구분 기호로 사용하여 “a.b”와 같이 부모 로거와 자식 로거가 존재하는 로거 계층을 형성할 수 있다고도 하였다. Filter 클래스는 이러한 로거 계층 내에서 특정 로거 계층에서의 로그 메시지만 처리하도록 필터링 해주는 클래스이다. 예를 들어 “a.b.c”라는 로거 계층과 “d.e.f”라는 로거 계층이 존재할 때, logging.Filter(”a”)와 같이 설정한다면 “a”와 그의 자식 로거들인 “a.b.”, “a.b.c”들의 로그 메시지만 처리되고, “d.e.f” 로거 계층의 로그 메시지는 처리되지 않는다. 즉, 기존의 DEBUG, ERROR 등의 수준 필터링에서 수준을 필터링하는 것이 아닌 특정 로거 계층만을 필터링하는 방식으로 바꿀 수 있다는 것이다. 

다음은 여러 로거 계층을 구성한 후, 이 여러 로거들이 단 하나의 handler를 사용할 때, 특정 로거 계층만 해당 handler를 통해 로깅될 수 있도록 필터링한 예제 코드이다.

```python
import logging

logger_a = logging.getLogger("apple")
logger_ab = logging.getLogger("apple.banana")
logger_c = logging.getLogger('cherry')

formatter = logging.Formatter(
    "%(asctime)s from logger %(name)s, %(levelname)s:\n%(message)s"
)

# "apple" 로거 게층의 로그 메시지만 전달하도록 필터 처리한다.
filter = logging.Filter("apple")

handler = logging.FileHandler(
    filename="example.log",
    mode='w',
    encoding='utf-8'
)
handler.setFormatter(formatter)
handler.addFilter(filter)  # handler 객체에 앞서 설정한 Filter 객체를 대입.

# 모든 로거 계층이 하나의 핸들러를 사용한다. 
logger_a.addHandler(handler)
# 부모 로거에 이미 handler가 추가되었다면 
# 자식 로거는 부모 로거에 추가된 해당 handler를 통해 
# 자동으로 로그 메시지를 전달한다. 
# 따라서 이 경우 자식 로거에 따로 handler를 설정하지 않아도 된다. 
# 아래 주석을 해제하고 실행하면 example.log 파일에 
# 같은 로거로부터의 같은 메시지가 두 번 중복되어 출력될 것이다. 
#logger_ab.addHandler(handler) 
logger_c.addHandler(handler)

logger_a.error("에러가 발생하였습니다.")
logger_ab.error("Error occured.")
logger_c.error("에러 발생.")
```
*예제 6-1*
```python
2023-09-03 07:12:43,442 from logger apple, ERROR:
에러가 발생하였습니다.
2023-09-03 07:12:43,442 from logger apple.banana, ERROR:
Error occured.
```
*example.log*

---

References

[1] [로깅 HOWTO](https://docs.python.org/ko/3/howto/logging.html#logging-basic-tutorial)

[2] [061 디버깅용 로그를 남기려면? ― logging](https://wikidocs.net/123324)

[3] [로그 — The Hitchhiker's Guide to Python](https://python-guide-kr.readthedocs.io/ko/latest/writing/logging.html)

[4] [[Python] Logger 사용법](https://jh-bk.tistory.com/40)

[5] 로깅에 대한 자세한 설명 및 여러 응용들에 대한 설명.   
[로깅 요리책](https://docs.python.org/ko/3/howto/logging-cookbook.html#logging-to-multiple-destinations)

[6] [로깅](https://terms.naver.com/entry.naver?docId=796754&cid=42347&categoryId=42347)

[7] [logging — 파이썬 로깅 시설](https://docs.python.org/ko/3/library/logging.html#logrecord-objects)

[8] [파이썬 로깅 설정 - logger, handler, formatter](https://www.daleseo.com/python-logging-config/)