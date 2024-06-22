---

title: "TrueSkill factor-graph 추론 (1)"
date: 2024-06-22
categories: Machine
mathjax: true
tags: [machine-learning, metric, binary, skill-rating]
---

아래는 PDF 파일의 내용을 마크다운 형식으로 변환한 것입니다. 이미지의 경우는 "(이미지 1)" 등의 번호로 표기하였습니다.

```markdown
# Trueskill 추론

## 기본 모델 추론

매치 결과가 주어졌을 때, 유저 실력 점수가 어떻게 업데이트되는 지 확인해본다.

Trueskill에서 모든 메시지는 가우시안이거나 가우시안으로 근사된다. 이해를 위해 canonical form 대신 $\mu$ , $\sigma$ 로 표현한다.

위 factor graph 유저 1 실력은 $\mu_1, \sigma_1$ , 유저 2 실력은 $ \mu_2, \sigma_2 $ 라고 가정한다.

유저 1이 이겼을때, 유저 1의 사후 실력을 추론한다고 하면, 위 belief propagation에서 (1)번 식을 사용하여 다음과 같이 구할 수 있다.

여기서 $ p(s_1) $ 은 미리 주어지는 유저 1의 사전 실력 분포, $ r $ 은 경기 결과, $ p(r|s_1,s_2) $ 는 경기 결과로 얻은 likelihood이다.

$ p(s_1) $ 는 사전에 주어지는 유저 1의 실력 분포로 $ \mathcal{N}(s_1 | \mu_1, \sigma_1^2) $ 이다.

$ m(s_1) $ 는 그 외 변수들에서 오는 메시지에 의존하여 모든 메시지를 계산해주어야 한다. 아래 흐름에 따라 메시지를 계산한다.

### Skill Factor

$ p(s_1) = \mathcal{N}(s_1 | \mu_1, \sigma_1^2) $

$ m_1, m_3 $ 는 사전에 주어진 정보로 그대로 전달된다.

스킬 $ s_1 $ 에서 factor $ p_1 $ 으로 가는 메시지의 경우 $ m_1 $ 과 동일하여 따로 표기 생략.

```python
# default setting
beta = 5

s1_mu = 80
s1_sigma = 30

s2_mu = 50
s2_sigma = 5

# m1, m3 
m1 = (s1_mu, s1_sigma**2)
m3 = (s2_mu, s2_sigma**2)
```

### Performance factor

Belief propagation에 따라 factor $ p_1 $ 에서 변수 $ p_1 $ 으로 나가는 메시지는 식 2.115 Bishop[2006]

$ p(p_1) = \mathcal{N}(p_1 | \mu_1, \sigma_1^2 + \beta^2) $

skill 분포에서 표준편차만 커짐

```python
# m2, m4 
m2 = (m1[0], m1[1] + beta**2)
m4 = (m3[0], m3[1] + beta**2)
```

### Difference factor

$p(d | p_1, p_2)$ 는 두 메시지의 차이를 구한다. belief propagation (2)를 통해 구할 수 있다.

difference factor에 대해 indicator 함수 $1(d>0)$ 와 dirac delta 함수 $ \delta(d - p_1 + p_2) $ 를 혼용해서 사용하는데 indicator 함수의 경우도 똑같이 계산되는 지 잘 모르겠음.

dirac delta와 $ f(x) $ 합성곱을 했을 시에 $ f(x) $ 분포 형태는 변화가 없이 위치만 평행 이동하게 됨(sifting property).

두 독립적인 확률 변수 차이는 $ p(d | p_1, p_2) = \mathcal{N}(d | p_1 - p_2, \sigma_1^2 + \sigma_2^2 + \beta^2) $ 이므로

가우시안의 합성곱은 가우시안 형태이다. 결국 마지막 계산 결과는 두 유저의 performance 차이를 구한 값과 같다.(가우시안 차이도 가우시안 형태를 가짐) 유저 1과 유저 2의 퍼포먼스 차이를 $ d $ 라고 할 때, $ p(d) $ 는 가우시안 분포로 $ \mathcal{N}(d | \mu_1 - \mu_2, \sigma_1^2 + \sigma_2^2 + \beta^2) $ 

따라서 $ p(d > 0 | p_1, p_2) = \Phi\left(\frac{\mu_1 - \mu_2}{\sqrt{\sigma_1^2 + \sigma_2^2 + \beta^2}}\right) $ 을 가진다.

아래 계산은 부정확할 수 있음

```python
# performance difference
m5 = gauss_diff(m2, m4)
diff = norm.pdf(x, m5[0], np.sqrt(m5[1]))

