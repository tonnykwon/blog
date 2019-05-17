---
title: "7 - Monte Carlo Approximation"
date: 2019-05-14
categories: Bayesian
mathjax: true
---

So far we have computed the posterior distribution by simulating directly from a combinations of standard distributions such as normal, beta, Poisson, and so on. Since $$\theta$$ is almost always continuous, the simulation mostly includes integrations. We could compute the posterior this way because the computations are analytically in closed forms. 

When it comes to high dimensions, or complex models, however, the posterior distribution may not have standard forms and it requires other methods to compute integration.



## Deterministic Method

Suppose we want to evaluate the posterior expectation of a function $h(\theta)$. 

That is:

$$ E(h(\theta)|y) = \int h(\theta)p(\theta|y) d\theta $$

Deterministic numerical integration methods evaluates $$ h(\theta) p(\theta \mid y) $$ at selected points $$\theta^s $$:

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

Rejection sampling is a technique to sample efficiently from a distribution. Rejection sampling is used when the target distribution, $$p(\theta)$$ is known, but so complicated that makes hard to directly sample. Rather, we draw samples from a proposal distribution, let's say $$q(\theta)$$, which is easy to draw samples. We accept the samples that are in the distribution $$p(\theta)$$, while rejecting others. Given enough samples, it will converge to $$p(\theta)$$. It is important that the proposal distribution covers all the target distribution. That is, $$kq(\theta) > p(\theta)$$, where k is s scaling factor.

Following steps are how rejection sampling works:

- Simulate $$ U \sim Unif(0,1) $$

- Simulate the proposal distribution $$\theta \sim q$$

- If $$ U \leq \frac{p(\theta)}{kq(\theta)}$$

  then accept the $$\theta$$, otherwise reject it.

```R
x = seq(-10, 10,0.1)
y = dnorm(x, 1, 1)
y2 = dnorm(x, -2, 2)
px = y+y2
plot(x,px, type = 'l', col='blue')
```

<p align='center'>
    <img src="../../assets/img/bayesian/7-px.png" style="width: 70%">
    <br/>
    <sub>Target distribution</sub>
</p>

Suppose above distribution is the target distribution, $$ p(\theta)$$. It is a mixture of Gaussian: N(1,1) +N(-2,2).

```R
qx = dnorm(x, 0, 3)
plot(x, px, col= "blue", type='l')
lines(x, qx)
legend(7, 0.45, legend= c('px', 'qx'), col = c('black', 'blue'), lty=1)
```

<p align='center'>
    <img src="../../assets/img/bayesian/7-qx.png" style="width: 70%">
    <br/>
    <sub>Proposal distribution</sub>
</p>

If we pick normal distribution with 0 mean and 3 variance as a proposal distribution, we can intuitively know samples from the proposal distribution will not cover the target distribution. Thus, we use a scaling factor, $$k$$, to envelop the target distribution. To do so, we need to get maximum ratio of the target distribution and the proposal distribution.

```R
k = max(px/qx)
plot(x, k*qx, type= 'l')
lines(x,px,col= "blue")
legend(7, 0.45, legend= c('px', 'qx'), col = c('black', 'blue'), lty=1)
```

<p align='center'>
    <img src="../../assets/img/bayesian/7-kqx.png" style="width: 70%">
    <br/>
    <sub>Scaled Proposal distribution</sub>
</p>





To do

- Importance Sampling
- Monte Carlo Error





## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



