---
title: "AB Testing - p-value"
date: 2020-04-16
categories: statistics
mathjax: true
tags: [AB, hypothesis, p-value, z-score]
---



p-value는 가설 검증에 있어서 굉장히 중요한 개념이다. 



## Z-Score

데이터 내의 변동 정도를 나타내는 방법으로 분산을 사용한다. 분산이란 산술 평균과 각 데이터간의 차이를 제곱한 값의 합을 데이터 개수로 나눈 값이다.

$$ \sigma^2 = \frac{\Sigma(x - \mu)^2} {n}$$

제곱한 값이기 때문에 일반 데이터와 단위가 다를 수 있다. 따라서 여기에 제곱근을 씌운 표준편차를 쓸 수도 있다. 예를 들어, 데이터가 확률값으로 이루어져있다면 표준편차도 확률 단위로 나온다.

$$ \sigma = \sqrt{\frac{\Sigma(x-\mu)^2}{n}}$$



표준 편차의 값이 크면 클수록 데이터 내의 값들이 평균으로부터 흩어져 있다는 것을 의미하며 반대는 평균 주변으로 잘 모여있다는 것을 의미한다.



표준 편차를 이용하여 관찰된 데이터와 가설에 기반한 데이터의 차이를 나타낼 수 있다. d는 이 거리를 나타내는 **distance function**이라고 한다.

$$ d = \frac{(\bar X - \mu_0)}{\sigma_x}$$

이를 **표준 점수(standard score)** 혹은 **Z-Score**라 한다. 관찰된 데이터의 평균 $$\bar X$$와 기대값 $$\mu_0$$의 차이를 관찰된 데이터의 표준편차로 나누어준 값이다. 이를 통해 이를 통해 기댓값과 표준 평균이 어느 정도의 표준 편차만큼 다른 지 나타낼 수 있다.



### Why Z-Score?

중심극한정리(Central Limit Theorem)에 따르면 샘플의 평균은 원래 샘플의 분포와 상관없이 정규 분포를 따르는 경향이 있다. 그렇기 때문에 $$\bar X$$는 어떤 $$\mu$$와 $$\sigma$$를 가지는 정규 분포를 따른다고 볼 수 있다.



데이터 단위가 binomial일 경우에는(전환율이나 이탈율) 표준 편차를 다음과 같이 구할 수 있다.

$$ \sigma_{pop} = \sqrt {\frac{p(1-p)}{n}}$$

표준 편차와 다르게 **표준 오차(Standard Error)**는 샘플링된 데이터들의 평균의 표준 편차를 의미한다. 즉 샘플 데이터들의 평균이 얼마만큼 흩어져있는 지를 측정한다. 표준오차는 다음과 같다.

$$ \begin{array} d Var(\bar X) &= Var(\frac{1}{n}\Sigma_i^n X_i) \\
&= \frac{1}{n^2} \Sigma_i^n Var(X_i) \\
&= \frac{1}{n^2}n \sigma^2 \\
&= \frac{\sigma}{n} 
\end{array} $$

$$ SE(X) = \frac{\sigma_{pop}}{\sqrt{n}}$$





## p-value

**p-value**란 귀무 가설이 사실이라는 가정하에서 관측된 데이터만큼 혹은 그보다 극단적인 데이터가 생성될 확률을 말한다. 식은 다음과 같다.

$$p = P(d(X) \geq d(x_0); H_0)$$

여기서 d는 distance function이고 X는 랜덤 변수이고 $$x_0$$은 이 변수의 관측된 샘플이다. 그리고 세미콜론 ;은 조건을 의미한다. 즉 $$H_0$$이 사실일 때, 랜덤 변수의 거리 값이 관측된 변수의 거리 값보다 더 클 확률을 의미한다.

이전에 사용했던 예를 통해 p-value에 대해 알아보자. "A제품이 B제품 보다 구매율이 높다"라는 대립가설과 이에 반대되는 "A제품의 구매율과 B제품의 구매율은 차이가 없거나 B제품 구매율이 더 높다" 귀무가설이 있다. 여기서 p-value가 0.01로 나온다면, A제품과 B제품의 구매율이 차이가 없거나 B제품 구매율이 더 높다는 상황에서 관측된 데이터와 같은 값이 나올 확률이 1프로라는 것을 의미한다.

안타깝게도 p-value는 대립 가설이 맞을 확률, 귀무 가설이 잘못될 확률 등 가설에 대한 직접적인 의미를 가지지는 못한다. 말그대로 귀무 가설이 사실일 때, 해당 데이터가 관측될 수 있을 확률을 의미한다.

그렇다는 것은 귀무 가설 상황에서 해당 관측된 데이터를 보는 게 흔치 않은 일을 의미한다. 그렇기 때문에 p-value가 일정 수준 이하로 나타났을 때, 우리는 귀무 가설을 기각한다.

그렇다면 그 일정 수준은 어떻게 정할 수 있을까? 일정 수준을 **유의 수준(Significance Level)** 혹은 **alpha**라고 부른다. 일반적으로 0.05가 통용되지만 분야에 따라 달리 사용할 수 있다. 만약 의료와 같이 중요한 거라면 더 낮은 0.0001을 사용할 수도 있고 그렇지 않다면 높은 유의 수준을 사용할 수 있다.

여기서 낮은 p-value는 다음과 같은 상황에서 일어날 수 있다.

- 귀무 가설이 거짓일 때
- 귀무 가설이 거짓이 아니지만, 매우 흔치않은 데이터가 관측되었을 때
- 통계적 모델이 잘못 설정되었고, 실제 p-value와 계산된 p-value가 다를 때



여기서 두번째의 경우를 **false positive** 혹은 **Type 1 Error**라고 한다. 즉, 사실인 귀무 가설을 사실이 아니라고 주장하는 오류를 범한 것을 뜻한다. 그런 의미에서 유의 수준은 Type 1 Error를 허용할 수 있는 상한선을 의미한다. 



## Confidence Interval

**신뢰 구간(Confidence Interval)**이란 샘플링을 반복했을 때, 특정 비율만큼 해당 모수가 속하는 구간을 나타낸다. 말이 어렵다. 예를 들어, A 후보자의 지지율을 알고 싶다고 하자. 무작위로 500명에게 지지 여부를 조사했고 그 중 260명이 지지한다고 나왔다. 그렇다면 실제 지지율은 얼마일까?

실제 지지율을 어느 범위에 속하는 지 신뢰 구간을 통해 구간 추정을 해볼 수 있다. 신뢰 구간 식은 다음과 같다.

$$CI = P \pm ME$$

해당 파라미터 $$P$$에 **허용 오차(Margin of Error)**를 더하고 뺀 구간이다. 그렇다면 허용 오차란 무엇인가? 이전에 구했던 표준 오차와 관련이 있다.





## p-value Misinterpretation

높은 p-value는 null hypothesis를 뒷받침하는 근거가 된다?

통계적 유의성(statistical significance)과 실질적 유의성(practical significance)의 차이



## Reference

- Georgiev, G. (2019). Statistical Methods in Online A/B Testing.