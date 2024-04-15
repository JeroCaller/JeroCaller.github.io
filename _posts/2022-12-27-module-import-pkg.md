---
layout: single
title:  "[Python] Module, import 선언문, package"
categories: "Python"
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# Standalone programs

다음의 코드는 독립 프로그램 (standalone program)으로 존재하는 파이썬 파일 내 코드이다.

```python
print("This standalone program works!")
```

위 코드를 terminal 창에서 다음과 같은 코드를 입력하고 실행하면 다음의 결과가 나온다.

```python
C:\python2\python_basic> python test1.py
This standalone program works!
```

화살표 (>) 이후부터 python test1.py이라고 입력하고 엔터키를 입력하면 된다. 화살표 앞에 나오는 경로 주소는 각자 해당 파일을 어떤 이름의 폴더를 어디에 저장했느냐에 따라 다르게 나온다. 

# Command-line arguments

파이썬 파일을 terminal창에서 실행시킬 때, 사용자가 터미널 창에 인자를 입력하여 프로그램에 넘길 수도 있다.

먼저 다음의 파이썬 파일을 만들고 저장한다.

```python
import sys
print('Program arguments:', sys.argv)
```

그리고 이 파일을 터미널 창에서 실행하면 다음과 같다.

```python
python test2.py
Program arguments: ['test2.py']
```

터미널 창에서 python test2.py 뒤에 여러 인자를 대입하면 이 프로그램은 이 인자들을 출력해준다. 

```python
python test2.py
Program arguments: ['test2.py', '안녕', '굳']
```

# 모듈(Module)과 import 선언문

모듈은 파이썬 코드의 파일이다. 앞서 살펴본 test1.py, test2.py와 같은 파일 하나하나가 모듈이다. 이러한 모듈은 다른 모듈을 참조할 수 있는데, 이는 import 선언문을 통해 가능하다. 

## 모듈 import하기

외부 모듈을 참조하기 위해 사용하는 import 선언문 사용법은 다음과 같다.

```python
import 모듈
```

이 때 모듈 뒤에 .py 확장자는 쓰지 않고 파일 이름만 그대로 쓴다. 

reporter.py라는 메인 프로그램이 있다.

```python
import lotto_generator

result_numbers = lotto_generator.get_numbers()
print(result_numbers)
```

위 코드에서 lotto_generator라는 모듈을 참조하고 있다. 해당 코드 파일은 다음과 같다.

```python
def get_numbers():
    from random import randint
    all_numbers = [num for num in range(1, 45+1)]
    six_numbers = []
    for i in range(6):
        index = randint(0, len(all_numbers) - 1)
        six_numbers.append(all_numbers.pop(index)) # 숫자가 중복으로 나오는 걸 방지하기 위해 pop 메서드 사용
    return sorted(six_numbers)
```

그리고 터미널 창에서 실행하면 다음의 결과를 얻게 된다.

```python
C:\python2\python_basic> python reporter.py
[6, 24, 26, 36, 37, 41]
C:\python2\python_basic> python reporter.py
[6, 12, 13, 22, 31, 39]
C:\python2\python_basic> python reporter.py
[6, 9, 16, 17, 31, 40]
```

위 두 파일이 하나의 똑같은 디렉토리(directory)에 존재하고, reporter.py 파일을 메인 프로그램으로 실행시킨다면, reporter 파일에서 lotto_generator파일을 참조할 것이고, 그 안에서 get_number()라는 함수를 실행시킬 것이다. 

위 두 파일에서 알 수 있듯, import 선언문을 각자 다른 곳에 했다. reporter.py 파일에서는 맨 처음 부분에 선언했고, lotto_generator.py 파일에서는 함수 안에 import 선언문을 사용하였다. 함수 안에 import 선언문을 사용할 시, import된 모듈은 그 함수 내부에서만 사용할 수 있고 외부에선 사용할 수 없다. 그래서 함수 바깥 코드에서 해당 모듈이 쓰일 일이 없고 함수 내에서만 쓰일 때 쓸 수 있다. 반면 모듈 첫 부분에 import 선언문 사용 시 어떤 함수에서도, 어떤 코드에서도 그 모듈 내부라면 어디든 import 된 모듈을 사용할 수 있다. 즉, 특정 함수 내에서만 쓰이지 않고 다른 코드에서도 외부 모듈이 사용된다면 이 방법을 쓸 수 있다. 

한 편, lotto_generator.py 에서 나온 또 다른 import 사용법은 다음과 같다.

```python
from 모듈명 import 모듈 내 함수명
```

그저 import 모듈명 으로 사용시, 모듈명.함수명 이런 식으로 사용해야 해서 글자를 더 많이 타이핑해야된다는 번거로움이 있다. 그래서 위 사용법처럼 사용하면 해당 외부 모듈 이름을 언급하지 않고 바로 그 모듈 내의 함수를 사용할 수 있다. 다만, 다른 외부 모듈에서도 같은 이름의 함수가 있다면 헷갈릴 수 있다는 단점이 있다. 

