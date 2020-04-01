---
title: "[구현] Alternating Least Sqaures"
date: 2020-03-13
categories: Recommender
mathjax: true
---



250만개의 movelen 데이터를 이용하여 implicit dataset을 행렬분해 해보았다. 



## 데이터

- ratings.csv

각 행은 유저가 시청한 영화에 대한 평가를 나타낸다. 

userId, movieId, rating, timestamp 열로 이루어져있다. 

|      | userId | movieId | rating |  timestamp | binary |
| :--- | -----: | ------: | -----: | ---------: | ------ |
| 13   |  95565 |    1101 |    3.0 | 1249876431 | 1      |
| 17   |  81474 |   33493 |    4.0 | 1262536357 | 1      |
| 19   | 120674 |    1197 |    4.5 | 1358545147 | 1      |
| 28   | 139236 |     141 |    5.0 |  834427766 | 1      |

binary 변수는 implicit data로 변경하기 위해 임의로 추가했다.



- movies.csv

각 영화에 대한 정보가 담겨있는 테이블이다. 

movieId, title, genres 열로 영화의 제목과 장르로 이루어져있다. 장르는 Action, Adventrue, Animation, Children's, Comedy 등이 있다.



ratings 데이터를 이용하여 각 유저와 영화에 대한 시청 이력 행렬로 변환한 다음에 ALS 행렬 분해를 진행하였다.

전체 데이터가 커서 2만7천명의 유저와 588개의 영화만 사용했다.유저는 영화 시청 이력이 10번 이상 20번 이하를 본 유저만 추출하였고, 영화는 최소 1000명이상 본 영화를 선택했다. 해당 기준은 아무런 근거 없이 데이터 사이즈가 적당하도록 골라본 것이다.



## ALS

Preference matrix $$R$$를 만들기 위해, 유니크한 유저 아이디와 유니크한 영화 아이디를 추출하여 카테고리화하고 pivot table을 만들어 주었다.

```python
user_c = CategoricalIDtype(sorted(ratings.userId.unique()), ordered= True)
movie_c = CategoricalIDtype(sorted(ratings.movieId.unique()), ordered= True)

row = ratings.userId.astype(user_c).cat.codes
col = ratings.movieId.astype(movie_c).cat.codes
R = csr_matrix((ratings["binary"], (row, col))\
              shape = (user_c.categories.size, movie_c.categories.size))
```



각 유저 행렬과 영화 행렬을 $$X, Y$$라 했을 때 이를 초기화한다.

```python
# set n: # user, k: # latent variables, m: # movie
user_size = ratings.userId.nunique()
movie_size = ratings.movieId.nunique()
hidden_size = 100

# Initialization
X = csr_matrix(np.random.normal(size = (user_size, hidden_size)))
Y = csr_matrix(np.random.normal(size = (movie_size, hidden_size)))
```



Confidence level matrix인 C의 경우 preference에 alpha값을 곱하고 1을 더한 값으로 세팅해준다. 여기서 알파값은 보통 40을 사용한다고 한다.

$$ c_{ui} = 1+ \alpha r_{ui}$$

```python
# confidence level
alpha = 40
C = csr_matrix(R.copy()*alpha+ np.array([1]*(user_size*movie_size)).reshape((user_size, movie_size)))
```



목적함수는 다음과 같다.

$$ \underset {x*, y*} {\min} \underset {r_{u,i} \text{is known}} \sum (r_{ui} - x_u^T y_i)^2 + \lambda(\|x_u\|^2 + \| y_i \| ^2) $$

```python
# cost
main_cost = C.multiply((R-X.dot(Y.T)).power(2)).sum()
reg_term = reg*(X.power(2).sum() + Y.power(2).sum())
cost = main_cost + reg_term
```



그리고 행렬 업데이트 식은 다음과 같다.

$$ x_u = (\sum_i c_{ui} \ y_i \ y_i^T + \lambda I)^{-1} \sum_i c_{ui} p_{ui} y_i$$

$$ y_i = (\sum_u c_{ui} \ x_u \ x_u^T + \lambda I) ^{-1} \sum_u c_{ui} p_{ui} x_u$$



```python
import sys
from scipy.sparse.linalg import inv

# hyperparameters
reg = 1e-3
iteration = 3

for iterate in range(iteration):
    print('iter: '+str(iterate))
    print('User Matrix')
    for u, Cu in enumerate(C):
        # Cu to diagonal
        C_diag = sparse.diags(Cu.toarray()[0], shape = [movie_size, movie_size])
        X[u] = inv(Y.T.dot(C_diag).dot(y) + sparse.eye(hidden_size)*reg).dot(Y.T).dot(R[u].T).T
        
        # progress
    	percent = (u+1)/user_size
        sys.stdout.write('\n')
        sys.stdout.write('[%-50s] %d%%' % ('='*int(50*percent), 100*percent))
        sys.stdout.flush()
        
    print('\nMovie Matrix')
    for i, Ci in enumerate(C.T):
        # Ci to diagonal
    	C_diag = sparse.diags(Ci.toarray()[0], shape = [user_size, user_size])
        Y[i] = inv(X.T.dot(C_diag).dot(X) + sparse.eye(hidden_size)*reg).dot(X.T).dot(C_diag).dot(R.T[i].T).T
        # progress
        percent = (i+1)/movie_size
        sys.stdout.write('\r')
        sys.stdout.write('[%-50s] %d%%' % ('='*int(50*percent), 100*percent))
        sys.flush()
   
	main_cost = C.multiply((R-X.dot(Y.T)).power(2)).sum()
    reg_term = reg*(X.power(2).sum() + Y.power(2).sum())
    cost = main_cost + reg_term
    print('\n')
    print('cost: '+str(cost))
```



```
cost: 804768.0846739176
iter: 0
User matrix
[==================================================] 100%
Movie matrix
[==================================================] 100%

770866.3099528673
iter: 1
User matrix
[==================================================] 100%
Movie matrix
[==================================================] 100%

749915.4507619103
iter: 2
User matrix
[==================================================] 100%
Movie matrix
[==================================================] 100%

734719.9788249986
Wall time: 2h 17min 49s
```



cost는 줄고 있는 거 같은데 학습시간이 너무 오래 걸려 많이 반복하지는 못했다. 결과는 대략 이렇게 나온다.



```python
# read and make dictionary
movies = pd.read_csv('ml-25m/movies.csv')
movie_title_dict = {movies.movieId[i]:movies.title[i] for i in range(movies.shape[0])}

# movies person i watched
i = 200
print('Watched')
for watched_movie in R[i].indices:
    movie_idx = movie_c.categories[watched_movie]
    print(movie_title_dict[movie_idx])
    
print('\nRecommended')
for each_movie in np.argsort(-prediction[i])[0, :20].tolist()[0]:
    if each_movie not in R[i].indices:
        movie_idx = movie_c.categories[each_movie]
        print(movie_title_dict[movie_idx])
```

추천 결과는 다음과 같다.

Watched
Seven (a.k.a. Se7en) (1995)
Mr. Holland's Opus (1995)
Léon: The Professional (a.k.a. The Professional) (Léon) (1994)
Dave (1993)
Willy Wonka & the Chocolate Factory (1971)
Terminator, The (1984)
Fifth Element, The (1997)

Recommended
Pirates of the Caribbean: At World's End (2007)
Lord of the Rings: The Return of the King, The (2003)
Godfather, The (1972)



세븐, 레옹, 초콜링 공장, 터미네이터 등을 봤을때, 캐리비안의 해적, 대부, 반지의 제왕을 추천해준다.