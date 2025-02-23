---
layout: post
date: 2023-08-06
title: Everyday statistics & R (2)
tag: Basic statistics
categories: [Statistics]
---
1. **When to treat a variable as fix variable, when as random variable, when as replicate?**<!--more--> According to Chat-GPT, a fixed variable is one that is deliberately manipulated or controlled by the researcher. It represents specific, predetermined conditions or levels in an experiment. Fixed variables are used when you are interested in comparing the effects of different treatments or conditions on the outcome variable. Fixed variables are often used in controlled experiments to study cause-and-effect relationships. A **random variable** is one that is not controlled or manipulated by the researcher but varies naturally or randomly within the study. Random variables are often characteristics or attributes of the study participants that are observed and measured, not manipulated. Although Chat-GPT said that it could be continuous or categorical variable, I feel like it is often categorical. **When should we treat it as replicate?** In the Biodiversity Exploratories project, there are three regions. In each region, there are 50 plots. We treat the three regions as three independent replicates. I don’t know how to justify why we treat regions as replicates rather than a random variable.

2. Generalized linear models are specifically designed for scenarios where the outcome variable is not continuous. There are two main challenges when the outcome is not continuous: **a)** the presence of boundaries or restrictions on the possible values of the outcome variable; and **b)** the variance of the outcome variable being dependent on its mean, which is often encountered in count data or binary data scenarios. GLMs account for this relationship between the mean and variance by using appropriate distributions from the **exponential family** (e.g., Poisson for counts, Binomial for binary outcomes) and **link functions** (e.g., log or logit).

3. It is intriguing that uncertainty reaches its peak when the probability of an event occurring is 0.5.

4. Odd, natural Odd, logit (log the odd with base of e), logit has several favourable properties. Symmetry, no boundary.

5. **What does ‘error’ and ‘residual’ mean in statistics**? “Error” refers to the difference between the observed data and the true (but usually unknown) values of the dependent variable. It is represented as **ε (epsilon)** in mathematical equations. “Residual” refers to the difference between the observed data and the predicted values obtained from a regression model, indicating how well the model fits the data. Mathematically, the residual for each data point i is calculated as: Residual[i] = ObservedValue[i] – PredictedValue[i].