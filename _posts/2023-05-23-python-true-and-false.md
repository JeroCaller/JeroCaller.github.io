---
title: "[지식/이론][python] 참과 거짓"
category: knowledge
tag: ["python", "knowledge", "false", "style guide"]
---
# 암묵적 False

구글 파이썬 스타일 가이드[2]에 따르면, True 또는 False인지 판별하는 구문을 작성할 때 “암묵적 False (implicit false) 사용”을 하도록 권고하고 있다. 여기서 암묵적 false는 `if bool_var != []:` 대신 `if bool_var:` 처럼 조건식을 말 그대로 “암묵적”으로 작성하는 것을 의미한다. 이처럼 boolean 또는 None이 조건식에 쓰일 경우 다음의 규칙을 따르도록 권고하고 있다. 

- None의 경우, 조건식에서 !=, == 기호 대신 `is None` 또는 `is Not None` 을 이용하여 작성한다.
- boolean의 경우 조건식에서 !=, == 기호 대신 `if not x:`  또는 `if x:` 와 같은 방식으로 작성한다.
- 조건식에 False와 None을 같이 사용해서 이 둘을 구별할 필요가 있는 경우, `if not x and x is not None`과 같이 연결 표현식을 사용한다.
- 시퀀스 자료형이 조건식에 포함되는 경우, 빈 시퀀스 (리스트는 [], 문자열은 ‘’ 등등) 가 False 값을 가진다는 사실을 이용한다. 따라서 `if len(seq):` 또는 `if not len(seq)` 대신 `if seq:` 또는 `if not seq:` 를 사용한다.
- 정수를 처리할 때는 의도치 않게 None을 0으로 처리하는 경우와 같이 암묵적 False의 사용이 더 위험하다. 따라서 정수의 경우 !=, == 등의 기호를 사용한다.

```python
some_seq = []
if not some_seq:
    print('no element in the sequence.')

number = 2
if number % 2 == 0:
    print(f'{number} is even.')

bool_var = True
def some_func():
    return None

if bool_var and some_func() is None:
    print('if_code is activated')
```

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)

[2] 

[styleguide](https://google.github.io/styleguide/pyguide.html#214-truefalse-evaluations)