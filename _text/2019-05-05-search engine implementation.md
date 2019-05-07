---
title: "3 - Search Engine Implementation"
date: 2019-05-05
categories: Text
mathjax: true
---



In the last post, we talked about the vector space model for calculating relevance between a document and a query. Here we go a little deeper about the vector space model. Recall that vector space model computes relevances based on similarity. Let's start with the basic vector space model, bit vector representation.

## Tokenizer

We use our vocabulary to define dimensions. That is, each dimension represents each term in vocabulary V, and if the term is present, it is 1, otherwise 0.



## Document Length Normalization

Obviously, documents with longer length have more words, and possibly much more diverse words. That is, simple TF transformation ends up ranking long documents high, while leaving short but relevant documents low. Pivoted length normalization rewards or penalizes documents based on their lengths. It uses like average document length as "pivot":

$$ normalizer = 1 - b +b \frac{|d|}{avdl}, \; where \; b \in [0,1] $$

\|d\| is the length of document and avdl is the average document length.

<p>
	<img src = "../../assets/img/text/2-doclen.PNG">
    <sub>Pivoted Length Normalization. Reprinted from Text data management and analysis: a practical introduction to information retrieval and text mining(p.107), by Zhai, C., & Massung, S. (2016). Morgan & Claypool.</sub>
</p>

As the name suggests, if the length of a document is on average, the score is about right. If it is longer than the average, it will get penalization based on its document length and the parameter b. If it is shorter, it will get reward. Depending on the value of b, the degree of penalization or reward varies.

With everything we learned so far such as TF, IDF, document length normalization and so on combined, pivoted length normalization looks like:

$$ f(q,d) = \sum_{w in q \cap d} c(w,q) \frac{ln(1+ln(1+c(w,d)))}{k(1 - b +b \frac{\|d\|}{avdl})} log \frac { M+1 }{df(w) } \;\; b \in [0,1], \;\;, k \in [0, + \inf]  $$

c(w,q) indicates the count of words, and for the first fraction, the nominator is TF transformation for sublinear, and the denominator is length normalizer. And lastly, we have IDF.

Another state-of-the-art vector space model is Okapi BM25:

$$ f(q,d) = \sum_{w in q \cap d} c(w,q) \frac{(k+1)c(w,d)}{c(w,d) + k(1 - b +b \frac{\|d\|}{avdl})} log \frac { M+1 }{df(w) } \;\; b \in [0,1], \;\;, k \in [0, + \inf]  $$

It is very similar to the pivoted length normalization formula above, except it uses upper bound transformation instead of double logarithm for sublinear transformation.



## Reference

- CS410 Text Information System by Professor ChengXiang Zhai
- Zhai, C., & Massung, S. (2016). *Text data management and analysis: a practical introduction to information retrieval and text mining*. Morgan & Claypool.