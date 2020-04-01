---
title: "[구현] Alternating Least Sqaures"
date: 2020-03-13
categories: Recommender
mathjax: true
---



250만개의 movelen 데이터를 이용하여 implicit dataset을 행렬분해 해보겠다. 목적함수는 다음과 같다.

$$ \underset {x*, y*} {\min} \underset {r_{u,i} \text{is known}} \sum (r_{ui} - x_u^T y_i)^2 + \lambda(\|x_u\|^2 + \| y_i \| ^2) $$



그리고 행렬 업데이트 식은 다음과 같다.

$$ x_u = (\sum_i c_{ui} \ y_i \ y_i^T + \lambda I)^{-1} \sum_i c_{ui} p_{ui} y_i$$

$$ y_i = (\sum_u c_{ui} \ x_u \ x_u^T + \lambda I) ^{-1} \sum_u c_{ui} p_{ui} x_u$$



Confidence level matrix인 C의 경우 preference에 alpha값을 곱하고 1을 더한 값으로 세팅해준다.

$$ c_{ui} = 1+ \alpha r_{ui}$$

여기서 알파값은 보통 40을 사용한다고 한다.