---
title: "[알고리즘][그래프] Depth first search (DFS, 깊이 우선 탐색법)"
category: "Algorithm"
tag: ["algorithm", "알고리즘", "그래프", "graph", "DFS", "깊이 우선 탐색", "backtracking"]
---

# 미로에서 출구 찾기

미로의 각 빈 방들을 노드로 표현하고, 빈 방에서 다른 빈 방으로 이동할 수 있으면 두 노드 사이를 엣지로 연결하고, 두 빈 방 사이에 벽이 존재하여 이동할 수 없다면 두 노드 사이를 엣지로 연결하지 않는 방식을 통해 미로를 무향 그래프로 모델링할 수 있겠다. 다음은 행이 5개고 열이 3개인 직사각형 형태의 미로를 그래프로 나타낸 그림이다. 

![그림 1. 미로를 무향 그래프(undirected graph)로 표현한 그림.](/images/2023-07-24/2023-07-24-algorithm-dfs-1.png)

그림 1. 미로를 무향 그래프(undirected graph)로 표현한 그림.

각 노드들은 2차원 직교좌표계를 이용하여 (X, Y)로 그 위치를 표시하였다. 위 그림에서는 맨 왼쪽 아래를 (0, 0)으로 기준 잡았으며, 오른쪽으로 양의 X축, 위쪽으로 양의 Y축으로 잡고 노드들의 위치를 표시하였다. 

위 그림 1에서는 미로의 입구를 (0, 2) 위치의 노드로 정하였고, 출구는 (2, 2)에 위치한 노드로 정하였다. 

“미로를 푼다(solve a maze)”라는 말은, 미로의 입구에서 출구로 향하는 경로를 찾는 것을 의미한다.

# 깊이 우선 탐색법으로 미로 문제 풀기

처음은 미로 입구인 (0, 2) source 노드에서 시작할 것이다. 현재 노드에서 인접한 노드들을 조사한다. 그림 1의 경우, (0, 2)의 인접한 노드는 (0, 1), (0, 3), (1, 2)이다.  이들 모두 잠재적으로 언젠가는 방문할 곳들이다. 이들을 기억한 후, 이 세 개의 인접한 노드들 중 하나를 임의로 골라 그곳으로 발을 내딛는다. 그 후, 해당 노드를 방문했다고 표시한다. (사실 처음에 source node도 방문 표시를 해놓는다.) 이는 후에 방문한 곳을 다시 재방문하는 일을 방지하기 위함이다. A라는 길로 가면 막다른 길임을 앞서 알아냈는데 굳이 그 곳으로 다시 갈 이유가 없는 것과 마찬가지이다. 여기서는 source node에서 인접한 노드들 중 (0, 3)으로 갔다고 가정하겠다. 해당 노드에도 방문 표시를 한 후, 해당 노드의 인접한 노드들 중 아직 방문하지 않은 노드들을 조사한다. 이 경우, (1, 3)과 (0, 4)가 있겠다. 이들을 모두 기억한 후, 이번에는 (1, 3)으로 가보겠다. 그런데 해당 노드는 인접한 노드들 중 아직 방문하지 않은 노드가 전혀 존재하지 않는다. 즉, 막다른 길(dead end)에 도달했다. 이 경우에는, 앞서 기록했던 미방문 노드들이 있던 곳으로 돌아가 그 곳을 탐색하면 된다. 이 경우, (1, 3)과 가까이 있었던 (0, 4)로 갈 수 있겠다. 이러한 과정을 미로 출구를 발견할 때까지 반복한다. 

![그림 2. 노드 (1, 3)이 막다른 길이라 더 이상 진전하지 못할 경우, 가까운 곳에 미방문한 노드까지 되돌아가 해당 노드를 탐색하면 된다. 위 그림에서는 (1, 3)에서 (0, 3)으로 되돌아가 (0, 4) 노드를 탐색하면 된다.](/images/2023-07-24/2023-07-24-algorithm-dfs-2.PNG)

그림 2. 노드 (1, 3)이 막다른 길이라 더 이상 진전하지 못할 경우, 가까운 곳에 미방문한 노드까지 되돌아가 해당 노드를 탐색하면 된다. 위 그림에서는 (1, 3)에서 (0, 3)으로 되돌아가 (0, 4) 노드를 탐색하면 된다.

지금까지의 과정을 정리하면 다음과 같다.

