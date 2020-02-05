---

title: "Merge Overlapping Interval"
date: 2020-02-05
categories: Code
mathjax: true

---

The city of Gridland is represented as an matrix where the rows are numbered from to and the columns are numbered from to .

Gridland has a network of train tracks that always run in straight horizontal lines along a row. In other words, the start and end points of a train track are and , where represents the row number, represents the starting column, and represents the ending column of the train track.

The mayor of Gridland is surveying the city to determine the number of locations where lampposts can be placed. A lamppost can be placed in any cell that is *not occupied* by a train track.

Given a map of Gridland and its train tracks, find and print the number of cells where the mayor can place lampposts.

**Note:** A train track may overlap other train tracks within the same row.

For example, if Gridland's data is the following:

```python
k = 3
r	c1	c2
1	1	4
2	2	4
3	1	2
4	2	3	
It yields the following map:
```



해당 예제의 답은 9이다. 1번 행에서는 모든 구역을 사용하여 lamppost 설치할 공간은 0이고, 2번 행에서는 2~4 구역을 사용하여 1(1)에 설치 가능하다. 3번 행에서는 3~4(2), 4번 행에서는 1,4(2)에 설치 가능하다.

따라서 전체 설치 가능 공간은 5이다.



단순히 n * m 배열을 만들어 풀 수 있을 거 같지만 다음과 같은 고려 사항이 있다.

- $$n, m \leq 10^9 $$ 이므로 배열을 만들고 각 자리마다 계산한다면 메모리 에러, 시간 초과 문제가 발생한다
- 같은 행에 대한 정보가 중복 발생한다. overlapping되는 구간이 생겼을 때를 처리해야한다



따라서 겹치는 interval을 합쳐주는 방법이 필요하다. 해당 알고리즘은 Geeksforgeeks에서 가져왔다.

- start time을 기준으로 오름차순으로 정렬한다
- 첫 interval을 스텍에 넣어준다
- 남은 interval마다 다음 중 하나를 수행한다
  - a: 만약 스텍 탑에 있는 interval과 현재 interval이 오버랩된다면 새로운 interval로 업데이트하여 스텍에 넣어준다
  - b: 오버랩되지 않는다면 스텍에 현재 interval을 넣어준다
- 새로 합쳐진 interval 스텍을 반환한다



해당 방법이 빠르게 interval을 merge하는 이유는 interval이 start time 기준으로 정렬되어 있기 때문이다. 만약 현재 interval이 스텍 탑 interval과 겹치지 않는다면 다음 interval 또한 겹치지 않을 것이다. 왜냐하면 다음 interval은 start time이 더 늦기 때문이다.

```python
def interval_merge(interval_list):
    # sort intervals based on starting time
    interval_list = sorted(interval_list, key = lambda data: data[0] )
    # put first interval into the stack
    stack = [interval_list.pop(0)]
    # repeat for every left intervals
    for start, end in interval_list:
        if stack[-1][1] >= start:
            top = stack.pop()
            new_interval = [top[0], max(top[1], end)]
            stack.append(new_interval)
        else:
            stack.append([start, end])
    
    return stack
```



나머지 부분:

```python
def gridlandMetro(n, m, k, track):
    answer = 0
    # zero case
    if k ==0:
    	return n*m
    
    # count dict for duplicate check
    count = Counter(list(zip(*track))[0])
    duplicated = defaultdict(list)
    for r, c1, c2 in track:
        if count[r] > 1:
            duplicated[r] += [[c1, c2]]
        else:
            answer + m - (c2-c1+1)
            
    # add train free zone
    answer += (n- len(count.keys()))*m
    
    # merge interval
    for key, interval_list in duplicated.items():
        # merge
        intervals = interval_merge(interval_list)
        
        # add intervals
        ranges = 0
        for c1, c2 in intervals:
            ranges += c2-c1+1
        answer += m - ranges
        
    return answer
```

