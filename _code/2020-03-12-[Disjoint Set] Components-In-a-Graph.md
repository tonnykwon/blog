---

title: "Components in a graph"
date: 2020-03-12
categories: Code
mathjax: true
---



그래프의 엣지가 주어졌을 때, 연결되어 있는 가장 큰 엣지의 개수와 가장 작은 수를 출력하는 문제이다.



**Sample Input**

```
5
1 6 
2 7
3 8
4 9
2 6
```

**Sample Output**

```
2 4
```



위 그래프는 [{1, 2, 6, 7}, {3,8}, {4,9}]로 이루어져 있다. 따라서 연결되어 있는 가장 작은 수의 엣지 3,8이나 4,9이므로 2이고 큰 수는 1,2,6,7이므로 4이다.

해당 문제를 그래프 객체를 만들어 DFS 방식으로 풀어볼 수 있다. 먼저 그래프는 객체는 edge 정보를 dictionary에 리스트로 담고, 방문 정보 visited와 탐색할 노드의 순서를 담는 visit 리스트를 데이터로 가진다. 그리고 각 연결되어 있는 노드들의 개수를 세는 n이 있다.

그리고 기능으로는 그래프 구성을 위해 edge를 추가하는 addEdge, 방문되어있는 지 확인하고 아니면 방문하는 DFSVisit, 그리고 노드별 DFS를 수행하는 DFS 함수로 구성한다.

```python
class Graph:
	def __init__(self):
        self.n = 0
        self.edges = defaultdict(list)
        self.visited = dict()
        self.visit = []
        
    def addEdge(self,u,v):
        self.edges[u] = v
        self.edges[v] = u
        self.visited[u] = False
        self.visited[v] = False
        self.visit.append(u)
        
    def DFSVisit(self, u):
        self.n += 1
        self.visited[u] = True
        for each in edges[u]:
            if self.visited[u]==False:
            	self.DFSVisit(each)
                
    def DFS(self, u):
        self.n = 0
        self.DFSVisit(u)
        return self.n
```



이제 메인 함수에서는 그래프를 구성하고 클래스를 이용해 그래프를 탐색한다. DFS는 함수 호출이 많기 때문에 recurssion limit을 높게 잡아놓는다.

```python
import sys
sys.setrecursionlimit(1e+6)

def componentsInGraph(gb):
    # 그래프 빌드
    graph = Graph()
    for u,v in gb:
        graph.addEdge(u,v)
        
    # 그래프 탐색
    cluster = []
    for u in graph.visit:
        if graph.visited[u] == False:
	        cluster.append(graph.DFS(u))
    
    return [min(cluster), max(cluster)]
```

