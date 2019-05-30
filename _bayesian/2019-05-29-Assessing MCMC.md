---
title: "10 - Assessing MCMC"
date: 2019-05-29
categories: Bayesian
mathjax: true
---

We have talked about Markov Chain Monte Carlo methods such as Metropolis-Hastings or Gibbs Sampler. And the key to successfully draw samples from these sampling methods is convergence. There are two main obstacles that hider the sampler from converging. First one is not enough iterations. Before the simulations reach approximate target distribution, it draws samples from starting approximations. Second one is correlations between $$p(\theta^t)$$ and $$\theta^s$$. By the definition of stationary distribution, the proposal distribution should not be variant on $$t$$ and its mean and variance should not change. The followings are methods to deal with these problems:

- Do multiple Markov Chain with different initial parameters
- Monitor the variation within each chains, and the variation between each chains



### Multiples chains with overdispersed initial points

Throughout using multiple chains with different initial parameters, we can see whether the chains are merged. If they are **mixed**, they should have similar means and variances. With one chain alone, it is hard to tell whether the chain is converged.



### Assessing within and between variance

Other than plotting each chains' samples, we can quantify their disparities. Let's say we label each simulation $$i$$ from each chain $$j$$ as $$\psi_{ij}$$ with $$n$$ iterations and $$m$$ chains. We can define between variance and within variance like so:

$$ B = \frac{n}{m-1}\sum_{j=1}^m (\bar \psi - \bar \psi_j)^2 $$, where $$\bar \psi = \frac{1}{m} \sum_{j=1}^m \bar \psi_j $$,   $$\psi_j = \frac{1}{n} \sum_{i=1}^n \psi_{ij}$$

$$ W = \frac{1}{m} \sum_{j=1}^m s^2$$, where $$s^2 = \frac{1}{n-1} \sum_{i=1}^n (\psi_{ij} - \bar \psi_j)^2 $$

The between-sequence variance, $$B$$, measures how much the mean of each chain is away from the overall mean. The within-sequence variance, $$W$$, measures how much each simulation in the chain differs from the mean of the chain. 





## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



