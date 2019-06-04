---
title: "8 - Canonical Correlation Analysis"
date: 2019-05-31
categories: Machine
mathjax: true
---

In often cases, we want to know the relations between two data about one individual. For instance, we want to know how the image data is related with the word captions of the image. Or we can think about how one's exercises data related to one's health data. We can put this pair data as

$$ p_i = {x_i, y_i}$$

where $$x_i$$ is a $$d_x$$ dimensional vector and $$y_i$$ is a $$d_y$$ dimensional vector.

Canonical Correlation Analysis finds the linear projections of $$x$$, a stack of $$x_i$$, and $$y$$, a stack of $$y_i$$, that maximizes correlations between two projected variables. That is, if we projects $$x$$ into $$u$$ space, and $$y$$ in to $$v$$ space, we are to maximize

$$ \rho = Corr(u^T x, v^Ty)$$

which is canonical correlation. In other words, we find a $$(u^Tx_i, v^Ty_i)$$, a canonical variate pair, which is the direction that accounts for much of the covariance between the two sets of variables. It is similar to PCA in a sense that PCA looks for the direction that accounts for much of variance in the data set.  



Let's say the variance of each sets of variables are

$$ Var(x) = \Sigma_{xx} $$

$$ Var(y) = \Sigma_{yy}$$

$$ Cov(x, y) = \Sigma_{xy}$$

Then, 

$$ Var(u^Tx) = u^T \Sigma_{xx} u$$

$$Var(v^T y) = v^T \Sigma_{yy} v$$

$$Cov(u^Tx, v^Ty) = u^T \Sigma_{xy} v $$

Then the correlation between two projected sets is

$$Corr(u^Tx, v^Ty) = \frac{u^T\Sigma_{xy}v}{\sqrt{u^T\Sigma_{xx}u} \sqrt{v^T\Sigma_{yy}v} }$$

We transform this problem to

Max $$u^T \Sigma_{xy} v$$  subject  to $$ c= u^T \Sigma_{xx} u$$ and $$d = v^T \Sigma_{yy} v$$

Then, we have Lagrangian

$$ u^T \Sigma_{xy} v - \lambda_1(u^T \Sigma_{xx} u - c) - \lambda_2 (v^T \Sigma_{yy} v - d) $$

Now we have to solve

$$ \nabla_u =  \Sigma_{xy} v - \lambda_1\Sigma_{xx}u = 0$$

$$ \nabla_v =  \Sigma_{xy}^Tu - \lambda_2 \Sigma_{yy}v =0 $$



If we suppose variables $$x$$ and $$y$$ do not contain redundant variables, all variance and covariance matrix is full rank and therefore invertible.

$$\Sigma_{xx}u = (1/ \lambda_1) \Sigma_{xy}v$$

$$ u = (1/\lambda_1) \Sigma_{xx}^{-1} \Sigma_{xy} v $$

If we substitute $$u$$ with above equation,

$$(1/\lambda_1) \Sigma_{xy}^T \Sigma_{xx}^{-1} \Sigma_{xy} v - \lambda_2 \Sigma_{yy}v  = 0$$

$$ \Sigma_{yy}^{-1} \Sigma_{xy}^T \Sigma_{xx}^{-1} \Sigma_{xy} v = \lambda_1 \lambda_2 v  $$

and as $$ v = \lambda_1 \Sigma_{xy} \Sigma_{xx} u$$,

$$ \Sigma_{xy}^T u - \lambda_1 \lambda_2 \Sigma_{yy} \Sigma_{xy} \Sigma_{xx}u = 0$$

$$ \Sigma_{xx}^{-1} \Sigma_{xy}^{-1} \Sigma_{yy}^{-1} \Sigma_{xy}^T u = \lambda_1 \lambda_2 u$$

These forms look quite similar. It is form of the eigenvalue decomposition, which is $$ Su = \lambda u$$.

Hence, $$u$$ and $$v$$ are the eigenvectors. From the equations of Lagrangian we can notice

$$ u^T \Sigma_{xy}v = u^T(\lambda_1 \Sigma_{xx}u) = (\lambda_2 v^T \Sigma_{yy}) v$$

Then we can substitute the correlation

