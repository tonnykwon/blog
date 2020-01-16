---

title: "Multi-Label Classification"
date: 2020-01-16
categories: Machine
mathjax: true

---



기본적인 머신러닝 문제들은 Regression이나 Classification으로 나뉘어진다. Classification 중에서도 하나의 분류에만 속하는 게 아니라 다중의 분류로 속하는 문제들도 있다. 이러한 종류를 multi-label classification이라고 한다.

간단한 예로 책의 종류로 볼 수 있다. 책은 한 가지의 종류로만 분류될 수도 있지만, '로맨스', '코미디' 등 여러 개의 분류를 가질 수 있다. 이러한 특성은 분류 체계가 상호 비배타적이기 때문이다. 따라서, 일반적인 상호배타적인 Multi-Class보다 까다롭다. 

Multi-label에 알맞는 모델링 방법들과 성능 지표에 대해 써볼려고 한다. 데이터는 이전에 진행한 프로젝트에서 사용한 한글 리뷰 데이터를 사용했다.



## Binary Relevance

