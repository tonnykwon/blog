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

- $$b_u \leftarrow b_u + \gamma(e_{ui}- \lambda \cdot b_u)$$
- $$b_i \leftarrow b_i + \gamma(e_{ui}- \lambda \cdot b_i)$$
- $$ \forall j \in R^k(i;u): \\
  w_{ij} \leftarrow w_{ij} + \gamma(\mid R^k(i;u) \mid^{-\frac{1}{2}} e_{ui} \cdot (r_{uj}-b_{uj}) - \lambda \cdot w_{ij})$$
- $$ \forall j \in N^k(i;u): \\
  c_{ij} \leftarrow c_{ij} + \gamma(\mid N^k(i;u) \mid^{-\frac{1}{2}} e_{ui} - \lambda \cdot c_{ij})$$



## 4. Latent Factor Model

LF의 경우, 발전된 NM과 크게 다르지 않고 행렬 분해로 바꾸어 준다. global weights인 $$w_{ij}$$와 $$c_{ij}$$을 아이템 벡터와의 내적을 통해 나타낼 수 있다.

$$ w_{ij} = q_i^T x_i$$

$$ c_{ij} = q_i^T y_i$$

NM에서 사용된 식을 적용한다면 다음과 같다.

$$ \hat r_{ui} = b_{ui} + q_i^T \big( \mid R^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in R^k(i;u)} (r_{uj} - b_{uj})x_{ij} \\
+\mid N^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in N^k(i;u)} y_{ij}\big)$$



유저 벡터를 p를 대체하였기에 해당 방법을 `Asymmetric-SVD`라 부른다. ASVD를 통해 다음과 같은 효과를 노릴 수 있다.

- **적어진 파라미터 수**: 더 적은 파라미터를 사용함으로써 모델의 복잡도가 낮아진다.
- **새로운 유저:** SVD의 문제점 중 하나가 새로운 유저가 왔을 때, 다시 학습해야 해당 유저의 잠재 변수를 얻을 수 있다는 것이다. 하지만 여기서는 유저가 피드백을 남기면  재학습 없이 바로 유저 벡터를 아이템 벡터를 통해 구할 수 있다.
- **설명성:** 유저의 이전 평가 항목 중에서 어느 부분이 예측하는 평가에 크게 영향을 미치는 지 알 수 있다.
- **Implicit Feedback의 효율적 이용:** Implicit feedback을 활용해서 정확도를 더 높일 수 있다.



<br/>

아래 식은 `SVD++`로 파라미터 수가 적어지지 않았지만 Netflix data에서 Asymmetric SVD보다 높은 성능을 보였다.

$$ \hat r_{ui} = b_{ui} + q_i^T \big( p_u+\mid N^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in N^k(i;u)} y_{ij}\big)$$



## 5. An Integrated Model

NM과 LF를 합치면 다음과 같다.

$$ \hat r_{ui} = \mu + b_u + b_i + q_i^T \big( p_u+\mid N^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in N^k(i;u)} y_{ij}\big) \\ + \mid R^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in R^k(i;u)} (r_{uj} - b_{uj})w_{ij} \\
+\mid N^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in N^k(i;u)} c_{ij}$$

처음 $$\mu + b_u +b_i$$ 부분은 일반적인 유저와 아이템의 특성을 통해 추측한다.  예를 들어, 이 항은 식스센스가 일반적으로 호평을 받는 영화이고, 조는 보통 긍정적으로 영화를 평가하는 것을 반영할 수 있다.

두번째 항인 $$q_i^T \big( p_u+\mid N^k(i;u) \mid^{-\frac{1}{2}} \sum_{j \in N^k(i;u)} y_{ij}\big) $$은 유저 프로파일과 아이템 프로파일의 상호작용을 평가한다. 여기서는 식스센스와 조가 스릴러라는 잠재 변수에서 높은 값을 가진다는 것을 알 수 있다.

마지막 항에서는 프로파일에서 얻기 힘든 세세한 부분을 조정하는 데 기여한다. 조가 식스센스과 비슷한 영화 Signs를 낮게 평가했다는 사실이 반영된다.

파라미터 업데이트를 위한 gradient는 다음과 같다.

- $$b_u \leftarrow b_u + \gamma(e_{ui}- \lambda \cdot b_u)$$
- $$b_i \leftarrow b_i + \gamma(e_{ui}- \lambda \cdot b_i)$$
- $$ q_i \leftarrow q_{ij} + \gamma(e_{ui} \cdot(p_u + \mid N^k(i;u) \mid^{-\frac{1}{2}}\sum_{j \in N(u)}y_j - \lambda \cdot q_i)$$
- $$ p_u \leftarrow p_u + \gamma(e_{ui} \cdot q_i - \lambda \cdot p_u)$$
- $$ \forall j \in N(u): \\
  y_j \leftarrow y_j + \gamma(e_{ui} \mid N(u) \mid^{-\frac{1}{2}} q_i - \lambda \cdot y_j)$$
- $$ \forall j \in R^k(i;u): \\
  w_{ij} \leftarrow w_{ij} + \gamma(\mid R^k(i;u) \mid^{-\frac{1}{2}} e_{ui} \cdot (r_{uj}-b_{uj}) - \lambda \cdot w_{ij})$$
- $$ \forall j \in N^k(i;u): \\
  c_{ij} \leftarrow c_{ij} + \gamma(\mid N^k(i;u) \mid^{-\frac{1}{2}} e_{ui} - \lambda \cdot c_{ij})$$



## Reference

- Koren, Y. (2008, August). Factorization meets the neighborhood: a multifaceted collaborative filtering model. In *Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining* (pp. 426-434).