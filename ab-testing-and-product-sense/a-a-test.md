# A/A test

### What is A/A testing?

A/B testing involves driving traffic to two different pages — the control and the variation (which is another version of the original page) — to see which version converts better.

Conversely, an A/A test pits two exactly identical pages against each other. Instead of discovering a lift in conversions, the goal of an A/A test is to check that there is **no difference** between your control and variation versions.

### Offline testing <a href="#offline-testing" id="offline-testing"></a>

The offline A/A test helps you ensure that Type 1 errors (false positives) are controlled as expected (i.e. 5%). One way that higher than expected false positives can arise is when your variance estimation is incorrect. This can occur is when the randomization unit is different than the analysis unit.&#x20;

The offline A/A test works by:

1. Querying a representative sample of your data.
   * For example, if you have a set of users you plan to run your A/B experiment with, then use a recent copy of their actual data that covers the same length of time you plan to run your experiment for.
2. Randomly assign your subjects (user, session, pageview, etc.) to test or control
3. Calculate your relevant metric (clicks per users, # of conversions, etc.)
4. Run the appropriate test (t-test, two proportion z-test, etc.)1
5. Repeat steps 2-5 up 1000 times2
6. Analyze the results
   * Ensure that the % of false positives are in line with expectations. For example, if your p-value cutoff is 0.05, a common industry practice, you’d expect a 5% false positive rate.
   * Plot the distribution of p-values and check that they are uniform3.

### Online testing <a href="#online-testing" id="online-testing"></a>

Contrary to the offline test, the online A/A test is run on real users. Engineers must implement the randomization logic to assign the subjects to the appropriate group, and log the assignments. Both groups receive the control experience, so the new treatment experience does not need to be built. While offline tests help you validate your metrics & statistical tests, online A/A tests will help you validate the rest of your experimentation infrastructure.

### A/A tests in the wild <a href="#aa-tests-in-the-wild" id="aa-tests-in-the-wild"></a>

* I used an offline A/A test simulation to validate that I could use a randomization unit (user) different than my analysis unit (session) without seeing a higher rate of false positives.
* For another experiment we ran in a new app we built, we were unable to properly randomize by user since the app did not have access to the necessary cookies when being loaded. As a hack around this limitation, we decided to rely on the browser cache to prevent a different version of the app being loaded for a user on each page view. We ran an online A/A test to understand this approach, and learned that using the browser cache would give us a good enough approximation of randomizing by user, since users rarely cleared their browser cache.

### Why some organizations practice A/A testing

A/A testing is generally done when organizations are implementing a new A/B testing tool. Running an A/A test at that time can help in:

* Checking the accuracy of the A/B testing tool being implemented
* Setting a baseline conversion rate for future A/B tests
* Deciding on a minimum sample size

#### **Checking the accuracy of an A/B testing tool**

Organizations who are about to purchase an A/B testing tool or want to switch to a new testing software may run an A/A test to ensure that the new software works fine and has been set up correctly.

“A/A testing is a good way to run a sanity check before you run an A/B test. This should be done whenever you start using a new tool or go for a new implementation. A/A testing in these cases will help check if there is any discrepancy in data, let’s say, between the number of visitors you see in your testing tool and the web analytics tool. ”

However, in cases where an A/A test provides a winner between two identical variations, there is a problem. The reasons could be any of the following:

* The tool has not been set up correctly.
* The test hasn’t been conducted correctly.
* The testing tool is inefficient.

#### **Determining the baseline conversion rate**

Before running any A/B test, you need to know the conversion rate that you will be benchmarking the results against. This benchmark is your baseline conversion rate.

An A/A test can help you set the baseline conversion rate for your website. Let’s explain this with the help of an example. Suppose you are running an A/A test where the control gives 303 conversions out of 10,000 visitors, and the identical variation B gives 307 out of 10,000 conversions. Consequently, the conversion rate for A is 3.03%, and that for B is 3.07%. There is no difference between the two variations. Therefore, the conversion rate benchmark for future A/B tests can be set at 3.03–3.07%.&#x20;

#### **Deciding a minimum sample size**

A/A testing can help you get an idea about the minimum sample size from your website traffic. A small sample size would not include sufficient traffic from multiple segments. You might miss out on a few segments which can potentially impact your test results. With a larger sample size, you have a greater chance of taking into account all segments that impact the test.

Corte says, “A/A testing can be used to make a client understand the importance of getting enough people through a test before assuming that a variation is outperforming the original.”

[Michal Parizek](https://www.linkedin.com/in/michalparizek), Senior eCommerce & optimization Specialist at Avast, shares similar thoughts. He says, “At Avast, we did a comprehensive A/A test last year. And it gave us some valuable insights and was worth doing!” According to him, “It is always good to check the statistics before final evaluation.”

At Avast, they ran an A/A test on two main segments—customers using the free version of the product and customers using the paid version. They did so to get a comparison.

The A/A test had been live for 12 days, and they managed to get quite a lot of data. Overall, the test involved more than 10 million users and more than 6,500 transactions.

The “free” segment, they saw a 3% difference in the conversion rate and a 4% difference in [Average Order Value (AOV)](https://vwo.com/blog/personalization-to-increase-aov-and-conversion-rates/). The “paid” segment, they saw a 2% difference in conversion and a 1% difference in AOV.

“However, all uplifts were NOT statistically significant,” says Michal. He adds, “Particularly in the ‘free’ segment, the 7% difference in sales per user (combining the differences in the conversion rate and AOV) might look trustworthy enough to a lot of people. But that is misleading. Given these results from the A/A test, we have decided to implement internal A/B testing guidelines/lift thresholds. For example, if the difference in the conversion rate or AOV is lower than 5%, be very suspicious that the potential lift is not driven by the difference in the design but by chance.”

Michal sums up his opinion by saying, “A/A testing helps discover how A/B testing could be misleading if it is not taken seriously. Plus, it is a great way to spot any bugs in the tracking setup.”

### Problems with A/A testing

In a nutshell, the two main problems inherent in A/A testing are:

* The ever-present element of randomness in any experimental setup
* The requirement of a large sample size

#### **Element of randomness**

The problem (for lack of a better word) with A/A testing is that there is always an element of randomness involved. In some cases, the experiment acquires statistical significance purely by chance, which means that the change in the conversion rate between A and its identical version is probabilistic and does not denote absolute certainty.&#x20;

#### **Requirement of a large sample size**

One problem with A/A testing is that it can be time-consuming. When testing identical versions, you need a large sample size to find out if A is preferred to its identical version.&#x20;

### Other methods and alternatives to A/A testing

A few experts believe that A/A testing is inefficient as it consumes a lot of time that could otherwise be used in running actual A/B tests. However, there are others who say that it is essential to run a health check on your A/B testing tool. That said, A/A testing alone is not sufficient to establish whether one testing tool should be preferred over another. When making a critical business decision, such as buying a new tool/software application for A/B testing, there are several factors that should be considered.

There are other things that must be taken into account when a new tool is being implemented:

1. Will the testing platform integrate with my web analytics program so that I can further slice and dice the test data for additional insight?
2. &#x20;Will the tool let me isolate specific audience segments that are important to my business and just test those audience segments?
3. &#x20;Will the tool allow me to immediately allocate 100% of my traffic to a winning variation? This feature can be important for more complicated radical redesign tests where standardizing on the variation may take some time. If your testing tool allows immediate 100% allocation to the winning variation, you can reap the benefits of the improvement while the page is being built permanently in your CMS.
4. Does the testing platform provide ways to collect both quantitative and qualitative information about site visitors that can be used for formulating additional [test ideas](https://vwo.com/blog/5-easy-ab-test-ideas/)? These would be tools like [heatmap](https://vwo.com/website-heatmap/), scrollmap, visitor recordings, exit surveys, page-level surveys, and visual form funnels. If the testing platform does not have these integrated, do they allow integration with third-party tools for these services.
5. Does the tool allow for personalization? If test results are segmented and it is discovered that one type of content works best for one segment and another type of content works better for a second segment, does the tool allow you to permanently serve these different experiences for different audience segments”?

## Diagnosis with A/A test

After running this analysis against all of the A/B testing metrics of around a dozen Microsoft products, we have found that the typical product has 10-15% of their metrics failing this test for uniformity under the null hypothesis. For some, the failure rate was as high as 30%. It is important to run checks like these on a regular basis.&#x20;

Let’s go ahead and simulate a few hundred of these A/A tests for one of our partner team’s metrics. In this case, we ran the simulation 200 times and made a histogram of the p-values. For a healthy metric with no major issues, here is what that looks like:

![chart, histogram, waterfall chart](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f1a-300x225.png) ![chart, line chart](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f1b-300x225.png)

_Figure 1 – A histogram (left) and cumulative distribution (right) of 200 p-values for a metric taken from simulated A/A tests. This metric does not show any major data quality issues, and the p-value distribution closely matches expectations._

For our healthy metric, the p-values are distributed pretty evenly between 0 and 1. We expect about 10 of the 200 to fall below 0.05, and we see 9. There is a bit of a spike and a dip near $$𝑝=0.5$$, but it’s well within the normal range of variation. It can be a bit difficult to see how good a match the distribution is by looking at the histogram alone, so we prefer looking at the cumulative distribution on the right.&#x20;

Of course, when analyzing dozens or hundreds of these graphs, visually inspecting each graph would be both tedious and subjective. Instead, we use the Anderson-Darling statistical test \[3], which itself produces a p-value, to see if the distribution deviates significantly from a uniform one. Before you ask, yes, we also checked to make sure the Anderson-Darling p-values were uniform under the null hypothesis. You’re not allowed to do a meta-analysis without getting meta. As we will see below, the metrics which fail this test fail it in a very big way, so we can get away with a pretty low p-value threshold to avoid false positives.&#x20;

Now, let’s repeat this for another metric:

![](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f2a-300x225.png) ![](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f2b-300x225.png)

_Figure 2 – Same as Figure 1, but for a metric affected by a single large outlier. Note the spike in the p-value distribution at p=0.32_

The results we see in Figure 2 are certainly…unexpected. In 200 of these simulated A/A tests, this metric never even came close to showing a statistically significant result. All 200 of these p-values are clustered around 0.32.  What could cause such an outcome? It turns out, **this is what you see when a metric is affected by a single large outlier.** In this case, a click event had been mistakenly duplicated many times in our logs. While most users were clicking on a link between 1 and 10 times in the one-week period we looked at, one user had over a million of these duplicated clicks. No matter how we split the user population into two groups for our simulated A/A test, this outlying user always makes it into one of the groups. This outlier inflates the mean and variance for the metric and forces the t-statistic to be very close to 1.0 in every single A/A test.&#x20;

### Detecting A/B issues in an A/A world

Let’s look at another unhealthy metric and see what else we can find.

![](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f3a-300x225.png) ![](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f3b-300x225.png)

_Figure 3 – Same as Figure 1, but for a metric affected by two large outliers of comparable magnitude. Note the two spikes in the distribution._

This metric’s diagnosis is actually pretty similar to the previous one. **This is what happens when you have two large outliers instead of one.** The peak on the left is what happens when both users get assigned to the same group in the A/A test and the peak on the right is what happens when they get assigned to two different groups. By changing the relative magnitudes of the two outliers or adding a third, you can shift these peaks around a bit, but it generally always follows this kind of multi-peaked pattern.&#x20;

In cases like these where outliers are strongly affecting the distribution of p-values, we recommend that our A/B testing partner teams truncate (or cap) their metric distribution. If a user had a million clicks, it is unlikely that this was caused by your A/B test. Instead, **you can pick a sensible upper bound for click count, maybe 50, and replace all click counts greater than 50 with that value.** This kind of truncation greatly improves metric sensitivity.&#x20;

Let’s look at one more unexpected metric in these A/A tests:

![chart, bar chart, histogram](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f4a-300x225.png) ![chart, line chart](https://www.microsoft.com/en-us/research/uploads/prod/2020/10/f4b-300x225.png)

_Figure 4 – Same as Figure 1, but for a metric which measures an extremely rare event. Note the few discretized spikes in the p-value distribution._

Okay, what is going on here? In 200 A/A tests, this metric only showed 8 different p-values. How could that be possible? As it turns out, **this is a case where the metric is measuring occurrences of a very rare event**. In the one-week period we looked at, only 15 users performed the action this metric was trying to measure. There are only 8 different ways to split these 15 people into two groups, from 15-0, 14-1, etc. down to 8-7, so we only get the same 8 p-values again and again. For this kind of metric, the only real solution is not to measure the metric at all, or at least not to view it as reliable. There simply are not enough users performing this action for the A/B testing system to detect meaningful results for this metric.

## Reference

{% embed url="https://vwo.com/blog/aa-test-before-ab-testing/" %}

{% embed url="https://www.microsoft.com/en-us/research/group/experimentation-platform-exp/articles/p-values-for-your-p-values-validating-metric-trustworthiness-by-simulated-a-a-tests/" %}
