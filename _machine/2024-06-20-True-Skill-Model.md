---

title: "TrueSkill 모델 (1)"
date: 2024-06-20
categories: Machine
mathjax: true
tags: [machine-learning, metric, binary, skill-rating]
---


## 목표
- Trueskill 공부하면서 이해한 내용 정리
- [논문](https://www.microsoft.com/en-us/research/uploads/prod/2018/03/trueskill2.pdf)에 나오는 계산식을 추론하는 데 초점
 

## 모델링
- Trueskill은 factor graph로 표현된다. factor graph를 사용함으로써 추론 과정이 더 효율적
  - 그 이유는 factor graph는 directed acylic graph(dag)와 다르게 최종 추론하려는 확률 분포 계산 과정에서 중간 계산값(message)을 저장하기 때문
  - 이러한 중간값을 반복적으로 사용하는 trueskill 모델에서 효율적이다.

## Factor graph(인자 그래프)
- 확률 분포를 도식적으로 표현하는 방법을 확률적 그래프 모델이라 함
- 각 확률 그래프 모델에서 노드는 확률 변수를 나타내고, 링크는 변수들 간의 관계를 표현
- 링크들이 방향성을 가지면 베이지안 네트워크, 그렇지 않으면 마르코프 무작위장
- 인자 그래프는 방향성 그래프, 비방향성 그래프를 모두 표현할 수 있음
- 베이지안, 마르코프 대신 쓰는 이유는 그래프의 지역적인 특성을 이용하여 marginalization에 더 효율적

- 확률 분포를 factor graph로 표현했을 때 각 factor는 변수들의 부분 집합에 대한 분포이다.
- 변수의 결합 분포는 인자들의 곱으로 표현될 수 있다(정규화가 불필요한 경우)
- $$x_s$$는 변수들의 부분 집합을 지칭

### Factor graph 예시
- $$(v, w, x, y, z)$$ 의 결합 확률 분포는 아래 인자들의 곱으로 표현됨
  $$  p(v, w, x, y, z) = p(v)p(w)p(x|v, w)p(y|w)p(z|x)$$
- 위 인수분해에 해당하는 factor graph:

<p align ='center'>
    <img src = "../../assets/img/machine/factor-graph.png" style="width: 70%"> <br/>
    <sub>Factor Graph</sub>
</p>

- 각 노드는 확률 변수, 인자(factor)는 관련 확률 변수들의 확률 분포를 의미.
- 만약 $$p(v)$$ 주변 확률을 구한다고 하면, 모든 인자의 곱에서 변수 $$w$$를 제외하여 marginalization을 진행해야 함
- 위 모든 변수가 이산형이고 $$K$$개의 값을 가질 수 있다고 하면, 각 $$w$$ 값에 대해 약 $$O(K^5)$$의 곱셈과, $$O(K^4)$$의 덧셈을 필요로 함
- 따라서, 계산 복잡도가 $$O(K^5)$$로 굉장히 높음
  → 인수 분해를 통해 더 효율적으로 진행할 수 있음(summation을 해당되는 인자끼리만 먼저 계산)

<p align ='center'>
    <img src = "../../assets/img/machine/factor-graph-2.png" style="width: 70%"> <br/>
    <sub>Factor Graph 2</sub>
</p>

- $$v$$를 포함하는 노드를 따로 그룹지어 계산

<p align ='center'>
    <img src = "../../assets/img/machine/factor-graph-message.png.png" style="width: 70%"> <br/>
    <sub>Factor Graph Message</sub>
</p>

- 이를 factor에서 노드 $$w$$로 가는 메시지로 표현
- 최종적으로는 다음과 같음

<p align ='center'>
    <img src = "../../assets/img/machine/factor-graph-message-2.png.png" style="width: 70%"> <br/>
    <sub>Factor Graph Message</sub>
</p>

## Belief Propagation(Sum-Product algorithm 특별 케이스)
- 위처럼 factor끼리 message를 주고 받으면서 연산하는 방법을 belief propagation이라 함. 좀 더 일반적인 식으로 표현했을 때, 
- 변수의 주변 확률은 해당 변수로 오는 모든 메시지의 product
  $$
  b(t) \propto \prod_{f \in N(t)} m_{f \to t} \quad (1)
  $$
  $$N(t)$$는 $$t$$ 변수와 연결되어 있는 모든 factor를 의미
- factor $$f$$에서 변수 $$t$$로 가는 메시지는 다음과 같음
  $$
  m_{f \to t}(t) = \sum_{\{x_i\}} f(\{x_i\}) \prod_{s \in N(f) \setminus t} m_{s \to f}(s) \quad (2)
  $$
  변수 $$t$$에서 오는 메시지를 제외한 factor $$f$$로 오는 모든 메시지와 해당 factor $$f$$ 함수의 곱을 $$x_i$$에 대해 sum out
- 변수 $$t$$에서 factor $$f$$로 가는 메시지
  $$
  m_{t \to f}(t) = \prod_{g \in N(t) \setminus f} m_{g \to t}(t) \quad (3)
  $$
  factor $$f$$에서 오는 메시지를 제외한 변수 $$t$$로 오는 모든 메시지의 곱
- 변수 $$t$$의 주변 확률 $$b(t)$$는 $$t$$로 오는 모든 메시지의 곱이므로 여기서 factor $$f_1$$에서 오는 메시지만 제외하면 변수 $$t \to f_1$$ 메시지와 동일함

## 기본 Trueskill 가정
- 각 유저는 실력인 skill을 가우시안을 따르는 확률 변수로 나타냄
  - 기대 실력값은 $$\mu$$, 실력에 대한 불확실성은 $$\sigma$$로 표현
  - $$\tau$$는 유저의 실력 분산이 어느 정도 이하로 떨어지는 걸 방지
  - 더 높은 skill값을 가진 유저가 주로 매치를 이기지만 항상 그렇지는 않음
- 각 유저는 매 게임마다 다른 performance를 보이고 이 performance의 평균값은 유저의 skill 실력을 나타냄.
  - performance 분포의 표준 분포는 유저마다 같음
  - 승패를 결정하는 데 실력 외에 다른 요소가 많을수록 performance의 표준편차 $$\beta\$$ 값이 커짐
- 유저 중 더 높은 performance를 가진 유저가 매치에서 이김
  - 팀 게임일 경우, 팀 performance는 각 팀원 performance의 합
    - 각 팀원에게 가중치를 두어 기여도를 조정할 수 있음
    - performance 차이가 일정 차이 $$\tau$$ 이상을 넘지 못하면 무승부가 됨

$$\tau$$, $$\beta$$, $$\eps$$는 모델 하이퍼파라미터로 미리 설정해야 함

## 좀 더 간단한 모델

- 각 유저는 실력인 skill을 가우시안을 따르는 확률 변수로 나타냄
  - 기대 실력값은 $$\mu$$, 실력에 대한 불확실성은 $$\sigma$$로 표현
  - ~~$$\tau$$는 유저의 실력 분산이 어느 정도 이하로 떨어지는 걸 방지~~
  - 더 높은 skill값을 가진 유저가 주로 매치를 이기지만 항상 그렇지는 않음
- 각 유저는 매 게임마다 다른 performance를 보이고 이 performance의 평균값은 유저의 skill 실력을 나타냄.
  - performance 분포의 표준 분포는 유저마다 같음
  - 승패를 결정하는 데 실력 외에 다른 요소가 많을수록 performance의 표준편차 $$\beta\$$ 값이 커짐
- 유저 중 더 높은 performance를 가진 유저가 매치에서 이김
  - ~~팀 게임일 경우, 팀 performance는 각 팀원 performance의 합~~
    - ~~각 팀원에게 가중치를 두어 기여도를 조정할 수 있음~~
    - ~~performance 차이가 일정 차이 $$\tau$$ 이상을 넘지 못하면 무승부가 됨~~


각 유저의 skill
$$
s_i \sim N(\mu_i, \sigma_i^2)
$$

해당 매치에서의 유저 performance
$$
  p_i \sim N(s_i, \beta^2)
$$

두 유저의 퍼포먼스 차이
$$
  \Delta_{ij} = p_i - p_j
$$

유저 퍼포먼스 차이에 따른 관측된 결과값(\(i\)가 이겼을 경우)
$$
  o_{ij} = 1 \quad \text{if} \quad \Delta_{ij} > 0
$$

해당 모델의 factor graph

<p align ='center'>
    <img src = "../../assets/img/machine/ts-factor-graph.png.png.png" style="width: 70%"> <br/>
    <sub>Factor Graph Message</sub>
</p>
