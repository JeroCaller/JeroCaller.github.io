---
title: "[알고리즘][그래프] Weighted graph - Bellman-ford algorithm"
category: "Algorithm"
tag: ["algorithm", "알고리즘", "그래프", "graph", "weighted graph", "bellman-ford"]
---

- 이 문서에서도 특정 노드 u에서 다른 노드 v로 향할 때 드는 비용을 “거리값”이라고 표현하겠다. 즉, 가중치 그래프를 지도처럼 다루겠다.

# Bellman-Ford 알고리즘

Bellman-Ford 알고리즘은 그래프에 음의 가중치가 있어도 source 노드로부터 특정 노드까지의 최단 경로를 계산할 수 있는 알고리즘이다. Dijkstra 알고리즘에서는 우선순위 큐라는 자료구조를 사용하는 반면, 해당 알고리즘은 특별한 자료구조를 사용하지 않는다.

음의 가중치가 존재하지 않은 그래프에 대해서, 다익스트라 알고리즘은 우선순위 큐를 이용하고, source 노드부터 시작하여 해당 노드에서 가장 가까운 간선부터 먼 간선 순서대로 탐색하여 각 노드에 대한 최소 비용을 정확히 계산할 수 있었다. 그런데 벨만 포드 알고리즘은 그래프 내 모든 간선들의 접근 순서 자체가 다르다. 벨만 포드 알고리즘은 그저 그래프 내 모든 간선들을 임의의 순서대로 접근한다. 다음의 그림을 보자.

