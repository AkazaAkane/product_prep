# t test

**In A/B testing you never know the population mean, you’re estimating it, so always use the t-test. For N>100, the t-test numerically yields the same results as the z-test.**

**"**We should use Welch’s _t_-test by default, instead of Student’s _t_-test, because Welch's _t_-test performs better than Student's _t_-test whenever sample sizes and variances are unequal between groups, and gives the same result when sample sizes and variances are equal."

#### **Student’s t-test:**

**Test statistic:** (x1 – x2)  /  sp(√1/n1 + 1/n2)

where x1 and x2 are the sample means, n1 and n2 are the sample sizes for sample 1 and sample 2, respectively, and where sp is calculated as:

sp = √ (n1-1)s12 +  (n2-1)s22 /  (n1+n2-2)

where s12 and s22 are the sample variances.

**Degrees of freedom:** n1 + n2 – 2

#### **Welch’s t-test**

**Test statistic:** (x1 – x2)  /  (√s12/n1 + s22/n2)

**Degrees of freedom:** (s12/n1 + s22/n2)2 / { \[ (s12 / n1)2 / (n1 – 1) ] + \[ (s22 / n2)2 / (n2 – 1) ] }

The formula to calculate the degrees of freedom for Welch’s t-test takes into account the difference between the two standard deviations. If the two samples have the same standard deviations, though, then the degrees of freedom for the Welch’s t-test will be the exact same as the degrees of freedom for the Student’s t-test.

Typically, the standard deviations for the two samples are not the same and thus the degrees of freedom for Welch’s t-test tends to be smaller than the degrees of freedom for Student’s t-test.

## **Reference**

{% embed url="https://en.wikipedia.org/wiki/Student's_t-test" %}

{% embed url="https://bytepawn.com/ab-testing-and-the-ttest.html" %}

{% embed url="http://daniellakens.blogspot.com/2015/01/always-use-welchs-t-test-instead-of.html" %}
