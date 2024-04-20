---
title: "[알고리즘] 시간복잡도와 공간복잡도"
category: "Algorithm"
tag: ["algorithm", "알고리즘", "complextiy"]
---

시간 복잡도(time complexity)는 알고리즘 내에서 수행되는 기본 연산(덧셈, 곱셈, 할당, 조건문 등등)의 수를 세어 이를 문제 인스턴스의 크기 N의 함수 c(N)으로 나타내어 해당 알고리즘의 수행 시간을 예측한다. 앞서 [알고리즘 개요](/algorithm/algorithm-introduce/) 페이지나 [토너먼트 알고리즘 (Tournament Algorithm)](/algorithm/algorithm-tournament-algorithm/) 의 예제에서 사용된 비교 연산 “<”의 수행 횟수를 세는 것도 이에 해당한다.

기본 연산을 한 번 하는 데에 t의 시간이 걸린다고 가정할 때, 알고리즘의 수행 시간은 T(N) = t x c(N)이라 할 수 있다. 여기서 t는 CPU의 성능에 따라 달라질 수 있다. 

공간 복잡도 (space complexity)는 크기 N의 문제 인스턴스를 풀기 위해 알고리즘에서 요구하는 추가 메모리양을 의미한다. 

```python
def find_max(array: list) -> int | None:
    try: 
        current_max = array[0]
    except IndexError: 
        # 최대값이 존재하지 않으므로 None 반환.
        return None  
    
    for i, value in enumerate(array):
        if i == 0: continue
        if current_max < value:
            current_max = value
    return current_max
```

위 코드는 [알고리즘 개요](/algorithm/algorithm-introduce/) 페이지의 예제 2-1을 그대로 가져온 것이다. 위 알고리즘을 보면 추가로 사용되는 공간은 current_max 뿐이고, 해당 공간은 입력값에 상관없이 일정한 것을 알 수 있다. 즉, 이 알고리즘의 공간 복잡도는 문제 인스턴스의 크기 N에 무관하여 상수이다. 

반면 [토너먼트 알고리즘 (Tournament Algorithm)](/algorithm/algorithm-tournament-algorithm/) 페이지에서 구현된 코드를 보면, winners, losers 등의 추가 변수가 각각 N-1의 크기만큼 추가되었다. 즉, 해당 알고리즘의 공간복잡도는 문제 인스턴스 N의 크기에 비례함을 알 수 있다. 

---

Reference

[1] George T. Heineman, “Learning Algorithms: A Programmer’s Guide to Writing Better Code”, (O’Reilly, 2021)