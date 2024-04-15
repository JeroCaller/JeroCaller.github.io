---
layout: single
title:  "[Python] 디렉토리 다루기"
categories: ["Python"]
tag: ["Python", "system"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

이 장에서는 파이썬 os 모듈을 이용하여 디렉토리를 조작하는 방법에 대해 알아보겠다.

# mkdir()로 새 디렉토리 만들기

해당 함수 안에 문자열로 디렉토리 명을 입력하면 그에 해당하는 새로운 디렉토리가 만들어진다. 

```python
import os

directory_name = 'new_directory'
os.mkdir(directory_name)
print(os.path.exists(directory_name))
```

```python
True
```

# rmdir()로 디렉토리 삭제하기

해당 함수는 기존에 존재하는 디렉토리를 삭제한다. 삭제하길 원하는 디렉토리 명을 문자열 형태로 입력하면 된다.

```python
import os

directory_name = 'new_directory'
os.rmdir(directory_name)
print(os.path.exists(directory_name))
```

```python
False
```

# listdir()로 특정 디렉토리 안에 어떤 요소들이 있는지 확인하기

해당 함수에 특정 디렉토리명을 적으면 그 디렉토리 안에 어떤 파일이나 디렉토리들이 있는지 그 이름들을 요소로 하는 list로 반환해준다. 다음을 실행해보면 현재 디렉토리 내에 어떤 것들이 있는지를 확인할 수 있다.

```python
import os

print(os.listdir('.'))
```

# chdir()로 현재 디렉토리 바꾸기

해당 함수에 이동하고자 하는 디렉토리명을 입력하면 해당 디렉토리로 현재 디렉토리가 바뀌게 된다. 

다음 예제는 ‘templates’ 디렉토리로 이동하여 해당 디렉토리 안에 어떤 파일들이 있는지를 확인하는 예제이다.

```python
import os

os.chdir('templates')
print(os.listdir('.'))
```

```python
['flask2.html', 'flask3.html', 'my_saying_homepage.html']
```

# glob() 함수로 특정 조건을 만족시키는 파일 리스트 얻기

해당 함수는 정규식과 비슷한 유닉스 쉘 규칙을 잘 따르면 특정 디렉토리 내에 특정 조건을 만족시키는 파일 이름만을 추출해낼 수 있다. 자세한 사용법은 [glob](/python/standard-lib2/) 문서 참조

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)