---
title: "[지식/이론][python] byte와 byte array"
category: knowledge
tag: ["python", "knowledge", "byte", "bytearray"]
---
파이썬에는 raw byte(원시 바이트) 처리 시 사용할 수 있는 자료형으로 불변형의 바이트(byte)와 가변형의 바이트 배열(bytearray)이 존재한다. 두 자료형 모두 0~255 범위의 부호 없는 8비트 정수 시퀀스로 구성되어있다. 

```python
byte_list = [1, 2, 16, 255]

some_bytes = bytes(byte_list)
print(some_bytes)

some_byte_array = bytearray(byte_list)
print(some_byte_array)

some_byte_array[2] = 32  # mutable
some_bytes[2] = 32 # immutable
^
TypeError: 'bytes' object does not support item assignme
```

```python
b'\x01\x02\x10\xff'
bytearray(b'\x01\x02\x10\xff')
```

---

Reference

[1] 지은이: 미아 스타인, 옮긴이: 최길우, “파이썬 자료구조와 알고리즘”, (2019, 한빛미디어)