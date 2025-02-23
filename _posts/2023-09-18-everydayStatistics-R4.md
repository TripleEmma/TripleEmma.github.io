---
layout: post
date: 2023-09-18
title: Everyday statistics & R (4)
tag: Basic statistics
categories: [Statistics]
---

1. Variance ($$\text{var}$$) is a parameter that describes the variability of a single variable. The formula to calculate the sample variance is given by $$var(x) = \frac{\sum(x_i - \bar{x})^2}{n-1}$$, where $$\bar{x}$$ is the sample mean and n is the number of data points in the sample.<!--more--> When you have two variables, you might be interested in understanding how they vary together. For this, you can use covariance. The formula to calculate the sample covariance between two variables x and y is given as $$cov(x, y) = \frac{\sum(x_i - \bar{x})(y_i - \bar{y})}{n-1}$$. Covariance can indicate the direction of the relationship between two variables. A positive covariance indicates that the variables tend to increase or decrease together, while a negative covariance indicates that one variable tends to increase as the other decreases. Covariance values are not standardized and depend on the scale of the variables involved. This makes it difficult to compare covariances across different datasets or variable units. Covariance alone does not provide clear insight into the strength or direction of the relationship between two variables. Correlation provides a standardized measure of the linear relationship between two variables. It is a unitless measure. The magnitude of the correlation coefficient reflects the strength of the linear association, and its sign (+ or -) indicates the direction of the relationship.

2. **Pearson correlation** and **Spearman correlation** are two different methods used to measure the relationship between two variables. `Pearson's r`, is used to measure the strength and direction of a linear relationship between two continuous variables. It assumes that the relationship between the variables is **linear and follows a straight-line pattern (monotonic relationship)**. Pearson correlation is sensitive to outliers, and if the data violates the assumptions of linearity, it may not provide an accurate measure of association. The formula to calculate Pearson correlation $r$ is: $$r(x,y)=\frac{cov(x,y)}{s_x⋅s_y}$$, where $$s_x$$ and $$s_y$$ are the sample standard deviations of $$x$$ and $$y$$ respectively.

3. **Spearman correlation** otherwise known as the Spearman rank correlation coefficient measures the relationship between two variables when that relationship is monotonic or ***not*** monotonic. It can be used for relationships that are linear or non-linear. The key difference between the Spearman Correlation and the Pearson Correlation is that the Spearman Correlation is based on the rank order of data - i.e. the raw data is converted to ordinal ranks. If all of the ranks within each variable are unique, the formula is $$\rho = \frac{6*\sum{d_i^2}}{n(n^2-1)}$$, where `d​` is the difference between the ranks of the two variables. Spearman correlation is more robust to outliers and can handle non-linear relationships better than Pearson correlation. It is especially useful when dealing with ordinal or non-normally distributed data, where the actual numerical values may not carry as much meaningful information as the order of the ranks.

4. The effect size of Pearson correlation and Spearman correlation is the absolute value of $$r$$ and $$\rho$$.

5. For Pearson correlation, with sufficiently large sample sizes, the sampling distribution of the correlation coefficient follows a $t$-distribution. Therefore, the $$p$$-value of $$r$$ could be calculated by $$t$$-test. However, for Spearman correlation, there is no specific known parametric distribution, and $$p$$-values are typically obtained through non-parametric methods like permutation tests.

6. Unstandardized coefficient and standardized coefficient are both terms used in the context of linear regression and regression analysis. Standardized coefficients are obtained by scaling the independent variables to have a mean of 0 and a standard deviation of 1 before fitting the regression model. They (standardized coefficients) express the effect of each predictor in standard deviation units. The advantage of standardized coefficients is that they allow for direct comparison of the importance of different predictors, regardless of their original units. This makes it easier to assess the relative impact of each predictor on the outcome.

7. Both **t-test** and **ANOVA** are statistical methods used to compare means between two or more groups. The **t-test** assumes that the data are normally distributed, and it tests whether the means of the two groups are significantly different based on the variability within each group. **ANOVA** is an extension of the t-test, which compares the means of three or more groups simultaneously to determine if there are any significant differences among the group means.

8. Sum of squares within each group (SSW) = $$\sum_{i=1}^{k}(x_{ij}-\bar{x_i})$$; degrees of freedom within groups (df_within) = $$\sum_{i=1}^{k}(n_i-1)$$. Variance within groups (MSW) = $$\frac{SSW}{df_{within}}$$. Sum of squares between groups (SSB) = $$\sum_{i=1}^k (n_i*(X_i-\bar{X}))$$; degrees of freedom between groups (df_between) = k-1. Variance between groups (MSB) = $\frac{SSB}{df_{between}}$. The F-statistic is then calculated as the ratio of MSB to MSW: F = $$\frac{MSW}{MSB}$$. If the `F-statistic` is large enough and the resulting p-value is smaller than the chosen significance level (e.g., 0.05), you reject the null hypothesis and conclude that there is a significant difference in means between at least two of the groups.

9. Effect size of One-Way ANOVA: $$\eta^2 = \frac{SSB}{SSB+SSW}$$

```R
# Assuming you have a data frame 'df' with the dependent variable 'y' and group variable 'group'
anova_result <- aov(y ~ group, data = df)

# Eta-squared (η^2)
eta_squared <- etaSquared(anova_result)

# Cohen's f
cohen_f <- sqrt(eta_squared / df.residual(anova_result))
```