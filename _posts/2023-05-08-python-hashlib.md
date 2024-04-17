---
title: "[지식/이론][python] 해시와 hashlib"
category: knowledge
tag: ["python", "knowledge", "hash", "해시", "hashlib", "library"]
---

hashlib 모듈은 MD5, SHA256 등의 해시 함수로 문자열을 해싱(hashing)할 때 쓰이는 모듈이다. 

# 해시의 개념

해시 함수(hash function) 또는 줄여서 해시(hash)는 임의의 길이를 가지는 데이터를 고정된 길이의 데이터로 변환시키는 단방향 함수이다. 입력값으로 주어지는 문자열이 아무리 길거나 짧아도 해시 함수의 출력값의 길이는 항상 똑같다. 

해시 함수에 입력값을 주어 고정된 길이의 데이터로 변환된 이 출력값을 해시값, 해시 코드, 해시섬, 체크섬 등으로 불린다. 보통 이 출력값은 인간의 언어로는 이해할 수 없는 무작위로 배열된 문자열들로 이루어진다. 

다음은 해시 함수 알고리즘 중 하나인 SHA-256을 이용하여 입력된 문자열을 해싱하는 코드이다.

```python
import hashlib

sentence1 = '너가 이 문장을 이해할 수 있을까?'

hash_obj = hashlib.sha256()
hash_obj.update(sentence1.encode('utf-8'))
digested_hex = hash_obj.hexdigest()

print(digested_hex)
print(len(digested_hex))
```

```python
42e8131a82dcfd781c782bfe55962299ef9ee00cfd4c9dcae8eb863e6f2528f2
64
```

한글로 된 문장이 위 출력결과의 첫 번째 줄과 같이 바뀌었다. 해당 해시값을 보면 사람이 이해할 수 있는 언어가 아님을 알 수 있다. 

이렇게 해시는 입력값, 즉 원문이 무엇인지를 알아낼 수 없도록 난수(random nubmer, 특정 순서나 규칙을 가지지 않는 수)의 문자열로 반환한다. 해시는 단방향 변환 알고리즘으로, 원문에서 해시값으로 변환할 수는 있어도 그 역과정은 불가능하다. 즉, 해시값만으로 원문이 무엇인지를 해석할 수 없다. 

해시는 입력값이 아주 살짝만 달라도 그 출력값이 크게 달라지는 특성을 갖도록 설계된다. 이러한 특징을 “눈사태 효과(avalanche effect)”라고 한다. 이러한 성질은 특히 일반적인 목적의 해시 함수가 아닌 암호학적 해시 함수에 주로 적용된다고 한다. 다음은 맨 끝의 문자만 다른 두 문장을 각각 해싱하는 코드이다.

```python
import hashlib

sentence1 = '너가 이 문장을 이해할 수 있을까?'
sentence2 = '너가 이 문장을 이해할 수 있을까!'

hash_obj = hashlib.sha256()
hash_obj.update(sentence1.encode('utf-8'))
digested = hash_obj.digest()
digested_hex = hash_obj.hexdigest()

print(digested)
print(digested_hex)
print(len(digested_hex))
print("=" * 40)

hash_obj2 = hashlib.sha256()
hash_obj2.update(sentence2.encode('utf-8'))
digested2 = hash_obj2.digest()
digested_hex2 = hash_obj2.hexdigest()

print(digested2)
print(digested_hex2)
print(len(digested_hex2))
```

```python
b'B\xe8\x13\x1a\x82\xdc\xfdx\x1cx+\xfeU\x96"\x99\xef\x9e\xe0\x0c\xfdL\x9d\xca\xe8\xeb\x86>o%(\xf2'
42e8131a82dcfd781c782bfe55962299ef9ee00cfd4c9dcae8eb863e6f2528f2
64
========================================
b'\x7f\x11\xb7\x1f\xc5\x96\xf0\xd9f\xed\xd2HL1q\xae@\xd7-p\xcfH9\x1d\xffz\xa6\xe8.\x9e\x9d&'
7f11b71fc596f0d966edd2484c3171ae40d72d70cf48391dff7aa6e82e9e9d26
64
```

