---
title: "10 - Expectation Maximization"
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

That is, the function of expected value is greater than or equal to the expected value of function when the function is concave.



### Kullback-Leibler Divergence

Kullback-Leibler divergence or KL measures the difference between two probability distribution. It looks like

$$ KL (q \| p) = \int q(x) log \frac{q(x)}{p(x)} dx $$

which is expected value of log ratio of q and p.

It has several properties:

- $$ KL(q \| p) \neq  KL( p\|q)$$. It is not symmetric.
- $$KL( q \| q) = 0 $$, since log 1 is zero.
- $$ KL(q \| p) \geq 0 $$

Proof for the last property:

$$ - KL(q \| p) = \int q(x) (- log \frac{q(x)}{p(x)} )dx  $$

$$ = E_q (-log \frac{q} {p})$$

$$ = E_q(log \frac{p}{q})$$

Here we can apply Jensen's inequality.

$$ \leq log ( E_q \frac{p}{q} ) $$

$$ = log \int{q(x) \frac{p(x)}{q(x)} dx} = 0 $$





## General Expectation Maximization

The two concepts take important part in expectation maximization algorithm. EM algorithm is basically to estimate likelihood when there are latent variables or missing values. EM algorithm enables approximate likelihood when likelihood is intractable. Let's get into details.

<br/>

Suppose we have a latent variable $$t$$ that affects the data points. Here we assume the latent variable t is discrete variable for simplicity, but it can be continuous.

$$ t_i  \rightarrow x_i$$



Then we have marginal likelihood of x and decompose it:

$$ p(X \mid \theta) = \sum_T p(X, T \mid \theta)$$

$$ p(x_i \mid \theta) = \sum_{c=1}^3 p( x_i, t_i = c \mid \theta) $$

$$ = \sum_c^3 p(t_i  =c \mid \theta) p(x_i \mid \theta, t_i = c)$$

assuming that the latent variable can have three discrete values 1~3.

And we want to maximize the likelihood.

$$ \operatorname{max}p(X \mid \theta) $$

$$ = \prod_i^N p(x_i \mid \theta)$$

$$ = \prod_i^N p(x_{i1},x_{i2}, ..., x_{id}   \mid \theta) $$ 

 

With log, we can simplify the equation:

$$L(\theta) =  \sum_i^N log(p(x_i \mid \theta) )$$

$$ = \sum_i^N log( \sum_{c=1}^3 p(x_i , t_i = c \mid \theta) )$$

$$ = \sum_i^N log( \sum_{c=1}^3 p(t_i =c\mid \theta) p(x_i \mid \theta,  t_i = c ) )$$

However, since above equation requires solving log sum of all values of the latent variable t, it is not easy to calculate.

<br/>

Therefore, with Jensen's inequality, we rather set lower bound of the log likelihood and make the lower bound as close as possible to the log likelihood. Here we introduce any probability function on the latent variable, which does not change anything.

$$ = \sum_i^N log \sum_{c=1}^3  \frac{q(t_i = c)}{q(t_i = c)}  p(x_i , t_i = c \mid \theta) $$

By treating q as a weight in Jenesen' inequality, we get

$$ \geq \sum_i^N \sum_{c=1}^3 q(t_i = c) log \frac{p(x_i, t_i = c \mid \theta)}{q(t_i = c)}$$

Because

$$ \operatorname{log} (\sum_c \alpha_c v_c) \geq \sum_c \alpha_c log(v_c)$$ 

where $$\alpha_c = q(t_i = c)$$ and $$ v_c = p(x_i, t_i=c \mid \theta) / q(t_i = c)$$

$$ p(x_i \mid \theta) = \sum_j^K \pi_j N(x_i \mid \mu_1, ..., \mu_k, \Sigma_1, ..., \Sigma_k)$$

Now the lower bound depends on q, that is

$$ = L(\theta, q)$$

<br/>

From here, we iterate E and M steps.

In E-step, we fix $$\theta$$ and find $$q$$ that maximize lower bound so that it is closest as possible to likelihood :

$$q^{k+1} = \underset{q}{\operatorname{argmax} } L(\theta^k, q)$$

In M-step, we fix $$q$$ and find  $$\theta$$  that maximize lower bound:

$$ \theta^{k+1} = \underset{\theta}{\operatorname{argmax}} L(\theta, q^{k+1})$$



### E-Step

As we defined, the lower bound is smaller or equal to the log likelihood according to Jensen's inequality, indicating the gap between the lower bound and the log likelihood.

We want to maximize the lower bound in order to minimize the gap.  And by definition the gap is:

