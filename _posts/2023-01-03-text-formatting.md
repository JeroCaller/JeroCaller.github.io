---
layout: single
title:  "[Python][텍스트와 문자열] 텍스트 포맷"
categories: "Python"
tag: ["Python", "텍스트와 문자열"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
사실 지금까지의 파이썬 예제들에서 자주 쓰였으나 정작 그것들에 대해서는 자세히 얘기한 적이 없다. 주로 print() 함수 내에서 쓰였다. 바로 텍스트 포맷이다. 문자열 사이에 문자열 값이 할당된 변수의 그 값을 삽입하는 방법이라고 말할 수 있겠다. 파이썬에서는 파이썬 포맷에 두 가지 방법이 있다. 옛날 방식과 최신 방식이 있다. 최신 방식은 파이썬 2.6 이상부터 지원된다고 한다. 

## %를 이용한 옛날 방식 포맷

%를 이용한 포맷 방식은 문자열 % 데이터 형식으로 쓰면 된다. 데이터가 문자열 내에 삽입되는 방식이다. % 뒤에 어떤 문자가 오느냐에 따라 어떤 데이터 자료형이 포맷될 것인지를 결정할 수 있다. 다음은 그 예이다.

- 형 변환 (type conversion)을 위해 쓰이는 형 지정자 (type specifier)들

| 기호 | 자료형 |
| --- | --- |
| %s | 문자열 |
| %d | 10진법 정수 |
| %x | 16진법 정수 |
| %o | 8진법 정수 |
| %f | 십진법 실수 |
| %e | 지수표기법을 이용한 실수 |
| %g | 십진법 또는 지수표기법을 이용한 실수 |
| %% | % 문자 자체를 출력하고자 할 때 |

다음은 위 테이블을 이용한 여러가지 예제들이다. 

```python
old_formats = [
    '%s' % 30,
    '%d' % 30,
    '%x' % 30,
    '%o' % 30
]

for value in old_formats:
    print(value)
```

```python
30
30
1e
36
```
---
```python
old_formats = [
    '%s' % 3.141592,
    '%f' % 3.14,
    '%e' % 3.141592,
    '%g' % 3.141592,
    '%d%%' % 100
]

for value in old_formats:
    print(value)
```

```python
3.141592
3.140000
3.141592e+00
3.14159
100%
```
---
```python
from faker import Faker

fake_data = Faker('ko-KR')

name = fake_data.name()
job = fake_data.job()
company = fake_data.company()
age = fake_data.pyint(20, 70)
my_favorite_color = fake_data.color_name()
wanna_say = fake_data.catch_phrase()  # Faker에서 나오는 모든 인적 데이터들은
# 테스트용 가짜 데이터들이라서 무작위로 나온다.

introducing = """안녕하세요, 제 이름은 %s입니다.
저의 직업은 %s이고, 다니고 있는 회사 이름은 %s입니다.
제 나이는 %d살 입니다. 제가 좋아하는 색깔은 %s입니다.
저의 좌우명은 \"%s\" 입니다.""" % (name, job, company, age, my_favorite_color, wanna_say)

print(introducing)
```

```python
안녕하세요, 제 이름은 김민지입니다.
저의 직업은 시스템 소프트웨어 개발자이고, 다니고 있는 회사 이름은 오이입니다.
제 나이는 29살 입니다. 제가 좋아하는 색깔은 Orange입니다.
저의 좌우명은 "선택적 24/7 계층" 입니다.
```
---
위의 마지막 예제인 예제 1-3에서, 여러 데이터들을 문자열 내에 삽입하기 위해선 문자열 내에 쓰인 %의 개수와 문자열 뒤에 튜플로 묶은 데이터들의 개수가 같아야 한다. 물론 데이터들의 순서대로 문자열 내에 대입된다. 

한 편, %와 형 지정자 사이에 숫자를 넣음으로써 해당 데이터가 출력될 공간 크기 설정, 최대 출력 글자 수, 정렬 방식 등을 정할 수 있다. 예제를 통해 좀 더 자세히 보겠다.

먼저 다음의 변수들을 정의하자.

```python
integer_num = 12
float_num = 3.14
string = 'hello, everone!'
```

먼저, 앞서 배웠던 포맷 방식으로 출력해보자.

```python
print('%d %f %s' % (integer_num, float_num, string))
```

```python
12 3.140000 hello, everone!
```

이번에는 글자들이 출력될 최대 공간을 제한할 것이다. 

```python
print('%5d %5f %5s' % (integer_num, float_num, string)) # 최대 5칸 공간 설정 및 오른쪽 정렬
```

```python
   12 3.140000 hello, everone!
```

위 예제에서 %와 d, f, s 사이에 숫자 5를 넣었다. 이는 해당 데이터들이 글자로 출력될 때 전체 길이를 5개로 제한하고, 해당 데이터들을 오른쪽 정렬시킨다. 이 때 글자가 출력되고 오른쪽 정렬 뒤 남는 공간은 공백으로 채워진다. 그래서 위 예제 실행결과가 예제 4-4-1에 비해 데이터들이 좀 더 오른쪽으로 치우쳐져서 출력되는 것이다. 

-를 넣고 숫자를 넣으면 왼쪽 정렬을 한다. 그 후 남는 공간은 공백으로 채운다.

```python
print('%-5d %-5f %-5s' % (integer_num, float_num, string))  # 최대 5칸 공간 설정 및 왼쪽 정렬
```

```python
12    3.140000 hello, everone!
```

이번에는 %5.3d와 같은 방식을 쓸 것이다. 이것은 5칸의 길이를 지정한 후, 출력될 최대 글자 수를 3칸으로 한정짓겠다는 것이다. 즉, 어떤 데이터 값의 길이가 3 이상이면 3글자까지만 출력되고 나머지는 잘린다는 뜻이다. 그 후 오른쪽 정렬한다. 

```python
print('%5.3d %5.3f %5.3s' % (integer_num, float_num, string)) # 최대 5칸 공간 설정, 최대 3글자로 제한
```

```python
  012 3.140   hel
```

실수의 경우 소수점 3자리까지만 표시된다. 위 예제에서 정수의 경우, 지정된 자리수보다 실제 데이터값의 자리수가 적을 경우 왼쪽에 그 만큼의 0을 붙이는 것을 알 수 있다. 

이번에는 전체 공간 길이는 지정하지 않고 출력될 글자수만 지정해보겠다.

```python
print('%.3d %.3f %.3s' % (integer_num, float_num, string)) # 공간 미설정, 최대 3글자로 출력 제한
```

```python
012 3.140 hel
```

다음으로, %와 형 지정자 사이에 들어가는 숫자 자체도 문자열에 넘기는 방식으로 사용할 수 있다. 이 때 지정 문자는 *이다.

```python
print('%*.*d %*.*f %*.*s' % (5, 3, integer_num, 5, 3, float_num, 5, 3, string)) # *에 숫자 대입
```

```python
012 3.140   hel
```

위 예제를 다 합쳐보았다.

```python
integer_num = 12
float_num = 3.14
string = 'hello, everone!'

print('%d %f %s' % (integer_num, float_num, string))
print('%5d %5f %5s' % (integer_num, float_num, string)) # 최대 5칸 공간 설정 및 오른쪽 정렬
print('%-5d %-5f %-5s' % (integer_num, float_num, string))  # 최대 5칸 공간 설정 및 왼쪽 정렬
print('%5.3d %5.3f %5.3s' % (integer_num, float_num, string)) # 최대 5칸 공간 설정, 최대 3글자로 제한
print('%.3d %.3f %.3s' % (integer_num, float_num, string)) # 공간 미설정, 최대 3글자로 출력 제한
print('%*.*d %*.*f %*.*s' % (5, 3, integer_num, 5, 3, float_num, 5, 3, string)) # *에 숫자 대입
```

```python
12 3.140000 hello, everone!
   12 3.140000 hello, everone!
12    3.140000 hello, everone!
  012 3.140   hel
012 3.140 hel
  012 3.140   hel
```

## {}와 format()함수를 이용한 새로운 포맷 방식

해당 방식은 문자열 안에 ‘{}’ 괄호를 넣고 그 다음에 .format(인자) 를 쓰는 방식이다. 즉, ‘{}’.format(인자) 형식이다. 

```python
integer_num = 12
float_num = 3.14
string = 'hello, everone!'

print('{} {} {}'.format(integer_num, float_num, string))
```

```python
12 3.14 hello, everone!
```

위 예제에서는 여러 개의 데이터들이 문자열 안으로 대입되는데, format() 함수에 들어간 인자 순서대로 대입된다. 이 때 중괄호의 개수와 format() 함수 안으로 대입되는 인자들의 숫자가 같아야 한다. 

인자들을 원하는 순서로 문자열 안에 대입할 수도 있다. 중괄호 {} 안에 인덱스를 넣음으로써 가능하다.

```python
integer_num = 12
float_num = 3.14
string = 'hello, everone!'

print('{1} {2} {0}'.format(integer_num, float_num, string))
```

```python
3.14 hello, everone! 12
```

위 예제에서 format() 함수에 들어간 인자들 integer_num, float_num, string이 각각 0, 1, 2의 인덱스를 부여받는다. 그리고 문자열 안의 중괄호 안에 인덱스를 적으면 예를 들어 {1}인 곳에 두 번째 인자인 float_num이 대입되는 것이다. 한 편 이 때의 인덱스는 리스트, 튜플과 같이 0부터 시작한다. 

한 편, 다음의 두 예제처럼 인자를 아예 키워드 인자로 넘겨서 사용하는 방법도 있다. 이 때, 키워드 인자로 사용시 중괄호 안에 해당 매개변수의 이름을 명시해야 한다. 

```python
print('{float_num} {integer_num} {string}'.format(integer_num=12, float_num=3.14, string='hello, everone!'))
```

```python
3.14 12 hello, everone!
```
---
```python
integer_num = 10
float_num = 1.4
string = 'oh'

print('{st} {int_n} {real_n}'.format(int_n=integer_num, real_n=float_num, st=string))
```

```python
oh 10 1.4
```

format() 함수에는 딕셔너리도 넣을 수 있다. 

```python
dictionary = {
    'int_num': 12,
    'real_num': 1.23,
    'string': 'hi',
}

print('{0[real_num]} {0[string]} {0[int_num]}, {1}'.format(dictionary, '난 꼽사리!'))
```

```python
1.23 hi 12, 난 꼽사리!
```

위 예제의 마지막 줄의 문자열에서 0은 format() 함수의 첫 번째 인자인 dictionary를 지칭한다. 해당 인자는 딕셔너리이므로 0[string]은 곧 dictioary[’string’]와 같다. 한 편 1은 format() 함수의 두 번째 인자로 넘겨진 ‘난 꼽사리!’ 문자열이다. 

앞서 %를 이용한 포맷 방식에서 나왔던 형 지정자는 여기서도 사용 가능하다. 이 때 { : } 으로 중괄호 안에 콜론을 넣은 후 콜론 뒤에 d, f와 같은 형 지정자를 넣는다. 

```python
int_num = 10
real_num = 1.4
string = 'hello'

print('{0:d} {1:f} {2:s}'.format(int_num, real_num, string))
```

```python
10 1.400000 hello
```

또한 앞서 살펴보았던 전체 글자 공간, 최대 글자 출력 길이, 정렬방법 등도 설정할 수 있다. 

전체 글자 공간 크기 설정은 % 포맷과 마찬가지로 형식 지정자 앞에 쓴다.

```python
int_num = 10
real_num = 1.4
string = 'hello'

print('{0:d} {1:f} {2:s}'.format(int_num, real_num, string))
print('{0:10d} {1:10f} {2:10s}'.format(int_num, real_num, string))
```

```python
10 1.400000 hello
        10   1.400000 hello
```

위 예제에서 형식 지정자 앞에 숫자를 넣으면 자동으로 오른쪽 정렬이 된다. 오른쪽 정렬을 명시할 수 있는데 >를 숫자 앞에 쓰면 된다.

```python
int_num = 10
real_num = 1.4
string = 'hello'

print('{0:d} {1:f} {2:s}'.format(int_num, real_num, string))
print('{0:>10d} {1:>10f} {2:>10s}'.format(int_num, real_num, string))
print('{0:<10d} {1:<10f} {2:<10s}'.format(int_num, real_num, string))
print('{0:^10d} {1:^10f} {2:^10s}'.format(int_num, real_num, string))
```

```python
10 1.400000 hello
        10   1.400000      hello
10         1.400000   hello
    10      1.400000    hello
```

왼쪽 정렬은 <를 쓰면 된다. 한 편 가운데 정렬은 ^를 쓰면 된다. 

그러나 옛날 포맷 방식과는 다르게, 10.4f와 같이 소수점 뒤에 숫자를 쓰는 방식은 허락되지 않는다.

```python
int_num = 10
real_num = 1.4
string = 'hello'

print('{0:^10.3d} {1:^10.3f} {2:^10.3s}'.format(int_num, real_num, string))
```

```python
Exception has occurred: ValueError
Precision not allowed in integer format specifier
```

새 포맷 방식에서 사용 가능한 기능으로 특정 문자로 문자열 공간 채우기가 있다. 이제까지는 여백을 공백으로 메꿨는데, 중괄호 안의 콜론 바로 뒤에 특정 문자를 써 넣으면 공백 대신 그 문자로 여백을 메꿔준다.

```python
print('{0:!^30s}'.format('이 세상에서 내가 짱이다'))
```

```python
!!!!!!!!이 세상에서 내가 짱이다!!!!!!!!!
```

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 문자열 자료형, 점프 투 파이썬

[점프 투 파이썬](https://wikidocs.net/13#_16)