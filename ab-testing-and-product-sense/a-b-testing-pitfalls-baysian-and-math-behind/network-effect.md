# Network Effect

At LinkedIn, most decisions are made using experiments. When we want to decide between two features, we test them against each other in the real world: we give feature A to a random set of members, feature B to another set, and we compare the results. Are users of feature A more engaged? Do they have a better experience with our products? If so, feature A wins.&#x20;

A/B testing is, of course, common practice in a large range of settings, from drugs and vaccines to web applications. However, most methods rely on a strong assumption: when comparing feature A and feature B, the behavior of feature A users is not impacted by the activity of feature B users. In other words, most methods assume that there is no interference, which is sometimes called "network effect," between features.&#x20;

For example, when testing whether a headache pill is more effective at reducing pain than a sugar pill, a natural way to proceed is to give the headache pill to one set of patients, and to give the others a sugar pill. Then, compare outcomes between these two groups. The reason this works is that if another person takes a pill, it is very unlikely to have an effect on my headaches. There is no interference.

![](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference2.png)

_A/B testing without interference_\
&#x20;

But can we really assume this when doing A/B tests on social networking platforms?

### Network effect

Imagine this: my friend is targeted for an experiment that gives her a better messaging experience. I am not targeted, and my messaging experience doesn’t change. However, her better messaging experience causes her to spend more time on the site and send more messages, including some to me. I then respond to her, and spend more time on the site as well. What happened here? The fact that my friend received a new feature had an impact on me, even though I was not part of the experiment. There is interference.

![](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference3.png)

_A/B testing with interference. When treatment leaks into control, we can no longer rely on computing mean(B) – mean(A)._\
&#x20;

**Why do we care?**\
In short, interference can bias the results of an A/B test to the point where it can lead to the wrong decision. For example, one could conclude that a new relevance algorithm has no impact (and stop investing in its development) when it in fact has a positive impact through the network that has simply gone unmeasured. Or, one could think a feature has a positive impact when it’s actually negative. For example, it is not uncommon to see low-quality “viral” content have positive engagement effects on users, but have a negative overall impact. If it’s not worth re-sharing, it has low (or sometimes negative) network effects and decreases the quality of the conversation overall.&#x20;

In reviewing the experiment in the graph above, when there is no interference, it gives the “effect of B” over A. However, when there is interference, it does not.

**What can we do about it?**\
In this three-part series, we will describe how we address this problem. This first post is about a detection mechanism for the network effect detailed further in our [paper](https://www.kdd.org/kdd2017/papers/view/detecting-network-effects-randomizing-over-randomized-experiments) from Saveski et al (2017).

### Detection

Our approach is simple:&#x20;

* We cluster the LinkedIn graph into 10,000 clusters. The graph comprises all active LinkedIn members as nodes and their “connections” as edges. &#x20;
* We then split these clusters into two parallel experiments:
* **A)** An individual-level experiment, where members are sorted randomly into treatment or control groups.
* **B)** A cluster-based experiment, where a whole cluster (i.e., community) of users is either in treatment or in control. In other words, if I am treated, a significant proportion of my connections are also treated. If I am part of the control group, a significant proportion of my connections are also under control.

The key intuition is that if there is no network effect, both of these experiments should give us the same estimated effect.&#x20;

