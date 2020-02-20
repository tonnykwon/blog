---

title: "The Full Counting Sort"
date: 2020-02-20
categories: Code
mathjax: true
---

In this challenge you need to print the string that accompanies each integer in a list sorted by the integers. If two strings are associated with the same integer, they must be printed in their original order so your sorting algorithm should be *stable*. There is one other twist. The first half of the strings encountered in the inputs are to be replaced with the character "-" (dash).

Insertion Sort and the simple version of Quicksort are stable, but the faster in-place version of Quicksort is not since it scrambles around elements while sorting.

In this challenge, you will use counting sort to sort a list while keeping the order of the strings preserved.

For example, if your inputs are [[0, a], [1, b], [0,c], [1,d]]  you could set up a helper array with three empty arrays as elements. The following shows the insertions:

```
i	string	converted	list
0				[[],[],[]]
1 	a 	-		[[-],[],[]]
2	b	-		[[-],[-],[]]
3	c			[[-,c],[-],[]]
4	d			[[-,c],[-,d],[]]
```

The result is then printed: - c- d.

**Function Description**

Complete the *countSort* function in the editor below. It should construct and print out the sorted strings.

countSort has the following parameter(s):

- *arr*: a 2D array where each *arr[i]* is comprised of two strings: *x* and *s*.

**Note**: The first element of each arr[i] , x, must be cast as an integer to perform the sort.



각 string이 index와 input으로 들어왔을때, index대로 sort하여 출력하는 문제이다. 여기서 처음부터 중간까지의 string 데이터는 '-'로 바꾸어주고 순서대로 출력해야된다.

문제 난이도는 medium이지만 실제 counting sort를 알고 있으면 굉장히 쉽다. 풀었던 방법은 다음과 같다:

- index는 100미만으로 존재하기에 100 크기의 리스트 'count'를 만든다.
- 데이터 전체를 루프로 돌린다.
  - 만약 인덱스가 중간 이전이면 string을 '-'로 바꾼다.
  - 해당 count[index]에 해당 string을 추가한다.
- count를 flatten하여 출력한다.



```python
def countSort(arr):
    count = [[] for _ in range(100)]
    mid = int(len(arr)/2)
    
    for index in range(len(arr)):
        if index < mid:
            arr[index][1] = '-'
        count[arr[index][0]].extend(arr[index][1])
    
    print(' '.join([' '.join(each) for each in count if each]))
```



