---

title: "징검다리 건너기"
date: 2020-04-30
categories: Code
mathjax: true
tags: [min-max, programmers, kakao]
---



window를 옮겨가며 min max를 구하는 문제이다.



###### 문제 설명

**[본 문제는 정확성과 효율성 테스트 각각 점수가 있는 문제입니다.]**

카카오 초등학교의 니니즈 친구들이 라이언 선생님과 함께 가을 소풍을 가는 중에 **징검다리**가 있는 개울을 만나서 건너편으로 건너려고 합니다. 라이언 선생님은 니니즈 친구들이 무사히 징검다리를 건널 수 있도록 다음과 같이 규칙을 만들었습니다.

- 징검다리는 일렬로 놓여 있고 각 징검다리의 디딤돌에는 모두 숫자가 적혀 있으며 디딤돌의 숫자는 한 번 밟을 때마다 1씩 줄어듭니다.
- 디딤돌의 숫자가 0이 되면 더 이상 밟을 수 없으며 이때는 그 다음 디딤돌로 한번에 여러 칸을 건너 뛸 수 있습니다.
- 단, 다음으로 밟을 수 있는 디딤돌이 여러 개인 경우 무조건 가장 가까운 디딤돌로만 건너뛸 수 있습니다.

니니즈 친구들은 개울의 왼쪽에 있으며, 개울의 오른쪽 건너편에 도착해야 징검다리를 건넌 것으로 인정합니다.
니니즈 친구들은 한 번에 한 명씩 징검다리를 건너야 하며, 한 친구가 징검다리를 모두 건넌 후에 그 다음 친구가 건너기 시작합니다.

디딤돌에 적힌 숫자가 순서대로 담긴 배열 stones와 한 번에 건너뛸 수 있는 디딤돌의 최대 칸수 k가 매개변수로 주어질 때, 최대 몇 명까지 징검다리를 건널 수 있는지 return 하도록 solution 함수를 완성해주세요.

#### **[제한사항]**

- 징검다리를 건너야 하는 니니즈 친구들의 수는 무제한 이라고 간주합니다.
- stones 배열의 크기는 1 이상 200,000 이하입니다.
- stones 배열 각 원소들의 값은 1 이상 200,000,000 이하인 자연수입니다.
- k는 1 이상 stones의 길이 이하인 자연수입니다.

------

##### **[입출력 예]**

| stones                         | k    | result |
| ------------------------------ | ---- | ------ |
| [2, 4, 5, 3, 2, 1, 4, 2, 5, 1] | 3    | 3      |

##### **입출력 예에 대한 설명**

------

**입출력 예 #1**

첫 번째 친구는 다음과 같이 징검다리를 건널 수 있습니다.
![step_stones_104.png](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/4560e242-cf83-4e77-a14c-174f3831499d/step_stones_104.png)

첫 번째 친구가 징검다리를 건넌 후 디딤돌에 적힌 숫자는 아래 그림과 같습니다.
두 번째 친구도 아래 그림과 같이 징검다리를 건널 수 있습니다.
![step_stones_101.png](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/d64f29ac-3e35-4fd3-91fa-4d70e3b6c80a/step_stones_101.png)

두 번째 친구가 징검다리를 건넌 후 디딤돌에 적힌 숫자는 아래 그림과 같습니다.
세 번째 친구도 아래 그림과 같이 징검다리를 건널 수 있습니다.
![step_stones_102.png](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/369bc8a1-7017-4135-a499-505247ab9cfc/step_stones_102.png)

세 번째 친구가 징검다리를 건넌 후 디딤돌에 적힌 숫자는 아래 그림과 같습니다.
네 번째 친구가 징검다리를 건너려면, 세 번째 디딤돌에서 일곱 번째 디딤돌로 네 칸을 건너뛰어야 합니다. 하지만 k = 3 이므로 건너뛸 수 없습니다.



![step_stones_103.png](https://grepp-programmers.s3.ap-northeast-2.amazonaws.com/files/production/e44e0a83-e637-48ad-858c-4c135c3b078f/step_stones_103.png)

따라서 최대 3명이 디딤돌을 모두 건널 수 있습니다.



## 문제 풀이



k개의 돌을 건너뛸 수 있기 때문에 각 k 구간 돌 사이에서는 k 구간의 최대 디딤돌의 숫자만큼 건널 수 있다. 즉, k = 3일때, [2, 1, 3]이라는 구간에서 가장 큰 값인 3명까지 이 구간을 건널 수 있다. 왜냐하면 2명이 건너가고 난 뒤인 [0, 0, 1]에서 한명만 더 건너면 더 이상 아무도 건널 수 없기 때문이다.

이를 전체 배열 stones에서 봤을 때는 각 k 구간의 최대값 중 가장 작은 값을 찾으면 된다. 왜냐하면 그 구간에서 건널 수 있는 디딤돌의 숫자를 모두 써버리면 더 이상 사람들이 건너지 못하기 때문이다.

간단히 subarray의 max값 중 min값을 찾는 문제이다.



단순히 루프를 돌려 한명씩 건너가게 한 후에 체크하는 방법은 효율성에서 떨어진다. 따라서, window를 옮겨 가면서 가장 큰 값을 넣어가며 비교해간다. 여기서 window 정렬 방법으로 bisect 이진 탐색을 사용했다.



```python
def solution(stones, k):
    answer = 2e+9
    n = len(stones)
    
    # k 길이의 window를 정렬하고 최대값을 구한다.
    k_stones = stones[:k]
    max_val = max(stones)
    # 글로벌 최소값
    min_val = max_val
    
    # 각 구간을 돌며 최대값을 비교해간다.
    for i in range(k, n):
        # 새로운 element 추가
        bisect.insort(k_stones, stones[k])
        # 이전 element 제거
        del k_stones[bisect.bisect_left(k_stones, stones[i])]
        # 글로벌 최소값 업데이트
        if k_stones[-1] < min_val:
            min_val = k_stones[-1]
        
    return min_val
```



이상하게 마지막 효율성 테스트 14에서 실패한다. 다른 방법으로 counting sort로 풀어봐야겠다.