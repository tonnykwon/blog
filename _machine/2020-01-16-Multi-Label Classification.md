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
    <img src = "../../assets/img/machine/ml-binary-graph.png" width= "70%" height = "70%">
    <br/>
    <sub>Binary Relevance</sub>
</p>

- 단점

분류마다 따로 학습시키기 때문에 분류끼리의 의존성이 무시된다. 예를 들어 '드라마'와 '로맨스'는 어느 정도 겹치는 부분이 있기 때문에 하나의 태그가 나왔다는 것은 어느 정도 다른 태그 출현에 대한 정보를 준다. 하지만 Binary Relevance에서는 이런 정보가 무시된다.

다른 문제는 분류 불균형이다. Multi-label 문제에서 대부분 나타나는 문제라고 생각되는데, 소수의 분류들이 전체 데이터 셋에서 큰 부분을 차지하는 경우가 많다. 이러한 경우, 어느 분류기에 할당되는 데이터는 굉장히 적어 학습이 잘 안되거나 overfitting될 수 있다.



### Chain Classifier

BR의 의존성 문제를 어느 정도 해소시켜줄 수 있는 방법이다. 각각 분류에 대해 학습기를 생성하는 것은 같지만, Chain rule과 같이 이전 분류기 예측값을 다음 분류기 학습에 변수로 사용한다.

<p align= "center">
    <img src = "../../assets/img/machine/ml-chain-classifier.png" width= "70%" height = "70%">
    <br/>
    <sub>Classifier Chain</sub>
</p>

$$ \hat y_j = h(x, \hat y_1, ..., , \hat y_{j-1} )$$

$$ = \underset{y_j \in \{0,1\}}{\operatorname{argmax} }p(y_j \mid x, \hat y_1, ..., \hat y_{j-1}) $$



### Label Powerset

Label Powerset은 각각의 분류 조합을 하나의 분류로 여기는 방법이다. Multi-class 방법으로  분류간의 의존성을 학습시킬 수 있다. 하지만 치명적인 단점이 있다. 분류의 종류가 $$2^L$$으로  질수 있다(L: 분류 클래스 개수). 이에 따른 단점은 다음과 같다.

- 단점

먼저 위 분류의 조합만큼 많이 생기기 때문에 분류기의 complexity가 굉징히 높아진다. 게다가 분류 조합이 다양하기에 각 분류에 속하는 데이터는 적어지고 클래스 불균형 악화시킨다.

또, 만약 신규 데이터에 새로운 분류 조합이 있다면 맞추기 어렵다.

<p align= "center">
    <img src = "../../assets/img/machine/ml-label-powerset.png" width= "70%" height = "70%">
    <br/>
    <sub>Label Powerset</sub>
</p>


### RAndom k-labEL subsets(RAKEL)[^1]

$$2^L$$에서 무작위로  $$2^k$$의 조합을 만들어 사용하는 방법이다. 과하게 많은 분류 조합을 어느 정도 해소할 수 있는 방법이다. 크게 조합을 disjoint하게 만드는 방법과 overlapping하게 만드는 방법으로 나뉘어진다. LP에서 발전한 점은 3가지로 볼 수 있다.

먼저 모델 연산이 간단해진다. 논문에서 언급한  `meadiamill` 데이터는 약 101의 Label이 있고 전체 6555개의 분류 조합이 있다.  여기서 $$k=3$$ 사용하면 총 $$2^3=8$$개의 분류가 생기고 약 200개의 모델을 만들어 앙상블하면 약 $$200*8=1600$$ 개수의 모델을 학습시키면 된다. 그에 비해 LP는 6555개의 모델을 학습시켜야한다.

또, 분류의 불균형이 어느 정도 해소된다. 보통 Multi-Label 문제는 long tail 모양의 분류 분포로 고통 받는데 Rakel을 사용하면 좀 더 균등하게 배분된다고 한다.

Rakel은 하나의 label set에서 여러 다른 label을 만들어내고 이를 기반으로 다른 모델이 학습된다. 따라서, 각각 다른 점을 학습할 수 있고 voting 과정을 통해 에러를 줄이고 전반적인 성능을 향상시킬 수 있다.

다음은 disjoint Rakel의 Pseudocode다.

```python
M = input('data size')
k = input('number of labelset')
R = input('1 ~ m k-labelsets')

# train phase
m = ceil(M/k)
for i in range(m):
    for j in range(k):
        if not L:
            break
        idx = random.choice(range(len(L))) # choose one from L
        lambda = L.pop(idx)
        R[i].append(lambda)
    classifier[i].fit(D, R[i]) # train classifier based on D corresponding R_i
    
# predict phase
x = input('new instances')

for i in range(m):
    for 
```





## Algorithm Adaption Method

기존 classification 알고리즘을 바꾸어 Multi-label 문제를 푸는 방법이다.



### K-Nearest Neighbors

MLkNN은 x에서 가장 가까운 k개의 이웃 중에 가장 흔한 label을 assign하는 방법이다. 여기서 발전 시켜 베이지안 추론을 통해 각 클래스에 대한 확률을 계산할 수 있다(Maximum A Posteriori 기반).

