---
title: "Recommendation System"
date: 2020-01-08
categories: Recommender
mathjax: true
---





유저의 아이템 사용 이력을 바탕으로 추천하는 협업 필터링 중 대표적인 방법은 Neighborhood models이다. 유저의 알려지지 않는 아이템에 대한 평가를 비슷한 유저의 평가로 나타내는 것이다. 아이템 간의 관계를 이용한 평가 예측은 다음과 같다.

$$ \hat r_{ui} = \frac{\sum_{j \in S^k(i;u)} s_{ij} r_{uj}}{\sum_{j \in S^k(i;u)} s_{ij}} $$

유저 u가 아이템 i에 대한 평가 예측을 $$\hat r_{ui}$$, $$s_{ij}$$를 아이템i와 j의 유사도 그리고 $$S^k{i;u}$$는 유저 u가 평가한 아이템 중 i와 가장 유사한 k개 아이템이다.

유저 u가 평가한 i와 가장 유사한 아이템 k개의 각각 유사도를 가중치로 두고 각각 j에 대한 평가로 가중치 평균을 구한다.



이러한 아이템의 관계를 이용한 평가 예측은 implicit dataset에서는 한계가 있다. 유저의 선호도에 대한 확신이 반영되지 않다는 것이다. 간접적인 평가 데이터이기에 단순히 이력으로만 다른 평가를 예측하기에는 무리가 있을 수 있다.



## Matrix Factorization

다른 대표적인 방법은 Latent Factor Model이다. Latent factor model은 평가를 설명할 수 있는 다른 차원의 features를 찾는 방법이다. pLSA, 인공신경망, Latent Dirichlet Allocation 등 다양한 방법이 있지만 행렬 분해도 그 중 하나이다. 행렬 분해란 여러 구조를 가진 행렬의 곱으로 기존의 행렬을 나타내는 것을 말한다. 이런 방법이 추천 시스템에서 유용한 이유는 기존 유저와 아이템간의 상호작용을 더 낮은 차원의 latent factor를 나타낼 수 있기 때문이다.

기본적으로 모델은 유저의 latent factor 벡터인 x와 아이템의 latent factor 벡터인 y로 이루어진다. 이 두 벡터의 내적이 해당 평가에 대한 예측이 된다($$ \hat r_{ui} = x_u^T y_i$$).

과적합을 막기 위해 정규화를 사용한 모델의 목적 함수는 다음과 같다.

$$ \underset {x*, y*} {\min} \underset{{r_{u,i} \text{is known}}}\sum (r_{ui} - x_u^T y_i)^2 + \lambda(\|x_u\|^2 + \| y_i \| ^2) $$

$$\lambda$$는 정규화를 위한 하이퍼 파라미터로 각 절대값의 제곱 합이 너무 크지 않도록 제한해준다.



## Alternating Least Squares