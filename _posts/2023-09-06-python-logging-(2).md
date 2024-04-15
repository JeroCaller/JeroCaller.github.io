---
layout: single
title:  "[Python] logging (2)"
categories: ["Python", "logging", "log"]
tag: ["Python", "logging", "log"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 동일한 로거 계층으로 여러 모듈에서 로깅하기

하나의 패키지 안에 여러 모듈들이 존재할 때, 한 개의 모듈에서 logging.getLogger()을 통해 로거 객체를 형성하면 다른 모듈에서 같은 로거 객체를 사용할 수 있다. 예를 들어 a.py라는 모듈에서 `logger = logging.getLogger(’hi’)` 라는 이름의 로거 객체를 형성하고 b.py 모듈에서도 똑같이 `logger = logging.getLogger(’hi’)` 라는 똑같은 이름의 로거 객체를 형성하면 이 두 로거 객체 변수는 서로 다른 로거 객체가 아니라 서로 동일한 객체를 참조하는 변수가 된다. 

```python
import logging

logger1 = logging.getLogger('hello')
logger1.setLevel(logging.INFO)

logger2 = logging.getLogger('hello')
logger2.setLevel(logging.ERROR)

logger3 = logging.getLogger('hi')
logger3.setLevel(logging.DEBUG)

print(logger1.name)
print(logging.getLevelName(logger1.getEffectiveLevel()))
print("-"*30)
print(logger2.name)
print(logging.getLevelName(logger2.getEffectiveLevel()))
print("-"*30)
print(logger1 == logger2)
print(logger1 == logger3)
```

```python
hello
ERROR
------------------------------
hello
ERROR
------------------------------
True
False
```

위 예제 1-1을 보면, 서로 다른 이름을 가진 두 변수 logger1, logger2에 이름이 ‘hello’로 똑같은 로거 객체를 참조하도록 하였다. 사실 이 두 변수는 서로 다른 로거 객체를 참조하는 게 아니라 동일한 로거 객체를 참조하는 두 개의 변수인 셈이다. 그렇기에 위 예제에서 logger2 변수를 이용하여 해당 로거 객체의 최소 수준을 logger1과 다르게 설정하면 결국엔 두 변수가 참조하는 동일한 로거 객체의 최소 수준이 logger2 변수를 통해 설정한 최소 수준으로 변경된다. 위 예제 1-1의 `print(logger1 == logger2)` 코드를 통해 두 변수가 서로 같은 로거 객체를 참조하고 있음을 직접적으로 확인할 수 있다. 

또한 다른 모듈에서 해당 로거에 자식 로거를 추가하여 사용할 수도 있다. 예를 들어 ‘study.py’ 모듈과 ‘study2.py’ 모듈이 있고, ‘study.py’에서 ‘study2.py’ 모듈을 import 한다고 가정하면, ‘study.py’ 모듈에서는 `parent_logger = logging.getLogger(’parent’)` 와 같이 부모 로거를 형성하면, ‘study2.py’ 모듈에서 `child_logger = logging.getLogger(’parent.child’)` 와 같이 자식 로거를 형성할 수 있다. 이 두 로거 객체는 비록 서로 다른 모듈에서 정의되었더라도 같은 로거 계층으로 묶이며, 두 로거 객체 사이에서 로거 정보들을 공유할 수 있다. 즉, 로그 메시지 등도 자식 로거에서 부모 로거로 전파가 가능하며, 서로 같은 handler를 사용할 수 있다. 이러한 기능은 여러 모듈들이 하나의 동일한 프로세스 안에서 동작될 때 가능하다. 

다음은 앞서 설명한 내용을 보여주는 예제 코드들이다. 

```python
import logging
import study2

parent_logger = logging.getLogger('parent')
parent_logger.setLevel(logging.DEBUG)

formatter = logging.Formatter(
    "%(asctime)s - from module %(module)s - line %(lineno)d - from logger %(name)s - %(levelname)s:\n%(message)s"
)

file_handler = logging.FileHandler(
    filename='example.log',
    mode = 'w',
    encoding='utf-8'
)
file_handler.setLevel(logging.DEBUG)
file_handler.setFormatter(formatter)
parent_logger.addHandler(file_handler)

if __name__ == '__main__':
    parent_logger.debug("프로그램 시작.")
    results = study2.calculator(2, 3)
    print(results)
    parent_logger.debug("프로그램 끝.")
```

```python
import logging

logger = logging.getLogger('parent.child')
logger.setLevel(logging.DEBUG)

def log_start_end_func(func: callable):
    def wrapper(*args, **kwargs):
        logger.debug(f'{func.__name__} 함수 호출됨.')
        func_return = func(*args, **kwargs)
        logger.debug(f'{func.__name__} 함수 작업 종료됨.')
        return func_return
    return wrapper

@log_start_end_func
def calculator(a: int, b: int):
    sum_result = a + b
    sub_result = a - b
    mul_result = a * b
    div_result = a / b
    debug_msg = f"""매개변수 정보)
    sum_result: {sum_result},
    sub_result: {sub_result},
    mul_result: {mul_result},
    div_result: {div_result},
    """
    logger.debug(debug_msg)
    four_arithmetics = {
        'sum': sum_result,
        'sub': sub_result,
        'mul': mul_result,
        'div': div_result
    }
    logger.debug(f"매개변수 정보)\nfour_arithmetics: {four_arithmetics}")
    return four_arithmetics

```

study.py 파일을 실행하면 ‘example.log’ 파일에서 다음의 결과를 얻는다. 

```python
2023-09-06 09:11:28,456 - from module study - line 21 - from logger parent - DEBUG:
프로그램 시작.
2023-09-06 09:11:28,456 - from module study2 - line 8 - from logger parent.child - DEBUG:
calculator 함수 호출됨.
2023-09-06 09:11:28,456 - from module study2 - line 26 - from logger parent.child - DEBUG:
매개변수 정보)
    sum_result: 5,
    sub_result: -1,
    mul_result: 6,
    div_result: 0.6666666666666666,
    
2023-09-06 09:11:28,456 - from module study2 - line 33 - from logger parent.child - DEBUG:
매개변수 정보)
four_arithmetics: {'sum': 5, 'sub': -1, 'mul': 6, 'div': 0.6666666666666666}
2023-09-06 09:11:28,456 - from module study2 - line 10 - from logger parent.child - DEBUG:
calculator 함수 작업 종료됨.
2023-09-06 09:11:28,457 - from module study - line 24 - from logger parent - DEBUG:
프로그램 끝.
```

study.py 파일 내 로거 객체와 study2.py 파일 내 로거 객체는 서로 다른 모듈에서 생성되었고, handler도 study.py 파일에서만 설정되었음에도 실행 결과, study2.py 파일 내 로깅 정보도 같은 ‘example.log’에 저장되었음을 알 수 있다. 

---

References

[1] [로깅 요리책](https://docs.python.org/ko/3/howto/logging-cookbook.html#using-logging-in-multiple-modules)