---
title: "3 - Gossip Protocol"
date: 2019-01-08
categories: Distributed
mathjax: true
---


## Multicast

Multicast is a group communication in computer networking. It is a way of transferring data in a designated group. Multicast can be one-to-many or many-to-many communication. Since it uses UDP, message loss or delay may happen.



Here are two important requirements of multicast protocol:

- Fault-Tolerance: Multicast should be reliable so that all the non-fault nodes receive messages
- Scalability: Multicast protocol can work with a big number of nodes. The overhead of a node should not be too high that it can accommodate lots of nodes.

<p align="center">
<img src="../../assets/img/distributed/3-multicast.PNG" style="width: 100%"> <br/>
<sub> Multicast slide from CS425 1.1 Multicast Problem</sub>
</p>

The simplest multicast protocol is a centralized protocol. The sender has a receiver list, and it simply sends the message to all the receivers. Implementation of the centralized approach is clear, but the overhead of the sender is high and it is failure prone.

Another approach is tree-based multicast. Nodes in the group build a spanning tree. If it is a balanced tree with N number of nodes, the latency of a message is O(log(N)) as the height of the tree is O(log(N)). However, maintaining and developing the tree is difficult. Whenever a new node comes, we need to change the structure of the tree.



## Gossip Protocol

Another way of multicasting is using gossip protocol. So, a node periodically sends a message to a certain number of random targets. Once a node received a message, it becomes *infected*. The infected node should send the received message to random targets periodically.

<p align="center">
<img src="../../assets/img/distributed/3-gossip.PNG" style="width: 100%"> <br/>
<sub> Gossip Protocol slide from CS425 1.2 Gossip Protocol</sub>
</p>

There are two types of gossip protocol; Push and Pull.

- Push
  - Turns into infected when receives a message. Gossip a random subset of messages received(priority or recently received)
- Pull
  - Periodically poll randomly selected processes for messages that are new



### Push Gossip Analysis

Gossip analysis has advantages of

- Lightweight in large groups
- Spread a multicast fast
- Highly fault-tolerant



Suppose contact rate of node is $$ \beta$$, number of uninfected at time 0 $$ x_0 =n $$, number of infected at time 0 $$ y_0 =1 $$, and thus, $$ x + y = n+1 $$ all the time.

Now we can write rate of change from uninfected to infected as follow:

$$ \frac{dx}{dt} = -\beta x y $$

That is, $$x $$ times $$ y $$ is total combination of contact between infected and uninfected, and among those, only fraction $$ \beta $$ are infected.

By substituting $$ y$$ with $$ y = n-x+1 $$, we can derive solution(refer below Appendix for solution):

$$ x = \frac{n(n+1)}{n+e^{\beta(n+1)t}},\;\;  y = \frac{(n+1)}{1+ne^{-\beta(n+1)t}} $$

The t on denominator indicates, ever round x increases and y decreases.



Since one infected node choose one uninfected node out of n is $$\beta = \frac{b}{n} $$, substituting t with $$ clog(n)$$ gives:

$$ y = \frac {n+1} {1+n e^{-b/n (n+1)clog(n)}} \approx \frac{n+1}{1+ \frac{1}{n^{cb-1}}} $$

$$ \approx (n+1)(1-\frac{1}{n^{cb-1}}) $$

$$ \approx (n+1) - \frac{1}{n^{cb-2}} $$



This implies the above advantages of gossip:

<p align="center">
<img src="../../assets/img/distributed/3-push-pro.PNG" style="width: 100%"> <br/>
<sub> Gossip Protocol slide from CS425 1.3 Gossip Analysis</sub>
</p>



## Pull Gossip Analysis

Let $$p_i$$ be the fraction of non-infected processes at round i. Then the probability of an uninfected node poll uninfected k nodes is $$ (p_i)^{k} $$. Therefore the probability of an uninfected node after round i+1 is staying uninfected until round i multiplied poll uninfected nodes with pull k:

$$ p_{i+1} = (p_i)^{k+1} $$

The second half of pull gossip finished in time O(log(log(n))), which is faster than push gossip with O(log(n)).



On the next post, we will see how gossip used in maintaining membership protocol.



## Reference

- CS425 Distributed System by professor Indranil Gupta
- Nathan Nard. (2018, September 13). Epidemiology Differential Equations Solution 
  Retrieved from https://www.youtube.com/watch?v=fLAqNFdnaJw



## Apendix

Solution for push gossip analysis:

$$ \frac {dx}{dt} = -\beta x (n-x+1) $$

$$ \frac {dx} {x(n-x+1)} = -\beta x dt $$

$$ \frac {dx} {x(n+1)} + \frac{dx}{(n+1-x)(n+1)} = \beta dt, \; since$$

$$ \frac{1}{x(n+1-x)} = \frac{1}{x(n+1)} + \frac{1}{(n+1-x)(n+1)} $$

 Integrate:

$$ \frac{lnx}{n+1} - \frac{ln(n+1-x)}{n+1} = -\beta t + c $$

$$ ln(\frac{x}{n+1-x}) = -\beta(n+1)t +c $$

$$ \frac{x}{n+1-x} = exp(-\beta(n+1)t +c) $$

$$ x = (n+1-x)exp(-\beta(n+1)t +c))$$

$$ x+x \;exp(-\beta(n+1)t +c) = (n+1) exp(-\beta(n+1)t +c))$$

$$ x(1+exp(-\beta(n+1)t +c)) = (n+1) exp(-\beta(n+1)t +c))$$

$$ x = \frac{(n+1)exp( -\beta(n+1)t +c))} {(1+exp(-\beta(n+1)t +c)) } $$

 $$ x = \frac{n+1}{(1+exp(-\beta(n+1)t +c)) exp(\beta(n+1)t - c)} $$

$$ x = \frac{n+1}{1 + e^{\beta(n+1)t-c}} $$



When $$ t= 0 $$, $$ x = n $$ so:

$$ n = \frac{n+1}{1+Ce^0} $$

$$ 1+C = \frac{n+1}{n} $$

$$ C= 1/n $$