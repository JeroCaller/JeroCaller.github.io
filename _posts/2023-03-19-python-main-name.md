---
layout: single
title:  "[Python] __main__, __name__"
categories: ["Python"]
tag: ["Python", "special methods"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
간혹 파이썬 코드를 보면 다음의 코드를 본 적이 있을 것이다.

```python
if __name__ == '__main__':
    코드
```

\_\_name\_\_은 모듈의 이름을 담는 변수이다. 예를 들어 파이썬 파일인 study.py이 존재한다면 \_\_name\_\_에는 .py인 확장자를 뺀 study란 이름이 저장되는 것이다. 

\_\_main\_\_은 현재 파이썬 인터프리터가 직접 실행하는 파이썬 스크립트 파일 자체를 의미하는 이미 정해진 상수값을 말한다. 즉, 프로그램의 시작점이 되는 모듈에서 \_\_main\_\_이 \_\_name\_\_의 값이 되는 것이다.

예를 들어, 다음의 두 파일이 있다고 가정해보자.

```python
import study2

study2.say_hello('study')
```

```python
def say_hello(words='original module'):
    print('hello everyone from {}'.format(words))

if __name__ == '__main__':
    say_hello()
```

여기서 study.py 파일을 실행하면 다음의 결과를 얻는다.

```python
hello everyone from study
```

study2.py를 실행하면 다음과 같은 결과를 얻는다.

```python
hello everyone from original module
```

study2.py 파일을 직접 실행하면 \_\_name\_\_에는 \_\_main\_\_, 즉 여기서는 ‘study2.py’ 자체를 지칭하기 때문에 (오해하면 안 되는게, \_\_main\_\_에 ‘study2.py’이 저장되는 것이 아니다. \_\_name\_\_이 모듈 이름을 갖는 변수이지 \_\_main\_\_은 변수가 아니고 이미 정해진 어떤 상수값이라 보면 되겠다. 그래서 \_\_main\_\_은 실제로는 문자열 형태로 ‘\_\_main\_\_’으로 쓰이는 것이다) ‘original module’ 문구가 포함된 결과가 나온다. 그러나 study.py 파일을 실행하면 \_\_name\_\_은 \_\_main\_\_이 아닌 study2로 저장되므로 if \_\_name\_\_ == ‘\_\_main\_\_’ 부분은 실행되지 않는 것이다. study2.py 파일은 파이썬 인터프리터가 직접 실행하는 것이 아니라 외부 모듈로서 import 되어 사용되기 때문이다. 

다음의 코드로 수정하면 좀 더 직접적으로 확인할 수 있을 것이다.

```python
import study2

study2.say_hello('study')
print('study.py: __name__ is...', __name__)
```

```python
def say_hello(words='original module'):
    print('hello everyone from {}'.format(words))

print('study2.py: __name__ is... ', __name__)
if __name__ == '__main__':
    say_hello()
```

study.py 파일을 직접 실행하면 다음의 결과를 얻는다.

```python
study2.py: __name__ is...  study2
hello everyone from study
study.py: __name__ is... __main__
```

study2.py 파일을 직접 실행하면 다음의 결과를 얻는다.

```python
study2.py: __name__ is...  __main__
hello everyone from original module
```

study.py 파일 실행 시에는 해당 파일을 직접 실행하기에 study.py의 \_\_name\_\_이 \_\_main\_\_이 되고 study2.py의 \_\_name\_\_은 study2가 된다. 반면 study2.py 파일 실행 시에는 study2.py 파일의 \_\_name\_\_이 \_\_main\_\_이 되는 것을 확인할 수 있다. 

이러한 사실을 이용하면 실제 프로그램에서는 출력되어 보이지 않으나 내가 작성한 코드가 잘 작동하는지 테스트하고 싶은 모듈이 있을 때 활용하면 좋을 것이다. 예를 들어 위 예제에서 study.py 을 메인으로 직접 실행시켜 프로그램을 동작시키고 study2.py 파일은 그저 study.py에서 사용하기 위한 외부 파일이라고 할 때, study2.py 파일 내 코드를 에러없이 잘 짰는지 먼저 확인하기 위해 if \_\_name\_\_ == ‘\_\_main\_\_’ 부분에 테스트하고 싶은 코드를 넣고 해당 파일을 직접 실행시키면 테스트 결과가 나온다. 반면 study.py 파일 실행 시에는 해당 테스트 코드가 실행되지 않는다. 이를 통해 실제 사용자들에게 보여지는 프로그램과 테스트 코드를 분리시키기 아주 좋아서 실제 작동시킬 코드에 일일이 테스트 코드를 썼다가 지웠다 하는 수고로운 작업을 방지할 수 있을 것이다. 

- \_\_main\_\_은 파이썬 인터프리터가 맨 처음 실행하는 모듈이고, 해당 모듈이 import를 통해 다른 모듈들을 import하므로 ‘top-level’ 즉, ‘최상위 코드’가 실행되는 환경이라고 표현하기도 한다.

---

Reference

[1] [파이썬 코딩 도장: 45.2 모듈과 시작점 알아보기](https://dojang.io/mod/page/view.php?id=2448)

[2] [Python \_\_main\_\_ 이란](https://yooloo.tistory.com/60)