---
layout: single
title:  "[Python][데이터 저장과 검색][Structured text files] JSON과 파이썬"
categories: "Python"
tag: ["Python", "데이터 저장과 검색", "Structured text files", "JSON"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---

JavaScript Object Notation (JSON)은 데이터 교환 포맷 중 하나이다. JSON에 대한 자세한 내용은 다음의 페이지들을 참고.   
[JSON 개요](/json/json-introduction/)  
[JSON 문법](/json/json-syntax/)  
[JSON Schema](/json/json-schema/)

JSON에 대한 내용은 따로 위 링크로 정리해두었으므로 여기서는 JSON 자체에 대한 내용보다는 파이썬에서 어떻게 JSON을 다룰 수 있는지에 초점을 맞출 것이다. 

# 들어가기에 앞서 용어 몇 가지만 정리하죠

네트워크를 통해 데이터를 전달하거나 저장하기 위해 데이터를 일련의 바이트(a series of bytes)로 변환하는 과정, 즉 데이터를 JSON으로 인코딩하는 과정을 serialization (직렬화)라고 한다. 반대로 JSON으로 쓰여진 구조화된 데이터를 일반적인 데이터로 디코딩하는 과정을 deserialization (역직렬화)라고 한다. 

# 파이썬에서 지원하는 json 모듈

파이썬에서는 JSON 데이터를 다루기 위해 내장 패키지인 json 모듈을 제공한다. 파이썬에서 JSON 데이터를 다룰려면 먼저 파이썬 파일에 다음의 문장을 넣으면 된다.

```python
import json
```

## JSON 직렬화

데이터를 JSON 데이터로 만들기 위해 파이썬의 json 모듈에서는 dump(), dumps() 메서드를 제공한다. dump() 메서드는 데이터를 파일로 쓰는데 사용되고, dumps() 메서드는 파이썬의 문자열 자료형으로 쓰는 데에 사용된다. 

JSON 문법과 파이썬에서 쓰이는 자료형의 형태를 보면 비슷한 점이 많다. 예를 들어 파이썬에서는 dictionary를 나타내기 위해 중괄호 ( {} )를 쓰는데, 이는 JSON에서 객체를 표현하기 위해 쓰이는 중괄호 표시와 똑같다. 모습만 비슷한 것 뿐만 아니라 실제로도 파이썬의 특정 자료형들은 JSON의 특정 자료형으로 직렬화될 수 있다. 다음은 그에 대한 표이다.

- python to JSON

| python | JSON |
| --- | --- |
| dict | object |
| list, tuple | array |
| str | string |
| int, long, float | number |
| True | true |
| False | false |
| None | null |

다음의 예는 파이썬 모듈에서 변수에 JSON 데이터를 파이썬의 dictionary, string, list 등을 이용하여 작성하고, 이를 JSON 데이터로 변환하여 JSON 파일에 저장하는 예제이다.

```python
import json

json_data = {
    "user": "나이썬",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "프리랜서 개발자",
        "second job": "배달",
        "hobby" : [
            "유튜브 보기", "넷플릭스 보기", "헬스"
        ]
    }
}

with open("user_data.json", "w") as write_file:
    json.dump(json_data, write_file)
```

```python
{"user": "\ub098\uc774\uc36c", "details": {"serialNumber": 5, "age": 25, "main job": "\ud504\ub9ac\ub79c\uc11c \uac1c\ubc1c\uc790", "second job": "\ubc30\ub2ec", "hobby": ["\uc720\ud29c\ube0c \ubcf4\uae30", "\ub137\ud50c\ub9ad\uc2a4 \ubcf4\uae30", "\ud5ec\uc2a4"]}}
```

dump() 메서드를 자세히 보면, 첫 번째 인자로 직렬화될 데이터 객체가 들어가고, 두 번째 인자로 파일로 저장하기 위해 사용된 파일 객체가 들어감을 알 수 있다. 

파일에 저장하기보다는 파이썬 모듈에서 JSON 데이터를 그대로 계속 쓰고프다면 데이터를 JSON으로 직렬화한 객체를 (파일이 아닌) 파이썬 string 객체로 저장하는 dumps() 메서드를 사용하면 된다.

```python
import json

json_data = {
    "user": "나이썬",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "프리랜서 개발자",
        "second job": "배달",
        "hobby" : [
            "유튜브 보기", "넷플릭스 보기", "헬스"
        ]
    }
}

json_string = json.dumps(json_data)
print(json_string)
print(type(json_string))
```

```python
{"user": "\ub098\uc774\uc36c", "details": {"serialNumber": 5, "age": 25, "main job": "\ud504\ub9ac\ub79c\uc11c \uac1c\ubc1c\uc790", "second job": "\ubc30\ub2ec", "hobby": ["\uc720\ud29c\ube0c \ubcf4\uae30", "\ub137\ud50c\ub9ad\uc2a4 \ubcf4\uae30", "\ud5ec\uc2a4"]}}
<class 'str'>
```

dump() 와 dumps()는 직렬화된 JSON 데이터를 파일에 저장할 것이냐 아니면 파이썬 자료형인 문자열 자료형으로 저장할 것인가의 차이일 뿐, 데이터를 JSON 데이터로 직렬화 한다는 점에서는 모두 똑같은 메서드이다. 

### dump(), dumps()에 들어갈 수 있는 키워드 인자들

앞서 본 예제의 결과들을 보면 사람이 읽기에는 좀 많이 힘든 형태를 띠고 있다. 분명 JSON은 사람이 읽기에도 좋게 개발되었다는데 위 결과들을 보면 꼭 무슨 외계어같기도 하고 가독성이 너무 떨어진다. 이를 위해 dump(), dumps() 메서드에 키워드 인자로 indent= 를 사용할 수 있다. 이는 JSON의 다중 구조에 들여쓰기 사이즈 (indentation size)를 몇으로 설정할 것인지를 정할 수 있다. 

앞선 예제의 결과들의 가독성을 높여보자.

```python
import json

json_data = {
    "user": "나이썬",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "프리랜서 개발자",
        "second job": "배달",
        "hobby" : [
            "유튜브 보기", "넷플릭스 보기", "헬스"
        ]
    }
}

with open("user_data.json", "w") as write_file:
    json.dump(json_data, write_file, indent=4)  #1 indent 키워드 인자 추가
```

```python
{
    "user": "\ub098\uc774\uc36c",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "\ud504\ub9ac\ub79c\uc11c \uac1c\ubc1c\uc790",
        "second job": "\ubc30\ub2ec",
        "hobby": [
            "\uc720\ud29c\ube0c \ubcf4\uae30",
            "\ub137\ud50c\ub9ad\uc2a4 \ubcf4\uae30",
            "\ud5ec\uc2a4"
        ]
    }
}
```

아까의 예제에 비해 지금 예제의 결과가 훨씬 보기 좋게 바뀌었음을 알 수 있다. dumps() 메서드에도 똑같이 적용된다.

```python
import json

json_data = {
    "user": "나이썬",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "프리랜서 개발자",
        "second job": "배달",
        "hobby" : [
            "유튜브 보기", "넷플릭스 보기", "헬스"
        ]
    }
}
json_string = json.dumps(json_data, indent=4)
print(json_string)
print(type(json_string))
```

```python
{
    "user": "\ub098\uc774\uc36c",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "\ud504\ub9ac\ub79c\uc11c \uac1c\ubc1c\uc790",
        "second job": "\ubc30\ub2ec",
        "hobby": [
            "\uc720\ud29c\ube0c \ubcf4\uae30",
            "\ub137\ud50c\ub9ad\uc2a4 \ubcf4\uae30",
            "\ud5ec\uc2a4"
        ]
    }
}
<class 'str'>
```

또 다른 키워드로 separators= 가 있다. 디폴트로는 (”, ”, “: ”)으로 설정되어 있다. 즉 튜플의 첫 번째 요소는 JSON 프로퍼티 간 구분자를, 두 번째 요소에는 한 프로퍼티의 key와 value를 구분해주는 구분자를 의미한다. 

디폴트 값으로 “, ”, “: ” 구분자 뒤에는 공백문자가 하나 들어가 있다. 다음 예제에서는 이 공백을 없애고자 해당 키워드 인자를 사용하는 모습이다.

```python
import json

json_data = {
    "user": "나이썬",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "프리랜서 개발자",
        "second job": "배달",
        "hobby" : [
            "유튜브 보기", "넷플릭스 보기", "헬스"
        ]
    }
}

json_string = json.dumps(json_data, indent=4, separators=(",", ":"))
print(json_string)
print(type(json_string))
```

```python
{
    "user":"\ub098\uc774\uc36c",
    "details":{
        "serialNumber":5,
        "age":25,
        "main job":"\ud504\ub9ac\ub79c\uc11c \uac1c\ubc1c\uc790",
        "second job":"\ubc30\ub2ec",
        "hobby":[
            "\uc720\ud29c\ube0c \ubcf4\uae30",
            "\ub137\ud50c\ub9ad\uc2a4 \ubcf4\uae30",
            "\ud5ec\uc2a4"
        ]
    }
}
<class 'str'>
```

## JSON 역직렬화

JSON에서 파이썬 객체 형태의 데이터로 변환하기 위해 쓰이는 메서드는 load(), loads()가 있다. 

직렬화와 마찬가지로, 역직렬화에서도 JSON에서 파이썬으로 변형되는 자료형들의 표를 다음에서 확인할 수 있다.

| JSON | python |
| --- | --- |
| object | dict |
| array | list |
| string | str |
| number (int) | int |
| number (실수) | float |
| true | True |
| false | False |
| null | None |

참고로 직렬화할 땐 파이썬의 tuple 자료형은 array로 바뀌나, 역직렬화할 때 tuple 자료형은 list 자료형으로 바뀐다. 이를 다음 예제에서 보여주고 있다.

```python
import json

one_card = (3, 'diamond')
encoded_card = json.dumps(one_card)
decoded_card = json.loads(encoded_card)
print(decoded_card)
print(type(decoded_card))
```

```python
[3, 'diamond']
<class 'list'>
```

이제 앞서 작성했던 json 파일 내 데이터를 불러들여 파이썬 객체로 변환시켜보자.

```python
import json
with open("user_data.json", "r") as write_file:
    decoded_data = json.load(write_file)

print(decoded_data)
print(type(decoded_data))
```

```python
{'user': '나이썬', 'details': {'serialNumber': 5, 'age': 25, 'main job': '프리랜서 개발
자', 'second job': '배달', 'hobby': ['유튜브 보기', '넷플릭스 보기', '헬스']}}
<class 'dict'>
```

인코딩된 JSON 데이터가 파이썬의 문자열 자료형으로 쓰인 경우 loads() 를 이용하여 디코드 할 수 있다.

```python
import json

json_data = '''{
    "user": "나이썬",
    "details": {
        "serialNumber": 5,
        "age": 25,
        "main job": "프리랜서 개발자",
        "second job": "배달",
        "hobby" : [
            "유튜브 보기", "넷플릭스 보기", "헬스"
        ]
    }
}'''

data = json.loads(json_data)
print(data)
print(type(data))
```

```python
{'user': '나이썬', 'details': {'serialNumber': 5, 'age': 25, 'main job': '프리랜서 개발 
자', 'second job': '배달', 'hobby': ['유튜브 보기', '넷플릭스 보기', '헬스']}}
<class 'dict'>
```

# 실전 예제로 살펴보기

[JSONPlaceholder](https://jsonplaceholder.typicode.com/) 라는 웹사이트에서는 연습 목적을 위해 가짜로 생성된 JSON 데이터 소스를 제공한다. 이를 통해 JSON 데이터를 파이썬으로 다루는 실전 연습을 해보자. 

먼저 JSONPlaceholder 서비스에 API 요청을 해야 한다. 이를 위해 requests 모듈을 import 해야 한다. 단, requests는 내장된 라이브러리는 아니므로 처음 쓰는 것이라면 터미널 창에서 pip install requests 라는 명령어를 쳐서 먼저 다운 받은 후에 실행하도록 하자.

해당 홈페이지에 있는 데이터 중에는 todo 리스트 데이터가 있는데 이를 사용해볼 것이다. 해당 사이트에서 아래로 스크롤하여 Resources라는 부분의 /todos 를 누르면 전체 JSON 데이터가 보일 것이다.

전체 데이터는 너무 양이 많으므로 그 중 랜덤으로 몇 개만 뽑아서 출력해봄으로써 해당 홈페이지로부터 데이터를 잘 전송 받았는지 확인해보자. 

```python
import json
import requests
import random
from pprint import pprint

response = requests.get("https://jsonplaceholder.typicode.com/todos")  #1
todos = json.loads(response.text)  #2

pick_random_todos = [random.choice(todos) for i in range(5)]
pprint(pick_random_todos)
print(type(todos))
```

```python
[{'completed': False,
  'id': 144,
  'title': 'ut eum exercitationem sint',
  'userId': 8},
 {'completed': False,
  'id': 181,
  'title': 'ut cupiditate sequi aliquam fuga maiores',
  'userId': 10},
 {'completed': False,
  'id': 160,
  'title': 'et praesentium aliquam est',
  'userId': 8},
 {'completed': True,
  'id': 157,
  'title': 'dolorem veniam quisquam deserunt repellendus',
  'userId': 8},
 {'completed': False,
  'id': 82,
  'title': 'voluptates eum voluptas et dicta',
  'userId': 5}]
<class 'list'>
```

#1에서 해당 홈페이지로부터 요청한 정보를 받고, #2에서 전송 받은 데이터를 파이썬 객체로 역직렬화하는 것을 알 수 있다. 

각 데이터에는 id 번호와 유저의 id, 그리고 할 일의 제목이 ‘title’이라는 key의 value로 저장되어 있으며, ‘completed’를 통해 그 할 일을 해당 유저가 해냈는지 아직 아닌지를 확인할 수 있다. 

이번에는 전체 데이터를 끌고 와 몇 번 유저가 할 일을 얼마나 많이 끝냈는지 확인해보는 코드를 짜보겠다. 결과값은 더 잘 보이기 위해 다시 json으로 직렬화하겠다.

```python
import json
import requests
from collections import defaultdict

response = requests.get("https://jsonplaceholder.typicode.com/todos")
todos = json.loads(response.text)

todo_done_count = defaultdict(lambda : 0)  # 존재하지 않은 key에 value는 0으로 자동 할당
for todo in todos:
    if todo['completed'] is True:
        todo_done_count[todo['userId']] += 1

count_list = list(todo_done_count.items())  # ex) [(3, 5), (2, 10), ...]

# 이제 가장 높은 횟수를 받은 유저 순으로 정렬해봅시다.
def sort_dict_items(data_list=[]):
    sorted_list = []
    while data_list:
        max_index_data = [0, 0]
        for i, data in enumerate(data_list):
            if data[1] > max_index_data[1]:
                max_index_data[0] = i
                max_index_data[1] = data[1]
        sorted_list.append(data_list.pop(max_index_data[0]))
    return sorted_list

# 정렬된 결과를 보기 좋게 json 구조화
def result_to_json(items_list = []):
    """ json 저장 구조 예
    [
        {
            "id": 3,
            "completedCount": 11
        }
        {
            "id": 5,
            "completedCount": 2
        }
    ]
    """
    json_dict_list = []  # 요소가 모두 dict인 리스트
    for item in items_list:
        temp_dict = {}
        temp_dict['userId'] = item[0]
        temp_dict['completedCount'] = item[1]
        json_dict_list.append(temp_dict)
    return json.dumps(json_dict_list, indent=4)

rank_list = sort_dict_items(count_list.copy())
json_result = result_to_json(rank_list)
print(json_result)
```

```python
[
    {
        "userId": 5,
        "completedCount": 12
    },
    {
        "userId": 10,
        "completedCount": 12
    },
    {
        "userId": 1,
        "completedCount": 11
    },
    {
        "userId": 8,
        "completedCount": 11
    },
    {
        "userId": 7,
        "completedCount": 9
    },
    {
        "userId": 2,
        "completedCount": 8
    },
    {
        "userId": 9,
        "completedCount": 8
    },
    {
        "userId": 3,
        "completedCount": 7
    },
    {
        "userId": 4,
        "completedCount": 6
    },
    {
        "userId": 6,
        "completedCount": 6
    }
]
```

# 사용자가 만든 객체를 직렬화 및 역직렬화하기

지금까지는 파이썬에서 기본으로 제공하는 list, dict 등의 자료형을 바로 json으로 직렬화 또는 역직렬화가 가능했다. 그렇다면 사용자가 직접 정의한 객체도 바로 json으로 직렬화 또는 역직렬화가 가능할까?

다음의 코드를 보자. 

```python
import json

class PlayerInfo:
    def __init__(self, level, hp, inventories):
        self.level = level
        self.hp = hp
        self.inventories = inventories

player_inventory = {
    1: "stone_axe",
    2: "worktable",
    3: "grilled_chicken",
}

player = PlayerInfo(3, 84, player_inventory)
json_result = json.dumps(player)
```

```python
예외가 발생했습니다. TypeError
Object of type PlayerInfo is not JSON serializable
  File "C:\python2\python_basic\study.py", line 17, in <module>
    json_result = json.dumps(player)
TypeError: Object of type PlayerInfo is not JSON serializable
```

위 예제에서는 PlayerInfo라는 클래스를 새로 정의하고 이를 인스턴스화하였다. 이 객체를 JSON으로 직렬화하고자 맨 마지막 코드처럼 바로 dumps() 메서드에 해당 객체를 대입했다. 그러나 실행결과를 보면 알 수 있듯, 해당 객체는 JSON으로 직렬화할 수 없는 자료형임을 알 수 있다. 역직렬화도 마찬가지로 안될 것이다. 

## 파이썬 내장 자료형들로 바꾼 뒤 직렬화하기

그렇다면 사용자가 직접 만든 객체들을 JSON으로 직렬화할 수 있을까? 일단, 사용자 정의 객체를 파이썬 내장 자료형들 (list, dict, str 등등)로 바꿔야 한다. 

이를 실행시켜주는 인코딩 함수를 만들어보겠다. 위 예제 5-1 맨 끝에 다음 코드를 이어서 써보자.

```python
def encode_playerinfo(inst):
    if isinstance(inst, PlayerInfo):
        return [inst.level, inst.hp, inst.inventories]  #1
    else:
        type_name = inst.__class__.__name__
        raise TypeError(f"Object of type '{type_name}' is not JSON serializable ")
```

위 함수는 객체를 인자로 받아들인다. 그 후, 해당 인자가 PlayerInfo 클래스의 인스턴스가 맞는지를 isinstance를 통해 확인한다. 여기서 isinstance(object, class)의 첫 번째 인자로 객체, 두 번째 인자로 클래스가 들어가며, 첫 번째 인자로 들어간 객체가 두 번째 인자로 들어간 클래스 자료형과 맞는지를 확인하는 함수이다. 위 코드에서는 inst 객체가 PlayerInfo 클래스 자료형과 맞을 경우, 해당 객체 내의 정보들을 list로 변환한 후 이를 반환하고 있다(#1). 만약 해당 객체가 해당 클래스와 같은 자료형이 아닐 경우, raise 키워드를 통해 에러를 일으키도록 설정하였다.

즉, 위 코드는 인코딩 되길 원하는 객체만을 인자로 받고, 해당 인자를 JSON으로 직렬화하기 쉽도록 파이썬 내장 자료형(위 코드에서는 list)으로 변환하는 과정이다. 

그 다음, dumps() 메서드에 첫 번째 인자로 직렬화하고자 하는 객체를, 두번째 인자로는 default= 키워드 인자에 앞서 작성한 encode_playerinfo 함수명을 적는다. 즉 다음의 코드와 같다.

```python
json_result = json.dumps(player_inst, default=encode_playerinfo)
```

그 후 print(json_result)로 JSON으로 직렬화가 잘 되었는지 확인해보자. 다음은 실행결과이다.

```python
[3, 84, {"1": "stone_axe", "2": "worktable", "3": "grilled_chicken"}]
```

잘 실행되었음을 알 수 있다.

이번에는 만약 PlayerInfo 클래스와 다른 자료형의 객체를 직렬화하려고 시도하면 어떻게 되는지 보겠다. 이를 위해 다음 코드에서처럼 EmptyClass라는 전혀 다른 클래스를 정의하고, 이의 객체인 empty_inst를 통해 실험해보도록 하겠다. 이 코드와 이전의 코드들을 전부 합친 소스 코드는 다음과 같다.

```python
import json

class PlayerInfo:
    def __init__(self, level, hp, inventories):
        self.level = level
        self.hp = hp
        self.inventories = inventories

class EmptyClass:  # 대조 클래스
    pass

player_inventory = {
    1: "stone_axe",
    2: "worktable",
    3: "grilled_chicken",
}
player_inst = PlayerInfo(3, 84, player_inventory)
empty_inst = EmptyClass()

def encode_playerinfo(inst):
    if isinstance(inst, PlayerInfo):
        return [inst.level, inst.hp, inst.inventories]
    else:
        type_name = inst.__class__.__name__
        raise TypeError(f"Object of type '{type_name}' is not JSON serializable ")
    

json_result = json.dumps(player_inst, default=encode_playerinfo)
print(json_result)
another_json = json.dumps(empty_inst, default=encode_playerinfo)
print(another_json)
```

```python
[3, 84, {"1": "stone_axe", "2": "worktable", "3": "grilled_chicken"}]
예외가 발생했습니다. TypeError
Object of type 'EmptyClass' is not JSON serializable 
  File "C:\python2\python_basic\study.py", line 28, in encode_playerinfo
    raise TypeError(f"Object of type '{type_name}' is not JSON serializable ")
  File "C:\python2\python_basic\study.py", line 33, in <module>
    another_json = json.dumps(empty_inst, default=encode_playerinfo)
TypeError: Object of type 'EmptyClass' is not JSON serializable
```

위 실행결과를 보면 알겠지만, empty_inst라는 객체는 PlayerInfo 클래스 자료형과 다르므로 직렬화하지 못했음을 알 수 있다. 

바로 앞에서는 인코딩을 위해 함수로 정의했지만, 이번에는 인코딩 클래스로 정의해보자. 코드는 다음과 같다.

```python
class PlayerInfoEncoder(json.JSONEncoder):  #1
    def default(self, inst):  #2
        if isinstance(inst, PlayerInfo):
            return [inst.level, inst.hp, inst.inventories]
        else:
            return super().default(inst)  #3
```

위 코드에서는 PlayerInfoEncoder라는 인코딩 클래스를 새로 정의하였다. 이 때 부모클래스로 json.JSONEncoder를 상속받도록 하였다. 이를 이용하면 #3에서 알 수 있듯, 인자로 들어온 객체가 해당 클래스 자료형과 맞지 않을 경우를 어떻게 처리할지에 대해 raise 키워드를 굳이 쓰지 않아도 부모 클래스가 알아서 처리하도록 할 수 있기에 위와 같이 설계를 한 것이다. #2에서 부모 클래스의 메서드인 default를 오버라이딩하여 쓰고 있다. #3에서는 앞서 말했듯, 인자로 들어온 객체가 사용자가 원하는 자료형이 아닐 경우 부모 클래스가 알아서 처리하도록 한 코드이다. 

위 코드는 인코딩 “클래스”이다. 따라서 dumps()에 쓸 매개변수명은 default가 아닌 cls를 쓰면 된다. 즉,

```python
json_result = json.dumps(player_inst, cls=PlayerInfoEncoder)
```

위와 같이 쓰면 되는 것이다. 

이를 종합한 소스 코드는 다음과 같다. 

```python
import json

class PlayerInfo:
    def __init__(self, level, hp, inventories):
        self.level = level
        self.hp = hp
        self.inventories = inventories

class EmptyClass:
    pass

player_inventory = {
    1: "stone_axe",
    2: "worktable",
    3: "grilled_chicken",
}
player_inst = PlayerInfo(3, 84, player_inventory)
empty_inst = EmptyClass()

class PlayerInfoEncoder(json.JSONEncoder):
    def default(self, inst):
        if isinstance(inst, PlayerInfo):
            return [inst.level, inst.hp, inst.inventories]
        else:
            return super().default(inst)
    

json_result = json.dumps(player_inst, cls=PlayerInfoEncoder)
print(json_result)
another_json = json.dumps(empty_inst, cls=PlayerInfoEncoder)
print(another_json)
```

```python
[3, 84, {"1": "stone_axe", "2": "worktable", "3": "grilled_chicken"}]
예외가 발생했습니다. TypeError
Object of type EmptyClass is not JSON serializable
  File "C:\python2\python_basic\study.py", line 28, in default
    return super().default(inst)
  File "C:\python2\python_basic\study.py", line 33, in <module>
    another_json = json.dumps(empty_inst, cls=PlayerInfoEncoder)
TypeError: Object of type EmptyClass is not JSON serializable
```

인코딩 함수를 쓸 때와 똑같이 작동함을 알 수 있다.

정리하면, 사용자 정의 객체를 JSON으로 직렬화하려면 다음의 과정을 거쳐야 한다.

1. 사용자 정의 객체 내 정보들을 파이썬 내장 자료형들로 표현한다. 이를 위해 인코딩 함수 또는 클래스를 정의한다.
2. 인코딩 함수 또는 클래스를 정의할 때 일단 인자로 들어오는 객체가 사용자가 원하는 자료형인지 먼저 확인한다(isinstance()). 그 후 해당 객체 내 정보들을 파이썬 내장 자료형들로 변환하고 이를 반환한다.
3. dump() 또는 dumps()의 첫 인자로 사용자 정의 객체를, 두 번째 인자로 default=인코딩함수명 또는 cls=인코딩클래스명을 써넣고 실행하면 직렬화가 가능하다. 

## 사용자 정의 객체 역직렬화하기

이번에는 앞서 직렬화한 사용자 정의 객체를 다시 역직렬화할 것이다. 앞서 직렬화를 할 때를 다시 한 번 기억해보자. 해당 객체를 파이썬 내장 자료형으로 변환하기 전에 먼저 수행했던 일이 있다. 바로 해당 객체가 바로 사용자가 인코딩하길 원하는 그 객체냐를 먼저 확인하는 작업이었다. 이는 역직렬화에서도 똑같이 적용된다. 역직렬화하고자 하는 JSON 데이터가 사용자가 원하는 사용자 정의 객체의 정보를 담고 있는지를 먼저 확인한 다음에 맞으면 그 때서야 역직렬화를 하는 과정을 거친다. 그러면 어떻게 해야할까?

다시 앞서 작성한 인코딩 클래스로 돌아가보자. 이 클래스 내용을 다음과 같이 변경시켜보자.

```python
class PlayerInfoEncoder(json.JSONEncoder):
    def default(self, inst):
        if isinstance(inst, PlayerInfo):
            return {
                "__playerInfo__": True, 
                "level": inst.level, 
                "hp": inst.hp, 
                "inventories": inst.inventories,
                }
        else:
            return super().default(inst)
```

반환값을 dictionary로 바꾼 것 이외에도 한 가지 추가된 사항이 있다. 바로 “__playerInfo__”: True 라는 코드이다. 사실 해당 value가 True가 아닌 다른 값이더라도 상관없다. 핵심은 “__playerInfo__”처럼 해당 데이터가 특정 사용자 정의 객체임을 알려주는 메타데이터(metadata)을 명시해주는 것이 중요하다는 것이다. 

그 다음, 직렬화된 JSON 데이터를 파이썬 내장 자료형으로 역직렬화해줄 함수를 정의해준다. 코드는 다음과 같다.

```python
def decode_player_info(dumped_json):
    if "__playerInfo__" in dumped_json:  #1
        return PlayerInfo(dumped_json['level'], dumped_json['hp'], dumped_json['inventories']) #2
    return dumped_json  #3
```

#1에서 직렬화된 데이터 내에 “__playerInfo__”라는 메타데이터가 있는지를 확인한다. 존재한다면 해당 데이터는 PlayerInfo 클래스 자료형과 같음을 의미한다. 같음을 확인하였으면 해당 데이터를 PlayerInfo 객체로 역직렬화하도록 #2처럼 코드를 설정하였다. 만약 해당 데이터가 PlayerInfo 객체가 아닐 경우 #3에서 해당 데이터를 그대로 반환하도록 설정하였다. 

그 후에 load(), loads() 메서드 사용시 object_hook=디코딩함수명 이라는 매개변수를 사용하면 된다. 전체 소스코드는 다음과 같다.

```python
import json

class PlayerInfo:
    def __init__(self, level, hp, inventories):
        self.level = level
        self.hp = hp
        self.inventories = inventories

player_inventory = {
    1: "stone_axe",
    2: "worktable",
    3: "grilled_chicken",
}
player_inst = PlayerInfo(3, 84, player_inventory)

class PlayerInfoEncoder(json.JSONEncoder):
    def default(self, inst):
        if isinstance(inst, PlayerInfo):
            return {
                "__playerInfo__": True, 
                "level": inst.level, 
                "hp": inst.hp, 
                "inventories": inst.inventories,
                }
        else:
            return super().default(inst)
    

json_result = json.dumps(player_inst, cls=PlayerInfoEncoder, indent=4)
#print(json_result)

def decode_player_info(dumped_json):
    if "__playerInfo__" in dumped_json:
        return PlayerInfo(dumped_json['level'], dumped_json['hp'], dumped_json['inventories'])
    return dumped_json

decoded_result = json.loads(json_result, object_hook=decode_player_info)
print(decoded_result.level, decoded_result.hp, decoded_result.inventories)
```

```python
3 84 {'1': 'stone_axe', '2': 'worktable', '3': 'grilled_chicken'}
```

무사히 역직렬화되었음을 알 수 있다.

참고로, 디코딩 클래스로 만들 경우, 상속할 부모 클래스로 JSONDecoder를 이용하고, object_hook이라는 메서드를 오버라이딩하면 된다.

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] 

[Working With JSON Data in Python - Real Python](https://realpython.com/python-json/)

[3] 직렬화, 위키백과

[직렬화 - 위키백과, 우리 모두의 백과사전](https://ko.wikipedia.org/wiki/%EC%A7%81%EB%A0%AC%ED%99%94)

[4]

[JSONPlaceholder](https://jsonplaceholder.typicode.com/)

[5] [python] 파이썬 isinstance 타입 확인 함수 설명과 예제

[[python] 파이썬 isinstance 타입 확인 함수 설명과 예제](https://blockdmask.tistory.com/536)