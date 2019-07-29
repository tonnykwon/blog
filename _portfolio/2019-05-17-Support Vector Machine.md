---
title: "3 - Support Vector Machine"
date: 2019-05-17
categories: Machine
mathjax: true
---

Support Vector Machine is a linear model that can separate classes with a hyperplane. Suppose we are given a feature vector $$a$$, and we need to classify the class y either 1 or -1. the linear model forms:

$$ f(x) = w^T a +b $$

Here $$ w $$ is a hyperplane and b is a real number.

For the convenience, we assume the data is linearly separable. Then, there would be numerous or even infinite numbers of decision boundaries or $$w$$ and $$b$$ that can separate the classes. So, SVM uses the concept of *margin*, which is the distance between the decision boundary and samples to find out the hyperplane. And in the below decision boundary plot, the margin is the distance between $$H_0$$ and $$H_1$$.

<p align='center'>
	<img src = '../../assets/img/machine/3-svm.png' style='width:70%'>
    <br/>
    <sub>Decision Boundary</sub>
</p>

Here $$+$$ signs are positives where $$y$$ should be 1, and $$-$$ signs are negatives where $$y$$ should be -1. And the data on the yellow line are support vectors like the one of the data vector $$\vec a$$. Using the hyperplane $$\vec w$$ and the real number $$b$$, we classify data vector:

$$ \vec w \cdot \vec a +b \geq 1 $$, then $$y_i = 1$$

$$ \vec w \cdot \vec a + b \leq -1 $$, then $$y_i = -1$$

In more simplified form,

$$y_i(\vec w \cdot \vec a + b) \geq 1$$

$$ y_i (\vec w \cdot \vec a +b) -1 \geq 0$$



The dot product of vector $$\vec w$$ on vector $$\vec a$$ gives the magnitude of $$\vec w$$  projected on $$\vec a$$ multiplied by the magnitude of $$\vec w$$. That is, if  this magnitude is bigger than the certain point, we assign positive,  and negative otherwise.



## Margin

<p align="center">
    <img src = "../../assets/img/machine/3-svm-margin.png" style="width:70%">
    <br>
    <sub>Margin between hyperplanes</sub>
</p>

Now we need to find a vector $$w$$ and a real number $$b$$ which gives us the largest margin between the support vectors. Suppose $$a_0$$ is a point in the hyperplane $$\vec w \cdot \vec a +b = -1 $$ as in the above plot. To get margin, all we need to calculate is the distance between $$a_0$$ and the hyperplane $$\vec w \cdot \vec a +b = 1 $$. And let's denote this distance as $$r$$. Then, the point in the hyperplane $$\vec w \cdot \vec a +b = 1 $$ would be:

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



## Lagrangian Multiplier Method

We can transform this into quadratic programming that is to minimize $$ \|w\|^2  $$. Now this is a constrained optimization, where we can solve it by the Lagrangian multiplier method. Since it becomes quadratic programming, the cost surface is paraboloid with a single global minimum. We can rewrite this into:

$$ L(\vec w, b, \lambda) = \frac{1}{2} \| w\|^2  - \sum_{i=1}^N \lambda_i (y_i(w \cdot a_i +b)-1 )$$

where $$\lambda = (\lambda_i, ..., \lambda_N) $$

Setting the derivative of $$L(\vec w, b, \lambda)$$ to zero with regard to $$\vec w$$ and $$b$$, we get:

$$ \frac {dL}{dw} = w - \sum_{i=1}^N \lambda_i y_i a_i$$

$$ w = \sum_{i=1}^N \lambda_i y_i a_i $$

$$ 0 = \sum_{i=1}^N \lambda_i y_i $$

Now we know how to calculate $$\vec w $$, the decision vector, which came out to be linear combinations of lambda, class, and data vectors. That is, if we solve for $$\lambda$$, we can solve for $$ \vec w$$.



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

Now we can get $$\lambda$$ by calculating derivative of L with regard to $$\lambda$$. With the result of $$\lambda$$, we can get $$w$$ too.



## Hinge Loss

Assume $$\gamma_i = w \cdot a + b$$. And function $$C(\gamma_i, y_i)$$ compares $$\gamma_i$$ with $$y_i$$. Then the overall cost function looks like:

