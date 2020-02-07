---

title: "Maximum Subarray Sum"
date: 2020-02-07
categories: Code
mathjax: true

---

We define the following:

- A *subarray* of array of length is a contiguous segment from through where .
- The *sum* of an array is the sum of its elements.

Given an element array of integers, , and an integer, , determine the maximum value of the sum of any of its subarrays modulo . For example, Assume and . The following table lists all subarrays and their moduli:

```
		sum	%2
[1]		1	1
[2]		2	0
[3]		3	1
[1,2]		3	1
[2,3]		5	1
[1,2,3]		6	0
```

The maximum modulus is .

배열이 주어졌을 때, 배열의 모든 일부 배열에 m에 대한 나머지 구하고 그 중 가장 큰 값을 문제이다. Brute Force로 사용했을 때, 배열 길이 1~n에 대해서 각 배열 일부부를 구하면 시간 복잡도가 $$O(n^2)$$이다. n이 $$10^5$$까지 가능하므로 다른 방법을 구해야 풀 수 있다.

풀리지 않아서 Quora와 Discussion을 참고했다. 여기서 Modular arithmetic을 사용하여 더 간단히 풀 수 있다.

$$ (a+b)\%m = (a\%m + b\%m)%m $$

$$ (a-b)\%m = (a\%m - b\%m)\%m$$



위 식을 이용하여 각 지점까지의 누적합에 대한 나머지값을 prefix 배열에 저장해놓은다.

$$ prefix[n] = (a[0]+ ... +a[n]) \% M$$

```python
prefix = [0]%n
curr = 0
for i in range(n):
    curr = (a[i]%m+curr)%m
    prefix[i] = curr
```



prefix를 이용하면 i~j에 해당되는 부분 배열 나머지를 쉽게 구할 수 있다.

$$sumModular[i,j] = (prefix[j]- prefix[i-1]+ M)\%M$$

코드로 구현하면 다음과 같다.

```python
max_module = 0
for i in range(n):
    for j in range(i+1, n):
        max_module = max(max_module, (prefix[j]-prefix[i-1]+m)%m)
    # compare current prefix
    max_module = max(max_module, prefix[i])
```

나머지값을 구할 때 연산하는 과정은 단순화되었지만 전체 과정이 $$O(n^2)$$의 시간복잡도를 가진다는 것에서는 바뀐 게 없다. 



이진 탐색 알고리즘을 통해 더 빠르게 구할 수 있다.

이진 탐색 알고리즘은 정렬되어 있는 배열에 새로운 값 x가 들어 왔을 때, 해당 x가 어느 곳에 들어가야할  지 알려주는 알고리즘이다. 정렬되어 있다는 점을 이용해서 중간값을 x와 비교하여 왼쪽, 오른쪽에 삽입될 지 정하고 다시 나머지 배열을 반으로 나누는 작업을 반복하여 삽입 자리를 구한다.

```python
def bisect(a, x, low, high):
	while low < high:
        mid = (low+high)//2
        if a[mid]<x:
            low = mid+1
        else:
        	high = mid
		
    return low
```



정렬된 prefix[0:i]값들과 prefix[i]를 비교하여 작은 수 중 최대값을 구한다. 그리고 해당 값과 prefix[i]를 더해 나머지값을 구한다.

```python
pq = [prefix[0]]
max_module = max(prefix)
for i in range(1,n):
	left = bisect_right(pq, prefix[i])
	if left != len(pq):
        modsum = (prefix[i] - pq[left]+m)%m
        max_module = max(max_module, modsum)
        
    insort(pq, prefix[i])
```

