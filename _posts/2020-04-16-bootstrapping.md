---
title:      "What I've learned about Bootstrapping (statistics)"
date:       2020-04-16 14:22:00 +0700
categories: statistics
mathjax:    true
toc:        true
---
I've recently been going through some new things in machine learning, and came across a seemingly important but small topic called _bootstrapping_. 
I looked it up and aside from a few places (and Wikipedia, of course), there weren't many explanations for it, so I thought I could try to explain it as well.

Bootstrapping is based loosely on the __law of large numbers__, which is a theorem that tells us that if an experiment is performed a large number of times, 
the _average_ of the results should be _approximately close_ to the true average. An example (shown in the following code) is rolling a dice multiple times. 
Taking the average of your dice rolls, as you roll the dice over and over, your average will get closer and closer to the average of 3.5.

<script src="https://gist.github.com/keptsecret/caec3b7d1266fda168fd4ef26d6c43dd.js"></script>

## That brings us to bootstrapping. 

Bootstrapping is a resampling method, where a small number of samples is drawn _with replacement_ from a larger sample $$P$$. For instance, 
from a sample of `[3, 6, 10, 13, 14, 18, 33, 37, 46]`, we can draw `[10, 18, 33]`, or `[6, 13, 37]`, or even `[14, 18, 18]`. Generally, a large number of these samples 
is drawn, say $$n$$ times. The statistics for each of these samples can then be calculated, and then an approximation of the _mean_ or _variance_ can be found. 
The steps can be broken down into:

1. Draw a random sample $$n$$ times from the dataset $$P$$
2. Calculate the statistics (called a __bootstrap statistic__) for each of the $$n$$ samples
3. Calculate the mean and variance for the bootstrap samples to get the approximate mean and variance for $$P$$

### "Why resample?" would be one of the first questions I had after reading through the explanations.

As it is, an ideal sampling situation would be from a very big population and so there would be no resampling needed. However, if the only a small sample is available, 
it could be treated as a mini-population, which we can then draw repeated smaller samples from with replacement. This makes bootstrapped samples good representations 
of the population. This can be shown in the code below:

<script src="https://gist.github.com/keptsecret/06b89b878efc5f0c64662f855ab51e09.js"></script>

## Uses

As shown, bootstrapping can be used to obtain data from a small or insufficient sample. 

The machine learning case I came across bootstrapping was in reading about ensemble learning algorithms and random forests. In a type of random forest, a number of 
decision trees are created and trained on a smaller sample of the dataset created _with replacement_ - this is called __bagging__ (bootstrap aggregating). 
The alternative to this, done without replacement, is called __pasting__. I was surprised by how effective bagging actually was, especially on a small dataset.

More information on bootstrapping:
* The [Wikipedia](https://en.wikipedia.org/wiki/Bootstrapping_(statistics)) article
* A [Towards Data Science](https://towardsdatascience.com/an-introduction-to-the-bootstrap-method-58bcb51b4d60) post I read, much more technical than I can write
* [An explanation paper from an MIT class](https://ocw.mit.edu/courses/mathematics/18-05-introduction-to-probability-and-statistics-spring-2014/readings/MIT18_05S14_Reading24.pdf) that is also very technical