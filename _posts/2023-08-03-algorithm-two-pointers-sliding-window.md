---
title: "[알고리즘] 투 포인터와 슬라이딩 윈도우 알고리즘"
category: "Algorithm"
tag: ["algorithm", "알고리즘", "투 포인터", "슬라이딩 윈도우", "two pointers algorithm", "sliding window algorithm"]
---

# 투 포인터 알고리즘 (two pointers algorithm)

투 포인터 알고리즘은 N의 크기를 가지는 1차원 배열에 대해, 어떠한 작업을 하기 위해 두 개의 포인터를 준비하여 1차원 배열 내의 각각 다른 원소들을 가리키고, 이 포인터들을 조작해가면서 원하는 작업을 수행하는 알고리즘이다. 

## 예시

예를 들어, 다음의 배열이 있다고 해보자.

```python
[2, 5, 3, 2, 2]
```

해당 배열에서, 총합이 5가 되는 연속 부분 배열의 개수를 구하고자 한다. 이러한 개수를 세어주는 변수를 num이라 하겠다. 이 문제를 투 포인터 알고리즘으로 해결하고자 한다면 다음의 규칙들을 따르면 된다.

1. 먼저, 시작점 포인터 start와 끝 포인터 end를 준비한다. (해당 변수들은 위 배열의 인덱스를 저장하는 변수이다)
2. 두 포인터 모두 처음에는 배열의 맨 첫 원소를 가리키게 한다. 위 예를 들면, start = end = 0으로 둔다.
    
    ```python
    start  ▼
          [2, 5, 3, 2, 2]
    end    ▲
    ```
    
3. start ~ end 사이의 원소는 2뿐이다. 2는 5가 되지 않는다. 따라서 이 때는 end 포인터를 1 증가시킨다. 
    
    ```python
    start  ▼
          [2, 5, 3, 2, 2]
    end       ▲
    ```
    
    또 한번 start 인덱스와 end 인덱스 (두 인덱스에 해당하는 원소들도 포함) 사이의 숫자들의 합을 구해본다. 2+5 = 7은 5를 만족시키지 않는다. 
    
4. 이미 두 포인터가 가리키는 범위의 부분 배열의 총합이 5를 넘어버렸으므로, 이 경우 start 포인터를 1 증가시킨다. 
    
    ```python
    start     ▼
          [2, 5, 3, 2, 2]
    end       ▲
    ```
    
    이제 두 포인터가 가리키는 범위의 부분 배열은 [5]이다. 해당 부분 배열의 총합이 5이므로 num의 값을 +1 증가시킨다. 
    
5. start = end이므로 end를 1 증가시킨다. 항상 start ≤ end 조건을 만족시켜야 한다. 
    
    ```python
    start     ▼
          [2, 5, 3, 2, 2]
    end          ▲
    ```
    
    한 편, 이미 전 단계에서 총합 5를 만족시키는 부분 배열 [5]를 찾았으며, 현재 부분 배열 [5, 3]은 총합 5를 넘기므로 start를 1 증가시킨다. 
    
6. 
    
    ```python
    start        ▼
          [2, 5, 3, 2, 2]
    end          ▲
    ```
    
    현재 부분 배열 [3] 이고, 총합 5를 만족시키지 못하므로 end를 1 증가시킨다.
    
7. 
    
    ```python
    start        ▼
          [2, 5, 3, 2, 2]
    end             ▲
    ```
    
    현재 연속 부분 배열 [3, 2]의 총합이 5를 만족시키므로 num 변수를 1 증가시킨다. 그 후, start 포인터를 1 증가시킨다. 
    
8. 
    
    ```python
    start           ▼
          [2, 5, 3, 2, 2]
    end             ▲
    ```
    
    현재 연속 부분 배열 = [2] → 총합 5 불만족 → start ≤ end 조건 만족을 위해 end 포인터 1 증가.
    
9. 
    
    ```python
    start           ▼
          [2, 5, 3, 2, 2]
    end                ▲
    ```
    
    현재 연속 부분 배열 = [2, 2] → 총합 5 불만족 → start 1 증가
    
10. 
    
    ```python
    start              ▼
          [2, 5, 3, 2, 2]
    end                ▲
    ```
    
    현재 연속 부분 배열 = [2] → 총합 5 불만족 → start 포인터가 배열의 끝에 도달하였으므로 탐색을 종료한다.
    

