---
title: "[자료구조] 배열 (array)"
category: data structure
tag: ["python", "자료구조", "data structure", "array", "배열"]
---
배열은 메모리상에서 N개의 값들을 1차원으로 연속적으로 배열한 자료구조이다. 여러 개의 값을 하나로 묶어 저장하기에 좋은 자료 구조이다. 

![배열의 예.](/images/2023-06-07/2023-06-07-data-structure-array-1.PNG)

배열의 예.

- 배열의 특정 값의 위치를 알려주는 인덱스는 보통 0부터 시작한다. 마지막 값의 위치는 N-1이다.
- 배열은 그 길이가 고정된다. C언어도 고정된 배열을 사용한다. 반면, 파이썬이나 자바같은 프로그래밍 언어는 실행 도중에 그 길이를 결정할 수 있도록 해준다.
- 배열 내 특정 값을 읽거나 수정하려면 A[i]와 같이 인덱스를 사용하면 된다. 물론, 0 < i < N-1 이다.
- 배열은 한 번 정해지면 그 길이를 변경시킬 수 없다. 다만, 원하는 길이를 갖는 새 배열에 기존 값들을 복사하는 방법을 대신 사용할 수 있다.

파이썬에서는 list를 배열로 사용할 수 있다. 다만 파이썬의 리스트는 길이를 변경시킬 수 있다는 장점이 있다. 

---

Reference

[1] George T. Heineman, “Learning Algorithms: A Programmer’s Guide to Writing Better Code”, (O’Reilly, 2021)