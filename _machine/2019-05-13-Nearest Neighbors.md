---
title: "1 - Nearest Neighbors"
date: 2019-05-13
categories: Machine
mathjax: true
---

Suppose we are given data $$x$$ that we want to find out label for. We can simply find the closest data from given data $${(x_1, y_1), ..., (x_N, y_N)} $$ to the $$x$$. And then vote for label $$x$$ based on the closest data points labels $$y_1, ..., y_K$$. This technique is called K-Nearest Neighbors or K-NN in brief.

## Properties

- Can be useful in practice
- Nonparametric method and therefore, somewhat restricted in sense of model complexity
- Requires entire dataset to be stored, which is expensive when dataset is large
- Shown that, with large enough data, the error rate is never more than twice the minimum achievable error rate of optimal classifier





## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
- Bishop, C. M. (2006). *Pattern recognition and machine learning*. springer.

