---
title: "8 - Markov Chain Monte Carlo"
date: 2019-05-21
categories: Bayesian
mathjax: true
---

Markov Chain Monte Carlo(MCMC) is a general method of drawing samples from a target distribution with relatively faster speed to converge and its capability of simulating various distributions.

## Markov Chain

Before we go through MCMC, what is Markov Chain? Suppose we have a sequence of random variables or state $$\theta_1, \theta_2, ..., \theta_n$$. We can use chain rule to represent the joint probability of these variables.

$$ p(\theta_1, ..., \theta_n) =  p(\theta_1) \cdot p(\theta_2 \mid \theta_1) \cdot p(\theta_3 \mid \theta_2,  \theta_1) \cdot ... \cdot p(\theta_n \mid \theta_{n-1}, ..., \theta_1) $$

Markov assumption is that the variable $$\theta_n$$ is independent of $$\theta_1, ..., \theta_{n-2}$$ given the value $$\theta_{n-1}$$. Then we have,

$$p(\theta_1, ..., \theta_n) = p(\theta_1) \cdot p(\theta_2 \mid \theta_1) \cdot p(\theta_3 \mid \theta_2) \cdot ... \cdot p(\theta_n \mid \theta_{n-1})$$

This Markov property helps converging to the target distribution.



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

If we look at more distance future when h =5 or 10, the transition distribution appears to converge. That is, the state we are currently in is not important in determining future. This is the case for the continuous variable.



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



