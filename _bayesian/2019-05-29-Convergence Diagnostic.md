---
title: "10 - Convergence Diagnostic"
date: 2019-05-29
categories: Bayesian
mathjax: true
---

We have talked about Markov Chain Monte Carlo methods such as Metropolis-Hastings or Gibbs Sampler. And the key to successfully draw samples from these sampling methods is convergence. There are two main obstacles that hider the sampler from converging. First one is not enough iterations. Before the simulations reach approximate target distribution, it draws samples from starting approximations. Second one is correlations between samples. By the definition of stationary distribution, the proposal distribution should not be variant on $$t$$ and its mean and variance should not change. The followings are methods to deal with these problems:

- Discard early simulations run

- Do multiple Markov Chain with different initial parameters
- Monitor the variation within each chains, and the variation between each chains
- Check correlations between samples(Autocorrelation)



### Discard early simulations run

To eliminate the influence of starting values, we discard samples from early iterations, which we call **burn-in**. The number of burn-in depends on the context.



### Multiples chains with overdispersed initial points

Throughout using multiple chains with different initial parameters, we can see whether the chains are merged. If they are **mixed**, they should have similar means and variances. With one chain alone, it is hard to tell whether the chain is converged.



### Assessing within and between variance

Other than plotting each chains' samples, we can quantify their disparities. Let's say we label each simulation $$i$$ from each chain $$j$$ as $$\psi_{ij}$$ with $$n$$ iterations and $$m$$ chains. We can define between variance and within variance like so:

$$ B = \frac{n}{m-1}\sum_{j=1}^m (\bar \psi - \bar \psi_j)^2 $$, where $$\bar \psi = \frac{1}{m} \sum_{j=1}^m \bar \psi_j $$,   $$\psi_j = \frac{1}{n} \sum_{i=1}^n \psi_{ij}$$

$$ W = \frac{1}{m} \sum_{j=1}^m s^2$$, where $$s^2 = \frac{1}{n-1} \sum_{i=1}^n (\psi_{ij} - \bar \psi_j)^2 $$

The between-sequence variance, $$B$$, measures how much the mean of each chain is away from the overall mean. The within-sequence variance, $$W$$, measures how much each simulation in the chain differs from the mean of the chain. 

Now we can estimate the marginal posterior variance of the chains by $$W$$ and $$B$$.

$$ \hat {var} (\psi \mid y) = \frac{n-1}{n} W + \frac{1}{n}B$$

The variance tends to overestimate, when starting points are overdispersed. It is unbiased eventually when chains are stationary. 

$$\hat R = \sqrt{\frac{\hat{var}(\psi \mid y)}{W} }$$

And $$ \hat R $$ is the **Gelman-Rubin** for $$\psi$$. It tends to be larger than 1, but it approaches to 1 when $$n \rightarrow \infty$$. Based on the book Bayesian Data Analysis, we declare it is convergence when Gelman-Rubin is less than 1.1. But if we take more stringent standard, we can expect Gelman-Rubin to be less than 1.05. When we use Gelman-Rubin, we use data after adaption phase. Also, passing Gelman-Rubin test does not guarantee convergence.



### Autocorrelation

Suppose the Markov chain reached stationarity, and $$\psi_i$$ is the sample from the i iteration. Then autocorrelation at lag $$t$$ is the correlation between two samples between $$\psi$$ and $$\psi_t$$.

$$\rho_t =  \frac{cov(\psi_i, \psi_{i+t})}{\sqrt{var(\psi_i)var(\psi_{i+t})}} $$

If the chain has a high autocorrelation, it means the chain needs to iterate a large number of times to traverse the whole sample spaces of parameters. Thus, we prefer a chain with a low autocorrelation which has **faster mixing**, moving the whole sample spaces of parameters fast. Moreover, by having autocorrelation, we can estimate the effective sample size.



## Code

We will use 2014 life expectancy data of countries to assess MCMC. Suppose the life expectancy of each country is $$y_i$$, and assume it is normally distributed. Then we have,

