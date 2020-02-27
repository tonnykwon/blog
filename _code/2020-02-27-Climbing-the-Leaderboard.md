---

title: "Climbing the Leaderboard"
date: 2020-02-27
categories: Code
mathjax: true
---



Alice is playing an arcade game and wants to climb to the top of the leaderboard and wants to track her ranking. The game uses [Dense Ranking](https://en.wikipedia.org/wiki/Ranking#Dense_ranking_.28.221223.22_ranking.29), so its leaderboard works like this:

- The player with the highest score is ranked number 1 on the leaderboard.
- Players who have equal scores receive the same ranking number, and the next player(s) receive the immediately following ranking number.

For example, the four players on the leaderboard have high scores of 100, 90, 90, and 80. Those players will have ranks 1,2,2, and 3, respectively. If Alice's scores are 70, 80 and 105, her rankings after each game are 4,3 and 1.

**Function Description**

Complete the *climbingLeaderboard* function in the editor below. It should return an integer array where each element $$res[j]$$ represents Alice's rank after the $$j^{th}$$ game.

climbingLeaderboard has the following parameter(s):

- *scores*: an array of integers that represent leaderboard scores
- *alice*: an array of integers that represent Alice's scores



문제 난이도는 medium이지만 생각보다 쉬운 문제이다. 정렬된 숫자가 있을때, 새로운 수가 들어왔을 때 맞는 위치를 찾아주는 것이다. 이때 숫자는 랭킹이므로 중복된 수는 같은 자리로 취급된다. 중복된 수를 처리하고 새로운 자리를 탐색하면 되는 문제이다. set함수를 통해 중복된 수를 제거해주고, 탐색은 이진탐색법을 사용하였다.

코드는 다음과 같다.

```python
# 이진탐색
def bisect(array, x):
    n = len(array)
    
   	low = 0
    high = n-1
    while low <= high:
        mid = (low+high)//2
        
        if array[mid] == x:
            return mid
        elif array[mid]< x:
            high = mid-1
        else:
            low = mid +1
            
    return low


def climbingLeaderboard(scores, alice):
    # 중복 제거
    scores = sorted(set(scores), reverse = True)
    
    # 자리 탐색
    rank = []
    for alice_score in alice:
        alice_rank = bisect(scores, alice_score)
        rank.append(alice_rank+1)
        
    return rank
```

