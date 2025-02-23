---
layout: post
date: 2023-02-12
title: Everyday statistics — Central Limit Theorem (2)
tag: Basic statistics
categories: [Statistics]
---

An extension of the Central Limit Theorem (CLT) states that if you take sufficiently large random samples from any population (where each sample contains a sufficiently large number of observations, typically n≥30), then the sampling distribution of the sample sum will tend to be approximately normal, regardless of the population’s original distribution.
<!--more-->


Caution: The standard deviation of the sampling distribution (often called the standard error of the sum) is calculated as 
$$\sigma * \sqrt{n}$$ rather than $$\sigma/\sqrt{n}$$.

A practical case is the random walk process. In a random walk, you take a series of steps, where each step is independent of the others. Each step can be thought of as a random variable, and if the steps are of fixed length and have the same probability distribution (e.g., moving left or right with equal probability), the position at any given point in time is simply the sum of those independent random variables.

As you take more steps in a random walk, the overall position is the cumulative sum of the individual random variables. According to the CLT, as the number of steps (or random variables) increases over time, the distribution of the sum of these variables tends to approach a normal distribution.

***This is the basis of the Brownian motion model used in phylogenetic comparative methods, where trait evolution is modeled as a random walk over a phylogeny, assuming small independent changes accumulate over time, leading to a normal distribution of trait values among species.***