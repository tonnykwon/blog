---
title: "8 - Markov Chain Monte Carlo"
date: 2019-05-21
categories: Bayesian
mathjax: true
---

Markov Chain Monte Carlo(MCMC) is a general method of drawing samples from a target distribution with relatively faster speed to converge and its capability of simulating various distributions. In addition, compared to previously introduced Monte Carlo Simulation such as Importance sampling and rejection sampling, it is capable of simulating complicated distribution, and easy to implement with fair reliability. Some of drawbacks are difficulty in assessing it convergence and accuracy. Although specific methods differ by each algorithms, general steps are following:

1 - Set initial parameter values

2 - Evaluate the unnormalized posterior

3 - Propose a new parameter value

4 - Evaluate newly proposed posterior

5 - Accept or reject new parameter

Repeat 3-5 until converge.



## Markov Chain

Before we go through MCMC, what is Markov Chain? Suppose we have a sequence of random variables or state $$\theta_1, \theta_2, ..., \theta_n$$. We can use chain rule to represent the joint probability of these variables.

$$ p(\theta_1, ..., \theta_n) =  p(\theta_1) \cdot p(\theta_2 \mid \theta_1) \cdot p(\theta_3 \mid \theta_2,  \theta_1) \cdot ... \cdot p(\theta_n \mid \theta_{n-1}, ..., \theta_1) $$

Markov assumption is that the variable $$\theta_n$$ is independent of $$\theta_1, ..., \theta_{n-2}$$ given the value $$\theta_{n-1}$$. Then we have,

$$p(\theta_1, ..., \theta_n) = p(\theta_1) \cdot p(\theta_2 \mid \theta_1) \cdot p(\theta_3 \mid \theta_2) \cdot ... \cdot p(\theta_n \mid \theta_{n-1})$$

This Markov property helps converging to the target distribution. This is because the region of high probability tends to be connected.



Transition distribution is the conditional distribution of $$\theta_n$$ given $$\theta_{n-1}$$:

$$ T(\theta_n \mid \theta_{n-1})$$

If the transition distribution does not depend on time $$n$$, then MC is time-invariant. This means the draws have certain stationary distribution regardless of its first draw.



```R
transition = matrix(c(0.7, 0.2, 0.1, 
                      0.1, 0.3, 0.6,
                      0.8, 0, 0.2), c(3,3),
                    byrow=TRUE)
```

Suppose we have three states, and below matrix is transition matrix. Each row corresponds to the each state. For instance, if we look at the first row(0.7, 0.2, 0.1), the probability of state one goes to the state one at next step is 0.7. And the probability of state one goes to the state two is 0.3. Now we want to know $$p(\theta_{n+h} \mid \theta_n)$$. If h is 2,

```R
transition %*% transition

     [,1] [,2] [,3]
[1,] 0.59 0.20 0.21
[2,] 0.58 0.11 0.31
[3,] 0.72 0.16 0.12
```



```R
station = transition
for(i in 1:5){
  station = station%*%transition
}
station

         [,1]     [,2]     [,3]
[1,] 0.615707 0.175932 0.208361
[2,] 0.613326 0.176283 0.210391
[3,] 0.616168 0.175120 0.208712

station = transition
for(i in 1:10){
  station = station%*%transition
}
station
          [,1]      [,2]      [,3]
[1,] 0.6153858 0.1758247 0.2087895
[2,] 0.6153764 0.1758259 0.2087977
[3,] 0.6153879 0.1758214 0.2087907
```

If we look at more distance future when h =5 or 10, the transition distribution appears to converge. That is, the state we are currently in is not important in determining future. This is the case for the continuous variable. This is called **unique limit distribution**, where the chain has forgotten its past. Using Markov Chain helps converging to this unique limit distribution.



## Gibbs Sampler

Gibbs sampling is one of MCMC method to sample posterior distribution. The idea is to generate posterior samples from the conditional posterior(or full conditional) distribution of each variable while other variables are fixed. Followings are the steps of Gibbs sampler:

Choose starting value $$\theta_0$$

For $$t=1, 2, ...$$

 1. Set $$\theta^* = \theta^{t-1}$$

 2. For $$ j= 1, ..., d$$

    ​	Replace $$\theta_j^*$$ with a sample from $$p(\theta_j \mid \theta_{-j}^*, y)$$

	3. Let $$\theta^t = \theta^*$$

