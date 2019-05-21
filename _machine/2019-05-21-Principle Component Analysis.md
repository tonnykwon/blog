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

Then, we diagonalize the covariance matrix of $$x$$, which is:

$$ U^T \operatorname{Covmat}(x) U = \Lambda$$

Recall that $$U$$ is orthonormal matrix that is a generalization of rotations and reflections. 





## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
- Bishop, C. M. (2006). *Pattern recognition and machine learning*. springer.
