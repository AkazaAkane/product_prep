# Statistical Power and Power Analysis

### Statistical Hypothesis Testing

A statistical hypothesis test makes an assumption about the outcome, called the null hypothesis.

The test is often interpreted using a p-value, which is the probability of observing the result given that the null hypothesis is true.

* **p-value (p)**: Probability of obtaining a result equal to or more extreme than was observed in the data.

The p-value is interested in the context of the chosen significance level. A result of a significance test is claimed to be “_statistically significant_” if the p-value is less than the significance level. This means that the null hypothesis (that there is no result) is rejected.

* **p <= alpha**: reject H0, different distribution.
* **p > alpha**: fail to reject H0, same distribution.

Where:

* **Significance level (alpha)**: Boundary for specifying a statistically significant finding when interpreting the p-value.

We can see that the p-value is just a probability and that in actuality the result may be different. The test could be wrong. Given the p-value, we could make an error in our interpretation.

There are two types of errors; they are:

* **Type I Error**. Reject the null hypothesis when there is in fact no significant effect (false positive). The p-value is optimistically small.
* **Type II Error**. Not reject the null hypothesis when there is a significant effect (false negative). The p-value is pessimistically large.

### What Is Statistical Power?

Statistical power, or the power of a hypothesis test is the probability that the test correctly rejects the null hypothesis. That is, the probability of a true positive result. It is only useful when the null hypothesis is rejected.

> … statistical power is the probability that a test will correctly reject a false null hypothesis. Statistical power has relevance only when the null is false.

The higher the statistical power for a given experiment, the lower the probability of making a Type II (false negative) error. That is the higher the probability of detecting an effect when there is an effect. In fact, the power is precisely the inverse of the probability of a Type II error.

| Power = 1 - Type II Error Pr(True Positive) = 1 - Pr(False Negative) |
| -------------------------------------------------------------------- |

More intuitively, the statistical power can be thought of as the probability of accepting an alternative hypothesis, when the alternative hypothesis is true.

When interpreting statistical power, we seek experiential setups that have high statistical power.

* **Low Statistical Power**: Large risk of committing Type II errors, e.g. a false negative.
* **High Statistical Power**: Small risk of committing Type II errors.

It is common to design experiments with a statistical power of 80% or better, e.g. 0.80. This means a 20% probability of encountering a Type II area.&#x20;

### Power Analysis

Statistical power is one piece in a puzzle that has four related parts; they are:

* **Effect Size**. The quantified magnitude of a result present in the population. [Effect size](https://machinelearningmastery.com/effect-size-measures-in-python/) is calculated using a specific statistical measure, such as Pearson’s correlation coefficient for the relationship between variables or Cohen’s d for the difference between groups.
* **Sample Size**. The number of observations in the sample.
* **Significance**. The significance level used in the statistical test, e.g. alpha. Often set to 5% or 0.05.
* **Statistical Power**. The probability of accepting the alternative hypothesis if it is true.

All four variables are related. For example, a larger sample size can make an effect easier to detect, and the statistical power can be increased in a test by increasing the significance level.

A power analysis involves estimating one of these four parameters given values for three other parameters. For example, the statistical power can be estimated given an effect size, sample size and significance level. Alternately, the sample size can be estimated given different desired levels of significance.

> Power analysis answers questions like “how much statistical power does my study have?” and “how big a sample size do I need?”.

Perhaps the most common use of a power analysis is in the estimation of the minimum sample size required for an experiment.

### Student’s t Test Power Analysis

We will look at the Student’s t test, which is a statistical hypothesis test for comparing the means from two samples of Gaussian variables. The assumption, or null hypothesis, of the test is that the sample populations have the same mean, e.g. that there is no difference between the samples or that the samples are drawn from the same underlying population.

The test will calculate a p-value that can be interpreted as to whether the samples are the same (fail to reject the null hypothesis), or there is a statistically significant difference between the samples (reject the null hypothesis). A common significance level for interpreting the p-value is 5% or 0.05.

* **Significance level (alpha)**: 5% or 0.05.

The size of the effect of comparing two groups can be quantified with an effect size measure. A common measure for comparing the difference in the mean from two groups is the Cohen’s d measure. It calculates a standard score that describes the difference in terms of the number of standard deviations that the means are different. A large effect size for Cohen’s d is 0.80 or higher, as is commonly accepted when using the measure.

* **Effect Size**: Cohen’s d of at least 0.80.

We can use the default and assume a minimum statistical power of 80% or 0.8.

* **Statistical Power**: 80% or 0.80.

For a given experiment with these defaults, we may be interested in estimating a suitable sample size. That is, how many observations are required from each sample in order to at least detect an effect of 0.80 with an 80% chance of detecting the effect if it is true (20% of a Type II error) and a 5% chance of detecting an effect if there is no such effect (Type I error).

We can solve this using a power analysis.

The statsmodels library provides the [TTestIndPower](http://www.statsmodels.org/dev/generated/statsmodels.stats.power.TTestIndPower.html) class for calculating a power analysis for the Student’s t test with independent samples. Of note is the [TTestPower](http://www.statsmodels.org/dev/generated/statsmodels.stats.power.TTestPower.html) class that can perform the same analysis for the paired Student’s t test.

The function [solve\_power()](http://www.statsmodels.org/dev/generated/statsmodels.stats.power.TTestIndPower.solve\_power.html) can be used to calculate one of the four parameters in a power analysis. In our case, we are interested in calculating the sample size. We can use the function by providing the three pieces of information we know (_alpha_, _effect_, and _power_) and setting the size of argument we wish to calculate the answer of (_nobs1_) to “_None_“. This tells the function what to calculate.

A note on sample size: the function has an argument called ratio that is the ratio of the number of samples in one sample to the other. If both samples are expected to have the same number of observations, then the ratio is 1.0. If, for example, the second sample is expected to have half as many observations, then the ratio would be 0.5.

The TTestIndPower instance must be created, then we can call the _solve\_power()_ with our arguments to estimate the sample size for the experiment.

```
# estimate sample size via power analysis
from statsmodels.stats.power import TTestIndPower

# parameters for power analysis
effect = 0.8
alpha = 0.05
power = 0.8

# perform power analysis
analysis = TTestIndPower()
result = analysis.solve_power(effect, power=power, nobs1=None, ratio=1.0, alpha=alpha)
print('Sample Size: %.3f' % result)

-----------------
Sample Size: 25.525
```

Running the example calculates and prints the estimated number of samples for the experiment as 25. This would be a suggested minimum number of samples required to see an effect of the desired size.

## Reference

{% embed url="https://machinelearningmastery.com/statistical-power-and-power-analysis-in-python/" %}
