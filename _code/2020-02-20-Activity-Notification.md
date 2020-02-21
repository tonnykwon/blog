---

title: "Fraud Activity Notification"
date: 2020-02-20
categories: Code
mathjax: true
---

HackerLand National Bank has a simple policy for warning clients about possible fraudulent account activity. If the amount spent by a client on a particular day is *greater than or equal to* 2X the client's [median](https://en.wikipedia.org/wiki/Median) spending for a trailing number of days, they send the client a notification about potential fraud. The bank doesn't send the client any notifications until they have at least that trailing number of prior days' transaction data.

Given the number of trailing days  $$d$$ and a client's total daily expenditures for a period of $$n$$ days, find and print the number of times the client will receive a notification over all $$n$$ days.

For example, $$d=3$$ and $$expenditure = [10,20,30,40,50]$$. On the first three days, they just collect spending data. At day 4, we have trailing expenditures of $$[10, 20, 30]$$. The median is $$[20]$$ and the day's expenditure is .$$40$$ Because $$40 \geq 2 *20$$, there will be a notice. The next day, our trailing expenditures are $$[20, 30, 40]$$ and the expenditures are $$50$$. This is less than $$2*30$$ so no notice will be sent. Over the period, there was one notice sent.

**Note**: The median of a list of numbers can be found by arranging all the numbers from smallest to greatest. If there is an odd number of numbers, the middle one is picked. If there is an even number of numbers, median is then defined to be the average of the two middle values. [(Wikipedia)](https://en.wikipedia.org/wiki/Median#Basic_procedure)

**Function Description**

Complete the function *activityNotifications* in the editor below. It must return an integer representing the number of client notifications.

activityNotifications has the following parameter(s):

- *expenditure*: an array of integers representing daily expenditures
- *d*: an integer, the lookback days for median spending



배열과 길이를 나타내는 정수 d가 주어졌을때 d 길이의 부분 배열의 중앙값을 구하고 부분 배열 다음 수와 비교하여 그 수가 중앙값의 2배 이상일 때를 세어야하는 문제이다.

Brute Force로 풀면 당연하게도 timeout으로 실패한다. 첫번째 이 문제를 시도했을때 힙을 사용하여 풀어보기도 하고 여러 방법을 시도했다가 실패했다.

이번에는 Counting sort를 써서 discussion을 참조하면서 겨우겨우 풀었다. 처음에는 Counting sort로 시작 부분 배열을 정렬한 뒤에 bisect를 통해 다음 수를 넣고 이전 수를 빼서 실패했다. 애초에 counting sort를 쓴 이상 bisect를 쓰지 않고 수를 삽입하고 삭제했어야했다.

다음에는 counting sort로 계산된 counting용 배열을 median을 구할때 일일이 새 배열을 만들어서 실패했다. n이 10000개 정도에서는 성공하지만 30000이상부터는 timeout이 되었다. 문제가 되는 코드는 다음과 같다.

```python
count = [[] for _ in range(200)]
[count[each].append(each) for each in expenditure[:d]]

for i in range(d, len(expenditure)):
    # count 배열을 정렬된 배열로 전환
    exp_sorted =[]
    for each in count:
        if len(exp_sorted) <= mid:
            exp_sorted += each
        else:
            break
```

list of list로 이루어진 count를 만들어 각 index에 해당되는 expenditure를 append해주었다.

그리고 median 값을 구하기 위해 직접 배열을 더해가며 새로 만들었다. 이 부분에서 시간이 오래걸렸다.



바뀐 코드는 다음과 같다.

```python
def activityNotifications(expenditure, d):
    # 홀수의 경우 2로 나눈 값에 다음 값이 median, 짝수는 mid, mid+1
    mid = int(d/2)
    answer = 0
    
    # counting sort( n<= 200)
    count = [0]*201
    for each in expenditure[:d]:
        count[each] +=1
    
    # 중앙값 구하기 함수
    if d%2 == 0:
        get_median = lambda median1, median2: (median1+median2)/2
    else:
        get_median = lambda median1, median2: median2
        
    # 부분 배열 중앙값 구하기
    for i in range(d, len(expenditure)):
        # 중앙값 위치 계산
        cumul = 0
        median1 = -1
        for idx, val in count:
            if val == 0:
                continue
            cumul += val
            # 중앙 위치를 넘었을때 위치 저장, median2의 idx가 다를 수 있으니 median1이 저장되면 바뀌지 않도록 조건
            if cumul >= mid and median1 == -1:
                median1 = idx
            if cumul >= mid+1:
                median2 = idx
                break
        
        median = get_median(median1, median2)
        if expenditure[i] >= 2*median:
            answer  +=1
        # 새로운 값 저장
        count[expenditure[i]] +=1
        # 처음 값 제거
        count[expenditure[i-d]] -=1
        
    return answer
```



