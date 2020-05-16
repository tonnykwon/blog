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

Neighborhood 방법은 아이템 간의 혹은 유저 간의 관계를 계산하는 데 초점이 맞추어져 있다. 예를 들어, 아이템 관계 기반 방법은 한 유저의 어느 아이템에 대한 선호도를 

<p align ='center'>
    <img src = "../../assets/img/recommender/difficulty-ml10m.png" style="width: 60%"> <br/>
    <sub>모델 성능 평가(ML10M)</sub>
</p>








## Reference

- Koren, Y. (2008, August). Factorization meets the neighborhood: a multifaceted collaborative filtering model. In *Proceedings of the 14th ACM SIGKDD international conference on Knowledge discovery and data mining* (pp. 426-434).