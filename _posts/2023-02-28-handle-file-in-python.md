---
layout: single
title:  "[Python] 파일 다루기"
categories: ["Python"]
tag: ["Python", "system"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

컴퓨터 내에선 파일이나 폴더, 디렉토리 등을 생성, 삭제, 편집하거나 정리하기도 한다. 이와 관련하여 파이썬에서는 관련 시스템 함수들을 제공한다. 특히 os (operating system, 운영 체제) 모듈이 그러하다.

# open() 함수로 파일 생성하기

테스트할 파일을 하나 생성해보겠다. 

```python
with open('file_test.txt', 'wt', encoding='utf-8') as fout:
    print('텍스트 파일을 하나 생성했습니다.', file=fout)
```

```python
텍스트 파일을 하나 생성했습니다.
```

# exists()로 파일 또는 디렉토리가 존재하는지 확인하기

파일 또는 디렉토리가 존재하는지 확인하기 위해서 os 모듈의 exists() 함수를 이용하면 된다. 인자로 상대경로 또는 절대경로를 입력하면 된다. 

```python
import os

print(os.path.exists('file_test.txt'))
print(os.path.exists('./file_test.txt'))
print(os.path.exists('some_file'))
print(os.path.exists('.'))
print(os.path.exists('..'))
```

```python
True
True
False
True
True
```

경로를 표현할 때 쓰이는 점 하나 (.)와 점 두개 (..)는 각각 현재 디렉토리와 상위 디렉토리를 의미한다. 

# 무엇이 존재하는지 확인: isfile(), isdir(), isabs()

isfile()은 그것이 파일인지 아닌지를 확인하는 함수이다. 

```python
import os

print(os.path.isfile('file_test.txt'))
print(os.path.isfile('.')) #1
```

```python
True
False
```

#1의 ‘.’은 말 그대로 현재 “디렉토리”를 나타내는 것이지 파일이 아니므로 False가 반환되었다.

디렉토리인지 아닌지를 판별하려면 isdir()를 쓰면 된다.

```python
import os

print(os.path.isdir('.'))
```

```python
True
```

특정 파일이 어느 디렉토리에 위치해 있는지를 나타내는 pathname 중 절대 경로 (absolute path)는 해당 파일이 위치한 곳의 모든 상위 디렉토리들을 포함하고 경로 맨 앞에 슬러쉬(/)가 붙는다. 파일의 경로가 절대 경로인지를 판별해주는 함수로 isabs()가 있다. 인자로 꼭 실제 존재하는 파일일 필요는 없다.

```python
import os

print(os.path.isabs('/big/fake/name'))
print(os.path.isabs('big/fake/name/iknow'))
```

```python
True
False
```

# copy()로 파일 복사하기

shutil 모듈의 copy() 함수는 파일을 다른 경로에 복사해주거나 다른 파일에 덮어쓰게 해준다. 자세한 내용은 [shutil](/python/standard-lib2/) 참조

# rename()으로 이름 바꾸기

사용법

```python
import os
os.rename('기존 파일 경로', '바꿀 이름')
```

```python
import os

os.rename('file_test.txt', 'file_text.txt')
```

# link(), symlink()로 링크하기

링크는 어떤 파일이 다른 이름을 갖는 것과 같다고 볼 수 있다. 링크에는 하드 링크(hard link)와 심볼릭 링크(symbolic link)로 나뉘는데, 둘 다 원본 내용을 보여준다. 원본 내용이 바뀌면 링크된 파일에서 보여주는 내용도 달라진다. 하드 링크는 원본이 삭제되어도 원본 파일에 저장했던 가장 최근의 내용을 담는다. 반면 심볼릭 링크는 원본 파일이 삭제되면 같이 작동되지 않는다. 심볼릭 링크는 마치 윈도우의 바로 가기와 같다고 보면 된다. 

파이썬 os 모듈에서는 하드 링크를 형성하고자 한다면 link()를, 심볼릭 링크를 형성하고자 한다면 symlink()를 사용하면 된다. islink() 함수는 해당 파일이 심볼릭 링크인지 아닌지를 확인한다. 

```python
os.link('원본 파일 경로', '하드 링크할 파일명')
```

다음 예제는 앞서 봤던 ‘file_text.txt’ 파일과 하드링크할 파일인 ‘some_link.txt’를 형성하는 예제이다.

```python
import os

os.link('file_text.txt', 'some_link.txt')
```

해당 예제를 실행 후, 디렉토리를 살펴보면 ‘some_link.txt’라는 파일이 형성되었을 것이다. 그리고 내용도 file_text.txt 텍스트 파일 속 내용과 동일함을 알 수 있다. file_text.txt 파일의 내용을 바꾸고 저장하면 자동으로 some_link.txt 파일 내용도 똑같이 바뀌는 것을 확인할 수 있다. 

이번에는 file_text.txt에 심볼릭 링크를 거는 예제이다.

```python
import os

os.symlink('file_text.txt', 'symbolic_link.txt')
print(os.path.islink('symbolic_link.txt'))
```

```python
True
```

> “OSError [WinError 1314] 클라이언트가 필요한 권한을 가지고 있지 않습니다” 만약 예제 5-2 실행 시 해당 에러가 뜨는 경우, 다음 사이트를 참고.
> 
> 
> ~~[클라이언트가 필요한 권한을 가지고 있지 않습니다 해결법 (+ 포토샵 스크래치 디스크에 나타나지 않는 하드디스크 나타나게 하는 법)](https://blog.naver.com/PostView.naver?blogId=y97322&logNo=222203505547&categoryNo=88&parentCategoryNo=0&viewDate=&currentPage=1&postListTopCurrentPage=1&from=postView)~~
> 
> (2024-04-15 현재 해당 블로그 주소가 사라짐)
> 
> 아마도 “관리자 권한으로 실행”하지 않아서 발생하는 문제인 것 같다. 
> 

symbolic_link.txt도 원본 텍스트 파일 내 내용이 바뀌면 자동으로 바뀐다. 그러나 앞서 설명했듯, some_link.txt와 달리 원본 파일이 삭제되면 해당 파일은 열리지 않는다. 

# chmod()로 권한 바꾸기

chmod()를 이용하면 파일을 소유자, 사용자, 그룹 중 특정 누군가에게만 읽기, 쓰기, 실행 권한을 부여하거나 금지할 수 있다. chmod()의 첫 번째 인자로는 권한을 바꿀 파일명을 입력하고, 두 번째 인자로는 그 파일에 대해 어떤 권한을 부여할지 명령어를 8진법으로 준다. 만일 8진법이 어렵다면 import stat을 이용해 여러 키워드 중 하나를 고르면 된다. 좀 더 구체적인 권한 부여 방법에 대해서는 다음 사이트 참고.

[파이썬 os.chmod () 메소드](https://www.w3big.com/ko/python/os-chmod.html#gsc.tab=0)

이번 예제에서는 ‘file_text.txt’ 파일을 “소유자에게는 읽기 권한”을 부여해보겠다.

```python
import os
import stat

os.chmod('file_text.txt', stat.S_IRUSR)
```

위 예제를 실행한 후, 해당 텍스트 파일을 열어보면 별반 달라진 게 없어보인다. 하지만 새로운 내용을 입력 후 저장을 하면 원래는 아무 메세지 창이 뜨지 않고 바로 저장되지만 여기서는 ‘다른 이름으로 저장’ 다이얼로그가 뜨면서 수정된 내용을 다른 곳에 저장하라고 한다. 이는 소유자가 해당 원본 파일을 수정하지는 못하고, 정 수정하고프다면 수정한 내용을 아예 새로운 파일에 저장해서 쓰라는 것이다. 즉, 읽기 권한이 적용된 것이다. 해당 파일을 마우스 우클릭 후 속성을 누른 후 “일반” 탭의 맨 아래 “읽기 전용” 체크 박스를 보면 체크되어 있는 것을 확인할 수 있다. 

> chmod()는 주로 unix 운영체제에서 쓰인다는데, 윈도우 운영체제의 컴퓨터로 실행해도 되는 걸 보면 윈도우에서도 사용 가능한 것 같다.
> 

# abspath()로 경로명 얻기

해당 함수는 상대 경로를 인자로 넘기면 그 파일에 대한 절대경로를 반환한다. 

```python
import os

print(os.path.abspath('file_text.txt'))
```

```python
C:\python2\python_basic\file_text.txt
```

# realpath()로 심볼릭 링크로부터 원본 파일의 경로 얻기

해당 함수에 심볼릭 링크의 경로를 문자열 형태로 입력하면 해당 링크가 연결되어 있는 원본 파일의 경로를 반환한다. 앞서 만들었던 심볼릭 링크인 ‘symbolic_link.txt’를 이용해보자.

```python
import os

print(os.path.realpath('symbolic_link.txt'))
```

```python
C:\python2\python_basic\file_text.txt
```

# remove()로 파일 삭제하기

```python
import os

file_name = 'file_text.txt'
os.remove(file_name)
print(os.path.exists(file_name))
```

```python
False
```

위 예제를 실행하면 ‘file_text.txt’ 파일은 삭제된다. 

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)