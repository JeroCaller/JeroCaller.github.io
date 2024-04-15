---
layout: single
title:  "[Python][Library] 파이썬 표준 라이브러리 (2)"
categories: "Python"
tag: ["Python", "Library"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# shutil

shutil은 파일을 복사 및 이동시킬 때 쓰이는 모듈이다. 

먼저 복사의 경우 사용법은 다음과 같다.

```python
import shutil
shutil.copy(복사할 파일.확장명 , 붙여넣기할 디렉터리 경로 또는 파일명)
```

다음은 study.py파일에서 이 파일과 같은 디렉터리에 있는 다른 파이썬 파일인 test1을 서브 디렉터리인 test_folder에 복사하려고 한다. 

```python
import shutil

shutil.copy('test1.py', 'test_folder')
```

그러면 test_folder에 test1.py가 복사된다. 

붙여넣기할 대상이 디렉터리가 아닌 파일도 된다. 다음은 study.py과 같은 디렉터리에 있는 test2.py를 복사 후 이 복사본을 test_folder의 test1.py에 붙여넣으려고 한다. 

```python
import shutil

shutil.copy('test2.py', 'test_folder/test1.py')
```

그러면 test_folder에 있던 test1.py의 내용이 test2.py의 내용으로 덮여쓰인다. 

파일 이동도 방법은 똑같다.

```python
import shutil
shutil.move(이동할 파일, 이동시킬 디렉터리 경로 또는 파일명)
```

# glob

glob는 특정 디렉터리 내에 있는 파일 이름을 반환시키는 함수를 가지는 모듈이다. 메타 문자를 적절히 활용하면 특정 디렉터리 내에 있는 특정 조건을 만족시키는 파일 이름만을 가져올 수도 있다. 

다음 예제에서는 특정 디렉터리 내의 모든 파일 이름을 모두 찾아 읽어들이고 있다.

```python
from pprint import pprint
import glob

pprint(glob.glob('C:\python2\python_basic\*'))
```

```python
['C:\\python2\\python_basic\\lotto_generator.py',
 'C:\\python2\\python_basic\\our_phone_web',
 'C:\\python2\\python_basic\\reporter.py',
 'C:\\python2\\python_basic\\study.py',
 'C:\\python2\\python_basic\\test1.py',
 'C:\\python2\\python_basic\\test2.py',
 'C:\\python2\\python_basic\\test_folder',
 'C:\\python2\\python_basic\\__pycache__']
```

참고로 glob() 함수 내에서 파일이나 디렉토리 이름과 매치되는지 확인하기 위해서 정규식 문법을 사용하지 않고 유닉스 쉘 (Unix shell) 규칙을 따른다. 

- \* 은 모든 것을 뜻함
    - ‘folder\*’는 ‘folder’라는 폴더 안에 존재하는 모든 파일을 지칭함
- ? 은 한 글자와 매치됨
    - ‘folder\???’는 해당 폴더 내에 세 글자를 가진 파일이 있는지를 확인함
- [abc]는 영문자 a, b, c와 매치됨
- [!abc] 는 a, b, c를 제외한 모든 문자와 매치됨