We devise a statistical test, inspired from [Hausman tests](https://en.wikipedia.org/wiki/Durbin%E2%80%93Wu%E2%80%93Hausman\_test) from econometrics, to figure out whether the difference we see in these two experiments is significant or not. If you would like to know how we derive the variance and distribution of the test statistic, more details can be found in the paper.

* ![graphic-of-our-procedure](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference4.png)

_Our procedure: A: cluster the graph into 10,000 clusters, B: split these between two arms (individually-randomized and cluster-randomized), C: assign treatment (individually for the first arm, by cluster for the second one), D: compute the effect and variance for each, E: compare._\
&#x20;

**Making the clusters**\
As you can see in the above figure, the first step of our procedure is to create clusters of LinkedIn members. This is a difficult task, given that we do not have naturally isolated communities. We connect the world’s professionals, and a great number of our members have many connections spanning the entire globe, and naturally belong to many communities: their industries, their friends, coworker groups from their current jobs, as well as several groups from their previous jobs. However, for the purposes of this analysis, it is important to make sure that a member belongs to one cluster only.&#x20;

The goal of a clustering procedure is to find “cuts” between members' groups, so that overall, as many connections (network edges) are inside clusters, and as few as possible are across clusters. We find ourselves trying to find a balance between two conflicting objectives:

* We want to have as many clusters as possible because they will translate to randomization units. More clusters equal more power for our A/B tests.
* The more clusters we create, the less isolated they are. For example, if you want to have zero connections across clusters, then the obvious solution is to have one cluster only. This, of course, would not lend itself to an A/B test.

We tried different clustering algorithms. Given that we have hundreds of millions of members, we needed a clustering algorithm that could work in parallel (over Hadoop, for example). Our method is primarily designed for balanced clustering (all clusters should have roughly equal size), which the most common clustering procedures don’t provide. We tried several procedures.

* ![reldg-algorithm-results-table](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference5\_.png)

The reLDG algorithm gave us the best results, so we decided to use it, and then did a few more simulations to estimate its performance with a higher number of clusters.

* ![clustering-results-table](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference6\_.png)

Given we wanted to maximize the number of clusters, we chose to run reLDG with 10,000 clusters, as the isolation loss was small relative to 5,000 or 7,000 clusters.

When our clusters were ready, we created strata of clusters with similar characteristics instead of randomizing them in a simple Bernoulli fashion (flipping a coin). We did this in order to be able to use a stratified t-test, which can help reduce variance and increase power.

* ![experiment-design-illustration](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference7.png)

**Analyzing the results**\
Once we have our clusters and strata, we can now allocate treatment in a 50/50-50/50 fashion:

* 50% of clusters are randomized at the individual level (left part of the image below). Inside this group:
  * 50% of members are treated
  * 50% of members are control
* 50% of clusters are randomized at the cluster level (right part of the image below).
  * 50% of clusters are treated
  * 50% of clusters are control
* ![illustration-of-experiment](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference8.png)

We then compute the results of the two A/B tests separately, and compute the differences between treatment and control separately, as well as their variances. In the individual A/B test, we call µBR the difference in means, and σBR the standard deviation. In the cluster-based A/B test, we call µCBR the difference in means, and σCBR the standard deviation.

* µBR is simply the mean of the metric across all treated users in the individual-level arm, minus the mean of the metric across all control users in that same arm.
* µCBR is a Horvitz-Thompson estimator. We first compute means for each cluster, and then average again over those to get mean of treatment and mean of control.

We are able to further reduce variance by using CUPED instead of averaging the metric alone. We first transform it by taking, for each user, the difference between the value of the metric during the experiment and before the experiment. As outlined in the CUPED paper, the resulting metric typically has lower variance and still allows for measurement of the treatment effect.&#x20;

Finally, we compute ∆ = µBR - µCBR, and perform a hypothesis test:

* H0: ∆ = 0
* Ha: ∆ ≠ 0

If we are able to reject H0, we conclude that there is interference. The power of this test can sometimes be low, which is why it is important to do as much as possible to reduce variance using the following tips:

* Most of the variance of ∆ comes from the number of clusters. This is why it is worth it to use as many clusters as possible.
* Stratification can help us further.
* UCUPED (doing an A/B test of the difference in the metric over time rather than on its absolute value).

The final result of combining these approaches can be seen in the table below:

* ![results-table](https://content.linkedin.com/content/dam/engineering/site-assets/images/blog/posts/2019/06/interference9.png)

The first three rows are the most important of the above table: the first row shows the results of the individual-level randomization, the second row shows the results of the cluster-based randomization, and the third row compares them. Looking at “experiment 2”, in the “post-treatment” column (second to last), one can see that the effect measured by the cluster-based experiment (0.81) is much higher than the effect measured by the individual-level experiment (0.24). Remember that if there were no network effects, we would expect these two numbers to be roughly the same.

### Conclusion

We now have a test to detect interference. This is used to get an idea of whether a specific type of product or feature needs special attention when running A/B tests. Then, we use specific methods that go beyond detecting network effects and allow us to measure them, going from a yes or no answer to an estimated impact number. Stay tuned for two more blog posts that present these methods, egoClusters and ELEMENT.&#x20;

**Limitations** of per-user assignment to run an A/B test on _**user-to-user features**_ or product relies heavily on interaction between users:

1. If you let the test group use video chat with anybody, the people in the control group wouldn't really be a control group because they're getting exposed to this new video chat feature.
2.  Can't measure "**higher-order effects**" (also known as **network effects**)

    **Network effects**: occur when the changes induced by a new feature leak out of the test group and affect behavior in the control group as well.

    * The change would theoretically improve the experience for test group as well as the control group
    * Higher-order effects might create an illusory change that disappears once you roll out a feature out to everybody.

#### **Solution: Using per-**_**community**_** random assignment** <a href="#solution-using-per-community-random-assignment" id="solution-using-per-community-random-assignment"></a>

> a "**community**" is any group of users whose interactions are primarily directed to other users within the same group.

**How to define a "community"?**

Model the relationships between users with a [social graph](https://en.wikipedia.org/wiki/Social\_graph), each user is a node, and edges are placed between nodes that have had some interaction. And then apply **graph partitioning** algorithms like [Normalized Cuts](https://people.eecs.berkeley.edu/\~malik/papers/SM-ncut.pdf) to find isolated, non-interacting groups.

**Pitfall**: In dating apps, the community is really defined by "anybody that's near you" as opposed to "people you have a history of interacting with".

#### Solution: Defining geographic communities <a href="#solution-defining-geographic-communities" id="solution-defining-geographic-communities"></a>

Define 'optimal' regions by drawing boundaries that maximizes the number of connections within each region relative to the number of connections that occur across regions using Shi & Malik's [Normalized Cut](https://people.eecs.berkeley.edu/\~malik/papers/SM-ncut.pdf) algorithm for graph partitioning.

1. Build a graph to describe how many messages were sent between pairs of US/Canadian locations as the square adjacency matrix **A** (size _num\_Locations_-by-_num\_Locations_). The value at **A**\[_i_,_j_] set to the number of messages sent between users in location _i_ to users in location _j_
2. Calculate the graph _Laplacian_, **L**, from the _adjacency_ matrix, **A**, and the _degree matrix_, **D**, as **L** = **D** - **A**. The degree matrix is just a diagonal matrix with the degree of each node on the diagonal and zeros everywhere else.

[![](https://upload.wikimedia.org/wikipedia/commons/thumb/2/28/6n-graph2.svg/350px-6n-graph2.svg.png)](https://upload.wikimedia.org/wikipedia/commons/thumb/2/28/6n-graph2.svg/350px-6n-graph2.svg.png)[![](https://wikimedia.org/api/rest\_v1/media/math/render/svg/e6f63ff7144996fb004e3d4b211c2c73c5e59fbd)](https://wikimedia.org/api/rest\_v1/media/math/render/svg/e6f63ff7144996fb004e3d4b211c2c73c5e59fbd)

1. Calculate the eigendecomposition of **L**, and inspect its eigenvalues. Each fully-isolated (_i.e._, disconnected) sets of nodes will be represented by an eigenvector with an associated eigenvalue of 0. Further segmentation of these regions is done by looking at the patterns in the eigenvectors with the _k_ smallest, non-zero eigenvalues. The value of _k_ can be varied depending on how many regions you want to make.

#### When we conduct random sampling, how to avoid network effect? What is network effect (interference)?

Example Question: An experiment to give a better engagement. User A and User B are friends. User B is assigned to test group while User A is at control group. User B has a better experience that make him engage more. And then his engagement leads to a better engagement for user&#x20;

Solution: A cluster-based experiment, where a whole cluster (i.e., community) of users is either in treatment or in control. In other words, if I am treated, a significant proportion of my connections are also treated. If I am part of the control group, a significant proportion of my connections are also under control.&#x20;

## Reference:

{% embed url="https://engineering.linkedin.com/blog/2019/06/detecting-interference--an-a-b-test-of-a-b-tests" %}
