---
title: "2 - MapReduce"
date: 2019-01-01
categories: Distributed
---
## What is MapReduce?
MapReduce is a framework and implementation for processing and generating big data. The term originally comes from functional language such as Lisp. As name suggests, MapReduce consists of two procedures, mapping and reducing. <br/>
In mapping phase, each node parallelly processes individual record to generate intermediate key and value pairs, which later passed to reduce phase.
<p align="center">
<img src="../../assets/img/distributed/2-map.png" style="width: 100%"> <br/>
<sub> Mapper slide from CS425 3.1 MapReduce Paradigm</sub>
</p>
As each node can apply the map function independently from other nodes, we can split large data to smaller pieces and assign each part to multiple map tasks. This allows handling large data. <br/>
In the above slide, it is an simple exmaple of using MapReduce to get word count. The mapping function maps each word as a key, and 1 as a value.

In reduce phase, similar to map phase, each nodes perform reduce function to all outputs of map operation that share the same key.
<p align="center">
<img src="../../assets/img/distributed/2-reduce.png" style="width: 100%"> <br/>
<sub> Reducer slide from CS425 3.1 MapReduce Paradigm</sub>
</p>
The slide shows that the reduce function merge by keys, which are words here, and sum up the values. As a result, whole MapReduce outputs counts for each word. 

## MapReduce Example
Other than the simple example of word counting, we can utilize MapReduce function a bit complicated problem. Suppose we want to find sets of three people who follow each other on Instagram. We need to produce <a,b,c> where a, b, and c follow each others, given input <a,b> pair, meaning a follows b. Following is the pseudo code of MapReduce of the function:
``` r
Mapper1(key, value) # where key follows value
{
	emit(<key, value>)
}
Reducer1(key, list) # list of followed values
{
	if list.length >= 2
    	for i in 1 to list.length
        	for j in i to list.length
           	 	emit(<sort(list(key, i,j)),1>)
}
# second MapReduce
Mapper2(key, value) # value is list
{
	emit(<key, value>)
}
Reducer2(key, value)
{
	if sum(list of values) == 3 emit(key)
}
```
On first MapReduce function, it creates all 2 combinations of values with key. If any group of three people follow each others, each person produces list of the group member. Thus, we get three same list of group members. So on second phase, it emits lists of groups who has value of 3.

## Reference
- CS425 Distributed System by prof Indranil Gupta