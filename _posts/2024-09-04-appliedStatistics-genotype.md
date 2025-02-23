---
layout: post
date: 2024-09-04
title: Inferring Maternal Genotype from Offspring
tag: Applied statistics
categories: [Statistics]
---
A colleague approached me with an intriguing problem: she suspected the metadata in her dataset might be incorrect. Specifically, the individual labeled as the “mother” may not actually be the biological mother. She asked if it would be possible to infer the mother’s genotype based on the genotypes of the offspring and the father.
<!--more-->

Initially, I had a gut feeling that this could be addressed using a maximum likelihood approach. Here’s how I tackled the problem:

<p></p>
***Let’s assume the father’s genotype is AB.***

**1. Define the Model and Probabilities** \
We hypothesize three potential maternal genotypes: **AA**, **AB**, and **BB**. Based on Mendelian inheritance, the probabilities of observing specific offspring genotypes for each maternal genotype are:
- When the mother’s Genotype is **AA**: the probabilities of observing **AA** offspring is 0.5, **AB** offspring is 0.5, **BB** offspring is 0;
- When the mother’s Genotype is **AB**: the probabilities of observing **AA** offspring is 0.25, **AB** offspring 0.5, **BB** offspring is 0.25;
- When the mother’s Genotype is  **BB**: the probabilities of observing **AA** offspring is 0, **AB** offspring 0.5, **BB** offspring is 0.5; 

**2. Write the Likelihood Function** \
The likelihood function represents the probability of observing the given data (the offspring genotypes) given a specific maternal genotype.

$$L(Genotyype) = P(AA)^{n_{AA}} * P(AB)^{n_{AB}} * P(BB)^{n_{BB}}$$

Here P(AA), P(AB), P(AB) are the probabilities of observing each offspring genotype given the maternal genotype, while $$n_{AA}, n_{AB}, n_{BB}$$ are the actual number of offspring with the genotype observed in the data.



For example:\
If the maternal genotype is AA, the likelihood function becomes:

$$L(AA) = 0.5^{n_{AA}} * 0.5^{n_{AB}} * 0^{n_{BB}}$$

**3. Calculate the Likelihood for Each Maternal Genotype** \
Using the observed offspring counts ($$n_{AA}, n_{AB}, n_{BB}$$), calculate the likelihood for each potential maternal genotype (AA, AB, BB) by plugging the probabilities into the likelihood function.


**4. Identify the Most Likely Maternal Genotype** \
The maternal genotype with the highest likelihood is the one most consistent with the observed offspring genotypes.

This methodology provides a systematic way to test hypotheses about maternal genotypes and helps verify dataset integrity. It’s a fascinating example of how statistical tools can help uncover hidden patterns in biological data.


#### Example Calculation:
**Let’s again assume the father’s genotype is AB, and suppose you have the following observed counts:**
    $$n_{AA} = 30, n_{AB} = 45, n_{BB} = 15$$

**Then:** \
 The likelihood of the mother's genotype to be:\
 **AA**: $$0.5^{30} * 0.5^{45} * 0^{15}$$ (because of the zero probability for BB offspring); \
 **AB**: $$0.25^{30} * 0.5^{45} * 0.25^{15}$$; \
 **BB**: $$0^{30} * 0.5^{45} * 0.5^{15}$$ (because of the zero probability for AA offspring);

The maternal genotype with the highest likelihood value is the one that is most consistent with the observed data. In the example, the genotype “AB” would have the highest likelihood since it doesn’t result in a likelihood of 0, while the other genotypes do. Based on the maximum likelihood, you would conclude that the mother’s genotype is most likely AB.



