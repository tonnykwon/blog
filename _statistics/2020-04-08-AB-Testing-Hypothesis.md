---
title: "AB Testing - Hypothesis"
date: 2020-04-08
categories: statistics
mathjax: true
tags: [AB, hypothesis]
---



이전 회사에서 프로젝트를 진행하면서 A/B 테스트를 해본 적이 있다. 새로운 추천 시스템을 통해 추출한 A 그룹과 기존 방법으로 추출한 B 그룹 사용자 중에 누가 더 광고에 반응할 지 알아보는 게 목표였다. 그때는 뭐가 뭔지 잘 모르고 클릭율, 전환율을 기반으로 판단했었다. 그래서 이후 이론적인 바탕 없이 진행한 게 면접 때 많이 털려서 다시 공부하려고 한다. 

Statistical Methods in Online AB Testing이라는 책을 읽고 정리해보려고 한다. 다른 책에 비해 평이 좋았던 것도 있고, 이론적인 부분을 깊게 다룬다는 점에서 고르게 되었다. 아마존에서 shipping이 안되서 아쉽지만 e-book으로 읽게 되었다.



## Substantive Hypothesis

실질 가설(Substantive Hypothesis)란 밝히고자 하는 관계에 대한 추측성 문장을 의미한다. 비즈니스적인 의문의 결과로 나타날 수 있다. 예를 들어, 'call-to-action 버튼을 배치한다면 판매가 증가할 것이다'나 '새로운 기능 추가로 고객 유지율이 5%로 늘것이다'와 같은 문장들이 실질 가설이다(call-to-action이란 사용자의 반응을 유도하는 요소를 의미).



## Statistical Hypothesis

통계 가설(Staitistical Hypothesis)은 밝히고자 했던 관계에 대한 추측성 문장을 통계적 모델에 기반하여 구조화하는 것이다. 여기서 통계적 모델이란 데이터가 어떤식으로 생겨났는지(data-generating-mechanism)에 관한 통계적 가정들을 반영하는 모델이라고 볼 수 있다. 

예를 들자면, '새로운 기능을 추가했을 때 고객 유지율이 5% 더 증가한다'라는 실질적 가설에 대한 통계적 모델을 가정해보자. 새로운 기능을 추가했거나 하지 않았을 때의 고객 유지율은 차이가 없고, 관찰된 고객 유지율에 대한 에러는 독립적이고 동일한 분포를 가지며 정규 분포를 따른다(NIID).

iid에 대한 추가적인 설명을 하자면 독립적이라는 것은 한 사건이 다른 사건에 영향을 주지 않음을 의미하고 동일한 분포를 가진다는 의미는 말그대로 같은 분포를 따른다고 가정하는 것이다(여러 번의 동전 던지기와 같음).

주장하는 가설에 따라 혹은 데이터에 따라 기반하는 통계적 모델은 바뀔 수 있다. 하지만 통계적 모델을 통해 우리가 관심있어 하는 사건들에 대한 확률을 규정할 수 있다.

좀 더 기술적으로는 다음과 같이 적을 수 있다.

$$ M_{\theta}(x) = \{f(x;\theta), \theta \in \Theta\}, x \in X := \R_X^n $$

모델 $$M$$은 x와 $$\theta$$에 관한 결합 확률 분포에 대한 함수이다(;는 x와 $$\theta$$의 성질이 다른 것을 나타낸다). $$\Theta$$ 파라미터 공간을 나타내고, X는 표본 공간을 나타낸다.



## Null vs Alternative

통계 가설에 대한 검증을 가설 검증이라고 한다. 모델이 표본 데이터와 적합한 지를 통해 통계 가설이 바탕한 통계적 모델이 맞는 지 확인해볼 수 있다. 여기서 우리는 귀무 가설(Null Hypothesis)와 대립 가설(Alternative Hypothesis)를 사용한다. 대립 가설이란 주장하는 통계 가설로 샘플 데이터와 $$\theta$$에 대한  통계 모델을 기반한다. 귀무 가설은 모델에 의해 정의된 전체 $$\theta$$에 대한 여집합으로 볼 수 있다.

간단히 보자면, 귀무 가설은 두 샘플은 같은 모집단에서 나왔고, 그렇기에 같은 통계 모델을 따른다고 가정한다. 대립 가설은 두 샘플은 다른 모집단에서 나왔고 다른 통계 모델을 따른다고 주장한다.

$$ H_0: \theta \in \Theta_1 \text{ vs } H_1: \theta \in \Theta_1 \\
\text{where } \Theta_1 \cap \Theta_2 = \emptyset \text{ and } \Theta_1 \cup \Theta_2 = \Theta   $$

가설 설정은 평균 차이와 같은 특정 변수로 나타낼 수도 있다.

$$ H_0: \Delta = 0 \text{ vs } H_1: \Delta \in (-\infty, 0) \cup (0,+\infty)$$



## Why Null Hypothesis?

바로 주장하고픈 통계 가설에 대한 검증을 하지 않고 왜 반대되는 귀무 가설을 설정해서 검증하는 걸까?

P라는 가정이 사실일 때, Q라는 결과를 얻는다고 해보자( $$ P \rightarrow Q$$). 예를 들어, A 제품이 B 제품보다 품질이 좋다라는 P 가정을 했을 때, 그 가정이 사실이면 우리는 A 제품의 구매율이 B보다 높다라는 Q 결과를 얻는다.

그렇다면 Q라는 결과, 즉 A 제품이 더 구매율이 높다는 데이터를 관찰했을 때, A제품이 더 좋다고 유추할 수 있을까? 그렇지 않다. P일때 Q인거지, Q일때 P가 아니므로 그렇게 유추하기는 힘들다. A 제품 회사가 마케팅을 더 잘했다든지, A 제품이 가격이 싸다든지 등 다른 요인이 Q라는 결과를 만들 수도 있기 때문이다.

$$ P \rightarrow Q \\
 Q \\
 \overline{\therefore P} $$

즉, 해당식은 논리적으로 잘못됐다.



반대로 P의 여집합(부정)을 가정해보자($$\neg P$$). B 제품의 구매율이 A 제품 구매율보다 높다면($$\neg Q$$) A제품이 B 제품보다 품질이 좋지 못하거나 같다라는 가정이 사실이 된다. 즉, $$Q$$가 관찰된다면 P의 여집합($$\neg P$$)은 사실이 아니게 된다.

$$ P \rightarrow Q \\
 \neg Q \\
 \overline{\therefore \neg P} $$

이렇게 간접적으로 P를 검증하는 방법을 가설 검증이라고 한다. 이를 통해 $$\neg P$$를 주장하는 사람들에게 책임을 전가하고, $$P$$가 더 설득력있는 주장으로 남게 된다.



## Reference

- Georgiev, G. (2019). Statistical Methods in Online A/B Testing.