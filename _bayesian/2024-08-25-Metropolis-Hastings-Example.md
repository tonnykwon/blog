---
title: "Metropolis Hastings Example"
date: 2024-08-25
categories: Bayesian
mathjax: true
---

## 암호 해독 문제와 변환 함수

암호문은 알파벳과 1대1 대응되는 암호로 다음과 같이 이루어져 있다.

```python
'[:p1lpl]x:r?!pw:np1]!?pmx;:?!w.;?pl?w!gp1lp/ws-?!prwm?p1?pg]1?pwnm[2?ps-wsp[ m?p.??:psx!:[:rp]m?!p[:p1lp1[:np?m?!pg[:2?5p6z-?:?m?!pl]xp/??;p;[7?p2![s[2[0[:rpw:lp]:?,6p-?ps];np1?,p6vxgsp!?1?1.?!ps-wspw;;ps-?pu?]u;?p[:ps-[gpz]!;np-wm?: sp-wnps-?pwnmw:swr?gps-wspl]x m?p-wn56p-?pn[n: spgwlpw:lp1]!?p.xspz? m?pw;zwlgp.??:px:xgxw;;lp2]11x:[2ws[m?p[:pwp!?g?!m?npzwl,pw:np[px:n?!gs]]nps-wsp-?p1?w:spwpr!?wspn?w;p1]!?ps-w:ps-ws5p[:p2]:g?9x?:2?p[ 1p[:2;[:?nps]p!?g?!m?pw;;pvxnr1?:sg,pwp-w.[sps-wsp-wgp]u?:?npxup1w:lp2x![]xgp:wsx!?gps]p1?pw:npw;g]p1wn?p1?ps-?pm[2s[1p]/p:]spwp/?zpm?s?!w:p.]!?g5ps-?pw.:]!1w;p1[:np[gp9x[27ps]pn?s?2spw:npwssw2-p[sg?;/ps]ps-[gp9xw;[slpz-?:p[spwuu?w!gp[:pwp:]!1w;pu?!g]:,pw:npg]p[sp2w1?pw.]xsps-wsp[:p2];;?r?p[pzwgpx:vxgs;lpw22xg?np]/p.?[:rpwpu];[s[2[w:,p.?2wxg?p[pzwgpu![mlps]ps-?pg?2!?spr![?/gp]/pz[;n,px:7:]z:p1?:5p1]gsp]/ps-?p2]:/[n?:2?gpz?!?px:g]xr-scc/!?9x?:s;lp[p-wm?p/?[r:?npg;??u,pu!?]22xuws[]:,p]!pwp-]gs[;?p;?m[slpz-?:p[p!?w;[0?np.lpg]1?px:1[gsw7w.;?pg[r:ps-wspw:p[:s[1ws?p!?m?;ws[]:pzwgp9x[m?![:rp]:ps-?p-]![0]:cc/]!ps-?p[:s[1ws?p!?m?;ws[]:gp]/pl]x:rp1?:p]!pwsp;?wgsps-?ps?!1gp[:pz-[2-ps-?lp?4u!?ggps-?1pw!?pxgxw;;lpu;wr[w![gs[2pw:np1w!!?np.lp].m[]xgpgxuu!?gg[]:g5p!?g?!m[:rpvxnr1?:sgp[gpwp1wss?!p]/p[:/[:[s?p-]u?5p[pw1pgs[;;pwp;[ss;?pw/!w[np]/p1[gg[:rpg]1?s-[:rp[/p[p/]!r?sps-ws,pwgp1lp/ws-?!pg:]..[g-;lpgxrr?gs?n,pw:np[pg:]..[g-;lp!?u?wspwpg?:g?p]/ps-?p/x:nw1?:sw;pn?2?:2[?gp[gpuw!2?;;?np]xspx:?9xw;;lpwsp.[!s-5'
```

여기서 찾고 싶은 함수는 $$f(x) = y$$로 f는 각각 암호 코드 $$x_i$$를 알파벳 $$y_i$$를 변환시켜주는 변환 함수이다.