Continue until it converges when the sample values have same distribution as if they are sampled from the true posterior joint distribution.

Note that the early samples are not representative of actual posterior distribution, since we randomly assigned initial values. Therefore, we discard these samples, and the discarded iterations are called 'burn-in' period(Mentioned about it when first using RGibbs). However, the theory of MCMC guarantees that the stationary distribution by Gibbs sampler algorithm is the target joint posterior.

The drawback of Gibbs sampler is that when parameters have high posterior correlation, its efficiency is very low.



## Metropolis Algorithm

The Metropolis-Hasting algorithm is a general term for MCMC which is useful for simulating posterior distribution. Gibbs sampler is a one of special case of Metropolis-Hasting. First we go through basic model Metropolis algorithm. 

Steps are following:

- Define a random walk on the parameter space or *jumping distribution*(proposal distribution) $$J_t(\theta^* \mid \theta^{t-1}) $$. The jumping distribution should be symmetric, which is $$ J_t(\theta_a \mid \theta_b) = J_t(\theta_b \mid \theta_a) $$. This includes $$Uniform(\theta - \delta, \theta + \delta)$$ and $$Normal(\theta, \delta^2)$$.

- Calculate the ratio of the densities,

  $$ r = \frac{p(\theta^* \mid y)}{p(\theta^{t-1} \mid y)}$$

- Accept new parameter with probability of $$min(r, 1)$$. Otherwise reject the new parameter.

If $$r > 1$$, it means the proposal has higher posterior density value, and it is always accepted. On the other hand, when $$r < 1 $$, it means the proposal has lower posterior density value, and it is accepted with the probability of $$r$$.

Note that normalizer in the ratio cancels out:

$$ r = \frac{p(\theta^*) p(y \mid \theta^*) }{p(\theta^{t-1})p(y \mid \theta^{t-1})}$$



### Normal Example

Using Metropolis, we will simulate following Bayesian model:

$$ y_1, ..., y_n \mid \mu, \sigma^2 \sim iid \operatorname{N}(\mu, \sigma^2)$$

$$ \mu \sim N(\mu_0, \tau_0^2) $$

$$ \sigma^2 \sim Inv-\chi^2(\gamma_0, \sigma_0^2) $$

Parameters $$\theta = (\mu, sigma^2)$$ is in two-dimensional real space.



For proposal distribution $$J(\theta^* \mid \theta)$$, we will use

$$ \theta^* \sim N(\theta, pI)$$

where $$I$$ is 2 by 2 identity matrix, and $$p > 0 $$.



Since it is normal distribution is symmetric, it is Metropolis algorithm. Then the ratio would be:

$$ r = \frac{p(\mu^*, (\sigma^2)^* \mid y)}{p(\mu^{t-1}, (\sigma^2)^{t-1} \mid y)} = \frac{p(\mu^*)p((\sigma^2)^*) p(y \mid \mu^*, (\sigma^2)^*)}{p(\mu^{t-1})p((\sigma^2)^{t-1}) p(y \mid \mu^{t-1}, (\sigma^2)^{t-1})} $$

Recall the likelihood:

$$p(y \mid \mu, \sigma^2) \propto \frac{1} {(\sigma^2)^{n/2}} exp(-\frac{1}{2\sigma^2}((n-1)s^2 + n(\mu - \bar y)^2)), \;\;where \; s^2 = sample \; variance $$



We are going to use the Flint water data, where $$y_i$$ is log of first-draw lead level. The R codes are from lecture STAT598 ABM.

```R
data = read.table("flint-data-simple.txt", sep='\t', header = TRUE)
n = nrow(data)
y = log(data$FirstDraw)
s.2 = var(y)
ybar = mean(y)

# set for convenience
mu0 = 1.10
tau.2.0 = sigma.2.0 = 1.17
nu0 = 1
```

We first get simple statistics, and set some variables for convenience. That is,

$$ \mu_0 = 1.1, \tau_0^2 = \sigma_0^2 =1.17, \gamma_0 = 1 $$



