---
title: "2 - Naive Bayes"
date: 2019-05-16
categories: Machine
mathjax: true
---



Applying Bayes' rule gives:

$$ p(y|x) = \frac{p(x|y) p(y)} {p(x)} $$

The post <a href ="<https://tonnykwon.github.io/blog/bayesian/2018-12-26-bayesian-statistics">Bayesian Statistics</a> in Bayesian session covers specific of each probability in Bayes' rule.



Here we will assume the features are conditionally independent conditioned on the class of the data, which is why we call this classifier "Naive". Then we get:

$$ p(x |y) = \prod_j p(x^{(j)} | y)  $$

Then the posterior would be:

$$ p(y|x) = \frac{p(x|y) p(y)} {p(x)} $$

$$ =  \frac{(\prod_j p(x^{(j)} | y))   p(y)} {p(x)} $$

$$ \propto (\prod_j p(x^{(j)} | y) ) p(y)$$

Since we only need to know $$\underset{y \in Y}{\operatorname{argmax}} p(y|x)$$, we do not necessarily know the normalizer $$p(x)$$, which y is not dependent on. By taking the highest proportionality, we can get the class y which is the most probable.





Naive Bayes Classifier is a classifier out of a probability model. Assume we already know the posterior probability, p(y|x), where x is a vector with multiple properties, and y is a scalar indicating  specific class. Now we will assign the class y for the data that has highest probability p(y|x)​. And the issue here is how to find the probability p(y|x).



## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
- Bishop, C. M. (2006). *Pattern recognition and machine learning*. springer.
