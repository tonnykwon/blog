---
title: "4 - Membership"
date: 2019-01-08
categories: Distributed
mathjax: true
---

## Membership

In a distributed system, it is very normal that processes or servers in a group fail. The failed process stop executing any further process and would not recover, which may cause deviation from code. Therefore keeping lists of member who are non-faulty to is essential in any distributed system. Membership protocol is to:

- Detect failure
- disseminate information about joins, leaves, and failures of processes



The quality of the semantics of the list differ:

- Complete lists of processes
- Almost Complete lists  of processes(Gossip)
- Partial Random list(SCAM, T-MAN and Cyclon)



## Failure Detector

Process $$p_j$$ crashes at frequent occurrence, and the frequency grows linearly with size of datacenter. Failure detector has some important rules here:

- **Completeness**: every fail is detected
- **Accuracy**: detected fail is not false
- Speed: time to detect failure
- Scale: Equal load to each member

Completeness and Accuracy are the most important properties of failure detectors. Both of properties, however, are impossible to achieve together in a network where message loss and delay occur. So, in real applications, we trade off between completeness and accuracy.



### Centralized Heartbeating

One of the most basic form of failure detector is centralized heartbeating. Heart beats carry sequence numbers.  $$ p_i$$ sends heartbeat to $$p_j$$ periodically, and every time it increases. When $$p_j $$ receives heartbeat from $$ p_i$$, it updates process i's heartbeat. If the heart beat not received for certain amount time, $$p_j$$ marks process i failed. 

Advantages:

- If any number of processes other than $$p_j$$ fails, process j detects(completeness)
- Easy to implement

Disadvantages:

- No completeness when $$ p_j$$ fails
- Too much overhead to one process

<p align="center">
<img src="../../assets/img/distributed/4-heartbeat.PNG" style="width: 100%"> <br/>
<sub> Centralized Heartbeating slide from CS425 2.2 Failure Detector in week 3</sub>
</p>


Suppose there are N processes and each sends



### Ring Heartbeating

Ring heartbeating is a variant of centralized heartbeating. All processes construct virtual ring, and each process sends and receives heartbeating from its neighbors. It is better than centralized heartbeating in a sense that it does not have much overhead. However, it is not complete since multiple failures of process can go undetected. For instance, if $$p_i$$ and both its neighbors fail, the failure of $$p_i$$ cannot be detected.



<p align="center">
<img src="../../assets/img/distributed/4-ring.PNG" style="width: 100%"> <br/>
<sub> Ring Heartbeating slide from CS425 2.2 Failure Detector in week 3</sub>
</p>



### All-To-All Heartbeating

In all-to-all heartbeating, each processes sends its heartbeat to every other processes.

Advantages:

- Equal load per member
- Complete

Disadvantages:

- One slow receiving process can cause ending up marking all other processes fail. Thus, high rate of false positive or accuracy

<p align="center">
<img src="../../assets/img/distributed/4-all.PNG" style="width: 100%"> <br/>
<sub> All-To-All  Heartbeating slide from CS425 2.2 Failure Detector in week 3</sub>
</p>



## Gossip Style

Another variant of heartbeating is gossip-style failure detector, still shows better robustness. Basic operations are same. Each process contains membership lists of other processes, and receive or send heartbeats periodically. Receiver processes mark any process failure if heartbeats of that process does not get renewed within a timeout.

<p align="center">
<img src="../../assets/img/distributed/4-gossip.PNG" style="width: 100%"> <br/>
<sub> Gossip-Style slide from CS425 2.3 Gossip-Style Membership in week 3</sub>
</p>


So suppose there are 4 processes as we can see in the slide, and each keeps membership list of other processes with their heartbeats, and local time when the process received the heartbeat. On process 1's list, the heartbeats of first and third row is higher than those on the process 2. If process one choose to send its membership list to process 2, the process 2's membership list gets renewed as received heartbeat which are '10120' and '10098' and its local time 70(the list on the right bottom).

