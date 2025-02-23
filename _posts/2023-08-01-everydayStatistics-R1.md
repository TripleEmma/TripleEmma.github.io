---
layout: post
date: 2023-08-01
title: Everyday statistics & R (1)
tag: Basic statistics
categories: [Statistics]
---

1. Regarding one (random) variable, most common descriptive statistics are mean, mode, median, range, variance, standard deviation, confidence interval(if we know the distribution of it).
<!--more-->

2. mean, range could be biased;

3. I used to mix up standard deviation (sd) and standard error (SE). standard deviation is calculated from the raw data, indicating the variation of the dataset (gives you an idea of how much individual data points deviate from the mean) ; standard error is a measure of the precision of a sample statistic (e.g., sample mean). It is used to indicate how good the statistic in representing the true population parameter (e.g., population mean). The smaller the better. standard error decrease as sample size increase. $$SE = \sqrt(\frac{sd}{n})$$

4. The Z-distribution is another name for the standard normal distribution with a mean of 0 and a standard deviation of 1.

5. When there are two variable, we usually would like to test the correlation between these two variables. In R we could use function corrr::correlate() to get the correlation between these two variables. The default method to calculate the correlation is Pearson correlation, but when there are outliers in either variable, it is better to use Spearman correlation so that the influence of outliers could be alleviated.
```R
# this code is useful to see the correlations.
library(corrr)
starwars %>%
 select(height, mass, birth_year) %>%
 correlate(method = "spearman") %>%
 shave() %>%
 fashion()
```

6. The R function `rbinom()` allows you to simulate binomial experiments. Its first argument specifies the number of experiments you want to conduct, the second argument represents the number of trials in each experiment, and the third argument is the probability of a specific outcome (e.g., “success”). Each trial is called Bernoulli trial, since there is only two outcomes for each trial with certain possibility. For instance, if you use `rbinom(2, 10, 0.6)`, it means you want to conduct two experiments, flipping a biased coin ten times in each experiment. The coin is biased such that it will show “heads” 60% of the time (probability of success being 0.6). The function will produce two numbers, each representing the number of “heads” observed in each experiment. `binom.test()` does the opposite. Given certain number of “heads” observed in one experiment, and the number trails conducted in the experiment, it is the probability is the the one you are guessing. `binom.test(x, n, p)`; `x` is the number of successes, `n` is the number of trials, and `p` is hypothesised probability of success. \
```R
n <- 10
fair_coin <- rbinom(1, n, 0.5)
biased_coin <- rbinom(1, n, 0.6)
binom.test(fair_coin, n, p = 0.5)
binom.test(biased_coin, n, p = 0.5)
```

7. The R function `t.test()` allows you to do various t-test. If you begin by calculating the means of two independent groups and then compare these means (null hypothesis: $$\overline{x}_1 - \overline{x}_2 = 0$$), you are performing an unpaired t-test. This test is suitable when dealing with two separate and independent groups. On the other hand, if you first compute the differences between paired subjects (e.g., before and after taking some drugs), and then calculate the mean of these differences to compare it to 0 (null hypothesis: $$\mu_{diff} = 0$$), you are conducting a paired t-test. This test is appropriate when working with paired or matched data, such as repeated measurements on the same individuals.
```R
# how exactly to conduct t-test in R
set.seed(2023)
parents <- rnorm(n = 50, mean = 480, sd = 40)
control <- rnorm(n = 50, mean = 500, sd = 40)
dat <- tibble(group = rep(c("parents", "control"), each = 50),
             rt = c(parents, control))
# unpaired t-test
t.test(dat$rt[dat$group == "parents"], dat$rt[dat$group == "control"])
t.test(rt ~ group, dat) # they are in the dataframe
# pair = FALSE (default)
​
# paired t-test
x1 <- rnorm(n = 50, mean = 480, sd = 40)
x2 <- rnorm(n = 50, mean = 500, sd = 40)
t.test(x1, x2, pair = TRUE)
```