위 출력결과의 2번째 줄과 6번째 줄을 비교해보라. 원문에서는 맨 끝에 물음표와 느낌표로 이 둘만 다를 뿐임에도 해시값이 서로 크게 차이나는 것을 확인할 수 있다. 이러한 눈사태 효과를 응용하여 원본 데이터의 사소한 변화도 감지하여 데이터 전송 시 중간에 해커가 데이터를 위변조했는지 판별하는 데에도 활용된다고 한다. 

해시값은 입력값의 길이가 몇이든 상관없이 똑같은 길이의 해시값을 반환한다. 다음은 서로 길이가 다른 두 문장을 각각 해싱하는 코드이다.

```python
import hashlib

def hash_by_sha256(sentence: str):
    hash_obj = hashlib.sha256()
    hash_obj.update(sentence.encode('utf-8'))
    return hash_obj.hexdigest()

sentence1 = "안녕하세요."
sentence2 = "예, 만나서 반갑습니다."

hash1 = hash_by_sha256(sentence1)
hash2 = hash_by_sha256(sentence2)
print(hash1, len(hash1))
print(hash2, len(hash2))
```

```python
8b118d6741f7cfa1a7ee246d0dda39f2f00bf9fd207b4e6c7fad87a15434a513 64
34f66311f43f942d33e97333b5431b2fc3c7a142aeb0ee886701c3225e6ff88a 64
```

두 문장은 길이가 서로 다름에도 불구하고 해시값의 길이는 똑같이 64임을 알 수 있다. 이렇게 입력값의 길이에 상관없이 해시값의 길이는 고정되어 있을 때 모든 가능한 출력값의 경우의 수보다 모든 가능한 입력값의 경우의 수가 더 많을 경우, 입력값이 서로 다른데도 똑같은 출력값이 나타날 수도 있다. 이를 충돌한다고 표현한다. (해시 충돌) 예를 들어, 어떤 사람이 어떤 홈페이지에 로그인하려고 비밀번호를 쳤는데 전혀 엉뚱한 사람의 것으로 로그인된 것이다. 알고보니 두 사람의 비밀번호가 서로 다른데도 이 둘의 비밀번호의 해시값이 우연히 동일했던 것이다. 실제에서 이러한 상황을 최대한 방지하기 위해 충돌을 해결하는 여러 방법들이 존재하고, 또, 여러 해시 함수 알고리즘 중 해시 충돌의 가능성이 최소인 알고리즘을 골라 사용한다고 한다. 

이러한 해시의 특징으로 인해, 해시는 데이터 인덱싱, 데이터 검색, 디지털 서명, 보안, 암호화 등의 여러 분야에서 활용된다고 한다. 

# hashlib

이제 해시를 실행해주는 파이썬 라이브러리인 hashlib에 대해 살펴보겠다.

앞서 해시 알고리즘에는 여러 가지가 있어서 그 중 하나를 택하면 된다. 여기서는 sha256 방식으로 진행해보겠다.

```python
import hashlib

hash_obj = hashlib.sha256()
```

이제 원문을 해싱해보겠다. 위 예제에서 hashlib.sha256()으로 생성한 객체를 담은 변수에서 .update() 함수를 사용하면 된다. 이 때, update() 인자 안에 원문을 담을 때 문자열 형태가 아닌 바이트 형태로 대입해야하므로 .encode(’utf-8’)을 이용하여 바이트로 인코드한 값을 대입해야 한다. 

```python
import hashlib

hash_obj = hashlib.sha256()
hash_obj.update('난 파이썬이 좋아'.encode('utf-8'))
```

추가할 문자열이 있다면 update() 함수를 통해 추가 문자열을 대입하면 된다.

```python
import hashlib

hash_obj = hashlib.sha256()
hash_obj.update('난 파이썬이 좋아'.encode('utf-8'))
hash_obj.update('나도 파이썬 좋아'.encode('utf-8'))
```

