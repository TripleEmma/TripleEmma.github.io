---
layout: post
date: 2023-01-30
title: Everyday statistics — Central Limit Theorem (1)
tag: Basic statistics
categories: [Statistics]
---
It is said that Central Limit Theorem (CLT) is one of the most fundamental concepts in statistics.
<!--more-->

It states that, if you take sufficiently large random samples from any population (where each sample contains a sufficiently large number of observations, typically n ≥ 30), then the sampling distribution of the sample mean will tend to be approximately normal, regardless of the population’s original distribution.

As the sample size (observations in a sample) increases, the sampling distribution of the sample mean becomes increasingly closer to a normal distribution with the same mean as the population and a standard deviation equal to the population’s standard deviation divided by the square root of the sample size (σ/√n). This makes the CLT a powerful tool for making inferences about population parameters, even when the population itself is not normally distributed.

Since we know the distribution, we can
- calculate the probability and make predictions about sample means using normal probability techniques;
- perform hypothesis tests and construct confidence intervals, which rely on normality assumptions;
- apply regression models, ANOVA, and other parametric tests that assume normality of errors or residuals;

#### Simulation of Central Limit Theorem:
```python
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns

# create a right-skewed population
data = np.random.exponential(scale = 2, size = 10000)

# take serveral random samples and compute their means
sample_sizes = [5, 30, 100]  # different sample sizes
fig, axes = plt.subplots(1, 3, figsize = (15, 5))

for i, n in enumerate(sample_sizes):
    sample_means = [np.mean(np.random.choice(data, size = n)) for _ in range(1000)]
    sns.histplot(sample_means, bins = 30, kde = True, ax=axes[i])
    axes[i].set_title(f'Sample Size = {n}')

plt.tight_layout()
plt.show()
```