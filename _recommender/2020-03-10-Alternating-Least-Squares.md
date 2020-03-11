---
title: "Recommendation System"
date: 2020-01-08
categories: Recommender
mathjax: true
---



Implicit dataset을 위한 행렬 분해 방법을 다루어 볼 것이다. 그러기 전에 Implicit dataset의 중요한 특성을 보고 갈 필요가 있다.

1. 부정적인 피드백이 존재하지 않는다.

유저가 어떤 아이템을 사용했는 지에 대한 정보만 있고 그 아이템에 불만족스러웠는 지는 알 수가 없다. 예를 들어, 넷플릭스를 생각해보자. 내가 시청하지 않고 지나간 프로그램들을 회사에서는 내가 싫어서 시청을 안한 것인지 아니면 잘 몰라서 지나친 것인지 알 수가 없다. 따라서 이렇게 비어있는 데이터를 어떤 방법으로 채우느냐가 중요하다.



2. 노이즈가 많다.

유저의 이력을 바탕으로 사용자의 선호도나 동기를 정확히 추측하기 어렵다. 구매 이력 같은 경우, 유저가 선물을 위해 상품을 샀을 수도 있고, 실제 산 상품에 대해 실망했을 수도 있다.



3. Explicit 피드백은 선호도를 나타내지만 implict 피드백은 확실한 정도를 나타낸다.

Explicit data는 바로 유저가 제품을 얼만큼 선호하는 지 나타낸다. 1이라면 불만족한 거고 5라면 만족했다는 거다. 하지만 implicit 데이터는 이를 알려주기 어렵다. 유저가 제일 선호하는 프로그램은 어느 영화이지만 영화이기에 보통 몇 번 시청하지 않는다. 반대로 티비 시리즈라면 조금만 좋아하더라도 많이 볼 수밖에 없다. 따라서 implicit 데이터는 선호도라기보다는 데이터에 대한 확실하는 정도를 나타낸다.



4. 평가가 어렵다.

점수 평가 예측에 대해서는 mean squared error 등 간단하게 에러를 표현할 수 있다. 하지만 티비 프로그램을 시청한 횟수라든지 동시에 시청할 수 없는 두 티비 쇼와 같은 경우 평가 기준을 세우기가 어렵다.



## Neighborhood Models

유저의 아이템 사용 이력을 바탕으로 추천하는 협업 필터링 중 대표적인 방법은 Neighborhood models이다. 유저의 알려지지 않는 아이템에 대한 평가를 비슷한 유저의 평가로 나타내는 것이다. 아이템 간의 관계를 이용한 평가 예측은 다음과 같다.

$$ \hat r_{ui} = \frac{\sum_{j \in S^k(i;u)} s_{ij} r_{uj}}{\sum_{j \in S^k(i;u)} s_{ij}} $$

유저 u가 아이템 i에 대한 평가 예측을 $$\hat r_{ui}$$, $$s_{ij}$$를 아이템i와 j의 유사도 그리고 $$S^k{i;u}$$는 유저 u가 평가한 아이템 중 i와 가장 유사한 k개 아이템이다.

유저 u가 평가한 i와 가장 유사한 아이템 k개의 각각 유사도를 가중치로 두고 각각 j에 대한 평가로 가중치 평균을 구한다.



이러한 아이템의 관계를 이용한 평가 예측은 implicit dataset에서는 한계가 있다. 유저의 선호도에 대한 확신이 반영되지 않다는 것이다. 간접적인 평가 데이터이기에 단순히 이력으로만 다른 평가를 예측하기에는 무리가 있을 수 있다.



## Matrix Factorization

다른 대표적인 방법은 Latent Factor Model이다. Latent factor model은 평가를 설명할 수 있는 다른 차원의 features를 찾는 방법이다. pLSA, 인공신경망, Latent Dirichlet Allocation 등 다양한 방법이 있지만 행렬 분해도 그 중 하나이다. 행렬 분해란 여러 구조를 가진 행렬의 곱으로 기존의 행렬을 나타내는 것을 말한다. 이런 방법이 추천 시스템에서 유용한 이유는 기존 유저와 아이템간의 상호작용을 더 낮은 차원의 latent factor를 나타낼 수 있기 때문이다.

기본적으로 모델은 유저의 latent factor 벡터인 x와 아이템의 latent factor 벡터인 y로 이루어진다. 이 두 벡터의 내적이 해당 평가에 대한 예측이 된다($$ \hat r_{ui} = x_u^T y_i$$).

과적합을 막기 위해 정규화를 사용한 모델의 목적 함수는 다음과 같다.

$$ \underset {x*, y*} {\min} \underset {r_{u,i} \text{is known}} \sum (r_{ui} - x_u^T y_i)^2 + \lambda(\|x_u\|^2 + \| y_i \| ^2) $$



$$\lambda$$는 정규화를 위한 하이퍼 파라미터로 각 절대값의 제곱 합이 너무 크지 않도록 제한해준다.



## Alternating Least Squares

Neighborhood model의 단점으로 유저 평가 $$ r_{ui}$$에 대한 확신이 부족하다고 위에서 언급했다. 이를 해결하기 위해 유저 u가 아이템 i에 대한 선호도를 나타내는 이진 변수 $$p_{ui}$$를 사용한다.

$$ p_{ui} = \cases{ 1 & r_{ui} > 0 \cr 0 & r_{ui}= 0}$$

만약 유저가 아이템을 사용했다면 ($$r_{ui} > 0$$) p값은 1이고 그렇지 않다면 선호가 없으므로 0이다. 

Implicit data 특성 상 선호도를 확실히 알기 힘드므로 선호도에 대한 확신 정도를 나타내는 $$c_{ui}$$ 변수를 사용한다. 이 변수는 $$p_{ui}$$를 확신하는 만큼을 나타낸다.

$$c_{ui} = 1+ \alpha r_{ui}$$



모든 user-item의 기본적인 최소 확신도를 정해놓고 선호에 대한 더 많은 데이터가 생기면 confidence를 높여가는 것이다. 보통 $$\alpha$$는 40으로 세팅해서 사용한다.



해당 confidence variable 적용하면 목적 함수를 새로 작성할 수 있다.

$$ \underset {x*, y*} {\min} \underset {u,i} \sum c_{ui}(p_{ui} - x_u^T y_i)^2 + \lambda(\sum_u \|x_u\|^2 + \sum_i\| y_i \| ^2) $$









## Refernce

- Hu, Y., Koren, Y., & Volinsky, C. (2008, December). Collaborative filtering for implicit feedback datasets. In *2008 Eighth IEEE International Conference on Data Mining* (pp. 263-272). Ieee.