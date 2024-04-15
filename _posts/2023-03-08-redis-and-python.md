---
layout: single
title:  "[Python][NoSQL] Redis와 파이썬"
categories: ["Python"]
tag: ["Python", "데이터베이스", 'DB', 'Database', "NoSQL", "Redis"]
toc: true
#search: false # 해당 포스트가 블로그 내 검색 기능에 검색되지 않고자 할 때 false 사용.
sidebar:
    nav: "counts"
---
# 개요

Redis는 데이터 구조 서버 (data structure server)로, 키-값(key-value) 형태의 데이터를 메모리(램, RAM)에 저장하는 인메모리(in-memory) 방식의 NoSQL이다. 데이터베이스를 메모리에 저장하여 쓰는 구조라서 서버를 종료시키면 데이터가 사라진다. 그러나 인메모리 DB임에도, 데이터를 디스크에 영구적으로 저장하여 다음에도 그대로 데이터를 사용할 수 있는 기능을 제공한다고 한다(참고로 이렇게 영구적으로 저장하여 다음에도 사용할 수 있는 특성을 “영속성(persistence)”이라고 한다). 

데이터를 디스크에 저장하는 방식은 문제가 발생할 때 데이터가 손실되지 않는다는 장점이 있으나 매번 디스크에 접근해야하므로 속도가 상대적으로 느리다. 그러나 인메모리 방식은 메모리에 데이터를 쓰고 읽기 때문에 접근 속도가 빠르다. 

Redis의 장점은 인메모리 방식에 의한 데이터베이스로의 빠른 접근 속도뿐만 아니라 string, list 등 다양한 자료구조를 제공하여 좀 더 쉽게 데이터베이스를 구축할 수 있기도 하다. 

# 파이썬에서 Redis를 실행하기 전 해야 할 두 가지

파이썬에서 Redis를 사용하려면 먼저 두 가지를 해야 한다. 

1. pip install redis를 통해 해당 모듈을 먼저 다운받아야 한다.
2. 그럼에도 파이썬에서 실행이 안될 수 있다. 만약 Windows 운영체제에서 코드를 잘 짰음에도 다음의 오류가 난다면,
    
    ```python
    ConnectionError Error 10061 connecting to localhost:6379. 대상 컴퓨터에서 연결을 거부했으므로 연결하지 못했습니다.
    ```
    
    Windows용 Redis 파일을 따로 설치해줘야한다. References의 [7] 사이트에서 .msi 파일 또는 .zip 파일을 다운받아 설치하면 된다. 참고로 .msi 파일로 다운 받으면 자동으로 환경변수를 설정해주는 등의 이유로 좀 더 편하게 설치할 수 있다는 장점이 있다.
    

위 과정을 거쳤으면 이제 파이썬에서 redis 모듈을 사용할 수 있을 것이다.

# 시작하기

Redis는 앞서 말했듯 “서버”이므로 host와 port를 명시하여 redis 서버에 먼저 접속해야 한다. 여기서는 네트워크나 온라인을 통해 다른 사용자와 공유하여 사용하는 목적이 아니라 Redis에 대해 간단히 알아보는 것이 목적이라서 자신의 컴퓨터 내에서만 사용할 것이다. 

파이썬에서 처음 Redis 서버에 연결하려면 코드 맨 윗 부분에 다음과 같이 적으면 된다.

```python
import redis

conn = redis.Redis()
```

Redis()의 인자로 첫 번째 인자는 host, 두 번째 인자로 port를 입력하면 된다. 디폴트는 host의 경우 ‘localhost’로 되어 있고, port는 6379로 되어 있다. 앞서 말했듯 여기서는 서버를 내 컴퓨터 내에서만 사용할 것이므로 위 예제처럼 아무런 인자를 넘기지 않아도 되고, Redis(’localhost’) 또는 Redis(’localhost’, 6379)라 해도 똑같이 내 컴퓨터에서 서버를 연결시킨다. 

이 페이지에서는 Redis에 대해 깊이 들어가기보다는 Redis가 어떤 것인지 간단히 알아볼 목적이므로 여러 자료형 중 몇 가지와 그와 관련된 여러 메서드들을 중심으로 살펴볼 것이다. 

# string

현재 연결된 서버에 저장된 모든 key들을 list 형태로 반환하여 확인해줄 수 있는 코드는 다음과 같다.