- 이미 방문한 노드에는 방문했다는 표시를 한다.
- 현재 노드에서 인접한 노드들 중 아직 방문 표시가 안된 노드들을 기록해둔다. 기록을 하는 이유는 나중에 다른 곳을 갔다가 막다른 길에 부딛힐 경우, 다시 되돌아와 아직 탐색하지 못한 다른 노드로 향하기 위함이다. 기록 후에는 미방문한 인접한 노드들 중 임의로 하나를 골라 그 노드로 발을 내딛는다. 발을 내딛은 노드도 방문 표시를 한다.
- 막다른 길에 도달한 경우, 마지막으로 기록해둔 미방문 노드로 되돌아가 해당 노드로 탐색한다.
- 이러한 과정을 모든 도달 가능한 노드들이 모두 방문 표시가 될 때까지 진행한다. (즉, 미로의 모든 곳까지 다 탐색할 때까지)

위와 과정을 거쳐 미로 탐색을 마치면 그 후에 미로 입구에서 출구까지 향하는 경로를 구성해야 한다. 이를 위해서는 딕셔너리를 담는 변수를 하나 정의한다. 해당 변수는 현재 노드를 key로 하고, 해당 노드로 도달할 수 있는 바로 이전 노드를 value로 하는 변수이다. (만약 어떤 노드가 도달 불가능하여 그 노드로 향할 수 있는 이전 노드들이 없다면 해당 key의 value를 None으로 설정하면 되겠다) 

이 외에도, 특정 노드를 방문했는지 여부를 기록하기 위해 노드를 key로, 방문 여부를 bool 자료형으로 표시하는 변수, 그리고 현재 미방문한 인접한 노드들을 기록하는 변수도 필요하다. 후자의 경우, 현재 노드가 막다른 길일 경우 현재 노드에서 가장 가까운 미방문한 인접 노드를 찾도록 할 수 있어야 한다. 즉, LIFO(후입선출) 구조를 만족해야 한다. 이를 위해 스택 자료 구조가 사용된다. 

이러한 전략이 깊이 우선 탐색이란 이름을 가진 이유는 모든 가능한 길 중 하나를 먼저 골라 그 길만 우선적으로 깊게 탐색하기 때문이다. (”한 우물만 판다”)

깊이 우선 탐색을 코드로 구현하기에 앞서, 미로를 구현하기 위해 우선 다음의 코드들을 입력하였다.

```python
import networkx as nx
import matplotlib.pyplot as plt
from my_stack import DynamicStack

# type aliases
# 함수 또는 메서드의 매개변수 또는 반환 값의 자료형이 무엇이고 
# 무엇을 의미하는지 알기 쉽게 하기 위함.
# 노드의 (X, Y) 위치
PosX = int
PosY = int
Node = tuple[PosX, PosY]
PriorNode = Node
CurrentNode = Node
PositionInfo = dict[Node, tuple[PosX, PosY]]
NodeTrace = dict[CurrentNode, PriorNode]

def create_retangular_maze(
        g: nx.Graph, 
        width: int = 3,
        height: int = 5,
        walls: list[tuple[tuple[PosX, PosY], tuple[PosX, PosY]]] = [None]
    ) -> (tuple[nx.Graph, PositionInfo]):
    """
    직사각형의 미로를 구현하는 함수. 
    지나갈 수 있는 빈 공간을 노드, 빈 공간에서 빈 공간으로 가는 길을 
    엣지로 표현. 
    """
    def create_nodes(
            g: nx.Graph, 
            width, 
            height
        ) -> (tuple[nx.Graph, PositionInfo]):
        pos = {}
        for j in range(height):
            for i in range(width):
                g.add_node((i, j))
                pos[(i, j)] = (i, j)
        return g, pos
    
    def create_edges(
            g: nx.Graph, 
            width, 
            height, 
            walls: list[tuple[tuple[PosX, PosY], tuple[PosX, PosY]]]
        ) -> (nx.Graph):
        for j in range(height):
            for i in range(width):
                node = (i, j)
                node_h = (i, j+1)
                node_v = (i+1, j)
                if (node, node_h) not in walls and (node_h, node) not in walls:
                    if j != height - 1:
                        g.add_edge(node, node_h)
                if (node, node_v) not in walls and (node_v, node) not in walls:
                    if i != width - 1:
                        g.add_edge(node, node_v)
        return g

    g, pos = create_nodes(g, width, height)
    g = create_edges(g, width, height, walls)
    return g, pos

def draw_maze(
        g: nx.Graph, 
        positions: PositionInfo, 
    ) -> (None):
    """
    미로 시각화 함수. 
    """
    nx.draw_networkx(g, pos=positions)
    plt.show()
```

