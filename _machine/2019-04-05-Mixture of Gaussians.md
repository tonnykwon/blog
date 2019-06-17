---
title: "10 - Mixture of Gaussian"
date: 2019-06-10
categories: Machine
---

K-means clustering is about grouping data based on its distance or similarity. We can think clustering in different perspective. Each of data points comes from a certain probability model. That is, if two data points are close, they are more likely to be from same probability model. Just like the problem of K-means, for probability model, we do not know which model produced which points. For now, we suppose know which data point is from which probability model, and  we will build a model.

We assume data points are from several normal distributions with known covariance matrix $$\Sigma$$ while mean is unknown. We can factorize the covariance matrix $$\Sigma = A A^T$$, and apply $$A^{-1}$$ to data points, and we now have identity covariance matrix.

Let's call $$\mu_j$$ is the mean of the jth normal distribution, and $$\pi_j$$ is the probability of a document belongs to topic j(which is the number of topic $$j$$ documents divided by the number of all documents), where $$\sum_j \pi_j = 1$$. Then, we can have the probability of sampling words from the probability distribution,

$$p(x \mid \mu_1, ..., \mu_k, \pi_1, ..,. \pi_k) = \sum_j \pi_j [\frac{1}{\sqrt{(2 \pi)^d}} exp(-\frac{1}{2} (x-\mu_j)^T (x - \mu_j)) ]$$

First we choose one of distribution from normal distributions. Then generate data points from that distribution. This form of multiple distributions is called a mixture of normal distributions. Instead of using log-likelihood, which ends up having sum of logs of sums, we introduce a hidden variable $$\delta_i$$. This hidden variable is a vector that indicates from which distribution the data point $$i$$ is generated. In other words, if $$\delta_{ij} = 1 $$, then the data point $$i$$ is from the clustering $$j$$. And also $$\sum_j \delta_{ij} = 1 $$.

Now with a hidden variable, we can rewrite our likelihood,

$$ p(x_i \mid \delta_i, \theta) = \prod_j [\frac{1}{\sqrt{(2 \pi)^d}} exp(-\frac{1}{2} (x_i-\mu_j)^T(x_i -\mu_j)) ]^{\delta_{ij}}$$

where $$\theta = (\mu_1, ..., \mu_k, \pi_1, ... , \pi_k)$$.

Also we have,

$$p(\delta_{ij} = 1 \mid \theta) = \pi_j$$

Then,

$$p(\delta_i \mid \theta) = \prod_j [\pi_j]^{\delta_{ij}}$$

since all values would be 1 when the $$\delta_{ij} = 0$$ .

Now we have the probability of getting word count vector $$x_i$$, and the cluster indicator $$\delta_{i}$$. That is,

$$ p(x_i, \delta_i \mid \theta) = \prod_j [(- \frac{1}{\sqrt{(2 \pi)^d}} exp(-\frac{1}{2} (x_i - \mu_j)^T (x_i - \mu_j))) \pi_j]^{\delta_{ij}}$$

<br/>

Then we can write a log-likelihood. Assuming we know the hidden variable $$\delta$$, we get

$$ L(\mu_1, ..., \mu_k, \pi_1, ... , \pi_k ; x, \delta) = L(\theta; x, \delta)$$

$$ = \sum_{ij} [- \frac{1}{2} (x_i - \mu_j)^T (x_i - \mu_j)  + \operatorname {log} \pi_j ] \delta_{ij} + K $$







## Reference

- CS498 Applied Machine Learning by Professor Trevor Walker