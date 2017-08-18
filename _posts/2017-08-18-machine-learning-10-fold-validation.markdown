---
layout: post
title:  "K-Fold Cross-validation with Python"
date:   2017-08-18 19:00:13
categories: Machine Learning
---

## Validation

No matter what kind of software we write, we always need to make sure everything is working as expected. Meaning - we have to do some __tests__!

Normally we develop unit or E2E tests, but when we talk about Machine Learning algorithms we need to consider something else - the accuracy.

Let's imagine we have the perfect dataset (already pre-processed and ready to be used). The next step would be to pick suitable ML model.

What we do next? We select some features, make the necessary adjustments and load the data into our algorithm. The magic should happen, right?

<img src="{{ site.baseurl }}/images/magic.gif">

Unfortunately we don't have any guarantee that our algorithm will work fine on new examples. 

The simplest way to test that is to divide our existing training data in two parts - training set  and test set. However, this is proven to not be 
the most efficient approach, as the training data might turn out to be different then the test one. 

In this article we are going to take a look at **K-Fold Cross-validation** technique. [Here](https://www.cs.cmu.edu/~schneide/tut5/node42.html) is presented a good explanation of the method.


## Dataset

**The most used variation of K-Fold validation is when K=10, but for the sake of simplicity we are going to use K=3.**

Let's divide our dataset in three subsets as it is shown in figure 1 below:

<img src="{{ site.baseurl }}/images/dataset1.jpg">

<p style="text-align: center;">Figure 1 - Dataset divided in 3 parts</p>

According to K-Fold technique we need to apply our algorithm on K folds whereas we leave one of the parts for testing and use the others for training.
See how this looks like for K=3 in Figure 2:

<img src="{{ site.baseurl }}/images/dataset2.jpg">

<p style="text-align: center;">Figure 2 - 3 Fold divided dataset</p>

*After that we can calculate the accuracy for every fold and find the average.*

## Python Implementation

We are going to use KFold module from [scikit-learn](http://scikit-learn.org/stable/) library, which is built on top of NumPy and SciPy.

The example shown below implements K-Fold validation on Naive Bayes Classification algorithm.

```python
import nltk # needed for Naive-Bayes
import numpy as np
from sklearn.model_selection import KFold
```

First of all we need to set our K parameter to be 3: `kf = KFold(n_splits=3)`.
After that `kf.split(data)` function will return three folds each one of them containing **two arrays** - one with the indices needed 
for the training set and one with the indices for the test set.  

Once we have that, we use NumPy arrays to extract only those examples from our original dataset with the required indices for every single fold.
*We can do that even without NumPy, but it won't be that efficient or short :)*

See the full example below:

```python
# data is an array with our already pre-processed dataset examples
kf = KFold(n_splits=3)
sum = 0
for train, test in kf.split(data):
    train_data = np.array(data)[train]
    test_data = np.array(data)[test]
    classifier = nltk.NaiveBayesClassifier.train(train_data)
    sum += nltk.classify.accuracy(classifier, test_data)
average = sum/3
```

If you enjoyed this article, I'd love to connect with you :). 

You can find me on [Twitter](https://twitter.com/lili_vs) or [GitHub](https://github.com/lilia-simeonova)