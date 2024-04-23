---
title: "[문제 풀이]순열과 조합 (Permutation and combination)"
category: "problem solving"
tag: ["python", "파이썬", "problem solving", "문제 풀이", "순열", "조합", "permutation", "combination"]
---

# 개요

순열(permutation)은, n개의 데이터들 중에서 r개를 뽑아 순서대로 배열하는 것을 말한다. 이를 수학적으로는 \\(nPr\\)로 표현한다. 순열의 모든 가능한 경우의 수는 다음의 공식을 통해 구할 수 있다. 

$$
nPr = \frac{n!}{(n-r)!} \ (0< r \le n)
$$

예를 들어, [a, b, c, d] 중에서 3개의 순열을 뽑고자 한다면 나올 수 있는 모든 경우의 수는 위 공식에 따르면 n = 4, r = 3이므로 4! = 24개가 나온다. 해당 문제의 가능한 모든 경우들은 다음과 같이 나온다.

```python
['a', 'b', 'c'], ['a', 'b', 'd'], ['a', 'c', 'b'], ['a', 'c', 'd'], 
['a', 'd', 'b'], ['a', 'd', 'c'], ['b', 'a', 'c'], ['b', 'a', 'd'], 
['b', 'c', 'a'], ['b', 'c', 'd'], ['b', 'd', 'a'], ['b', 'd', 'c'], 
['c', 'a', 'b'], ['c', 'a', 'd'], ['c', 'b', 'a'], ['c', 'b', 'd'], 
['c', 'd', 'a'], ['c', 'd', 'b'], ['d', 'a', 'b'], ['d', 'a', 'c'], 
['d', 'b', 'a'], ['d', 'b', 'c'], ['d', 'c', 'a'], ['d', 'c', 'b']
```

반면, 조합(combination)은 n개의 데이터들 중 r개를 순서없이 뽑는 것을 말한다. 이를 수학적으로는 \\(nCr\\)이라고 표현한다. 조합의 모든 가능한 경우의 수를 구하는 공식은 다음과 같다.

$$
nCr = \frac{nPr}{r!} = \frac{n!}{(n-r)!r!} \ (0 < r \le n)
$$

앞서 들었던 [a, b, c, d]를 이번에는 r=3개의 조합으로 나올 수 있는 모든 가능한 경우를 구하면 다음과 같이 나온다.

```python
[['a', 'b', 'c'], ['a', 'b', 'd'], ['a', 'c', 'd'], ['b', 'c', 'd']]
```

모든 조합의 경우의 수는 앞선 공식과 조합의 예시1을 보면 알 수 있듯, 4개가 나온다. 똑같은 문제에서, 순열에서는 문자 a, b, c에 대해서 [a, b, c], [b, c, a]등 같은 데이터셋에 대해서도 여러가지 경우의 수가 나왔던 반면, 조합에서는 같은 데이터셋에 대해 중복없이 단 하나의 경우의 수 ([a, b, c])만 허락하는 것을 알 수 있다. 

이러한 순열 또는 조합의 모든 가능한 경우를 구하는 함수로, 파이썬의 내장 모듈인 itertools의 permutation, combination 함수가 존재한다. (이에 대한 더 자세한 사항은 [itertools]() 문서 참조 (해당 페이지 준비 중))

여기서는 내장 또는 외부 라이브러리 사용 없이 순열, 조합을 구현해보겠다. 

# 백트래킹을 이용하여 구하기

([Depth First Search, DFS (깊이 우선 탐색) (그리고 backtracking)](/algorithm/algorithm-dfs/) 해당 문서 참조) 

백트래킹 (backtracking)은 DFS 알고리즘에서 특정 조건을 만족시키면 탐색을 중단시키게끔 하거나, 특정 경로에선 해가 없을 경우 더 이상의 해당 경로에 대한 탐색을 중단하고 다른 경로로 탐색하게끔 조건을 부여한 알고리즘을 말한다. 

순열과 조합 문제를 백트래킹 알고리즘으로 푼다는 것은 곧 해당 문제를 그래프 탐색 문제로 환원하는 것과 같다. 예를 들어 [a, b, c, d]에서 3개를 뽑아 모든 가능한 경우의 순열을 뽑는다고 해보자. 그러면 예를 들어 먼저 a를 뽑으면 그 다음으로 뽑을 수 있는 데이터들은 b, c, d이다. 여기서 b를 뽑으면 그 다음으로 뽑기 가능한 데이터들은 c, d이다. 즉, 하나를 뽑으면 그 다음으로 뽑을 수 있는 데이터 후보는 뽑히지 않은 나머지 데이터들임을 알 수 있다. 위 과정을 표현하면 a → b → c와 같이 표현할 수 있을 것이다. 그런데 이는 곧 a → b → c 경로를 통해 a라는 노드에서 시작하여 c라는 노드에 도착하는 그래프 문제와 같아진다. 