$$ y_j = \begin{cases} 1, \;\text{if } P(c_{j,x} \mid y_j = 1)P(y_j = 1) \geq P(c_{j,x} \mid y_j =0) P(y_j =0) \\ 0, \; \text{otherwise}\end{cases} $$

$$ P(y_j = 1 \mid c_{j,x}) \propto P(c_{j,x} \mid y_j = 1) P(y_j = 1) $$

여기서 $$c_{j,x}$$는 x의 이웃 중에서 j label을 가지고 있는 데이터의 수를 말한다.  따라서 j label에 대한 사전 확률과 j label이 주어졌을 때의 우도확률을 j label이 아닌 경우와 비교해서 더 크면 j label을 부여한다.($$P(y_j=0) = 1 - P(y_j =1) $$)



### Decision Tree

기존의 C4.5의 경우, entropy를 계산하여 information gain이 가장 큰 노드를 만들어가며 만들어진다. 여기서 entropy는 데이터의 불확실성을 측정한 것으로 다음과 같이 계산한다.[^2]

$$ \text{entropy}(S) = - \sum_{i=1}^N p(c_i) \operatorname{log} p(c_i) $$ 

여기서 $$p(c_i)$$는 set 내에서 $$c_i$$ 클래스의 확률을 뜻한다(상대 도수). 다른 말로 하면 entropy는 해당 데이터셋을 표현하는 데 얼만큼의 정보량이 필요한 지 나타난다. 데이터셋에 포함되는 클래스를 표현하는데 필요한 bit 수를 뜻하기도 한다. 따라서 entopry식을 multi-class에 맞게 바꾼다면 데이터셋에 포함되는 label가 그렇지 않은 label을 표시하기 위한 bit 수를 구할 수 있어야 된다. N개의 클래스에 대해 필요한 bit를 나타내자면 다음과 같다.

$$ -\sum_{i=1}^N [P(c_i) log(P(c_i)) + q(c_i)log(q(c_i)) ]$$

where

$$ p(c_i) = $$ 클래스가 $$c_i$$일 확률

$$ q(c_i) = 1- p(c_i) =$$ 클래스가  $$c_i$$가 아닐 확률



해당 multi-class entropy를 통해 tree를 구성하고, 각 leaf에 labels 구성을 통해 예측한다. Ensemble과 Random Forest도 역시 사용할 수 있다.



### Backpropogation for Multi-Label Learning(BP-MLL)

Neural network를 사용할 경우 ouput node를 multi-label 수만큼 설정하고 categorical cross-entropy를 손실함수, 활성화 함수를 sigmoid로 나타내어 각 label에 대한 확률을 출력해서 사용할 수 있다. 모든 label에 대해 출력 노드이 있는 점에서 같지만 BP-MLL 모델은 손실함수가 조금 다르다.

$$ E = \sum_{i=1}^m  \frac {1}{ \mid Y_i \mid \mid \overline Y_i \mid} \sum_{(k,l) \in Y_i \times \overline Y_i } exp(-(c_k^i - c_l^i)) $$

where

i = 데이터 index

$$Y_i = $$ i번째 데이터의 실제 label set

$$ \overline Y_i = $$ 전체 label 중 Y의 complementary set

$$\mid \cdot \mid = $$  cardinarilty, 집합의 크기

$$c_k^i$$는 i번째 데이터의 k label에 대한 network의 출력



$$ c_k^i - c_l^i$$ 부터 보자면 $$x_i (k \in Y_i)$$에 속하는 label과 속하지 않는 $$(l \in \overline Y_i)$$ label과의 network 출력 차이를 의미한다. 이 차이가 클수록 모델의 성능은 좋기 때문에 negation해준다. 그리고 exponential은 $$c_k^i$$가 크게 작을 때 penalty를 줌으로써 

$$c_k^i$$ 출력이 커지도록 한다. 이렇게 모든 pair에 대해 출력 차이를 합하고 두 집합 크기에 대한 곱으로 normalize해준다. pair가 많아질수록 exp 합 값이 커질 수 있기 때문이다.



## Reference

- Zhang, M. L., & Zhou, Z. H. (2013). A review on multi-label learning algorithms. *IEEE transactions on knowledge and data engineering*, *26*(8), 1819-1837.

- Tsoumakas, G., Katakis, I., & Vlahavas, I. (2006, September). A review of multi-label classification methods. In *Proceedings of the 2nd ADBIS workshop on data mining and knowledge discovery (ADMKD 2006)* (pp. 99-109).

- [^1]:Tsoumakas, G., & Vlahavas, I. (2007, September). Random k-labelsets: An ensemble method for multilabel classification. In *European conference on machine learning* (pp. 406-417). Springer, Berlin, Heidelberg.

- [^2]: Clare, A., & King, R. D. (2001, September). Knowledge discovery in multi-label phenotype data. In *european conference on principles of data mining and knowledge discovery* (pp. 42-53). Springer, Berlin, Heidelberg.

- Zhang, M. L., & Zhou, Z. H. (2006). Multilabel neural networks with applications to functional genomics and text categorization. *IEEE transactions on Knowledge and Data Engineering*, *18*(10), 1338-1351.
