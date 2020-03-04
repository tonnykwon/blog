---

title: "[Search] Red Knight's Shortest Path"
date: 2020-03-04
categories: Code
mathjax: true
---

n ordinary chess, the pieces are only of two colors, black and white. In our version of chess, we are including new pieces with unique movements. One of the most powerful pieces in this version is the *red knight*.

The red knight can move to six different positions based on its current position (UpperLeft, UpperRight, Right, LowerRight, LowerLeft, Left) as shown in the figure below.

![image](https://s3.amazonaws.com/hr-challenge-images/0/1479394883-0caf08859d-Capture1.PNG)

The board is a grid of size $$ n \times n$$. Each cell is identified with a pair of coordinates $$ (i, j) $$, where i is the row number and j is the column number, both zero-indexed. Thus, $$(0, 0)$$ is the upper-left corner and $$(n-1, n-1)$$ is the bottom-right corner.

Complete the function `printShortestPath`, which takes as input the grid size n, and the coordinates of the starting $$(i_{start}, j_{start}) $$and ending position $$(i_{end}, j_{end})$$ and respectively, as input. The function does not return anything.

Given the coordinates of the starting position of the red knight and the coordinates of the destination, print the minimum number of moves that the red knight has to make in order to reach the destination and after that, print the order of the moves that must be followed to reach the destination in the shortest way. If the destination cannot be reached, print only the word "Impossible".

*Note:* There may be multiple shortest paths leading to the destination. Hence, assume that the red knight considers its possible neighbor locations in the following order of priority: *UL, UR, R, LR, LL, L*. In other words, if there are multiple possible options, the red knight prioritizes the first move in this list, as long as the shortest path is still achievable. Check sample input for 2 an illustration.



UL, UR, R, LR, LL, L과 같이 움직일 수 있을 때, 시작점부터 도착지점까지 최소 몇 번을 움직이고 어떻게 움직이는 지 출력하는 문제이다. Knight On a chessboard 문제와 거의 비슷하다. BFS를 사용하여 각 움직임에 대해 조건에 만족했는 지 구할 수 있다. 하지만 BFS대로 풀면 timeout이 된다. 현재 위치와 목표 위치에 따라 움직임을 제한해서 좀 더 효율적으로 트리를 구성할 수 있다.

- 목표 지점 위에 존재할 때, LR, LL 움직임은 필요가 없다.
  - 만약 같은 j열 위치에 있다면 위로만 움직이면 된다.(UR, UL)
  - 그렇지 않다면 위 왼쪽 오른쪽으로만 움직이면 된다.(UR, R, UL, L)
- 반대로는 UL, UR이 필요없다.
  - 만약 같은 j열 위치에 있다면 아래로만 움직이면 된다.(LR, LL)
  - 그렇지 않다면 아래 왼쪽 오른쪽으로만 움직이면 된다.(LR, R, LL, L)
-  같은 i행에 위치한다면 L, R로만 이동하면 된다.
  - 왼쪽에 있을 경우(L)
  - 오른쪽에 있을 경우(R)

이러한 방법들을 backtracking이라고 한다. 어떤 노드의 유망성을 검사하여 유망하지 않을 경우, 다시 부모 노드로 가 다른 자식 노드를 검사하도록 한다. 이러한 방법으로 풀이 시간이 단축된다. 또 visited 행렬을 만들어 방문했을 시 1로 표기하여 다시 방문하지 않도록 하였다.



```python
def printShortestPath(n, i_start, j_start, i_end, j_end):
	# 방문 기록
    visited = [[0 for _ in range(n)] for _ in range(n)]
    # BFS 큐에 시작지점, 단계, path 저장
    queue = [[i_start, j_start, 0, []]]
    while queue:
        i_cur, j_cur, step, path = queue.pop(0)
        if visited[i_cur][j_cur]==0:
            current = [i_cur, j_cur]
            # 방문 체크
            visited[i_cur][j_cur] = 1

            # 결과에 도달했는 지 검사
            if current[0] == i_end and current[1]==j_end:
                print(step)
                [print(each, end = ' ') for each in path]
                break

            # backtracking upper
            if i_end < i_cur:
                if j_end == j_start:
                    possible = [[-2, -1, 'UL'],[-2, 1, 'UR']]
                else:
                    possible = [[-2, 1,'UR'], [0, 2,'R'], [-2, -1,'UL'], [0, -2,'L']]
            # lower
            elif i_end > i_cur:
                if j_end == j_start:
                    possible = [[2, 1, "LR"], [2, -1, 'LL']]
                else:
                    possible =  [[0, 2, 'R'],[2, 1, "LR"], [2, -1, 'LL'], [0,-2, 'L']]
            # same
            else:
                if j_end < j_start:
                    possible = [[0, -2, 'L']]
                else:
                    possible = [[0, 2, 'R']]


            # 경로 valid한지 검사
            moves = [[current[0]+move_i, current[1]+move_j, step+1, path+[new_path]] for move_i, move_j, new_path in possible
                     if current[0]+move_i <n and current[0]+move_i >=0 and 
                     current[1]+move_j <n and current[1]+move_j >=0 and 
                     visited[current[0]+move_i][current[1]+move_j]==0 ]

            # queue 추가
            queue.extend(moves)
		else:
            pass
   # 미도달시
   print('Impossible')
```

