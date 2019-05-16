---
title: "2 - Naive Bayes"
date: 2019-05-16
categories: Machine
mathjax: true
---

## Naive Bayes Classifier

Naive Bayes Classifier is a classifier out of a probability model. Assume we already know the posterior probability, p(y|x), where x is a vector with multiple properties, and y is a scalar indicating  specific class. Now we will assign the class y for the data that has highest probability p(y|x). And the issue here is how to find the probability p(y|x).



Applying Bayes' rule gives:

$$ p(y|x) = \frac{p(x|y) p(y)} {p(x)} $$

The post <a href ="https://tonnykwon.github.io/blog/bayesian/2018-12-26-bayesian-statistics">Bayesian Statistics</a> in Bayesian session covers specific of each probability in Bayes' rule.



Now we will assume the features are conditionally independent conditioned on the class of the data, which is why we call this classifier "Naive". Then we get:

$$ p(x |y) = \prod_j p(x^{(j)} | y)  $$

Then the posterior would be:

$$ p(y|x) = \frac{p(x|y) p(y)} {p(x)} $$

$$ =  \frac{(\prod_j p(x^{(j)} | y))   p(y)} {p(x)} $$

$$ \propto (\prod_j p(x^{(j)} | y) ) p(y)$$



Since we only need to know $\underset{y \in Y}{\operatorname{argmax}} p(y|x)$, we do not necessarily know the normalizer $$p(x)$$, which y is not dependent on. By taking the highest proportionality, we can get the class y which is the most probable.

Note that it requires multiplying probabilities. When a large number of probabilities are multiplied, we may end up getting zero due to the underflow of floating point system. Thus, we use log to the probabilities, and now we only need adding them up.

$$log(p(y|x)) \propto \sum_j log(p(x^{(j)}|y)) + log(p(y)) $$

Since the logarithm function is monotonic that  a > b is equivalent to log(a) > log(b), we choose the class which gives the highest value of log probability. It is usual to get p(y) by counting the number of examples with class y, and divide it by the number of whole training examples.

For $p(x^{(j)}|y) $, there are wide range of possible parametric models. If $x^{(j)}$ is continuous, we could use a normal distribution. When $x^{(j)}$ is a count, we could fit a Poisson distribution. If $x^{(j)}$ is a binary variable, we could use a Bernoulli distribution. And we choose parameters of distributions by using maximum likelihood. It turns out these basic forms of distributions work effectively.



## Data

