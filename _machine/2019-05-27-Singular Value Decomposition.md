---
title: "6 - Singular Value Decomposition"
date: 2019-05-27
categories: Machine
mathjax: true
---

We've discussed PCA in previous posts. And the problem of PCA is that it is quite expensive to compute covariance matrix if the data set is in high dimension. So in this post, we will see another way of representing high dimensional data in low dimensional data; Singular Value Decomposition(SVD).



## SVD

Suppose we have data $$X$$, m x p matrix, then the singular value decomposition looks like:

$$ X = U \Sigma V^T$$

where $$U$$ is m x m matrix, $$\Sigma$$ is m x p matrix, and $$V$$ is p x p matrix. Both matrix $$U$$ and $$V$$ are orthonormal, which is $$U U^T = I $$ and $$V V^T = I$$. Ans $$\Sigma$$ is diagonal with non-negative values, which are singular values. we assume these singular values are ordered from the highest to the smallest.

Using the decomposition, we can diagonalize $$X^TX$$ and $$XX^T$$.

$$ X^TX = (U \Sigma V^T)^T(U \Sigma V^T)$$

$$ = V \Sigma^T U^T U \Sigma V^T $$

$$ = V \Sigma^T \Sigma V^T$$

$$XX^T = U \Sigma \Sigma^T U $$



## Relationship between PCA and SVD

Note that the covariance matrix is:

$$ \operatorname{Covmat}(X) = \frac{1}{N}X^TX$$

Using above diagonalization, we get:

$$ \operatorname{Covmat}(X) =  \frac{1}{N}X^T X = V \frac{\Sigma^T \Sigma}{N} V^T  $$

Recall the form of eigenvalue decomposition of covariance matrix:

$$ U^T \operatorname{Covmat}(X)U =  \Lambda$$



Then, we can realize,

$$ \operatorname{Covmat}(X) = V \frac{\Sigma^2}{N} V^T $$

$$ => V^T \operatorname{Covmat}(X)V = \frac{\Sigma^2}{N}$$

$$ => \Lambda = \frac{\Sigma^2}{N}$$

In other words, the right singular vectors $$V$$ are the principal components and $$\Sigma$$ contains corresponding eigenvalues of covariance.



```python
# center mean
mean = np.mean(data)
X = data - mean
n,m = X.shape

# C: covariance matrix
C = np.dot(X.T, X)/n
eigval, eigvec = np.linalg.eig(C)
# SVD
U, Sigma, V = np.linalg.svd(X, full_matrices=False)

# Sigma^2/N equals Lambda(eigval)
np.allclose(np.square(Sigma)/n, eigval)

True
```





## Reference

- CS498 Applied Machine Learning by Professor Trevor Walker