```python
print(conn.keys('*')
```

## set(), get()

아무런 데이터도 없으므로 데이터를 만들어보자. conn 객체의 set() 메서드의 첫 째 인자로 문자열 형태의 key를, 두 번째 인자로 value를 대입하면 key-value 데이터가 저장될 것이다. 해당 데이터들이 잘 저장되었는지 확인하기 위해 get() 메서드 안에 key를 대입하면 그에 대응되는 value를 추출할 수 있다.

```python
import redis

conn = redis.Redis()

# 새로 데이터 삽입하기
print(conn.set('some_string', 'hello'))
conn.set('korean', '한글도 되나?')  # string
conn.set('my_books', 18)  # integer
conn.set('some_float', 42.2)  # float

# key를 명시하여 그에 해당하는 value 추출
print(conn.get('some_string'))
print(conn.get('korean'))
print(conn.get('my_books'))
print(conn.get('some_float'))
```

```python
True
b'hello'
b'\xed\x95\x9c\xea\xb8\x80\xeb\x8f\x84 \xeb\x90\x98\xeb\x82\x98?'
b'18'
b'42.2'
```

한글은 유니코드로 변환된 것 같다. 

## setnx()

setnx() 메서드는 첫 번째 인자로 대입되는 key가 데이터베이스에 없을 때만 두 번째 인자로 대입되는 value와 함께 새 데이터가 삽입된다. 

```python
import redis

conn = redis.Redis()

# 새로 데이터 삽입하기
conn.set('some_string', 'hello')
conn.set('korean', '한글도 되나?')  # string
conn.set('my_books', 18)  # integer
conn.set('some_float', 42.2)

conn.setnx('some_string', '이거 데이터 이미 있을까?') #1
print(conn.get('some_string'))

conn.setnx('coding', 'python') #2
print(conn.get('coding'))
```

```python
b'hello'
b'python'
```

#1에서는 이미 some_string이라는 key가 앞서 정의되었기 때문에 새로운 value로 치환되지 않았음을 실행결과를 통해 알 수 있다. 그러나 #2의 coding이라는 key는 이전에 없던 데이터이기 때문에 새로 정의된 것을 알 수 있다.

## getset()

getset(’key’, value) 메서드는 이전에 있던 key의 value를 반환함과 동시에 두 번째 인자로 대입되는 새로운 value로 재설정된다. 

```python
print(conn.getset('some_string', 'hi, nice to meet you!'))
print(conn.get('some_string'))

print(conn.getset('nothing', 'nothing'))
print(conn.get('nothing'))
```

```python
b'hello'
b'hi, nice to meet you!'
None
b'nothing'
```

만약 기존에 없는 key를 참조하면 None이 반환되며, 새로 데이터가 생성된다. 

## getrange(), setrange()

getrange(’key’, start, end) 메서드는 첫째 인자로 key를 대입하면 그에 대응되는 value에서 start 인덱스부터 end 인덱스까지의 부분 문자열을 추출하여 반환한다. 이 때 인덱스는 파이썬과 같이 0부터 시작하며, 맨 끝 부분은 -1로 인덱싱한다. 

```python
print(conn.getrange('some_string', 3, -1))
```

```python
b' nice to meet you!'
```

위 예제는 해당 value의 4번째 문자부터 마지막 문자까지를 반환하고 있다. 

setrange(’key’, start, ‘새로 바꿀 문자열’) 메서드는 반대로 해당 key의 value 중 start 인덱스에 해당하는 문자부터 새로운 부분 문자열로 치환해준다. 

```python
conn.setrange('some_string', 0, 'oh,')
print(conn.get('some_string'))
```

```python
b'oh, nice to meet you!'
```

위 예제는 0, 즉 첫 번째 문자열부터 시작하여 ‘oh,’라는 새로운 문자열로 “덮어썼다”. 여기서는 ‘hi,’를 ‘oh,’로 바꿨는데, 이보다 더 긴 문자열로 치환하면 그대로 덮어쓰기 때문에 조금 이상한 결과를 얻을 수도 있다. 예를 들면 다음과 같다.

```python
conn.setrange('some_string', 0, 'hello,')
print(conn.get('some_string'))
```

```python
b'hello,ce to meet you!'
```

