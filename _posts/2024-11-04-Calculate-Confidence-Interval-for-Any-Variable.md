---
layout: post
date: 2024-11-04
tag: Applied statistics
categories: [Statistics]
---

I came across a question like the following: Given 100 patients, 36 died within 10 days. What is the 95% confidence interval (CI) of the mortality rate?
<!--more-->

The general formula for a 95% confidence interval (CI) is:

$CI= point estimate ± (critical value * standard error (SE))$

#### Components of the Formula:
- **Point Estimate**: The point estimate could be a sample mean, sample proportion, or other summary statistics. In this case, it is the sample proportion, which is 36/100 = 0.36:

- **Critical Value**: The critical value depends on the desired confidence level and the type of distribution:
  For a 95% confidence level and a normal distribution (large sample sizes or known population variance), the critical value (z) is approximately 1.96.
  For a -distribution (small sample sizes or unknown population variance), the critical value depends on the degrees of freedom (df).

- **Standard Error (SE)**: The standard error quantifies the variability of the sample estimate. It is calculated differently depending on the type of variable:
  For a mean:where is the sample standard deviation and is the sample size.
  For a proportion:where is the sample proportion.
  For the given example: $SE = n * p * (1−p) = 0.36 * (1−0.36)​$

- **Confidence Interval**: Substituting the values into the formula:
  $0.36 ± 1.96 * SE$

#### Assumptions for Using the CI Formula:
- The sample data are random and representative of the population.
- The variable or statistic of interest approximately follows a normal distribution (or the sample size is large enough for the Central Limit Theorem to hold).

This formula is a universal starting point, but the specifics (e.g., the calculation of SE) depend on the type of variable and context of the analysis.