$$ y_i \mid \theta_i, \sigma_i \sim N(\theta_i, \sigma_i^2)$$

$$\theta_i \sim N(\mu, \tau^2)$$

$$ \mu \sim U(-1000, 1000)$$

$$ \tau \sim U(0, 1000)$$

$$ \frac{1}{\sigma^2} \sim Gamma(1, 0.0001)$$

```R
require(rjags)
data = read.csv("Life_expectancy_dataset.csv", stringsAsFactors = FALSE)
head(data)

  Rank      Country Overall.Life Male.Life Female.Life Continent    y
1    1       Monaco         89.5      85.6        93.5    Europe 89.5
2    2        Japan         85.0      81.7        88.5      Asia 85.0
3    3    Singapore         85.0      82.3        87.8      Asia 85.0
4    4 Macau; China         84.5      81.6        87.6      Asia 84.5
5    5   San Marino         83.3      80.7        86.1    Europe 83.3
6    6      Iceland         83.0      80.9        85.3    Europe 83.0
```



We initialize 4 different chains with overdispersed starting values for hyperpriors.

```R
initial.vals = list(list(mu=100, tau=100, sigmasqyinv = 10),
                    list(mu=100, tau=0.01, sigmasqyinv = 10),
                    list(mu=-100, tau=100, sigmasqyinv = 0.0001),
                    list(mu=-100, tau=0.01, sigmasqyinv = 0.0001))
```



Now we put our hierarchical model into a bug file. Noe that normal distribution of rbug takes precision which is the inverse of sigma.

```R
#lifeexpectancy.bug file
model{
	for(i in length(y)){
		y[i] ~ dnorm(theta[i], sigmasqyinv)
		theta[i] ~ dnorm(mu, 1/tau^2)
	}
	
	mu ~ dunif(-1000, 1000)
	tau ~ dunif(0, 1000)
	sigmasqyinv ~ dgamma(1, 0.0001)
}
```

With model built, we will run the model with 2000 burn-in and draw 5000 samples. And see if the chain converges with methods we learned above.

```R
# jags automatically conducts adaptation for default
m1 = jags.model("lifeexpectancy.bug", data, inits = initial.vals, n.chains= 4, n.adapt = 1000)

# burn-in
update(m1, 2000)
x1 = coda.samples(m1, c("mu", "tau", "sigmasqyinv"), n.iter=5000)
```

Here x1 is the matrix contain the parameters of mu, tau, and sigmasqyinv. As we go over 5000 iterations, there are 5000 samples for each four chains.



Let's see the posterior density of parameters we sampled.

```R
plot(x1, smooth=FALSE)
```

<p align='center'>
    <img src = '../../assets/img/bayesian/10-density.png' style="width: 90%">
    <br/>
    <sub>Density and trace plots of parameters</sub>
</p>

The left column of plots are trace plots of parameters. It shows how the parameters vary over each draw, and its color represent four chains we made. If we look at the trace plots of each parameters $$\mu, \sigma, \tau$$, we can see they are quite stationary over time, and all chains seem to stay at certain point. Density of each parameters look proper too.



Let's use Gelman-Rubin to find out its convergence.

```R
gelman.diag(x1, autoburnin = FALSE)

Potential scale reduction factors:

            Point est. Upper C.I.
mu                   1          1
sigmasqyinv          1          1
tau                  1          1

Multivariate psrf

1
```

The point estimate of each parameters are 1. Since we decided the threshold as 1.05 earlier, we can say they converged.



Now look at the autocorrelation of the first chain.

```R
autocorr.plot(x1[1])
```

<p align='center'>
    <img src = '../../assets/img/bayesian/10-auto.png' style="width: 90%">
    <br/>
    <sub>Autocorrelation plot</sub>
</p>

As the lag increases, the autocorrelation between the samples decrease fast. We can say the chains have fast mixing.





Other than assessing the convergence of the chain, we need to diagnose whether the prior or the posterior are proper too. We will cover this later.



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



