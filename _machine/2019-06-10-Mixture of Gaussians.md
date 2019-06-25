---
title: "10 - Mixture of Gaussian"
date: 2019-06-10
categories: Machine
mathjax: true
---

K-means clustering is about grouping data based on its distance or similarity. We can think clustering in different perspective. Each of data points comes from a certain probability model. That is, if two data points are close, they are more likely to be from same probability model. Just like the problem of K-means, for the probability model, we do not know which model produced which points. For now, we suppose know which data point is from which probability model, and build a model.

Let's assume data points are from several normal distributions with known covariance matrix $$\Sigma$$ while mean is unknown. We can factorize the covariance matrix $$\Sigma = A A^T$$, and apply $$A^{-1}$$ to data points, and we now have identity covariance matrix.

Let's call $$\mu_j$$ is the mean of the $$j$$th normal distribution, and $$\pi_j$$ is the probability of a point belongs to cluster $$j$$ (which is the number of points in cluster $$j$$ divided by the number of all data points), where $$\sum_j \pi_j = 1$$. Then, we can have the probability of sampling words from the probability distribution,

$$p(x \mid \mu_1, ..., \mu_k, \pi_1, ..,. \pi_k) = \sum_j \pi_j [\frac{1}{\sqrt{(2 \pi)^d}} exp(-\frac{1}{2} (x-\mu_j)^T (x - \mu_j)) ]$$

$$ = \sum_j \pi_j N(x \mid \mu_1, ..., \mu_k)$$

First we choose one of distribution from normal distributions, $$\pi_j$$. Then generate data points from that distribution. This form of multiple distributions is called a mixture of normal distributions. If we take log-likelihood, we have

$$ \sum_j \operatorname{log}(\pi_j N(x \mid \mu_1, ..., \mu_k) ) $$

which is intractable for finding maximum likelihood. Instead, we introduce a hidden variable $$\delta_i$$. This hidden variable is a vector that indicates from which distribution the data point $$i$$ is generated. In other words, if $$\delta_{ij} = 1 $$, then the data point $$i$$ is from the cluster $$j$$. Since each data point belongs to only one cluster, it comes out that $$\sum_j \delta_{ij} = 1 $$.

And using the Expectation-Maximization algorithm, we will update $$\theta = (\mu_1, ..., \mu_k, \pi_1, ... , \pi_k)$$ as follows:

​	1) Define a expectation function

$$Q(\theta; \theta^{n}, X) = E_{\delta}(L)$$

$$ = \sum_{\delta} L(\theta ; X, \delta) p(\delta \mid \theta^{(n)}, X) $$

​	where, $$\theta^{(n)}$$ is fixed.



​	2) Update $$\theta$$ by

$$ \theta^{(n+1)} = \underset{\theta} {\operatorname{argmax \operatorname Q(\theta; \theta^{(n)}, X) }} $$



### E-step

Now let's work on how to define the loss function.

With a hidden variable known, we can rewrite our likelihood,

$$ p(x_i \mid \delta_{ik} =1, \theta)  = N(x \mid \mu_k)$$

And for the general form of likelihood, we have

$$ p(x_i \mid \delta_i, \theta) = \prod_j [\frac{1}{\sqrt{(2 \pi)^d}} exp(-\frac{1}{2} (x_i-\mu_j)^T(x_i -\mu_j)) ]^{\delta_{ij}}$$

$$ = \prod_j N(x_i \mid \theta)^{\delta_{ij}} $$



We can calculate the probability of a certain cluster assignment, 

$$p(\delta_{ij} = 1 \mid \theta) = \pi_j$$

Then, the general form is

$$p(\delta_i \mid \theta) = \prod_j [\pi_j]^{\delta_{ij}}$$

as all other values would be 1 when the $$\delta_{ij} = 0$$ .

<br/>

With all equations above, we can make joint probability of $$x_i , \delta_i$$ given $$\theta$$,

$$p(x_i, \delta_i \mid \theta) = p(x_i \mid \delta_i, \theta) \cdot p(\delta_i, \theta)$$

$$ = \prod_j [(- \frac{1}{\sqrt{(2 \pi)^d}} exp(-\frac{1}{2} (x_i - \mu_j)^T (x_i - \mu_j))) \pi_j]^{\delta_{ij}}$$

