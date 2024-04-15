---
layout: single
title:  "[Python][정규 표현식] 정규 표현식 심화"
categories: "Python"
tag: ["Python", "정규 표현식"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 그루핑 (grouping)

정규식 내의 일부 문자들을 하나로 묶어주는 것을 그루핑이라 한다. 그루핑을 해주는 문자는 소괄호 ()이다. 이를 이용하면, 그룹지어진 전체 정규식에 메타 문자를 적용시킬 수 있다. 

다음의 예제에서 그루핑을 이용하면 반복되는 특정 문자열을 검색할 수 있음을 알게 될 것이다. 

```python
import re

pattern = re.compile('(blah)+')
match_obj = pattern.search('he said blahblahblah')
print(match_obj)
```

```python
<re.Match object; span=(8, 20), match='blahblahblah'>
```

그루핑을 통해 ‘blah’라는 문자열이 반복되는 구간을 손쉽게 찾을 수 있다. 

다음 예제에서는 소스 속에서 ‘set’ 또는 ‘setvalue’라는 문자열을 찾고자 하는 예제이다. 우리가 그루핑을 모르는 상황이라고 가정해보자. 

```python
import re

pattern = re.compile('set[value]?')
match_obj = pattern.search('they set')
print(match_obj)
```

```python
<re.Match object; span=(5, 8), match='set'>
```

‘set’ 문자열은 찾을 수 있음을 확인할 수 있다. 그런데 문제는 그 다음부터다.

```python
match_obj = pattern.search('they setvalue')
```

```python
<re.Match object; span=(5, 9), match='setv'>
```

소스 문장에서 set 뒤에 value라는 문자열을 덧붙였다. 그런데 매치 결과는 ‘setv’이다. ‘setvalue’가 되지 않는다. 

이를 해결하기 위해 그루핑을 이용해보자.

```python
import re

pattern = re.compile('set(value)?')
match_obj = pattern.search('they setvalue')
print(match_obj)
```

```python
<re.Match object; span=(5, 13), match='setvalue'>
```

```python
import re

pattern = re.compile('set(value)?')
match_obj = pattern.search('they setval')  # 문자열 변경
print(match_obj)
```

```python
<re.Match object; span=(5, 8), match='set'>
```

그루핑을 이용하니 더 수월하게 ‘set’ 또는 ‘setvalue’ 문자를 검색할 수 있게 되었다.

그루핑을 통해 사용할 수 있는 또 다른 기능은, 매치된 문자열들 중 일부만 따로 추출할 수 있다는 것이다. 

```python
import re

source = """
name: gil dong Hong
phone_number: 010-1111-2222
"""
pattern = re.compile('(\w+):\s+(\d+[-]\d+[-]\d+)')
match_obj = pattern.search(source)
print(match_obj)
print('all)', match_obj.group())
print('one)', match_obj.group(0))
print('two)', match_obj.group(1))
print('three)', match_obj.group(2))
```

```python
<re.Match object; span=(21, 48), match='phone_number: 010-1111-2222'>
all) phone_number: 010-1111-2222
one) phone_number: 010-1111-2222
two) phone_number
three) 010-1111-2222
```

패턴의 정규식을 자세히 보면 \w+와 \d+[-]\d+[-]\d+ 부분에 괄호가 쳐짐으로서 그루핑이 된 것을 확인할 수 있다. 그러면 이 정규식에 해당하는 문자열을 찾았을 때 그 문자열 중 일부만을 추출할 수 있는 것이다. 인덱스는 정규식 내에 괄호가 쳐진 순서대로 정해진다. 예를 들어 (\w+)는 인덱스 1, (\d+[-]\d+[-]\d+)는 인덱스 2로 정해진다. 이렇게 그루핑된 부분에 매치되는 문자열을 따로 추출하고자 한다면 위 예제에서처럼 match 객체의 group() 메서드에 인덱스를 인자로 넣으면 된다. 참고로 group() 메서드에 아무런 인자를 넣지 않거나 0을 넣는 경우 패턴에 일치하는 문자열 전체가 출력된다. 

그루핑 안에 또 그루핑을 할 수도 있다. 다음은 전화번호의 국번을 추출하고자 그루핑을 한 경우이다. 

```python
import re

source = """
name: gil dong Hong
phone_number: 010-1111-2222
"""
pattern = re.compile('(\w+):\s+((\d+)[-]\d+[-]\d+)')  # 전화번호 안에 그루핑 추가
match_obj = pattern.search(source)
print(match_obj)
print('all)', match_obj.group())
print('one)', match_obj.group(0))
print('two)', match_obj.group(1))
print('three)', match_obj.group(2))
print('four)', match_obj.group(3))  # 국번 추가
```

```python
<re.Match object; span=(21, 48), match='phone_number: 010-1111-2222'>
all) phone_number: 010-1111-2222
one) phone_number: 010-1111-2222
two) phone_number
three) 010-1111-2222
four) 010
```

위 예제에서 보면 알 수 있듯, 전화 번호 전체에 그루핑이 되어있는 상태에서 그 안에 국번 부분만 또 그루핑을 한 것을 알 수 있다. 그리고 이 그루핑의 인덱스는 3인 것을 알 수 있다. 그루핑 안에 또 그루핑 된 경우 인덱스는 괄호 안쪽으로 들어갈수록 증가한다. 

## 그루핑된 문자열 재참조

```python
import re

source = 'i do not know know to bow'
pattern = re.compile(r'(\b\w+)\s+\1')
match_obj = pattern.search(source)
print(match_obj)
```

```python
<re.Match object; span=(9, 18), match='know know'>
```

그루핑에서는 한 번 그루핑된 문자열을 재참조(back references)할 수 있다. 위 예제에서 \1 이 재참조 메타 문자이다. \1은 정규식의 그룹 중 첫 번째 그룹을 가리키며, 이 첫 번째 그룹에 해당하는 똑같은 문자열을 재참조하는 것이다. 위 예제에서는 (\b\w+) + \s+ + (그룹과 똑같은 단어) 으로 나타나는 것이다. 이 정규식을 만족하는 문자열이 know였고, know 바로 뒤에 공백 문자가 나오고 그 바로 뒤에 know라는 똑같은 문자열이 나오므로 해당 패턴과 일치되는 것이다. 

참고로 n번째 그룹을 참조하고자 한다면 \그룹인덱스 식으로 사용하면 된다. 3번 째 그룹 재참조는 \3 이라고 하는 것처럼. 

## 그룹 이름 지정  (named group)

정규식이 길어지고 그룹을 많이 지정하게 될 때 인덱스로 지정하는 방식은 자칫 어떤 그룹이 어떤 인덱스를 가지는 지 헷갈려 잘못 참조할 수도 있다. 이를 방지하기 위해 그룹에 이름을 따로 지정해줄 수 있다. 사용방법은 이미 그루핑된 소괄 안의 맨 앞에 ?P<지정하고자 하는 이름>이라고 하면 된다. 즉,

(?P<그룹이름>정규식) 이런 식이다. 

다음 예제에서 그룹에 이름을 지정하고, 그 이름을 참조하여 출력하는 모습을 보여주고 있다. 

```python
import re

source = """
name: gil dong Hong
phone_number: 010-1111-2222
"""
pattern = re.compile('(?P<number>\w+):\s+(?P<phone>(?P<code>\d+)[-]\d+[-]\d+)')  
match_obj = pattern.search(source)
print(match_obj)
print('all)', match_obj.group())
print('one)', match_obj.group(0))
print('two)', match_obj.group('number'))
print('three)', match_obj.group('phone'))
print('four)', match_obj.group('code'))
```

```python
<re.Match object; span=(21, 48), match='phone_number: 010-1111-2222'>
all) phone_number: 010-1111-2222
one) phone_number: 010-1111-2222
two) phone_number
three) 010-1111-2222
four) 010
```

(?P<number>\w+) 에서는 해당 그룹에 number라는 이름을 붙인 것이다. 이 그룹을 참조하고자 한다면 ‘number’를 group() 메서드에 넘기는 방식으로 쓰면 된다. 이렇게 하면 인덱스로 지정 및 참조하는 것보다 더 직관적이고 헷갈리지 않을 것이다. 

참고로 (?…) 표현식은 정규 표현식의 확장 구문이라고 한다. 

# 전후방 탐색 (lookaround assertions)

전방 탐색(lookahead assertions)과 후방 탐색(lookbehind), 합쳐서 전후방 탐색은 zero-length assertion이다. 이전에 배운 ^, $ 등의 메타 문자와 다른 점은, 전후방 탐색은 실제 문자와 매치 여부를 검색한다. 그러나 매치 여부만 검색할 뿐, 검색 결과에는 노출시키지 않는다. ‘탐색’이란 말에 부합하듯 말 그대로 탐색만 하고 일치 여부만 확인할 뿐, 그걸 결과로 내놓지는 않는다. 이러한 특성을 이용하면 정규식 조건은 만족시키나 결과로는 출력하기 원치 않는 문자열에 대해서 유용하게 적용할 수 있을 것이다. 

## 전방 탐색

전방 탐색에는 두 개의 종류가 있다.

- 긍정형 전방 탐색 (positive lookahead) (?=정규식) : 정규식과 매치하는지를 확인하며, 매치하더라도 그 결과를 출력하지 않는다(문자열을 소비하지 않는다고 표현한다).
- 부정형 전방 탐색 (negative lookahead) (?!정규식): 정규식과 매치되지 않는지를 확인하며, 해당 조건을 통과해도 문자열은 소비되지 않는다.

### 긍정형 전방 탐색

```python
import re

source = 'mytravel/gallery.jpeg'
pattern = re.compile('.+/')
match_obj = pattern.search(source)
print(match_obj)
```

```python
<re.Match object; span=(0, 9), match='mytravel/'>
```

위 예제에서 .+/ 이라는 정규식을 만족하는 문자열을 찾고자 하나 결과에 /이란 문자는 출력되고 싶지 않다고 해보자. 이 때 긍정형 전방 탐색을 다음과 같이 쓰면 해결할 수 있다.

```python
import re

source = 'mytravel/gallery.jpeg'
pattern = re.compile('.+(?=/)')  # 긍정형 전방 탐색
match_obj = pattern.search(source)
print(match_obj)
```

```python
<re.Match object; span=(0, 8), match='mytravel'>  # /가 출력되지 않았다.
```

기존의 정규식과 비교하여 검색에는 똑같이 걸리지만, 긍정형 전방 탐색법을 씀으로써 / 라는 문자열은 소비되지 않아, 즉 검색 자체에는 매치된다고 나오지만 검색 결과로 출력되지는 않아서 해당 문자열이 제거된 채로 검색 결과를 반환하는 것이다. 

다른 예제를 보자.

```python
import re

source = 'quiz'
pattern = re.compile('q(?=u)iz')
match_obj = pattern.search(source)
print(match_obj)
```

```python
None
```

언뜻 보면 문자열은 정규식과 매치되는 것처럼 보이나 실행결과를 보면 그렇지 않은 것을 알 수 있다. 왜 그럴까? 앞서 말했듯 “매치는 하지만 그 매치되는 결과를 검색에 포함하지 않기 때문이다”. 

```python
▼
q(?=u)iz

▼
quiz
```

맨 처음 정규식의 q와 문자열 q는 일치하므로 다음 문자로 넘어간다.

```python
	 ▼
q(?=u)iz

 ▼
quiz
```

정규식 u와 문자열 u가 일치한다. 

```python
	    ▼
q(?=u)iz

 ▼
quiz
```

그러나 정규식 u에는 전방 탐색이 쓰였고, 전방 탐색은 zero-width assertion이기에 문자열에서의 검색 커서는 i로 가지 않고 그대로 u에 머물게 되고, 정규식 커서만 뒤로 움직인다. 그래서 결국 i와 u와의 매치 여부를 검색하게 되는 것이다. u와 i는 서로 다르므로 결국 매치되지 않고, 더 이상 소스 문자열에 더 조사할 문자열이 없으므로 정규식 엔진은 매치 결과가 없다고 반환하게 되는 것이다. 

### 부정형 전방 탐색

이번에는 다양한 확장자를 갖는 파일 이름 중 특정 확장자는 제외하고 나머지 파일 이름을 출력하고자 하는 상황을 가정해보자.

```python
import re

source = 'gallery.jpeg sea.jpg unknown.jar concertmusic.mp4'
pattern = re.compile(r'\b\w*[.]\w*\b')
match_obj = pattern.findall(source)
print(match_obj)
```

```python
['gallery.jpeg', 'sea.jpg', 'unknown.jar', 'concertmusic.mp4']
```

이제 jpeg와 jpg 등 이미지 파일은 제외하고 나머지 파일 이름만을 출력하고자 한다. 그래서 다음과 같이 정규식을 수정하였다. 

```python
pattern = re.compile(r'\b\w*[.][^j]\w*\b')
```

```python
['concertmusic.mp4']
```

jpeg와 jpg가 똑같이 j로 시작한다는 사실에 착안하여 기존 정규식에 [^j]를 넣어 j로 시작하는 확장자는 해당 정규식 조건에 만족시키지 않도록 하였다. 그런데 jar도 j로 시작하여 해당 파일 이름까지도 제외되어버리고 말았다. jar 확장자는 출력하도록 하려면 정규식이 복잡해질 것이다. 이를 좀 더 간단히 하기 위해 부정형 전방 탐색을 써보자.

```python
pattern = re.compile(r'\b\w*[.](?!jpeg|jpg)\w*\b')
```

```python
['unknown.jar', 'concertmusic.mp4']
```

(?!jpeg|jpg) 라는 부정형 전방 탐색법을 사용하여 확장자가 jpeg이거나 jpg인 파일은 제외시키도록 하였다. 그 결과 jar는 제외되지 않고 그대로 출력되어 원하고자 하는 바를 이뤘다.

## 후방 탐색

후방 탐색은 전방 탐색과 비슷하다. 단지 검색 순서가 뒤바뀌었을 뿐이다. 전방탐색의 경우 정규식에 리터럴 문자가 먼저 오고 그 다음에 (?=…) 확장 구문이 오는 식이었다. 후방 탐색은 반대로 확장 구문이 먼저 오고 그 다음에 리터럴 문자가 오는 식이다. 그래서 전방탐색의 경우, 일치하는 리터럴 문자를 먼저 찾은 후에 그 문자 뒤의 문자를 확장 구문과 비교하여 일치 여부를 결정한다. (그래서 이름이 “전방” 탐색인가보다) 반면 후방탐색의 경우 일치하는 리터럴 문자를 뒤에서 찾은 후에 그 리터럴 문자의 앞 문자가 확장 구문과 일치하는 지를 본다. 

후방 탐색에도 긍정형 후방 탐색 (positive lookbehind)과 부정형 후방 탐색 (negative lookbehind)이 존재한다. 문법은 각각 (?<=…)와 (?<!…)이다. (…에는 정규식이 들어간다) 중간에 <이라는 문자가 들어가는 것만 빼면 형태는 전방 탐색과 똑같다. 

# 문자열 바꾸기

sub 메서드를 통해 정규식과 매치되는 부분을 다른 문자로 바꿀 수 있다. 

```python
import re

source = 'blue laptop red button'
replace = 'black and white'
pattern = re.compile('(blue|red|purple)')
match_obj = pattern.sub(replace, source)
print(match_obj)
```

```python
black and white laptop black and white button
```

sub() 메서드의 첫 번째 인자로 바꿀 문자열을 넣고, 두 번째 인자로 소스를 넣으면 된다. 그러면 위 실행결과처럼 정규식에 해당되는 문자열을 새로 바꿀 문자열로 바꿔준다. 한 편, 정규식에 해당되는 모든 문자열을 바꾸지 않고 일부만 바꾸고 싶을 수도 있다. 이 때 sub 메서드에 count=숫자를 대입하면 된다. 

```python
import re

source = 'blue laptop red button'
replace = 'black and white'
pattern = re.compile('(blue|red|purple)')
match_obj = pattern.sub(replace, source, count=1) # 바꿀 횟수 1회로 한정
print(match_obj)
```

```python
black and white laptop red button
```

위 예제에서 count=1로 설정하여 문자열을 바꿀 횟수를 1회로 제한하였다. 그 결과로 문자열도 맨 처음의 딱 한 번만 바뀜을 알 수 있다. 

sub() 메서드와 유사한 메서드로 subn() 메서드가 있다. 정규식에 해당하는 문자열을 새 문자열로 바꾼다는 점에서는 똑같으나, subn() 메서드 사용 시 튜플이 반환된다. 튜플의 첫 번째 요소는 새 문자열로 바꾼 최종 소스 문자열이고, 두 번째 요소는 문자열을 바꾼 횟수를 의미한다.

```python
import re

source = 'blue laptop red button'
replace = 'black and white'
pattern = re.compile('(blue|red|purple)')
match_obj = pattern.subn(replace, source)  # subn() 메서드 사용
print(match_obj)
```

```python
('black and white laptop black and white button', 2)
```

## sub 메서드에서 그룹 이름 참조하기

sub 메서드 사용시 그룹 이름을 참조할 수 있다. 다음은 이를 이용하여 이름 + 전화번호를 전화번호 + 이름으로 바꾸는 예이다.

```python
import re

source = 'kim 010-0000-1111'
replace = '\g<phone> \g<name>'
pattern = re.compile(r"(?P<name>\w+)\s+(?P<phone>(\d+)[-]\d+[-]\d+)")
match_obj = pattern.sub(replace, source)
print(match_obj)
```

```python
010-0000-1111 kim
```

# Greedy and non-greedy

greedy는 “탐욕스러운”이란 뜻이다. 

```python
import re

source = '<html><head><title>my_website</title>'
pattern = re.compile('<.*>')
match_obj = pattern.match(source)
print(match_obj.group())
```

```python
<html><head><title>my_website</title>
```

위 예제에서, 원래는 <html>까지만 추출하려고 했으나 * 문자는 “탐욕스러워서” 뒤의 문자열까지 추출하고 말았다. 그래서 * 문자는 greedy 문자라고 한다. 위 예제에서 뒤의 문자열까지 추출하는 탐욕을 억제하려면 greedy 문자 뒤에 non-greedy 문자인 ?을 사용하면 된다. 그러면 최소한의 반복만을 수행할 수 있게 해준다. 

```python
import re

source = '<html><head><title>my_website</title>'
pattern = re.compile('<.*?>') # non-greedy 문자 추가
match_obj = pattern.match(source)
print(match_obj.group())
```

```python
<html>
```

# 정규식에 한글 사용

지금까지의 정규식 예제들은 영문자 아니면 숫자 뿐이었다. 한글도 적용시킬 수 있지 않을까?

모든 자음만을 지정하고자 한다면 [ㄱ-ㅎ]라고 하면 된다.

```python
import re

source = '개웃기넼ㅋㅋㅋ'
pattern = re.compile('[ㄱ-ㅎ]+')
match_obj = pattern.findall(source)
print(match_obj)
```

```python
['ㅋㅋㅋ']
```

모음만을 원한다면 [ㅏ-ㅣ]로 묶을 수 있다. 

```python
import re

source = '아ㅏㅏ 넘모 슬푸다ㅠㅠ'
pattern = re.compile('[ㅏ-ㅣ]+')
match_obj = pattern.findall(source)
print(match_obj)
```

```python
['ㅏㅏ', 'ㅠㅠ']
```

자음 따로 모음 따로가 아닌 자모음 다 합쳐진 한글을 원한다면 [가-힣]이라고 하면 된다.

```python
import re

source = 'what i want for right now is 삼겹살 and 소주ㅜ cuz i am so hungry'
pattern = re.compile('[ㄱ-ㅣ가-힣]+')
match_obj = pattern.findall(source)
print(match_obj)
```

```python
['삼겹살', '소주ㅜ']
```

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 정규 표현식, 점프 투 파이썬

[점프 투 파이썬](https://wikidocs.net/1669)

[3] RegExp.info

[Regex Tutorial - Parentheses for Grouping and Capturing](https://www.regular-expressions.info/brackets.html)

[Lookahead and Lookbehind Zero-Length Assertions](https://www.regular-expressions.info/lookaround.html)

[4]

[[도서 정리] 9. 전방탐색과 후방탐색 - 손에 잡히는 10분 정규 표현식](https://aroundck.tistory.com/6448)

[5]

[[Python] 정규식 (Regex)를 이용해 한글만 추출하는 방법 (모음, 자음 구분)](https://doubly12f.tistory.com/64)

[6]

[[Python] 정규표현식 한글 매치 주의점](https://kynk94.github.io/devlog/post/re-match-hangul)