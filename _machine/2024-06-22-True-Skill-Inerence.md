---

title: "TrueSkill 추론 (2)"
date: 2024-06-22
categories: Machine
mathjax: true
tags: [machine-learning, metric, binary, skill-rating]
---

# Trueskill 추론(2)

## 기본 모델 추론

매치 결과가 주어졌을 때, 유저 실력 점수가 어떻게 업데이트되는 지 확인해본다.

Trueskill에서 모든 메시지는 가우시안이거나 가우시안으로 근사된다. 이해를 위해 canonical form 대신 $$\mu$$, $$\sigma$$로 표현한다.

위 factor graph 유저 1 실력은 $$\mathbf{s}_1$$, 유저 2 실력은 $$\mathbf{s}_2$$ 라고 가정한다.

유저 1이 이겼을때, 유저 1의 사후 실력을 추론한다고 하면, 위 belief propagation에서 (1)번 식을 사용하여 다음과 같이 구할 수 있다.

$$p(s_1 \mid r) = m_{ f_{s1} \rightarrow s_1 } \cdot m_{ f_{p1} \rightarrow s_1}$$

여기서 $$p(s_1) = m_{ f_{s1} \rightarrow s_1 }$$ 은 미리 주어지는 유저 1의 사전 실력 분포, $$r$$은 경기 결과, $$p(r \mid s_1) = m_{ f_{p1} \rightarrow s_1}$$는 경기 결과로 얻은 likelihood이다.

$$m_{ f_{s1} \rightarrow s_1 }$$는 사전에 주어지는 유저 1의 실력 분포로 $$N(80, 30^2)$$ 이다.

$$m_{ f_{p1} \rightarrow s_1}$$는 그 외 변수들에서 오는 메시지에 의존하여 모든 메시지를 계산해주어야 한다. 아래 흐름에 따라 메시지를 계산한다.


<p align ='center'>
    <img src = "../../assets/img/machine/message_scheudle.png" style="width: 50%"> <br/>
    <sub>Factor Graph</sub>
</p>

### Skill Factor

<p align ='center'>
    <img src = "../../assets/img/machine/msg_13.png" style="width: 50%"> <br/>
    <sub>Msg 13</sub>
</p>

$$m_1$$, $$m_3$$는 사전에 주어진 정보로 그대로 전달된다

스킬 $$s_1$$에서 factor $$p_1$$으로 가는 메시지의 경우 $$m_1$$과 동일하여 따로 표기 생략

$$m_{s_1 \rightarrow f_{p1}} = m_1, m_{s_2 \rightarrow f_{p2}} = m_3$$


<details 그래프 및 코드>

<p align ='center'>
    <img src = "../../assets/img/machine/prior_dist.png" style="width: 50%"> <br/>
    <sub>Prior dist</sub>
</p>

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

</details>


### Performance factor

<p align ='center'>
    <img src = "../../assets/img/machine/msg_24.png" style="width: 50%"> <br/>
    <sub>Message</sub>
</p>

Belief propagation에 따라 factor $$p_1$$에서 변수 $$p_1$$으로 나가는 메시지는

$$
\begin{align}m2 & = \int f_{p_1}(s_1, p_1) \cdot m_{s_1 \rightarrow f_{p_1}}(p_1) d s_1 \\&  =  \int N(s_1, 80, 30^2) \cdot N(p_1, s_1, 5^2) d s_1 \\& = \int N(s_1, 80 , 30^2) \cdot N(p_1, s_1 \cdot 1 + 0, 5^2) d s_1 \\& = N( p_1, 80, 30^2 + 5^2 )\end{align}
$$

<details x에 대한 다변량 주변 가우시안 분포와 x가 주어졌을 때의 y의 조건부 분포가 주어졌을때,>

$$
p(x) = N(x \mid \mu, \Lambda^{-1}) \\ p(y \mid x) = N(y \mid Ax + b, L^{-1}) \\ p(y) = N(y \mid A\mu + b, L^{-1} + A\Lambda^{-1}A^T)
$$

식 2.115 Bishop[2006]
</details>

<details 스킬 분포에서 표준편차만 커짐>

<p align ='center'>
    <img src = "../../assets/img/machine/perf_dist.png" style="width: 50%"> <br/>
    <sub>Performance Distribution</sub>
</p>

skill 분포에서 표준편차만 커짐

```python
# m2, m4 
m2 = (m1[0], m1[1] + beta**2)
m4 = (m3[0], m3[1] + beta**2)
```

</details>

### Difference factor