$$ Corr(u^Tx, v^Ty) = \frac{u^T \Sigma_{xy}v}{\sqrt{u^T \Sigma_{xx}u} \sqrt{v^T \Sigma_{yy}v}} = \sqrt{\lambda_1} \sqrt{\lambda_2}$$

It means that the eigenvectors with the highest eigenvalues give the the highest correlation values $$\rho$$. There are $$min(d_x, d_y) $$ directions.



## Code

Now we will find canonical correlation and variates about psychological data and academic data.

```python
import pandas as pd
import numpy as np

# read data
data =  pd.read_csv("https://stats.idre.ucla.edu/stat/data/mmreg.csv")
# 600 university freshman
data.head()

	locus_of_control	self_concept	motivation	read	write	math	science	female
0	-0.84	-0.24	1.00	54.8	64.5	44.5	52.6	1
1	-0.38	-0.47	0.67	62.7	43.7	44.7	52.6	1
2	0.89	0.59	0.67	60.6	56.7	70.5	58.0	0
3	0.71	0.28	0.67	62.7	56.7	54.7	58.0	0
4	-0.64	0.03	1.00	41.6	46.3	38.4	36.3	1
```

"locus of control", "self concept", and "motivation" variables are psychological variables and "read", "write", "math",  and "science" are academic variables, with higher values indicating higher abilities of each area.



```python
# center mean
p = data.iloc[:,:-1]
mean = np.mean(p)
std = np.std(p)
center_data = p - mean

# divide data into psychological variables(X) and academic variables(Y)
X = center_data.iloc[:,:3] # 600 x 3
Y = center_data.iloc[:, 3:] # 600 x 4
```

We center the data and divide into each variables set, psychological(X) and academic(Y).



```python
# sigma is block of sigma xx, sigma xy, sigma yx, and sigma yy
sig = np.cov(center_data.T)
sigx = sig[:dx, :dx]
sigy = sig[dx:, dx:]
sigxy = sig[:dx, dx:]
```

And get covariance of each and between variables, $$\Sigma_{xx}, \Sigma_{yy}, \Sigma_{xy}$$.



```python
# get inverse matrix of simga xx, and sigma yy
invx = np.linalg.inv(sigx)
invy = np.linalg.inv(sigy)

# get eigenvalues and eigenvectors of matrix
umat = invx.dot(sigxy).dot(invy).dot(sigxy.T)
lambda12, u = np.linalg.eig(umat) # u: 3 x 3
idx = np.argsort(-lambda12)
u = u[idx]
vmat = invy.dot(sigxy.T).dot(invx).dot(sigxy)
_, v = np.linalg.eig(vmat) # v: 4 x 4
```

Now by using eigenvalue decomposition, we get $$ \Sigma_{xx}^{-1} \Sigma_{xy}^{-1} \Sigma_{yy}^{-1} \Sigma_{xy}^T u = \lambda_1 \lambda_2 u$$, and$$ \Sigma_{yy}^{-1} \Sigma_{xy}^T \Sigma_{xx}^{-1} \Sigma_{xy} v = \lambda_1 \lambda_2 v  $$  to evaluate $$u, v$$ and $$\lambda1, \lambda2$$.



```python
# correlation scores
np.sqrt(lambda12)

array([0.44643648, 0.15335902, 0.02250348])
```

Since $$ Corr(u^Tx, v^Ty) = \frac{u^T \Sigma_{xy}v}{\sqrt{u^T \Sigma_{xx}u} \sqrt{v^T \Sigma_{yy}v}} = \sqrt{\lambda_1} \sqrt{\lambda_2}$$, the canonical correlations between first canonical variate pair $$ u^Tx, v^Ty$$ are about 0.446.



```python
u1 = u[:, 0]
v1 = v[:, 0]
utx = u1.dot(X.T)
vty = v1.dot(Y.T)
np.corrcoef(utx, vty)

array([[ 1.        , -0.44643648],
       [-0.44643648,  1.        ]])
```

As we can see, the canonical variate pair has high correlations. Psychological variables comes out to be effective at predicting academic variables and vice versa.



## Reference

- CS498 Applied Machine Learning by Professor Trevor Walker
