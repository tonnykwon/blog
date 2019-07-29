---
title: "11 - Mixture of Gaussian"
date: 2019-07-25
categories: Machine
mathjax: true
---

In the last article, we learned how general Expectation Maximization algorithm works. Here, we will use EM algorithm to use Gaussian Mixture model. The programming assignment is from Bayesian Methods for Machine Learning by National Research University Higher School of Economics.



## Gaussian Mixture Model

Gaussian mixture model assumes each data points comes from a certain Gaussian from several Gaussian. That is, once from which Gaussian the data point would be generated  chosen, the Gaussian generates the data point based on its own parameters such as mean and covariance.

Thus the likelihood would be:

$$p(x_i \mid \theta) = \sum_j^K N(x_i \mid \mu_1, ..., \mu_k, \Sigma_1, ... ,\Sigma_k)$$

Here the $$\pi_j$$ indicates the probability of a random data points generated from j Gaussian. The latent variable $$t_i$$ shows which Gaussian the data point $$x_i$$ is created. For better understanding, we can use directed acyclic graph below.

<p align="center">
    <img src ="../../assets/img/machine/11-gmm-dag.png" style="width:70%">
    <br/>
    <sub>DAG of Gaussian Mixture Model</sub>
</p>

Based on the graph, we can come up with some probability equations:

$$p(t_i =c \mid \theta)  = \pi_c$$

$$p(x_i \mid t_i =c, \theta) = N(x_i \mid \mu_c, \Sigma_c)$$

$$ p(x_i \mid \theta) = \sum_c^k p(x_i \mid t_i = c, \theta )p(t_i = c \mid \theta)$$

$$p(x_i, t_i= c \mid \theta) = p(x_i \mid t_i = c, \theta) p(t_i =c \mid \theta)$$



### Expectation

In **E-step**, we want to find $$q$$, which minimizes KL divergence.

That is, we want to evaluate $$p(t_i \mid x_i, \theta^k)$$. It can be decomposed as

$$ p(t_i = c \mid x_i, \theta) =\frac{p(x_i, t_i = c \mid \theta)}{p(x_i \mid \theta)} = \frac{p(x_i, t_i = c \mid \theta)}{\sum_j^K p(x_i, t_i= j \mid \theta)}$$

$$ \frac{p(t_i = c \mid \theta) p(x_i \mid \theta, t_i = c)}{\sum_{j=1}^k p(t_i =j\mid \theta) p(x_i \mid \theta, t_i = j)}$$

$$ = \frac{ \pi_c N(x_i \mid \mu_c, \Sigma_c) }{\sum_{j=1}^k \pi_j N(x_i \mid \mu_j , \Sigma_j)}$$

$$ = \frac{ \pi_c \frac{1}{(2\pi )^{n/2} \mid \Sigma_c \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) } {\sum_{j=1}^k \pi_j \frac{1}{(2\pi )^{n/2} \mid \Sigma_j \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) } $$

where $$\pi_c$$ indicates the probability of a data point belongs to cluster c, while $$\pi$$ is just mathematical pi. Note that the constant part of pi disappear, then

$$ p(t_i = c\mid x_i, \theta_c) = \frac{\pi_c \mid \Sigma_c \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) }{\sum_{j=1}^K \pi_j \mid \Sigma_j \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) )}$$



### Maximization

In **M-step**, we maximize the lower bound with respect to $$\theta$$, which is maximizing

$$ E_{q} \operatorname{log} p(X, T \mid \theta)$$

<br/>

Start with mean.

$$ \nabla{u_c} L(\theta,q) = \nabla{u_c} \sum_{i=1}^N  q(t_i = c) \operatorname {log} p(x_i \mid t_i =c, \theta) = 0$$

Note that we set $$q(t_i = c)$$ as $$p(t_i = c \mid x_i, \theta)$$, and we can decompose the likelihood given the latent variable and parameters. Then

$$ \nabla_{u_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \operatorname {log} p(x_i \mid t_i = c, \theta)  p(t_i = c \mid \theta)$$

Note that $$p(x_i \mid t_i, \theta) = N(x_i \mid \mu_c, \Sigma_c)$$ and $$p(t_i = c \mid \theta)  = \pi_c$$. Now it becomes

$$ = \nabla_{u_c} \sum_{i=1}^N p(t_i=c \mid x_i, \theta) \operatorname{log} \{ \frac{1}{ 2 \pi^{n/2} \mid \Sigma_c \mid^{1/2}} exp(-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c)) \pi_c \}$$

