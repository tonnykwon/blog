---
title: "Factorization meets the Neighborhood Part2"
date: 2020-05-20
categories: Recommender
mathjax: true
tags: [recommendation, SVD++, Asymmetric-SVD, Neflix-Prize]
---



이전 부분에 이어 발전된 NM 모델과 LF 모델에 대해 정리해보았다.



## 3. A Neighborhood Model

여기서는 implicit, explicit feedback을 모두 활용한 neighborhood model을 제안한다. 단계 별로 모델의 구성 요소가 어떻게 이루어지는 지 보여준다. 이전에 소개한 기본 Neighborhood model은 다음과 같다.

$$\hat r_{ui} = b_{ui} + \frac{ \sum_{j \in S^k(i;u)} s_{ij}(r_{uj} - b_{uj}) }{\sum_{j \in S^k(i;u)} s_{ij}} $$

<br/>

여기서 조금 식을 다르게 써볼 수 있다.

$$ \hat r_{ui} = b_{ui} + \sum_{j \in R(u)} (r_{uj} - b_{uj})w_{ij} $$

이전 모델과 크게 다르지 않고, s관련 항을 $$w_{ij}$$로 대체했다. 여기서 중요한 것은 $$w_{ij}$$가 **각 유저에 관한 항이 아니라는 것**이다. $$S^k(i;u)$$를 통해 각 유저가 평가한 아이템을 바탕으로 아이템 간의 유사도($$s_{ij}$$)를 가중 평균하였다. 하지만 $$w_{ij}$$는 전체적인 아이템 간의 관계를 나타낸 것으로 특정 유저에 관한 가중치가 아니다. 아이템 i와 아이템 j가 비슷할 수록 w값은 높아진다.

또한, $$w_{ij}$$는 baseline 추측을 상쇄(offset)시킨다. 잔차, $$r_{uj}- b_{ij}$$,는 baseline과 유저의 평가가 어느 정도 다른 지를 나타내고 w의 가중치 역할을 한다. 즉, 유저 u가 아이템 j에 대한 평가가 일반적인 평가보다 높다고 했을 때, 아이템 i와 j가 비슷한 만큼 $$r_{ui}$$값이 커지게 된다. 

<br/>

여기서 implicit feedback을 사용하면 식을 다음과 같이 쓸 수 있다.

$$ \hat r_{ui} = b_{ui} + \sum_{j \in R(u)} (r_{uj} - b_{uj})w_{ij} + \sum_{j \in N(u)} c_{ij}$$

$$c_{ij}$$도 $$w_{ij}$$와 비슷하게 baseline 추측에 상쇄 작용을 한다. 유저가 시청하거나 검색한 implicit feedback $$N(u)$$ 중 해당 아이템 i와 j의 유사함을 포함한다.

이처럼 가중치를 각 유저에 관한 것이 아니라 전체적으로 사용했을 때, 평가가 없는 항목의 영향을 강조할 수 있다. 예를 들어, 영화 평가 데이터에서 `반지의 제왕3`을 재밌게 보고 긍정적으로 평가한 많은 유저 중  `반지의 제왕1-2` 또한 긍정적으로 평가했다면 `반지의 제왕3`과 `반지의 제왕1-2`의 가중치는 높을 것이다. 만약 한 유저가 `반지의 제왕1-2`를 평가하지 않았다면, 그 유저의 `반지의 제왕3`에 대한 예측된 평가는 두 영화의 높은 가중치를 가져가지 못하기 때문에 낮게 평가된다.

<br/>

단위 정규화를 위해 어느 정도의 shrinkage factor가 포함되어야 한다. 

$$ \hat r_{ui} = b_{ui} + \mid R(u) \mid^{-\frac{1}{2}} \sum_{j \in R(u)} (r_{uj} - b_{uj})w_{ij} \\
+\mid N(u) \mid^{-\frac{1}{2}} \sum_{j \in N(u)} c_{ij}$$

$$\mid R(u) \mid$$는 유저가 평가한 항목의 수다. 만약 -1/2 대신 0을 넣는다면 평가를 많이 한 사람이거나($$R(u)$$ 값이 큰 사람) 평가를 하지 않았지만 이력이 많은 사람이라면($$N(u)$$이 큰 사람) baseline에서 값이 크게 벗어나게 된다. 반대로 1을 사용하면 offset 항목들이 크게 영향을 가지지 못한다. 그렇기에 -1/2을 사용함으로써 평가 항목이 많은 사람들은 baseline보다 변칙적이고 덜 일반적인 추천을 하게 되고, 평가나 이력이 적은 사람이라면 baseline에 가까운 일반적인 추천을 하게 된다.

<br/>

유저가 평가하거나 이력이 있는 항목 중 예측하려는 아이템과 크게 유사한 아이템만을 사용함으로써, 모델의 복잡함을 줄일 수 있다.

$$ \hat r_{ui} = b_{ui} + \mid R^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in R^k(i;u)} (r_{uj} - b_{uj})w_{ij} \\
+\mid N^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in N^k(i;u)} c_{ij}$$



여기서 $$R^k(i;u) \overset{def}{=} R(u) \cap S^k(i) $$ 을 뜻한다. 예측할 아이템에 가장 유사한 아이템을 고려한다. k값이 무한에 가까울수록 평가한 모든 아이템을 고려한 이전 식과 같아지고, 반대로 적으면 적을수록 사용되는 변수를 줄일 수 있다. 유저 수가 n명이고 아이템이 m개일 때, 이전 식의 공간 복잡도는 $$O(n + m^2)$$라면 변경된 식은 $$O(n + mk)$$이다.

이를 통해 이전 NM에 비해 전체적인 구조를 파악할 수 있다. 마지막으로 loss function은 다음과 같다.

$$ \operatorname{\underset{b*, w*, c*}{min}} \sum_{(u, i) \in R} \hat r_{ui} - \mu -b_i - b_u \\
-\mid R^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in R^k(i;u)} (r_{uj} - b_{uj})w_{ij} -\mid N^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in N^k(i;u)} c_{ij} \\
+\lambda(b_u^2 + b_i^2 + \sum_{j \in R^k(i;u)} w_{ij}^2 + \sum_{j\in N^k(i;u)} c_{ij}^2)$$



Gradient descent 방법으로 파라미터 값을 업데이트하며 구할 수 있다. 여기서 $$e_{ui} = r_{ui}-\hat r_{ui}$$을 의미한다.





## Reference

- Koren, Y. (2008, August). Factorization meets the neighborhood: a multifaceted collaborative filtering model. In *Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining* (pp. 426-434).