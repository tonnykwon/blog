---
title: "3 - Mean-Only Normal"
date: 2019-01-02
categories: Bayesian
---

## Normal Distribution
Normal distribution is major statistical model. Thanks to central limit theorem, many statistical models justify using the normal model. So, in this post, we will talk about Bayesian modeling using normal distribution.

Consider a variable from a normal distribution of parameter $$ \mu$$ and $$ \sigma^2 $$:

$$ y | \mu, \sigma^2 \sim N(\mu, \sigma^2) $$

Then the density of y: 

$$ p(y|\mu, \sigma^2) = \frac{1}{\sqrt{2\pi \sigma^2}}^{e^{-\frac{1}{2\sigma^2}(y-\mu)^2}} $$

In case we have multiple observations of y, which are independent , it forms:

$$ y_1, ..., y_n | \mu, \sigma^2 \sim iid \, N(\mu, \sigma^2)$$

Then normal sampling joint density:

$$ p(y | \mu, \sigma^2) = \prod^n_{i=1} p(y_i | \mu, \sigma^2) $$

$$ \propto \prod^n_{i=1}  \frac{1}{\sqrt{\sigma^2} }{exp(-\frac{1}{2\sigma^2}(y_i-\mu)^2}) $$

$$ \propto \frac{1}{(\sigma^2)^{n/2}}{exp(-\frac{1}{2\sigma^2}\sum^n_{i=1}(y_i-\mu)^2}) $$

In the Bayesian analysis, we only need to know only proportionality of parameters. So on second line, we can drop two pi portion.  And then on third line, we combine product to so product of $$ \sqrt{\sigma^2} $$ becomes  $$ (\sigma^2)^{n/2} $$. On exponential portion , the product of exponential is the common factor multiplies the sum of $$ (y_i - \mu)^2 $$ factors.

Note:

$$ \sum^n_{i=1} (y_i - \mu)^2 = \sum^n_{i=1} ((y_i - \bar y) + (\bar y - \mu) )^2 $$

$$ = \sum^n_{i=1} (y_i - \bar y)^2 + \sum^n_{i=1} (\bar y - \mu)^2 -2 \sum^n_{i=1}(y_i -\bar y)(\mu - \bar y)$$

$$ = (n-1)s^2 + n(\mu -\bar y)^2, where \; s^2 = \frac{1}{n-1} \sum^n_{i=1}(y_i - \bar y)^2$$

Since cross term of first line equation disappear, we can divide the equation into two pieces.

Then the likelihood can be expressed as:

$$ p(y | \mu, \sigma^2) \propto \frac{1} {(\sigma^2)^{n/2}} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)) $$

$$ \propto \frac{1}{(\sigma^2)^{n/2}} exp(- \frac{(n-1s^2)} {2\sigma^2}) exp(-\frac{n}{2\sigma^2}(\mu - \bar y)^2) $$

Notice only last exponential part depends on $$ \mu$$.



## Mean-Only Normal Example

As a simple case, we consider observation y from a normal distribution parameterized by a mean  $$\theta$$  and  $$\sigma^2$$ , where  $$\sigma^2$$  is known. The only unknown parameter is $$ \mu $$, and we call it $$ \theta $$ for convenience. <br/>

As we derived from the above section, only the last part of likelihood depends on $$ \mu$$. If $$\sigma^2$$ is constant, we can drop all other factors:

$$ p(y| \mu) \propto exp(-\frac {n}{2\sigma^2} (\mu- \bar y)^2) $$



Here we use flint water crisis data. It first started in 2014 that water source for the city of Flint, Michigan claimed to contain  high levels of contaminants due to the lack of treatment. If over 10% of homes sampled have lead levels over 15 parts per billion(15 ppb), action is required according to the law. <br/>
The data set consists of 271 observations, and three lead readings:

- Initial draw
- After flushing 45 seconds
- After flushing 2 minutes

``` r
Flintdata = read.csv("Flintdata.csv", row.names = 1)
head(Flintdata)

  SampleID ZipCode Ward FirstDraw After45Sec After2Min Notes
1        1   48504    6     0.344      0.226     0.145      
2        2   48507    9     8.133     10.770     2.761      
3        4   48504    1     1.111      0.110     0.123      
4        5   48507    8     8.007      7.446     3.384      
5        6   48505    3     1.951      0.048     0.035      
6        7   48507    9     7.200      1.400     0.200  
```

``` r
hist(Flintdata$FirstDraw)
abline(v=15, col='red')
```
<p align="center"> 
<img src="../../assets/img/bayesian/3-flint-hist.png" style="width: 100%">
</p>

