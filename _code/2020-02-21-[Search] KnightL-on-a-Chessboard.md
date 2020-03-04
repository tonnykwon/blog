---

title: "KnightL on a Chessboard"
date: 2020-02-21
categories: Code
mathjax: true
---

*KinghtL* is a chess piece that moves in an `L` shape. We define the possible moves of as any movement from some position $$(x_1, y_1)$$ to some $$ (x_2, y_2) $$ satisfying either of the following:

-  $$x_2 = x_1 \pm a$$ and $$ y_2 = y_1 \pm b $$, or
-  $$ x_2 = x_1 \pm b $$ and $$y_2 = y_1 \pm a $$

Note that (a,b) and (b,a) allow for the same exact set of movements. For example, the diagram below depicts the possible locations that KinghtL(1,2) or KinghtL(2,1) can move to from its current location at the center of a 5 X 5 chessboard:

![image](https://s3.amazonaws.com/hr-assets/0/1486410238-98ef4547f1-knightl-example-ps.png)

Observe that for each possible movement, the Knight moves 2 units in one direction (i.e., horizontal or vertical) and 1 unit in the perpendicular direction.

Given the value of n for an n X n chessboard, answer the following question for each (a,b) pair where $$ 1 \leq a,b < n $$:

- What is the minimum number of moves it takes for Knight(a,b) to get from position (0,0) to position (n-1, n-1)? If it's not possible for the Knight to reach that destination, the answer is `-1` instead.

Then print the answer for each KinghtL(a,b) according to the *Output Format* specified below.



n이 주어졌을때, 가로 세로 n미만으로  (1,1), (1,2), (1,3), (2,3), ... 처럼 움직일 수 있을때 각각 (0,0)에서 (n-1,n-1)까지 가려면 몇 번 움직여야하는 지 구하는 문제이다.

(1,2)나 (2,1)은 결국 같은 움직임이기에 두 번 구할 필요가 없다.

처음 이 문제를 접했을 때, BFS나 DFS 방법으로 해결하려고 했으나 왠지 모르게 실패했고 포기했다. 이번에는 BFS로 접근해서 풀어주었다. 일단 한번 너비 탐색으로 도달하면 최소 횟수를 구할 수 있기 때문에 BFS가 적합하다. 알고리즘은 간단하게 다음과 같다.

- 결과를 저장할 행렬 mat n-1 X n-1을 만들어 0으로 지정한다.
- 각 1 ~ n-1 루프를 두 번 돌려 (i , j) 움직임에 대한 최소 횟수를 구한다.
  - $$mat[j-1][i-1] != 0$$ 이라면 $$ mat[i-1][j-1] $$에 대입한다.
  - $$mat[i-1][j-1]$$에 해당되는 값을 구한다.
    - queue가 비어있을때까지 반복한다. queue가 비어있으면 -1을 반환한다.
    - 현재 위치가 n-1, n-1이면 count를 반환한다.
    - 0,0에서 시작하여 (i,j), (i,-j), (-i, j), ... 등 8가지의 방향에 대해 움직임에 대해 queue에 삽입한다. 각 움직임에 대한 count도 같이 저장한다.



코드는 다음과 같다.

```python
def bfs(i,j):
    
    queue = [[0,0,0]]
    visited = []
    while queue:
        current = queue.pop(0)
        x, y, count = current
        visited.append((x,y))
        queue_visited = [each[:2] for each in queue]
        
        # 도달했을 시 count 반환
        if x ==n-1 and y == n-1:
            return count
        
        direct = [(x+i, y+j, count+1), (x+i, y-j, count+1), (x-i, y+j, count+1), (x-i, y-j, count+1),
(x+j, y+i, count+1), (x+j, y-i, count+1), (x-j, y+i, count+1), (x-j, y-i, count+1)]
        
        # 방향이 범위에서 벗어나지 않는 지, 방문했는 지, 이미 큐에 있는 지 검사
        direct = [for x,y, count in direct if x >=0 and x< n anad y>=0 and y<n and (x,y) not in visited and (x,y) not in queue_visited]
        direct = list(set(direct))
        queue.extend(direct)
        
   return -1

def knightlOnAChessboard(n):
    mat = [[0 for _ in range(n-1)] for _ in range(n-1)]
    
    for i in range(1, n):
        for j in range(1, n):
            if mat[j-1][i-1]!=0:
                mat[i-1][j-1] = mat[j-1][i-1]
            else:
                # bfs로 i,j에 대한 최소 횟수 구함
                mat[i-1][j-1] = bfs(i,j)
    
    return mat
```

