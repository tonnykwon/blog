---

title: "Logistic Regression"
date: 2020-04-03
categories: Machine
mathjax: true
tags: [machine-learning, logistic, linear]
---



분류 방법 중에서 일반적이고 간단한 logistic regression에 대해 정리해보았다.



### Log-Odds

odds(승산)이란 어떤 사건이 일어날 확률을 일어나지 않을 확률로 나눈 것을 말한다. 

$$ odds(A)  = \frac{P(A)}{1-P(A)}$$

예를 들어 박스 내에 검정색 공 4개와 하얀색 공 6개가 들어있다고 하자. 만약 박스에서 무작위로 공을 뽑았을 때, 검정색 공이 나올 확률은 0.4이다. 승산의 경우는

$$ \frac{0.4}{0.6} = 0.67$$이 된다. 즉, 검정색 공이 나올 확률이 그렇지 않을 확률의 약 0.67배를 나타낸다. odds값이 1보다 크면 특정 사건의 확률이 일어나지 않을 확률보다 높으므로 1이 결정 기준이 된다고 보면 된다. 또, 확률 간의 비율이므로 odds는 $$\in (0, \infty)$$ range를 가진다.



여기서 로그를 취한값을 log-odds 혹은 logit이라 한다. logit의 범위는 $$(-\infty, \infty)$$이다. odds가 1인 지점에서 logit은 0값을 가지므로 결정의 기준이 logit에선 0이 된다.



### Logit을 사용하는 이유

로지스틱 분류기는 선형 분류기이다. x라는 데이터가 주어졌을 때, 분류 C가 1일 사후 확률 $$P(C=1 \mid X= x)$$와 x 관계가 선형적이고, 따라서 클래스를 나누는 decision boundary가 선형적이라는 가정을 한다.

일반 선형 회귀식$$ \hat y = \beta^T x + \beta_0$$은 연속형 종속 변수를 예측할 수 있도록 만들어졌다. 따라서, decision boundary가 선형이 될 수 있도록 사후 확률값을 변환해줄 단조함수(monotone function)가 필요하다.

$$ log(\frac{P(C=1 \mid X=x)}{P(C=0 \mid X=x)}) = \beta^T x + \beta_0$$



## Reference

- Friedman, J., Hastie, T., & Tibshirani, R. (2001). *The elements of statistical learning* (Vol. 1, No. 10). New York: Springer series in statistics.