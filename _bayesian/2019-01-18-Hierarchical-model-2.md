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

As the margin of errors represents standard errors, we can assume $$ \sigma_j$$ for the normal distribution $$y_j$$ follows is known. Only thing we need to know is the mean of normal, $$ \theta_j $$.

That is:

$$ y_j | \theta_j \sim N(\theta_j, \sigma^2_j) $$



We can also assume that each $$ \theta_1, ..., \theta_7 $$ are different. So, it is convenient to model means to follow normal distribution:

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