## 다른 이름으로 외부 모듈 import하기

```python
import 모듈명 as 별명
```

위 사용법처럼 import해오는 외부 모듈명을 다른 이름으로 바꿀 수 있다. 모듈명이 너무 길거나, 이름만으로는 해당 모듈이 어떤 역할을 하는지 짐작하기 어려울 때 등등 여러 상황에서 쓰일 수 있다. 

```python
import lotto_generator as lg

result_numbers = lg.get_numbers()
print(result_numbers)
```

## 모듈에서 원하는 것만 가져오기

다음의 import 사용법을 통해 외부 모듈에서 필요한 함수만 가져올 때 해당 함수에 다른 이름을 붙여 가져올 수 있다. 

```python
from 모듈명 import 모듈 내 함수명 as 함수 별명
```

사용 예제는 다음과 같다.

```python
from lotto_generator import get_numbers as lotto_numbers
result_numbers = lotto_numbers()
print(result_numbers)
```

# Package

패키지는 여러 모듈들을 계층적으로 관리해주는 파일 계층이다. 여러 모듈들을 체계적으로 분류하여 관리하는 거대한 폴더같은 것이라고도 볼 수 있겠다. 

예를 들어, 스마트폰을 소개하고 판매하는 웹페이지를 만들기 위해 다음의 패키지를 만들었다고 해보자.

```python
our_phone_web/
	__init__.py
	main.py
	introduction/
		__init__.py
		history_of_our_company.py  # 우리 회사 역사 소개
		used_tech_to_phone.py   # 폰 제작에 사용된 기술들 소개
	products/
		__init__.py
		ohsung_andromeda_z_clip2.py
		ij_phone_13_bro_max.py
	qna/
		__init__.py
		repeated_questions.py  # 자주 묻는 질문
		one_on_one_consult.py  # 1대1 상담
```

여기서 our_phone_web, introduction, products, qna는 디렉터리이다. our_phone_web 디렉터리는 해당 패키지의 루트 디렉터리이고, 그 외 디렉터리는 서브 디렉터리라고 한다. 

## __init__.py

__init__.py 파일은 해당 디렉터리가 패키지의 일부임을 알려주는 역할을 한다. 만약 패키지 내 디렉터리에 해당 파일이 없다면 패키지로 인식되지 않는다. 그러나 파이썬 3.3 버전 이후로는 해당 파일이 없어도 패키지로 인식된다고 한다. 

위에서 설계한 패키지 내 몇몇 모듈 내에 다음의 코드들을 입력해보겠다.

```python
# our_phone_web/introduction/history_of_our_company.py
def print_history():
    script = '''저희 회사는 10년 전통을 자랑하며...'''
    print(script)
```

```python
def get_desc_ohsung():
    price = 1000000
    release_date = '2022/12/25'
    description = '''접을 수 있는 폴더블폰...'''
    name = '오성 안드로메다 제트 클립2'
    print('상품명:', name)
    print('출시일:', release_date)
    print('가격:', price)
    print('상세설명:', description)
```

패키지 외부의 다른 파이썬 파일에서 이들을 실행시키고자 한다.

```python
from our_phone_web.introduction.history_of_our_company import print_history
from our_phone_web.products.ohsung_andromeda_z_clip2 import get_desc_ohsung
print_history()
print('-' * 30)
get_desc_ohsung()
```

```python
저희 회사는 10년 전통을 자랑하며...
------------------------------
상품명: 오성 안드로메다 제트 클립2
출시일: 2022/12/25
가격: 1000000
상세설명: 접을 수 있는 폴더블폰...
```

여기서 import 방식을 조금 달리 하여 다음과 같이 실행해본다.

```python
from our_phone_web.introduction import *
history_of_our_company.print_history()
```

```python
Exception has occurred: NameError
name 'history_of_our_company' is not defined
  File "C:\python2\python_basic\study.py", line 2, in <module>
    history_of_our_company.print_history()
```

그러면 위와 같이 오류가 난다. 위 예제의 경우 from 다음에 디렉터리의 이름들만 쓰고 마지막에 모듈 이름을 쓰지 않고 import *을 쓴 경우에 오류가 난다. 편리하게 해당 디렉토리 안의 모듈 내 함수들을 모두 사용하기 위해 위 예제와 같이 import 선언문을 썼음에도 오류가 났다. 위 예제처럼 import를 사용하는 경우 해당 디렉터리(위 예제에서 introduction 디렉터리) 내에 존재하는 __init__.py 파일 내에 __all__ 변수 선언하고, 그 변수 내에 import하고자 하는 모듈명을 list 안에 넣어 대입한다. 즉 다음처럼 한다.

```python
# our_phone_web/introduction/__init__.py
__all__ = ['history_of_our_company']
```

이 후 다시 실행하면 다음의 결과를 얻는다.

```python
저희 회사는 10년 전통을 자랑하며...
```

