---
title: "Factorization meets the Neighborhood"
date: 2020-05-14
categories: Recommender
mathjax: true
tags: [recommendation, SVD++, Asymmetric-SVD, Neflix-Prize]
---



Factorization Meets the Neighborhood: a Multifaceted Collaborative Filtering Model(2008) 논문을 읽고 내용을 정리해봤다. 모델 평가의 어려움에 관한 논문에서 SVD++ 관련된 기술이 언급돼서 읽게 된 논문이다.

해당 논문에서는 Explicit data를 사용한 모델들에서 Implicit data를 추가적으로 활용하는 방안과 행렬 분해와 Neighborhood model을 합치는 방법을 제안한다. 사용자의 평가를 어떤 식으로 예상할 것인지 loss function에 반영하는 것이 인상 깊었다.



## 1. Introduction

많은 추천 시스템들이 협업 필터링(Collaborative Filtering) 방법을 사용한다. 그 이유 중 하나는 CF는 도메인 지식이 필요하지 않고, 많은 데이터를 수집할 필요가 없어서이다. 또, 유저 행동 데이터를 이용하기에 예상치 못한 복잡한 패턴을 찾아낼 수 있다. 

CF 모델은 크게 두 가지 방법으로 이루어 진다; Neighborhood approach와 latent factor model이다.

**Neighborhood** 방법은 아이템 간의 혹은 유저 간의 관계를 계산하는 데 초점이 맞추어져 있다. 예를 들어, 아이템 관계 기반 방법은 한 유저의 어느 아이템에 대한 선호도를 해당 아이템과 비슷한 아이템들의 유저 평가를 보고 유추할 수 있다.

**Latent factor model**은 Singular Value Decomposition(SVD)과 같이 아이템과 유저를 다른 잠재 공간으로 변환시키는 방법이다. 잠재 유저-벡터 공간을 통해 유저 피드백에서 유저와 아이템의 특성을 추출할 수 있다. 영화를 예로 들자면, 유저의 영화 평점을 잠재 공간으로 변형시켰을때, 잠재 변수들은 영화의 장르인 '호러', '드라마'를 나타낼 수도 있고, 혹은 유아용 영화인지를 나타낼 수도 있다. 아니면 해석이 불가능한 차원을 가질 수도 있다.

따라서 Neighborhood는 좀 더 국지적인 관계를 잘 발견하지만 그 외의 데이터를 고려하지 않는다. 반대로, Factor model은 전체적인 구조를 잘 이해하지만 작은 세트의 아이템끼리의 강한 연결은 발견하지 못한다.

그렇기 때문에 해당 논문에서는 Neighborhood모델과 Factor모델을 결합하여 이러한 장점을 결합하여 모델의 성능을 높이려고 한다.

또 다른 점은 implicit feedback과 explicit feedback 모두 활용한다는 것이다(둘의 차이점은  <a href = '../2020-01-08-Recommendation.md'>여기에</a> 좀 더 자세히 적어놨다). Explicit 데이터가 더 중요한 정보를 담고 있다. 부정적, 긍정적 피드백 여부도 확실하고 노이즈가 없다. 하지만 대체로 explicit 데이터가 없고 impicit 데이터가 있는 경우가 많다. TV 프로그램 시청 이력이나 웹사이트 방문 기록, 상품 구매 이력이 그러하다. 아무래도 Explicit 데이터의 경우 사용자로부터의 액션을 요구하기에 구하기가 쉽지 않다. 

또한, implicit 데이터 역시 중요한 정보를 담고 있다고 얘기한다. 해당 논문에서는 explicit feedback에서 얻을 수 없는 사용자 정보를 얻을 수 있다고 한다.



## 2. Preliminaries

### 2.1 Baseline Estimates

협업 필터링(CF)에서 대부분의 데이터는 비어있다. 상품의 수는 엄청나지만 그에 비해 유저가 평가하는 항목 그리 많지가 않다. 따라서, CF에서는 이러한 빈 항목들을 추측한다.

빈 부분을 예측하는 가장 기본적인 방법은 유저의 bias, 아이템의 bias을 사용하는 것이다. 예측 방법은 다음과 같다.

$$b_{ui} = \mu + b_u + b_i$$

파라미터 $$b_u, b_i$$는 평균 평가에서 유저 u와 아이템 i가 얼만큼 벗어나있는 지를 표현한다. 예를 들어, 유저 u가 일반적으로 영화를 높게 평가한다면 $$b_u$$는 양수일 것이고 반대로 아이템 i가 다른 일반적인 아이템에 비해 낮게 평가된다면 $$b_i$$는 음수일 것이다. 다음 표를 통해서 풀어보자면,

|        | item 1     | item 2 | item 3 |
| ------ | ---------- | ------ | ------ |
| user 1 | 2          |        | 1      |
| user 2 | $$r_{21}$$ | 4      | 3      |
| user 3 | 1          |        |        |





<p align ='center'>
    <img src = "../../assets/img/recommender/difficulty-ml10m.png" style="width: 60%"> <br/>
    <sub>모델 성능 평가(ML10M)</sub>
</p>








## Reference

- Koren, Y. (2008, August). Factorization meets the neighborhood: a multifaceted collaborative filtering model. In *Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining* (pp. 426-434).