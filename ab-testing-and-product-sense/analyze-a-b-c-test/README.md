# Analyze A/B/C test

## The problem with A/B/C tests <a href="#d787" id="d787"></a>

There are three new designs proposed, design A, design B, and design C. They are ready to challenge the status-quo — the existing design. We, therefore, have the following four segments in our A/B/C test:

1. Existing design
2. Design A
3. Design B
4. Design C

We roll the experiment by exposing each of the targeted users with one of the above designs at random and record his/her action accordingly — whether or not he/she redeems the voucher. Suppose we have the following results (_not real data —_ the numbers cited are fictitious). Note that we will use an ⍺ of 5%.

![](https://miro.medium.com/max/1400/0\*SpZfAtv\_9JvURB-1)Table 1 — Experiment results

The first thing to do on analyzing AB tests is to conduct a Chi-square test. Yet, when there are more than two segments in the test, the **Chi-square test only takes us to the aggregate level conclusion. If the test gets a significant result, it only informs us that at least one segment performs significantly different from the others, but we don’t know which segment it is.** In light of this, it seems natural to experiment individually on each pair of the experimental groups. But, there is a hidden devil in this — that should be taken care of!

Consider the previous example; we have the following 6 pairs to test individually:

* Control vs. Design A
* Control vs. Design B
* Control vs. Design C
* Design A vs. Design B
* Design A vs. Design C

By assuming independence, this latter event's probability is (1-⍺)⁶ = (1–0.05)⁶ = 0.735. This means our original desired probability is one minus this value or 1–0.735 = 0.265; a significantly greater value than the original ⍺ = 0.05 that we set!

By the way, the mentioned scenario of testing all the combination pairs of experiment groups, armed with a certain P-value correction strategy, is also called the posthoc test.

## **P-Value correction for** proportion metric <a href="#e4df" id="e4df"></a>

There are many approaches to do P-value correction. In this article, we will only show two of them.

**First approach: controlling Family Wise Error Rate (FWER)**

We control the probability of at least having 1 false positive out of all the paired-tests we conducted in this approach. Put it precisely,

![](https://miro.medium.com/max/1400/0\*qjkpR\_2cTcTfHL0s)

One method to achieve this is called Bonferroni correction, whose derivation is given below.

![](https://miro.medium.com/max/792/0\*j9sLpJLnyJIGxYdb)

Where _n_ is the number of comparisons (or pairs). Consequently,

![](https://miro.medium.com/max/628/0\*Ip1a1JLpX32qfq8-)

Note that ⍺\* is _no_ longer the same as our ⍺ in the initial test, i.e., it is _not_ the chance of having a false positive at the individual comparison. Instead, ⍺\* is the probability of **having** **at least one false positive among all comparisons conducted.**

For example, ⍺\* = 5% means out of all comparisons we made, there is a 5% chance to have at least one false-positive conclusion.

Despite being intuitive and simple, Bonferroni Correction is criticized for being too conservative. It is because the adjusted P-values explode so fast. To get an idea, 10 comparisons would lead to 10x greater P-values. This makes rejecting the NULL hypothesis so hard, i.e., it needs a very strong signal (evidence) to do so.

Moreover, on the flip side, this would increase the Type II error rate; There would be more comparisons we conclude as “non-significant,” although the ground truth is actually significant.

**Second approach: controlling False Discovery Rate (FDR)**

In this approach, we control the expected value of the proportion of false positives divided by all positives we get from all comparisons, more formally,

![](https://miro.medium.com/max/1400/0\*a5HxI0uZ26rfuSjE)

One method to achieve this is using the Benjamini-Hochberg correction. **** This method’s step-by-step is as follows:

* Get the original (raw) P-values of each segment pair
* Sort ascending the raw P-values
* For the raw P-value at rank k

![](https://miro.medium.com/max/788/0\*QKcqSQW3Pva8OwZr)

Where _n_ is the same as before: the total number of comparisons we make.

![Figure 1 - Step-by-step performing Benjamini-Hochberg correction on P-values](https://miro.medium.com/max/1282/1\*HTcWkSnrlTUGFHnaTmVJ2g.png)

Again, note that ⍺\* is _no_ longer the same as our ⍺ in the initial test (also, it is different from the one in the FWER method). The interpretation of ⍺\* is now the expected proportion of the number of false positives divided by all positives.

For example, ⍺\* = 5% means, suppose from _n_ comparisons, we have 100 positive conclusions, then it is expected to have 5 of them being false positives.

### Analyzing the experiment <a href="#d6a2" id="d6a2"></a>

So we got the weapon to use, let us carry out the analysis for the experiment!

![](https://miro.medium.com/max/1400/0\*SpZfAtv\_9JvURB-1)Table 1 — Experiment results (also put here for convenience)

**First step: Do a Chi-square test on the whole table.**&#x20;

**Second step: Perform a Z-proportion test for each pair of segments.**&#x20;

**Third step: Compute adjusted P-values & gather the conclusion.**&#x20;

## Analyze an A/B/C test with Mean-Based Metric <a href="#2cd0" id="2cd0"></a>

Consider an experiment about different promotion strategies. There are three treatments (experiment groups), say:

1. Control: the existing promo strategy
2. Variant1: the first challenger promo strategy
3. Variant2: the second challenger promo strategy

The metric of interest is the **average transaction amount**. To make the computation tractable (re: to make this article a good tutorial blog), let’s assume there are only 10 transactions gathered from each of the experiment groups, with the following details. We are interested to know **which promo strategy is the best performant** among the three being tested.

![Figure 1: Experiment data](https://miro.medium.com/max/1400/1\*JVIYVC0fRroeed7YGYIKtg.png)

### One-Way ANOVA <a href="#2726" id="2726"></a>

The first step is to test whether there is at least one group with a different mean. More formally, we want to test the following competing hypotheses.

**H0**: All transaction amount means (averages) are the same

**H1**: There is at least one transaction amount mean that differs from the rest

We can use ANOVA to accomplish this. In a nutshell, ANOVA compares the means (averages) of different groups by determining how likely it is that they are coming from the same distribution (i.e., the means are actually the same) by analyzing their variances (hence the name _analysis of variance_ — ANOVA).

As a refresher, recall that variance is the measure of how far apart data points are from their mean.

The intuition goes like this. First, we partition the variance from the overall data points (treating all data points as a single large group) into two components: **variance between data points within the same group** and **variance between different groups’ means** (treating each group’s mean as an imaginary aggregated data point).

![Figure 2: Different cases of total variance partition illustration](https://miro.medium.com/max/1400/1\*-1m5CxJcWdQzcns1qkoi6Q.png)

If the first component of variance is smaller than the second, we can conclude that the means of the groups differ because the distribution of data points from different groups is well separated (Case 1 in Figure 2), and we can be confident that they come from different populations — with different averages.

And vice versa; if most of the total variation is contributed from within-groups variation, we can say that the groups’ means are perhaps not different in the first place (Case 2 in Figure 2).

Going technical, we do this by calculating the F statistic, which is the ratio between two sums of squared error (SSE), normalized by their degree of freedom: between-group SSE and within-group SSE.

This value is then compared with an appropriate F reference value. If the F statistic is greater than the F reference value, then we conclude that we can reject H0. That is, we are confident that there is at least 1 group whose mean is significantly different from the rest.

Below are the concrete steps of our working example above.

1. **Find the average of all data points**. Note that we have 30 data. The value is IDR 84,250.
2. **Calculate the total sum of squared errors (SSE) from all data points**. For each data point, we subtract by IDR 84,250, then we square it. Take the sum of those 30 such terms. The value is 2,875,395,000.
3. **Calculate the within-group SSE**. Note that we have 3 groups, each has 10 data points. For each data point in a given group, we subtract by the mean of the given group, then we square it. Take the sum of those 10 such terms. Do the same for each group, and sum everything. The value is 2,142,649,000. (See Figure 3 for a visual calculation details on step 1–3)
4. **Derive the between-group SSE**, it is the delta between total SSE and within-group SSE. The value is 2,875,395,000–2,142,649,000 = 732,746,000.
5. **Get the degree of freedom (DF)** value for between-group SSE and within-group SSE. Let N and k be the total number of data points (30) and the number of groups (3), respectively. Then between-group SSE’s DF = k-1 = 2, and within-group SSE’s DF = N-k = 27. (Great explanation on what are degrees of freedom is available [here](https://www.omnicalculator.com/statistics/degrees-of-freedom))
6. **Normalize (divide) each SSE with its DF** to get between-group MSE = 732,746,000/2=366,373,000 and within-group MSE = 2,142,649,000/27=79,357,370.
7. Finally, **compute the F statistic**. It is the ratio between: between-group MSE and within-group MSE. F statistic = 366,373,000/79,357,370 = 4.61.
8. **Get the appropriate F reference value**. F reference value has three parameters: alpha (significant level, equal to 0.05 as usual), between-group MSE’s DF, and within-group MSE’s DF. Thus in our case, it is F(0.05,2,27). We can utilize any online calculator to get the value (I use [this site](https://www.danielsoper.com/statcalc/calculator.aspx?id=4)). The value is 3.35.
9. Lastly, **compare the F statistic (4.61) with the F reference value (3.35)**. Because the F statistic is larger than the F reference, we reject the null hypotheses (H0). **Therefore we conclude that there is at least one transaction amount mean that differs from the rest.** (see Figure 4 for the classical tabulation of step 4–9)

![](https://miro.medium.com/max/1400/1\*sFV9Atum871wAPV9dHRKNA.png)Figure 3: Step 1–3 computation details in spreadsheet![](https://miro.medium.com/max/1400/1\*yRsphiO\_qSZMYD5ZypulXQ.png)Figure 4: The classic ANOVA tabulation (Step 4–9)

## Tukey HSD Post-hoc Test <a href="#96a0" id="96a0"></a>

Although we previously rejected the null hypotheses in ANOVA, our analysis is not yet complete. The problem is that ANOVA does not reveal which group is the clear winner. So far, the rejection indicates that at least one group has a significantly different mean than the others. Which of these is it? We don’t know.

A post-hoc test is required to determine the clear winner. There are numerous tests available, but we will use the most commonly used one, known as the Tukey Honestly Significant Difference (HSD) test.

A Tukey HSD test compares each pair of group means and calculates the corresponding q statistic. What exactly is the q statistic? It is essentially a variant of the t statistic (as used in the infamous t-test) that accounts for Type I error inflation due to multiple testing. Read _“The problem with A/B/C tests”_ section on [my previous tutorial](https://medium.com/bukalapak-data/how-to-analyze-an-a-b-c-testing-658e18b5596d) to understand about the issue on multiple testing. Therefore, **we can think of Tukey HSD test as a more conservative t-test** (i.e. it requires greater evidence for rejecting the null hypothesis).

See Figure 5 below for the comparison of critical values from t-distribution and multiple versions of q distributions (depending on the number of means being considered — to test in total). We can clearly see that the greater the number of means, the higher the critical value — and thus the more conservative test we have.

![](https://miro.medium.com/max/1204/1\*cTmERl15-MfNtmUa7\_hTIQ.png)Figure 5: Critical value comparison between t and various q distributions

Please find the q statistic formula below before proceeding with the working steps of conducting the Tukey HSD test. It is important to note that _n_ denotes the number of data points in a group (in our case, n = 10).

![](https://miro.medium.com/max/1400/1\*3hCKljhLxtbtDIZBfJ4jHg.png)Figure 6: q statistic between mean A and mean B formula

The practical steps are as follows. For each pair of means of experiment groups, we do:

1. Calculate the delta between two groups’ means.
2. Derive the corresponding q statistic value using the formula in Figure 6
3. Get the appropriate q reference value. The good news is, the parameters are the same as in ANOVA. I.e., we are looking for q(0.05,2,27). Using an [online q value calculator](https://www.socscistatistics.com/pvalues/qcalculator.aspx), we get the value of 2.90.
4. Compare the q statistic with the q reference value. Same as before, we say the means pair are significantly different IF its q statistic is greater than the q reference value.

See below for the worked-out tabulation.

![](https://miro.medium.com/max/1400/1\*5GyMh4XNkrHHsMlrKi4oIQ.png)Figure 7: Tukey HSD test results

From the results in Figure 7, we learn that:

1. Control and Variant1 have the same transaction amount performance.
2. Control and Variant2 have different transaction amount performances.
3. Variant1 and Variant2 have different transaction amount performances.

**Finally, because Variant2 has the highest mean transaction amount (91,040), we can conclude that it is the best promotion strategy.**

## Reference

{% embed url="https://medium.com/bukalapak-data/how-to-analyze-an-a-b-c-test-with-mean-based-metric-1971bf13a9d7" %}

{% embed url="https://medium.com/bukalapak-data/how-to-analyze-an-a-b-c-testing-658e18b5596d" %}