## mset(), mget()

한꺼번에 여러 개의 key-value 데이터들을 쓰거나 읽어올 수 있다. 

mset() 안에 dictionary 형태로 여러 개의 key-value 데이터를 대입하면 해당 데이터들이 데이터베이스에 저장된다.

mget() 안에 list 안에 key들을 입력하면 해당 key들에 대응되는 value들을 한꺼번에 얻을 수 있다.

```python
conn.mset({'roll': 'cake', 'cheese': 'pizza'})
print(conn.mget(['roll', 'cheese']))
```

```python
[b'cake', b'pizza']
```

## delete()

데이터를 삭제하려면 delete() 메서드에 해당 key를 대입하면 된다.

```python
print(conn.keys('*'))
conn.delete('roll')
print(conn.keys('*'))
```

```python
[b'some_float', b'some_string', b'cheese', b'korean', b'roll', b'nothing', b'coding', b'my_books']
[b'some_float', b'some_string', b'cheese', b'korean', b'nothing', b'coding', b'my_books']
```

## 숫자 증감시키기

기존의 정수, 실수 value를 갖는 데이터에 대해 해당 숫자를 증감시킬 수 있다. 숫자를 증가시키는 메서드는 incr() 또는 incrbyfloat() 이다. 감소는 decr() 메서드를 사용하면 된다. 단, decrbyfloat() 메서드는 존재하지 않으므로 실수를 감소시키고자 한다면 decr() 메서드의 두 번째 인자로 감소시킬 숫자를 대입할 때 앞에 마이너스 부호 (-)를 붙이면 된다.

```python
result = conn.incr('my_books')
print(result)
```

```python
19
```

incr(), decr() 함수의 두 번째 인자에 아무것도 대입하지 않는다면 기본으로 1을 증감시킨다. 

그리고 해당 함수들을 쓰면 실제로 데이터베이스 내에서 값이 바뀌어서 저장된다. 이를 확인하기 위해서 다음과 같은 코드를 짜고 실행하여 출력 결과를 살펴보았다. 다음 예제는 위의 예제 7-1을 실행하기 이전에 먼저 실행하였다.

```python
import redis

conn = redis.Redis()
all_keys = conn.keys('*')

def dict_str(all_keys):
    """
    현재 데이터베이스 내의 모든 데이터를 dictionary 형태로 반환
    """
    all_keys_str = [key.decode('utf-8') for key in all_keys]
    all_values_str = [value.decode('utf-8') for value in conn.mget(all_keys_str)]
    dict_data = dict(zip(all_keys_str, all_values_str))
    return dict_data

all_data = dict_str(all_keys)
print(all_data)
```

```python
{'some_float': '42.2', 'some_string': 'hi, nice to meet you!', 'cheese': 'pizza', 'korean': '한글도 되나?', 'nothing': 'nothing', 'coding': 'python', 'my_books': '18'}
```

위 실행결과를 자세히 보면 ‘my_books’에 해당하는 value가 18이고, 이는 예제 7-1을 실행하기 이전의 데이터이다. 다음은 예제 7-1을 실행 후 똑같이 예제 7-2를 실행한 결과이다.

```python
{'some_float': '42.2', 'some_string': 'hi, nice to meet you!', 'cheese': 'pizza', 'korean': '한글도 되나?', 'nothing': 'nothing', 'coding': 'python', 'my_books': '19'}
```

잘 보면 ‘my_books’의 값이 19로 변한 것을 알 수 있다. 

다음부터는 앞서 언급한 함수들을 여러 경우로 테스트한 결과들이다.

```python
print(conn.incr('my_books', 10))
```

```python
29
```

```python
print(conn.decr('my_books', 10))
```

```python
19
```

```python
print(conn.decr('my_books'))
```

```python
18
```

```python
print(conn.incrbyfloat('some_float')) # 기존값: 42.2
```

```python
43.2  # 실수도 1씩 오른다.
```

```python
print(conn.incrbyfloat('some_float', 0.5))
```

```python
43.7
```

```python
print(conn.incrbyfloat('some_float', -1.2))
```

```python
42.5
```

# list

