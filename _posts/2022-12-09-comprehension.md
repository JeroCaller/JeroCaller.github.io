---
layout: single
title:  "[Python-basic] Comprehension"
categories: "Python"
tag: ["Python", "basic"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# Comprehension

## List comprehension

숫자로 이뤄진 list를 생성할 때 [] 괄호 안에 for문을 대입하여 간단하게 만들 수 있다. 

사용법

```python
[표현식 for 요소 in 반복자]
```

```python
[표현식 for 요소 in 반복자 if 조건]
```

사용법2에서 if 부분은 생략해도 된다.

```python
number_list = [number for number in range(1, 6)]
print(number_list)
```

```python
[1, 2, 3, 4, 5]
```

comprehension이 없었다면 다음과 같은 방법들을 이용해야 했을 것이다.

```python
number_1 = []
for number in range(1, 6):
    number_1.append(number)

print(number_1)

number_2 = list(range(1, 6))
print(number_2)
```

```python
[1, 2, 3, 4, 5]
[1, 2, 3, 4, 5]
```

예제2와 비교하면 comprehension 방법이 좀 더 깔끔하고 간결하게 숫자로 이뤄진 list를 만들 수 있다. 

```python
even_list = [number for number in range(1, 10) if number % 2 == 0]
print(even_list)
```

```python
[2, 4, 6, 8]
```

이중 중첩 for문을 만들 수 있듯, list comprehension 내에도 이중 for문이 가능하다.

```python
left_num = range(2, 10)
right_num = range(1, 10)
gugudan = [(i, j) for i in left_num for j in right_num]
for left, right in gugudan:
    print('{} * {} = {}'.format(left, right, left * right))
```

```python
2 * 1 = 2
2 * 2 = 4
2 * 3 = 6
2 * 4 = 8
2 * 5 = 10
2 * 6 = 12
2 * 7 = 14
2 * 8 = 16
2 * 9 = 18
3 * 1 = 3
3 * 2 = 6
3 * 3 = 9
3 * 4 = 12
3 * 5 = 15
3 * 6 = 18
3 * 7 = 21
3 * 8 = 24
3 * 9 = 27
4 * 1 = 4
4 * 2 = 8
4 * 3 = 12
4 * 4 = 16
4 * 5 = 20
4 * 6 = 24
4 * 7 = 28
4 * 8 = 32
4 * 9 = 36
5 * 1 = 5
5 * 2 = 10
5 * 3 = 15
5 * 4 = 20
5 * 5 = 25
5 * 6 = 30
5 * 7 = 35
5 * 8 = 40
5 * 9 = 45
6 * 1 = 6
6 * 2 = 12
6 * 3 = 18
6 * 4 = 24
6 * 5 = 30
6 * 6 = 36
6 * 7 = 42
6 * 8 = 48
6 * 9 = 54
7 * 1 = 7
7 * 2 = 14
7 * 3 = 21
7 * 4 = 28
7 * 5 = 35
7 * 6 = 42
7 * 7 = 49
7 * 8 = 56
7 * 9 = 63
8 * 1 = 8
8 * 2 = 16
8 * 3 = 24
8 * 4 = 32
8 * 5 = 40
8 * 6 = 48
8 * 7 = 56
8 * 8 = 64
8 * 9 = 72
9 * 1 = 9
9 * 2 = 18
9 * 3 = 27
9 * 4 = 36
9 * 5 = 45
9 * 6 = 54
9 * 7 = 63
9 * 8 = 72
9 * 9 = 81
```

## Dictionary comprehension

사용법

```python
{ key_표현식 : value_표현식 for 요소 in 반복자 if 조건}
```

여기서 if문은 생략 가능하다. 

```python
word = 'letters'
letter_counts = {letter: word.count(letter) for letter in word}
print(letter_counts)
```

```python
{'l': 1, 'e': 2, 't': 2, 'r': 1, 's': 1}
```

word란 변수 안에 든 문자열 내의 각 글자들의 출현빈도를 알기 위해 위와 같은 코드를 작성하였다. dictionary안에서 for문이 작동할 때 첫 번째 e를 만나면 word.count(letter)를 통해 word 문자열 안에 든 총 e의 개수 2를 반환할 것이다. 그런데 문자열에 두 번째 e도 존재하므로 두 번째 e에 대해서도 같은 연산을 하게 된다. 첫 번째에서 이미 해둔 연산을 반복하는 것이기에 이것은 시간 낭비이기도 하다. 이러한 시간 단축을 위해 set(word)를 사용할 수 있다.

```python
word = 'letters'
letter_counts = {letter: word.count(letter) for letter in set(word)}
print(letter_counts)
```

```python
{'e': 2, 's': 1, 't': 2, 'r': 1, 'l': 1}
```

## Set comprehension

set에도 comprehension이 존재하며, 문법은 이전과 동일하다.

```python
{ 표현식 for 표현식 in 반복자 if 조건}
```

역시 if문은 생략 가능하고, 이중 for문 중첩도 가능하다.

```python
some_set = {number for number in range(1, 10) if number % 2 == 1}
print(some_set)
```

```python
{1, 3, 5, 7, 9}
```

## Generator comprehension

tuple은 comprehension이 없다. () 괄호 안에 comprehension을 쓰면 tuple comprehension이 아닌 generator comprehension이 된다. 

```python
number_array = (number for number in range(1, 10))
print(number_array)
print(type(number_array))
print(list(number_array))
```

```python
<generator object <genexpr> at 0x000001BDBFAC0580>
<class 'generator'>
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

generator에 대한 자세한 사항은 generator 장에서 다룬다.

generator는 for문의 반복자로 들어갈 수 있다.

> generator는 list, set 등의 반복자와는 달리 오직 한 번만 실행된다. 즉 메모리에 저장되지 않는다.
> 

```python
number_array = (number for number in range(1, 6))
for number in number_array:
    print(number)

print(list(number_array))
```

```python
1
2
3
4
5
[]
```

---

Reference

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)