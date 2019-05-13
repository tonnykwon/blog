---
title: "6 - Hierarchical Model 2"
date: 2019-01-18
categories: Bayesian
mathjax: true
---

In this post, we will discuss about hierarchical model based on normal distributions where several groups are observed with different means. Let's look at an example of 2016 presidential polls data.



## Data

2016 Presidential polls data were conducted on 2016 for US presidential election. The data consists of three columns; the name of poll, percentage of Clinton lead(y), and margin of error(ME).

```R
data = read.table("polls2016.txt", header=TRUE)
data
       poll y   ME
1    YouGov 4 1.70
2 Bloomberg 3 3.50
3   ABCWaPo 3 2.50
4       Fox 4 2.50
5       IBD 1 3.10
6  Monmouth 6 3.60
7    NBCWSJ 5 2.73
```

With this data, we will build a hierarchical model that allows differences between different polls, but containing somewhat similarity overall.



## Modeling

Let 

$$y_j$$ = Clinton lead in the poll j

$$\sigma_j $$ = Margin of errors in the poll j

$$j  = 1, .., 7$$



Since each poll is conducted respectively, each $$y_1, ..., y_7$$ are independent. Also, we will consider each y is normally distributed.

As the margin of errors represent standard errors, we can assume $$ \sigma_j$$ is known and $$y_j$$ follows a normal distribution. Only thing we need to know is the mean of normal, $$ \theta_j $$.

That is:

$$ y_j | \theta_j \sim N(\theta_j, \sigma^2_j) $$

$$\sigma_j$$ is not conditioned, since it is known.





We can also assume that each $$ \theta_1, ..., \theta_7 $$ are different, because conducted polls came from different media. So, it is convenient to model means to follow normal distribution:

$$ \theta_j | \mu, \tau \sim N(\mu, \tau^2) $$

Here, $$\mu$$ and $$\tau $$ are hyperpriors, hyperparameters of the prior.





For hyperpriors, we will assume noninformative priors:

$$p(\mu) \propto 1, where \  -\infty< \mu < \infty$$

$$ p(\tau) \propto 1, where \ \tau > 0$$

By multiplying both hyperpriors, we can obtain joint distribution:

$$p(\mu, \tau) \propto 1$$

Since the flat priors are improper, exceeding sum of probability 1, it requires to check whether posterior is proper later.





In Directed Acyclic Graph, it looks like:

<p align ='center'>
    <img src = "../../assets/img/bayesian/6-poll-dag.png" style="width: 60%"> <br/>
    <sub>Directed Acyclic Graph for the 2016 polls</sub>
</p>


## Simulation

By usings JAGS in R, we can simulate above hierarchical model, and obtain posterior density. Since JAGS does not allow improper density, we should approximate priors.

Then the whole model is:

$$ y_j | \theta_j \sim N(\theta_j, \sigma_j^2) $$

$$ \theta_j | \mu, \tau \sim N(\mu, \tau^2) $$

$$ \mu \sim U(-1000, 1000) $$

$$ \tau \sim U(0, 1000) $$ 



The JAGS code looks like:

```R
model {
	for( j in 1:length(y)){
		y[j] ~ dnorm(theta[j], 1/sigma[j]^2)
		theta[j] ~ dnorm (mu, 1/tau^2)
	}
	
	mu ~ dunif(-1000, 1000)
	tau ~ dunif(0,1000)
}
```

dunif functions is for uniform function. Note that the normal function, dnorm, takes a precision, not a variance.



After proper burn in process, obtain 10000 samples for each variables, $$\tau, \mu, \theta_j$$.

```R
data$sigma = (data$ME)/2
m1 = jags.model("polls20161.bug", data)
update(m1, 2500)
x1 = coda.samples(m1, c("mu", "tau", "theta"), 10000)
```

Use summary() function to summarize the results of obtained samples.

```R
summary(x1)

Iterations = 3501:13500
Thinning interval = 1 
Number of chains = 1 
Sample size per chain = 10000 

1. Empirical mean and standard deviation for each variable,
   plus standard error of the mean:

          Mean     SD Naive SE Time-series SE
mu       3.720 0.7125 0.007125        0.01788
tau      1.009 0.8602 0.008602        0.04197
theta[1] 3.835 0.6498 0.006498        0.01536
theta[2] 3.542 0.9765 0.009765        0.01947
theta[3] 3.475 0.8422 0.008422        0.01699
theta[4] 3.789 0.8309 0.008309        0.01678
theta[5] 2.959 1.1154 0.011154        0.03767
theta[6] 4.243 1.1259 0.011259        0.03040
theta[7] 4.102 0.9228 0.009228        0.02284

2. Quantiles for each variable:

            2.5%    25%    50%   75% 97.5%
mu       2.34203 3.2910 3.7113 4.122 5.212
tau      0.04113 0.3926 0.8034 1.401 3.267
theta[1] 2.62106 3.4138 3.8058 4.249 5.179
theta[2] 1.42903 3.0125 3.5821 4.098 5.474
theta[3] 1.65211 2.9966 3.5220 4.002 5.088
theta[4] 2.19161 3.2757 3.7589 4.266 5.602
theta[5] 0.32128 2.3521 3.1654 3.730 4.677
theta[6] 2.45446 3.4985 4.0442 4.822 6.967
theta[7] 2.54081 3.4854 3.9864 4.622 6.213
```

The first table of the results shows the means and standard deviations of variables. There are some differences between means of each experiment, $$\theta_j$$, and note that $$\mu$$ is far above 0, indicating positive Clinton lead overall. The second table shows quantiles of each variables.



## General Hierarchical Normal Model

Like the model for the 2016 polls data, for observed data with a different mean and variance for each experiment, we can build simple hierarchical model. Suppose we have different normally distributed $$i$$ data points in each $$j$$ experiments with known variance, then:

$$ y_{ij} | \theta_j \sim N(\theta_j, \sigma^2) $$



We can calculate the sample mean and variance for each experiments:

$$ \bar y_j = \frac{1}{n_j} \sum_{i=1}^{n_j} y_{ij }$$

$$ \sigma_j^2 = \sigma^2 / n_j $$





And now we have:

$$\bar y_j | \theta_j \sim N(\theta_j, \sigma^2_j) $$

Although the assumption of known variances is not strictly true, it is still widely applicable.





We can assume partial conjugacy for the prior $$\theta_j $$ to conveniently build the model:

$$ p(\theta_1, ..., \theta_J | \mu, \tau) = \prod_{j=1}^J N(\theta_j | \mu, \tau^2 ) $$

$$ p(\theta_1, ..., \theta_J) = \int \prod_{j=1}^J [N(\theta_j | \mu, \tau^2)]p(\mu, \tau)d(\mu, \tau) $$

For the hyperprior distribution, $$\mu$$ , we can assign uniform density. This is reasonable because the combined data from all J experiments are quite informative about $$\mu$$.





From above building blocks, we can draw the joint posterior distribution:

$$p(\theta, \mu, \tau |y) \propto p(\mu, \tau) p(\theta|\mu, \tau) p(y|\theta) $$

$$ \propto  p(\mu, \tau) \prod_{j=1}^J N(\theta_j | \mu, \tau^2 ) \prod_{j=1}^J N(\bar y_j |\theta_j, \sigma^2_j) $$





To do:

- how to derive p(mu, tau) maybe



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



