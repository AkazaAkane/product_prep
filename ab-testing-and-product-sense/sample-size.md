# Sample Size

The guarantee of a hypothesis test:&#x20;

“If the treatment has no effect, the chance of false discovery is at most 5%.”  **Type I error rate**

What if the treatment does have an effect?&#x20;

“If the treatment has at least a **20% lift (Minimum planned-for effect/MDE)**, the chance of detecting it is at least **80%. (Power)**”&#x20;

How to guarantee the second statement? Sample size planning.&#x20;

## Kohavi's&#x20;

> A Numerical Example (Kohavi, Longbotham et al. 2009)

Given an OEC metric with standard deviation σ and a desired level of sensitivity, Δ, that is, the amount of change you want to detect, the minimum sample size for a confidence level of 95% and power of 80% (van Belle2008, 31) is as shown:

$$
n = \frac{16}{\delta^2}\sigma^2
$$

Let’s take an e-commerce site with 5% of users who visit during the experiment period ending up making a purchase. The conversion event is a Bernoulli trial with p = 0.05. The standard deviation, σ, of a Bernoulli is  and thus  σ2 = 0.05(1 − 0.05) = 0.0475 . According to the above formula, you need at least 16 ∗ 0.0475/(0.05 ⋅ 0.05)2 = 121,600 users.

You will need the **baseline conversion rate (bcr)** and the **minimum detectable effect**, which is the minimum difference between the control and test group that you or your team will determine to be worth the investment of making the design change in the first place.

![](<../.gitbook/assets/Screen Shot 2022-02-26 at 1.06.25 AM.png>)

![](<../.gitbook/assets/image (6).png>)

![](<../.gitbook/assets/image (2).png>)

### Products

In the majority of cases, test sample size depends on 3 parameters:\
&#x20;

1. Test significance level. This represents the probability to wrongly think that the new version is better than the old version. Obviously, the lower this number is, the better. A pretty common value for this is 0.05\
   &#x20;
2. Test power. This is the probability of correctly saying that the new version is better, when it is indeed better. 1-power is the probability of not making the change to the site, while you should have done it (being too conservative essentially). A common value for the power is 0.8. Obviously the higher, the better\
   &#x20;
3. Minimum effect you are interested in detecting: when you run a test, there is going to be a threshold below which, even if the test is winning, you won’t bother to make the change. Like if test is better than control by 0.000001%, you probably still won’t make the change, even if that difference were statistically significant. The engineering costs, time, and opportunity-cost in general, will make it useless to make a change to get that small of a gain. So the minimum effect defines what’s that threshold above which it makes sense to go ahead with the change\
   &#x20;

Sample size increases the smaller the significance level, the higher the power, and the smaller the minimum effect. In practice, power and significance level are pretty much always 0.8 and 0.05. So what really affects sample size is the minimum effect.\
\
&#x20;

#### Minimum Detectable Effect

The MDE is the minimum effect size that should be detected with a certain probability.

**Your minimum MDE should be the smallest effect that would justify implementing the change that is being tested.** Of course, you can always overpower the test to learn more from it. But when doing this, the risk and opportunity costs of running the experiment have to be kept in mind.

**Method 1**

So how do you come up with an exact number? It comes down to a simple ROI calculation. Consider the following (very simplified) situation:

* A team is validating an MVP to make users add travel insurance to their purchase on a travel website’s checkout.
* The website registers 2000 bookings per day (730.000 per year).
* The estimated net profit for insurance is 3$ per user.
* Implementing the full feature would cost the team ca. 150 developer hours with, let’s say 500$ per hour, totaling up to 75.000$ (not considering any opportunity costs).

On a yearly basis, the website would have to sell 25.000 insurances to break even, equalling 3.42% of bookings adding insurance.

With the insurance conversion rate being the primary metric for the experiment, 3.42% would be a reasonable MDE. Any value lower than this would not be of interest to the team and would unnecessarily prolong the test’s duration.

**Method2**

&#x20;have to configure an experiment in such a way that it declares the winner when the conversion rate difference is at least 22% – 20% = 2%. To set that up, you have to count your _estimated_ MDE.&#x20;

MDE is calculated as a percent of the baseline conversion rate:&#x20;

MDE = desired conversion rate lift / baseline conversion rate x 100%

In this example, 2% of the 20% baseline conversion rate is 10% – this is your _estimated MDE_ for the experiment.&#x20;

#### Calculate Sample Size

In the example below, we want to change our home page and the metric we are trying to improve is conversion rate from the home page to the second page in the funnel. Said it another way, we want to decrease the percentage of bouncers, i.e. people who come to the home page and then leave without doing anything. Let’s assume the current home-page to next-page conversion rate is 10%. And let’s assume our product manager has told us that we only care about an improvement of at least +1%. Meaning, the new conversion rate for the test has to be at least 11%, up 1% from the 10% starting point. Otherwise, it is not going to be worth our time to even try the test and there are other stuff we could work on with higher potential.

```
import statsmodels.stats.api as sms
#Firstly, we need to define the two conversion rates via proportion_effectsize. 
#The first element here (0.1) is simply the conversion rate of the site prior to running the test. Aka control conversion rate
#The second one (0.11) is the minimum conversion rate of the test that would make it worth it to make the change
p1_and_p2 = sms.proportion_effectsize(0.1, 0.11)
#Now we can run the function that after passing the two conversion rates above + power and significance, returns sample size
sample_size = sms.NormalIndPower().solve_power(p1_and_p2, power=0.8, alpha=0.05)
print("The required sample size per group is ~", round(sample_size))
```

The output is pretty self-explanatory: this is telling us that we need \~15K in both test and control for us to be able to detect an increase of at least +1% in our metric.

### Duration

A widely used approach to this is:

* If you need more than 2 weeks to collect that many people, just split the traffic 50/50 and run it until you have the required sample size\
  &#x20;
* Otherwise, still run it for two weeks to be able to reliably capture weekly patterns. But test it on the smallest percentage of users that would allow to collect enough data in 2 weeks\
  &#x20;

Example:

1. Let’s assume our site has 1K visitors per day. We found out we need \~15K users in both test and control to detect an improvement of at least +1%. So we would split the traffic 50/50 for test and control. Each day we would have 500 users in test and 500 in control. And we would run the test for 1 month, i.e. 30\*500 = 15K\
   &#x20;
2. Let’s assume our site has 100K visitors per day. By splitting test and control 50/50, we would get all users we need in 1 day. But that would be too noisy, Sunday is different from Monday. And if we ran it for two weeks with a 50/50 split, we would collect way more data than we need. And that’s also not efficient, after all we could use that traffic to test other things. So we do something like this:\
   &#x20;
   * We want to have 15K people in two weeks. That means \~1K people per day. Since we have 100K daily visitors, 1% of our users will enter the test and see the different version of the site. Because of this, large companies typically run tests on 1% or less of their users

### Reference:

Kohavi, Ron, Roger Longbotham, Dan Sommerfield, and Randal M. Henne. 2009. “Controlled Experiments on the Web: Survey and Practical Guide.” Data Mining and Knowledge Discovery 18: 140–181. [http://bit.ly/expSurvey](http://bit.ly/expSurvey).

{% embed url="https://jeffshow.com/caculate-abtest-required-sample-size.html" %}

{% embed url="https://splitmetrics.com/blog/mobile-a-b-testing-sample-size" %}

{% embed url="https://productds.com/wp-content/uploads/Sample_size.html" %}

{% embed url="https://towardsdatascience.com/how-to-set-the-minimum-detectable-effect-in-ab-tests-fe07f8002d6d" %}
