---
title: "4 - Two Parameters Normal"
date: 2019-01-03
categories: Bayesian
---

In the previous post, we used normal sampling distribution assuming variance $$ \sigma^2$$ is known. However, it is more realistic that both $$ \mu$$ and $$ \sigma^2 $$ are unknown. Now we have two unknown parameters, and the posterior is:

$$ p(\theta_1, \theta_2 |y) \propto p(y | \theta_1, \theta_2) p(\theta_1, \theta_2) $$



Recall likelihood from the previous post:

 $$p(y | \mu, \sigma^2)  \propto \frac{1}{(\sigma^2)^{n/2}}{exp(-\frac{1}{2\sigma^2}\sum^n_{i=1}(y_i-\mu)^2})$$

$$\propto \frac{1} {(\sigma^2)^{n/2}} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)) $$



Now all of the factors contain either $$\sigma^2$$ and $$ \mu $$. For the vague prior, we can use:

$$ p(\mu, \sigma^2) \propto (\sigma^2)^{-1} $$

Then, the posterior is:

$$ p(\mu, \sigma^2|y)\propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)) $$

So, we look for prior density that has similar form of likelihood. 



Averaging out over $$ \theta_2$$:

$$ p(\theta_1 | y) = \int p(\theta_1, \theta_2 | y) d\theta_2$$

$$ = \int p(\theta_1 | \theta_2, y) p(\theta_2| y) d\theta_2 $$



To get the marginal posterior distribution of $$ \sigma^2 $$, we need to average out over $$ \mu $$:

$$ p(\sigma^2 |y) \propto \int \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2 + n(\bar y - \mu)^2 ]) d \mu $$

$$ \propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2]) \int exp([n(\bar y - \mu)^2 ]) d \mu  $$





## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



