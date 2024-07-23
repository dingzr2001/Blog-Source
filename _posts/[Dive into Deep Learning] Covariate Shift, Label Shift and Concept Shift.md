---
title: Covariate Shift, Label Shift and Concept Shift
tags: [machine learning, distribution shift]
categories: [machine learning, Dive into Deep Learning-Notes]
date: [2023-01-29 16:43:00]
index_img: /img/distribution_shift.jpg
---



# [Dive into Deep Learning] Covariate Shift, Label Shift and Concept Shift

When studying distribution shift, these concepts kept confusing me for a long time. After searching they finally become clearer.

## Covariate Shift

First, what exactly is a **covariate** in machine learning? Well, after searching I found that covariates in statistics corresponds with features in machine learning, which means that covariate shift is actually the shift of features. In many cases, the features of test datasets might differ from those of the training data sets, yet the labels remain the same. For example, one model is trained with a set of real dog pictures, but it might be required to distinguish a comic dog like Snoopy. The model has never seen Snoopy-like cartoon dogs before, and such dogs have different distribution of features and labels with real dogs, but they share the same label: dog. Such a shift of distribution is called **covariate shift**.

## Label Shift

Label shift is exactly the opposite of covariate shift. Here is what I found at __https://towardsdatascience.com__, written(or quoted) by Dr. Matthew Stewart:

>Prior probability shift appears only in Y->X problems, and is defined as the case where P~tra~(x|y) = P~tst~(x|y) and P~tra~(y) ≠ P~tst~(y).

So therefore the example of diseases and symptoms makes sense. We want to predict disease by symptoms, so 'symptoms' is X, and 'diseases' is Y. Apparently diseases cause symptoms, so this is a typical Y->X case. The distribution of diseases is not always the same, since diseases occur at different rates in different seasons, so the distribution of Y changes. This is a label shift.

## Concept Shift

Concept shift is different, it is the mere change of P(y|x). Let's assume there was once a type of medicine, but it was later redefined as a type of beverage by FDA, though its ingredients remains the same. So a model developed at the earlier period trying to classify medicine and beverage by the ingredients may not work well later.



That is my preliminary understanding of distribution shifts in this book. There's still some ambiguity though, and I'll keep working on that.

