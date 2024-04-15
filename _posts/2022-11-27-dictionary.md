---
layout: single
title:  "[Python-basic][Data type] 딕셔너리 (Dictionary)"
categories: "Python"
tag: ["Python", "basic", "Data type"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 딕셔너리 (dictionary)

dictionary는 list, tuple과 달리 index가 아닌 고유의 key, value를 가진다. key는 string, boolean, integer, float처럼 immutable 자료형이 쓰인다. dictionary 자체는 mutable 자료형이다.

## {}으로 dictionary 생성하기

{}으로 빈 dictionary를 생성할 수 있으며, { key : value } 형식으로 데이터를 대입, 추가할 수 있다. 이 때 key-value 데이터 간에는 콤마로 구분한다.

```python
# 예제 1
empty_dict = {}
print(empty_dict)

personal_info = {
    "이름": '가나다',
    "나이": '20대 후반',
    "성별": '남'
}
print(personal_info)
```

```python
# 예제 1 실행결과
{}
{'이름': '가나다', '나이': '20대 후반', '성별': '남'}
```

## dict()로 다른 자료형을 dictionary로 변환

dict() 함수를 이용하여 요소가 두 개인 배열을 하나의 key-value dictionary로 만들 수 있다. 이 때 두 요소 중 첫 번째 요소는 key, 두 번째 요소는 value로 반환된다.

```python
# 예제 2
some_list = [['a', 'b']]
some_dict = dict(some_list)
print(some_dict)

another_list = [['1', '2'], ['3', '4'], ['5', '6']]
another_dict = dict(another_list)
print(another_dict)
```

```python
# 예제 2 실행결과
{'a': 'b'}
{'1': '2', '3': '4', '5': '6'}
```

list의 경우, 위 예제처럼 list들을 요소로 갖는 list 형태여야 dictionary로 변환 가능하다. (tuple도 마찬가지)

dictionary에서 key의 순서는 임의적이다.

## [key]를 이용하여 아이템을 추가 또는 변경하기

```python
# 예제 3-1
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependent',
}

print('나만의 한영사전')
print(real_dict)
```

```python
# 예제 3-1 실행결과
나만의 한영사전
{'실행 가능성': 'feasibility', '부정적으로': 'adversely', '믿을 수 있는': 'dependent'}
```

위 예제에서 real_dict라는 dictionary를 정의하고 key-value 데이터들이 대입되어 있다.

dictionary[key] = value 식으로 dictionary에 새로운 아이템을 추가하거나 이미 있는 데이터를 변경할 수도 있다.

```python
# 예제 3-2
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependent',
}
real_dict['기간, 구간'] = 'stretch'  # 새로운 아이템 추가
real_dict['믿을 수 있는'] = 'dependable'  # 기존 아이템 변경

print('나만의 한영사전')
print(real_dict)
```

```python
# 에제 3-2 실행결과
나만의 한영사전
{'실행 가능성': 'feasibility', '부정적으로': 'adversely', '믿을 수 있는': 'dependable', 
'기간, 구간': 'stretch'}
```

dictionary의 key는 고유하다. 즉, 값은 값을 가지는 key는 존재할 수 없다. 만일 dictionary 안에 두 개 이상의 서로 같은 key가 있다면 맨 마지막 값이 쓰인다.

```python
# 예제 4
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '부정적으로': 'negatively'
}

print('나만의 한영사전')
print(real_dict)
```

```python
# 예제 4 실행결과
나만의 한영사전
{'실행 가능성': 'feasibility', '부정적으로': 'negatively', '믿을 수 있는': 'dependable'}
```

## update() 함수로 dictionary 합치기

dictionary.update(다른 dictionary) 를 이용하여 한 dictionary 안의 key, value 값들을 다른 dictionary 안에 복사할 수 있다.

```python
# 예제 5
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
}
other_dict = {
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
    '부정적으로': 'negatively'
}

real_dict.update(other_dict)

print('나만의 한영사전')
print(real_dict)
```

```python
# 예제 5 실행결과
나만의 한영사전
{'실행 가능성': 'feasibility', '부정적으로': 'negatively', '믿을 수 있는': 'dependable', '직원': 'representative', '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for', '적어도': 'at least'}
```

두 dictionary에 같은 key를 가지는 데이터들이 존재할 경우, 추가되는 dictionary의 key값을 따른다. 위 예제의 코드 3번째 줄과 10번째 줄은 key가 서로 똑같으나 other_dict가 추가되는 dictionary이므로 결과값을 보면 추가되는 other_dict의 key값을 따르는 것을 확인할 수 있다.

## del과 key값을 이용하여 아이템 제거

del dictionary[key] 를 이용하여 해당 dictionary 안의 특정 key-value 데이터를 삭제할 수 있다.

```python
# 예제 6
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

del real_dict['~에 책임이 있다, ~에 원인이 있다.']

print('나만의 한영사전')
print(real_dict)
```

```python
# 예제 6 실행결과
나만의 한영사전
{'실행 가능성': 'feasibility', '부정적으로': 'adversely', '믿을 수 있는': 'dependable', 
'직원': 'representative', '적어도': 'at least'}
```

## dictionary 내 모든 데이터 삭제하기

1. dictionary.clear() 함수 사용
2. 해당 dictionary 변수에 {}를 대입하기

```python
# 예제 7
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}
other_dict = {
    '고객이름': '대기번호',
    '나코딩': 1,
    '이기자': 2,
    '구구단': 3
}

real_dict.clear()
other_dict = {}

print(real_dict)
print(other_dict)
```

```python
# 예제 7 실행결과
{}
{}
```

## in 키워드로 key값 존재 여부 확인

사용법: key in dictionary

반환값: 참일 시 True, 거짓일 시 False

dictionary 안에 해당 key값이 있는지를 조사하고 boolean값으로 반환한다.

```python
# 예제 8
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

print('실행 가능성' in real_dict)
print('대표' in real_dict)
```

```python
# 예제 8 실행결과
True
False
```

## [key]를 이용하여 아이템 얻기

dictionary[key]를 이용하면 해당 dictionary 안의 key에 해당하는 value를 반환한다.

```python
# 예제 9
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

word = real_dict['믿을 수 있는']
other_word = real_dict.get('적어도', '단어가 사전 안에 없는데요?')
other_word2 = real_dict.get('대표', '단어가 사전 안에 없는데요?')
other_word3 = real_dict.get('기껏해야')
print(word)
print(other_word)
print(other_word2)
print(other_word3)
```

```python
# 예제 9 실행결과
dependable
at least
단어가 사전 안에 없는데요?
None
```

위 예제에서, 10행에서 [] 안에 key를 대입함으로써 그 key에 해당하는 value를 추출하여 word라는 변수에 반환하고 있다. 이는 실행결과의 맨 첫 번째에서 그 결과를 확인할 수 있다.

한 편, key가 딕셔너리 안에 없으면 에러가 발생한다. 이를 피할 수 있는 방법으로 두 가지 정도가 있다.

하나는 앞서 살펴본 in 키워드를 이용해 해당 key가 딕셔너리 안에 존재하는지 미리 확인하는 방법이다.

두 번째로는 get()함수를 이용하는 것이다. 위 예제의 11, 12, 13행에서 쓰이고 있다.

사용법: 딕셔너리.get(key, key가 해당 딕셔너리에 없을 경우 반환할 값(선택))

get() 함수에 들어가는 인수 중 두 번째 인수는 생략해도 상관없다. 이 함수를 사용 시 만약 해당 key가 딕셔너리 안에 없을 경우, None을 반환한다(위 예제의 13행 및 실행결과의 마지막 줄 참고). get() 함수에 두 번째 인수를 넣은 경우, 해당 key가 딕셔너리에 없을 경우에 두 번째 인수를 반환해준다.

## keys() 함수를 이용하여 모든 key 얻기

사용법: 딕셔너리.keys()

해당 딕셔너리 안의 모든 key값들을 반환함 (key값만 반환하고 value값에 대해선 아무런 행동을 취하지 않음)

```python
# 예제 10-1
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

print(real_dict.keys())
```

```python
# 예제 10-1 실행결과
dict_keys(['실행 가능성', '부정적으로', '믿을 수 있는', '직원', '~에 책임이 있다, ~에 원
인이 있다.', '적어도'])
```

참고로 위 예제의 결과를 보면 알듯, keys() 함수의 반환값은 dict_keys()로 나타난다. 만약 이 값을 리스트로 사용하고자 한다면 list() 함수를 이용해야 한다.

```python
# 예제 10-2
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

key = real_dict.keys()
i_want_list = list(key)

print(key)
print(type(key))
print(i_want_list)
print(type(i_want_list))
```

```python
# 예제 10-2 실행결과
dict_keys(['실행 가능성', '부정적으로', '믿을 수 있는', '직원', '~에 책임이 있다, ~에 원
인이 있다.', '적어도'])
<class 'dict_keys'>
['실행 가능성', '부정적으로', '믿을 수 있는', '직원', '~에 책임이 있다, ~에 원인이 있다.', '적어도']
<class 'list'>
```

## values() 함수를 이용하여 모든 value값 얻기

사용법: 딕셔너리.values()

```python
# 예제 11
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

print(real_dict.values())
```

```python
# 예제 11 실행결과
dict_values(['feasibility', 'adversely', 'dependable', 'representative', 'be responsible for', 'at least'])
```

## items() 함수를 이용하여 모든 key-value 데이터 얻기

key와 value 동시에 얻고자 한다면 items() 함수를 이용한다.

사용법: 딕셔너리.items()

```python
# 예제 12
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

print(real_dict.items())
```

```python
# 예제 12 실행결과
dict_items([('실행 가능성', 'feasibility'), ('부정적으로', 'adversely'), ('믿을 수 있는', 'dependable'), ('직원', 'representative'), ('~에 책임이 있다, ~에 원인이 있다.', 'be responsible for'), ('적어도', 'at least')])
```

실행결과를 보면 알겠지만, 반환값에서 각각의 key, value값은 tuple로 반환된다. 그리고 이 tuple 데이터들이 list의 요소로 모여 반환됨을 알 수 있다.

## copy() 함수로 딕셔너리 복사하기

사용법: 딕셔너리.copy()

위 함수를 이용하여 다른 변수에 딕셔너리를 복사할 수 있다. 이 함수를 이용하여 복사하면 두 딕셔너리는 서로 독립적으로 분리되어서 한 딕셔너리 내의 내용이 바뀌어도 다른 딕셔너리에 영향을 주지 않는다.

```python
# 예제 13
real_dict = {
    '실행 가능성': 'feasibility',
    '부정적으로': 'adversely',
    '믿을 수 있는': 'dependable',
    '직원': 'representative',
    '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for',
    '적어도': 'at least',
}

other_dict = real_dict.copy()
real_dict['기껏해야'] = 'at best'

print(real_dict)
print(other_dict)
```

```python
# 예제 13 실행결과
{'실행 가능성': 'feasibility', '부정적으로': 'adversely', '믿을 수 있는': 'dependable', 
'직원': 'representative', '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for', '적
어도': 'at least', '기껏해야': 'at best'}
{'실행 가능성': 'feasibility', '부정적으로': 'adversely', '믿을 수 있는': 'dependable', 
'직원': 'representative', '~에 책임이 있다, ~에 원인이 있다.': 'be responsible for', '적
어도': 'at least'}
```

---

Reference

[1] Bill Lubannovic, "Introducing Python",  (O'REILLY, 2015)