Redis의 list에는 오로지 문자열만 담을 수 있다. lpush() 메서드를 이용하면 list내에 메서드의 인자를 요소로 삽입하며, list가 없었다면 그 list를 자동으로 새로 생성해준다. 다음 예제는 food라는 list에 ‘apple’이라는 요소를 삽입한다.

```python
len_list = conn.lpush('food', 'apple')
print(len_list)
```

```python
1
```

food라는 list의 길이를 반환하는 것 같다. 참고로 lpush()는 list의 맨 왼쪽에 데이터를 삽입하는 방식이다. 맨 오른쪽에 데이터를 삽입하고자 한다면 rpush() 메서드를 사용하면 된다.

한 번에 여러 개의 요소를 넣을 수도 있다.

```python
len_list = conn.lpush('food', 'banana', 'tomato')
print(len_list)
```

```python
3
```

list의 요소 중 하나를 출력하려면 lindex() 메서드를 쓰면 된다. 첫 째 인수는 list 이름, 둘 째 인자는 index를 넣으면 된다.

```python
some_list = conn.lindex('food', 1)
print(some_list)
```

```python
b'banana'
```

한 번에 여러 개의 요소들을 받고자 한다면 lrange(list_name, start_idx, end_idx) 메서드를 이용하면 된다. (여기서도 인덱싱은 파이썬과 같이 0부터 시작하고, 맨 마지막 요소의 인덱스를 -1로 지정할 수 있다)

```python
some_list = conn.lrange('food', 0, -1)
print(some_list)
```

```python
[b'tomato', b'banana', b'apple']
```

linsert(’list’, ‘after or before’, ‘기존 요소’, ‘새로 넣을 요소’) 메서드를 이용하면 list내 특정 위치에 새 요소를 삽입할 수 있다. 

```python
some_list = conn.linsert('food', 'before', 'apple', 'hamburger')
print(some_list)
conn.linsert('food', 'after', 'apple', 'pizza')
print(conn.lrange('food', 0, -1))
```

```python
4
[b'tomato', b'banana', b'hamburger', b'apple', b'pizza']
```

위 예제의 첫 번째 코드에서, ‘food’라는 list 내에 ‘apple’이라는 요소 바로 앞(before)에 ‘hamburger’를 삽입하는 코드를 입력하였다. 세번째 줄에서는 ‘apple’이라는 요소 뒤(after)에 ‘pizza’를 삽입하고 있다. 

lset(’list’, index, ‘새 요소’) 메서드를 이용하면, 특정 index에 새 요소를 삽입한다. 이 때, 기존에 있던 해당 index에 있던 요소가 새 요소로 바뀐다. 

```python
print(conn.lset('food', 1, 'noodle'))
print(conn.lrange('food', 0, -1))
```

```python
True
[b'tomato', b'noodle', b'hamburger', b'apple', b'pizza']
```

ltrim(’list’, start_idx, end_idx) 메서드는 list 중 원하는 요소들만 남기고 나머지 요소들은 삭제하는 메서드이다. start_idx부터 end_idx까지의 인덱스에 해당하는 요소들만 남고 나머지는 삭제된다.

```python
print(conn.ltrim('food', 1, -2))
print(conn.lrange('food', 0, -1))
```

```python
True
[b'noodle', b'hamburger', b'apple']
```

# hash

Redis의 자료형 중 하나인 hash는 파이썬의 dictionary와 비슷하나 오직 문자열만 삽입할 수 있다.

hash 데이터를 하나 만들고 여러 개의 field를 동시에 삽입하려면 hmset() 메서드를 이용하면 된다.

```python
print(conn.hmset('my_favorite_songs', {'remember': 'katie', 'hypeboy': 'newjeans'}))
```

```python
True
```

아니면 한 번에 하나의 필드만을 삽입할 수 있다. 이는 hset() 메서드를 이용하면 된다. hset(’hash’, ‘key’, ‘value’)

```python
print(conn.hset('my_favorite_songs', 'pandora', 'mave'))
```

```python
1
```

필드 값 하나를 얻으려면 hget(’hash’, ‘key’) 메서드를 이용하면 된다.

```python
print(conn.hget('my_favorite_songs', 'remember'))
```

```python
b'katie'
```

여러 개의 key들에 대응되는 여러 value들을 얻으러면 hmget(’hash’, ‘key1’, ‘key2’, …) 메서드를 이용하면 된다.

