# Effect Size

### What Is Effect Size?

An effect size refers to the size or magnitude of an effect or result as it would be expected to occur in a population.

It is common to organize effect size statistical methods into groups, based on the type of effect that is to be quantified. Two main groups of methods for calculating effect size are:

* **Association**. Statistical methods for quantifying an association between variables (e.g. correlation).
* **Difference**. Statistical methods for quantifying the difference between variables (e.g. difference between means).

The result of an effect size calculation must be interpreted, and it depends on the specific statistical method used. A measure must be chosen based on the goals of the interpretation. Three types of calculated result include:

* **Standardized Result**. The effect size has a standard scale allowing it to be interpreted generally regardless of application (e.g. Cohen’s d calculation).
* **Original Units Result**. The effect size may use the original units of the variable, which can aid in the interpretation within the domain (e.g. difference between two sample means).
* **Unit Free Result**. The effect size may not have units such as a count or proportion (e.g. a correlation coefficient).

The effect size does not replace the results of a statistical hypothesis test. Instead, the effect size complements the test. Ideally, the results of both the hypothesis test and the effect size calculation would be presented side-by-side.

* **Hypothesis Test**: Quantify the likelihood of observing the data given an assumption (null hypothesis).
* **Effect Size**: Quantify the size of the effect assuming that the effect is present.

### How to Calculate Effect Size

The calculation of an effect size could be the calculation of a mean of a sample or the absolute difference between two means. It could also be a more elaborate statistical calculation.

#### Calculate Association Effect Size

The association between variables is often referred to as the “_r family_” of effect size methods. This name comes from perhaps the most common method for calculating the effect size called Pearson’s correlation coefficient, also called Pearson’s r.

The Pearson’s correlation coefficient measures the degree of linear association between two real-valued variables. It is a unit-free effect size measure, that can be interpreted in a standard way, as follows:

* \-1.0: Perfect negative relationship.
* \-0.7: Strong negative relationship
* \-0.5: Moderate negative relationship
* \-0.3: Weak negative relationship
* 0.0: No relationship.
* 0.3: Weak positive relationship
* 0.5: Moderate positive relationship
* 0.7: Strong positive relationship
* 1.0: Perfect positive relationship.

The Pearson’s correlation coefficient can be calculated in Python using the [pearsonr() SciPy function](https://docs.scipy.org/doc/scipy/reference/generated/scipy.stats.pearsonr.html).

The example below demonstrates the calculation of the Pearson’s correlation coefficient to quantify the size of the association between two samples of random Gaussian numbers where one sample has a strong relationship with the second.&#x20;

Another very popular method for calculating the association effect size is the r-squared measure, or r^2, also called the coefficient of determination. It summarizes the proportion of variance in one variable explained by the other.

#### Calculate Difference Effect Size

The difference between groups is often referred to as the “_d family_” of effect size methods.

This name comes from perhaps the most common method for calculating the difference between the mean value of groups, called Cohen’s d.

Cohen’s d measures the difference between the mean from two Gaussian-distributed variables. It is a standard score that summarizes the difference in terms of the number of standard deviations. Because the score is standardized, there is a table for the interpretation of the result, summarized as:

* **Small Effect Size**: d=0.20
* **Medium Effect Size**: d=0.50
* **Large Effect Size**: d=0.80

The Cohen’s d calculation is not provided in Python; we can calculate it manually.

The calculation of the difference between the mean of two samples is as follows:

| d = (u1 - u2) / s |
| ----------------- |

Where _d_ is the Cohen’s d, _u1_ is the mean of the first sample, _u2_ is the mean of the second sample, and _s_ is the pooled standard deviation of both samples.

The pooled standard deviation for two independent samples can be calculated as follows:

| s = sqrt(((n1 - 1) \* s1^2 + (n2 - 1) \* s2^2) / (n1 + n2 - 2)) |
| --------------------------------------------------------------- |

Where _s_ is the pooled standard deviation, _n1_ and _n2_ are the size of the first sample and second samples and _s1^2_ and _s2^2_ is the variance for the first and second samples. The subtractions are the adjustments for the number of degrees of freedom.

The example below calculates the Cohen’s d measure for two samples of random Gaussian variables with differing means. The example is contrived such that the means are different by one half standard deviation and both samples have the same standard deviation.

<pre><code># calculate the Cohen's d between two samples
from numpy.random import randn
from numpy.random import seed
from numpy import mean
from numpy import var
from math import sqrt 

# function to calculate Cohen's d for independent samples
<strong>def cohend(d1, d2):
</strong>    # calculate the size of samples
    n1, n2 = len(d1), len(d2)

    # calculate the variance of the samples
    s1, s2 = var(d1, ddof=1), var(d2, ddof=1)

    # calculate the pooled standard deviation
    s = sqrt(((n1 - 1) * s1 + (n2 - 1) * s2) / (n1 + n2 - 2))

    # calculate the means of the samples
    u1, u2 = mean(d1), mean(d2)

    # calculate the effect size 
    return (u1 - u2) / s 

# seed random number 
generatorseed(1)

# prepare data
data1 = 10 * randn(10000) + 60
data2 = 10 * randn(10000) + 55

# calculate cohen's d
d = cohend(data1, data2)
print('Cohens d: %.3f' % d)
--------------------------
Cohen's d: 0.500</code></pre>

We can see that as expected, the difference between the means is one half of one standard deviation interpreted as a medium effect size.

Two other popular methods for quantifying the difference effect size are:

* **Odds Ratio**. Measures the odds of an outcome occurring from one treatment compared to another.
* **Relative Risk Ratio**. Measures the probabilities of an outcome occurring from one treatment compared to another.

## Reference

{% embed url="https://machinelearningmastery.com/effect-size-measures-in-python/" %}
