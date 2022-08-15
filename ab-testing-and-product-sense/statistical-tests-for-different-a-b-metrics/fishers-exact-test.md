# Fisher's exact test

Fisher’s exact test is a non-parametric test that is often used as a substitute for chi-square when the data set is small or categories are imbalanced.

What the binomial test is to the Z-test, [Fisher’s exact test](https://en.wikipedia.org/wiki/Fisher's\_exact\_test) is to the χ2 test. It’s a direct calculation of the p value in case of a F×C contingency table. Fisher’s exact test is accurate at all Ns, and the χ2 test’s p converges to it at high Ns, similar to the above case.

The null hypothesis is that all funnels have the same conversion event probabilities. Given the F×C contingency table outcome of an A/B test (F funnels tested, C mutually exclusive conversion events), the calculation of the p value is:

1. first, calculate the marginals:
   * row marginals: how many users were randomly assigned into each funnel in the A/B test
   * column marginals: across the tested funnels, conversion event totals
2. given the marginals, what is the probability of the observed outcomes
3. for all the ways we can change numbers in the contingency table while keeping the marginals fixed, take the ones that have equal or lower probability then the actual outcome, and add up those probabilities; this is the p value

The trick is, how to calculate the quantity “given the marginals, what is the probability of a specific outcome (numbers in the contingency table that add up to the marginals)”; we need this in both step 2. and 3. For this we have to use the hypergeometric distribution, the distribution for “urn draws”. Let’s reuse the contingency table from the previous post:

![Contingency table](https://bytepawn.com/images/contingency\_table3.PNG)

Imagine this: we have a total of N=10,000 marbles. Each marble is one of C=3 colors (**No conversion, Monthly, Annual**). There are a total of 7,922 marbles **No conversion** marbles, 1,085 **Monthly** conversion marbles, etc. All these marbles are in one big urn. We start drawing marbles; what’s the probability that the first 5,916 drawn will be colored **(No conversion, Monthly, Annual) = (4748, 595, 573)**, irrespective or the order they are drawn? We can break this into two probabilities that we multiply: what is the probability that of 5,916 drawn the colors are **(No conversion, Rest) = (4748, 595+573)** from an urn that contains **(No conversion, Rest) = (7922, 1085+993)** marbles, multiplied by, what is the probability that of the rest 595+573=1,168 drawn the colors are **(Monthly, Annual) = (595, 573)** from an urn that contains **(Monthly, Annual) = (1085, 993)** marbles. These individual probabilities are given by the hypergeometric probability P(X=k|N,K,n), ie. what is the probability of drawing k red marbles from an urn that contains a total of N marbles, K of which are red, of total n drawn (k≤n). It is P(X=k|N,K,n)=(Kk)(N−Kn−k)(Nn). Then, we go on and calculate the same probabilities in the second row, **but keeping in mind that we have already removed (4748, 595, 573) marbles from the urn**. The ordering of the rows doesn’t matter.

## Reference

{% embed url="https://towardsdatascience.com/fishers-exact-fb49432e55b5" %}