위 과정을 정리하면 다음과 같다. N개의 숫자들이 담긴 1차원 배열에 대해 총합이 M이 되는 연속 부분 배열의 개수 C를 구하고자 할 때,

1. 두 포인터 start, end를 start = end = 0 으로 초기화.
2. 연속 부분 배열 subarray[start:end+1]의 총합이 M인지를 확인하고, 맞으면 C를 증가시킨다. sum(subarray) < M이면 end += 1을 하고, 
sum(subarray) ≥ M 이면 
1) start<end이면 start += 1,
2) start == end이면 end += 1을 한다.
3. 항상 start ≤ end 조건을 만족시키면서 진행해야한다. 
4. 위와 같은 과정을 반복하여 C를 구해나간다. 이러한 과정은 start ≤ N일 동안 반복한다. start 인덱스가 N에 도달하면 탐색을 종료한다. 

위 문제를 해결하는 코드는 다음과 같이 구현하였다.

```python
def count_sum_subarray(goal: int, array: list) -> (int):
    """
    투 포인터 알고리즘을 이용하여 N개의 숫자들이 담긴 1차원 배열 array에 대해, 
    총합이 goal이 되는 연속되는 부분 배열의 개수와 그 부분 배열들을 반환하는 함수.
    """
    start, end = 0, 0
    N = len(array)
    count = 0
    subarrays = []
    while start != N:
        sub_a = array[start:end+1]
        get_sum = sum(sub_a)
        if get_sum >= goal:
            if get_sum == goal:
                count += 1
                subarrays.append(sub_a)
            if start == end and end + 1 < N:
                end += 1
            else:
                start += 1
        else:
            # if get_sum < goal
            if end + 1 < N:
                end += 1
            else:
                start += 1
    return count, subarrays

if __name__ == '__main__':
    import random
    goal = 5
    data = [random.randint(1, goal) for i in range(goal)]
    count, subarrays = count_sum_subarray(goal, data)
    print(f"Original array: {data}")
    print(f"Goal: {goal}")
    print(f"Sub arrays with total of goal: {subarrays}")
    print(f"The number of sub arrays with total of goal: {count}")
```

## 위 예시에서의 시간복잡도

두 포인터는 각자 따로 움직인다. 두 포인터 모두 1씩 증가하는 방향으로만 진행되며, 각 포인터가 모두 N번까지 도달해야 탐색이 끝난다. 따라서 두 포인터의 각각의 시간복잡도가 O(N)이고, 이 둘을 합쳐도 결국 시간복잡도는 O(N)이 된다. 따라서 결론은 O(N)이다. 

> 문제에 따라, 또는 투 포인터 알고리즘을 어떻게 사용하느냐에 따라 시간복잡도가 그 때 그 때 달라질 수 있다. 알고리즘 관련 문제를 풀 때, 투 포인터 알고리즘을 반복문 2개를 사용하여 푼 적이 있는데, 이 경우 시간복잡도는 \\(O(N^2)\\)가 된다. 

# 슬라이딩 윈도우 알고리즘

슬라이딩 윈도우 알고리즘은 투 포인터 알고리즘과 거의 똑같으나, 차이점은 투 포인터 알고리즘의 경우, 두 포인터 간의 간격이 유동적인데에 비해 슬라이딩 윈도우 알고리즘에서는 두 포인터 간의 간격을 동일하게 유지한다는 것이다. 이러한 형태가 마치 미닫이 창문을 옆으로 슬라이딩 하는 모습과 같다고 해서 지어진 이름이다. 

두 포인터의 고정된 폭을 유지한다는 점만 빼면 투 포인터 알고리즘과 같으므로 위에서 소개한 예시와 비슷한 문제를 풀 때의 시간복잡도도 O(N)이다. 

---

Reference

[1]

[[Algorithm] 투포인터(Two Pointer) 알고리즘](https://butter-shower.tistory.com/226)

[2]

[[이것이 코딩 테스트다 with Python] 39강 투 포인터](https://freedeveloper.tistory.com/393)

[3]

[https://github.com/WooVictory/Ready-For-Tech-Interview/blob/master/Algorithm/투포인터 알고리즘.md](https://github.com/WooVictory/Ready-For-Tech-Interview/blob/master/Algorithm/%ED%88%AC%ED%8F%AC%EC%9D%B8%ED%84%B0%20%EC%95%8C%EA%B3%A0%EB%A6%AC%EC%A6%98.md)

[4]

[슬라이딩 윈도우 알고리즘](https://soeasyalgo.tistory.com/49)