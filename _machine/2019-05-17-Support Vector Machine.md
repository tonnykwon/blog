---
title: "3 - Support Vector Machine"
date: 2019-05-17
categories: Machine
mathjax: true
---

Support Vector Machine is a linear model that can separate classes with a hyperplane. Suppose we are given a feature vector $$x$$, and we need to classify the class y either 1 or -1. the linear model forms:

$$ f(x) = w^Ta +b $$

Here $$ w $$ is a hyperplane and b is a real number.

For the convenience, we assume the data is linearly separable. Then, there would be numerous or even infinite numbers of decision boundaries or $$w$$ and $$b$$ that can separate the classes. So, SVM uses the concept of *margin*, which is the distance between the decision boundary and samples to find out the hyperplane. And in the below decision boundary plot, the margin is the distance between $$H_0$$ and $$H_1$$.

<p align='center'>
	<img src = '../../assets/img/machine/3-svm.png' style='width:70%'>
    <br/>
    <sub>Decision Boundary</sub>
</p>

Here $$+$$ signs are positives where $$y$$ should be 1, and $$-$$ signs are negatives where $$y$$ should be -1. And the data on the yellow line, margin, are support vectors like the one of the data vector $$\vec a$$. Using the hyperplane $$\vec w$$ and the real number $$b$$, we classify data vector:

$$ \vec w \cdot \vec a +b \geq 1 $$, then $$y_i = 1$$

$$ \vec w \cdot \vec a + b \leq -1 $$, then $$y_i = -1$$

In more simplified form,

$$y_i(\vec w \cdot \vec a + b) \geq 1$$

$$ y_i (\vec w \cdot \vec a +b) -1 \geq 0$$



The dot product of vector $$\vec w$$ on vector $$\vec a$$ gives the magnitude of $$\vec w$$  projected on $$\vec a$$ multiplied by the magnitude of $$\vec w$$. That is, if  this magnitude is bigger than the certain point, we assign positive,  and negative otherwise.



## Loss Function

<p align="center">
    <img src = "../../assets/img/machine/3-svm-margin.png" style="width:70%">
    <br>
    <sub>Margin between hyperplanes</sub>
</p>

Now we need to find a vector $$w$$ and a real number $$b$$ which gives us the largest margin between the support vectors. Suppose $$a_0$$ is a point in the hyperplane $$\vec w \cdot \vec a +b = -1 $$. To get margin, all we need to calculate is the distance between $$a_0$$ and the hyperplane $$\vec w \cdot \vec a +b = 1 $$. And let's denote this distance as $$r$$. Then, the point in the hyperplane $$\vec w \cdot \vec a +b = 1 $$ would be:

$$ a_0 + r \frac {w}{\| w \|} $$

since $$\frac {w}{\| w \|} $$ is a unit normal vector and $$r$$ is the distance between the hyperplanes. Now we have:

$$ \vec w(a_0 + r \frac{\vec w}{\|w\|}) +b = 1$$

Expanding the equation gives:

$$\vec w \vec a_0 + r \frac{\vec w \vec w}{\|w\|}+b = 1 $$

$$  \vec w \vec a_0 + r \| w\| +b = 1 $$

$$ \vec w \vec a_0  + b = 1 - r \|w\| $$

$$ 1 = 1 - r \| w\| $$

$$ r = \frac{2} {\| w \|}$$

Therefore, we need to maximize the margin $$\frac {2} {\|w\|} $$. This problem is equivalent to minimizing the value of $$\|w\|/2 $$. Now we need to minimize $$\|w \|$$, while fulfilling the condition:

$$ y_i (\vec w \cdot \vec a +b) -1 \geq 0$$



We can transform this into quadratic programming that is to minimize $$ \|w\|^2  $$. Now this is a constrained optimization, where we can solve it by the Lagrangian multiplier method. Since it becomes quadratic programming, the cost surface is paraboloid with a single global minimum. We can rewrite this:

$$ L(\vec w, b, \lambda) = \frac{1}{2} \| w\|^2  - \sum_{i=1}^N \lambda_i (y_i(w \cdot a_i +b)-1 )$$

where $$\lambda = (\lambda_i, ..., \lambda_N) $$

Setting the derivative of $$L(\vec w, b, \lambda)$$ with regard to $$\vec w$$ and $$b$$ to zero, we get:

$$ \frac {dL}{dw} = w - \sum_{i=1}^N \lambda_i y_i a_i$$

$$ w = \sum_{i=1}^N \lambda_i y_i a_i $$

$$ 0 = \sum_{i=1}^N \lambda_i y_i $$



Instead of solving this original problem, we will solve the dual of the problem. This is because, this way let us just compute the inner products of $$a_i, a_j$$, which is important when we solve non-linearly separable problems, while the solution is the same as the solution of the original problem.

Then what is dual problem? instead of minimizing over $$w$$ and $$ b$$ subject to constraints involving $$\lambda$$, maximize over $$\lambda$$, the dual variable, subject to the relations we obtained above.

The solution must satisfy two relations:

$$ w = \sum_{i=1}^N \lambda_i y_i a_i $$

$$ 0 = \sum_{i=1}^N \lambda_i y_i $$

We first substitute $$w$$ and $$b$$, and get rid of dependency on those variables. Then, we solve for the $$\lambda $$ by differentiating the dual problem with regard to $$\lambda$$, setting it zero.

Dual problem:

$$ \underset{\lambda_i} {\operatorname{max}} L(\lambda_i) = \sum_i^N \lambda_i - \frac  {1}{2}\sum_i^N \sum_j^N \lambda_i \lambda_j y_i y_j k(a_i \cdot a_j) - b \sum_{i}^N \lambda_i y_i$$

subject to $$\sum_i^N \lambda_i y_i = 0 $$, and $$ \lambda_i \geq 0 $$

Since the last term is zero, we obtain:

$$\underset{\lambda_i} {\operatorname{max}} L(\lambda_i) = \sum_i^N \lambda_i - \frac  {1}{2}\sum_i^N \sum_j^N \lambda_i \lambda_j y_i y_j k(a_i \cdot a_j)$$







## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
- Bishop, C. M. (2006). *Pattern recognition and machine learning*. springer.