$$ \operatorname{GAP} = \operatorname {log} p(X \mid \theta) - L(\theta, q)$$

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

$$q^{k+1} =  \operatorname {\underset{q}{arg min}} KL[q(T) \| p(T \mid X, \theta^k)]$$

$$ q^{k+1}(t_i) = p(t_i \mid x_i, \theta^k) $$



### M-Step

In M-step, we maximize the lower bound with respect to $$\theta$$.

$$ L(\theta, q) = \sum_i^N \sum_c q(t_i = c) log \frac{p(x_i, t_i = c \mid \theta)}{q(t_i = c)}$$

$$  =\sum_i^N \sum_c q(t_i = c) \operatorname{log} p(x_i, t_i = c \mid \theta)  - \sum_i^N \sum_c q(t_i = c) \operatorname{log} q(t_i = c)$$

$$ = E_q \operatorname{log} p(X, T \mid \theta) + const $$

since the second term does not depend on $$\theta$$

We only need to maximize the expectation of joint posterior probability.

In short,

$$ \theta^{k+1} = \operatorname{ \underset{\theta}{argmax}} E_{q^{k+1}} \operatorname{log} p(X,T \mid \theta)$$



In next article we will look at how this EM algorithm works in Gaussian Mixture Model.



### Example

Let's say we have mixture of Gaussian model.

In **E-step**, we want to find $$q$$, which minimizes KL divergence.

That is, we want to evaluate $$p(t_i \mid x_i, \theta^k)$$. It can be decomposed as

$$ p(t_i = c \mid x_i, \theta) =\frac{p(x_i, t_i = c \mid \theta)}{p(x_i \mid \theta)} = \frac{p(x_i, t_i = c \mid \theta)}{\sum_j^K p(x_i, t_i= j \mid \theta)}$$

$$ \frac{p(t_i = c \mid \theta) p(x_i \mid \theta, t_i = c)}{\sum_{j=1}^k p(t_i =j\mid \theta) p(x_i \mid \theta, t_i = j)}$$

$$ = \frac{ \pi_c N(x_i \mid \mu_c, \Sigma_c) }{\sum_{j=1}^k \pi_j N(x_i \mid \mu_j , \Sigma_j)}$$

$$ = \frac{ \pi_c \frac{1}{(2\pi )^{n/2} \mid \Sigma_c \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) } {\sum_{j=1}^k \pi_j \frac{1}{(2\pi )^{n/2} \mid \Sigma_j \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) } $$

where $$\pi_c$$ indicates the probability of a data point belongs to cluster c, while $$\pi$$ is just mathematical pi. Note that the constant part of pi disappear, then

$$ p(t_i = c\mid x_i, \theta_c) = \frac{\pi_c \mid \Sigma_c \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) }{\sum_{j=1}^K \pi_j \mid \Sigma_j \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) )}$$



In **M-step**, we maximize the lower bound with respect to $$\theta$$, which is maximizing

$$ E_{q} \operatorname{log} p(X, T \mid \theta)$$

<br/>

Start with mean.

$$ \nabla{u_c} L(\theta,q) = \nabla{u_c} \sum_{i=1}^N  q(t_i = c) \operatorname {log} p(x_i \mid t_i =c, \theta) = 0$$

Note that we set $$q(t_i = c)$$ as $$p(t_i = c \mid x_i, \theta)$$, and we can decompose the likelihood given the latent variable and parameters. Then

$$ \nabla_{u_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \operatorname {log} p(x_i \mid t_i = c, \theta)  p(t_i = c \mid \theta)$$

Note that $$p(x_i \mid t_i, \theta) = N(x_i \mid \mu_c, \Sigma_c)$$ and $$p(t_i = c \mid \theta)  = \pi_c$$. Now it becomes

$$ = \nabla_{u_c} \sum_{i=1}^N p(t_i=c \mid x_i, \theta) \operatorname{log} \{ \frac{1}{ 2 \pi^{n/2} \mid \Sigma_c \mid^{1/2}} exp(-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c)) \pi_c \}$$

