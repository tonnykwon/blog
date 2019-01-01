---
title: "2 - Binomial Example Continued"
date: 2018-12-26 23:15:00 -0400
categories: Bayesian
---
In the last post, we talked about the binomial example from Bayesian perspective. We only considered using the uniform prior distribution for $$ \theta $$. It is reasonable to use such non-informative prior when we do not have any knowledge about the $$ \theta $$. <br/>
We consider other prior distribution for the coin tossing experiments. The prior express all plausible parameter values of $$ \theta $$, but it does not need to be concentrated on realistic values. This is because the information about $$ \theta $$ in data usually outweighs the prior knowledge about $$ \theta $$. <br/>
The likelihood of $$ \theta $$ for the coin tossing:
\\[ p(y|\theta) \propto \theta^a(1-\theta)^b \\]
where a and b are the values of the head and tail. <br/>
Now instead of using uniform prior, we suppose the prior density is of the same form of the likelihood. We will parameterize prior density:
\\[ p(\theta) \propto \theta^{\alpha-1}(1-\theta)^{\beta - 1} \\]
which follows a beta distribution with $$ \alpha $$ and $$ \beta $$. These two parameters of the prior distribution is called *hyperparameters*. Then, the posterior density of $$ \theta $$ looks:
\\[ p(\theta|y) \propto \theta^y (1-\theta)^{n-y} \theta^{\alpha-1}(1-\theta)^{\beta-1} \\]
\\[ = \theta^{y+\alpha-1} (1-\theta)^{n-y+\beta-1} \\]
\\[ Beta(\theta | \alpha + y, \beta + n -  y) \\]
The property of the posterior distribution showing the same parametric form as the prior distribution is called *conjugacy*. And, in this example, the beta prior is a conjuage family for the binomial likelihood.

Let's try a beta prior density with hyperparameters $$ \alpha = 10, \beta = 8 $$, which $$ E(\theta) $$ gives approximately 0.55. <br/>
The posterior distribution is proportional to:
\\[ Beta(\theta| 10 + 55, 8 + 100 - 55) \\]
Plotting shows:
``` r
curve(dbeta(x, 65, 53), 0, 1, ylab = "Density") # posterior (informative prior)
curve(dbeta(x,  10, 8), 0, 1, add =TRUE, lty=2) # informative prior
curve(dbeta(x, 56, 45), 0, 1, add =TRUE, col='blue') # posterior (non-informative)
curve(dunif(x, 0, 1), 0, 1, add=TRUE, col="blue", lty=2) # flat prior
```
<p align="center"> 
<img src="../../assets/img/bayesian/2-post-beta.jpg" style="width: 90%">
</p>

As we can see in the plot, with informative knowledge, the posterior density is more concentrated.