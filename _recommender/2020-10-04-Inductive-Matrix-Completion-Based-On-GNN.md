---
title: "Inductive Matrix Completion Based ON Graph Neural Network"
date: 2020-10-04
categories: Recommender
mathjax: true
tags: [recommendation, Matrix-Factorization, GNN, matching]
---



## 개요

행렬 채우기(matrix completion)은 추천 시스템의 대표적인 방법이다. 유저와 아이템을 행렬의 각 행과 열로 나타낼 때, 유저가 아직 사용하지 않은 빈 아이템 평가를 예측하는 방법이다. 행렬 채우기에서 자주 쓰이는 방법 중 하나는 행렬 분해(matrix factorization)이다. 행렬 분해는 low-rank matrix를 가정하고, 행렬의 각 평가 $$r_{ij}$$를 유저 i와 아이템 j의 latent feature vector의 내적, $$w_i^Th_j$$로 분해하 나타낼 수 있다.

하지만 행렬 분해 방법은 transdutive하다.

transduction 방법은 데이터가 주어졌을 때 특정 값에서의 함수값을 얻는 것임에 반해 induction 방법은 해당 함수를 추정한다. 그렇기에 두 방법의 가장 큰 차이점은 induction은 predictive model을 반환하는 반면 transduction은 그렇지 않고 따라서 새로운 데이터 x가 왔을때, induction은 x에 대한 함수값을 금방 얻을 수 있는 반면 transductive한 알고리즘은 새로운 데이터에 대해 다시 학습해야한다.

기존 대부분의 matrix factorization 추천 방법들은 transductive하다. 특정 유저, 아이템간의 상호작용 행렬이 주어지면 해당 행렬을 더 낮은 차원의 latent embedding으로 분해함으로써 비어있는 값들을 예측한다. 하지만 새로운 유저나 아이템이 추가되었을 때는 행렬 분해를 새로 다시 학습해야한다.

해당 논문에서는 추가 side information을 사용하지 않고 graph neural network로 inductive한 matrix completion을 소개한다.





## Reference

Zhang, M., & Chen, Y. (2019). Inductive matrix completion based on graph neural networks. *arXiv preprint arXiv:1904.12058*.