$$ = \nabla_{u_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{  \operatorname{log}(\frac{\pi_c}{2 \pi^{n/2} \mid \Sigma_c \mid^{1/2}} ) +\operatorname{log} \operatorname{exp}(-1/2 (x_i \ - \mu_c)^T \Sigma_c^{-1} (x_i -\mu_c)) \}$$

$$= \nabla_{u_c} -\frac{1}{2} \sum_{i=1}^N p(t_i = c \mid x_i, \theta )\{ (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) \} $$

since the first log term does not depend on $$\mu_c$$.

$$ = -\frac{1}{2} \sum_{i=1}^N p(t_i =c \mid x_i, \theta) \nabla_{u_c}\{ (x_i-\mu)^T \Sigma_c^{-1}(x_i - \mu_c) \} $$

$$ = \frac{1}{2} \sum_{i=1}^N p(t_i =c \mid x_i, \theta) \{ (\Sigma_c^{-1} + (\Sigma_c^{-1})^T) (x_i - \mu_c) \} $$

since $$\nabla_x x^T A x = (A + A^T)x $$

$$ = \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{ (\Sigma_c^{-1}) (x_i - \mu_c) \}$$

as the covariance matrix is symmetric, so is its inverse. And we multiply $$\Sigma_c$$ to both sides.

$$ = \sum_{i=1}^N p(t_i = c \mid x_i, \theta) (x_i - \mu_c)$$

$$ \sum_{i=1}^N p(t_i = c \mid x_i, \theta)x_i = \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \mu_c$$

$$ \mu_c = \frac{\sum_{i=1}^N p(t_i = c \mid x_i, \theta)x_i }{ \sum_{i=1}^N p(t_i = c \mid x_i, \theta)} $$

 

Note that 

$$ p(t_i = c \mid x_i, \theta_c) = q(t_i=c) \frac{p(t_i = c) p(x_i, \theta_c \mid t_i = c)}{\sum_{j=1}^k p(t_i =c) p(x_i, \theta_c \mid t_i = c)}$$

$$ = \frac{ \pi_c N(x_i \mid \mu_c, \Sigma_c) }{\sum_{j=1}^k \pi_j N(x_i \mid \mu_j , \Sigma_j)}$$

$$ = \frac{ \pi_c \frac{1}{(2\pi )^{n/2} \mid \Sigma_c \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) } {\sum_{j=1}^k \pi_j \frac{1}{(2\pi )^{n/2} \mid \Sigma_j \mid^{1/2}} \operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) } $$

$$ = \frac{\mid \Sigma_c \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) }{\sum_{j=1}^K \mid \Sigma_j \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) )}$$

<br/>

Now for the covariance,

$$ \nabla_{\Sigma_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \operatorname {log} p(x_i \mid t_i = c, \theta)  p(t_i = c \mid \theta)$$

$$ = \nabla_{\Sigma_c} \sum_{i=1}^N p(t_i=c \mid x_i, \theta) \operatorname{log} \{ \frac{1}{ 2 \pi^{n/2} \mid \Sigma_c \mid^{1/2}} exp(-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c)) \pi_c \}$$

$$ = \nabla_{\Sigma_c} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{ \operatorname{log} (\frac{1} {\mid \Sigma_c \mid^{1/2}}) - 1/2 (x_i -\mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) \}$$

$$  = -\frac{1}{2} \sum_{i=1}^N p(t_i = c \mid x_i, \theta) \{ \frac{\partial log \mid \Sigma_c \mid}{ \partial \Sigma_c} +\frac{\partial}{\partial \Sigma_c} (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) \} $$

First we evaluate the derivative of log determinant,

$$ \frac{\partial \operatorname{log} \mid \Sigma_c \mid}{\partial \Sigma_c} = \frac{1}{\mid \Sigma_c \mid} \frac{\partial \mid \Sigma_c \mid}{\partial \Sigma_c}$$

$$ = \frac{1}{\mid \Sigma_c \mid} \mid \Sigma_c \mid (\Sigma_c^{-1})^T $$

as $$ \frac{\partial \mid X \mid}{ \partial X} = \mid X \mid (X^{-1})^T$$

$$ = \Sigma_j^{-1}$$

And for the second part of the derivative,



$$\Sigma_c = \frac{\sum_i^N p(t_i = c \mid x_i, \theta) (x_i - \mu_c) (x_i - \mu_c)^T } {\sum_{i=1}^N p(t_i = c \mid x_i, \theta)}$$



<br/>

Lastly for $$\pi_c$$,

$$ \pi_c = \frac{\sum_{i=1}^N q(t_i = c)}{N} $$



## Codes

For **E-step**, we set q function equal to the posterior t probability function,

$$ p(t_i = c\mid x_i, \theta_c) = \frac{\pi_c \mid \Sigma_c \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_c)^T \Sigma_c^{-1} (x_i - \mu_c) ) }{\sum_{j=1}^K \pi_j \mid \Sigma_j \mid^{-1/2}\operatorname{exp} (-1/2 (x_i - \mu_j)^T \Sigma_j^{-1} (x_i - \mu_j) )}$$

```python
from scipy.stats import multivariate_normal as mvn

def E_step(X, pi, mu, sigma):
    """
    Expectation step in gmm
    Input:
    X: (N x d), data points
    pi: (C), mixture component weights 
    mu: (C x d), mixture component means
    sigma: (C x d x d), mixture component covariance matrices
    
    Returns:
    gamma: (N x C), probabilities of clusters for objects
    """
    N = X.shape[0]
    C = pi.shape[0]
    d = mu.shape[1] 
    gamma = np.zeros((N, C)) 

    for c in range(C):
        gamma[:,c] = pi[c]*mvn.pdf(X, mu[c,:], sigma[c,])
    
    gamma /= np.sum(gamma, axis=1)[:, np.newaxis]
    
    return gamma
```



