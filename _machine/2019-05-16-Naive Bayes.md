---
title: "2 - Naive Bayes"
date: 2019-05-16
categories: Machine
---

Naive Bayes Classifier is a classifier out of a probability model. Assume we already know the posterior probability, \(p(y|x)\). Now we will assign the class y for the data that has highest probability $$p(y|x)$$. And the issue here is how to find the probability $$p(y|x) $$.

Applying Bayes' theorem gives:

$$ p(y|x) = \frac{p(x|y) p(y)} {p(x)} $$



- Can be useful in practice
- Nonparametric method and therefore, somewhat restricted in sense of model complexity
- Requires entire dataset to be stored, which is expensive when dataset is large
- Shown that, with large enough data, the error rate is never more than twice the minimum achievable error rate of optimal classifier





## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
- Bishop, C. M. (2006). *Pattern recognition and machine learning*. springer.

