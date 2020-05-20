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

파라미터 $$\mu$$는 전체 평균 평가를 의미한다. 파라미터 $$b_u, b_i$$는 평균 평가에서 유저 u와 아이템 i가 얼만큼 벗어나있는 지를 표현한다. 예를 들어, 유저 u가 일반적으로 영화를 높게 평가한다면 $$b_u$$는 양수일 것이고 반대로 아이템 i가 다른 일반적인 아이템에 비해 낮게 평가된다면 $$b_i$$는 음수일 것이다. 다음 표를 통해서 풀어보자면,

|        | item 1     | item 2 | item 3 |
| ------ | ---------- | ------ | ------ |
| user 1 | 2          |        | 1      |
| user 2 | $$r_{21}$$ | 4      | 3      |
| user 3 | 1          |        |        |



비어있는 값 $$r_{ui}$$를 예측해보자. 전체 평균 값은 $$\mu = \frac{(2+1+4+3+1)}{5}= 2.2 $$이다. 그렇다면 $$b_{u=2} = \frac{\sum_i^n b_{2i}}{n} -\mu = \frac{4+3}{2}-2.2 = 1.3 $$이고, $$b_{i=1} = \frac{\sum_u^n b_{u1}}{n} -\mu = \frac{2+1}{2}-2.2 = -0.7 $$이다. 유저2는 일반적인 평가보다 긍정적으로 평가를 하고, 아이템1은 일반적인 평가보다 부정적인 평가를 받았다.

그럼 $$\hat r_{21} = \mu + b_2 + b_1 = 2.2+1.3-0.7 = 2.8$$이다. 



실제 데이터는 더 크기 때문에 해당 방법처럼 하나씩 계산하지 않고 최소제곱법을 통해  $$b_u, b_i$$를 푼다.

$$ \operatorname {min}_{b*} \sum_{(u,i) \in K} (r_{ui} - \mu - b_u - b_i)^2 + \lambda_1(\sum_u b_u^2 + \sum_i b^2_i)$$

첫번째 항은 rating에 맞는 유저, 아이템의 bias를 찾기 위한 항이다. 두번째 항은 과적합을 방지하기 위해 파라미터 크기를 제한하기 위함이다.



### 2.2 Neighborhood Models

기본적인 neighborhood 모델은 유저 기반 방법이다.  유저 기반 방법은 빈 평가 항목을 다른 비슷한 유저를 기반으로 예측하는 방법이다. 이후 아이템 기반 방법이 확장성이나 더 나은 정확도 덕분에 더 자주 이용 되었다. 그 이유는 아무래도 유저의 수가 아이템보다 더 많기 때문에 아이템 기반 방법이 연산이 더 적어서이다. 또, 아이템 기반 모델은 유저가 아닌 각 아이템마다의 평점 분포를 통해 이루어지진다. 그렇기 때문에, 각 유저의 평가는 빠르게 바뀌는 거에 비해 아이템의 평점 분포는 비교적 안정적이다.

  아이템 기반 모델의 핵심은 아이템간의 유사성 측정이다. 대게 피어슨 상관계수를(Pearson correlation coefficient), $$p_{ij}$$를 사용한다.

$$p_{ij} = \frac{\sum_u(r_{ui} - \mu_i)(r_{uj} - \mu_j)}{\sqrt{\sum_u(r_{ui}-\mu_i)^2} \sqrt{\sum_u (r_{uj}-\mu_j)^2}}$$

아이템 ij의 피어슨 상관계수는 유저들이 아이템에 대한 평가가 어떻게 비슷한 지 파악한다. 즉, 상관계수가 양이면 i에 대한 평가를 좋게 하는 사람은 j에 대해 평가를 좋게 하고, i에 대한 평가가 나쁘면 j도 나쁘게 한다. 반대로 음수면 i에 대한 평가와 j에 대한 평가가 엇갈린다는 뜻이다. 그 외로 코사인 유사도 사용할 수 있다.

위 표를 바탕으로 아이템 1과 3의 피어슨 상관계수를 계산한다면,

$$ p_{13}  = \frac{(2-1.5)(1-2)}{ \sqrt{(2-1.5)^2}\sqrt{(1-2)^2} } = -1$$



아이템 1과 3을 모두 평가한 사람이 유저 1밖에 없기에 한 명의 유저만 사용되었다. 유저는 아이템 1 평가에 비해 아이템 3에 대한 평가가 나쁘다. 따라서, 피어슨 상관계수는 음수를 가지게 된다.

이처럼 평가 행렬 대부분이 비어있기 때문에 각 아이템에 대해 공통으로 평가한 유저가 많지 않다.











## Reference

- Koren, Y. (2008, August). Factorization meets the neighborhood: a multifaceted collaborative filtering model. In *Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining* (pp. 426-434).