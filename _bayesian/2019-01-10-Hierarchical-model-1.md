---
title: "5 - Hierarchical Model I"
date: 2019-01-10
categories: Bayesian
mathjax: true
---

Many statistical applications have multiple parameters related to each other. In some cases, similar parameters apply to different subsets of data with a common prior distribution with its *hyperparameters*. In such cases, we can build a hierarchical model. 

Let's start with simple a rat tumor example. Suppose there are 71 multiple experiments on rat whether they develop tumors or not. So, 

$$n_j = $$ total number of rats in experiment j

$$ y_j = $$ the number of rats developed a tumor in the experiment j

$$ \theta_j =$$ tumor probability in experiment j

If it was about only one experiment, we can simulate binomial distribution like we did on previous posts. However, there are several experiments.

Now, we can assume several options:

- Option 1: Same probability of all experiments that all $$ \theta_j $$ are equals
- Option 2: Probabilities are completely unrelated that $$\theta_j$$ are all different
- Option 3: Compromise: probabilities are distinct but related



### Option 1

Now experiments share all same probability of developing tumor, that is:

$$ y_1 + ... + y_{71} | \theta \sim Bin(n_1 +... + n_{71} , \theta) $$

Disadvantages:

- No way to assess variability among experiments



### Option 2

Assume every experiments have completely distinct probability, that:

$$ y_j |\theta_j \sim Bin(n_j, \theta_j) $$

Disadvantages:

- Limited by precision of each individual experiment due to size
- No Bayesian way to answer overall probabaility
- No way to predict new result since it assumes different probability



### Option 3

Assume there are separate probabilities, but also assume they are related:

$$ \theta_1, ..., \theta_{71} \sim \;\;? $$

Then we can derive $$\theta_j$$ random from a common population distribution.

Advantages:

- Can define overall probability
- Can assess variability among experiments
- Can improve estimation of $$ \theta_j$$ through more informative prior(pooling)

In the experiments, no information is available about $$ \theta_j $$. In other words, we cannot distinguish each $$ \theta_j $$, and this symmetry is called *exchangeability* that their joint distribution is invariant to permutations of the indexes(1, .., J).





## Modeling

Now we found out option 3, building a hierarchical model, has advantages over other options, it is time to model $$ \theta_j $$ as if they are independent from same distribution.

So based on hierarchical model, this model has two levels:

Lower level:

$$ y_j | \theta_j \sim Bin(n_j, \theta_j) $$

Higher level:

$$ \theta_j \sim Beta(\alpha, \beta) $$

As we discussed in the previous post, natural parametric choice of a prior is Beta distribution. In this hierarchical model, $$ \alpha$$ and $$\beta $$ are hyperparameters, parameters chosen before data. We can either guess, use prior information, estimate from data, or give a prior distribution to choose hyperparameters. If we are to give them a prior distribution, the prior of hyperparameters is a **hyperprior**.

When choosing hyperprior for hyperparameters, there is no natural conjugate choice. Hyperpriors need to be convenient to specify and not too informative that does not affect the result too much.

A simple distribution on positive values is the exponential distribution. As exponential curve decays only on positive values, and considering $$ \alpha $$ and $$\beta $$ are positive, we can use exponential distribution:

$$ \alpha, \beta \sim Expon(\lambda) $$

That is:

$$ p(\alpha, \beta) = p(\alpha)p(\beta) = \lambda e ^{-\lambda \alpha} \lambda e ^{-\lambda \beta}, where \;\; \alpha > 0, \; \beta > 0 $$

By choosing lambda close to zero, we can get less informative prior.



## Graphical Model

**Directed Acyclic Graph(DAG)**  can show hierarchical models graphically with edges and arrows. Each variables are nodes, and connections are edges. The plate indicates that nodes in the plate are vector.

In case of our rat tumor example, our DAG is:

<p align ='center'>
    <img src = "../../assets/img/bayesian/5-rat-dag.png" style="width: 60%"> <br/>
    <sub>Directed Acyclic Graph for the rat tumor exmaple</sub>
</p>


Each edge is from a parent to a child, and a distribution of a child is specified conditionally on its parents only. For instance, the distribution of $$\theta$$ is specified by its parents $$\alpha $$ and $$ \beta $$.



## Simulation

There are many Bayesian simulation software. And we will use JAGS(Just Another Gibbs Sampler), which automates posterior simulation, requiring only a DAG model to be specified.

So our full model is:

$$ y_j | \theta_j \sim Bin(n_j, \theta_j) $$

$$ \theta_j  | \alpha, \beta \sim Beta(\alpha, \beta) $$

 $$\alpha, \beta \sim \; indep. \;\; Expon(\lambda) $$

$$ where \;\; j = 1, ..., J $$



In JAGS, we define a stochastic relation, which is the nodes that are defined in terms of a distribution. So JAGS model looks like:

``` 
model {
  for (j in 1:length(y)){
    y[j] ~ dbin(theta[j], N[j])
    theta[j]~ dbeta(alpha, beta)
  }
  
  alpha ~ dexp(0.001)
  beta ~ dexp(0.001)
}
```

for loop goes through 1 to J on variable theta and y, which are on the plate in DAG. Note that we used length() function. $$ \lambda $$ for exponential distribution is chosen to 0.001, which is quite non informative.

Some properties of JAGS:

