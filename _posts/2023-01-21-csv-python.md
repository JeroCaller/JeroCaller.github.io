---
layout: single
title:  "[Python][데이터 저장과 검색][Structured text files] CSV와 파이썬"
categories: "Python"
tag: ["Python", "데이터 저장과 검색", "Structured text files", "CSV"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

csv는 comma-separated values의 약자로, 말 그대로 콤마( , )라는 기호로 구분된 텍스트 포맷을 말한다. 이 때 콤마와 같은 기호로 텍스트들을 구분해주는 이 기호들을 delimiter, separator (구분자)라고 한다. 콤마 뿐만 아니라 탭 (\t), 수직바( \| ) 등도 delimiter로 쓰일 수 있다. 콤마를 통해 작성된 텍스트 파일에는 다음의 예가 있겠다.

```python
물품,가격,종류
떡볶이,4000,음식
고전역학,30000,서적
건전지,3000,잡화
맨투맨,19900,의류
```

위 예처럼 데이터들을 표처럼 구분하여 표시하기 위해 csv를 쓸 수 있다. csv는 스프레드시트나 데이터베이스와 호환하여 쓸 수 있는 포맷이다. (위 예도 스프레드시트나 데이터베이스와 같은 표로 만들 수 있는 형태이다)

이러한 csv파일을 다루기 위해서 파이썬에서는 csv 모듈을 제공한다.

# csv 파일 쓰기

먼저 실습을 위해 csv 파일을 작성해보겠다. 다음은 파이썬 코드로 csv파일을 만들어 데이터를 쓰는 모습이다.

```python
import csv

csv_data = [
    ['물품', '가격', '종류'],
    ['떡볶이', 4000, '음식'],
    ['고전역학', 30000, '서적'],
    ['건전지', 3000, '잡화'],
    ['맨투맨', 19900, '의류'],
]

with open('receipt.csv', 'w', encoding='utf-8', newline='') as file_w:
    csv_file = csv.writer(file_w)  #1
    csv_file.writerows(csv_data)  #2
```

```python
물품,가격,종류
떡볶이,4000,음식
고전역학,30000,서적
건전지,3000,잡화
맨투맨,19900,의류
```

open() 메서드를 이용해 csv파일 생성 후 데이터 입력 시 데이터 한 줄이 입력되고 그 다음에 빈 줄이 입력되는 현상이 발생한다. 이를 방지하기 위해 newline=’’를 open() 메서드 안에 써준다. 그러면 개행문자가 데이터마다 자동으로 삽입되는 현상을 방지해준다. 한 편, 한글을 텍스트로 쓰고 있으므로 인코딩은 ‘utf-8’로 설정하였다. 

#1에서 csv모듈의 writer() 클래스에 파일 객체 file_w를 넘겨 _csv.writer라는 객체를 생성하여 csv_file 변수에 대입하였다 (type(csv_file)를 출력하면 <class ‘_csv.reader’>라고 나와서 객체로 추정된다). 그 후 #2에서 writerows() 메서드에 우리가 입력할 데이터 csv_data를 넘겨 데이터를 파일 안에 작성하도록 하고 있다. 그 결과로 receipt.csv가 위와 같이 생성됨을 알 수 있다. writerows() 사용 시 데이터 형태는 반드시 csv_data 변수처럼 한 행의 데이터를 list로 갖는 list 구조여야 한다. 

writerow() 메서드를 통해서 한 줄씩 쓸 수도 있다. 다음과 같이 for문과 결합하여 사용하면 결과는 동일하다.

```python
import csv

csv_data = [
    ['물품', '가격', '종류'],
    ['떡볶이', 4000, '음식'],
    ['고전역학', 30000, '서적'],
    ['건전지', 3000, '잡화'],
    ['맨투맨', 19900, '의류'],
]

with open('receipt.csv', 'w', encoding='utf-8', newline='') as file_w:
    csv_file = csv.writer(file_w)
    for row in csv_data:
        csv_file.writerow(row)
```

# csv 파일 읽기

이번에는 csv파일로부터 데이터들을 읽어오도록 해보자.

```python
import csv
from pprint import pprint

with open('receipt.csv', 'r', encoding='utf-8') as file_r:
    csv_file = csv.reader(file_r)  #1
    read_data = [data for data in csv_file]  #2

pprint(read_data)
```

```python
[['물품', '가격', '종류'],
 ['떡볶이', '4000', '음식'],
 ['고전역학', '30000', '서적'],
 ['건전지', '3000', '잡화'],
 ['맨투맨', '19900', '의류']]
```

#1에서 csv모듈의 reader 클래스에 file_r 파일 객체를 넘겨 _csv.reader 객체를 csv_file 변수에 할당하였다. 

#2 에서는 csv_file로부터 직접 for문을 이용하여 데이터를 한 행씩 추출하여 list로 변수에 저장하였다.

# 구분자 변경하여 파일 읽고 쓰기

csv파일에 데이터 입력 시 구분자가 콤마가 아닌 다른 구분자를 사용할 수 있다. writer() 또는 reader() 안에 delimiter 매개변수에 원하는 구분자를 입력하면 된다.

다음 예제에서는 콤마 대신 공백 문자 ‘ ‘를 구분자로 사용하는 모습이다.

```python
import csv
from pprint import pprint

csv_data = [
    ['물품', '가격', '종류'],
    ['떡볶이', 4000, '음식'],
    ['고전역학', 30000, '서적'],
    ['건전지', 3000, '잡화'],
    ['맨투맨', 19900, '의류'],
]

with open('receipt.csv', 'w', encoding='utf-8', newline='') as file_w:
    csv_file = csv.writer(file_w, delimiter=' ')  # 구분자를 공백문자로
    csv_file.writerows(csv_data)
```

```python
물품 가격 종류
떡볶이 4000 음식
고전역학 30000 서적
건전지 3000 잡화
맨투맨 19900 의류
```

receipt.csv 파일 내 데이터를 봐도 콤마 대신 공백 문자로 데이터들이 구획되어 있는 것을 알 수 있다.

다음 예제는 구분자를 공백 문자 ‘ ‘로 받아들여 csv 파일 내 데이터들을 읽어오는 예제이다.

```python
import csv
from pprint import pprint

with open('receipt.csv', 'r', encoding='utf-8') as file_r:
    csv_file = csv.reader(file_r, delimiter=' ')  # 구분자를 공백문자로 설정
    read_data = [data for data in csv_file]

pprint(read_data)
```

```python
[['물품', '가격', '종류'],
 ['떡볶이', '4000', '음식'],
 ['고전역학', '30000', '서적'],
 ['건전지', '3000', '잡화'],
 ['맨투맨', '19900', '의류']]
```

delimter를 생략하면 콤마( , )가 디폴트 값으로 설정된다.

# dictionary 형태의 데이터로 파일 읽고 쓰기

위 예제에서 데이터는 list 안에 list가 있는 형태였다. 이번에는 list안에 dictionary를 쓰는 방식을 이용해보자. 이 때 dictionary로 csv를 읽고 쓸 때 사용하는 객체는 따로 있다. 

먼저 dictionary 형태로 저장된 각 데이터들을 요소로 갖는 list 데이터를 csv파일에 쓰는 코드를 보자.

```python
import csv

csv_data = [
    {'물품': '떡볶이', '가격': 4000, '종류': '음식'},
    {'물품': '고전역학', '가격': 30000, '종류': '서적'},
    {'물품': '건전지', '가격': 3000, '종류': '잡화'},
    {'물품': '맨투맨', '가격': 19900, '종류': '의류'},
]

with open('receipt.csv', 'w', encoding='utf-8', newline='') as file_w:
    header = ['물품', '가격', '종류']   #1
    csv_file = csv.DictWriter(file_w, fieldnames=header)   #2
    csv_file.writeheader()  #3
    csv_file.writerows(csv_data)  #4
```

```python
물품,가격,종류
떡볶이,4000,음식
고전역학,30000,서적
건전지,3000,잡화
맨투맨,19900,의류
```

csv_data 변수에 저장된 각 데이터가 dictionary임을 확인할 수 있다. ‘물품’, ‘가격’, ‘종류’와 같이 각 데이터의 특성을 설명해주는 제목을 필드명 (field name)이라 한다. 

#2에서 DictWriter()라는 클래스를 이용하여 csv.DictWriter라는 클래스의 객체를 먼저 형성하고 있다. 이 때 첫 번째 인자로 파일 객체가 들어가며, 두 번째 인자로 필드명을 따로 입력해준다. 이렇게 데이터를 dictionary로 입력하면 필드명을 따로 입력할 수 있다는 것이 장점이 될 수 있겠다. 아무튼 DictWriter 객체를 csv_file이라는 변수에 할당한다.

#3에서 먼저 csv 파일에 필드들을 먼저 입력해준다. 

#4에서 writerows() 메서드에 데이터가 들어있는 csv_data 변수를 넘김으로써 해당 파일에 본격적으로 데이터들을 입력한다. DictWriter에도 모든 데이터를 한꺼번에 쓰는 writerows()와 데이터 한 행만을 입력하는 writerow() 메서드가 존재한다. 

이번에는 해당 csv파일로부터 dictionary로 데이터를 읽어오겠다.

```python
import csv
from pprint import pprint

with open('receipt.csv', 'r', encoding='utf-8') as file_r:
    csv_file = csv.DictReader(file_r)  #1
    csv_data = [row_data for row_data in csv_file]  #2

pprint(csv_data)
```

```python
[{'가격': '4000', '물품': '떡볶이', '종류': '음식'},
 {'가격': '30000', '물품': '고전역학', '종류': '서적'},
 {'가격': '3000', '물품': '건전지', '종류': '잡화'},
 {'가격': '19900', '물품': '맨투맨', '종류': '의류'}]
```

dictionary 데이터로 읽어오려면 먼저 #1에서처럼 DictReader() 클래스에 파일 객체를 입력한다. 이 때 해당 클래스에도 fieldnames=[…] 를 쓸 수 있다. 그러나 이 매개변수를 쓰는 경우는 csv파일 첫 행에 필드명이 따로 써져 있지 않고 데이터들만 있는 경우에 쓸 수 있다. 위 예제에서 사용하는 receipt.csv 파일에는 이미 첫 행에 ‘물품’, ‘가격’, ‘종류’라는 필드명이 적혀 있으므로 만약 #1에 fieldnames=[’물품’, ‘가격’, ‘종류’]를 입력하고 실행시키면 csv파일 내 첫 행인 ‘물품’, ‘가격’, ‘종류’라는 필드명을 하나의 데이터로 인식시켜서 엉뚱한 결과를 얻을 수 있다. 그래서 위 예제에서는 해당 매개변수를 생략하였다. 

#2에서 csv_file로부터 직접 for문을 사용하여 데이터들을 한 행씩 추출하여 이를 list로 저장하는 모습이다. 

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] “Python - CSV 파일 읽기, 쓰기”, codechacha.com

[Python - CSV 파일 읽기, 쓰기](https://codechacha.com/ko/python-csv/)

[3] (csv파일 개행문자 자동 삽입 방지하는 방법)

[11. 파이썬 CSV모듈 (읽기, 쓰기) + 클래스 분할하기)](https://nittaku.tistory.com/250)

[4] (필드명이란?)

[# 6. 엑셀, 데이터 가공 원리를 익혀라! 데이터베이스의 원리 살펴보기](https://m.post.naver.com/viewer/postView.naver?volumeNo=7512112&memberNo=25379965)