$$ = \nabla_{u_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{  \operatorname{log}(\frac{\pi_c}{2 \pi^{n/2} \mid \Sigma_c \mid^{1/2}} ) +\operatorname{log} \operatorname{exp}(-1/2 (x_i \ - \mu_c)^T \Sigma_c^{-1} (x_i -\mu_c)) \}$$

$$= \nabla_{u_c} -\frac{1}{2} \sum_{i=1}^N p(t_i = c \mid x_i, \theta )\{ (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) \} $$

since the first log term does not depend on $$\mu_c$$.

$$ = -\frac{1}{2} \sum_{i=1}^N p(t_i =c \mid x_i, \theta) \nabla_{u_c}\{ (x_i-\mu)^T \Sigma_c^{-1}(x_i - \mu_c) \} $$

$$ = \frac{1}{2} \sum_{i=1}^N p(t_i =c \mid x_i, \theta) \{ (\Sigma_c^{-1} + (\Sigma_c^{-1})^T) (x_i - \mu_c) \} $$

since $$\nabla_x x^T A x = (A + A^T)x $$

$$ = \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{ (\Sigma_c^{-1}) (x_i - \mu_c) \}$$

as the covariance matrix is symmetric, so is its inverse. And we multiply $$\Sigma_c$$ to both sides.

$$ = \sum_{i=1}^N p(t_i = c \mid x_i, \theta) (x_i - \mu_c)$$

$$ \sum_{i=1}^N p(t_i = c \mid x_i, \theta)x_i = \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \mu_c$$

$$ \mu_c = \frac{\sum_{i=1}^N p(t_i = c \mid x_i, \theta)x_i }{ \sum_{i=1}^N p(t_i = c \mid x_i, \theta)} $$

 

Note that 

$$ p(t_i = c \mid x_i, \theta_c) = q(t_i=c) \frac{p(t_i = c) p(x_i, \theta_c \mid t_i = c)}{\sum_{j=1}^k p(t_i =c) p(x_i, \theta_c \mid t_i = c)}$$

$$ = \frac{ \pi_c N(x_i \mid \mu_c, \Sigma_c) }{\sum_{j=1}^k \pi_j N(x_i \mid \mu_j , \Sigma_j)}$$

$$ = \frac{ \pi_c \frac{1}{(2\pi )^{n/2} \mid \Sigma_c \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) } {\sum_{j=1}^k \pi_j \frac{1}{(2\pi )^{n/2} \mid \Sigma_j \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) } $$

$$ = \frac{\mid \Sigma_c \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) }{\sum_{j=1}^K \mid \Sigma_j \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) )}$$

<br/>

Now for the covariance,

$$ \nabla_{\Sigma_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \operatorname {log} p(x_i \mid t_i = c, \theta)  p(t_i = c \mid \theta)$$

$$ = \nabla_{\Sigma_c} \sum_{i=1}^N p(t_i=c \mid x_i, \theta) \operatorname{log} \{ \frac{1}{ 2 \pi^{n/2} \mid \Sigma_c \mid^{1/2}} exp(-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c)) \pi_c \}$$

$$ = \nabla_{\Sigma_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{ \operatorname{log} (\frac{1} {\mid \Sigma_c \mid^{1/2}}) - 1/2 (x_i -\mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) \}$$

$$  = -\frac{1}{2} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{ \frac{\partial log \mid \Sigma_c \mid}{ \partial \Sigma_c} +\frac{\partial}{\partial \Sigma_c} (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) \} $$

First we evaluate the derivative of log determinant,

$$ \frac{\partial \operatorname{log} \mid \Sigma_c \mid}{\partial \Sigma_c} = \frac{1}{\mid \Sigma_c \mid} \frac{\partial \mid \Sigma_c \mid}{\partial \Sigma_c}$$

$$ = \frac{1}{\mid \Sigma_c \mid} \mid \Sigma_c \mid (\Sigma_c^{-1})^T $$

as $$ \frac{\partial \mid X \mid}{ \partial X} = \mid X \mid (X^{-1})^T$$

$$ = \Sigma_j^{-1}$$

And for the second part of the derivative,



$$\Sigma_c = \frac{\sum_i^N p(t_i = c \mid x_i, \theta) (x_i - \mu_c) (x_i - \mu_c)^T } {\sum_{i=1}^N p(t_i = c \mid x_i, \theta)}$$



<br/>

Lastly for $$\pi_c$$,

$$ \pi_c = \frac{\sum_{i=1}^N q(t_i = c)}{N} $$



$$p(t_i =c \mid \theta)  = \pi_c$$

$$p(x_i \mid t_i =c, \theta) = N(x_i \mid \mu_c, \Sigma_c)$$

$$ p(x_i \mid \theta) = \sum_c^k p(x_i \mid t_i = c, \theta )p(t_i = c \mid \theta)$$

$$p(x_i, t_i= c \mid \theta) = p(x_i \mid t_i = c, \theta) p(t_i =c \mid \theta)$$



## Reference

- Bayesian Methods for Machine Learning by National Research University Higher School of Economics
-  CS498 Applied Machine Learning by Professor Trevor Walker