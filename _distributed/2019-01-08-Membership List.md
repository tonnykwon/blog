---
title: "4 - Membership"
date: 2019-01-08
categories: Distributed
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



## Reference

- CS425 Distributed System by professor Indranil Gupta
