---
title: "7 - Principal Coordinate Analysis"
date: 2019-05-28
categories: Machine
mathjax: true
---

Visualization is a great tool to understand data. However, when it comes to visualize high-dimensional data, it is difficult to choose which dimensions to display. General way of visually representing data based on the similarity is called **Multidimensional Scaling**. 



## Principal Coordinate Analysis

Principal Coordinate Analysis is one of the methods of multidimensional scaling. As its name refers, it visualizes data based on its coordinate distance between data points. Let's say we are to plot the high dimensional points $$x$$ into the low dimensional points $$y$$. And we want the distance between data points in low dimensions reflects those in high dimensions. We are using distance function:

$$ D^{(2)}_{ij}(x) = (x_i - x_j)^T(x_i - x_j)$$

And the data points far away in $$x$$ space must be far away in $$y$$ space. Then we have to minimize,

$$ \sum_{ij}(D^{(2)}_{ij}(x) - D^{(2)}_{ij}(y))^2 $$

which is same as minimizing the difference between $$X X^T$$ and $$Y Y^T$$, where $$X$$ is stack of $$x$$ vectors and $$Y$$ is stack of $$y$$ vectors.

For convenience, we center the mean of data, as the exact data points do not matter much. Now we form,

$$ A = [I - \frac{1}{N}1 1^T]$$

where $$1$$ refers to an $$N$$-dimension column vector.

Then we have

$$ X X^T =  -\frac{1}{2}A D^{(2)}(X) A^T$$





As we want to find the row rank approximation of $$X$$, which is $$Y$$, we suppose $$Y$$ has $$s$$ ranks. Then we form an SVD of $$X$$, 

$$ X = U\Sigma V^T$$

And by using only part of entries of $$\Sigma$$, we will construct row rank approximation.

$$ X_s = U_s \Sigma_s V^T_s $$

Then we have,

$$ X_s X_s^T = (U_s \Sigma_s V_s^T)(U_s \Sigma_s V_s^T)^T$$

$$ = U_s \Sigma_s V_s^T V_s \Sigma_s^T U_s^T$$

$$ = U_s \Sigma_s^2 U_s^T $$

since $$V_s$$ is orthonormal matrix.

We can realize $$X_s$$ is actually $$Y$$. That being said, rewrite the equations:

$$ Y_s = U_s \Sigma_s$$



Using $$D^{(2)}(X)$$ instead of actual $$X$$, we can find out the row rank approximation.



## Code

Suppose we have the distance data between 5 cities; "Sydney", "Brisbane", "Perth", "Melbourne", and "Alice Spring". Using Principal Coordinate Analysis, we will plot these 5 dimensional data into 2 dimensions.

```python
city_distance = 
np.array([[0, 732.36, 3290.42, 877.65, 2772.97],
[732.36, 0, 4317.41, 1680, 2532.58],
[3290.42, 4317.41, 0, 3406.42, 2493.38],
[877.65, 1680, 3406.42, 0, 2257.19],
[2772.97, 2532.58, 2493.38, 2257.19, 0]])
cities = ['sydney','brisbane','perth','melbourne', 'alice spring']

# calculate squared distance between cities
D = [np.sum(np.subtract(dist, dist)**2) for dist in city_distance for dist in city_distance]
D = np.array(D).reshape(-1,5)

# Create A and ADAT
d = 5
A = np.identity(d)-np.dot(np.ones((d,1)), np.ones((1,d)))/d
ADA = -np.dot(np.dot(A, D), A.T)/2

# check if ADA is same as XXT
distance = city_distance - np.mean(city_distance, axis=1)
XXT = np.dot(distance, distance.T)
np.allclose(XXT, ADA)

True
```

It came out $$-\frac{1}{2}A D^{(2)}(X) A^T$$ and $$X X^T$$ are same. Now plot 2 dimensional data $$Y$$.

```python
# decompose ADA and order them in decrasing order
eigval, eigvec = np.linalg.eig(ADA)
idx = np.argsort(-eigval)
eigval = eigval[idx]
eigvec = eigvec[:, idx]

# get Y(row rank) and plot
ys = eigvec[:,0:2]* np.sqrt(eigval[0:2])
fig, ax = plt.subplots()
for i, txt in enumerate(cities):
    ax.annotate(txt, (ys[i,0], ys[i,1]))

plt.scatter(ys[:,0], ys[:,1])
plt.title('PartB 2D scatter plot')
plt.xlabel('first component')
plt.ylabel('second component')
plt.plot()
```

<p align="center">
    <img src ="../../assets/img/machine/7-2d.png" style="width:70%">
    <br/>
    <sub>2D plot of city data</sub>
</p>





## Reference

- CS498 Applied Machine Learning by Professor Trevor Walker