If we darw histogram for first draw of water and add line for the limit(15 ppb), it is difficult to tell whether 10% of the samples exceed the limit.
``` r
summary(Flintdata$FirstDraw)
summary(log(Flintdata$FirstDraw))
  Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
  0.344   1.578   3.521  10.646   9.050 158.000 
   Min. 1st Qu.  Median    Mean 3rd Qu.    Max. 
-1.0671  0.4561  1.2587  1.4029  2.2024  5.0626 
```
The summary of the first draw shows some right skewness in data, where the mean is much larger than the mean. By looking at log scale, we see far less skewness, and apply normal model to the data.
``` r
hist(log(Flintdata$FirstDraw))
abline(v=log(15), col='red')
```
<p align="center"> 
<img src="../../assets/img/bayesian/3-log-flint.png" style="width: 100%">
</p>
### Modeling

Let's consider the variable y(the first draw of tap water) follow a normal distribution with variance known:

$$ p(y | \mu) \propto exp(-\frac{n}{2\sigma^2}(\mu - \bar y)^2) $$

And we assume the conjugate prior form:

$$ \mu \sim N(\mu_0, \tau_0^2) $$

$$ p(\mu) exp(\propto exp(-\frac{1}{2\tau_0^2}(\mu - \mu_0)^2) ) $$

Then according to Bayes' rule:

$$ p(\mu | y) \propto p(\mu) p(y | \mu) $$

$$ \propto exp(-\frac{1}{2\sigma^2}(\mu - \mu_0)^2 -\frac{n}{2\sigma^2}(\mu - \bar y)^2) $$

We can notify the first term from prior and the second term from likelihood is in quadratic form with $$ \mu $$. So, the product of two form is a normal:

$$p(\mu | y) \propto exp(-\frac{1}{2\sigma^2}(\mu - \mu_0)^2 -\frac{n}{2\sigma^2}(\mu - \bar y)^2) $$

$$ =  exp(- \frac{\mu^2 -2\mu\mu_0 + \mu_0^2}{2\tau_0^2} -\frac{n(\mu^2 -2\mu\bar y + \mu^2)}{2\sigma^2}) $$

$$ = exp(-\frac{\mu^2}{2}(\frac{1}{\tau_0^2} + \frac{n}{\sigma^2} ) +\mu(\frac{\mu_0}{\tau_0^2} +\frac{n\bar y}{\sigma^2}) - (\frac{\mu_0^2}{2\tau_0^2} + \frac{n\mu^2}{2\sigma^2}) ) $$

And define it as:

$$ = exp(- \frac{1}{2\tau^2_n} (\mu^2 - \mu \mu_n +\mu_n^2) ) $$ 

Then,

$$ \mu_n = \frac{\frac{1}{\tau_0^2} \mu_0 + \frac{n}{\sigma^2}\bar y}{\frac{1}{tau_0^2} + \frac{n}{\sigma^2}} \; \; \;\;\;\; \frac{1}{\tau_n^2} = \frac{1}{\tau_0^2}+\frac{n}{\sigma^2} $$

So the normal mean-only model has posterior:

$$ \mu | y \sim N (\mu_n, \tau_n^2) $$

Note that the reciprocal of a variance is called *precision*, and the posterior mean $$ \mu_n $$ is the weighted average of prior mean $$ \mu_0$$ and sample mean $$\bar y$$ with precision.

### Back to Flint Data

```r
(n = nrow(Flintdata))
(ybar = mean(log(Flintdata$FirstDraw)))
(s.2 = var(log(Flintdata$FirstDraw)))

[1] 271
[1] 1.402925
[1] 1.684078
```

So n = 271, $$ \bar y \approx 1.4 $$, and $$ s^2 \approx 1.684 $$.

As we assumed the variance is known, we use $$s^2$$ as $$ \sigma^2$$. For hyper parameters $$ \mu_0$$ and $$ \tau_0^2 $$ ,we use 1.1 and 1.684. Given information, we can compute posterior.

```r
sigma.2 = s.2
mu0 = log(3)
tau.2.0 = sigma.2

# posterior mean
(mun = (mu0/tau.2.0 + n*ybar/sigma.2) / (1/tau.2.0 +n/sigma.2))
[1] 1.401807

# posterior variance
(tau.2.n = 1 / (1/tau.2.0 + n/sigma.2))
[1] 0.006191465
```

$$ E(\mu | y) = \mu_n \approx 1.40 \; \;\;\; var(\mu|y) = \tau_n^2 \approx 0.0062 $$



```r
# posterior density
curve(dnorm(x, mun, sqrt(tau.2.n)), -1, 5, n=1000, ylab="Density")

# prior density
curve(dnorm(x, mu0, sqrt(tau.2.0)), -1, 5, add=TRUE, lty=2)
```

![](..\..\assets\img\bayesian\3-post.png)



Now, we can get posterior central interval using calculated $$\mu_n$$ and $$ \tau_n $$:

$$ \mu_n \pm 1.96 \sqrt(\tau_n^2) $$

```r
(pci = mun+c(1, -1)*1.96*sqrt(tau.2.n))
[1] 1.556031 1.247582

# original scale
exp(pci)
[1] 4.739971 3.481915
```



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.