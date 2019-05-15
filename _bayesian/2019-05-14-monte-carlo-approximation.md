---
title: "7 - Monte Carlo Approximation"
date: 2019-05-14
categories: Bayesian
mathjax: true
---



## Deterministic Method

Suppose we want to evaluate the posterior expectation of a function $$h(\theta)$$. That is:

$$ E(h(\theta)|y) = \int h(\theta)p(\theta|y) d\theta $$

Deterministic numerical integration methods evaluates $$ h(\theta) p(\theta|y) $$ at selected points $$\theta^s $$:

$$ E(h(\theta) |y) = \int h(\theta) p(\theta | y) d\theta \approx \frac {1}{S} \sum_{s=1}^S w_s h(\theta^s) p(\theta^s |y)$$,

where weight $$w_s$$ corresponding to the volume of the space. The more the points $$\theta_s$$ are, the more accurate the accuracy would be. The deterministic numerical method typically has lower variance than simulation numerical method, however, when the dimensions get higher, it becomes intractable to compute integrations. For instance, suppose there are $$s$$ points in $$d$$-dimensions. Then it needs $$s^d$$ integrand evaluations to conduct deterministic numerical methods.



## Simulation Methods

Instead of splitting interval to s points, simulation methods are based on stochastic samples $$\theta^s$$ from the distribution $$p(\theta)$$ and estimating the expectation of function $$h(\theta)$$.

$$ E(h(\theta)|y) = \int h(\theta) p(\theta|y) d\theta \approx \frac{1}{S}\sum_{s=1}^S h(\theta^s) $$

Unlike deterministic methods, simulation methods can be used for high-dimensional distributions.



Let's approximate the integral value of $$xe^x$$:

$$ \int_0^1 xe^xdx  \approx \frac{1}{S} \sum_{s=1}^S x_i e^{x_i}$$



```R
x = runif(1000, 0, 1)
mean(x*exp(x))

0.9788117
```

```R
x = runif(10000, 0, 1)
mean(x*exp(x))

0.999829
```

The integral of $$xe^x$$ equals 1 when analytically calculated. As we can see above R code, simulating 10,000 times are much closer to 1 compared to simulating 1,000 times.



## Rejection Sampling





To do

- Rejection Sampling
- Importance Sampling
- Monte Carlo Error





## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



