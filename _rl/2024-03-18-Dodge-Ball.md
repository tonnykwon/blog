---
title: "강화학습을 게임 봇 만들기 (2)"
date: 2024-03-18
categories: rl
mathjax: true
tags: [RL, ml-agents, Unity]
---

## Dodge Ball
- 강화학습을 게임 봇 만들기 (1)에서 문제를 계속 풀다가 FPS와 가장 유사한 환경으로 구성된 DodgeBall 샘플 프로젝트를 찾음
- 총을 사용하는 대신 공(투사체)를 사용하는 것 외에는 큰 차이점이 없었음

### Dodge Ball 게임 룰
- 4대 4 팀 대전
- 약 40개의 공(dodge ball)이 랜덤한 위치에 생성
- 플레이어는 공을 주어 상대 팀원을 n번 맞추어 모든 적 팀원을 제거하면 이김
- 
<p align ='center'>
    <img src = "../../assets/img/rl/ShorterElimination.gif" style="width: 70%"> <br/>
    <sub>DodgeBall gitlab(https://github.com/Unity-Technologies/ml-agents-dodgeball-env)</sub>
</p>

### 기존 모델 학습과 다른 점
**MA-POCA(Multi-Agent POsthumous Credit Assignment)**
<p align ='center'>
    <img src = "../../assets/img/rl/mapoca.png" style="width: 70%"> <br/>
    <sub>MA-POCA</sub>
</p>

- Multi Agent
  - 각 Agent가 아닌 전체 팀 상황에서 기대되는 보상을 판단
    - 팀 내의 Agent가 다수(1 ~ N개)
    - 따라서, 각 Agent에 대한 가치 판단 모델이 아니라 전체 상황에 대한 판단(가치 모델)을 공용(Centralized)으로 사용
    - 행동 모델은 각자의 정보를 이용하여 사용
  - 각 Agent의 기여도 판단(Counterfactual baseline)
    - 각 Agent 행동에 대한 학습을 할 때, 전체 팀에서 기대되는 보상에서 유저의 보상을 바탕으로 학습
      - Agent 기여도: 팀 전체 기대되는 보상 - 해당 Agent를 제외하고 상황/행동에 따른 현재 가치
- Post Credit
  - 각 Agent는 현재 상황/행동에서 기대되는 전체 팀 상황 보상이 최대가 되도록 학습
  - 따라서 Agent가 희생하는 행위 → 미래 기대되는 Group Reward를 높이는 행위를 학습하게 됨
  - 각 Agent가 동일 Episode 내에서 사라지는 시점이 다름

**모델 동작 방식**
- 가치 함수 V(s)와 q 함수 Q(s, a) 모두 학습
- 가치 함수 V(s)
  - 현재 상황을 바탕으로 팀의 기대되는 보상 계산
  - Self-attention을 이용해 각 Agent **Observation만 축소**
  - t시점 Input: 위 self-attention으로 나온 임베딩 / Output: 기대되는 팀 보상 
  - 기대되는 팀 보상과 실제 t시점 이후 Episode 내 discount된 reward와 V(s)값 차이가 최소가 되도록 학습
- q 함수 Q(s, a)
  - 특정 Agent를 제외하고 현재 상황/행동에 대해 팀의 기대되는 보상 계산
  - Agent j만 제외하고 Self-attention을 이용해 각 Agent **Observation/Action 축소**
  - t 시점 Input: 위 self-attention으로 나온 임베딩 / Output은 Agent j를 제외한 state/action에 대한 팀 보상
  - 기대되는 팀 보상과 V(s)값 차이가 최소가 되도록 함수
- 행동 모델 학습
  - Agent에 대한 기여도 계산 = 현재 상황에서 기대되는 팀 보상 - 특정 Agent를 제외하고 현재 상황/행동에 대해 팀의 기대되는 보상
  - advantage 기반으로 행동 학습

**Self Play**
- 대전 게임에서 Agent를 학습하는 방식으로 Agent끼리 매치해서 학습
- 학습 방식
  - 학습 팀과 대립하는 팀이 존재
  - 대전 데이터를 통해 학습 팀 모델을 학습 시키고, 일정 기간(save step)마다 학습된 모델을 저장
  - 일정 기간(team swap step)마다 저장한 최근 모델 중 일정 확률로 선택하여 대립하는 팀의 모델을 변경
- self play의 장점
  - 학습 팀의 상대가 너무 어렵지도 너무 쉽지도 않기 때문에 학습이 더 수월함
    - 어떤 행동을 해도 보상이 항상 크거나 작거나 하지 않음
- 학습 팀, 대립하는 팀이 같이 학습되기 때문에 reward보다 ELO를 통해 학습 경과를 확인 

### DodgeBall 환경 세팅

| Observation | Agent Ray Cast        | Ray Cast 별 태그된 대상이 맞았는 지 여부구분 태그우리 팀<br>상대 팀<br>상대 팀 앞면<br>우리 팀 앞면 |
|-------------|-----------------------|--------------------------------------------------------------------|
|             | Ball Ray Cast         | 구분 태그바닥에 떨어진 공<br>날아오는 공                                           |
|             | Wall Ray Cast         | 구분 태그벽<br>수풀                                                       |
|             | Back Ray Cast         | 구분 태그벽<br>수풀                                                       |
|             | Cool Down             | 공 던지기 쿨다운 시간                                                       |
|             | Ball One Hot(5개)      | 들고 있는 공 개수                                                         |
|             | Forward Velocity      | 앞으로 움직이는 속도(앞면 position x velocity)                                |
|             | Relative Position     | 전체 맵에서의 상대적 x, z값                                                  |
| Action      | Forward/Backward move | Agent 앞, 뒤로 움직임<br>값 범위: -1 ~ 1                                    |
|             | Left/Right Move       | Agent 왼쪽/오른쪽 움직임                                                   |
|             | Roatation move        | agent y축으로 회전                                                      |
|             | Shoot                 | 공 던지기                                                              |
|             | Dash                  | Dash                                                               |
| Reward      | Hit Reward(개인 보상)     | 공으로 상대팀을 맞췄을 때, +0.1                                               |
|             | Win Reward(팀 보상)      | 팀이 이겼을 때, +1                                                       |
|             | Count down reward     | 매 step마다 -1/max_step<br>빠르게 게임을 진행하도록 유도                           |

#### 협동 룰 추가
- Agent 그로기 상태 추가
  - 전체 체력 5에서 2가 되었을 때, 전투 불능 상태
  - 다른 팀원이 태깅하면 다시 전투 상태로 돌입
  - 부활에 따른 reward는 따로 부여하지 않음

<p align ='center'>
    <img src = "../../assets/img/rl/groggy.gif" style="width: 70%"> <br/>
    <sub>그로기 상태</sub>
</p>

-> 다음을 확인해보고자 함
- MA-POCA 모델에서의 팀 전체 가치 모델이 잘 동작하는 지 확인
  - 그로기 상태의 팀원을 부활시키는 행위가 팀 전체에 도움이 된다는 것이 학습되는지
- 기존 Behavior tree에서 분기로 추가되어야 하는 행동을 학습할 수 있을지 확인

### 학습 결과
기존 총 5억 step 학습 중 1억 5천 step 학습 후 중단
#### 개인 보상
<p align ='center'>
    <img src = "../../assets/img/rl/image-2024-2-18_22-1-34.png" style="width: 70%"> <br/>
    <sub>개인 보상</sub>
</p>

- 각 개별 보상 수준(상대팀 맞추었을 때 +1)은 초기에 급격하게 오르다 약 10M(천만) step부터 정체
- self play를 통해 양 팀 모두 전투를 학습해 맞추기로 이전보다 큰 보상을 얻기 어려워짐

#### 팀 보상
<p align ='center'>
    <img src = "../../assets/img/rl/image-2024-2-18_22-2-26.png" style="width: 70%"> <br/>
    <sub>팀 보상</sub>
</p>

- 평균 팀 보상(승리 시 + 5)는 꾸준히 증가 

#### ELO 변화
<p align ='center'>
    <img src = "../../assets/img/rl/image-2024-2-18_22-2-38.png" style="width: 70%"> <br/>
    <sub>팀 보상</sub>
</p>

- ELO 증가는 이전 학습 모델을 50% 이상 승률로 이기고 있다는 것을 알려줌

#### 팀원 부활 횟수
<p align ='center'>
    <img src = "../../assets/img/rl/image-2024-2-18_22-2-2.png" style="width: 70%"> <br/>
    <sub>팀원 부활 횟수</sub>
</p>

- 2만 step 별 총 팀원 부활 횟수
- 부활 행동에 따른 보상을 주지 않았음에도 팀원 부활을 학습하여 빈도가 더 높아짐

**팀원 부활 예시**
<p align ='center'>
    <img src = "../../assets/img/rl/dodgeball_heal2.gif" style="width: 70%"> <br/>
    <sub>팀원 부활</sub>
</p>

## 내용 정리
Unity 환경에서 강화학습 AI를 구성해보면서 느낀점
- 적절한 Action을 유도하려면 적절한 Observation이 필요
  - 맵에서 어떤 구간을 봐야하는 지 → grid map 정보 추가
  - Agent 주변 장애물을 피하고 다녀야함 → grid sensor 추가
- 상태, action 등을 새로 정의해도 보상을 높이도록 적절히 학습
  - 그로기 상태 / 팀원 부활 기능 추가 → 부활하도록 행동
- 다중 Agent나 대전 환경에서는 새로운 신경망 모델과 학습 방법이 중요
  - 팀 단위로 가치를 판단하는 가치 모델 추가해서 팀 보상이 최대가 되도록 학습
  - 현재 학습 모델과 이전 학습 모델을 사용하여 대전하고 학습 