이 원문의 해시값을 얻고자 한다면 digest() 또는 hexdigest() 함수를 사용하면 얻을 수 있다.

```python
import hashlib

hash_obj = hashlib.sha256()
hash_obj.update('난 파이썬이 좋아'.encode('utf-8'))
hash_obj.update('나도 파이썬 좋아'.encode('utf-8'))

print(hash_obj.digest())
print(hash_obj.hexdigest())
```

```python
b'\xc2\xc4\xd2\xad i\x99\x9d\x98\xdc\xb5\x9d\xa4\xdfR\xf3a\xdb\xd9\x17\xb3\x9a\xb1\x05\x18\xf7\n\xc4<K\xf4\xea'
c2c4d2ad2069999d98dcb59da4df52f361dbd917b39ab10518f70ac43c4bf4ea
```

digest()는 해싱한 바이트 문자열을 반환하고, hexdigest()는 해싱한 바이트 문자열을 16진수로 변환한 값을 반환한다. 

# 활용

해시값만으로는 원문을 알 수 없다. 그러나 동일한 원문을 해싱하면 동일한 해시값을 얻게 된다. 이를 이용하면, 내 비밀번호를 외부인이 알아볼 수 없는 형태로 뭉개버려 내 비밀번호가 무엇인지 모르도록 보호할 수 있으면서도 동시에 로그인할 때 인증 가능하다. 다음은 텍스트 파일에 내 비밀번호를 저장할 때 보안을 위해 SHA-256 알고리즘으로 해싱하여 그 해시값을 저장하고, 로그인 시 사용자가 입력한 비밀번호의 해시값이 텍스트 파일에 저장된 해시값과 똑같은지 판별하고, 똑같으면 해당 사용자가 맞다고 간주하고 통과시키는 예제이다. 

```python
import hashlib

password_file_name =' my_password.txt'

def make_new_password(new_password: str):
    hash_obj = hashlib.sha256()
    hash_obj.update(new_password.encode('utf-8'))
    with open(password_file_name, 'w') as f:
        f.write(hash_obj.hexdigest())
    

def check_password(input_password: str):
    hash_obj = hashlib.sha256()
    hash_obj.update(input_password.encode('utf-8'))
    with open(password_file_name, 'r') as f:
        hash_data = f.read()
    return hash_data == hash_obj.hexdigest()

new_password = input("새 비밀번호 입력: ")
make_new_password(new_password)
login = input("로그인을 위해 비밀번호를 입력: ")
if check_password(login):
    print("로그인 완료! 어서오세요.")
else:
    print("로그인 실패. 비밀번호가 일치하지 않습니다.")
```

```python
새 비밀번호 입력: 난 파이썬이 좋아
로그인을 위해 비밀번호를 입력: 난 파이썬이 좋아
로그인 완료! 어서오세요.
```

다음은 일부러 틀린 비밀번호를 입력했을 때의 모습이다. 이미 비밀번호는 생성했기 때문에 예제 3-1의 new_password=input() 줄과 make_new_password(new_password) 코드를 주석 처리한 다음 해당 예제를 그대로 실행시킨다. 그리고 다음과 같이 입력하였다.

```python
로그인을 위해 비밀번호를 입력: 난 파이썬이 조아
로그인 실패. 비밀번호가 일치하지 않습니다.
```

한 글자만 틀렸음에도 기존 비밀번호와 일치하지 않아 로그인되지 않았다. 

---

Reference

[1] 

[056 비밀번호를 암호화하여 저장하려면? ― hashlib](https://wikidocs.net/122201)

[2]

[](https://namu.wiki/w/해시#s-8)

[3]

[해시 함수는 무엇인가? - 업비트 투자자보호센터](https://upbitcare.com/academy/education/blockchain/52)

[4]

[Phind: AI search engine](https://www.phind.com/search?cache=01c1894a-748b-4b0f-a188-6a5f45a5204a)