<p align ='center'>
    <img src = "../../assets/img/machine/msg_5.png" style="width: 50%"> <br/>
    <sub>Message</sub>
</p>

$$f_d$$는 두 메시지의 차이를 구한다. belief propagation (2)를 통해 구할 수 있다.

$$
\begin{align}m_5 & = \int m_2 \cdot m_4 \cdot f_d (p_1, p_2, d) dp_1 dp_2  \\& = \int N(p_1 ; \mu_1, \sigma_1^2 + \beta^2) \cdot N(p_2 ; \mu_2, \sigma_2^2 + \beta^2) \cdot\delta(d - p_1 + p_2) d p_1 d p_2 \\& = \int N(p_1 ; \mu_1, \sigma_1^2 + \beta^2)  \int_{-\infty}^\infty N(p_2 ; \mu_2, \sigma_2^2 + \beta^2) \delta(d - p_1 + p_2) d p_2 d p_1 \\\end{align}
$$

difference factor에 대해 indicator 함수($$\mathbb{I}(p_1 - p_2)$$) 와 dirac delta 함수($$\delta(d - (p_1 - p_2))$$)를 혼용해서 사용하는데 indicator 함수의 경우도 똑같이 계산되는 지 잘 모르겠음

dirac delta와 $f(x)$ 합성곱을 했을 시에 $f(x)$ 분포 형태는 변화가 없이 위치만 평행 이동하게 됨(sifting property).

$$
\begin{align}& = \int N(p_1 ; \mu_1, \sigma_1^2 + \beta^2) \int_{-\infty}^\inftyN(p_2; \mu_2, \sigma_2^2 + \beta^2 ) \delta(p_2 - (p_1 - d) ) d p_2 d p_1 \\& = \int N(p_1 ; \mu_1, \sigma_1^2 + \beta^2) N(p_1 - d; \mu_2, \sigma_2^2 + \beta^2 ) d p_1 \\& = \int f_{p_1} (p_1)\cdot f_{p_2} (p_1 - d) d p_1\end{align}
$$

두 독립적인 확률 변수 차이는 $$f_{x- y}(z) = \int f_x(x) f_y(x-z) dx$$이므로
$$
\begin{align}&= f_{p_1 - p_2} (d)\\& = p_1 - p_2 \\& = m_2 - m_4\end{align}
$$

가우시안의 합성곱은 가우시안 형태이다. 결국 마지막 계산 결과는 두 유저의 performance 차이를 구한 값과 같다.(가우시안 차이도 가우시안 형태를 가짐)유저 1과 유저 2의 퍼포먼스 차이를 $$d$$라고 할 때, $$d = p_1 - p_2$$는 가우시안 분포로 
$$d \sim N(\mu_{p1} - \mu_{p2}, \sigma^2_{p1} + \sigma^2_{p2} - 2 \rho \sigma_{p1} \sigma_{p2})$$
따라서 $$N(d ; \mu_1 - \mu_2, \sigma_1^2 + \sigma_2^2 + 2 \beta^2)$$을 가진다.


더 큰 performance 값을 가진 유저가 이긴다고 가정했기 때문에, 유저 1이 이길 확률은 performance 차이 분포에서 차이가 0보다 큰 부분이다.
$$
P(p_1 > p_2) = P(d >0 ) = \Phi (\frac{\mu_1 - \mu_2}{\sqrt{\sigma_1^2 + \sigma_2^2 + 2 \beta^2}}) = \Phi(\frac{\mu_d}{\sigma_d})
$$

유저 1과 유저 2의 퍼포먼스 차이 분포

0보다 큰 부분 면적이 유저 1이 유저 2를 이길 확률을 나타냄

```python
def gauss_diff(msg1, msg2):
    mu1, var1 = msg1
    mu2, var2 = msg2

    mu_new = mu1 - mu2
    var_new = var1 + var2

    return mu_new, var_new

# performance difference
m5 = gauss_diff(m2, m4)
diff = norm.pdf(x, m5[0], np.sqrt(m5[1]))

# win probability
norm.cdf(m5[0]/np.sqrt(m5[1]))

0.8316658161949806
```

### Result Factor

$r$은 관측된 값으로 유저 1이 이겼을 때는 $d > 0$ 값을 가지고, 지면 $d < 0$ 값을 가진다.

유저 1이 이겼다고 가정했으므로 $r = 1_{[d > 0]}$ 을 적용한다.

### Result to difference factor

해당 결과를 바로 message passing하여 유저 1 사전 분포를 업데이트한다고 했을때,

