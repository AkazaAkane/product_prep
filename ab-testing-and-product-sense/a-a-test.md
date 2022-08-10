# A/A test

### What is A/A testing?

A/B testing involves driving traffic to two different pages — the control and the variation (which is another version of the original page) — to see which version converts better.

Conversely, an A/A test pits two exactly identical pages against each other. Instead of discovering a lift in conversions, the goal of an A/A test is to check that there is **no difference** between your control and variation versions.

### Offline testing <a href="#offline-testing" id="offline-testing"></a>

The offline A/A test helps you ensure that Type 1 errors (false positives) are controlled as expected (i.e. 5%). One way that higher than expected false positives can arise is when your variance estimation is incorrect. This can occur is when the randomization unit is different than the analysis unit. Running an offline A/A test can help you quickly identify whether you’ll be prone to these issues.

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

From just a handful of A/A tests I’ve run in my career, I’ve seen big benefits:

* I used an offline A/A test simulation to validate that I could use a randomization unit (user) different than my analysis unit (session) without seeing a higher rate of false positives.
* For another experiment we ran in a new app we built, we were unable to properly randomize by user since the app did not have access to the necessary cookies when being loaded. As a hack around this limitation, we decided to rely on the browser cache to prevent a different version of the app being loaded for a user on each page view. We ran an online A/A test to understand this approach, and learned that using the browser cache would give us a good enough approximation of randomizing by user, since users rarely cleared their browser cache.
* Prior to running an A/B test that involved a [server side redirect](https://en.wikipedia.org/wiki/HTTP\_302), we ran an online A/A’ test4, where users in one group were redirected to the same page. This allowed us to isolate the impact of redirects alone, so that in our subsequent A/B test with redirects to a different page, we could have a sense of how much of the change was coming from redirects versus seeing the different page. The test also revealed some sample ratio mismatch (SRM) issues that arose from doing redirects.
  * The redirects caused duplicate requests for certain browsers, which led to SRM at the session grain as each request created a new session.
  * Certain users, like bots, won’t follow redirects, which can cause them to drop off and not appear in your system of record, leading to SRM at the user grain when using the system of record.
  * Discovering these issues before our A/B test allowed us to switch to user grain metrics leveraging a new data source, which eliminated the SRM issues.

A/A tests are incredibly useful. We should run more of them.

### Why some organizations practice A/A testing

A/A testing is generally done when organizations are implementing a new A/B testing tool. Running an A/A test at that time can help in:

* Checking the accuracy of the A/B testing tool being implemented
* Setting a baseline conversion rate for future A/B tests
* Deciding on a minimum sample size

#### **Checking the accuracy of an A/B testing tool**

Organizations who are about to purchase an A/B testing tool or want to switch to a new testing software may run an A/A test to ensure that the new software works fine and has been set up correctly.

“A/A testing is a good way to run a sanity check before you run an A/B test. This should be done whenever you start using a new tool or go for a new implementation. A/A testing in these cases will help check if there is any discrepancy in data, let’s say, between the number of visitors you see in your testing tool and the web analytics tool. ”

In an A/A test, a web page is A/B tested against an identical variation. When there is absolutely no difference between the control and the variation, it is expected that the result will be inconclusive. However, in cases where an A/A test provides a winner between two identical variations, there is a problem. The reasons could be any of the following:

* The tool has not been set up correctly.
* The test hasn’t been conducted correctly.
* The testing tool is inefficient.

“I typically will run an A/A test when a client seems uncertain about their testing platform, or needs/wants additional proof that the platform is operating correctly. There is no better way to do this than to take the same page and test it against itself with no changes whatsoever. We’re essentially tricking the platform and seeing if it catches us! The bottom line is that while I don’t run A/A tests very often, I will occasionally use it as a proof of concept for a client, and to help give them confidence that the split testing platform they are using is working as it should.”

#### **Determining the baseline conversion rate**

Before running any A/B test, you need to know the conversion rate that you will be benchmarking the results against. This benchmark is your baseline conversion rate.

An A/A test can help you set the baseline conversion rate for your website. Let’s explain this with the help of an example. Suppose you are running an A/A test where the control gives 303 conversions out of 10,000 visitors, and the identical variation B gives 307 out of 10,000 conversions.&#x20;

Consequently, the conversion rate for A is 3.03%, and that for B is 3.07%. There is no difference between the two variations. Therefore, the conversion rate benchmark for future A/B tests can be set at 3.03–3.07%. If you run an A/B test later and get an uplift within this range, this might mean that the result is not significant.

#### **Deciding a minimum sample size**

A/A testing can help you get an idea about the minimum sample size from your website traffic. A small [sample size](https://vwo.com/blog/how-to-calculate-ab-test-sample-size/) would not include sufficient traffic from multiple segments. You might miss out on a few segments which can potentially impact your test results. With a larger sample size, you have a greater chance of taking into account all segments that impact the test.

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

Let us look at them one by one:

#### **Element of randomness**

As pointed out earlier in the post, checking the accuracy of a testing tool is the main reason for running an A/A test. However, what if you find a difference between conversions of control and an identical variation? Do you always point it out as a bug in the A/B testing tool?

The problem (for lack of a better word) with A/A testing is that there is always an element of randomness involved. In some cases, the experiment acquires statistical significance purely by chance, which means that the change in the conversion rate between A and its identical version is probabilistic and does not denote absolute certainty.&#x20;

Tomaz Mazur explains randomness with a real-world example. “Suppose you set up two absolutely identical stores in the same vicinity. It is likely, purely by chance or randomness, that there is a difference in results reported by the two. And it doesn’t always mean that the A/B testing platform is inefficient.”

#### **Requirement of a large sample size**

One problem with A/A testing is that it can be time-consuming. When testing identical versions, you need a large sample size to find out if A is preferred to its identical version. This, in turn, will take too much time.As explained in one of [ConversionXL’s posts](https://cxl.com/blog/aa-testing-waste-time/), “The amount of sample and data you need for an A/A test to prove that there is no significant bias is huge by comparison with an A/B test. How many people would you need in a blind taste testing of Coca-Cola (against Coca-Cola) to conclude that people liked both equally? 500 people, 5000 people?”. Experts at ConversionXL explain that the entire purpose of an optimization program is to reduce wastage of time, resources, and money. They believe that even though running an A/A test is not wrong, there are better ways to use your time when testing. In the post, they mention, “The volume of tests you start is important but even more so is how many you \*finish\* every month and from how many of those you \*learn\* something useful from. Running A/A tests can eat into the “real” testing time.



### VWO’s Bayesian approach and A/A testing

VWO uses a Bayesian-based statistical engine for A/B testing. This allows VWO to deliver smart decisions–it tells you which variation will minimize potential loss.

Most A/B testing tools are seeking the truth. When running an A/A test in a frequentist tool, an erroneous “winner” should only be reported 5% of the time. In contrast, [VWO SmartStats](https://vwo.com/why-us/technology/bayesian-statistics/) is attempting to make a **smart business decision**. We report a **smart decision** when we are confident that a particular variation is not worse than all the other variations, that is, we are saying, “you’ll leave very little money on the table if you choose this variation now.” In an A/A test, this condition is always satisfied—you’ve got nothing to lose by stopping the test now.

The correct way to evaluate a Bayesian test is to check whether the **credible interval for lift** contains 0% (the true value).

He also says that the **possible and simplest reason for A/A tests to provide a winner** is random chance. “With a frequentist tool, 5% of A/A tests will return a winner due to bad luck. Similarly, 5% of A/A tests in a Bayesian tool will report erroneous lifts. Another possible reason is configuration error; perhaps the javascript or HTML is incorrectly configured.”

### Other methods and alternatives to A/A testing

A few experts believe that [A/A testing is inefficient](https://cxl.com/blog/aa-testing-waste-time/) as it consumes a lot of time that could otherwise be used in running actual A/B tests. However, there are others who say that it is essential to run a health check on your A/B testing tool. That said, A/A testing alone is not sufficient to establish whether one testing tool should be preferred over another. When making a critical business decision, such as buying a new tool/software application for A/B testing, there are several factors that should be considered.

Corte points out that though there is no replacement or alternative to A/A testing, there are other things that must be taken into account when a new tool is being implemented:

1. Will the testing platform integrate with my web analytics program so that I can further slice and dice the test data for additional insight?
2. &#x20;Will the tool let me isolate specific audience segments that are important to my business and just test those audience segments?
3. &#x20;Will the tool allow me to immediately allocate 100% of my traffic to a winning variation? This feature can be important for more complicated radical redesign tests where standardizing on the variation may take some time. If your testing tool allows immediate 100% allocation to the winning variation, you can reap the benefits of the improvement while the page is being built permanently in your CMS.
4. Does the testing platform provide ways to collect both quantitative and qualitative information about site visitors that can be used for formulating additional [test ideas](https://vwo.com/blog/5-easy-ab-test-ideas/)? These would be tools like [heatmap](https://vwo.com/website-heatmap/), scrollmap, visitor recordings, exit surveys, page-level surveys, and visual form funnels. If the testing platform does not have these integrated, do they allow integration with third-party tools for these services.
5. Does the tool allow for personalization? If test results are segmented and it is discovered that one type of content works best for one segment and another type of content works better for a second segment, does the tool allow you to permanently serve these different experiences for different audience segments”?

That said, there is still a set of experts or people who would opt for alternatives such as triangulating data over an A/A test. Using this procedure means you have two sets of performance data to cross-check with each other. Use one analytics platform as the base to compare all other outcomes against, to check if there is something wrong or something that needs fixing.

And then there is the argument—why just A/A test when you can get more meaningful insights by running an A/A/B test. Doing this, you can still compare two identical versions while also testing some changes in the B variant.

### **Conclusion**

When businesses face the decision to implement a new testing software application, they need to run a thorough check on the tool. A/A testing is one method that some organizations use for checking the efficacy of the tool when deciding to [either build the tool in-house or buy it](https://vwo.com/why-us/experimentation-platform-build-or-buy/). Along with the other pointers mentioned in this post, A/A testing enables personalization and segmentation. Plus, it can help check if the software application is good for implementation.

## Reference

{% embed url="https://vwo.com/blog/aa-test-before-ab-testing/" %}

