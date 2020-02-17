---

title: "Count Luck"
date: 2020-02-17
categories: Code
mathjax: true

---

Ron and Hermione are deep in the Forbidden Forest collecting potion ingredients, and they've managed to lose their way. The path out of the forest is blocked, so they must make their way to a portkey that will transport them back to Hogwarts.

Consider the forest as an grid. Each cell is either empty (represented by **.**) or blocked by a tree (represented by ). Ron and Hermione can move (together inside a single cell) *LEFT*, *RIGHT*, *UP*, and *DOWN* through empty cells, but they *cannot* travel through a tree cell. Their starting cell is marked with the character , and the cell with the portkey is marked with a . The upper-left corner is indexed as .

```
.X.X......X
.X*.X.XXX.X
.XX.X.XM...
......XXXX.
```

In example above, Ron and Hermione are located at index and the portkey is at . Each cell is indexed according to [Matrix Conventions](https://www.hackerrank.com/scoring/board-convention).

Hermione decides it's time to find the portkey and leave. They start along the path and each time they have to choose a direction, she waves her wand and it points to the correct direction. Ron is betting that she will have to wave her wand exactly times. Can you determine if Ron's guesses are correct?

The map from above has been redrawn with the path indicated as a series where is the starting point (no decision in this case), indicates a decision point and is just a step on the path:

```
.X.X.10000X
.X*0X0XXX0X
.XX0X0XM01.
...100XXXX.
```

There are three instances marked with where Hermione must use her wand.

**Note:** It is guaranteed that there is only one path from the starting location to the portkey.

**Function Description**

Complete the *countLuck* function in the editor below. It should return a string, either if Ron is correct or if he is not.

countLuck has the following parameters:

- *matrix*: a list of strings, each one represents a row of the matrix
- *k*: an integer that represents Ron's guess



미로 찾기와 비슷한 문제이다. 좀 더 어려운 점이 있다면 갈림길이 목표지점까지 가는 최종 경로에서 몇 번 나왔는 지 계산해야된다. 처음에 아무런 생각없이 DFS(Depth First Search)로 작성하니 테스트 케이스에서 틀리는 경우가 있어 변경했다.

visited라는 dictionary에 key로는 위치 i,j 인덱스 튜플을 사용하고 value로는 그 지점까지 가는데 거쳐간 갈림길 수를 저장했다. visited를 업데이트하는 방법은 다음과 같다.

1. 좌우상하 가능한 경로가 있는 지 확인

2. 경로가 2개 이상 있다면 갈림길 현재 위치의 visited 개수 업데이트
3. 각 새로운 경로의 visited를 현재 visited와 같도록 업데이트
4. stack에 새로운 경로 추가



```python
def countLuck(matrix, k):
    # 경로 검사시 out of range 에러를 없애기 위해 matrix padding
    m = len(matrix[0])
    matrix.insert(0, ''.join(['X']*m))
    matrix.append(''.join(['X']*m))
    matrix = ['X'+row+'X' for row in matrix]
    
    # 시작 위치 찾기
    current = [(row_idx, row.index('M')) for row_idx, row in enumerate(matrix) if 'M' in row][0]
    
    # dfs
    stack = [current]
    visited = defaultdict(int)
    visited[] = 0
    while stack:
        current = stack.pop()
        # 도착 지점에 도달했는 지 검사
        if matrix[current[0]][current[1]]=='*':
            # 도달했을 때 예상한 k와 같은 지 검사
            if visited[current]==k:
            	return 'Impressed'
        
        # 새로운 경로 검사
        all_exits = set()
        left = (current[0], current[1]-1)
        right = (current[0],current[1]+1)
        up = (current[0]-1, current[1])
        down = (current[0]+1, current[1])
        dir_list = [left, right, up, down]
        for direction for dir_list:
            if matrix[direction[0]][direction[1]]!='X' and direction not in visited:
                all_exits.add(direction)
        
        # 갈림길인 경우, visited 업데이트
        if len(all_exits)>1:
            visited[current] +=1
        
        # 새로운 경로 visited 업데이트 및 stack에 추가
        for exit in all_exits:
            visited[exit] = visited[current]
            stack.append(exit)
            
	return 'Oops!'
```


