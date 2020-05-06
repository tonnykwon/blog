---
title: "모델 평가의 어려움"
date: 2020-04-30
categories: Recommender
mathjax: true
tags: [recommendation, evaluation]
---



On the Difficulty of Evaluation Baselines: A Study on Recommender Systems라는 논문을 읽어 정리해보았다.



해당 논문은 추천 시스템 모델 성능 비교시 기준이 되는 모델(Baseline Model)을 결정하고 평가하는 것이 어렵고,  많은 연구에서 이를 간과한다고 한다. 이러한 어려움을 어떻게 극복할 지에 대해 제안한다.



### Movielens

영화 평점 데이터 Movielens 10M은 추천 시스템에서 많이 사용되는 데이터 중 하나이다. 학습, 테스트 데이터를 90:10으로 나눈 뒤에 Root Mean Squared Error(RMSE)를 바탕으로 모델을 평가하는 것이 일반적이다. 평가된 모델들을 보면 다음과 같다.

<p align ='center'>
    <img src = "../../assets/img/recommender/difficulty-ml10m.png" style="width: 60%"> <br/>
    <sub>모델 성능 평가(ML10M)</sub>
</p>


비교적 이전에 나왔던 모델들; Randomized Singluar Value Decomposition(RSVD), Biased Matrix Factorization(Biased MF), Bayesian Probalistic Matrix Factorization(BPMF) 등이 새로 나온 모델에 비해 성능이 낮다. 하지만 논문에서는 잘못된 setup에서 학습한 것이 문제라고 지적한다. 기본적인 baseline 모델들은 다음과 같다.

- Biased MF와 RSVD는 같은 방법으로 L2 정규화된 행렬 분해를 SGD 방법으로 학습하는 것이다. 하이퍼파라미터나 학습 데이터 순서, 혹은 구현과 같은 setup에 따라 다음과 같이 RMSE 성능이 매우 향상되었다
- Alternating Least Sqaures with Weighted-lambda-Regularization(ALS-WR)과 Biased-MF, RSVD는 다른 방법으로 학습되는 같은 방법이다. Netflix Prize 데이터에 있어서 모두 비슷한 성능을 보인다.
- BPMF 또한 RSVD/ALS-WR와 모델은 같은나 Gibbs sampler로 학습된다. 다른 모델에 비해 Netflix Prize에서 가장 좋은 성능을 보였다.



해당 모델들의 embedding 차원을 변경하거나 bag-of-words predictor 변수를 추가하는 등의 방식으로 비교적 새로 나온 모델들인 Local Low-Rank Matrix Approximation(LLORMA), Autoencoders Meet Collaborative Filtering(Autorec), Weighted and Ensemble Matrix Approximation(WEMAREC), I-CFN++에 비해 좋은 성능을 보였다. 

<p align ='center'>
    <img src = "../../assets/img/recommender/difficulty-improved-ml10m.png" style="width: 60%"> <br/>
    <sub>향상된 모델 성능(ML10M)</sub>
</p>



## Netflix Prize

Netflix Prize 데이터 역시 추천 시스템에서 자주 쓰이는 프로그램 평점 데이터이다. 학습용 데이터, 검증을 위한 probe 데이터, 그리고 테스트를 위한 qualifying 데이터로 이루어져있다.

결측치를 무시하는 FunkSVD, implicity feedback을 추가한 SVD++, 거기에 time 변수르 추가한 timeSVD++ 등 기본적인 matrix factorization 모델들이 매우 높은 성능을 보였다.



## 결론

 통계적 유의성, 재생산성, 튜닝된 하이퍼파라미터 외에 standardized benchmarks와 기존 baseline 모델을 향상시키는 데에 보상을 줌으로써 더 신뢰할만한 결과를 얻을 수 있다고 본다.

Standardized benchmarks란 표준 테스트로 해당 데이터로 모델을 평가할 때 어떤 식으로 학습, 검증용 데이터를 나누고, 성능 지표는 무엇을 쓰고, 또 데이터는 어떤 전처리 과정을 거치는 지에 대한 일정한 과정을 의미한다. 이를 통해 새로 나온 모델 혹은 이전 모델들이 같은 환경에서 검증받을 수 있다.

Baseline 모델들을 다시 향상시킴으로써 실제 새로 제안된 모델이 정말 좋은 모델인지 확실하게 평가할 수 있다. 보통 학술적인 연구는 얼마나 결과가 새로운 지에 초점이 맞추어져 있다. 따라서, 이전 모델들을 재학습하는 것에 매우 소홀한 편이다. 하지만 위 결과들이 보여주듯이 잘 교정된 모델들은 이전 연구 결과보다 더 좋은 성능을 보여주었다. 따라서, Netflix Prize나 Kaggle처럼 이전 모델들을 다시 교정함으로써 더 신뢰할만한 결과를 얻을 수 있다고 한다.





## Reference

- Rendle, S., Zhang, L., & Koren, Y. (2019). On the difficulty of evaluating baselines: A study on recommender systems. *arXiv preprint arXiv:1905.01395*.