---

title: "Multi-Label Classification"
date: 2020-01-16
categories: Machine
mathjax: true

---



기본적인 머신러닝 문제들은 Regression이나 Classification으로 나뉘어진다. Classification 중에서도 하나의 분류에만 속하는 게 아니라 다중의 분류로 속하는 문제들도 있다. 이러한 종류를 multi-label classification이라고 한다.

간단한 예로 영화의 특성을 볼 수 있다. 영화는 한 가지의 종류로만 분류될 수도 있지만, '로맨스', '코미디' 등 여러 개의 분류를 가질 수 있다. 이러한 특성은 분류 체계가 상호 비배타적이기 때문이다. 즉, 분류끼리의 의존성이 존재하기에 일반적인 상호배타적인 Multi-Class보다 까다롭다. 

Multi-label 문제를 풀기 위한 방법은 크게 두 가지로 나뉜다. 데이터를 알고리즘에 맞게 변형시키는 방법(Problem Transformation Method)와 알고리즘을 데이터에 맞게 바꾸는 방법(Algorithm Adaption Method)이다.

각각 방법에 해당하는 모델들과 성능 지표에 대해 써볼려고 한다. 데이터는 이전에 진행한 프로젝트에서 사용한 한글 리뷰 데이터를 사용했다.



## Problem Transformation Method

Multi-label 데이터를 Single-label로 문제를 푸는 방법이다. 아무 Single-label 분류기를 사용할 수 있다. 우리가 흔히 알고 있는 One Versus All 방법이 여기에 속한다. 각 분류와 해당 분류가 아닌 데이터를 기반으로 분류기를 학습시켜 문제를 풀 수 있다.



### Binary Relevance

익히 알고 있는 One Versus All 방법의 다른 이름이다. 각 분류마다 분류기를 학습시켜 해당 분류에 속하는 지 속하지 않는 지 예측해준다. 

<p align= "center">
    <img src = "../../assets/img/machine/ml-binary.png" width= "70%">
    <br/>
    <sub>Binary Relevance Data</sub>
</p>

<p align= "center">
    <img src = "../../assets/img/machine/ml-binary-graph.png" width= "70%">
    <br/>
    <sub>Binary Relevance</sub>
</p>

- 단점

분류마다 따로 학습시키기 때문에 분류끼리의 의존성이 무시된다. 예를 들어 '드라마'와 '로맨스'는 어느 정도 겹치는 부분이 있기 때문에 하나의 태그가 나왔다는 것은 어느 정도 다른 태그 출현에 대한 정보를 준다. 하지만 Binary Relevance에서는 이런 정보가 무시된다.

다른 문제는 분류 불균형이다. Multi-label 문제에서 대부분 나타나는 문제라고 생각되는데, 소수의 분류들이 전체 데이터 셋에서 큰 부분을 차지하는 경우가 많다. 이러한 경우, 어느 분류기에 할당되는 데이터는 굉장히 적어 학습이 잘 안되거나 overfitting될 수 있다.



#### Chain Classifier

BR의 의존성 문제를 어느 정도 해소시켜줄 수 있는 방법이다. 각각 분류에 대해 학습기를 생성하는 것은 같지만, Chain rule과 같이 이전 분류기 예측값을 다음 분류기 학습에 변수로 사용한다.

<p align= "center">
    <img src = "../../assets/img/machine/ml-chain-classifier.png" width= "70%">
    <br/>
    <sub>Classifier Chain</sub>
</p>

$$ \hat y_j = h(x, \hat y_1, ..., , \hat y_{j-1} )$$

$$ = \underset{y_j \in \{0,1\}}{\operatorname{argmax} }p(y_j \mid x, \hat y_1, ..., \hat y_{j-1}) $$



### Label Powerset

<p align= "center">
    <img src = "../../assets/img/machine/ml-label-powerset.png" width= "70%", height = "50%">
    <br/>
    <sub>Label Powerset</sub>
</p>



- 단점

분류의 조합만큼의 분류가 생기기 때문에 complexity가 굉징히 높다. 또 BR이 가지고 있던 클래스 불균형 문제를 풀기보다는 악화시킨다.

또, 만약 신규 데이터에 새로운 분류 조합이 있다면 맞추기 어렵다.



## Algorithm Adaption Method







## Reference

- Zhang, M. L., & Zhou, Z. H. (2013). A review on multi-label learning algorithms. *IEEE transactions on knowledge and data engineering*, *26*(8), 1819-1837.
- Tsoumakas, G., Katakis, I., & Vlahavas, I. (2006, September). A review of multi-label classification methods. In *Proceedings of the 2nd ADBIS workshop on data mining and knowledge discovery (ADMKD 2006)* (pp. 99-109).