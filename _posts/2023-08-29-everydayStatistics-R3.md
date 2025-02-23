---
layout: post
date: 2023-08-29
title: Everyday statistics & R (3)
tag: Basic statistics
categories: [Statistics]
---


1. **$$\chi^2$$ test**: When you have one categorical variable with different categories or groups, the **chi-square goodness-of-fit test** can be used to determine whether the observed frequency distribution of the categories matches an expected distribution.<!--more--> When you have two categorial variables, it is used to examine the association between two categorical variables. The null hypothesis typically assumes no association or independence between the variables. For example, one categorical variable is gender (male and female), the other categorical variable is getting promotion in three years ('yes' and 'no'). This is a 2x2 contingency table. The $$\chi^2$$ test will help determine if there is a significant difference between males and females in terms of promotion outcomes. Once the $$\chi^2$$ test shows a significant association between gender and promotion, you may want to investigate the strength of this relationship. One way to do this is by calculating the effect size, which is commonly measured using `Cramer's V` for larger contingency tables or Phi for 2x2 tables. A larger effect size indicates a stronger association between the two categorical variables, while a smaller effect size suggests a weaker relationship. Additionally, you can perform a one-sample $$\chi^2$$ test to explore specific hypotheses within each category. For instance, you may want to determine if females are more likely to get promoted compared to males. When conducting the one-sample $$\chi^2$$ test, you should use the same expected values as in the previous two-sample $$\chi^2$$ test.

2. Another application of the chi-square test is in the context of **likelihood ratio (LR) testing**. The LR test is used to compare the fit of two nested models across various types of regression and statistical models. It helps determine if the inclusion of additional predictors in a model significantly improves the model fit compared to a simpler version of the model. The null hypothesis is additional predictors in the complex model do not significantly improve the fit (the simpler model is sufficient).

3. How to interpret "effect size"? According to Chat-GPT, "effect size" is a broad concept that measures the strength of the relationship between variables and how much one variable explains the variability in another. In the context of linear regression analysis, the effect size is often denoted as R-squared. R-squared quantifies the proportion of the variance in the dependent variable (response variable) that can be explained by the independent variable(s). While R-squared in regression captures variance explained, and coefficients capture the direction and magnitude of individual predictors. In the context of $$\chi^2$$ test, 'effect size' quantifies the magnitude of the relationship between variables or the strength of an effect observed in a study. It is particularly useful when dealing with significant results, as statistical significance alone does not provide information about the practical or real-world importance of the findings.

4. How to get "effect size" of a $$\chi^2$$ test, given that it is a significant result?
```R
# Install and load the vcd package if you haven't already
install.packages("vcd")
library(vcd)
​
# Create a contingency table
# Replace the data with your actual data
data <- matrix(c(20, 30, 15, 25), nrow = 2)
​
# Perform a chi-square test
result <- chisq.test(data)
​
# Calculate Cramer's V
cramers_v <- assocstats(data)$cramer
​
# Print Cramer's V
print(cramers_v)
```

5. How to interpret degree of freedom? Degrees of freedom represent the number of values in a sample that are free to vary after certain constraints have been applied. In statistics, degrees of freedom are specifically determined by the number of categories or classes of data involved in an analysis. For example, in a chi-square test for independence, the degrees of freedom are calculated based on the number of rows and columns in the contingency table. In linear regression, degrees of freedom are associated with the number of predictor variables and sample size. Each statistical test has its own method of calculating degrees of freedom, ensuring appropriate interpretations of test results.

6. **t test** is used when you want to compare the means of difference groups (continuous variable) and determine if there is a statistically significant difference between them. If you only have one group, the formula is $$t = \frac{\bar{x} - \mu}{s/\sqrt{n}}$$. $$s$$ is the standard deviation of the sample of interested and $$\mu$$ is a preset value (or the population mean). I once asked Chat-GPT when to use z-test and when to use t-test. The answer depends on whether you know the population standard deviation. If you know the population standard deviation, you can use the z-test. However, when the population standard deviation is unknown, or when the sample size is small (typically less than 30), you use the t-test. The t-distribution accounts for the uncertainty introduced by using the sample standard deviation instead of the population standard deviation, making it more appropriate for smaller samples.

7. When you have two groups and want to compare the means of these groups, the formula to calculate the $$t$$ value (and $$df$$) depends on if the variances of these two populations (from which the groups have sampled from) equal or not. If you assume the variances is the same, then $$t = \frac{\bar{X_1} - \bar{X_2}} {s_p * \sqrt{\frac{1}{N_1} + \frac{1}{N_2}}}$$ ($$s_p$$ is the pooled standard deviation), otherwise $$t=\frac{\bar{X_1} - \bar{X_2}}{\sqrt{\frac{s_1}{n_1} + \frac{s_2}{n_2}}}$$. When you compared two different conditions of each subject in your sample. In such scenario, first calculate the difference between the two conditions of each subject, and then the mean of these differences ($$\bar{D}$$) and standard deviation of these differences ($$sd_D$$). Then use formula $$t = \frac{\bar{D}}{\frac{sd_D}{\sqrt{N}}}$$, where N is the number of the subjects in your sample. The df in paired t-test is $$df = N-1$$.

8. In the context of t-test, effect size is to quantify the standardized difference between group means. A common effect size used for t-test is `Cohen's d`. When you only have one group (you compared to the population mean), the calculation is $$d = \frac{t}{\sqrt{N}}$$; when you have unpaired $t$-test, the calculation is $$d = \frac{2t}{\sqrt{df}}$$; when you have paired $t$-test, the calculation is $$d=\frac{t}{\sqrt{N}}$$.

9. Another application of *t*-distribution is that in linear regression. The distribution of the estimated regression coefficients follows a *t*-distribution under certain assumptions. This is applicable when the residuals of the model are normally distributed, and the sample size is sufficiently large.

10. In logistic regression, the goal is to estimate the regression coefficients ($$β_0$$, $$β_1$$, $$β_2$$, ..., $$β_k$$) that best fit the observed data. The estimation of the logistic regression coefficients is typically performed using maximum likelihood estimation (MLE). Under certain regularity conditions, the MLE estimates of the coefficients asymptotically follow a multivariate normal distribution. This means that for sufficiently large sample sizes, the estimated coefficients are approximately normally distributed. As the sample size increases, the normality of the coefficient estimates becomes more pronounced, and the central limit theorem ensures that the distribution of the coefficients approaches a normal distribution.