즉, introduction 디렉터리에서 * 기호를 이용하여 해당 디렉터리 내 모든 모듈들을 import할 경우, \_\_all\_\_ 변수 내에 정의된 모듈들만 import 가능해짐을 뜻한다. 

## relative package

위에서 예로 든 패키지에서 한 서브 디렉토리 내의 모듈이 다른 서브 디렉토리 내의 모듈을 import로 참조하여 사용할 수 있다.

예로, introduction 디렉터리에 있는 used_tech_to_phone.py에서 products 패키지에 있는 ohsung_andromeda_z_clip2.py라는 모듈을 참조하고자 한다면 다음과 같이 코드를 쓸 수 있을 것이다.

```python
from our_phone_web.products.ohsung_andromeda_z_clip2 import get_desc_ohsung

def show_tech():
    get_desc_ohsung()
    print('해당 기기에는 다음의 기술들이 들어갔습니다...')
    print('(생략)')
```

위 코드 첫번째 줄을 보면 알겠지만, 부모 디렉터리명부터 명시하여 도트(.)을 통해 해당 디렉터리의 서브 디렉터리까지 명시할 수 있다. 

해당 패키지 외부에서 다음의 파이썬 코드를 실행시켜보자

```python
from our_phone_web.introduction.used_tech_to_phone import show_tech
show_tech()
```

```python
상품명: 오성 안드로메다 제트 클립2
출시일: 2022/12/25
가격: 1000000
상세설명: 접을 수 있는 폴더블폰...
해당 기기에는 다음의 기술들이 들어갔습니다...
(생략)
```

used_tech_to_phone.py 파일에서 전체 경로를 직접 써서 import 할수도 있지만 다음과 같이 relative적으로 import하는 것도 가능하다. 

```python
from ..products.ohsung_andromeda_z_clip2 import get_desc_ohsung

def show_tech():
    get_desc_ohsung()
    print('해당 기기에는 다음의 기술들이 들어갔습니다...')
    print('(생략)')
```

여기서 점 두개 (..)는 해당 파일이 들어있는 디렉터리의 부모 디렉터리를 지정한다. 참고로 used_tech_to_phone.py 파일이 든 현재 디렉터리는 introduction 디렉터리이고, 이 디렉터리의 부모 디렉터리는 our_phone_web이다. 

정리하자면, 상대 경로에 대한 접근자에는 다음이 있다.

- .. (점 두개): 부모 디렉터리
- . (점 하나): 현재 디렉터리

## 상위 디렉토리의 모듈 import하기

이번에는 다음과 같은 패키지 구조를 가정해보겠다.

```
\Project
    main.py
    \test
        some_test.py
        \test_unit_test
            unit_test.py
```

여기서 만약 some_test.py 또는 unit_test.py 파일에서 main.py 모듈을 import 하려면 어떻게 해야할까? 이는 해당 모듈에서 sys.path.append()라는 함수를 사용해야 한다. 

예를 들어, some_test.py에서 main.py 모듈을 import하려면 some_test.py 파일에 다음의 코드를 작성해줘야 한다.

```python
import sys

sys.path.append("\\절대경로\\Project") # 해당 디렉토리의 절대경로명 입력.

import main
```

sys.path.append() 함수의 인자로 main.py 모듈이 포함된 디렉토리인 Project 디렉토리를 절대경로명으로 대입하면 된다. 

또는 os 모듈을 이용하여 다음과 같이 작성할 수도 있다.

```python
import sys
import os

sys.path.append(os.path.dirname(os.path.abspath(__file__)))

import main
```

여기서 \_\_file\_\_은 some_test.py 자기 자신의 모듈의 경로명을 의미한다. os.path.dirname() 함수는 인자로 대입된 경로의 상위 절대경로명을 반환한다. 위 코드의 os.path.dirname(os.path.abspath(\_\_file\_\_))은 "\\절대경로\\Project”라고 직접 입력하는 것과 같다.

그런데 여기서 만약 unit_test.py에서 main.py 모듈을 import하는 것과 같이 2단계 이상의 상위 디렉토리에 존재하는 모듈을 import하려면 어떻게 해야할까? 방금 보인 os.path.dirname()을 이용하면 된다. 다음은 unit_test.py 파일에서 main.py을 import하기 위한 사전 작업 코드이다. 

```python
import sys
import os

def get_super_dir(current_dir, relative_height):
    super_dir = current_dir
    for _ in range(relative_height):
        super_dir = os.path.dirname(super_dir)
    return super_dir

current_dir = os.path.dirname(os.path.abspath(__file__))
super_dir = get_super_dir(current_dir, 2) 
sys.path.append(f"{super_dir}")

import main
```

위와 같이 하면 main.py 모듈을 문제없이 import 할 수 있다. 

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 점프 투 파이썬

[점프 투 파이썬](https://wikidocs.net/33#random)

[점프 투 파이썬](https://wikidocs.net/1418)

[3]

[[Python] 상위, 하위 , 동일 폴더 내 모듈 from, import 하는 방법](https://brownbears.tistory.com/296)

[4] chat-gpt