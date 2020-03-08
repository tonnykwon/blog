---

title: "[Greedy] Goodland Electiricty"
date: 2020-03-08
categories: Code
mathjax: true
---

**Function Description**

Complete the *pylons* function in the editor below. It should return an integer that represents the minimum number of plants required or -1 if it is not possible.

pylons has the following parameter(s):

- *k*: an integer that represents distribution range
- *arr*: an array of integers that represent suitability as a building site



**Sample Input**

```
6 2
0 1 1 1 1 0
```

**Sample Output**

```
2
```



0, 1 배열이 주어지고 k가 주어진다. 각 배열 중 1을 선택할 수 있는데 그렇게 되면 해당 인덱스-k-1, 인덱스+k-1 까지의 요소를 채울 수 있다. 배열의 모든 부분을 채울 때까지 몇 개의 1을 선택해야지 고르는 문제이다. 만약 1이 알맞은 위치에 없다면 다 못 채울 수 있다. 그럴 때는 -1을 반환하면 된다.



Greedy 카테고리에 있어 탐욕적인 방법으로 풀었다. 

- i = k-1
- count = 0

- i가 n보다 작으면 반복

  - 만약 해당 위치가 이미 방문했던 곳이라면 가능한 설치 위치가 없으므로 -1을 반환한다.
  - 왼쪽을 기준으로 선택했을 때 가장 많이 채울 수 있는 가장 먼 1을 선택하고 count를 1 올린다.
  - 해당 위치에 1이 없다면 인덱스를 줄여가며 1을 찾아채운다.

- 만약 i-k+1위치가 n보다 작으면 count를 하나 더 올린다.

  

```python
def pylons(k, arr):
    n = len(arr)
    count = 0
    selected = [0]*n
    i = k-1
    while i < n:
        if selected[i]==1:
            return -1
        
    	if arr[i]==1:
            selected[i]=1
            count +=1
            i += 2*k-1
        else:
            i -=1
            
    if i-k+1<n:
        if sum(arr[n-k:])>=1:
            count+=1
        else:
            return -1
    return count
```



