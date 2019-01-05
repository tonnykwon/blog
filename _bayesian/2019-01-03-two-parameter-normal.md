---
title: "4 - Two Parameters Normal"
date: 2019-01-03
categories: Bayesian
---

## Multiparameter Model

In the previous post, we used normal sampling distribution assuming variance $$ \sigma^2$$ is known. However, it is more realistic that both $$ \mu$$ and $$ \sigma^2 $$ are unknown.  In such case where we do not know multiple parameters, first, we get joint posterior distribution of all unknowns. Then, integrate over the unknowns that are not interest, and get the marginal distribution for the parameter of interest. Parameters that are required to build model but not in interest called *nuisance parameters*. Here we average out nuisance parameters to get posterior marginal distributions for parameters in interest.

Suppose we have two unknown parameters $$ \theta_1 $$ and $$\theta_2$$, and the posterior is:

$$ p(\theta_1, \theta_2 |y) \propto p(y | \theta_1, \theta_2) p(\theta_1, \theta_2) $$



If we want to find out the posterior distribution of $$ \theta_1$$, we average out $$ \theta_2 $$ by using integral:

$$ p(\theta_1 | y) = \int p(\theta_1, \theta_2 | y) d\theta_2$$

$$ = \int p(\theta_1 | \theta_2, y) p(\theta_2| y) d\theta_2 $$



## Posterior Marginal Density of $ \sigma^2 $

Recall likelihood from the previous post:

 $$p(y | \mu, \sigma^2)  \propto \frac{1}{(\sigma^2)^{n/2}}{exp(-\frac{1}{2\sigma^2}\sum^n_{i=1}(y_i-\mu)^2})$$

$$\propto \frac{1} {(\sigma^2)^{n/2}} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)) $$



Then, the posterior is:

$$ p(\mu, \sigma^2|y)\propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)) $$

So, we look for prior density that has similar form of likelihood. 



To get the marginal posterior distribution of $$ \sigma^2 $$, we need to average out over $$ \mu $$:

$$ p(\sigma^2 |y) \propto \int \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2 + n(\bar y - \mu)^2 ]) d \mu $$

$$ \propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2]) \int exp([n(\bar y - \mu)^2 ]) d \mu  $$

$$ \propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2])  \sqrt{2\pi \sigma^2/n} $$

$$  \propto (\sigma^2)^{-(n+1)/2)} exp(-\frac{(n-1)s^2}{2\sigma^2} )$$

As it is integrating over $$ \mu$$ , we only require to  integral the last exponential part $$ \int exp([n(\bar y - \mu)^2 ]) d \mu $$. Then, it comes out that it is just a simple normal integral. From $$ \sqrt{2\pi \sigma^2 /n} $$  part, only $$ \sigma^2 $$ part depends on parameters, we can get proportionality from it.

The last equation is a scaled inverse-$$ \chi^2 $$ density:

$$ \sigma^2 | y  \sim Inv-\chi^2 (n-1, s^2) $$



## Posterior Marginal Density of $ \mu$

Very similar way, we can find posterior marginal density of mean.



## Non-informative Prior

Now all of the factors contain either $$\sigma^2$$ and $$ \mu $$. For the vague prior, we can use:

$$ p(\mu, \sigma^2) \propto (\sigma^2)^{-1} $$



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



