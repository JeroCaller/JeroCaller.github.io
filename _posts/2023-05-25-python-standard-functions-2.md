---
layout: single
title:  "[Python] 내장 함수 모음 (2)"
categories: ["Python"]
tag: ["Python", "내장 함수"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
#use_math: true  # 수식이 없는 포스트 글에는 해당 속성 자체를 삽입하지 않으면 됨.
---
# getattr()

getattr(object, “attribute”[, default]) 함수는 object 인자로 넘겨진 객체에 “attribute”에 해당하는 속성을 가져온다. 해당 속성이 없을 때 default 인자를 명시하지 않으면 AttributeError 예외가 발생하고, 해당 인자를 명시하면 해당 인자를 반환한다.

```python
class TestClass():
    def __init__(self):
        self.test_number = 1

if __name__ == '__main__':
    test_inst = TestClass()
    object_attr = getattr(test_inst, "test_number")
    print(object_attr)

    print(getattr(test_inst, "age", "해당 속성 없음"))
    print(getattr(test_inst, "name")) # AttributeError 예외 발생
```

```python
1
해당 속성 없음
AttributeError: 'TestClass' object has no attribute 'name'
```

# setattr()

setattr(object, “attribute”, value) 함수는 object 객체에 “attribute”라는 새로운 속성을 부여하며, 해당 속성에 value 값을 할당한다.

```python
class TestClass():
    def __init__(self):
        self.test_number = 1

if __name__ == '__main__':
    test_inst = TestClass()
    object_attr = getattr(test_inst, "test_number")
    print(object_attr)

    setattr(test_inst, "name", 'unknown') # self.name = 'unknown'
    print(getattr(test_inst, "age", "해당 속성 없음"))
    print(getattr(test_inst, "name"))
```

```python
1
해당 속성 없음
unknown
```

# hasattr()

hasattr(object, “attribute”) 함수는 해당 object 객체에 “attribute” 속성이 있는지 확인해주는 함수로, 해당 속성이 존재하면 True, 없으면 False를 반환한다. 

```python
class TestClass():
    def __init__(self):
        self.test_number = 1

if __name__ == '__main__':
    test_inst = TestClass()
    object_attr = getattr(test_inst, "test_number")
    print(object_attr)

    setattr(test_inst, "name", 'unknown')
    print(hasattr(test_inst, "age"))  # 속성 존재 여부 확인
    print(hasattr(test_inst, "name"))
```

```python
1
False
True
```

# delattr()

delattr(object, “attribute”) 함수는 object 객체에 존재하는 “attribute” 속성을 삭제하는 함수이다.

```python
class TestClass():
    def __init__(self):
        self.test_number = 1
        self.sample_data = [1, 2, 3]

if __name__ == '__main__':
    test_inst = TestClass()
    
    delattr(test_inst, "sample_data")
    print(hasattr(test_inst, "test_number"))
    print(hasattr(test_inst, "sample_data"))
```

```python
True
False
```

---

Reference

[1] [[Python] 파이썬 함수 getattr, setattr, hasattr, delattr 사용법 및 예시](https://jeonghyeokpark.netlify.app/python/2020/12/11/python1.html)

[2] [변수가 있는지 확인하기 ( hasattr / getattr / setattr )](https://wikidocs.net/13945)