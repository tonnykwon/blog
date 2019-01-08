---
title: "4 - Two Parameters Normal"
date: 2019-01-03
categories: Bayesian
---

## Multiparameter Model

In the previous post, we used normal sampling distribution assuming variance $$ \sigma^2$$ is known. However, it is more realistic that both $$ \mu$$ and $$ \sigma^2 $$ are unknown.  In such case where we do not know multiple parameters, first, we get joint posterior distribution of all unknowns. Since in many Bayesian analysis, we are to obtain the marginal posterior distribution of the particular parameter, we integrate over the unknowns that are not interest. Parameters that are required to build model but not in interest called *nuisance parameters*. Here we average out nuisance parameters to get posterior marginal distributions for parameters in interest.



Suppose we have two unknown parameters $$ \theta_1 $$ and $$\theta_2$$, and the posterior is:

$$ p(\theta_1, \theta_2 |y) \propto p(y | \theta_1, \theta_2) p(\theta_1, \theta_2) $$



If we want to find out the posterior distribution of $$ \theta_1$$, we average out $$ \theta_2 $$ by using integral:

$$ p(\theta_1 | y) = \int p(\theta_1, \theta_2 | y) d\theta_2$$

$$ = \int p(\theta_1 | \theta_2, y) p(\theta_2| y) d\theta_2 $$

And as we can see in the equation, the posterior distribution of $$ \theta_1 $$ is the mixture of the conditional distribution given $$ \theta_2 $$, *nuisance parameter*, and the posterior distribution of $$ \theta_2 $$. Considering the expense of computing the integral, we rarely evaluate explicitly. Rather, by simulating from drawing $$ \theta_2 $$ from its marginal posterior distribution and then $$ \theta_1 $$ from its conditional posterior distribution, we compute the posterior distribution.



We apply this method to normal case where we do not know about both $$ \mu $$  and $$ \sigma^2 $$:

$$ p(\mu, \sigma^2 |y) = p(\mu| \sigma^2, y) p(\sigma^2 |y) $$

The posterior distribution is the product of conditional and marginal posterior distributions.





## Non-informative Prior

In the normal only sample, we assumed non-informative prior for mean as:

$$ p(\mu) \propto 1 $$

For variance, we suppose prior:

$$ p(\sigma^2) \propto (\sigma^2)^{-1} $$

Assuming independence between priors $$\sigma^2$$ and $$ \mu $$, we can get joint prior by multiplying:

$$ p(\mu, \sigma^2) \propto (\sigma^2)^{-1} $$



Recall likelihood from the previous post:

 $$p(y | \mu, \sigma^2)  \propto \frac{1}{(\sigma^2)^{n/2}}{exp(-\frac{1}{2\sigma^2}\sum^n_{i=1}(y_i-\mu)^2})$$

$$\propto \frac{1} {(\sigma^2)^{n/2}} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)), \;\;where \; s^2 = sample \; variance $$



Then, the posterior is:

$$ p(\mu, \sigma^2|y) \propto p(\mu, \sigma^2) p(y| \mu, \sigma^2) $$

$$\propto \frac{1}{\sigma^2} \frac{1} {(\sigma^2)^{n/2}} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)) $$

$$\propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)) $$



By analytically computing integral, now we can get posterior marginal density, and conditional posterior density.



## Posterior Marginal Density

To get the marginal posterior distribution of $$ \sigma^2 $$, we need to average out the posterior over $$ \mu $$:

$$ p(\sigma^2 |y) \propto \int \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2 + n(\bar y - \mu)^2 ]) d \mu $$

$$ \propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2]) \int exp([n(\bar y - \mu)^2 ]) d \mu  $$

$$ \propto \sigma^{-n-2} exp(-\frac{1}{2\sigma^2} [ (n-1)s^2])  \sqrt{2\pi \sigma^2/n} $$

$$  \propto (\sigma^2)^{-(n+1)/2} exp(-\frac{(n-1)s^2}{2\sigma^2} )$$

As it is integrating over $$ \mu$$ , we only require to  integral the last exponential part $$ \int exp([n(\bar y - \mu)^2 ]) d \mu $$. Then, it comes out that it is just a simple normal integral. From $$ \sqrt{2\pi \sigma^2 /n} $$  part, only $$ \sigma^2 $$ part depends on parameters, we can get proportionality from it.

The last equation is a scaled inverse-$$ \chi^2 $$ density:

$$ \sigma^2 | y  \sim Inv-\chi^2 (n-1, s^2) $$ 



## Conditional Posterior Density ##

To find joint posterior density, we need to find the conditional posterior density of

 $$ p(\mu | \sigma^2, y) $$

From the joint posterior density above, we eliminate other terms that are constant with respect to $$ \mu $$, and rewrite the term give:

$$ p(\mu | \sigma^2 , y) \propto exp(- \frac{ n(\mu-\bar y)^2)}{2\sigma^2}$$

$$ \propto exp(-\frac{n\mu^2 -2n\bar y \mu} {2 \sigma^2})  $$

$$ \propto exp(- \frac {\mu^2 - 2\bar y \mu} {2\sigma^2/n}) $$

$$ \propto exp(- \frac{(\mu-\bar y)^2}{2\sigma^2/n}) $$

From this result, we can derive that conditional posterior density follows normal distribution with $$ \bar y$$ and $$\sigma^2 / n $$:

$$ \mu | \sigma^2 , y \sim N (\bar y, \sigma^2 /n) $$



Now we can draw samples from the joint posterior distribution: first we draw $$ \sigma^2 $$ from the posterior marginal distribution, which is a scaled inverse chi square, and then draw $$ \mu $$ from above conditional posterior distribution.

Simulate

$$ \sigma^2_{sim} = (n-1)s^2 /X $$

Then simulate

$$ \mu_{sim} \sim N(\bar y, \sigma^2_{sim} /n) $$

Finally we can easily simulate

$$ \hat y \sim N(\mu_{sim}, \sigma^2_{sim}) $$



## Simulation with R

We can continue using flint data for two parameters normal sampling with non-informative priors. We set $$n$$, $$ \bar y $$, $$ s^2 $$ in log scale.

``` r
Flintdata = read.csv("Flintdata.csv", row.names = 1)
(n = nrow(Flintdata))
(ybar = mean(log(Flintdata$FirstDraw)))
(s.2 = var(log(Flintdata$FirstDraw)))

[1] 271
[1] 1.402925
[1] 1.684078
```



Now simulate $$ \sigma^2 $$ , then $$ \mu$$.

``` r
post.sigma.2.sim = (n-1)*s.2/rchisq(1000, n-1)
post.mu.sim = rnorm(1000, ybar, post.sigma.2.sim/n)
```



By using `quantile` function we can find out posterior interval for both parameters(in log scale):

``` r
quantile(post.sigma.2.sim, c(0.025, 0.975))
quantile(post.mu.sim, c(0.025, 0.975))

    2.5%    97.5% 
1.434001 2.010442 
    2.5%    97.5% 
1.390583 1.414756 
```



Now we can sample new observation from our posterior predictive distribution, 

``` r
post.pred.sim = rnorm(1000, post.mu.sim, sqrt(post.sigma.2.sim))

hist(post.pred.sim)
abline(v=log(15), col="red")
```

![](..\..\assets\img\bayesian\4-pred-post.png)



Using posterior samples, we can check the posterior probability of exceeding the limit(15 ppb):

``` r 
mean(post.pred.sim>log(15))

[1] 0.145
```



To do

Conjugate prior for two parameter normal sampling.



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