$$ \frac{1}{N} \sum_{i=1}^N C(\gamma_i, y_i)$$

To define the specific cost function we need to consider three cases. First when $$\gamma_i$$ and $$y_i$$ have different signs, the C must have large magnitude. And C should be much larger, when the magnitude of $$\gamma_i$$ gets large.

Second case is when $$\gamma_i$$ and $$y_i$$ have the same signs, but the magnitude of $$\gamma_i$$ is small. Since the magnitude of $$\gamma_i$$ is small, it may not classify other nearby points correctly. In this case, C should be small, but not zero.

Third case is when $$\gamma_i$$ and $$y_i$$ have the same signs, and the magnitude of $$\gamma_i$$ is also big. C can be zero in this case.

The hinge loss satisfies above all cases. It takes:

$$ C(\gamma_i, y_i) = max(0, 1-y_i \gamma_i) $$



Beside $$C$$, we use regularization for preventing overfitting. As a result, the final form of our loss function would be:

$$ \underset{w, b}{\operatorname {min}} \lambda \|w\|^2 /2 +  \frac{1}{N} \sum_{i=1}^N C(\gamma_i, y_i) $$

$$\lambda$$ here is regularization parameter, not Lagrangian multiplier.



## Stochastic Gradient Descent

To obtain $$\theta = (w, b) $$ that minimizes the loss, we will use stochastic gradient descent.

$$ g(\theta) = [(1/N) \sum_{i=1}^N g_i(\theta)] + g_0(u)$$

where $$g_0(u) = \lambda(w^T w)/2 $$ and $$g_i(\theta) = max(0, 1-y_i\gamma_i)$$.

First choose batch size $$N_b$$, and step size $$\eta$$, which is typically $$ \frac{m}{s+n}$$, where s = number of steps per season, m = number of variables, and n = number of data.

Split the training dataset into a training part, and a validation part. For the training part, get gradients:

$$ p ^{(n)} = - \frac{1}{N_b} (\sum_i \nabla g_i (\theta^{(n)}) ) - \lambda u^{(n)}$$

Then update parameters:

$$ u ^{(n+1)} = u^{(n)} + \eta p^{(n)}$$

Finally evaluate the model and parameters by computing accuracy on the validation part.



The gradients for $$\vec w $$ is:

$$ \lambda w $$ if $$y_i (w^T a_i +b) \geq 1 $$

$$\lambda w - y_i$$ otherwise.



For the gradient $$b$$,

0 if $$y_i (w^T a_i +b) \geq 1 $$

$$-y_i$$ otherwise.



## Code

Here is the function code for svm.

```python
def svm(a, b, x, y, reg = 1e-2, n=1000, m=0.05, epoch = 1):
    accuracies = []

    # select batch
    train, _, labels, _ = train_test_split(x, y, test_size=300)

    # predict and concat with zero column
    pred = (np.dot(train, a) + b)*labels
    pred0 = np.vstack((1-pred, np.zeros(pred.shape)) ).T

    # calculate total loss
    loss = np.mean(np.max(pred0, axis=1))+ reg*np.dot(a, a.T)/2

    # count positive, negative numbers for repeat
    pos = np.sum(pred>=1)
    neg = np.sum(pred<1)

    # calculate gradient
    # gradientA: the graident for weight
    # graidentB: the graident for bias
    gradientA = np.zeros((pred.shape[0], len(a) ) )
    gradientB = np.zeros(pred.shape)
    gradientA[pred>=1] = np.repeat(reg*a, pos).reshape(-1,5)
    gradientA[pred<1] = (reg*a-np.multiply(labels.reshape(-1,1), train))[pred<1]
    gradientB[pred<1] = -labels[pred<1]

    # step size
    alpha = m/(epoch+n)

    # update coef
    gradA = np.mean(gradientA, axis=0)
    gradB = np.mean(gradientB)
    a -= alpha*gradA
    b -= alpha*gradB

    return (a,b, accuracies, loss)
```


To do:

- Soft margin SVM
- Kernel Trick



## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
- Bishop, C. M. (2006). *Pattern recognition and machine learning*. springer.