# win probabilty
norm.cdf(m5[0]/np.sqrt(m5[1]))
```

0.8316658161949806

### Result Factor

$ p(r | d) $ 은 관측된 값으로 유저 1이 이겼을 때는 $ 1(d > 0) $ 값을 가지고, 지면 $ 1(d < 0) $ 값을 가진다.

유저 1이 이겼다고 가정했으므로 $ 1(d > 0) $ 을 적용한다.

### Result to difference factor

해당 결과를 바로 message passsing하여 유저 1 사전 분포를 업데이트한다고 했을때,

$ 1(d > 0) $ 인 값만 나오게 됨.

$d$가 음수인 구간에서는 $ p(r | d) = 1(d > 0) $ 가 성립하지 않으므로 $ d $ 의 적분 구간은 $ \int_{0}^{\infty} \mathcal{N}(d | \mu, \sigma^2) \,dd $ 로 치환해서

### Performance to skill factor

최종적으로 구하려는 구하려하는 유저 1의 사후 분포는 다음과 같다 (1).

여기서 상대방 유저와 매치 결과에서 오는 메시지는 $ m_1 = \mathcal{N}(\mu_1, \sigma_1^2) $ 

prior와 likelihood를 통해 사후 분포를 계산하면,

이러면 다음 유저 1은 3개의 파라미터를 통해 표현해야되고 다음 계산도 복잡해진다.

→ 따라서 이전 $ 1(d>0) $ 을 가우시안 형태로 근사해주어 모든 메시지가 가우시안이 되도록 만들어준다.

$ 1(d>0) $ 은 지시함수로 바로 가우시안으로 근사하기 힘들다. 대신 difference variable 주변 확률을 가우시안으로 근사해서 대신 사용한다.

belief propagation 식 $ p(d) = p(p_1, p_2) \frac{1(d>0)}{Z} $ 에 따르면 difference variable 주변 확률은 $ \mathcal{N}(\mu, \sigma^2) $ 이다. 그리고 $ \mathcal{N}(\mu, \sigma^2) \frac{1(d>0)}{Z} $ 다시 나누어 $ p(d|r) $ 을 계산한다.(3)

### Approximated result factor to difference factor

먼저 $ p(d|r) \cdot \mathcal{N}(d | \mu, \sigma^2) $ 두 메시지의 곱은 $ d>0 $ 범위로 잘린 가우시안이다.

해당 식은 총 면적이 1이 되지않으므로 proper한 확률 분포가 되도록 normalize해준다.

$ \mathcal{N}(d | \mu, \sigma^2) $ Proper해진 rectified gaussian 분포를 gaussian 분포로 근사해준다.

moment matching을 사용하여 rectfied gaussian의 평균과 분산을 동일하게 갖는 gaussian으로 근사한다. 이를 통해 가장 두 분포의 KL 분산을 최소화할 수 있다.

Rectified gaussian의 평균과 분산은 다음과 같이 구한다.

$ \mathbb{E}_{q}[d] $ 는 rectified 되기 전의 gaussian의 평균과 분산을 의미

$ \min_{\phi} KL(p||q) $ 를 최소화하는 방향으로 고정된 $ p $ 에 대해서 지수족 분포 $ q $ 로 근사하면

$ \min_{\phi} KL(p||q) = \mathbb{E}_{p}[log p - log q] $ 에 관한 함수이므로 독립적인 상수항을 제거해준다.

$ \mathbb{E}_{p}[log q] $ 에 대한 기울기가 0으로 되도록 KL을 최소화해주면

정규 분포를 지수족 분포로 표현 했을 시에, $ p = \mathcal{N}(\mu_p, \sigma_p^2) $ , $ q = \mathcal{N}(\mu_q, \sigma_q^2) $ , $ KL(p||q) = \frac{1}{2}\left(\frac{\sigma_p^2}{\sigma_q^2} + \frac{(\mu_q -

 \mu_p)^2}{\sigma_q^2} + log\frac{\sigma_q^2}{\sigma_p^2}\right) $ 식을 사용

$ p(d) = \frac{p(d,r)}{p(r)} = \mathcal{N}(d | \mu, \sigma^2) \cdot \frac{1(d>0)}{Z} $ 를 $ d > 0 $ 구간만 normalization하고 normalized한 rectified gaussian 분포를 구한 후, gaussian으로 근사해서(앞에서 KL을 최소화 했으므로) normalize 된 gaussian의 moment matching을 통해 구한 후 $ p(d) $ 를 gaussian 분포로 근사해서 전달해주면 $ m_6 $

```python
# rectified gaussian 
def rectified(m1, v1):
  mean = m1 + norm.pdf(m1 / np.sqrt(v1)) / norm.cdf(m1 / np.sqrt(v1)) * np.sqrt(v1)
  var = v1 * (1 + (m1 / np.sqrt(v1) * norm.pdf(m1 / np.sqrt(v1)) / norm.cdf(m1 / np.sqrt(v1))) - (norm.pdf(m1 / np.sqrt(v1)) / norm.cdf(m1 / np.sqrt(v1)))**2)
  return mean, var

# m6
m6 = rectified(m5[0], m5[1])
```

### New difference factor to new performance factor

$ p(d|p_1,p_2) = \mathcal{N}(d | \mu_d, \sigma_d^2) $ 이므로 차이를 구하면 $ \mathcal{N}(p_1 | p_2 + \mu_d, \sigma_d^2) $

다시 marginalization해서 $ p(p_1 | d) \sim \mathcal{N}(p_1 | p_2 + \mu_d, \sigma_d^2) \cdot \mathcal{N}(p_1 | \mu_p1, \sigma_p1^2) $

```python
# new performacne of player 1
m7 = gauss_conv(m6, m4)
```

### New Performance to new skill factor

$ p(p_1 | s_1) = \mathcal{N}(p_1 | s_1, \beta^2) $

최종적으로 convolution 결과 $ p(p_1 | s_1) $ 와 convolution해서 사후 분포를 구한다.

```python
# new skill of player 1
m8 = gauss_conv(m7, m2)
```

### 결과

결과의 mean이 93점으로 약 13점 올랐음

유저 1이 매치에서 이겼기 때문에 유저 1의 skill이 올라가는 형태

```python
# final posterior
m8
```

```python
(92.87877612509184,  22.18861172992057)
```
```