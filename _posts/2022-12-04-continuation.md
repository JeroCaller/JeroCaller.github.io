---
layout: single
title:  "[Python-basic] 다음 줄에 계속 쓰기"
categories: "Python"
tag: ["Python", "basic"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# '\\'으로 다음 줄에 계속 쓰기

한 라인의 길이가 짧으면 가독성이 좋아진다. 한 라인의 최대 길이는 80글자로 추천된다 (필수는 아니다). 그 이상의 코드를 한 줄에 다 쓸 수 없다면 연속 문자(continuation charactor) \ (백슬래쉬)를 쓰면 된다. 라인 끝에 \을 쓰면 된다.

```python
some_string = '가나다' + \
    '라마바사아' + \
    '자차카타' + \
    '파하'
print(some_string)
```

```python
가나다라마바사아자차카타파하
```

```python
def some_func(*args):
    print(args)

some_func('hello', 'nice', 'to', \
    'meet', 'you')
```

```python
('hello', 'nice', 'to', 'meet', 'you')
```

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)