그 다음, 깊이 우선 탐색으로 미로 문제를 풀기 위한 클래스를 다음과 같이 정의하였다.

```python
class FindPathInMazeDFS():
    """
    미로를 탈출하는 경로를 찾는 클래스. 
    깊이 우선 탐색법 (Depth First Search)으로 경로를 찾는다. 
    """
    def __init__(
            self,
            maze_g: nx.Graph,
            maze_nodes_pos: PositionInfo,
            source_node: Node = None,
            target_node: Node = None
        ):
        """
        매개변수
        -------
        maze_g: draw_retangular_maze() 함수를 통해 이미 미로를 구성한 \
        nx.Graph 객체. \n
        source_node: 미로의 출발점 노드. \n
        target_node: 미로의 탈출구 노드. 
        """
        self.G: nx.Graph = maze_g
        self.maze_pos: PositionInfo = maze_nodes_pos
        self.src: Node = source_node
        self._target: Node = target_node

        self.node_trace = self._search()
        #self.node_trace = self._recurSearch()
        self.path = self._constructPathToExit(self.node_trace)

    @property
    def source(self) -> Node: return self.src

    @source.setter
    def source(self, new_src: Node) -> None: self.src = new_src

    @property
    def target(self) -> Node: return self._target

    @target.setter
    def target(self, new_target: Node) -> None: self._target = new_target

    def getPath(self) -> (list[Node]): return self.path
```

앞서 구현한 코드를 이용하여 실제 미로를 구성하고 미로 탐색 알고리즘을 테스트하기 위해 다음의 코드를 작성하였다.

```python
if __name__ == '__main__':
    graph = nx.Graph()
    walls = [
        ((0, 1), (1, 1)), ((1, 1), (1, 2)), ((1, 2), (1, 3)),
        ((1, 3), (1, 4)), ((1, 3), (2, 3)), ((2, 0), (2, 1)),
    ]
    g, pos = create_retangular_maze(graph, walls=walls)
    draw_maze(g, pos)

    entrance_node = (0, 2)
    exit_node = (2, 2)
```

이제 본격적으로 깊이 우선 탐색법을 코드로 구현하겠다. 다음의 코드를 앞선 예제 1-2의 클래스에 이어서 작성하면 된다.

```python
def _search(self) -> (NodeTrace):
        """
        깊이 우선 탐색법(Depth-First Search)을 통해 미로의 입구부터 
        출구까지의 경로를 탐색. 

        탐색 방법
        ---
        1. 소스 노드(미로 입구)에 플레이어가 발을 디뎠다고 가정하고 해당 노드를 
        이미 방문한 노드로 표시한다. 
        2. 현재 노드에서 인접하고 접근 가능한 (즉, 벽에 막히지 않은 = 엣지가 존재하는) 
        노드들 중 아직 방문하지 않은 노드들 중 하나를 골라 그 곳으로 내딛어 탐색한다. 
        3. 만약 막다른 길(dead end)에 도착하면, 앞서 아직 방문하지 않은 노드들이 
        있는 곳으로 돌아가 아직 방문하지 않은 노드들을 차례대로 다시 방문하고, 
        앞선 과정 2번을 반복한다. 
        4. 이 과정을 모든 접근 가능한(reachable) 노드들에 모두 방문했을 때까지 반복한다. 
        """
        # visited: 이미 방문한 노드들을 기록하는 딕셔너리 변수. 
        # 이미 방문한 노드의 value를 True로 설정한다. 
        # 아직 방문하지 않은 노드들은 해당 변수에 기록되지 않은 상태일 것이다. 
        visited: dict[Node, bool] = {}

        # cp_nodes: current-prior nodes의 줄임말. 
        # 현재 노드와 그 현재 노드에 닿기 위해 접근했던 바로 이전 노드를
        # 딕셔너리 형태로 기록한다. 
        cp_nodes: NodeTrace = {}

        stack: DynamicStack[Node] = DynamicStack()
        visited[self.src] = True
        stack.push(self.src)

        while not stack.is_empty():
            node = stack.pop()
            for adjacent_node in self.G[node]:
                if adjacent_node not in visited:
                    cp_nodes[adjacent_node] = node
                    visited[adjacent_node] = True
                    stack.push(adjacent_node)
        return cp_nodes
```