Following dataset is the medical records for Pima Indians(https://www.kaggle.com/kumargh/pimaindiansdiabetescsv). It has several patients condition information and contains whether or not each patient had diabetes or not as a target.

```python
import pandas as pd
import numpy as np
import sklearn.naive_bayes as nb

data = pd.read_csv('pima-indians-diabetes.csv', 
                   header=0, names=['Preganancies', "Glucose", "BloodPressure", "SkinThickness", "Insulin", "BMI", "DiabetesPedigreeFunction", "Age", "Class"])
data.head()
	Preganancies	Glucose	BloodPressure	SkinThickness	Insulin	BMI	DiabetesPedigreeFunction	Age	Class
0	1	85	66	29	0	26.6	0.351	31	0
1	8	183	64	0	0	23.3	0.672	32	1
2	1	89	66	23	94	28.1	0.167	21	0
3	0	137	40	35	168	43.1	2.288	33	1
4	5	116	74	0	0	25.6	0.201	30	0
```

Most of features such as 'Glucose', 'BloodPressure' are continuous, and it is proper to use a normal distribution for modeling likelihood probability p(x|y). And the 'Class' feature is the target variable.

Split dataset into train, and test set. And we will use train set for fitting parameters with maximum likelihood, and then evaluate the model with test set.

```python
# splitting data into train and test set
def data_split(x, ratio=0.8):
    n, m = data.shape
    idx = np.random.rand(n)
    train = x[idx <= ratio]
    test = x[idx > ratio]
    return train, test

train, test = data_split(data)
```



## Modeling

Suppose we have $$x^{(j)} =v $$ and class = k. Using a normal distribution as a parametric model, we get:

$$ p(x^{(j)} = v |y = k) = \frac{1}{\sqrt{2\pi\sigma^2_{jk}}} exp(-\frac{(v-\mu_{jk})^2}{2\sigma^2_{jk}}) $$

The equation for a normal distribution is parameterized by $$\mu_{jk}$$ and $$\sigma^2_{jk}$$. And the maximum likelihood estimate of mean is:

$$\mu_{jk} = \frac{1}{\sum_i \delta(y^{i} = k)} \sum_i x^{(j)} \delta(y^i = k)$$

$$i, j, k$$  indicate each instance, each feature, and each class respectively. $$\delta(x) =1$$ if x true, otherwise 0. It acts like a count function. Basically we get feature j data where the instance is class k, and calculate mean of it.

Then the variance is:

$$ \sigma^2_{jk} = \frac{1}{\sum_i \delta(y^i = k)} \sum_i (x^{(j)}-\mu_{jk})^2 \delta (y^i = k)$$



So the parameters needed for the classifier is 2 X j X k.

Now we can build Naive Bayes classifier.

```python
# naive function
# train: data for training
# test: data for testing
# target: target variable
def naive(train, test, target):
    n = train.shape[0]
    # divide data based on the target variable
    p_train = train[train[target] == 1]
    n_train= train[train[target] == 0]
    p_train = p_train.drop([target], axis=1)
    n_train = n_train.drop([target], axis=1)
    
    # get mean and variance for each variable
    p_mu = np.mean(p_train, axis=0)
    n_mu = np.mean(n_train, axis=0)
    p_var = np.var(p_train, axis=0)
    n_var = np.var(n_train, axis=0)
	
    # calculate the probability of test dataset
    p_prob = 1/np.sqrt(2*np.pi*p_var) * np.exp(-(test - p_mu)**2/(2*p_var))
    n_prob = 1/np.sqrt(2*np.pi*n_var) * np.exp(-(test - n_mu)**2/(2*n_var))
    p_result = np.sum(np.log(p_prob), axis=1) + np.log(p_train.shape[0]/n)
    n_result = np.sum(np.log(n_prob), axis=1) + np.log(n_train.shape[0]/n)
    return p_result, n_result
```

First we divided training data into two parts based on instances' target class; positive train set and negative train set. Then, we calculate mean and variance for each 10 variables for each positive and negative set. Here numpy functions helps vectorized computations. Lastly, we calculate each probabilities of test set based on the parameters we fitted from training set. Then we use log function and add them up.



```python
# 10 cross validation with all variables
accuracies = []
for i in np.arange(10):
    train, test = data_split(data)
    actual = test['Class']
    p_result, n_result = naive(train, test, 'Class')
    pred = np.array([int(tf) for tf in (p_result > n_result)])
    accuracy= np.count_nonzero(pred==actual)/ test.shape[0]
    accuracies.append(accuracy)
np.mean(accuracies)

0.7545752283849231
```

As the result of conducting 10 folds cross validations, we get about 75% accuracy.



```python
# sklearn naive test
accuracies = []
for i in np.arange(10):
    train, test = data_split(data)
    p_prior = np.count_nonzero(train.Class==1)/train.shape[0]
    n_prior = np.count_nonzero(train.Class==0)/train.shape[0]
    nb_classifier = nb.GaussianNB(priors=[p_prior, n_prior])
    nb_classifier.fit(train.drop(["Class"], axis=1), train.Class)
    pred = nb_classifier.predict(test.drop(['Class'], axis=1))
    accuracies.append(np.count_nonzero(pred== test.Class)/test.shape[0])
np.mean(accuracies)

0.7314461062685064
```

Now here we used a Gaussian Naive Bayes classifier, which is same thing, and we get about 73% accuracy. Mine is better.



## Reference

- CS498: Applied Machine Learning by Professor Trevor Walker
