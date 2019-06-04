---
title: "9 - Clustering"
date: 2019-06-04
categories: Machine
mathjax: true
---

In this post, we will discuss about clustering, and it will be mostly about K-means clustering. Clustering is mostly unsupervised method to group data which have similar features. There are different kinds of clustering based on its way of grouping data, such as hierarchy, density, gird and distance-based methods. I will discuss various ways of clustering in data mining posts. 



### K-means Clustering

Procedure of K-means clusterings are the followings:

Set $$k$$, the number of clusters, and choose $$k$$ points $$c_j$$ to act as cluster centers.

Repeat until there is no big changes in cluster centers:

- Allocate each data points to the closet cluster center
- Calculate the mean of data points for each cluster, and replace it as a new cluster center



### K-means with soft weight

One problem of K-means clustering is that each point should belong to only one cluster. For some cases that a point is close to multiple clusters, we need other methods to use. It is K-means clustering with soft assignment. Rather than assigning only one cluster, we can give weights, $$w_{i,j}$$. It represents how much the data point $$i$$ belongs to the cluster $$j$$. And the sum of one data point should be one($$\sum_j w_{ij} =1 $$). We define affinity between the point and the cluster

$$ s_{ij} = e^{\frac{\mid x_i - c_j \mid} {2 \sigma^2} } $$

We need to choose a scale, $$\sigma$$ , before calculate the affinity. Now for each data point and a cluster, compute soft weight

$$ w_{ij} = \frac{s_{ij}}{\sum_{l=k}^k s_{il}}$$

Now new weight, we calculate a new center

$$ c_j =  \frac{\sum_i w_{ij} x_i}{\sum_j w_{ij}}$$

Repeat this process until there is no big difference in the cluster centers.



## Vector Quantization

We can utilize clustering for processing data. When it comes to data like images or sounds, we may find patterns in small pieces of data. Rather than using each pixel or each sound signal, we can group a batch of pixels or sound signals that has similar patterns. This strategy is called **vector quantization**. We reshape the training data into d-dimensional vectors. Cluster these vectors into k clusters. And the counts of each vectors can be used for classification.

For test, ADL dataset is used. It is sound data of 14 different actions(brush teeth, climb stairs, comb haris...) by 16 volunteers.

```python
from sklearn.cluster import KMeans

k = 40
kmean = KMeans(n_clusters=k, n_init=40, max_iter=1000, n_jobs=3, random_state=1)
kmean.fit(data_df)
```

Using sklearn Kmeans, we build 40 clusters with data. The data is set of sound serial. Before vector quantization, data is segmented with some overlap. 



```python
idx = 1
unique, counts = np.unique(np.hstack(vq_dict.get(idx)), return_counts=True)
plt.title(meta.get(idx))
plt.bar(unique, counts / np.sum(counts))
plt.plot()
```

<p align ='center'>
    <img src="../../assets/img/machine/9-up-stairs.png" style="width: 70%">
    <br/>
    <sub>Vector quantization of climbing stairs</sub>
</p>

Above plot is the mean counts of vectors for sound of climbing stairs.

<p align ='center'>
    <img src="../../assets/img/machine/9-down-stairs.png" style="width: 70%">
    <br/>
    <sub>Vector quantization of descending stairs</sub>
</p>

Above plot is the mean counts of vectors for sound of descending stairs. As expected, it has similar noticeable counts for certain vectors like 0, 9, or 21. 

<p align ='center'>
    <img src="../../assets/img/machine/9-get-up.png" style="width: 70%">
    <br/>
    <sub>Vector quantization of getting up bed</sub>
</p>

Compared to climbing or descending stairs, getting up bed has very different counts ratio of vectors.  Utilizing vector quantization with random forest for classification gives about 80% of accuracy on test set.





## Reference

- CS498 Applied Machine Learning by Professor Trevor Walker
