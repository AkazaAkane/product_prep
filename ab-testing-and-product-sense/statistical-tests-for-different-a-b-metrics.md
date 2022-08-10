# Statistical Tests for Different A/B Metrics

### Summary <a href="#1215" id="1215"></a>

The question on the correct statistical tests is essentially asking which test has the **most sensitivity or statistical power**, given the experiment and control group data.

The power is closely connected with the underlying distribution of our metrics and the independence of our samples. Therefore, it is always good practice to **check the distribution skewness** and **think through the assumptions** before proceeding with the analysis.

Below are three tips based on my personal experience and my learning from the references:

1. When the data is skewed, the t-test is not that robust and the Mann-Whitney test is not that weak.
2. Consider transforming the metric (e.g. log-transformation) to alleviate the skewness or even replace it with a more normally distributed one.
3. If you don’t want to mess with the daunting statistics, another way to go is to try variance reduction techniques such as CUPED. After all, our purpose is to attain high power and trustworthy significance inferences.

### 1. User Average Metrics (e.g. like/user, staytime/user, etc) <a href="#1215" id="1215"></a>

> Suggested Test**:** Welch’s T-Test if the metric is not heavily sparse and skewed. Otherwise, choose **Mann-Whitney Rank Sum U-test**

_**What are user average metrics?**_

User average metrics are indicators that average the total business performance by the number of unique users, such as the **average number of likes per user** and **average stay time per user** during the experiment period.&#x20;

_**How to test user average metrics**_

The method for the hypothesis testing on the sample mean statistics is usually the **two-sample t-test.** In plain terms, the two-sample t-test determines if the expected means of a metric in two populations are equal, by comparing the sample mean between the two sample groups. The **two** **assumptions** in a candid but less precise way are:

* **Each sample observation needs to be independent and follow a normal or quasi-normal distribution (i.i.d.):** The independent distribution assumption is often satisfied because each user can be seen as an independent individual\*, and the randomization unit for an experiment is usually user-level (most experiment determines which experiment groups a certain user falls into by its user id). **With independence, Central Limit Theorem can then be applied to derive normality**, which satisfies this assumption for the two-sample t-test. It is worth reiterating that though the robustness of the t-test under CLT is recognized that the t-test result is generally effective even if the population distribution is not technically normal (quasi/asymptotic normal), which is often the case in real-world problems, **the robustness is bounded for distributions with non-moderate departure from normality** ([Bartlett, 1935](https://www.cambridge.org/core/journals/mathematical-proceedings-of-the-cambridge-philosophical-society/article/abs/effect-of-nonnormality-on-the-t-distribution/400D10D04B8C956E6F2D9783CB6BEF23); [Snijders, 2011](https://www.stats.ox.ac.uk/\~snijders/SM\_robustness.pdf)). It becomes less trustworthy if the underlying distributions are severely skewed, multi-modal, or sparse with extreme outliers.
* **The variances for the evaluation metric in the two populations can be unknown, but they need to be homogeneous:** This is not a stringent assumption as we can employ Welch’s t-test, or the unequal variances t-test, to proceed with the hypothesis testing for the equal means. The mathematical differences between Welch’s t-test and Student's t-test are mainly about the degrees of freedom and sample variance estimates. In fact, **we should always use Welch’s t-test instead of the Student t-test**. This is not only implied by some academic articles (Delacre et al., 2017), but also suggested by the experiment platform engineers in tech companies based on my experience because it can be **computationally expensive** and time-costly to pull all the data from the database and assert the variances are equal, which is also usually a false proposition (Calculating variances can be very onerous at millions and billions scale). Delacre et al. (2017) show that the Welch’s _t_-test provides better control of Type 1 error rates when the assumption of homogeneity of variance is not met, and it loses little robustness compared to Student’s _t_-test when the assumptions are met. Therefore, they argue that Welch’s _t_-test should be used as a default strategy.

![Equation 1: t-statistic](https://miro.medium.com/max/1400/1\*MnNwmvsrJDMQoGd6FYFE9w.png)

For those highly skewed metrics, the nonparametric approach for two independent groups — **Mann-Whitney Rank Sum U-test (MW Test)** — is more appropriate because it uses ‘ranks’ rather than real values to determine whether the difference in metrics is significant, which bypasses the distortion introduced by extreme values. What’s more, the sensitivity (power) of the MW test is not even disappointing even when it loses information by substituting the absolute values with ranks, because other parametric tests (e.g. t-test) have a more substantial drop in their capability to make true positive inferences when the distribution is highly skew — _VK Team conducted simulations and demonstrated this idea with great details. I would strongly recommend checking out their Medium article_ [_here_](https://vkteam.medium.com/practitioners-guide-to-statistical-tests-ed2d580ef04f#9e58)_._

However, the downside of the MW test is that it is **computationally intensive** because it requires sorting the complete sample set to generate the ranks, so one should take the data size into consideration before deciding to go with the MW test.

### 2. User-level Conversion Metrics <a href="#f15d" id="f15d"></a>

> Suggested Test: Proportional Z-test

_**What are user-level conversion metrics?**_

User-level conversions are metrics out of **binary outcomes**— whether this user retains or not, whether this user converts or not, etc. In other words, all the users will have only one observation which is an identifier of either 1 or 0, making the user-level conversion metrics essentially **Binomial proportional statistics** (# converted users/# all users).

_**How to test user-level conversion metrics?**_

According to the Central Limit Theorem (CLT), we can safely approximate the distribution of the Binomial proportional statistics using normal distributions. Another way to think about this is that **proportioning the Binomial aggregation is very much like taking the average**, where CLT is effective in asserting normality for the distribution of any sample means when the total number of users (population size) is sufficiently large. As a result, the z-test becomes a perfect candidate.

![Equation 2: Binomial Proportion](https://miro.medium.com/max/1400/1\*AkRxag7OEiSN\_09GXFIfpg.png)

The assumption for the normal approximation is usually satisfied, compared to the above-discussed user average metrics, because the underlying sampling distribution is binomial rather than drastically skewed thanks to the nice property of binary Bernoulli events. Additionally, the number of users, or the population size, is generally very large to support a reasonable approximation (we are often confident when _N\*p>5_ and _N\*(1-p)>5_ as a rule of thumb. See reference for more details).

It is worth noting that Central Limit Theorem cannot be trusted and the z-test cannot be applied if the sample values in the experiment are not **independent and identically distributed (i.i.d.)**. This is not a concern in user-level-metric scenarios because by default the randomization unit is on users, and therefore we believe every person behaves individually. However, **this independent assumption might not hold** when the metrics are aggregated at a granular level than users, such as the pageview-level conversion metrics in section three below.

![Equation 3: z-statistic](https://miro.medium.com/max/1400/1\*HPQwUMmBKb6jMb0QwwIa-A.png)

### 3. Pageview-level Conversion Metrics <a href="#e753" id="e753"></a>

> Suggested Test: Delta Method + T-Test

_**What are some pageview-level conversion metrics?**_

One common example is the click-through rate (CTR), defined as # click events / # view events.

_**What are the problems of simply using t-tests here?**_

It is usually complicated to analyze the statistical significance of pageview-level conversion metrics, such as Click-through rate (CTR), in A/B experiments. The essential problem is that the **unit of analysis** (i.e. event level — click events and impression events) is different from the **unit of randomization** (i.e. user level), which could **invalidate our t-test and underestimate the variance.**

Specifically, When we calculate the p-values for CTRs between the experiment and control group, we are essentially aggregating all the click events and dividing by all the impression events of all the users in each group, and the t-test the difference. This process has an inherent normality assumption that data samples need to be independently drawn from a normal distribution. Put differently, since each sample observation is a view event, **we need to ensure all the view events are independent.** Otherwise, we are not able to use CLT to assert normality for our mean statistics.

However, this independence assumption is violated. In A/B experiments where we randomize by users (_this is usually the default option because each user would have an inconsistent product experience if we randomize by sessions_), a user can have multiple clicks/views events, and these events are correlated to our common sense — We can at most assert each individual behaves independently, but rather the behaviors for now and tomorrow are not time series correlated. As a result, the sample variance will no longer be an unbiased estimate of the population variance — the **true sample variance for our mean statistics could be higher** than our estimation because **co-variance steps in** and we missed it in our calculation.

Therefore, for metrics with a granularity of event level, it is inevitable to underestimate the variance if sticking to the traditional t-test methods. The direct consequence of underestimating the sample variance is false positive — The true and high variance can **possibly drive the sample means away** from the null hypothesis and **mislead us to derive low p-values even then the treatment is not effective**.

_**How to solve this sample-dependency problem?**_

There are two ways to resolve this issue: (1) t-test the difference using the **delta method** or (2) proceeding t-test with the empirical variance estimated using the **bootstrapping** approach, which is also emphasized in the <_Trustworthy Online Controlled Experiments: A Practical Guide to A/B Testing_> "Having the randomization unit be coarser than the analysis unit, such as\
randomizing by the user and analyzing the click-through rate (by page), will work, but requires more nuanced analysis methods such as bootstrap or the delta method (Deng et al. 2017, Deng, Knoblich and Lu 2018, Tang et al. 2010, Deng et al. 2011)."

* **Delta Method**: Delta method is generally considered a more efficient approach. The key idea can be interpreted as re-writing the pageview-level CTR ratio metrics into the **ratio of two ‘average user level metrics’ and asserting CLT on this ratio metric** _(using Taylor expansion and Slutsky theorem)_, so that we could convert the **granularity of analysis from page level to user level**, which is now consistent with our unit of diversion (i.e. user). In this way, we manage to restore i.i.d. because **both our numerator and the denominator are the averages of i.i.d. samples**. Using CTR as an example, the`AVG.clicks` and `AVG.views` are jointly bivariate normal in the limit and i.i.d., making`CTR` , the ratio of the two averages, becoming also normally distributed (Kohavi, R., Tang, D., & Xu, Y. (2020)). The code implementation can be found in Ahmad Nur Aziz’s medium post [here](https://medium.com/@ahmadnuraziz3/applying-delta-method-for-a-b-tests-analysis-8b1d13411c22).

![Equation 4: Click-through Rate (CTR) Definition](https://miro.medium.com/max/1400/1\*Agfs70YUv8\_uuHW19a1GbA.png)

![Equation 5: t-statistic using the Delta Method](https://miro.medium.com/max/1400/1\*r09F96EwG8wdxM6fA9NOTg.png)

* **Bootstrapping**: Bootstrapping is a simulation-based method to empirically calculate the variance of the sample mean statistics. The key idea is to repeatedly draw many samples from all the view events in each group stratified by user ids, then calculate many mean statistics from these bootstrap samples, and estimate the variance for these statistics accordingly. **Stratifying by user ID ensures i.i.d. assumption is met** and the variance estimation is trustworthy.

However, bootstrapping is computationally expensive as the simulation is basically manipulating and aggregating the tens of billions of user logs for many iterations (_Complexity \~ O(nB) where n is the sample size and B is the number of bootstrap iterations_). As a result, in spite of its flexibility, it is usually not the first go-to option at tech companies and is more used as a bullet-proof double-check benchmark for critical decisions.

### 4. Percentile Metrics <a href="#5b15" id="5b15"></a>

> Suggested Test: Central Limit Theorem (CLT) + Z-Test

_**What are percentile metrics?**_

Quantile metrics such as 95th percentile page load time are critical to A/B testing as many business aspects are characterized by edge cases and thus are better described by quantile metrics.

_**What are the challenges of testing percentile metrics?**_

Most of the percentile metrics, for example, 95 percentile page load latency, are engineering performance indicators defined in the page-view granularity. Consequently, the randomization unit (users) won’t match the analysis unit (page views), which invalidates the independence assumption and the population variance estimate using the plain sample variance.

_**How to test percentile metrics?**_

* **Bootstrapping:** Bootstrapping, as a ‘universally applicable’ tool, can be used here to estimate the empirical variance and t-test the sample means of percentile metrics. Again, nevertheless, its computation is prohibitively expensive and thus cannot scale well.
* **CLT+Proportional Z-test:** There is quite a lot of maths behind the scene but I will try to demonstrate the main ideas in simple terms (Strongly recommend reading [Alex Deng (2021)](https://alexdeng.github.io/causal/abstats.html#percentilevar) if interested). To begin with, we shall first probe into the distribution of the quantile metrics, statisticians have proved that the sample percentiles are approximately normal:

![Equation 6: An Illustration of Asymptotic Normality for 95th Percentile Metrics.](https://miro.medium.com/max/1400/1\*1IizPmbW2FaUnXOAsmNx-Q.png)

_**σ**_ refers to the standard deviation of whether a data point is smaller than the 95th percentile (it cannot be estimated in the ‘traditional’ Bernoulli way because observations here are dependent). _**F**_ is an unknown probability density function for the underlying data.

Great! Since quantiles follow the normal distribution, we can use the Z-test to calculate the p-values. Well, this is generally the direction we are going for, but there is one crucial blockers— the _**σ**_ as well as the probability density function _**F**_ is unknown and could be hard to estimate.

So our goal, for now, is to derive a good estimate of the variance (circled in red). There is more than one approach employed in the industry. Data scientists at LinkedIn, Quora and Wish estimate this density function directly, while Microsoft and TikTok refer to a novel idea introduced by [Alex Deng, Knoblich, and Lu](https://alexdeng.github.io/causal/probability-minimum.html#ref-Deng2018kdd) ([2018](https://alexdeng.github.io/causal/probability-minimum.html#ref-Deng2018kdd)) that bypass the estimation of the density function _**F**_. Instead, they first estimate the confidence interval for the true percentile by investigating the distribution of the Binomial proportion of an observation being smaller than the percentile, as denoted in equation 7:

![](https://miro.medium.com/max/1400/1\*O12ApoBTCRxS2za66pKjew.png)Equation 7: Confidence Interval for 95th Percentile Metrics

Secondly, they work backward to derive the variance estimate based on the length of the approximated confidence interval.

![](https://miro.medium.com/max/1400/1\*SH3xHwmHxn7eWbPkW1s7oA.png)Equation 8: Deriving Standard Deviation from Confidence Interval

Lastly, with the variance estimate, we can proceed to calculate the z-statistic for our sample percentile and the according p-values, similar to those average metrics.

### 5. SUM Metrics (Not Recommended) <a href="#3ca6" id="3ca6"></a>

> Suggested Test: Simulations

_**What are SUM metrics?**_

SUM metrics refer to the aggregated-up indicators including the total number of the article read times, the total GMV, the total videos posted, and so forth for the experiment and control groups.

_**What are the challenges here?**_

SUM metrics are usually the north star metrics for product development but can be difficult to test due to the lack of statistical validity and confounding errors. Specifically, the SUM metrics can be decomposed into **user average metrics** and **user count** in the experiment and control groups, which means the SUM metrics are affected not only by the **actual business fluctuations** (user average metrics) and by the **inevitable random error introduced by the traffic diversion**.:

![](https://miro.medium.com/max/1400/1\*V5xmmH2x-VybQSp1jRt9mA.png)Equation 9: Disentangling SUM Metrics

While the user average metric follows the (quasi) normal distribution and the traffic diversion follows the Binomial distribution with p = 0.5, the classic statistic has no complete study regarding the distribution of the product of the two.

_**How should we test user average metrics?**_

We can approach this problem with **simulation**. Since the two distributions are deterministic and independent, we can acquire their own probability density function (PDF) by iteratively drawing samples. If then multiplying the two PDFs, we could derive an empirical PDF for the SUM metric. The p-value can thus be calculated by investigating how the SUM statistic from what we actually see in the treatment group fit into our simulated distribution.

This is relatively a daunting process with a fair amount of approximations. Therefore, it is usually not recommended. I would suggest testing the effectiveness/statistical significance of the product feature using the user average metric and calculating the incremental lift in the SUM metrics if the management really cares.