실제 변환 함수 f를 모른다고 할 때, 임의로 생선한 변환 함수($$g(x) = \hat y$$)가 f함수와 얼마나 유사한 지 혹은 더 비슷하게 만들 수 있도록 어떻게 개선할 수 있을까?


## MCMC 방법을 통한 최적화
Markov Chain Monte Carlo 방법을 이용하여 암호문을 해독해본다.(해당 예제는 [링크](https://github.com/christinakouridi/MCMC)에서 가져왔다)

목표 함수를 해독문 D에 대해 특정 변환 함수 g가 얼마나 잘 맞는지 평가하는 확률로 표현할 수 있다.
$$P(D|g)$$

만약 어떤 문서 D가 있을 때, 해당 문서에서 하나의 알파벳이 나올 확률은 이전 알파벳에 의해서만 영향을 받는다고 가정하자(단어 혹은 문맥에 대한 정보를 생략).
그렇다면 알파벳 $$y_i$$가 출현했을 때, $$y_j$$가 나올 확률(**전이 확률**)을 $$p(y_j | y_i)$$라고 할때, 전체 문서 D가 나올 확률은 다음과 같이 구할 수 있다.
$$ P(D|g) = \Pi_i^{n-1} p(\hat y_{i+1} | \hat y_i)$$

주어진 데이터가 특정 모델에 의해 설명될 확률(가능도)로 표현 가능하다. 이 확률 값에 로그를 취해 계산을 더 용이하게 할 수 있다.

$$log(P(D|g)) = \sum_i^{n-1} log( p(\hat y_{i+1} |\hat y_i) )$$


그리고 암호 변환 함수를 변경했을 때, 새로운 변환 함수로 나온 해독문의 확률이 기존 변환 함수로 나온 해독문 확률보다 얼마나 높은 지 평가할 수 있다.

$$\frac{P(D|g')}{P(D|g)} = \frac{\Pi_i^{n-1} p(\hat y'_{i+1} |\hat y'_i)}{\Pi_i^{n-1} p(\hat y_{i+1} |\hat y_i)}$$

해당 확률 값에 로그를 취하면 다음과 같다.

$$log(\frac{P(D|g')}{P(D|g)}) = \sum_i^{n-1} log( p(\hat y'_{i+1} |\hat y'_i) ) - \sum_i^{n-1} log( p(\hat y_{i+1} |\hat y_i) )$$

새로운 변환 함수 $$g'$$가 기존 함수보다 더 해독문을 잘 설명하는 지에 따라 수락 여부를 결정하는 수락 확률을 계산할 수 있다.
수락 확률은 다음과 같다.

$$min(1, exp(log(\frac{P(D|g')}{P(D|g)})))$$

## 코드

일반 문서(`war_and_peace.txt`)를 통해 알파벳 간의 전이 확률을 계산한다.
```python
# transition probabilty matrix
n = len(all_letter_list)
cnt_matrix = np.ones((n,n))

# number of biagrams total
all_cnt = 0

for each_sen in doc_replace:
    for idx, each_str in enumerate(each_sen):
        if idx == 0:
            pass
        else:
            a = letter_dict[each_sen[idx-1]]
            b = letter_dict[each_sen[idx]]
            cnt_matrix[a][b] +=1
            all_cnt += 1

# normalize
transition_matrix = cnt_matrix/np.sum(cnt_matrix, axis=1, keepdims=True)

# plot
plt.figure(figsize=(12,10))
sns.heatmap(transition_matrix, cmap="Greys", cbar=False, 
           xticklabels=symbols, yticklabels=symbols)
```

<p align ='center'>
    <img src = "../../assets/img/bayesian/1-mcmc-ex-heatmap.png" style="width: 70%"> <br/>
    <sub>Transition Probatility</sub>
</p>

임의의 변환 함수 g를 생성한다.
```python
# create initial mapping
def initialize_mapping():
    mapping = {}
    for idx, values in enumerate(msg_letter_sorted):
        msg_letter, msg_cnt = values
        mapping[msg_letter] = doc_letter_sorted[idx][0]
        
    return mapping
```

기존 변환 함수에서 새로운 변환 함수를 생성한다.
일정 확률로 둘 중 하나의 작업을 수행한다.
- 기존 변환 함수에서 매핑을 변경
  - a -> c, b -> d였으면 a -> d, b ->c 로 변경
- 기존 변환 함수 공역에는 있으나 치역(y 공간)에는 없는 값을 치역에 추가

```python
def swap_mapping(mapping, random_prob = 0.7):
    """
    Swap current mapping or assign new letter from documents
    """
    mapping_new = mapping.copy()
    # swap codes
    if np.random.ranf() < random_prob:
        a = np.random.choice(list(mapping_new.keys()), 1)[0]
        b = np.random.choice(list(mapping_new.keys()), 1)[0]
        
        if a==b:
            mapping_new = swap_mapping(mapping)
        else:
            # swap code a, b
            # print(f"{a} -> {mapping_new[a]}, {a} -> {mapping_new[b]}")
            temp = mapping_new[a]
            mapping_new[a] = mapping_new[b]
            mapping_new[b] = temp
            
    # get new letter from documents
    else:
        # choose one letter from documents
        a_mapping = np.random.choice(doc_letter, 1)[0]
        # check if b_mapping already is used
        inv_mapping = {letter:code for code, letter in mapping_new.items()}
        if a_mapping not in inv_mapping.keys():
            a = np.random.choice(list(mapping_new.keys()), 1)[0]
            # print(f"{a}->{mapping_new[a]}, {a} -> {a_mapping}")
            mapping_new[a] = a_mapping
        else:
            mapping_new = swap_mapping(mapping)

    return mapping_new
```

n번 반복하여 기존 변환 함수보다 새로운 변환 함수로 나온 해독문이 더 설명력이 좋은 만큼 새로운 함수를 채택한다.
```python
iter_n = 50000
accept_cnt = 0
accept_prob_list = []
log_lik_sum_list = []

# assign intial mapping
mapping = initialize_mapping()

# current log likelihood sum
log_lik_sum = get_log_lik_sum(message, mapping, transition_matrix)
print(message)

for i in range(iter_n):
    
    # new log likelihood sum
    mapping_new = swap_mapping(mapping)
    log_lik_sum_new = get_log_lik_sum(message, mapping_new, transition_matrix)
    
    accept_prob = min(1, np.exp(log_lik_sum_new - log_lik_sum))
    accept_prob_list.append(accept_prob)
    
    if accept_prob > np.random.ranf():
        accept_cnt += 1
        mapping = mapping_new
        log_lik_sum = log_lik_sum_new
        
    if i%5000==0 and i!=0:
        accepted_ratio = np.round((accept_cnt+eps)/(i+eps), 4)
        print(f"""iter: {i}
        mean accepted probability: {np.round(np.mean(accept_prob_list),4)}, accepted ratio: {accepted_ratio}""")
        
        decoded_message = ''.join([mapping[msg] for msg in message])
        print(decoded_message)
        print('')
    log_lik_sum_list.append(log_lik_sum)
```

기존 암호문
```
[:p1lpl]x:r?!pw:np1]!?pmx;:?!w.;?pl?w!gp1lp/ws-?!prwm?p1?pg]1?pwnm[2?ps-wsp[ m?p.??:psx!:[:rp]m?!p[:p1lp1[:np?m?!pg[:2?5p6z-?:?m?!pl]xp/??;p;[7?p2![s[2[0[:rpw:lp]:?,6p-?ps];np1?,p6vxgsp!?1?1.?!ps-wspw;;ps-?pu?]u;?p[:ps-[gpz]!;np-wm?: sp-wnps-?pwnmw:swr?gps-wspl]x m?p-wn56p-?pn[n: spgwlpw:lp1]!?p.xspz? m?pw;zwlgp.??:px:xgxw;;lp2]11x:[2ws[m?p[:pwp!?g?!m?npzwl,pw:np[px:n?!gs]]nps-wsp-?p1?w:spwpr!?wspn?w;p1]!?ps-w:ps-ws5p[:p2]:g?9x?:2?p[ 1p[:2;[:?nps]p!?g?!m?pw;;pvxnr1?:sg,pwp-w.[sps-wsp-wgp]u?:?npxup1w:lp2x![]xgp:wsx!?gps]p1?pw:npw;g]p1wn?p1?ps-?pm[2s[1p]/p:]spwp/?zpm?s?!w:p.]!?g5ps-?pw.:]!1w;p1[:np[gp9x[27ps]pn?s?2spw:npwssw2-p[sg?;/ps]ps-[gp9xw;[slpz-?:p[spwuu?w!gp[:pwp:]!1w;pu?!g]:,pw:npg]p[sp2w1?pw.]xsps-wsp[:p2];;?r?p[pzwgpx:vxgs;lpw22xg?np]/p.?[:rpwpu];[s[2[w:,p.?2wxg?p[pzwgpu![mlps]ps-?pg?2!?spr![?/gp]/pz[;n,px:7:]z:p1?:5p1]gsp]/ps-?p2]:/[n?:2?gpz?!?px:g]xr-scc/!?9x?:s;lp[p-wm?p/?[r:?npg;??u,pu!?]22xuws[]:,p]!pwp-]gs[;?p;?m[slpz-?:p[p!?w;[0?np.lpg]1?px:1[gsw7w.;?pg[r:ps-wspw:p[:s[1ws?p!?m?;ws[]:pzwgp9x[m?![:rp]:ps-?p-]![0]:cc/]!ps-?p[:s[1ws?p!?m?;ws[]:gp]/pl]x:rp1?:p]!pwsp;?wgsps-?ps?!1gp[:pz-[2-ps-?lp?4u!?ggps-?1pw!?pxgxw;;lpu;wr[w![gs[2pw:np1w!!?np.lp].m[]xgpgxuu!?gg[]:g5p!?g?!m[:rpvxnr1?:sgp[gpwp1wss?!p]/p[:/[:[s?p-]u?5p[pw1pgs[;;pwp;[ss;?pw/!w[np]/p1[gg[:rpg]1?s-[:rp[/p[p/]!r?sps-ws,pwgp1lp/ws-?!pg:]..[g-;lpgxrr?gs?n,pw:np[pg:]..[g-;lp!?u?wspwpg?:g?p]/ps-?p/x:nw1?:sw;pn?2?:2[?gp[gpuw!2?;;?np]xspx:?9xw;;lpwsp.[!s-5
```

1000번 반복 이후
```
in m. .oungel any mole durnelapre .eals m. fathel gade me some aydice that i'de peen tulning odel in m. miny edel since? "whenedel .ou feer rike cliticiving an. one," he tory me, "just lemempel that arr the beobre in this wolry haden't hay the aydantages that .ou'de hay?" he yiyn't sa. an. mole put we'de arwa.s peen unusuarr. communicatide in a leseldey wa., any i unyelstooy that he meant a gleat year mole than that? in consequence i'm incriney to leselde arr juygments, a hapit that has obeney ub man. culious natules to me any arso maye me the dictim of not a few detelan poles? the apnolmar miny is quick to yetect any attach itserf to this quarit. when it abbeals in a nolmar belson, any so it came apout that in correge i was unjustr. accusey of peing a boritician, pecause i was blid. to the seclet gliefs of wiry, unknown men? most of the confiyences wele unsought--flequentr. i hade feigney sreeb, bleoccubation, ol a hostire redit. when i learivey p. some unmistakapre sign that an intimate lederation was quideling on the holivon--fol the intimate lederations of .oung men ol at reast the telms in which the. exbless them ale usuarr. bragialistic any malley p. opdious subblessions? leselding juygments is a mattel of infinite hobe? i am stirr a rittre aflaiy of missing something if i folget that, as m. fathel snoppishr. suggestey, any i snoppishr. lebeat a sense of the funyamentar yecencies is balcerrey out unequarr. at pilth?
```

2000번 반복 이후
```
in my younger and more vulnerable years my father gave me some advice that i've been turning over in my mind ever since! "whenever you feel like critici2ing any one," he told me, "just remember that all the people in this world haven't had the advantages that you've had!" he didn't say any more but we've always been unusually communicative in a reserved way, and i understood that he meant a great deal more than that! in consequence i'm inclined to reserve all judgments, a habit that has opened up many curious natures to me and also made me the victim of not a few veteran bores! the abnormal mind is quick to detect and attach itself to this quality when it appears in a normal person, and so it came about that in college i was unjustly accused of being a politician, because i was privy to the secret griefs of wild, unknown men! most of the confidences were unsought--frequently i have feigned sleep, preoccupation, or a hostile levity when i reali2ed by some unmistakable sign that an intimate revelation was quivering on the hori2on--for the intimate revelations of young men or at least the terms in which they express them are usually plagiaristic and marred by obvious suppressions! reserving judgments is a matter of infinite hope! i am still a little afraid of missing something if i forget that, as my father snobbishly suggested, and i snobbishly repeat a sense of the fundamental decencies is parcelled out unequally at birth!
```

5000번 반복 이후
```
in my younger and more vulnerable years my father gave me some advice that i've been turning over in my mind ever since. "whenever you feel like criticizing any one," he told me, "just remember that all the people in this world haven't had the advantages that you've had." he didn't say any more but we've always been unusually communicative in a reserved way, and i understood that he meant a great deal more than that. in consequence i'm inclined to reserve all judgments, a habit that has opened up many curious natures to me and also made me the victim of not a few veteran bores. the abnormal mind is quick to detect and attach itself to this quality when it appears in a normal person, and so it came about that in college i was unjustly accused of being a politician, because i was privy to the secret griefs of wild, unknown men. most of the confidences were unsought--frequently i have feigned sleep, preoccupation, or a hostile levity when i realized by some unmistakable sign that an intimate revelation was quivering on the horizon--for the intimate revelations of young men or at least the terms in which they e?press them are usually plagiaristic and marred by obvious suppressions. reserving judgments is a matter of infinite hope. i am still a little afraid of missing something if i forget that, as my father snobbishly suggested, and i snobbishly repeat a sense of the fundamental decencies is parcelled out unequally at birth.
```

10,000번 반복 이후
```
in my younger and more vulnerable years my father gave me some advice that i've been turning over in my mind ever since, "whenever you feel like criticizing any one." he told me. "just remember that all the people in this world haven't had the advantages that you've had," he didn't say any more but we've always been unusually communicative in a reserved way. and i understood that he meant a great deal more than that, in consequence i'm inclined to reserve all judgments. a habit that has opened up many curious natures to me and also made me the victim of not a few veteran bores, the abnormal mind is quick to detect and attach itself to this quality when it appears in a normal person. and so it came about that in college i was unjustly accused of being a politician. because i was privy to the secret griefs of wild. unknown men, most of the confidences were unsought--frequently i have feigned sleep. preoccupation. or a hostile levity when i realized by some unmistakable sign that an intimate revelation was quivering on the horizon--for the intimate revelations of young men or at least the terms in which they e?press them are usually plagiaristic and marred by obvious suppressions, reserving judgments is a matter of infinite hope, i am still a little afraid of missing something if i forget that. as my father snobbishly suggested. and i snobbishly repeat a sense of the fundamental decencies is parcelled out unequally at birth,
```

1000번 이후부터 어느 정도 영어 단어 형태를 갖추다 2000번부터는 오류가 있지만 읽기가 가능하다.

## Reference

- https://github.com/christinakouridi/MCMC