순열, 조합 문제를 그래프 문제로 환원하면 해당 그래프는 완전 그래프가 된다. 즉, 모든 노드들이 다른 모든 노드들과 연결되어 있는 그래프가 된다. 

![그림 1. 순열, 조합 문제에서의 [a, b, c, d] 데이터셋을 완전 그래프로 표현한 모습.](/images/2023-08-05/2023-08-05-permutation-and-combination-1.png)

그림 1. 순열, 조합 문제에서의 [a, b, c, d] 데이터셋을 완전 그래프로 표현한 모습.

[a, b, c, d] 데이터셋에서 3개를 뽑는 순열 문제를 구하는 과정에서 먼저 맨 처음에 a를 뽑았다고 가정하였을 때, 그 다음으로 뽑을 수 있는 가능한 데이터들과 그 경로들을 그래프로 묘사하면 다음과 같다. 

![그림 2. [a, b, c, d] 데이터셋에서 3개의 순열을 뽑는 문제에서 맨 처음 a를 뽑았을 경우, 그 후에 가능한 모든 경로를 그래프로 시각화한 모습.](/images/2023-08-05/2023-08-05-permutation-and-combination-2.png)

그림 2. [a, b, c, d] 데이터셋에서 3개의 순열을 뽑는 문제에서 맨 처음 a를 뽑았을 경우, 그 후에 가능한 모든 경로를 그래프로 시각화한 모습.

즉, 순열, 조합 문제는 그래프 문제로 환원할 수 있음을 알 수 있다. 

## 코드 구현

### 스택과 반복문을 이용한 코드

```python
def my_permutation(array: list, r: int):
    all_result = []
    stack = [(i, [ele]) for i, ele in enumerate(array)]
    while stack:
        idx, path = stack.pop()
        if len(path) == r:
            all_result.append(path)
            continue
        for i, element in enumerate(array):
            if i != idx and element not in path:
                stack.append((i, path + [element]))
    all_result.reverse()
    return all_result

def my_combination(array: list, r: int):
    all_result = []
    stack = [(i, [ele]) for i, ele in enumerate(array)]
    while stack:
        idx, path = stack.pop()
        if len(path) == r:
            all_result.append(path)
            continue
        for i, element in enumerate(array):
            if i <= idx:
                continue
            stack.append((i, path + [element]))
    all_result.reverse()
    return all_result

if __name__ == '__main__':
    data = ['a', 'b', 'c', 'd']
    r = 3
    #result = my_permutation(data, r)
    result = my_combination(data, r)
    print(result)
    print(len(result))
```

```python
[['a', 'b', 'c'], ['a', 'b', 'd'], ['a', 'c', 'd'], ['b', 'c', 'd']]
4
```

조합의 경우, 결과를 보면 모든 알파벳들이 오름차순으로 정렬되어 있음을 알 수 있다. 즉, 조합을 코드로 구현할 때 오름차순을 만족하는 부분 배열들만 조합의 답으로 채택하도록 하였다.

### 재귀 호출을 이용한 코드

```python
def my_permutation_recursive(array: list, r: int):
    all_result = []
    N = len(array)
    idx_selected = [False for i in range(N)]

    def recursive(one_result: list):
        if len(one_result) == r:
            all_result.append(one_result.copy())
            #one_result = []
            return
        for i in range(N):
            if idx_selected[i] is False:
                one_result.append(array[i])
                idx_selected[i] = True
                recursive(one_result)
                idx_selected[i] = False
                one_result.pop()
    
    recursive([])
    return all_result

def my_combination_recursive(array: list, r: int):
    all_result = []
    N = len(array)

    def recursive(start_idx: int, one_result: list):
        if len(one_result) == r:
            all_result.append(one_result.copy())
            return
        for i in range(start_idx, N):
            one_result.append(array[i])
            recursive(i+1, one_result)
            one_result.pop()
    
    recursive(0, [])
    return all_result
```

# 내 생각