```python
print(conn.hmget('my_favorite_songs', 'remember', 'hypeboy', 'pandora'))
```

```python
[b'katie', b'newjeans', b'mave']
```

hash 내 모든 key들을 얻으려면 hkeys(’hash’)를 이용하면 된다.

```python
print(conn.hkeys('my_favorite_songs'))
```

```python
[b'remember', b'hypeboy', b'pandora']
```

모든 value들을 얻으러면 hvals(’hash’) 메서드를 사용하면 된다.

```python
print(conn.hvals('my_favorite_songs'))
```

```python
[b'katie', b'newjeans', b'mave']
```

hash에 저장된 필드들의 수는 hlen() 메서드를 이용하여 얻을 수 있다.

```python
print(conn.hlen('my_favorite_songs'))
```

```python
3
```

모든 key-value 데이터들을 얻으려면 hgetall() 메서드를 사용한다.

```python
print(conn.hgetall('my_favorite_songs'))
```

```python
{b'remember': b'katie', b'hypeboy': b'newjeans', b'pandora': b'mave'}
```

만약 key가 존재하지 않는다면 그에 해당하는 value를 부여하기 위해 hsetnx(’hash’, ‘key’, ‘value’) 메서드를 사용할 수 있다. 

```python
print(conn.hsetnx('my_favorite_songs', 'apollo 11', 'jamie'))
print(conn.hgetall('my_favorite_songs'))
```

```python
1
{b'remember': b'katie', b'hypeboy': b'newjeans', b'pandora': b'mave', b'apollo 11': b'jamie'}
```

# set

Redis의 set도 파이썬의 set과 유사하다. 

sadd() 메서드를 이용하여 set에 한 개 또는 그 이상의 값들을 삽입한다. 

```python
print(conn.sadd('my_house', 'books', 'dishes', 'clock'))
```

```python
3
```

scard(’set’) : 해당 set 내 모든 값들의 개수를 반환

```python
print(conn.scard('my_house'))
```

```python
3
```

smembers(’set’) : set의 모든 값 반환

```python
print(conn.smembers('my_house'))
```

```python
{b'clock', b'dishes', b'books'}
```

srem(’set’, ‘value’) : set 내에 value 하나를 삭제한다.

```python
print(conn.srem('my_house', 'dishes'))
print(conn.smembers('my_house'))
```

```python
1
{b'clock', b'books'}
```

sinter(’set1’, ‘set2’) : 두 set 간 교집합 반환

```python
set1 = 'my_house'
set2 = 'my_room'
conn.sadd(set2, 'books', 'laptop', 'clothes')
print(conn.sinter(set1, set2))
```

```python
{b'books'}
```

sinterstore(’new_set’, ‘set1’, ‘set2’) : 두 set의 교집합 요소들을 새로운 set에 삽입하여 저장

```python
set1 = 'my_house'
set2 = 'my_room'
print(conn.sinterstore('intersection_house', set1, set2))
print(conn.smembers('intersection_house'))
```

```python
1
{b'books'}
```

sunion(’set1’, ‘set2’) : 두 set 간 합집합 반환

```python
set1 = 'my_house'
set2 = 'my_room'
print(conn.sunion(set1, set2))
```

```python
{b'laptop', b'books', b'clothes', b'clock'}
```

sunionstore(’new_set’, ‘set1’, ‘set2’) : 두 set 간 합집합 요소들을 새 set을 만들어 저장.

```python
set1 = 'my_house'
set2 = 'my_room'
print(conn.sunionstore('union_house', set1, set2))
print(conn.smembers('union_house'))
```

```python
4
{b'laptop', b'clothes', b'clock', b'books'}
```

sdiff(’set1’, ‘set2’) : set1 - set2. 즉 차집합 반환. (set1에만 존재하는 것)

```python
set1 = 'my_house'
set2 = 'my_room'
print(conn.sdiff(set1, set2))
```

```python
{b'clock'}
```

sdiffstore(’new_set’, ‘set1’, ‘set2’) : set1 - set2 차집합 요소들을 새 set을 만들어 거기에 저장.

```python
set1 = 'my_house'
set2 = 'my_room'
print(conn.sdiffstore('house_minus_room', set1, set2))
print(conn.smembers('house_minus_room'))
```

```python
1
{b'clock'}
```

