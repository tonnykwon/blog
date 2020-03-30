---

title: "[Greedy] 큰 수 만들기"
date: 2020-03-30
categories: Code
mathjax: true
tags: [greedy, programmers]
---



탐욕법을 사용하여 주어진 숫자를 가장 크게 만드는 문제



###### 문제 설명

어떤 숫자에서 k개의 수를 제거했을 때 얻을 수 있는 가장 큰 숫자를 구하려 합니다.

예를 들어, 숫자 1924에서 수 두 개를 제거하면 [19, 12, 14, 92, 94, 24] 를 만들 수 있습니다. 이 중 가장 큰 숫자는 94 입니다.

문자열 형식으로 숫자 number와 제거할 수의 개수 k가 solution 함수의 매개변수로 주어집니다. number에서 k 개의 수를 제거했을 때 만들 수 있는 수 중 가장 큰 숫자를 문자열 형태로 return 하도록 solution 함수를 완성하세요.

##### 제한 조건

- number는 1자리 이상, 1,000,000자리 이하인 숫자입니다.
- k는 1 이상 `number의 자릿수` 미만인 자연수입니다.

##### 입출력 예



| number     | k    | return |
| ---------- | ---- | ------ |
| 1924       | 2    | 94     |
| 1231234    | 3    | 3234   |
| 4177252841 | 4    | 775841 |



## 풀이

왼쪽부터 k+1 길이의 window를 이용해서 가장 큰 수를 만날때까지 버리고 버린 수만큼 k를 줄인다. 그리고 다시 k+1길이의 window를 이용하여 큰 수를 만날때까지 버리는 방식을 반복하여 가장 큰 수를 만들었다. k대신 k+1 길이를 사용하는 이유는 마지막 버릴 수 있는 수가 1개 남았을 때(k=1), 비교할 수 없기에 최소 2개의 길이의 window를 사용할 수 있도록 해주기 위함이다.

k가 1 미만으로 감소하면 while문을 멈춘다.

```python
def solution(number, k):
    n = len(number)
    answer = ''
    
    start = 0
    i = k+1
    while i>=1:
        # window 설정
        frac = number[start:start+i]
        if frac:
            j = frac.index(max(frac))
            answer += frac[j]
            # 다시 j+1 지점에서 k 길이 window 설정
            start += j+1
            i -= j
            
        # number 모두 탐색한 경우
        else:
            break
        
        # 이미 answer 길이가 완성되었을 때, 뒤 숫자를 concat함
        if len(answer) == n-k:
            answer += number[start+i+1:]
            break
    
    return answer
```



해당 방식으로는 테스트 케이스 1개에서 시간 초과가 나서 풀지 못한다. 문제는 list slide에 있다. `number[start:start+i]`는 결구 `O(n)`만큼의 시간 복잡도가 들고 k가 굉장히 큰 문제에서는 시간이 많이 소요된다.

마찬가지로`frac.index(max(frac))`과 같이 max값을 구하고 인덱스를 찾는 방법도 많은 시간 복잡도가 소요된다.



따라서, 스택을 이용하여 풀어보게 되었다. 새로운 수가 현재 스택에 있는 수보다 크면 스택을 비우고 해당 수를 포함하고, 비운 만큼 k를 줄인다. 이런 방식으로 k를 전부 소진하거나 answer길이가 완성되었을 때까지 반복한다.

```python
def solution(number, k):
	n = len(number)
    answer_len = n-k
    stack = []
    # 모든 수에 대해 반복
    for i in range(n):
        # 현재 수가 해당 스택에 있는 수보다 작을때까지 반복
        while stack and k >0:
            top = stack.pop()
            if top < number[i]:
                k-=1
            else:
                # 스택에 있는 수가 더 클때, stack에 top 유지
                stack.append(top)
                break
        stack.append(number[i])
    
    # stack에 정답 길이보다 더 길때, 가장 오른쪽부터 삭제
    while len(stack) != answer_len:
        stack.pop()
    
	return ''.join(stack)
```