위 예제의 메서드의 반환값은 앞서 말했던 현재 노드와 이전 노드를 기록하는 딕셔너리이다. (후에 언급하겠지만, 경로를 구성하기 위해 이러한 방식으로 노드들을 기록하는 자료형을 인접 리스트(adjacency list)라 한다) 

예제 1-4에서는 모든 미로를 방문하도록 설정하였는데, 효율을 위해서라면 target node에 발을 내딛는 즉시 while문을 탈출하도록 코드를 변경시킬 수도 있을 것이다. (위 코드는 아직 target node가 무엇인지 주어지지 않았다고 가정하고, 인접 리스트를 구한 이후에야 target node가 주어진다는 상황에서 쓸 수 있을 것이다)

> 이와 같이, DFS로 탐색 도중 해를 이미 찾아 더 이상의 탐색이 불필요해지거나 특정 경로를 탐색 도중 해당 경로가 해가 될 것 같지 않아 해당 경로를 더 깊게 탐색하지 않고 중간에 중단하고 새 경로를 탐색하게끔 하는 것을 백트래킹(backtracking)이라 한다. 그리고 이렇게 조건에 따라 탐색을 도중에 중단하도록 하는 것을 가지치기(pruning)라고 한다. 앞서 미로의 예를 들면, target 노드를 찾는 순간 바로 탐색을 종료하게끔 코드를 수정한다면 해당 알고리즘은 백트래킹이 되는 것이다. 반대로 DFS는 완전 탐색으로, 모든 가능한 경로를 다 찾는다. 따라서 문제에 따라서 모든 경로를 다 찾을 필요가 없을 때 DFS를 사용하면 메모리 및 시간 낭비가 될 것이다. 따라서 이 경우엔 탐색 종료 조건 또는 조건을 만족하지 않는 경로는 미리 빠져나와 다른 경로를 찾게끔 하는 조건을 더해주어 백트래킹 알고리즘으로 해결하면 해당 문제점을 없앨 수 있을 것이다.
> 

위 메서드에서 얻은 값을 시각화하여 살펴보기 위해 해당 클래스에 이어서 다음의 코드를 작성한다.

```python
def drawAllPath(self) -> None:
    """
    DFS_Search 메서드를 통해 얻은 cp_nodes를 통해 
    특정 노드와 그 노드로 향하는 이전 노드간의 관계를 모두 
    direct graph로 나타냄.
    """
    dg = nx.DiGraph()
    positions: PositionInfo = {}
    for cnode, pnode in self.node_trace.items():
        dg.add_nodes_from([cnode, pnode])
        dg.add_edge(cnode, pnode)
        positions[cnode] = cnode
        positions[pnode] = pnode
    nx.draw_networkx(dg, pos=positions)
    plt.show()
```

앞선 예제 1-3 바로 위에 다음의 코드를 작성한다.

```python
def test_dfs(g: nx.Graph, pos: PositionInfo, src: Node, target: Node):
    solution_dfs = FindPathInMazeDFS(g, pos, src, target)
    solution_dfs.drawAllPath()
    #solution_dfs.drawAnswer()
    print(solution_dfs.getPath())
```

예제 1-3의 코드를 다음과 같이 변경한다.

```python
if __name__ == '__main__':
    graph = nx.Graph()
    walls = [
        ((0, 1), (1, 1)), ((1, 1), (1, 2)), ((1, 2), (1, 3)),
        ((1, 3), (1, 4)), ((1, 3), (2, 3)), ((2, 0), (2, 1)),
    ]
    g, pos = create_retangular_maze(graph, walls=walls)
    draw_maze(g, pos)  # 초기 미로를 시각화하여 보여줄 것이다.

    entrance_node = (0, 2)
    exit_node = (2, 2)

    test_dfs(g, pos, entrance_node, exit_node)  # 추가된 코드.
```

이제 위 모듈을 실행해보면 총 두 장의 그래프가 차례대로 뜰 것인데, 첫 번째 사진은 초기 미로 (그림1)이고, 두 번째 그래프가 예제 1-4에서 얻은 인접리스트를 시각화한 그래프이다.

![그림 3. 인접리스트를 방향 그래프로 나타낸 그림. 노드 근처를 자세히 보면 엣지가 그냥 직선이 아니라 화살표임을 알 수 있다. ](/images/2023-07-24/2023-07-24-algorithm-dfs-3.png)

그림 3. 인접리스트를 방향 그래프로 나타낸 그림. 노드 근처를 자세히 보면 엣지가 그냥 직선이 아니라 화살표임을 알 수 있다. 

