# Multivariate testing

With multivariate tests, you test a hypothesis for which several variables are modified and determine which combination from among all possible solutions performed the best. If you create 3 different versions of 2 specific variables, you then have nine combinations in total (number of variants of the first variable X number of variants of the second).

**1. Full Factorial**

Every variant in your MVT gets equal amounts of all the traffic coming to your site. If you have 5 variants, each of them will get 20% of the traffic coming to your site. To reach statistical significance, this method will require a lot of traffic and will have to run for a long time. But this trait also makes full factorial is the most accurate option to go with.

**2. Fractional Factorial**

Unlike its “full” counterpart, fractional factorial only tests a subset of your variants. That’s why there’s “fraction” in its name. If you have 10 variants, it will test 5 of them, and based on the results, predict the performance of the 5 it didn’t test.

Because of this, it runs faster than full factorial but is significantly less accurate. Its benefit is that you get a glimpse of what the full test will look like before you commit.

#### **3. Taguchi testing** <a href="#taguchi-testing" id="taguchi-testing"></a>

Not recommended

**Common uses**

The most commonly cited example of multivariate testing is a page on which several elements are up for debate — for example, a page that includes a sign-up form, some kind of catchy header text, and a footer. To run a multivariate test on this page, rather than creating a radically different design as in A/B testing, you might create two different lengths of sign-up forms, three different headlines, and two footers. Next, you would funnel visitors to all possible combinations of these elements. This is also known as full factorial testing, and is one of the reasons why multivariate testing is often recommended only for sites that have a substantial amount of daily traffic — the more variations that need to be tested, the longer it takes to obtain meaningful data from the test.

**Advantages**

* **Avoid performing successive A/B tests** and save time since multi variant testing can be seen as performing several A/B tests on the same page at the same time.
* **Determine the impact of each variable** in measured gains.
* **Measure the impact of interactions** between different elements presumed to be independent (for example, page title and illustration visual).

**Limitations**

The single biggest limitation of multivariate testing is the amount of traffic needed to complete the test. By multiplying the number of variables and versions tested in your multivariate test, you will quickly reach a large number of combinations. The sample assigned to each combination will be reduced proportionally.

Conducting an A/B test is often easier than a multivariate test, especially when analysing the results.&#x20;

Multivariate tests are comparatively complex and slow to set up, and slower to run. All the time lost during its setup phase and course of running creates an opportunity cost. Amidst the time MVT takes to show meaningful results, you could have run dozens of A/B tests and drawn conclusions. A/B tests are quick to set up and also provide definite answers to many specific problems. &#x20;

## Reference

{% embed url="https://vwo.com/multivariate-testing/#advantages-and-disadvantages-of-mvt" %}
