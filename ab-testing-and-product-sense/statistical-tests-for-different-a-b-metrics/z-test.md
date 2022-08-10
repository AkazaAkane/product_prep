# z test

With our understanding of the CLT, the Z-test is simple to explain. We’re running a Z-test if:

* our null hypothesis is the relationship between population means or other test statistics, and
* we can assume that the CLT holds and the test statistics follow a normal distribution

> In probability theory, the central limit theorem (CLT) establishes that, in some situations, when independent random variables are added, their properly normalized sum tends toward a normal distribution (informally a "bell curve") even if the original variables themselves are not normally distributed. The theorem is a key concept in probability theory because it implies that probabilistic and statistical methods that work for normal distributions can be applicable to many problems involving other types of distributions.

In layman’s terms, the CLT says that: given a population P, with some metric M whose true average is μM, and you take a random sample of independent measurements from P and take the average aM, then aM follows a normal distribution. Note that the error of the measurement, eM=μM−aM also follows a normal distribution since μM is a constant. eM is the quantity most closely related to the H0 null hypothesis' test statistic.

#### 3 cases when we cannot rely on the CLT to hold:

1. the distribution does not have a mean, eg. the Cauchy distribution
2. violating the independence assumption of the CLT, eg. with a random walk
3. small sample size, eg. when events such as fraudulent transactions have a very low probability

### Types of Z-test

It’s called Z-test because when running the numbers, it’s common to transform the data to a standard normal distribution N(0,1). In the old days, before computers, the p-value, ie. the portion of the normal distribution outside the normalized test statistic (eg. the difference of the means) would be read off a printout table (eg. the back of statistics textbooks), and this statistic is conventionally denoted with the letter z. A more verbose, but descriptive name would be **test-for-normally-distributed-test-statistics**.

The Z-test is not one specific test, it’s a kind of test.

* Test 1: Z-test for a population mean (variance known)
* Test 2: Z-test for two population means (variances known and equal)
* Test 3: Z-test for two population means (variances known and unequal)
* Test 4: Z-test for a proportion (binomial distribution)
* Test 5: Z-test for the equality of two proportions (binomial distribution)
* Test 6: Z-test for comparing two counts (Poisson distribution)
* Test 13: Z-test of a correlation coefficient
* Test 14: Z-test for two correlation coefficients
* Test 23: Z-test for correlated proportions
* Test 83: Z-test for the uncertainty of events
* Test 84: Z-test for comparing sequential contingencies across two groups using the ‘log odds ratio’

### Formulas

The math is similar to the discussion in the CLT post. We’re sampling a distribution and computing a test statistic, and assuming that it follows a normal distribution N(μ,σ2). In an A/B test, we have two normal distributions, N(μA,σ2A) and N(μB,σ2B) with samples sizes NA and NB, and the test statistic is N(μA,σ2A)−N(μB,σ2B)=N(μ=μB−μA,σ2=σ2A+σ2B).

The test statistic then is Z=μσ, we use this to get the p-value for the experiment. This is simply the normalized distance from the mean of the distribution. With this normalized distance, we can use a table for a standard normal distribution table and read off the p-value. In the age of computers, we actually don’t have to do this final normalization step to get Z, we can just get the p-value from the original N(μ,σ2) distribution.

In an A/B test setting, μA and μB are known. The trick is, what are the standard deviations σ2A and σ2B? We compute it from the sample standard devation s2, like σ2=s2/N. The sample standard deviation is:

* for conversion, the population distribution is [Bernoulli](https://en.wikipedia.org/wiki/Bernoulli\_distribution), so s2=μ(1−μ)
* for timespent, you can compute the standard error from the data directly s2=1/N\*∑(μ−xi)^2, where xi are the invididual timespents per user, and μ=1/N\*∑xi.

## Reference

{% embed url="https://bytepawn.com/ab-testing-and-the-ztest.html" %}