코딩에서 처음 순열, 조합 문제에 부딪혔을 때부터 “이걸 내가 스스로 구현할 방법은 없을까?”라는 의문이 들었었다. 그러나 그 당시에는 지금처럼 백트래킹이니 dfs니 하는 알고리즘에 대한 지식이 전혀 없을 때라 아무리 고민을 해도 방법이 나오지 않아 결국 포기하고 한동안 다루질 않았다. 그러다 최근 알고리즘 기초 내용을 공부하고 알고리즘 문제를 풀 수 있는 사이트에서 문제를 풀다가 다시 순열, 조합 문제에 부딪혔다. 그래서 이번에도 해당 문제를 푸는 방법에 대해 고민하기 시작했다. 그러다 순열, 조합 문제가 결국 그래프로 환원되어 DFS로 풀 수 있다는 사실을 깨달았다. 그러나 그 때 당시에는 DFS에 탐색 종료 조건을 부여하는 백트래킹의 존재를 몰랐고, (즉 DFS만 알고 있었다) 정확히 어떻게 구현해야할지 아무리 고민해도 나오질 않아 결국 구글 검색을 통해 다른 분들이 구현한 코드를 살펴보았다. 그런데 내가 생각했던 코드와는 조금 달랐다. 나는 해당 문제가 DFS로 풀 수 있다고 해서 DFS 공부할 때 접했던 인접 행렬, 이미 방문한 노드를 표시하는 변수 등을 떠올렸는데 정작 순열, 조합 문제를 푸는 코드에서는 그러한 일부 개념들이 생략되어도 잘만 작동되었던 것이었다. 여기서 조금은 혼란스러웠다. 그리고 구글 검색으로 나온 대부분의 백트래킹을 이용한 코드들을 보면 다 재귀 호출을 이용한 방법밖에 없어서 “그러면 스택을 이용해서 풀려면 어떻게 해야 하지?”에 대한 답이 나오질 않았다. 게다가 스택에는 정확히 어떤 걸 넣어야 하는 건지도 감이 잡히지 않았다. 그래서 처음에는 내가 배운 정석대로 “스택에는 인접한 노드들을 넣어야 하나보다”라는 관점을 가지고 코딩을 하였다. 그런데 그렇게 하다보니 전혀 답이 나오질 않는 것이었다. 게다가 코드도 다른 사람들이 구현한 코드에 비해 더더욱 길어지고 복잡해지기 시작했다. 이 문제는 결국 chat-gpt에게 물어봄으로서 궁금증이 해소되었다. 스택에는 단순히 각 데이터들을 노드로 하여 이를 저장하는 것이 아니라, 현재까지 구한 순열과 그 순열의 원소를 어디까지 찾았는지에 대한 인덱스 정보를 넣는 것임을 깨달았다. 그 후에는 스택을 이용한 백트래킹 알고리즘으로 순열과 조합 문제를 풀 수 있게 되었다. chat-gpt에게 코드를 구현해달라하였고, 그로 인해 나온 코드를 읽기는 하였으나, 내가 직접 머리로 이해하는 게 좋다고 생각하여 그대로 그 코드를 복붙하지 않고 내가 이해한대로 처음부터 끝까지 내 손으로 코딩하여 해당 코드를 구현하였다. 

아예 풀리지 않을 것 같은 순열, 조합 구현 문제가 사실 백트래킹만 알면 그렇게까지 어려운 문제는 아님을 알고 나니 역시 알고리즘 공부를 잘했다는 생각을 하였다. 사실 스스로 어떤 프로그램을 개발할 때 알고리즘을 모르는 상태에서 시작하니 막히는 부분이 많았다. 이제 알고리즘의 기초 정도는 알게되었으니 후에 프로그램을 개발할 때 좀 더 수월하게 개발할 수 있지 않을까, 라는 생각과 동시에 내가 어떤 앱이나 프로그램을 만들고자 한다면 알고리즘은 사실상 필수이고, 그래서 왜 수많은 IT 기업에서 코딩 테스트를 보는지를 체감하게 된 것 같다. 

---

References

[1] [조합과 순열 알고리즘, 파이썬으로 구현하기](https://shoark7.github.io/programming/algorithm/Permutations-and-Combinations)

[2] chat-gpt

[3] [순열과 조합 - 순열이란](https://mathbang.net/545#gsc.tab=0)

[4] [순열과 조합 - 조합이란](https://mathbang.net/547#gsc.tab=0)