- Improper priors are not allowed
- Data values are not deterministic



Now let's simulate the model with R.

```r
d = read.table("rattumor.txt", header=TRUE)
str(d)

'data.frame':	71 obs. of  2 variables:
 $ y: int  0 0 0 0 0 0 0 0 0 0 ...
 $ N: int  20 20 20 20 20 20 20 19 19 19 ...
```

Rat tumor data consists of two variables, the number of rats developed tumor in a experiment, and the total number of rats in the experiment.

We can get naive estimate of $$ \theta_j = y_j / n_j$$ and plot histogram of it:

```r
with(d, hist(y/N, freq=FALSE, xlim=c(0,1)))
```

<p align ='center'>
    <img src = "../../assets/img/bayesian/5-hist.png" style="width: 80%"> <br/>
    <sub>Histogram of theta</sub>
</p>


$$ \theta$$ is within around 0 to 0.4, and skewed to right.

```r
require(rjags)
# d can be list or dataframe
m<- jags.model("rattumor1.bug", d)

Compiling model graph
   Resolving undeclared variables
   Allocating nodes
Graph information:
   Observed stochastic nodes: 71
   Unobserved stochastic nodes: 73
   Total graph size: 216

Initializing model

  |++++++++++++++++++++++++++++++++++++++++++++++++++| 100%
```

First we call 'rjags' packages to use JAGS. Then, we build a model that is specified in "rattumor1.bug" file. The model is same as above. jags.model() function takes the data as list or data frame. Object m defines an iterative random sampling scheme, but not yet contain posterior samples.

```r
# First run it for many iteartions until it becomes reliable
# None of the samples will be used. Discard.
update(m, 2500) # burn-in

# Then run it for many more iterations, storing samples from selected nodes
x <- coda.samples(m, c("alpha", "beta"), n.iter=10000)

|**************************************************| 100%
|**************************************************| 100%
```

update() function runs sampling that would not be used, but for reliability. This process is called 'burn-in' and will be covered in later posts. After 'burn-in', we run more samples with coda.samples(), specifying nodes to sample and the number of iterations.

Object x contains the output of simulation, $$ \alpha $$ and $$ \beta $$.

```r
head(x)

[[1]]
Markov Chain Monte Carlo (MCMC) output:
Start = 3501 
End = 3507 
Thinning interval = 1 
        alpha     beta
[1,] 3.145194 19.83808
[2,] 3.078303 15.53540
[3,] 2.961327 16.26449
[4,] 2.926920 20.06305
[5,] 3.244688 20.32434
[6,] 3.447661 21.33302
[7,] 3.896117 19.64274
```



```r
# 2500 iterations, and 1000 adaptations
summary(x)

Iterations = 3501:13500
Thinning interval = 1 
Number of chains = 1 
Sample size per chain = 10000 

1. Empirical mean and standard deviation for each variable,
   plus standard error of the mean:

        Mean    SD Naive SE Time-series SE
alpha  3.576 1.721  0.01721          0.217
beta  21.357 9.994  0.09994          1.271

2. Quantiles for each variable:

       2.5%    25%    50%    75%  97.5%
alpha 1.545  2.445  3.128  4.169  8.598
beta  9.285 14.694 18.870 24.944 49.155
```

summary() function explains most of the sampling result. Iterations indicate the iterations number used. Burn-in iterated over 1:2500 and another 1000 iterations are used for adaptations. And we can get estimates of posterior mean and quantiles of variables. From them. we can summarize results:

$$ E(\alpha | y) \approx 3.5 $$

$$ \sqrt{var(\alpha|y)} \approx 1.7 $$

$$ E(\beta | y) \approx 21 $$

$$ \sqrt{var(\beta|y)} \approx 10 $$



```r
require(lattice)

# Posterior marginal distribution
densityplot(x)
```

<p align ='center'>
    <img src = "../../assets/img/bayesian/5-post-dense.png" style="width: 100%"> <br/>
    <sub>Histogram of theta</sub>
</p>


Using `lattice` package, we can estimate posterior density of $$ \alpha $$ and $$ \beta $$.



If we plot $$ \alpha $$ and $$ \beta $$:

```r
alpha =as.matrix(x)[,"alpha"]
beta =as.matrix(x)[,"beta"]
plot(alpha, beta, pch=".", cex=2)
```

<p align ='center'>
    <img src = "../../assets/img/bayesian/5-cor.png" style="width: 100%"> <br/>
    <sub>Histogram of theta</sub>
</p>


It seems that both parameters have some correlations which violates the assumption that $\alpha$ and $\beta$ are independnet. It seems that exponential hyperpriors does not diffuse enough, and needs to try different prior.



When we plot log($\alpha / \beta $) and log($\alpha +\beta$) instead:

```R
plot(log(alpha/beta), log(alpha+beta), pch=".", cex=2)
```

<p align ='center'>
    <img src = "../../assets/img/bayesian/5-new-cor.png" style="width: 100%"> <br/>
    <sub>Histogram of permutations of alpha and beta</sub>
</p>



## Reference

- STAT 578: Advanced Bayesian Modeling by Professor Trevor Park
- Gelman, A., Stern, H. S., Carlin, J. B., Dunson, D. B., Vehtari, A., & Rubin, D. B. (2013). Bayesian data analysis. Chapman and Hall/CRC.