In gossip-style, there are two kinds of timeouts:

- $$T_{fail}$$: time intervals after which a process marks another fail, if the heartbeat has not increased

- $$T_{cleanup} $$: time intervals after which a process delete the member from the list after marking it as failure

If a process delete a member from the list right after $$T_{fail}$$, any other processes that have not checked that failed process as failure may send the list including the failed process. As a result, the process who deleted failed process perceive the previously deleted member as new one, and add the member in the list. That is why there is $$ T_{cleanup} $$. Processes wait for other processes mark the failed process as failure.



A single heartbeat takes O(log(N)) time to propagate to all other processes as discussed in the Gossip Protocol post.



## Comparison

Now we can compare protocols based on above criteria:

- Completeness: guarantee always
- Accuracy: probability PM(T)
- Speed: T time units to first detection of a failure
- Scale: N*L compare across protocols



Let's denote $$T =$$ time units process sending out heartbeat, and $$N = $$ number of processes,  $$L=$$ message load per process in terms of T, and tg​ = period takes to send O(n) gossip messages (every process chooses k targets, k*N = O(N)).



**All-To-All Heartbeating**

It takes T time unit that processes find out failure, since every process sends out heartbeat to any other processes. Load increases linear with size:

$$ L = N / T $$

**Gossip**

As it takes log(n) time to propagate to all other processes, time takes all processes for the detection is:

$$ T = log(n) * tg $$

As each member contains entire members, load for a process is O(n) per gossip period $$ tg $$. And $$ tg $$ can be substituted in terms of $$ T $$ and $$N $$:

$$ L = N / tg = N * log(n) / T $$



As we compared, gossip message load is lower than heartbeating protocol, since heartbeating sends more messages with higher accuracy.



## SWIM Failure Detector

SWIM(Scalable Weakly consistent Infection style Membership protocol) uses pining instead of heartbeating.

<p align="center">
<img src="../../assets/img/distributed/4-swim.PNG" style="width: 100%"> <br/>
<sub> SWIM slide from CS425 2.5 Another Probablistic Failure Detector in week 3</sub>
</p>

Process pi ping randomly chosen process pj. If a process receives a ping message, it responds back with ack(acknowledgement). If pi receives ack message, everything is fine. However, if pj fails or ping message drops, pi indirectly check whether pj fails. It choose another third process and send ping, and that chosen process sends ping to pj. pj responds ack to the third process sends ack message to pi. If pi neither receive direct or indirect message, it marks pj failed.

<p align="center">
<img src="../../assets/img/distributed/4-swim-vs-heartbeat.PNG" style="width: 100%"> <br/>
<sub> SWIM slide from CS425 2.5 Another Probablistic Failure Detector in week 3</sub>
</p>

For heartbeat protocol, if we decrease process load we get high detection time. For instance, if we set process load constant, then detection time would be O(n). In other case when we decrease detection time, we get high process load.

For SWIM, it has both constant load and time.

Probability of not being pinged by other process in T(ping period):

$$ 1- \frac{1}{N} $$

Probability of not being pinged by all other N-1 processes:

$$(1-\frac{1}{N})^{N-1}$$

Then, probability of being pinged by any process in T:

$$1-(1-\frac{1}{N})^{N-1}$$

If N goes infinite, we get:

$$ 1 - e^{-1} = \frac{e} {e-1} $$

Thus, expected detection time is:

$$ E[T] = T \frac{e}{e-1} $$



<p align="center">
<img src="../../assets/img/distributed/4-swim-analysis.PNG" style="width: 100%"> <br/>
<sub> SWIM Analysis from CS425 2.5 Another Probablistic Failure Detector in week 3</sub>
</p>

So expected detection time is constant which is independent of group size N. By changing k, number of ping targets, in time period.





## Reference

- CS425 Distributed System by professor Indranil Gupta
