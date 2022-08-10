# Lasso and Ridge

A regression model that uses L1 regularization technique is called _**Lasso Regression**_ and model which uses L2 is called _**Ridge Regression**_.

_The key difference between these two is the penalty term._

**Ridge regression** adds “_squared magnitude_” of coefficient as penalty term to the loss function. Here the _highlighted_ part represents L2 regularization element.

![](https://miro.medium.com/max/486/1\*jgWOhDiGjVp-NCSPa5abmg.png)

Here, if _lambda_ is zero then you can imagine we get back OLS. However, if _lambda_ is very large then it will add too much weight and it will lead to under-fitting. Having said that it’s important how _lambda_ is chosen. This technique works very well to avoid over-fitting issue.

**Lasso Regression** (Least Absolute Shrinkage and Selection Operator) adds “_absolute value of magnitude_” of coefficient as penalty term to the loss function.

![](https://miro.medium.com/max/480/1\*4MlW1d3xszVAGuXiJ1U6Fg.png)

Again, if _lambda_ is zero then we will get back OLS whereas very large value will make coefficients zero hence it will under-fit. The key difference between these techniques is that Lasso shrinks the less important feature’s coefficient to zero thus, removing some feature altogether. So, this works well for feature selection in case we have a huge number of features.

## Reference:

{% embed url="https://towardsdatascience.com/l1-and-l2-regularization-methods-ce25e7fc831c" %}

[https://www.stat.cmu.edu/\~ryantibs/statml/lectures/sparsity.pdf](https://www.stat.cmu.edu/\~ryantibs/statml/lectures/sparsity.pdf)

{% embed url="https://www.geeksforgeeks.org/lasso-vs-ridge-vs-elastic-net-ml" %}