![그림 1. Bellman-Ford 알고리즘 이용 시 모든 노드들의 최소 비용을 계산하는 최악의 경우. Reference [3] 참고함.](https://upload.wikimedia.org/wikipedia/commons/thumb/8/85/Bellman-Ford_worst-case_example.svg/330px-Bellman-Ford_worst-case_example.svg.png)

그림 1. Bellman-Ford 알고리즘 이용 시 모든 노드들의 최소 비용을 계산하는 최악의 경우. Reference [3] 참고함.

위 그림 1에서 source 노드를 A라고 가정하겠다. 다익스트라 알고리즘을 적용하면 무조건 source 노드와 가장 가까운 간선부터 탐색한다. 즉, 그래프 내 모든 간선들을 무조건 왼쪽에서 오른쪽 순으로 탐색할 것이다. 그러나 앞서 말했듯 Bellman-Ford 알고리즘은 무작위 순으로 간선들을 선택하고 탐색한다. 이 때 최악의 경우는 해당 알고리즘이 매번 실행할 때마다 맨 오른쪽의 간선부터 왼쪽 순으로 탐색하는 것이다. 위 그림 1의 맨 윗 줄을 보자. 아직 탐색이 시작되기 전의 모습이어서 source 노드를 제외한 나머지 노드들의 최소 비용값이 무한대로 설정되어 있다. 이 상황에서 맨 오른쪽 간선부터 탐색한다고 가정해보자. (D, E) 간선부터 살펴볼 것이다. 그런데 이 경우 D의 최소 비용 + (D, E) 간선의 가중치 = ∞ + 1 = ∞이고 이는 곧 노드 E의 최소 비용인  ∞와 같게 된다. 그래서 최소 비용의 갱신이 일어나지 않는다. 이는 (B, C) 간선에서까지 마찬가지이다. (A, B)까지 와서야 B 노드의 최소 비용값의 갱신이 이뤄진다. 아직 나머지 노드들의 최소 비용이 정확히 계산되지 않았으니 또 한 번 앞선 과정을 반복해야 한다. 매 번마다 맨 오른쪽 간선부터 왼쪽 순으로 탐색한다면 결국, 모든 노드들의 최소 비용을 계산하기 위해서는 N-1번의 반복 실행이 필요함을 알 수 있다. 

## Negative cycle과 그 탐지

그런데 이 알고리즘을 적용할 때에도 주의할 점이 있다. 바로 그래프 내의 negative cycle의 존재이다. negative cycle은 cycle의 일종으로, cycle을 한 번 순회하면 거리값이 음수가 되게 하는 cycle을 일컫는다. 

![그림 2. negative cycle의 예. Reference [1] 참고함.](/images/2023-07-31/2023-07-31-algorithm-bellman-ford-algorithm-1.png)

그림 2. negative cycle의 예. Reference [1] 참고함.

cycle을 이루는 간선의 가중치가 무조건 음수라고 해서 negative cycle이 되는 것은 아니다. 위 그림 1을 보면 알겠지만, 예를 들어 노드 a에서 시작하여 cycle을 돌아서 b 노드에 도착하도록 걷는다고 가정해보겠다. 그러면 왼쪽 그림의 경우, cycle을 따라 거리를 계산해나가도 1-3+5-1=2로 양수이기에 해당 cycle은 negative cycle이 되지 않는다. 반면 오른쪽 그림의 경우, cycle을 따라 걸으면 결국 거리값이 음수가 된다. 이러한 cycle이 바로 negative cycle이 된다. 

negative cycle을 따라 돌면 돌수록 그 거리값이 점점 더 커져 결국에는 a에서 b 노드로 가는 최소 비용이 -∞가 된다. 이는 적절한 최소 비용을 구했다고 보기 어렵다(이 경우 최소 비용의 경로는 없다고 봐야 한다). 따라서 Bellman-Ford 알고리즘을 코드로 구현할 때 negative cycle이 포함된 가중치 그래프라면 최소 비용이 -∞가 나올 것이기에 이 경우 negative cycle을 포착하자마자 알고리즘 실행을 멈추는 형식으로 작성한다.

그렇다면 negative cycle을 어떻게 감지할 수 있을까? 앞서 그림 1을 예로 들며 설명헀던 곳으로 다시 돌아가보자. 그래프 내 모든 노드들의 최소 비용값을 결정하기 위해서는 N-1번의 반복 실행이 필요했다. 만약 이 상태에서 또 한 번의 반복 실행을 한다면? 그림 1의 경우 negative cycle이 존재하지 않기에 그 어떤 노드의 최소 비용값도 갱신되지 않는다. 그러나 만약 negative cycle이 존재했다면 해당 cycle의 특성상 더 낮은 최소 비용값으로 갱신되었을 것이다. 바로 이 “N번째 실행 후에도 단 하나의 노드라도 그 최소 비용이 갱신되었느냐”를 감지하면 되는 것이다. 

따라서 Bellman-Ford 알고리즘 실행 도중 negative cycle을 탐지하려면 해당 알고리즘을 그래프 내 전체 노드의 개수인 N번 만큼 실행하게 한다. 그리고 N번째 실행 시에도 최소 비용값이 갱신된다면 해당 그래프에는 negative cycle이 존재한다고 판단할 수 있겠다. 

## Negative cycle 탐지 코드도 포함된 Bellman-Ford 알고리즘 코드 구현

다음의 코드는 negative cycle을 탐지하면 에러를 일으키도록 고안된 Bellman-Ford 알고리즘 코드이다. 해당 코드는 이전 문서 [Weighted graph - Dijkstra algorithm](/algorithm/algorithm-dijkstra/) 의 예제 1-2의 클래스 내부에 인스턴스 메서드로써 작성하였다.

```python
def bellmanFordAlgorithm(self) -> (None):
        """
        Directed weighted graph에 대해서, 전체 가중치 중 하나라도 음수가 있는 
        경우 source 노드에서 특정 노드까지의 최단 거리 및 경로를 탐색하고 이를 
        반환하는 함수. 
        """
        dist_to: dict[Node, Distance] = {v:INF for v in self.graph.nodes()}    #1
        dist_to[self.src] = 0
        edge_to: dict[Node, Edge] = {}    #2

        def relax(e):
            u, v, weight = e[0], e[1], e[2]['weight']
            if dist_to[u] + weight < dist_to[v]:    #5
                dist_to[v] = dist_to[u] + weight    #6
                edge_to[v] = e    #7
                return True    #8  # 노드 v로 향하는 최단 경로를 찾았다는 뜻.
            return False
        
        # 모든 edge들에 대해 전체 노드 N개에 대해 작업 수행.
        for i in range(self.graph.number_of_nodes()):    #3
            for e in self.graph.edges(data=True):    #4
                if relax(e):
                    # 주어진 전체 노드 개수 N에 대해, 가장 긴 경로를 구성하는 
                    # edge의 개수는 적어도 N-1을 넘지는 않는다. 
                    # 마지막 노드에 대해서도 src 노드로부터 특정 노드 v로
                    # 향하는 최단 경로를 새로 발견한다면 (즉, 거리값이 줄었다면)
                    # 이는 negative cycle에 갇힘을 의미.
                    if i == self.graph.number_of_nodes() - 1:    #9
                        raise RuntimeError("Negative cycle exists in graph.")
        self.dist_to = dist_to.copy()
        self.edge_to = edge_to.copy()
```

그림 1의 그래프를 상대로 적용했을 때, ‘a’ 노드부터 ‘e’노드로 가는 최단 경로와 그 거리를 구하면 다음과 같이 나온다.

```python
['a', 'b', 'c', 'd', 'e']
0
```

만약 해당 그래프에 (’e’, ‘a’, -1)의 간선을 추가하면 해당 알고리즘은 negative cycle을 찾게 되어 에러를 발생시킨다. 

## 시간복잡도

Bellman-Ford 알고리즘의 시간복잡도는 사실 예제 1-1의 코드에서 for문이 두 번이나 쓴 것에서도 유추할 수 있다. 해당 알고리즘 내에 Negative cycle 탐지 코드를 넣건 넣지 않건 최소한 (N-1)번의 실행이 필요하다. 그리고 각각의 실행에서 모든 E개의 간선들을 탐색해야 한다. 따라서 해당 알고리즘의 시간복잡도는 이 둘을 곱한 O(NE)이다. 

> 위 예제 코드의 풀버전은 다음의 링크에서 확인 가능. 
> [https://github.com/JeroCaller/ds-and-algo-in-python/blob/main/algorithms/graph/weighted_graph.py](https://github.com/JeroCaller/ds-and-algo-in-python/blob/main/algorithms/graph/weighted_graph.py)

---

Reference

[1] George T. Heineman, “Learning Algorithms: A Programmer’s Guide to Writing Better Code”, (O’Reilly, 2021)

[2] [벨만-포드 알고리즘(Bellman-Ford Algorithm)](https://sorjfkrh5078.tistory.com/30)

[3] [Bellman–Ford algorithm](https://en.wikipedia.org/wiki/Bellman–Ford_algorithm)