---

title: "Least Squares and Convexity"
date: 2020-04-02
categories: Machine
mathjax: true
tags: [loss, convexity]
---



Least squares(최소자승법)은 해를 구하는 방법으로 미지수의 개수보다 식의 수가 많을 때 사용된다. 식은

$$ l(\beta) = (y- X \beta)^T (y- X \beta) $$

n x p라는 데이터 행렬 $$X$$, 종속 변수 p x 1 벡터 $$y$$와 p x 1 가중치 행렬 $$\beta$$가 주어졌을 대이다.

식을 펼치면 다음과 같아진다.

$$ \begin{align} l(\beta) 
&= (y^T - \beta^T X^T)(y-X\beta) 
\\ &= y^Ty - 2y^T X \beta + \beta^TX^TX\beta
 \end{align} $$

$$ \beta^TX^Ty$$는 스칼라이기 때문에 $$ \beta^T X^T y = y^T X \beta$$이다.



기울기가 0이 되는 지점이 해당 error가 최소가 되는 지점으로 $$\beta$$에 대해 해당 식을 1차 미분하면 $$\beta$$에 관해 풀 수 있다.

$$ \frac{\partial l(\beta)}{\partial \beta} = -2y^TX + \beta^TX^TX $$

$$ \beta^T X^T X = 2 y^TX $$

$$ XX^T\beta = 2X^Ty$$

$$ \therefore \beta = 2(XX^T)^{-1}X^Ty $$



이처럼 앞에 2가 나오는 걸 없애기 위해 least squares를 1/2를 붙여서 쓴다.

$$ l(\beta) = \frac{1}{2} (y^T - \beta^T X^T)(y-X\beta) $$



## Convexity

함수 $$ f : \R^n \rightarrow \R$$가 임의의 두 점 $$x, y$$에서 $$\lambda \in [0,1]$$에 대하여 다음과 같은 성질이 항상 성립되면 convex function이라 한다.

$$ f (\lambda x + (1- \lambda) y) \leq \lambda f(x) + (1 - \lambda) f(y)$$



그럼 least squares를 대입해서 해당 함수가 convex한 지 알아보자. 일단 편의를 위해 식을 왼쪽으로 모두 넘기면 $$x, y$$를 $$\beta_1, \beta_2$$로 바꾸어보자.

$$ f (\lambda \beta_1 + (1- \lambda) \beta_2) - \lambda f(\beta_1) - (1 - \lambda) f(\beta_2) \leq 0$$

$$ f (\lambda \beta_1 + (1- \lambda) \beta_2) - \lambda f(\beta_1) - (1 - \lambda) f(\beta_2) $$ 

$$ = y^Ty -2y^T X(\lambda \beta_1 + (1-\lambda)\beta_2) + (\lambda \beta_1 + (1-\lambda)\beta_2)^T X^TX(\lambda \beta_1 + (1-\lambda)\beta_2) \\ -  \lambda(y^Ty - 2y^T X \beta_1 + \beta_1^T X^T X \beta_1) - (1-\lambda)(y^Ty - 2y^T X \beta_2 + \beta_2^T X^T X \beta_2)$$

$$= -2y^TX(\lambda \beta_1 + (1-\lambda)\beta_2 - \lambda \beta_1 - (1-\lambda)\beta_2) + \lambda^2 \beta_1^T X^TX\beta_1
\\ + \lambda(1-\lambda) \beta_1^T X^TX \beta_2 + \lambda(1-\lambda) \beta_2^T X^TX \beta_1 + (1-\lambda)^2 \beta_2^T X^TX \beta_2 
\\ -\lambda \beta^T_1 X^TX\beta_1 -(1-\lambda)\beta_2^T X^TX \beta_2$$

여기서 $$y^Ty -\lambda y^T y -(1-\lambda)y^Ty$$ 항은 사라진다.



$$ = \lambda^2 \beta_1^T X^TX\beta_1 + 2\lambda(1-\lambda) \beta_1^T X^TX \beta_2  
\\ + (1-\lambda)^2 \beta_2^T X^TX \beta_2 -\lambda \beta^T_1 X^TX\beta_1 -(1-\lambda)\beta_2^T X^TX \beta_2 $$

또, $$-2y^TX$$항 역시 사라진다.

이를 정리하면,

$$ = -\lambda(1-\lambda)[(\beta_1 - \beta_2)^T X^TX(\beta_1 - \beta_2)]
\\ = -\lambda(1-\lambda)\|X(\beta_1 - \beta_2) \|^2 $$ 

$$-\lambda(1-\lambda)$$는 음수이고 행렬의 norm의 제곱이므로 양수이므로 해당 식은 $$\leq 0$$이다.



