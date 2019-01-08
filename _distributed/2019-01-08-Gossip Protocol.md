---
title: "3 - Gossip Protocol"
date: 2019-01-08
categories: Distributed
---


## Multicast

Multicast is a group communication in computer networking. It is a way of transferring data in a designated group. Multicast can be one-to-many or many-to-many communication. Since it uses UDP, message loss or delay may happen.



Here are two important requirements of multicast protocol:

- Fault-Tolerance: Multicast should be reliable so that all the non-fault nodes receive messages
- Scalability: Multicast protocol can work with a big number of nodes. The overhead of a node should not be too high that it can accommodate lots of nodes.

<p align="center">
<img src="../../assets/img/distributed/3-multicast.png" style="width: 100%"> <br/>
<sub> Multicast slide from CS425 1.1 Multicast Problem</sub>
</p>

The simplest multicast protocol is centralized protocol. The sender has a receiver list, and it simply sends the message to all the receivers. Implementation of centralized approach is clear, but overhead of sender is high and it is failure prone.

Another approach is tree-based multicast. Nodes in the group build a spanning tree. If it is a balanced tree with N number of nodes, the latency of a message is O(log(N)) as the height of tree is O(log(N)). However, maintaining and developing the tree is difficult. Whenever new node comes, we need to change the structure of tree.



## Gossip Protocol

Another way of multicasting is using gossip protocol. So, a node periodically sends a message to a certain number of random targets. Once a node received a message, it becomes *infected*. The infected node should send the received message to random targets periodically.

<p align="center">
<img src="../../assets/img/distributed/3-multicast.png" style="width: 100%"> <br/>
<sub> Gossip Protocol slide from CS425 1.2 Gossip Protocol</sub>
</p>

There are two types of gossip protocol; Push and Pull.

- Push
  - Turns into infected when receives message. Gossip a random subset of messages received(priority or recently received)
- Pull
  - Periodically poll randomly selected message 



### Gossip Analysis

Gossip analysis has advantages of

- Lightweight in large gorups
- Spread a multicast fast
- Highly fault-tolerant



Suppose contact rate of node is $$ \beta$$, number of uninfected at time 0 $$ x_0 =n $$, number of infected at time 0 $$ y_0 =1 $$, and thus, $$ x + y = n+1 $$ all the time.

Now we can write rate of change from uninfected to infected as follow:

$$ \frac{dx}{dt} = -\beta x y $$

That is, $$x $$ times $$ y $$ is total combination of contact between infected and uninfected, and among those, only fraction $$ \beta $$ are infected.



## Reference

- CS425 Distributed System by professor Indranil Gupta