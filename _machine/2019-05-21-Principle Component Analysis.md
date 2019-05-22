---
title: "4 - Principle Component Analysis"
date: 2019-05-21
categories: Machine
mathjax: true
---

Principal Component Analysis is a feature extraction method. It is useful for removing noise and avoiding the curse of dimension. Before we get into PCA, let's look how to transform high dimension data.



## Transformation of Data

First way of transforming is affine transformation. Suppose we have d-dimensional data $$x$$. By multiplying matrix $$A$$ and adding vector $$b$$, we form an new dataset $$m$$. That is:

$$ m = Ax + b $$

Some of interesting properties are:

$$\operatorname{mean}({m}) = A \operatorname{mean}({x})+b$$

$$\operatorname{Covmat}({m}) = A \operatorname{ Covmat}({x})A^T $$



Next one is diagonalization. Suppose there is a $$d $$ X $$d$$ symmetric matrix, S. Then we can form,

$$ S u = \lambda u $$,

where $$u$$ is a d by 1 vector, and $$\lambda$$ is a scalar. Here $$u$$ is refereed as **eigenvector** of $$S$$ and $$\lambda$$ is the **eigenvalue**. There can be d distinct eigenvectors and eigenvalues, and the stacked eigenvectors can be represented as $$U = [u_1, u_2, ..., u_d]$$, which is orthonormal matrix. Then we can rewrite diagonalization:

$$ SU = U \Lambda $$

And now we can transform matrix S to a diagonal form:

$$ U^T S U = \Lambda $$



Note that orthonormal matrix, $$U$$, is a generalization of rotations and reflections. In other words, the inner product of $$U$$ to other matrix does not change angle and lengths.

Suppose  $$ m = Ux$$, then

$$m^Tm = x^T U^T U x = x^T I x = x^T x $$

Like so, it does not change of length. Also, suppose unit vector $$x$$ and $$y$$. We have the cosine of angle between two vectors:

$$<y, x>  = y^T x$$

$$ <U \cdot y, U \cdot x> = (U \cdot y)^T (U \cdot x) = y^T \cdot U^T \cdot x \cdot U = y^T x $$

That is, the angle between two does not change.



## Principal Component Analysis

First with affine transformation, we can move the data vectors $$x$$ to center, which is zero mean.

$$ m_i = x_i - \operatorname{mean}(x) $$

Then, we diagonalize the covariance matrix of $$m$$, which is:

$$ U^T \operatorname{Covmat}(m) U = U^T \operatorname{Covmat}(x)U =  \Lambda$$

Recall that $$U$$ is orthonormal matrix that is a generalization of rotations and reflections. 

Now, we can transform original data to new data $$r$$:

$$r_i = U^T (x_i - \operatorname{mean}(x))$$



Good things of new data $$r$$ is that it has zero mean, and its covariance matrix is diagonal.

$$ \operatorname{Covmat}(r) = \operatorname{Covmat}(U^T x)$$

$$ = U^T \operatorname{Covmat}(x) U $$

$$ = \Lambda$$

Here we assume the diagonal values of $$\Lambda$$ is in descending order for convenience, which also implies corresponding eigenvectors of $$U$$ is ordered.

Then what is good about it? This means that every pairs of column or row vectors in $$r$$ has covariance 0 and so has correlation 0. 

So the each eigenvalues of $$\operatorname{Covmat}(x)$$ or of the covariance matrix of $$r$$ corresponds to the variance of $$x$$. Now using only subset of eigenvalues or components with the highest variance, we can reconstruct $$x$$. In that way, we can preserve the variance of the data and remove noise. Suppose the newly projected data in $$s$$- dimension($$s<d$$) is $$p$$. That is:

$$ p_i = u_i (x_i - mean(x))$$

Then reconstructed data would be:

$$\hat x_i = U p_i +mean(x)$$

$$ \hat x_i = \operatorname {mean}(x) + \sum_{j=1}^s[u^T_j (x_i - mean(x))]u_j $$



## Code

Now we can build PCA using python.

```python
def pca(ndata, n_comp=2, cdata=None):
    # use noiseless data
    if cdata is not None:
        data = cdata
    else:
        # use noisy data
        data = ndata
    
    # mean and covariance
    mean = np.array(np.mean(data))
    X = data-mean
    covmat= np.cov(X.T)
    # diagonalization
    eigval, eigvec = np.linalg.eig(covmat)
    idx = np.argsort(-eigval)
    eigval = eigval[idx]
    eigvec = eigvec[:,idx]
    
    if n_comp==0:
        # use data mean
        mean = mean.reshape(-1,4)
        recon = np.repeat(mean, ndata.shape[0], axis=0)
    else:
        # reconstruct
        X = ndata - mean
        r = np.dot(X, eigvec[:,0:n_comp])
        recon = np.dot(r, eigvec[:,0:n_comp].T)+mean
    

    return recon, eigval, eigvec
```

Ignore first noisy, noiseless data part. As we covered above, we first get mean and extract mean from data. Then get covariance matrix of data. After diagonalizing the covariance matrix, we reconstruct data.







## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
- Bishop, C. M. (2006). *Pattern recognition and machine learning*. springer.
