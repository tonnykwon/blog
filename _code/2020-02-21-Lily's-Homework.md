---

title: "Lily's Homework"
date: 2020-02-21
categories: Code
mathjax: true
---

Whenever George asks Lily to hang out, she's busy doing homework. George wants to help her finish it faster, but he's in over his head! Can you help George understand Lily's homework so she can hang out with him?

Consider an array of n distinct integers, $$arr = [a[0], a[1], ..., a[n-1]] $$. George can swap any two elements of the array any number of times. An array is *beautiful* if the sum of $$\mid arr[i]-arr[i-1]\mid$$ among $$ 0 < i < n $$ is minimal.

Given the array , determine and return the minimum number of swaps that should be performed in order to make the array *beautiful*.

For example, $$ arr= [7, 15, 12, 3]$$. One minimal array is $$ [3, 7, 12, 15] $$. To get there, George performed the following swaps:

```
Swap      Result
      [7, 15, 12, 3]
3 7   [3, 15, 12, 7]
7 15  [3, 7, 12, 15]
```

It took 2 swaps to make the array beautiful. This is minimal among the choice of beautiful arrays possible.

**Function Description**

Complete the *lilysHomework* function in the editor below. It should return an integer that represents the minimum number of swaps required.

lilysHomework has the following parameter(s):

- *arr*: an integer array



배열이 주어졌을때 인접해 있는 수 차이의 전체 절대값이 최소가 되도록 배열의 위치를 바꾸고 바꾼 최소 횟수를 구하는 문제이다. 당연하게도 정렬되어있을 때 인접해 있는 수들의 차이가 최소가 되므로 배열을 정렬해주어야 한다. 여기서 ascending 혹은 descending으로 정렬할 때 swap해주는 회수가 변한다.

처음에는 단순히 기존 배열 위치와 각각  ascending sort, descending sort된 배열의 위치 차이에 -1을 해주어 최소값을 구하려했다. 아무래도 모든 값을 한번씩 바꾸어주려면 위치 차이에서 -1 회수 만큼 swap을 해줘야되기 때문이다. 하지만 한번 swap으로 두 자리가 sorted 배열과 같아지는 경우가 존재해서 실패했다.

따라서 sorted 배열과 같아지도록 기존 배열을 직접 swap을 해가면서 횟수를 구했다. 여기서 list의 index 함수를 사용하면서 swap할 위치를 구했다. 답은 맞았지만 timeout으로 실패하는 경우가 있었다.

마지막에는 hash table을 사용하여 위치를 저장하고 이를 사용해서 swap할 위치를 바로 구하는 방법을 사용했다. 전체 알고리즘은 다음과 같다.

- 배열의 값을 key, 위치를 value로 dictionary에 저장하고 복사한다.
- 배열을 ascending, descending 방법으로 각각 정렬한다.
- 기존 배열과 각 정렬된 배열을 비교하며 기존 배열의 위치를 정렬된 배열과 같도록 위치를 바꾼다. 이때 정렬된 배열을 기준으로 하나씩 바꾸어간다. 바뀌는 횟수를 저장해둔다.
- ascending과 descending에 사용된 swap 횟수를 비교하여 최소값을 반환한다.



```python
def lilysHomework(arr):
    n = len(arr)
    # 각 값에 대한 위치 정보 hash table에 저장
    mapping = dict()
    for idx, value in enumerate(arr):
    	mapping[value] = idx
        
    # 기존 배열과 hash table 복사
    mapping_reverse = mapping.copy()
    arr_reverse = arr.copy()
    
    # 정렬
    sorted_arr = sorted(arr, key = abs)
    reverse_arr = sorted(arr, reverse = True, key = abs)
    
    # descending
    count1 = 0
    for i in range(n):
        if sorted_arr[i] != arr[i]:
            sorted_idx = mapping[sorted_arr[i]]
            
            # hash table 업데이트
            mapping[sorted_arr[i]] = i
            mapping[arr[i]] = sorted_idx
            
            # swap
            arr[i], arr[sorted_idx] = arr[sorted_idx], arr[i]
            count1 +=1
            
            
    # ascending
    count2 = 0
	for i in range(n):
        if reverse_arr[i] != arr_reverse[i]:
            sorted_idx = mapping_reverse[reverse_arr[i]]
            
            mapping[reverse_arr[i]] = i
            mapping[arr_reverse[i]] = sorted_idx
            
            arr_reverse[i], arr_reverse[sorted_idx] = arr_reverse[sorted_idx], arr_reverse[i]
            count2 +=1
    
    return min(count1, count2)
```

