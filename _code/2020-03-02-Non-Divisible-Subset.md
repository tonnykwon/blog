---

title: "Non-Divisible Subset"
date: 2020-03-02
categories: Code
mathjax: true
---

Given a set of distinct integers, print the size of a maximal subset of  S where the sum of any 2 numbers in $$S' $$ is *not* evenly divisible by k.

For example, the array $$ S = [19, 10, 12, 10, 24,25, 22] $$ and $$k =4$$. One of the arrays that can be created is $$S'[0] = [10, 12, 25] $$ . Another is $$ S'[1] = [19, 22, 24] $$. After testing all permutations, the maximum length solution array has 3 elements.

**Function Description**

Complete the *nonDivisibleSubset* function in the editor below. It should return an integer representing the length of the longest subset of S meeting the criteria.

nonDivisibleSubset has the following parameter(s):

- *S*: an array of integers
- *k*: an integer



배열이 주어졌을 때, 어느 pair이든 합했을때 k로 안나누어지는 요소들로 이루어진 부분 배열 중 가장 큰 부분 배열을 찾는 문제이다. Discussion에서 힌트를 얻어 풀게 되었다. 

$$ a \% k +b \% k = (a+b)\% k$$ 

두 수의 합의 나머지는 각각 수의 나머지 합과 같기 때문에 모든 수를 k에 대한 나머지로 구해놓고 시작할 수 있다. 그러면 $$0, 1, ..., k-1$$의 수로 나타나게 된다. 이때 합이 k가 되는 나머지의 pair을 피해서 부분 배열을 구해야한다. 그런 pair은 $$ (0, k), (1, k-1), (2, k-2), (3, k-3), ..., $$ 처럼 묶일 수 있다. 

그렇다면 최대 부분 배열을 만들려면 k가 안되도록 pair중에서 개수가 많은 수를 각각 하나씩 뽑아 사용하여 된다. 

여기서 주의해야될 점은 0인 케이스와 pair 두 수가 같을 때이다. 

- 나머지가 0인 경우는 나머지가 0인 다른 수와 합쳤을 때 k로 나누어 떨어진다
- k가 짝수인 경우, pair의 두 수 모두 k/2로 이루어졌을 때, 두 pair합 역시 k로 나누어 떨어진다.

위 경우만 제외해서 최대 부분 배열의 길이를 구할 수 있다.



```python
from collections import Counter
def nonDivisibleSubset(s, k):
    
    # 모든 수 나머지 구하기
    remainder = [each_s%k for each_s in s]
    
    # 각 나머지 개수 구하기
    count = Counter(remainder)
    
    # pair 만들기
    pair = [[i, k-i] for i in range(k)]
    
    # pair 선택
    answer  = 0
    for a, b in pair:
        
        # 나머지가 0인 경우 한 개만 사용
        if a == 0 and count[a]>=1:
            answer +=1
        # 나머지가 k/2인 경우 한 개만 사용
        elif a == b and count[b] >=1:
            answer +=1
        elif count[a] > count[b]:
            answer += count[a]
        elif count[b] >= count[a]:
            answer += count[b]
        else:
            pass
   
   return answer
```





