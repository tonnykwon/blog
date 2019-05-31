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

$$ \Sigma_{xy} v - \lambda_1\Sigma_{xx}u = 0$$

$$ \Sigma_{xy}^Tu - \lambda_2 \Sigma_{yy}v =0 $$



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



## Reference

- CS498 Applied Machine Learning by Professor Trevor Walker
