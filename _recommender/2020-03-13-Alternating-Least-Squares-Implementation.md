---
title: "[구현] Alternating Least Sqaures"
date: 2020-03-13
categories: Recommender
mathjax: true
---



250만개의 movelen



$$ \underset {x*, y*} {\min} \underset {r_{u,i} \text{is known}} \sum (r_{ui} - x_u^T y_i)^2 + \lambda(\|x_u\|^2 + \| y_i \| ^2) $$



$$ x_u = (\sum_i c_{ui} \ y_i \ y_i^T + \lambda I)^{-1} \sum_i c_{ui} p_{ui} y_i$$



$$ y_i = (\sum_u c_{ui} \ x_u \ x_u^T + \lambda I) ^{-1} \sum_u c_{ui} p_{ui} x_u$$