> 인접 리스트는 노드 간의 연결 관계를 기록하는 하나의 형태이다. 그런데 예제 1-4의 cp_nodes를 시각화한 결과인 그림 3은 그림 1과는 조금 다르다. 예로 원래는 (0, 3) 노드와 (0, 4)노드는 연결되어있어야 하나 그림 3에서는 엣지로 연결되어있지 않다. 그럼에도, cp_nodes 변수도 특정 노드와 인접한 노드를 기록하는 변수기에 편의상 여기서는 해당 변수도 인접 리스트라고 하겠다.
> 

다음은 예제 1-4에서 스택에서 일어나는 일들을 나타낸 예시이다.

```
맨 왼쪽 끝 노드가 가장 먼저 꺼내지는(dequeue) 노드임.
stack

(0, 2)
(1, 2), (0, 3), (0, 1)
(2, 2), (0, 3), (0, 1)
(2, 1), (2, 3), (0, 3), (0, 1)
(1, 1), (2, 3), (0, 3), (0, 1)
(1, 0), (2, 3), (0, 3), (0, 1)
(0, 0), (2, 0), (2, 3), (0, 3), (0, 1)
(2, 3), (0, 3), (0, 1)
(2, 4), (0, 3), (0, 1)
(1, 4), (0, 3), (0, 1)
(0, 4), (0, 3), (0, 1)
(0, 3), (0, 1)
(1, 3), (0, 1)
(0, 1)
None
```

미로의 크기가 유한하고, 이미 방문한 노드를 방문했다고 표시하고 기록하는 visited 변수가 존재하는 한, 예제 1-4의 스택은 반드시 비게 되어있고, 따라서 while 문을 탈출할 수 있다. 미로의 크기가 무한이거나, 방문 노드를 기록하는 변수가 존재하지 않았더라면 무한 루프에 갇혔을 것이다. 

이제 인접리스트를 토대로 미로 입구에서 출구로 향하는 경로를 구성해보자. 다음의 코드를 예제 1-2의 FindPathInMazeDFS 클래스 내부에 이어서 작성하자.

```python
def _constructPathToExit(self, cp_nodes: NodeTrace) -> list[Node]:
    """
    DFS_Search 메서드를 통해 찾은 모든 경로 중, 
    미로 입구(self.src)에서 미로 출구(self.target)로 이어지는 
    경로를 구성한다. 
    """
    if self.src is None:
       print("소스 노드(출발점)를 먼저 설정해주세요.")
       print("source 속성을 통해 설정 가능.")
       return
    if self._target is None:
       print("타겟 노드(탈출구)를 먼저 설정해주세요.")
       print("target 속성을 통해 설정 가능.")
       return
    if self._target not in cp_nodes:
       print("해당 탈출구에 도달할 수 없습니다.")
       return
        
    path: list[Node] = []
    node = self._target
    while node != self.src:
        path.append(node)
        node = cp_nodes[node]
    path.append(self.src)
    path.reverse()
    return path

def drawAnswer(self) -> None:
    """
    constructPathToExit 메서드를 통해 얻은 
    입구부터 출구까지의 경로를 시각화.
    """
    dg = nx.DiGraph()
    dg.add_node(self.path[0])
    positions = {self.path[0]: self.path[0]}
    for i in range(1, len(self.path)):
        dg.add_node(self.path[i])
        dg.add_edge(self.path[i-1], self.path[i])
        positions[self.path[i]] = self.path[i]
    nx.draw_networkx(dg, pos=positions)
    plt.show()
```

예제 1-6에서 주석 처리된 부분을 주석 해제하고 실행하면 다음의 그래프를 얻는다.

![그림 4. 미로의 입구에서 출구로 향하는 경로를 DFS로 탐색하여 알아낸 결과.](/images/2023-07-24/2023-07-24-algorithm-dfs-4.png)

그림 4. 미로의 입구에서 출구로 향하는 경로를 DFS로 탐색하여 알아낸 결과.

```python
[(0, 2), (1, 2), (2, 2)]
```

DFS는 스택 뿐만 아니라 재귀 호출로도 구현할 수 있다. 다음은 스택 없이 재귀 호출을 이용하여 DFS를 코드로 구현한 예제이다.