```R
# inverse chi square
dinvchisq = function(x, df, scale){
  if(x>0)
    ((df/2)^(df/2) / gamma(df/2)) * scale^df * x^(-(df/2+1))*
    exp(-df*scale/(2*x))
  else 0
}

# normal likelihood
likelihood = function(mu, sigma.2){
  sigma.2^(-n/2) * exp(-(n-1)*s.2/(2*sigma.2) )*exp(-n/(2*sigma.2)*(mu - ybar)^2)
}

# ratio
ratio = function(mu.prop, sigma.2.prop, mu.old, sigma.2.old){
  dnorm(mu.prop, mu0, sqrt(tau.2.0)) * dinvchisq(sigma.2.prop, nu0, sigma.2.0) * likelihood(mu.prop, sigma.2.prop) / (dnorm(mu.old, mu0, sqrt(tau.2.0)) * dinvchisq(sigma.2.old, nu0, sigma.2.0)*likelihood(mu.old, sigma.2.old))
}
```

Then we defined inverse chi square density function, likelihood function, and ratio function for future use.

```R
n.sim = 1000
mu.sim = numeric(n.sim)
sigma.2.sim = numeric(n.sim)
accept.prob = numeric(n.sim - 1)

rho = 0.01

## setting start value
mu.sim[1] = 2
sigma.2.sim[1] = 2.5
```

Now set up 1000 iterations(n.sim) and make empty vector for simulated mean and sigma, and acceptance rate.

```R
for(t in 2:n.sim){
  # proposal mu and sigma
  mu.prop = rnorm(1, mu.sim[t-1], sqrt(rho))
  sigma.2.prop = rnorm(1, sigma.2.sim[t-1], sqrt(rho))
  
  # calculate ratio
  accept.prob[t-1] =
    min(ratio(mu.prop, sigma.2.prop, mu.sim[t-1], sigma.2.sim[t-1]), 1)
  
  # accept
  if(runif(1) < accept.prob[t-1]){
    mu.sim[t] = mu.prop
    sigma.2.sim[t] = sigma.2.prop
  } else{
    # reject
    mu.sim[t] = mu.sim[t-1]
    sigma.2.sim[t] = sigma.2.sim[t-1]
  }
  
}
```

First using proposal ratio, we get new parameters of $$\mu^*$$ and $$(\sigma^2)^*$$. Then by evaluating ratio value, we determine whether accept or not the proposal parameters. If accepted, we draw new samples from newly proposed parameters.

```R
# acceptance rate
mean(accept.prob)
[1] 0.5335643

plot(mu.sim, sigma.2.sim, type="p")
for(i in 1:(n.sim-1)){
  segments(mu.sim[i], sigma.2.sim[i], mu.sim[i+1], sigma.2.sim[i+1])
}
```

As an acceptance rate, we got about 0.53, which is fairy high. And plot of simulated $$\mu$$ and $$\sigma^2$$ seems somewhat stationary.

<p align='center'>
    <img src = "../../assets/img/bayesian/8-metropolis.png" style="width:70%">
    <br/>
    <sub>Simulated parameters for each step</sub>
</p>







## Metropolis-Hasting Algorithm

Sometimes sampling efficiency is better when the proposal distribution is not symmetric. Metropolis-Hasting Algorithm is a lot like Metropolis, but it uses asymmetric proposal distribution.

- Define proposal distribution $$J_t(\cdot \mid \theta^{t-1})$$ and sample proposal $$\theta^*$$

- Calculate the ratio of

  $$ r = \frac{p(\theta^* \mid y)/ J_t(\theta^* \mid \theta^{t-1})} {p(\theta^{t-1} \mid y) / J_t (\theta^{t-1} \mid \theta^*)} $$

- Accept new parameter with probability of $$min(r, 1)$$. Otherwise reject the new parameter.

Unlike Metropolis, Metropolis-Hasting needs evaluation of the proposal density.



Overall the fraction of iterations that are accepted is called **acceptance rate** in both Metropolis and Metropolis-Hasting algorithm. While the proposal highly concentrated around $$\theta$$ may have high acceptance rate, the wide proposal would have low acceptance rate. It normally ranges from 0.23 ~ 0.44. We can tune the proposal scale periodically to get optimal acceptance rate. This process is called **adaptive algorithm**. These adaptation iterations are not exactly a Markov chain. Therefore, we need to discard these iterations, after scales are fixed.



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



