---
title: "11 - General EM"
date: 2019-06-27
categories: Machine
mathjax: true
---

Before get into General EM algorithm, we need to know two related ideas; **Jensen's Inequality** and **Kullback-Leibler Divergence**.

$$f(x)$$ is concave if 

for any $$a, b, \alpha : f(\alpha a + (1- \alpha)b) \geq \alpha f(a) + (1-\alpha) f(b) $$

where $$ 0 \leq \alpha \leq 1 $$

So basically for any point between a and b, concave function has higher value compared to the linear function.



### Jensen's Inequality

Now Jensen's inequality is:

if $$ f(\alpha a + (1-\alpha) b) \geq \alpha f(a) + (1-\alpha) f(b)$$

then $$\alpha_1 + \alpha_2 + \alpha_3 = 1; \alpha_k \geq 0. $$

$$ f(\alpha_1 a_1 + \alpha_2 a_2 + \alpha_3 a_3) \geq \alpha_1 f(a_1) + \alpha_2 f(a_2) + \alpha_3 f(a_3) $$

$$ p(t = a_1) = \alpha_1, p(t = a_2) = \alpha_2, p(t = a_3) = \alpha_3$$ 

where left hand side is expected value of $$E_{p(t)} t$$ and right hand side is expected value of function of $$E_{p(t)} f(t) $$.



### Kullback-Leibler Divergence

Kullback-Leibler divergence or KL measures the difference between two probability distribution. It looks like

$$ KL (q \| p) = \int q(x) log \frac{q(x)}{p(x)} dx $$

which is expected value of log ratio of q and p.

It has several properties:

- $$ KL(q \| p) \neq  KL([ p\|q])$$. It is not symmetric.
- $$KL( q \| q) = 0 $$, since log 1 is zero.
- $$ KL(q \| p) \geq 0 $$

Proof for the last property:

$$ - KL(q \| p) = \int q(x) (- log \frac{q(x)}{p(x)} )dx  $$

$$ = E_q (-log \frac{q} {p})$$

$$ = E_q(log \frac{p}{q})$$

Here we can apply Jesen's inequality.

$$ \leq log ( E_q \frac{p}{q} ) $$

$$ = log \int{q(x) \frac{p(x)}{q(x)} dx} = 0 $$





## General Expectation Maximization

Suppose we have a latent variable $$t$$ that affects the data points.

$$ t_i  \rightarrow x_i$$



Then we have marginal likelihood of x:

$$ p(x_i \mid \theta) = \sum_{c=1}^3 p( x_i \mid t_i = c, \theta) p(t_i = c \mid \theta)$$

assuming that the latent variable can have three discrete values 1~3.

And we want to maximize the likelihood.

$$ \operatorname{max}p(X \mid \theta) $$

$$ = \prod_i^N p(x_i \mid \theta)$$

With log, we can simplify the equation:

$$ = \sum_i^N log(p(x_i \mid \theta) )$$

$$ = \sum_i^N log( \sum_{c=1}^3 p(x_i , t_i = c \mid \theta) )$$

$$ \geq L(\theta)$$

With Jensen's inequality, we can set lower bound of the log likelihood. Here we introduce any function on the latent variable, which does not change anything.

$$ \sum_i^N log \sum_{c=1}^3  \frac{q(t_i = c)}{q(t_i = c)}  p(x_i , t_i = c \mid \theta) $$

By treating q as a weight in Jenesen' inequality, we get

$$ \geq \sum_i^N \sum_{c=1}^3 q(t_i = c) log \frac{p(x_i, t_i = c \mid \theta)}{q(t_i = c)}$$

Now the lower bound depends on q, that is

$$ = L(\theta, q)$$

<br/>

From here, we iterate E and M steps.

In E-step, we fix $$\theta$$ and maximize $$q$$:

$$q^{k+1} = \underset{q}{arg max} L(\theta^k, q)$$

In M-step, we fix $$q$$ and maximize $$\theta$$:

$$ \theta^{k+1} = \underset{\theta}{argmax} L(\theta, q^{k+1})$$



### E-Step

There is a gap between the log likelihood we want to maximize and the lower bound of it. Which is

$$ GAP = \operatorname {log} p(X \mid \theta) - L(\theta, q)$$

$$ = \sum_i^N \operatorname{log} p(x_i \mid \theta) - \sum_i^N \sum_{c=1}^3 q(t_i = c) log \frac{p(x_i, t_i = c \mid \theta)}{ q(t_i = c)}$$

$$ = \sum_i^N \{ \operatorname{log} p(x_i \mid \theta) \sum_{c=1}^3 q(t_i =c) - \sum_{c=1}^3 q(t_i=c) log \frac{p(x_i, t_i = c \mid \theta)}{ q(t_i = c)} \}$$

since $$\sum_{c=1}^3 q(t_i =c) = 1$$

$$ = \sum_i^N \sum_{c=1}^3 q(t_i=c) (\operatorname{log} p(x_i \mid \theta) - log \frac{p(x_i, t_i = c \mid \theta)}{ q(t_i = c)} ) $$

$$  = \sum_i^N \sum_{c=1}^3 q(t_i=c) \operatorname{log} \frac{p(x_i \mid \theta) q(t_i=c)} {p(x_i, t_i =c \mid \theta)}$$ 

$$ = \sum_i^N \sum_{c=1}^3 q(t_i=c) \operatorname{log} \frac{q(t_i=c)} {p(t_i =c \mid x_i, \theta)} $$

$$ = \sum_i^N KL(q(t_i) \| p(t_i  \mid x_i, \theta))$$

That is,

$$ L(\theta, q) = \operatorname{log} p(X \mid \theta) - KL (q \| p) $$

The gap between the log likelihood and its lower bound is the KL divergence of q function and posterior function of t. And therefore maximizing the gap is maximizing the lower bound with respect to q, because the log likelihood does not depend on q.

Recall the property of KL that it is always equal to or greater than zero. And it is zero only if two distribution are same. Given that property, we can get the lowest gap by setting q function equal to the posterior function of t $$p(t_i \mid x_i, \theta)$$.



<br/>

In short, 

$$q^{k+1} = \underset{q}{arg min} KL[q(T) \| p(T \mid X, \theta^k)]$$

$$ q^{k+1}(t_i) = p(t_i \mid x_i, \theta^k) $$





### M-Step

In M-step, we maximize the lower bound with respect to $$\theta$$.

$$ L(\theta, q) = \sum_i^N \sum_c q(t_i = c) log \frac{p(x_i, t_i = c \mid \theta)}{q(t_i = c)}$$

$$  =\sum_i^N \sum_c q(t_i = c) \operatorname{log} p(x_i, t_i = c \mid \theta)  - \sum_i^N \sum_c q(t_i = c) \operatorname{log} q(t_i = c)$$

$$ = E_q \operatorname{log} p(X, T \mid \theta) + const $$

since the second term does not depend on $$\theta$$

We only need to maximize the expectation of joint posterior probability.



## Reference

- Bayesian Methods for Machine Learning by
- CS498 Applied Machine Learning by Professor Trevor Walker