---
title: "2 - Vector Space Model"
date: 2019-01-12
categories: Text
---

In the last post, we talked about the vector space model for calculating relevance between a document and a query. Here we go a little deeper about the vector space model. Recall that vector space model computes relevances based on similarity. Let's start with the basic vector space model, bit vector representation.

## Bit Vector Representation

We use our vocabulary to define dimensions. That is, each dimension represents each term in vocabulary V, and if the term is present, it is 1, otherwise 0.

<p align="center">
    <img src= "../../assets/img/text/2-bit.PNG" style="width:100%">
    <sub>Bit vector slide from CS410 Lesson 1.6: Vector Space Retrieval Model</sub>
</p>

Now we can place documents and queries in the vector space. A common way of computing similarity between a document and a query is a dot product. The dot product computes how many words are present in both a document and a query. This simple way works, because the more words occur simultaneously, the higher similarity the document gets. However, there are some limitations. Suppose we queried **text mining with** to find documents that are related to text mining algorithms:

d1: ... **text mining** ... **mining** techniques...  			$$ f(q,d) = 2 ​$$

d2: ... **text mining with text**  analysis... 			$$  f(q,d) = 3 $$

d3: ... available on **text** ... 							$$  f(q,d) = 1  $$

d4: ... **mining** new knowledge **with** given **text** book ...  $$  f(q,d) = 3  $$

Based on the dot product, similarities of each document is 2, 3, 1, 3. With bit representations, d2 with a higher frequency of words get the same score with d1. Also, words **text** and **mining** seem to be more important than frequently appeared words **with**.



## Term Frequency

Term frequency solves the frequency issue of bit representations by counting a term occurrence in a document:

$$ TF(w, d) = count(w,d) $$

Apply term frequency to the previous example:

d1: ... **text mining** ... **mining** techniques 			$$ f(q,d) = 3 $$

d2: ... **text mining with text**  analysis... 			$$  f(q,d) = 4 $$

d3: ... available on **text** ... 							$$  f(q,d) = 1  ​$$

d4: ... **mining** new knowledge **with** given **text** book ... $$  f(q,d) = 3  $$

Now d2 has higher score than d4, since it has higher frequencies of words. d1 has same score with d4, however, due to the word **with**. Words like with, which does not carry important meaning called **stop word**. Stop words appear in most of documents, and matching them do not have significant meaning. Thus, we need to distinguish words that actually carry contents such as **text** and **mining**, with words occur everywhere. 



## Inverse Document Frequency

Inverse document frequency is a key for solving above the mentioned problem. It is *inverse* because we reward a word that does not occur in other documents. This way we can score high for rarely occurring words, while decrease the score of  stop words. IDF defined as:

$$ IDF(w) = log(\frac{M + 1}{df(w)}), \; where \; M = $$ the total number of  documents

Suppose we have 1000 documents and **with** occur 500 times and **mining** occur 100 times. Then:

$$ IDF(with) = log(\frac{1001}{500}) \approx 0.301 $$

$$ IDF(mining) = log(\frac{1001}{100}) \approx 1 $$



Now recalculate similarity of document 1 and 4 with TF-IDF Weighting:

$$ f(q, d) = \sum_{i=1}^N x_i, y_i = \sum_{w \in q \cap d}  c(w,q) c(w,d) log\frac{M+1}{df(w)}​$$

d1: ... **text mining** ... **mining** techniques 			$$ f(q,d) = 3 ​$$

d4: ... **mining** new knowledge **with** given **text** book ... $$  f(q,d) = 3  $$

​         V = { text, mining, with}

IDF(w) =    0.7,       1,      0.3

​       d1 =    1\*0.7,  2\*1,    0			= 2.7

​       d4 =    1\*0.7,  1\*1,    0.3*1		= 2.3

With low **with** score, d1 ranks higher than d4.



However, with TF transformation, it has an issue of assigning too high scores for the document with repeatedly using same words. For instance, if document 1 uses the word **mining** two frequently, it would rank high unnecessarily.

<p>
    <img src= "../../assets/img/text/2-tfidf.PNG" style = "width:100%">
    <sub>TF transformation slide from CS410 2.2 TF Transformation</sub>
</p>

By using log(1+x) or log(1+log(1+x)) instead of just using counts of words, the importance of multiple occurrence of words decrease. Otherwise, the rank of document increases linearly with the occurrence of certain words. 

Although there are various functions of penalizing multiple occurrence of words, **BM 25** transformation comes out to work very well.

<p>
    <img src= "../../assets/img/text/2-bm.PNG" style = "width:100%">
    <sub>BM25 transformation slide from CS410 2.2 TF Transformation</sub>
</p>

By the nature of BM25, it is upper-bounded by (k+1). If we set k high, we are more likely to score similarly with TF transformation. On the other hand, if we set k to 0, it works just like bit transformation. Entire formula of BM25 will be covered sooner.



## Document Length Normalization

Obviously, documents with longer length have more words, and possibly much more diverse words. That is, simple TF transformation ends up ranking long documents high, while leaving short but relevant documents low. Pivoted length normalization rewards or penalizes documents based on their lengths. It uses like average document length as "pivot":

$$ normalizer = 1 - b +b \frac{|d|}{avdl}, \; where \; b \in [0,1] ​$$

\|d\| is the length of document and avdl is the average document length.

<p>
	<img src = "../../assets/img/text/2-doclen.PNG">
    <sub>Pivoted Length Normalization. Reprinted from Text data management and analysis: a practical introduction to information retrieval and text mining(p.107), by Zhai, C., & Massung, S. (2016). Morgan & Claypool.</sub>
</p>

As the name suggests, if the length of a document is on average, the score is about right. If it is longer than the average, it will get penalization based on its document length and the parameter b. If it is shorter, it will get reward. Depending on the value of b, the degree of penalization or reward varies.

With everything we learned so far such as TF, IDF, document length normalization and so on combined, pivoted length normalization looks like:

$$ f(q,d) = \sum_{w in q \cap d} c(w,q) \frac{ln(1+ln(1+c(w,d)))}{k(1 - b +b \frac{\|d\|}{avdl})} log \frac { M+1 }{df(w) } \;\; b \in [0,1], \;\;, k \in [0, + \inf]  ​$$

c(w,q) indicates the count of words, and for the first fraction, the nominator is TF transformation for sublinear, and the denominator is length normalizer. And lastly, we have IDF.

Another state-of-the-art vector space model is Okapi BM25:

$$ f(q,d) = \sum_{w in q \cap d} c(w,q) \frac{(k+1)c(w,d)}{c(w,d) + k(1 - b +b \frac{\|d\|}{avdl})} log \frac { M+1 }{df(w) } \;\; b \in [0,1], \;\;, k \in [0, + \inf]  $$

It is very similar to the pivoted length normalization formula above, except it uses upper bound transformation instead of double logarithm for sublinear transformation.



## Reference

- CS410 Text Information System by Professor ChengXiang Zhai
- Zhai, C., & Massung, S. (2016). *Text data management and analysis: a practical introduction to information retrieval and text mining*. Morgan & Claypool.