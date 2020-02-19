---

title: "Cut the Tree"
date: 2020-02-18
categories: Code
mathjax: true

---

Anna loves graph theory! She has a tree where each vertex is numbered from 1 to n, and each contains a data value.

The *sum* of a tree is the sum of all its nodes' data values. If she cuts an edge in her tree, she forms two smaller trees. The *difference* between two trees is the absolute value between their sums.

Given a tree, determine which edge to cut so that the resulting trees have a minimal *difference* between them, then return that difference.

For example, your tree's nodes have weights of [1,2,3,4,5,6]. In this case, node numbers match their weights for convenience. In the diagram below, you have the following edges: [(1,2), (1,3), (2,6), (3,4), (3,5)].

The values are calculated as follows:

```python
Edge    Tree 1  Tree 2  Absolute
Cut     Sum      Sum     Difference
1        8         13         5
2        9         12         3
3        6         15         9
4        4         17        13
5        5         16        11
```

The minimum absolute difference is 1.



트리가 주어졌을때, 각 엣지를 잘랐을때 두 트리 전체 값의 차이가 최소값을 구하는 문제이다. 각 엣지를 자르고 이에 따라 생기는 트리를 dfs로 구하여 풀었는데 timeout으로 실패했다.

다른 사람의 풀이를 보니 데이터를 트리보다는 그래프로 표현하여 해결하였다. 엣지가 없는 자식 노드들을 그래프에서 제거하면서 노드값들을 부모 노드에 합산한다. 엣지를 제거했을때 엣지가 제거된 노드와 그 아래 있는 자식 노드들로 이루어지게 된다. 따라서 전체 노드값에서 해당 부모 노드의 합산된 값을 두번 빼주면 두 그래프의 차이가 나온다.



```python
def cutTheTree(data, edges):
    n = len(data)
    totla = sum(data)
    
    # create graph
    graph = [[] for i in range(n)]
    for u, v in edges:
        graph[u-1].append(v-1)
        graph[v-1].append(u-1)
    
    # update sum values
    totVal = [0]*n
    child = {i for i in range(n) if len(graph[i])==1}
    while len(child):
        for i in child:
            totVal[i] +=data[i]
            
            if len(graph[i]):
                # remove node from parnaet
                graph[graph[i][0]].remove(i)
                # add node value to parent node
                totVal[graph[i][0]] += totVal[i]
        # update child nodes
        child = {graph[i][0] for i in child if len(graph[i]) and len(graph[graph[i][0]])==1}
        
        # calculate min diffrence
        min_dif = abs(total - 2*totVal[0])
        for i in range(1,n):
            dif = abs(totoal-2*totVal[i])
            if dif < min_dif:
            	min_dif = dif
        
        return min_dif
```