# sorted set (zset)

zset 자료형은 고유한 값들의 set이지만 각 값들에 그와 대응되는 숫자인 score를 제공한다. 따라서 각각의 데이터를 값 또는 score로 접근할 수 있다. 이러한 특성으로 인해, “게임 내 최상위 유저 순위와 점수”, “홈페이지에 로그인한 유저명과 로그인한 시간” 등에 응용할 수 있다. 

오락실 기기에 최고 점수를 얻은 유저들의 이름과 각각 얻은 점수를 보여주는 leader board 예를 들어보자.

zadd(’zset_key’, {‘value’: score’}) : key에 새로운 값과 score를 삽입해준다. (해당 형식은 redis 3.x 버전 이상부터 변경되었다. 그 이전 버전에서는 zadd(’zset_key’, ‘value’, ‘score’) 형식이었다)

```python
print(conn.zadd('users', {'iamfighter': random.random() * 1000}))
conn.zadd('users', {'greatkill': random.random() * 1000})
conn.zadd('users', {'imtheboss': random.random() * 1000})
conn.zadd('users', {'whoami': random.random() * 1000})
print(conn.zrange('users', 0, -1))
print(conn.zrange('users', 0, -1, withscores=True))
```

```python
1
[b'whoami', b'greatkill', b'imtheboss', b'iamfighter']
[(b'whoami', 130.6948967993945), (b'greatkill', 481.9502575921775), (b'imtheboss', 693.8963658889295), (b'iamfighter', 834.1359318592408)]
```

위 예제에서 zrange(’key’, start_idx, end_idx)는 시작 인덱스와 끝 인덱스를 지정하면 그 사이에 위치한 데이터들을 반환한다. withscores=True를 대입하면 score도 함께 나온다. 

zrank(’key’, ‘value’) : key 내의 value의 순위를 반환

```python
print(conn.zrank('users', 'imtheboss'))
```

```python
2
```

zscore(’key’, ‘value’) : value와 대응되는 score 반환

```python
print(conn.zscore('users', 'imtheboss'))
```

```python
693.8963658889295
```

# expiration

Redis에서는 expiration date (만료 시간) 기능을 제공한다. 특정 키에 대해서 특정 시간이 지난 후에는 삭제하도록 하는 기능이다. 디폴트로 영원히 키를 보존하도록 설정되어있다. 

expire(key, seconds) 메서드를 통해 특정 key가 몇 초까지 존재하게 할 지를 결정할 수 있게 해준다. 

다음은 특정 key가 5초 후에 삭제되도록 하는 예제이다.

```python
import redis
import time

conn = redis.Redis()

temp_key = 'i will disappear'
conn.set(temp_key, 'in 5 seconds')
conn.expire(temp_key, 5)
print(conn.ttl(temp_key))  # 해당 키가 만료될 때까지 남은 시간 반환
print(conn.get(temp_key))
time.sleep(6)
print(conn.get(temp_key))
```

```python
5
b'in 5 seconds'
None
```

해당 기능을 이용하면 로그인 유지 시간 제한, 본인인증 제한 시간 등의 기능 구현에 활용될 수 있을 것이다. 

---

References

[1] Bill Lubannovic, "Introducing Python" (O'REILLY, 2015)

[2] [Windows에서 Python으로 Redis 사용법](https://jhb.kr/353)

[3] [Windows 에 Redis 설치하기](https://hwigyeom.ntils.com/entry/Windows-에-Redis-설치하기-1)

[4] [[REDIS] 📚 캐시 데이터 영구 저장하는 방법 (RDB / AOF)](https://inpa.tistory.com/entry/REDIS-📚-데이터-영구-저장하는-방법-데이터의-영속성)

[5] [[DB] Redis란 무엇일까? 간단하게 알아보기!](https://devlog-wjdrbs96.tistory.com/374)

[6] [Redis란? 레디스의 기본적인 개념 (인메모리 데이터 구조 저장소)](https://wildeveloperetrain.tistory.com/21)

[7] [https://github.com/microsoftarchive/redis/releases](https://github.com/microsoftarchive/redis/releases)

[8] [not able to insert data using ZADD(sorted set ) in redis using python](https://stackoverflow.com/questions/53553009/not-able-to-insert-data-using-zaddsorted-set-in-redis-using-python)