$d > 0$ 인 값만 나오게 됨.

$d$가 음수인 구간에서는 $1_{[d > 0]}$ 가 성립하지 않으므로 $d$의 적분 구간은 $d \geq 0$

로 치환해서 $1_{[d > 0]}$ 

### Performance to skill factor

최종적으로 구하려는 구하려하는 유저 1의 사후 분포는 다음과 같다 (1).

여기서 상대방 유저와 매치 결과에서 오는 메시지는 $N(d|\mu_{d},\sigma_{d}^2)$ 

prior와 likelihood를 통해 사후 분포를 계산하면,

이러면 다음 유저 1은 3개의 파라미터를 통해 표현해야되고 다음 계산도 복잡해진다.

→ 따라서 이전 $1_{[d > 0]}$ 을 가우시안 형태로 근사해주어 모든 메시지가 가우시안이 되도록 만들어준다.

$1_{[d > 0]}$은 지시함수로 바로 가우시안으로 근사하기 힘들다. 대신 difference variable 주변 확률을 가우시안으로 근사해서 대신 사용한다.

belief propagation 식 $N(d|d_{new},\sigma_{new}^2)$ 에 따르면 difference variable 주변 확률은 $N(d|\mu_{d},\sigma_{d}^2)$ 이다. 그리고 $N(d|d_{new},\sigma_{new}^2)$ 다시 나누어 $N(d|d_{new},\sigma_{new}^2)$ 을 계산한다.(3)

### Approximated result factor to difference factor

먼저 $m_5p(o|d)$ 두 메시지의 곱은 $d>0$ 범위로 잘린 가우시안이다.

해당 식은 총 면적이 1이 되지않으므로 proper한 확률 분포가 되도록 normalize해준다.

Proper해진 rectified gaussian 분포를 gaussian 분포로 근사해준다.

moment matching을 사용하여 rectfied gaussian의 평균과 분산을 동일하게 갖는 gaussian으로 근사한다. 이를 통해 가장 두 분포의 KL 분산을 최소화할 수 있다.

Rectified gaussian의 평균과 분산은 다음과 같이 구한다.

$\mu$는 rectified 되기 전의 gaussian의 평균과 분산을 의미

$\mathbb{KL}(P \parallel Q)$를 최소화하는 방향으로 고정된 $p$에 대해서 지수족 분포 $q$로 근사하면

$Q$에 관한 함수이므로 독립적인 상수항을 제거해준다.

$\mathbb{KL}(P \parallel Q)$에 대한 기울기가 0으로 되도록 KL을 최소화해주면

정규 분포를 지수족 분포로 표현 했을 시에, $\eta_{1} = \frac{\mu}{\sigma^2}$, $\eta_{2} = -\frac{1}{2\sigma^2}$, $A(\eta) = \frac{\mu^2}{2\sigma^2} + \frac{1}{2} \log(2\pi\sigma^2)$이고 이에 대해 풀면

$p$의 1차, 2차 적률과 $q$의 평균 분산을 같도록 설정함으로서 쿨백 라이블러 발산을 최소화할 수 있음

Bishop PRML 식 2.226, 식 10.186 참조

```python
# truncated gaussian upper bound only(win case)
def v_wl(t, e):
    return norm.pdf(t-e)/norm.cdf(t - e)

def w_wl(t, e):
    return v_wl(t, e)*(v_wl(t , e) + (t-e))

# approximate truncated gaussian
# proj(m_5 * p(o|d))
t_mu = m5[0] + np.sqrt(m5[1])*v_wl(m5[0]/np.sqrt(m5[1]), 0)
t_var =

 m5[1]*(1-w_wl(m5[0]/np.sqrt(m5[1]), 0))

trunc = norm.pdf(x, t_mu, np.sqrt(t_var))

# truncated gaussian
diff = np.where(x >= 0, diff, 0)
diff /= np.trapz(diff, x)
plt.plot(x, diff, label='real')
plt.plot(x, trunc, label='approximate')
plt.legend()
plt.show()
```

해당 식을 유도하여 계산하면 rectified gaussian의 평균과 분산은 다음과 같다.

유저 1이 이길 확률이 $p$일 때

해당 rectified gaussian 분포를 평균과 분산을 같게하는 가우시안으로 근사하면

### Skill Update

최종적으로 해당 분포를 통해 유저 1의 실력을 업데이트 한다. 사후 분포는 다음과 같다.

이후 업데이트된 분포는 다른 유저와의 매치에서 사전 분포로 활용할 수 있다.