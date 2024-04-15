---
layout: single
title:  "[Python] logging 구성 설정"
categories: ["Python", "logging", "log"]
tag: ["Python", "logging", "log", "configuration", "config"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 개요

앞서 [Logging](/python/logging/log/python-logging/) 문서에서 특정 이름을 가진 로거 객체, handler, formatter 등의 로깅에 관한 여러 정보들을 이용하여 로깅할 수 있음을 배웠다. 이 과정은 파이썬 코드를 작성하여 실행하였다. 하지만 파이썬 코드로만이 아니라, 아예 로깅에 관한 정보들을 구성 파일(configuration file)에 저장하여 이를 읽어들인 후, 해당 정보들을 바탕으로 로깅을 하는 방식도 존재한다. 예를 들면 원래 다음의 코드는 

```python
import logging

# create logger
logger = logging.getLogger('simple_example')
logger.setLevel(logging.DEBUG)

# create console handler and set level to debug
ch = logging.StreamHandler()
ch.setLevel(logging.DEBUG)

# create formatter
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# add formatter to ch
ch.setFormatter(formatter)

# add ch to logger
logger.addHandler(ch)

# 'application' code
logger.debug('debug message')
logger.info('info message')
logger.warning('warn message')
logger.error('error message')
logger.critical('critical message')
```

다음의 구성 파일로 저장하여 후에 해당 정보들을 토대로 로깅을 할 수 있다. 

```python
[loggers]
keys=root,simpleExample

[handlers]
keys=consoleHandler

[formatters]
keys=simpleFormatter

[logger_root]
level=DEBUG
handlers=consoleHandler

[logger_simpleExample]
level=DEBUG
handlers=consoleHandler
qualname=simpleExample
propagate=0

[handler_consoleHandler]
class=StreamHandler
level=DEBUG
formatter=simpleFormatter
args=(sys.stdout,)

[formatter_simpleFormatter]
format=%(asctime)s - %(name)s - %(levelname)s - %(message)s
```

# logging.config

logging.config 모듈은 로깅 구성 파일을 읽어오거나 만들 수 있는 기능을 제공한다. 

> 구성 파일에 대해서는 INI 파일과 configparser 문서 참조(문서 준비 중).
> 

해당 모듈로 구성 파일을 읽어오는 방법에는 크게 두 가지가 있다. 하나는 이미 만들어진 구성 파일을 fileConfig() 함수를 통해 읽어오는 것, 또 하나는 dictionary 형태의 로깅 구성을 dictConfig() 함수를 통해 읽어오는 방법이다.

## fileConfig()

해당 함수는 이미 존재하는 로깅 구성 파일(.conf, .ini 확장자가 붙은 파일들)을 읽어와 이를 통해 로깅 처리를 할 수 있다. 

fileConfig() 함수를 통해 로깅 구성 파일을 읽어오는 실습을 위해 우선 다음의 파이썬 코드를 작성, 실행시켰다. 다음의 파이썬 코드는 로깅 구성 파일을 생성하는 예제이다.

```python
import configparser

cfps = configparser.ConfigParser()
cfps['formatters'] = {}
cfps['formatters']['keys'] = 'file_formatter'
cfps['formatter_file_formatter'] = {}
cfps['formatter_file_formatter']['format'] = \
'%(asctime)s from module %(module)s %(levelname)s:\n%(message)s'
cfps['handlers'] = {'keys': 'console_handler,file_handler'}
cfps['handler_console_handler'] = {
    'class': 'StreamHandler',
    'level': 'DEBUG',
    'args': '(sys.stdout,)',
}
cfps['handler_file_handler'] = {
    'class': 'FileHandler',
    'args': ('example.log', 'w'),
    'level': 'WARNING',
    'formatter': 'file_formatter',
}
cfps['loggers'] = {'keys': 'root,study'}
cfps['logger_root'] = {'level': 'DEBUG', 'handlers': ""}  # 문제의 코드.
cfps['logger_study'] = {
    'level': 'DEBUG',
    'qualname': 'study',
    'handlers': 'console_handler,file_handler',
}

with open('example.conf', 'w') as cf:
    cfps.write(cf)
```

위 예제 실행 시 다음과 같이 ‘example.conf’ 파일이 생성되었다.

```python
[formatters]
keys = file_formatter

[formatter_file_formatter]
format = %(asctime)s from module %(module)s %(levelname)s:
	%(message)s

[handlers]
keys = console_handler,file_handler

[handler_console_handler]
class = StreamHandler
level = DEBUG
args = (sys.stdout,)

[handler_file_handler]
class = FileHandler
args = ('example.log', 'w')
level = WARNING
formatter = file_formatter

[loggers]
keys = root,study

[logger_root]
level = DEBUG
handlers = 

[logger_study]
level = DEBUG
qualname = study
handlers = console_handler,file_handler
```

구성 파일을 만드는 방법은 다음과 같다. 우선 formatter, handler, logger에 대한 각각의 이름 있는 개체들의 이름을 각각 formatters, handlers, loggers 섹션에 등록한다. 위 파일처럼 [handlers]에 ‘console_handler’, ‘file_handler’라는 이름들을 적는다. 그 후에는 각각의 개체들의 매개변수를 적어준다. 예를 들어, [handlers] 섹션에 적힌 두 개체 ‘console_handler’, ‘file_handler’에 대해 각각 [handler_console_handler], [handler_file_handler]와 같은 섹션을 추가하고, 각각의 섹션에 각 handler에 대한 속성들을 매개변수 형태로 적으면 된다. 이와 같은 작업을 다른 formatter, logger 등에도 적용하면 된다. 참고로 example.conf 파일의 [handler_file_handler] 섹션을 보면 args 매개변수가 존재하는데, 해당 매개변수의 값에는 class 매개변수의 값으로 지정된 FileHandler 클래스의 생성자에 들어갈 인자들을 차례대로 대입하면 된다.

‘example.conf’ 파일 형성 후, 다른 파이썬 파일에서 다음과 같이 코드를 작성한 후 실행시켜보았다.

```python
import logging
import logging.config

logging.config.fileConfig("example.conf")

logger = logging.getLogger('study')

logger.info("테스트를 시작합니다.")
try:
    some_var = int("hi")
except ValueError as e:
    logger.exception(e)
```

```python
테스트를 시작합니다.
invalid literal for int() with base 10: 'hi'
Traceback (most recent call last):
  File "C:\python\python_study\study3.py", line 10, in <module>
    some_var = int("hi")
ValueError: invalid literal for int() with base 10: 'hi'
```

```python
2023-09-06 01:43:06,523 from module study3 ERROR:
invalid literal for int() with base 10: 'hi'
Traceback (most recent call last):
  File "C:\python\python_study\study3.py", line 10, in <module>
    some_var = int("hi")
ValueError: invalid literal for int() with base 10: 'hi'
```

위 예제를 만약 파이썬 코드로만 구성했다면 다음과 같다. 

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

예제 2-3의 실행 결과도 예제 2-2의 실행 결과와 같다. 이와 같이, 코드로만 로깅 구성 설정을 하는 것보다는 아예 로깅 구성 설정 파일을 만들어 이를 토대로 로깅 처리를 하는 것이 유지보수면에서 더 나을 수 있다. 

그런데 위와 같이 fileConfig() 함수를 이용한 방법에는 몇가지 단점이 존재한다. 첫째로, 파이썬 공식문서 (References [3])에 따르면, 해당 함수는 다음에 다룰 dictConfig() 함수에 비해 오래 되었고, Filter 객체를 구성할 수 없다고 한다. 또한 향후 업데이트도 dictConfig() 함수에 추가된다고 하니 가급적 fileConfig() 보다는 dictConfig() 함수를 사용하는 것이 권장된다. 

또 하나는, 위에서 소개한 예제 2-1~2-3까지의 내용을 자세히 보면 알겠지만, 해당 예제에서는 루트 로거가 불필요헀다. 그럼에도 구성 파일에는 root 로거에 대한 정보를 반드시 삽입해야한다. (’example.conf’ 파일의 ‘logger_root’ 섹션을 보면 적어도 ‘handler=’ 매개변수는 적어야 한다) 그렇지 않으면 구성 파일을 읽어오는 예제 2-2에서 에러가 발생하여 로깅 처리가 진행되지 않는다. 하지만 이러한 문제점은 dictConfig() 함수를 사용하면 사라진다. 해당 함수에서는 루트 로거를 사용하지 않으면 루트 로거에 대한 설정을 할 필요가 없다. 

## dictConfig()

이번에는 앞서 보인 예제 2-3을 dictConfig() 함수를 이용하여 로깅 구성 정보를 만들어보겠다. 예제부터 먼저 살펴보자.

```python
import logging
import logging.config
import json

config = {
    'version': 1,
    'formatters': {
        'file_formatter': {
            'format': '%(asctime)s from module %(module)s %(levelname)s:\n%(message)s'
            },
    },
    'handlers': {
        'console_handler': {
            'class': 'logging.StreamHandler',
            'level': 'DEBUG',
        },
        'file_handler': {
            'class': 'logging.FileHandler',
            'filename': 'example.log',
            'mode': 'w',
            'encoding': 'utf-8',
            'level': 'WARNING',
            'formatter': 'file_formatter',
        }
    },
    'loggers': {
        'study': {
            'level': 'DEBUG',
            'handlers': ['console_handler', 'file_handler'],
        },
    },
}
logging.config.dictConfig(config)

# 앞선 로깅 구성 정보들을 토대로 로깅 코드 작성.
logger = logging.getLogger('study')

logger.info("테스트를 시작합니다.")
try:
    some_var = int("hi")
except ValueError as e:
    logger.exception(e)

# 로깅 구성 정보 딕셔너리를 파일로 저장.
with open('example.json', 'w') as jsonfile:
    json.dump(config, jsonfile, indent=4)
```

```python
테스트를 시작합니다.
invalid literal for int() with base 10: 'hi'
Traceback (most recent call last):
  File "C:\python\python_study\study.py", line 14, in <module>
    some_var = int("hi")
ValueError: invalid literal for int() with base 10: 'hi'
```

```python
2023-09-06 02:04:22,860 from module study2 ERROR:
invalid literal for int() with base 10: 'hi'
Traceback (most recent call last):
  File "C:\python\python_study\study2.py", line 39, in <module>
    some_var = int("hi")
ValueError: invalid literal for int() with base 10: 'hi'
```

```python
{
    "version": 1,
    "formatters": {
        "file_formatter": {
            "format": "%(asctime)s from module %(module)s %(levelname)s:\n%(message)s"
        }
    },
    "handlers": {
        "console_handler": {
            "class": "logging.StreamHandler",
            "level": "DEBUG"
        },
        "file_handler": {
            "class": "logging.FileHandler",
            "filename": "example.log",
            "mode": "w",
            "encoding": "utf-8",
            "level": "WARNING",
            "formatter": "file_formatter"
        }
    },
    "loggers": {
        "study": {
            "level": "DEBUG",
            "handlers": [
                "console_handler",
                "file_handler"
            ]
        }
    }
}
```

예제 3-1에서 딕셔너리 형태의 로깅 구성 정보를 구성한 다음, 해당 정보가 들어있는 config 변수를 logging.config.dictConfig() 함수의 인자로 대입하였다. 그 후에는 해당 정보를 바탕으로 로깅 코드를 작성하여 로깅이 정상적으로 되는지 체크해보았다. 그 결과 콘솔창과 example.log 파일에 모두 정상적으로 로그가 출력되는 것을 확인하였다. 맨 마지막 코드에는 앞서 작성한 딕셔너리 로깅 구성 정보를 json 파일로 저장하였다. json 파일로 저장한 파일은 후에 다른 파이썬 파일에서 읽어와서 그대로 재사용할 수 있다. 다음이 그 예이다.

```python
import logging
import logging.config
import json

with open('example.json', 'r') as f:
    data = json.load(f)

logging.config.dictConfig(data)

logger = logging.getLogger('study')

logger.info("테스트를 시작합니다.")
try:
    some_var = int("hi")
except ValueError as e:
    logger.exception(e)
```

예제 3-1에서 보인 딕셔너리의 구성 방법은 다음과 같다. 

1. version 이라는 키가 반드시 작성되어야 한다. 현재는 해당 키의 값은 1밖에 없다. 해당 키는 딕셔너리 스키마의 버전을 나타내는 정수값이다. 해당 키 사용 시 호환성을 유지하면서 스키마 발전이 가능하다고 한다. 
2. 다른 키들은 모두 개발자의 선택 사항이다. 
3. formatter, handler, logger와 같은 속성을 딕셔너리 안에 작성하려면 우선 각각 “formatters”, “handlers”, “loggers” 키를 작성한다. 해당 키들의 값은 반드시 딕셔너리 자료형이다. 해당 값에는 각각의 개체들 (handler라면 console_handler, file_handler와 같은 변수들)을 키로 정의한다. 각각의 개체들의 값 또한 딕셔너리로, 해당 값에서는 해당 개체의 속성들을 작성한다. 예를 들면,
    
    ```python
    'handlers': {
            'console_handler': {
                'class': 'logging.StreamHandler',
                'level': 'DEBUG',
            },
            'file_handler': {
                'class': 'logging.FileHandler',
                'filename': 'example.log',
                'mode': 'w',
                'encoding': 'utf-8',
                'level': 'WARNING',
                'formatter': 'file_formatter',
            }
        },
    ```
    
    와 같이 작성할 수 있다. Handler 클래스의 생성자 인자들 설정도 위와 같이 키-값 아이템 형식으로 작성하면 된다. 
    

---

Reference

[1] [로깅 HOWTO](https://docs.python.org/ko/3/howto/logging.html#configuring-logging-for-a-library)

[2] [파이썬 로깅 설정 - logger, handler, formatter](https://www.daleseo.com/python-logging-config/)

[3] [logging.config — 로깅 구성](https://docs.python.org/ko/3/library/logging.config.html#configuration-functions)

[4] (에러 관련 정보)

[Can I have logging.ini file without root logger?](https://stackoverflow.com/questions/28737858/can-i-have-logging-ini-file-without-root-logger)