---
title: "12 - Latent Dirichlet Allocation"
date: 2019-07-27
categories: Machine
mathjax: true
---

We have discussed about general Expectation Maximization algorithm and how it can be used in optimizing Gaussian Mixture Model. In this article, we will talk how EM can be used in Latent Dirichlet Allocation, which is one of method of topic modeling.

Before we get into LDA, let's see what Dirichlet distribution  and Beta distribution is.



### Beta Distribution

Beta distribution is:

$$Beta(\theta \mid \alpha, \beta)  = \frac{\Gamma(\alpha + \beta)} {\Gamma(\alpha) \Gamma( \beta )} \theta^{\alpha-1}(1-\theta)^{\beta-1}$$

where $$ \theta \in [0,1]$$

And gamma function in above equation is:

$$ \Gamma(x) = \int_0^\infty \theta^{x-1} e^{-\theta} d\theta$$

Beta distribution is widely used, because it is  conjugate priors for the binomial and Bernoulli distribution. Here is how Beta distribution looks like with different parameters:

<p align= "center">
    <img src = "../../assets/img/machine/12-beta.png" width= "70%">
    <br/>
    <sub>Beta Distribution</sub>
</p>





### Dirichlet Distribution

Dirichlet Distribution is a family of continuous multivariate distribution which is parameterized by a vector of $$\alpha$$:

$$Dir(\theta \mid \alpha) = Dir(\theta_1, .., \theta_K, \alpha_1, .., \alpha_K) = \frac{1}{Beta(\alpha_1, ..., \alpha_K)} \prod_{i=1}^K \theta_i^{\alpha_i -1}$$

where $$ \theta_i \in (0,1)$$ and $$\sum_{i=1}^K \theta_i = 1$$.

<br/>

If we set K =2, we can see  Beta distribution is special case of Dirichlet distribution, where $$ \theta_1 = \theta, \theta_2 = 1 - \theta, \alpha_1 = \alpha, \alpha_2 = \beta$$

$$Beta(\theta \mid \alpha, \beta)  = \frac{\Gamma(\alpha + \beta)} {\Gamma(\alpha) \Gamma( \beta )} \theta^{\alpha-1}(1-\theta)^{\beta-1}$$

$$ = \frac{\Gamma(\alpha_1 + \alpha_2)} {\Gamma(\alpha_1) \Gamma( \alpha_2)} \theta_1^{\alpha_1-1}\theta_2^{\alpha_2-1}$$

$$ = \frac{1}{Beta(\alpha_1, \alpha_2)} \prod_{i=1}^2 \theta_i^{\alpha_i -1} $$

We can say Dirichlet distribution is an extension of Beta distribution.

<br/>



## Latent Dirichlet Allocation

Suppose we have a customer review on a product. Then the review might consists of several different topics such as positive, negative, opinions about products, price or any other subjects. A document is a distribution over several topics. And the topic is a distribution over words. That is, we assume each document consists of words $$w$$ that are generated from selected topic $$z$$.  And the topic is selected with topic probabilities $$\theta$$, like it does in Gaussian Mixture Model. Directed Acyclic Graph of LDA looks like:



<p align="center">
    <img src ="../../assets/img/machine/12-lda-dag.png" style="width:70%">
    <br/>
    <sub>DAG of LDA</sub>
</p>

So each document has topic proportions which is $$\theta_d$$. And for each document and each word, it has per word topic assignment $$z_{dn}$$. And the latent variable $$z_{dn}$$ can take values from 1 to T, the number of topics. We will find this latent variable values from the corpus. Finally, each word $$w_{dn}$$ can take values from 1 to V, where V is the size of category.

Now we can define joint probability from above DAG:

$$ p(W, Z, \Theta) = \prod_{d=1}^D p(\theta_d) \prod_{n=1}^{N_d} p(z_{dn}\mid \theta_d) p(w_{dn} \mid z_{dn} ) $$

$$p(\theta_d) \sim Dir(\alpha)$$

$$ p(z_{dn} \mid \theta ) = \theta_{dz_{dn}}$$

$$ p(w_{dn} \mid z_{dn}) = \phi_{z_{dn}w_{dn}}$$



## LDA with EM

Recall that EM is to find q function which maximizes lower bound $$L(\theta, q)$$ and to find parameters which maximize expected value of log posterior joint probability. That is:

$$ \operatorname{log} p(X \mid \theta) \geq L(\theta, q) = \mathbb{E}_{q(T)} log \frac{p(X, T \mid \theta)}{q(T)} dT $$

$$ L(\theta, q) \rightarrow \underset{q}{\operatorname{argmin}} KL [q(T) \| p(T \mid X, \theta)] $$

$$ L(\theta, q) \rightarrow \underset{q}{\operatorname{argmax}} \mathbb{E}_{q(T)} \operatorname{log} p (X, T \mid \theta)$$

Here in EM, we evaluate full posterior of T, $$p(T \mid X, \theta)$$. However, this way of evaluating could be slow or even intractable sometimes. Instead, we 



In **E- step**,

$$ L(\theta, q)  $$

To find out latent parameters $$\theta, z, \phi$$, we can use EM algorithm we learned from previous articles.

In **E-step**, we maximize 



## Reference

- Bayesian Methods for Machine Learning by National Research University Higher School of Economics
-  CS498 Applied Machine Learning by Professor Trevor Walker
-  데이터 사이언스 스쿨. (2016, 05 25 Published). 디리클레분포. https://datascienceschool.net/view-notebook/e0508d3b7dd6427eba2d35e1f629d3de/