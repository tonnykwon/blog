---
title: "1 - Cloud Concept"
date: 2018-01-01
categories: Distributed
---
## What is Cloud?
Cloud is a bunch of storage that can store big amount of data with computed cycle located data moving around.
Single Data center consists of:
- Compute nodes
- Switches, connecting the racks
- A network topology
- Storage nodes connected to the network
- Front-end for submitting jobs and receiving client requests
- Software servcies

Before cloud computing, the distributed system has existed from 1940. However, there are several differences that distinguish the cloud computing from previous distributed system.
- Massive scale: Data Centers store and process large amount of data
- On-demand access: Only pay for the amount of used.
- Data-intensive nature: Data stored and processed in real time
- New cloud programming paradigm: MapReduce/Hadoop,NoSQL/ Cassandra/ MongoDB and others
-- High in accessibility and eas of programmability
-- Lots of open-source

## What is Distributed System?
Cloud is just another name of a distributed system, and all of the features of the cloud actually are the features of a distributed system? then what is a distributed system exactly?

This is definition by prof.Indranil Gupta: <br/>
A distributed system is a collection of entities, each of which is *autonomous, programmable, asynchronous, and failure-prone*, and which communicate through an *unreliable* communication medium

Each entity in a distributed system can work its own, implying autonomus. And they are programmable, which means we can write code to run processes in the entities. Asynchronous means that each entity runs its own clock and is different from other entities. Failure prone indicates that entities may crash and not work sometimes.
Unreliable property of communication suggests that messages sending and receiving may get lost.

## Reference
- CS425 Distributed System by prof Indranil Gupta