$$ = \prod_j [\pi_j N(x_i \mid \theta)]^{\delta_{ij}} $$

<br/>

Finally we define loss function using a log-likelihood.

$$ L(\mu_1, ..., \mu_k, \pi_1, ... , \pi_k ; X, \delta) = L(\theta; X, \delta)$$

$$ = \sum_i \operatorname {log} p(x_i, \delta_i \mid \theta)$$

$$ = \sum_i \sum_j [- \frac{1}{2} (x_i - \mu_j)^T (x_i - \mu_j)  + \operatorname {log} \pi_j ] \delta_{ij} + K $$

where $$K$$ is the log of $$- \frac{1}{\sqrt{(2\pi)^d }} $$ part and drops out in the derivative.

Let's say $$c_i = [- \frac{1}{2} (x_i - \mu_j)^T (x_i - \mu_j)  + \operatorname {log} \pi_j ]$$, where it is k-dimension. Now we have,

$$ L(\theta ; X, \delta) = \sum_i c_i^T \delta_i +K $$

$$Q(\theta ; \theta^{(n)}) = \sum_{\delta} [L(\theta; X, \delta) p(\delta \mid \theta^{(n)}, X)]$$

$$=  \sum_{\delta_i} [\sum_i c_i^T \delta_i p(\delta_i \mid \theta^{(n)}, X)] + K $$

$$=  \sum_i [\sum_{\delta_i} c_i^T \delta_i p(\delta_i \mid \theta^{(n)}, X)] + K $$

$$ = \sum_i [\sum_{\delta_i} {\{-\frac{1}{2}(x_i - \mu_j)^T (x_i - \mu_j) + \operatorname{log} \pi_j \}} \cdot E(\delta_{ij})_{\delta_i \mid \theta^{(n)}, X}] + K$$

And the expected value of delta is,

$$ E(\delta_{ij})  = 1 \cdot p(\delta_{ij} =1 \mid \theta^{(n)}, X) + 0 \cdot p(\delta_{ij}=0 \mid \theta^{(n)}, X)$$

$$ = p(\delta_{ij} = 1 \mid \theta^{(n)}, X) $$

where the probability is

$$p(\delta_{ij} = 1 \mid \theta^{(n)}, X) = \frac{p(X, \delta_{ij}=1 \mid \theta^{(n)})}{p(X \mid \theta^{(n)})} $$

$$ = \frac {p(X, \delta_{ij} =1 \mid \theta^{(n)})} {\sum_l p(X, \delta_{il} \mid \theta^{(n)})}$$

$$ = \frac{p(x_i, \delta_{ij} = 1 \mid \theta^{(n)})} {\sum_l p(x_i, \delta_{ij} = 1 \mid \theta^{(n)})} $$

$$ p(x_i, \delta_{ij} \mid \theta^{(n)}) = p(x_i \mid \delta_{ij} = 1, \theta^{(n)}) \cdot p(\delta_{ij} = 1 \mid \theta^{(n)})$$

$$ = \frac{1}{(2\pi)^d} exp( - \frac{1}{2}(x_i - \mu_j)^T (x_i - \mu_j))\pi_j $$

<br/>

If we rewrite the Q function,

$$ Q(\theta, \theta^{(n)}, X) = \sum_i \sum_j \{[-1/2 (x_i-\mu_j)^T(x_i - \mu_j) ] + log(\pi_j)\} \cdot w_{ij}^{(n)} +K $$

where $$w_{ij} = E(\delta_{ij})$$.

Since $$w_{ij}$$ depends on $$\theta^{(n)}$$, we can easily calculate it.



### M-step

Recall that in M-step, we find new parameters

$$ \theta^{(n+1)} = \underset {\operatorname{\theta}} {argmax} \operatorname Q (\theta; \theta^{(n)}, X)$$

If we get derivative of $$Q$$ with respect to parameters $$\mu_j$$ and $$\pi_j$$, then we have

$$\mu_j^{(n+1)} = \frac{\sum_i x_i w_{ij}}{\sum_i w_{ij}} $$

$$ \pi_j^{(n+1)} = \frac{\sum_i w_{ij}}{N}$$





## Reference

- CS498 Applied Machine Learning by Professor Trevor Walker