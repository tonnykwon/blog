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