In **M-step**, we update parameters

$$ \mu_c = \frac{\sum_{i=1}^N p(t_i = c \mid x_i, \theta)x_i }{ \sum_{i=1}^N p(t_i = c \mid x_i, \theta)} $$
$$\Sigma_c = \frac{\sum_i^N p(t_i = c \mid x_i, \theta) (x_i - \mu_c) (x_i - \mu_c)^T } {\sum_{i=1}^N p(t_i = c \mid x_i, \theta)}$$
$$ \pi_c = \frac{\sum_{i=1}^N q(t_i = c)}{N} $$

```python
def M_step(X, gamma):
    """
    Maximization step in GMM
    Input:
    X: (N x d), data points
    gamma: (N x C), distribution q(T)  
    
    Returns:
    pi: (C)
    mu: (C x d)
    sigma: (C x d x d)
    """
    N = X.shape[0]
    C = gamma.shape[1] # number of clusters
    d = X.shape[1]

    # mu
    mu = (X.T.dot(gamma) / np.sum(gamma, axis=0)).T
    
    # sigma
    sigma = np.zeros((C, d, d))
    for c in range(C):
        xmu = X-mu[c,:]
        gamma_diag = np.diag(gamma[:,c])
        sigma[c,:] = xmu.T.dot(gamma_diag).dot(xmu)
        sigma[c,:] /= np.sum(gamma[:,c], axis=0)

    # pi
    pi = np.sum(gamma, axis=0) / N
    

    return pi, mu, sigma
```



Now we compute expected value of lower bound. We use this value for checking whether it reaches minimum.

```python
def compute_vlb(X, pi, mu, sigma, gamma):
    """
    Inputs :
    X: (N x d)
    gamma: (N x C)  
    pi: (C)
    mu: (C x d)
    sigma: (C x d x d)
    
    Returns value of variational lower bound, which is L(theta, q)
    """
    N = X.shape[0] # number of objects
    C = gamma.shape[1] # number of clusters
    d = X.shape[1] # dimension of each object

    loss = 0
    second = 0
    for c in range(C):
        loss += np.sum(gamma[:,c]*(np.log(pi[c])+mvn.logpdf(X, mu[c,:], sigma[c,:])))
        second += np.sum(gamma[:,c]*np.log(gamma[:,c]))
    
    loss -= second
    
    return loss
```

Finally we define training function. Here we iterate E and M steps, checking convergence. Since the initial point affects the convergence, we initializes different points.

```python
def train_EM(X, C, rtol=1e-3, max_iter=100, restarts=10):
    '''
    restarts: random initialize
    rtol: check optimization
    max_iter: number of iterations
    
    X: (N, d), data points
    C: int, number of clusters
    '''
    N = X.shape[0]
    d = X.shape[1]
    best_loss = None
    best_pi = None
    best_mu = None
    best_sigma = None


    for _ in range(restarts):
        try:
            # inti variables
            mu = np.random.rand(C,d)
            pi = np.random.rand(C)
            pi /= np.sum(pi)
            sigma = np.array([np.identity(d) for i in range(C)])
            
            for i in range(max_iter):
              
                # E -step
                gamma = E_step(X, pi, mu, sigma)

                # M step
                pi, mu, sigma = M_step(X, gamma)

                # loss
                loss = compute_vlb(X, pi, mu, sigma, gamma)

                # update if so far best
                if best_loss is None or np.abs(best_loss - loss) > rtol:
                    best_loss = loss
                    best_pi = pi
                    best_mu = mu
                    best_sigma = sigma

        except np.linalg.LinAlgError:
            print("Singular matrix: components collapsed")
            pass

    return best_loss, best_pi, best_mu, best_sigma
```



Now we have 280 data points with 2 dimensions. We are going to cluster data points into 3 groups using Gaussian Mixture Model.

<p align="center">
    <img src ="../../assets/img/machine/11-gmm-data.png" style="width:70%">
    <br/>
    <sub>Data points</sub>
</p>



```python
best_loss, best_pi, best_mu, best_sigma = train_EM(X, C=3, rtol=1e-3, max_iter=100, restarts=10)
gamma = E_step(X, best_pi, best_mu, best_sigma)
labels = gamma.argmax(axis=1)
colors = np.array([(31, 119, 180), (255, 127, 14), (44, 160, 44)]) / 255.
plt.scatter(X[:, 0], X[:, 1], c=colors[labels], s=30)
plt.axis('equal')
plt.show()
```

<p align="center">
    <img src ="../../assets/img/machine/11-gmm-data_clustered.png" style="width:70%">
    <br/>
    <sub>Data points clustered</sub>
</p>





## Reference

- Bayesian Methods for Machine Learning by National Research University Higher School of Economics
-  CS498 Applied Machine Learning by Professor Trevor Walker