```python
def _recurSearch(self) -> NodeTrace:
        """
        재귀 호출을 이용한 DFS 검색법. 
        """
        def recurDFS(
                node: Node, 
                visited: dict[Node, bool],
                node_trace: NodeTrace
            ) -> NodeTrace:
            visited[node] = True
            for adjacent_node in self.G[node]:
                if adjacent_node not in visited:
                    node_trace[adjacent_node] = node
                    recurDFS(adjacent_node, visited, node_trace)
            return node_trace
        
        visited: dict[Node, bool] = {}
        cp_nodes: NodeTrace = {}

        cp_nodes = recurDFS(self.src, visited, cp_nodes)
        return cp_nodes
```

재귀 호출을 이용하여 구현한 DFS를 이용했을 때 다음의 결과를 얻었다.

![그림 5. 재귀 호출을 사용한 DFS를 이용하여 얻은 인접리스트를 방향 그래프로 시각화한 그림.](/images/2023-07-24/2023-07-24-algorithm-dfs-5.png)

그림 5. 재귀 호출을 사용한 DFS를 이용하여 얻은 인접리스트를 방향 그래프로 시각화한 그림.

![그림 6. 재귀 호출을 이용한 DFS를 이용하여 미로 입구부터 출구까지의 경로를 시각화한 그림.](/images/2023-07-24/2023-07-24-algorithm-dfs-6.png)

그림 6. 재귀 호출을 이용한 DFS를 이용하여 미로 입구부터 출구까지의 경로를 시각화한 그림.

```python
[(0, 2), (0, 1), (0, 0), (1, 0), (1, 1), (2, 1), (2, 2)]
```

DFS는 항상 최단 경로를 발견할 것이란 보장을 하지 않는다. 다음은 이를 보여주는 그림이다.

![그림 7. DFS 알고리즘을 이용하여 미로 출구로 향하는 경로를 표시한 그림.](/images/2023-07-24/2023-07-24-algorithm-dfs-7.png)

그림 7. DFS 알고리즘을 이용하여 미로 출구로 향하는 경로를 표시한 그림.

위 그림의 미로에서 최단 경로는 다음과 같다.

![그림 8. 미로 출구로 향하는 경로를 표시한 그림. 해당 그림에는 BFS 탐색법이 사용되었다.](/images/2023-07-24/2023-07-24-algorithm-dfs-8.png)

그림 8. 미로 출구로 향하는 경로를 표시한 그림. 해당 그림에는 BFS 탐색법이 사용되었다.

탐색 도중 target node를 발견하면 즉시 루프문을 빠져나오도록 코드를 수정한다면, DFS는 운이 좋으면 모든 미로를 다 방문하지 않아도 된다. 이 말은 스택에 저장되는 데이터의 수가 비교적 적어서 사용되는 저장 용량이 비교적 적다. (이는 나중에 살펴볼 너비 우선 탐색법(Breadth First Search, BFS)과 비교할 때 적다는 것이다) 

그러나 DFS는 특성 상 모든 탐색 가능한 길 중 하나를 선택하여 그 길을 깊이 탐색하므로, 만약 재귀 호출을 이용하여 DFS를 구현하면 호출 제한에 걸릴 수 있다.

또한 미방문한 인접한 노드들 중 어떤 노드로 먼저 탐색해나가느냐에 따라 실행 때마다 매번 다른 경로를 찾아준다. 

> 이 페이지에서 나온 예제 코드의 풀버전은 다음의 링크에서 확인할 수 있다. 
> [https://github.com/JeroCaller/ds-and-algo-in-python/blob/main/algorithms/graph/draw_maze.py](https://github.com/JeroCaller/ds-and-algo-in-python/blob/main/algorithms/graph/draw_maze.py)


---

Reference

[1] George T. Heineman, “Learning Algorithms: A Programmer’s Guide to Writing Better Code”, (O’Reilly, 2021)

[2] [[알고리즘][탐색] 너비탐색(BFS) & 깊이탐색(DFS)](https://zprooo915.tistory.com/77)

[3] [파이썬 네트워크 시각화 분석하기(python networkx 예제) / 파이썬 데이터 분석 실무 테크닉 100](https://suy379.tistory.com/62)

[4] [Software for Complex Networks — NetworkX 3.1 documentation](https://networkx.org/documentation/stable/index.html)

[5] [문과생이 적어보는 백트래킹 (재귀와 DFS 를 곁들인)](https://velog.io/@newon-seoul/문과생이-적어보는-백트래킹-재귀와-DFS-를-곁들인)

[6] [[백트래킹] 백트래킹의 설명과 간단한 예제풀이](https://youngdroidstudy.tistory.com/entry/백트래킹-백트래킹의-설명과-간단한-예제풀이)