---

title: "Metric for Binary Classification"
date: 2020-05-11
categories: Machine
mathjax: true
tags = [machine-learning, metric, binary]
---



Sensitivity = $$ \frac{\text{True Positive}} {\text{True Positive + False Negative}} $$ = Recall

F1 cares only for the misclassification of positive labels. So, F1 makes more sense when you have a small positive class over ROC-AUC.

If you care for a class which is smaller in number independent of the fact whether it is positive or negative, go for ROC-AUC score.

Micro average method sums up the individual values of the system for different sets and then apply them to get the statistics

Macro average method take the average of precision or recall of the system on different sets.

Micro-average is preferable if there is a class imbalance problem.