---

title: "Connected Cells in a Grid"
date: 2020-02-07
categories: Code
mathjax: true

---

Consider a matrix where each cell contains either a or a . Any cell containing a is called a *filled* cell. Two cells are said to be *connected* if they are adjacent to each other horizontally, vertically, or diagonally. In the following grid, all cells marked `X` are connected to the cell marked `Y`.

```
XXX
XYX  
XXX    
```

If one or more filled cells are also connected, they form a *region*. Note that each cell in a region is connected to zero or more cells in the region but is not necessarily directly connected to all the other cells in the region.

Given an matrix, find and print the number of cells in the largest *region* in the matrix. Note that there may be more than one region in the matrix.

For example, there are two regions in the following matrix. The larger region at the top left contains cells. The smaller one at the bottom right contains .

```
110
100
001
```

**Function Description**

Complete the *connectedCell* function in the editor below. It should return an integer that denotes the area of the largest region.

connectedCell has the following parameter(s):
\- *matrix*: a 2D array of integers where represents the row of the matrix



위처럼 0, 1 행렬이 주어졌을때, 1로 연속해서 이루어진 가장 큰 행렬 값을 찾는 것이다. 행렬은 상하좌우뿐만 아니라 대각선으로도 이어질 수도 있다.

해당 문제를 풀기 위해 depth first search를 사용했다.

각 행 i, 열 j 칸에서 갈 수 있는 8가지 방향을 그래프로 만든 뒤에 dfs로 해당 칸이 1이면 stack에 추가하고 아니면 다음 연속된 칸으로 넘어가는 방식으로 만들었다.

칸에서의 움직임이 행렬을 넘어가지 않도록 0으로 전체를 padding하는 방법을 사용했다.

그리고 마지막으로 모든 1인 칸에 대해 dfs를 시행했다. 별로 마음에 들지 않은 풀이였지만 테스트 케이스는 통과했다.

그래프 만들기

```python
graph = defaultdict(list)
n = len(matrix)
m = len(matrix[0])

# padding
matrix.insert(0, [0]*(m+2))
matrix.append([0]*(m+2))
for i in range(1, n+1):
    matrix[i].insert(0, 0)
    matrix[i].append(0)
    
    for j in range(1, m+1)
        graph[(i,j)] += [(i+1,j)]
        graph[(i,j)] += [(i+1,j+1)]
        graph[(i,j)] += [(i,j+1)]
        graph[(i,j)] += [(i-1,j+1)]
        graph[(i,j)] += [(i-1,j)]
        graph[(i,j)] += [(i-1,j-1)]
        graph[(i,j)] += [(i,j-1)]
        graph[(i,j)] += [(i+1,j-1)]
```



dfs 구현

```python
def dfs(graph, root, matrix):
	visited = []
    stack = [root]
    
    while stack:
        node = stack.pop()
        if node not in visited:
            visited.append(node)
            # add if 1
            [stack.append(element) for element in graph[node] if matrix[element[0]][element[1]]==1]
    
    return len(visited)
```



dfs 실행

```python
# starts and ends with +1 since padded
max_result = 0
for i in range(1, n+1):
	for j in range(1, m+1):
		max_result = max(max_result, dfs(graph, (i,j), matrix ))
```

