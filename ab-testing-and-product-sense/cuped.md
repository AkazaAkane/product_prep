# CUPED

CUPED (Controlled-experiment Using Pre-Experiment Data) is a variance reduction technique used in A/B testing to increase statistical power and reduce experiment runtime. Here's an explanation of how it works:

### How CUPED Reduces Variance

CUPED leverages pre-experiment data to reduce the variance of the metric being measured in an A/B test. It does this by:

1. Using a covariate (usually the pre-experiment value of the metric) that is correlated with the experiment metric.
2. Adjusting the experiment metric based on this covariate to remove some of the natural variation.
3. This results in a lower-variance adjusted metric that can detect smaller effects with the same sample size, or detect the same effects with a smaller sample size.

The key is that the covariate is not affected by the experiment treatment, so using it doesn't introduce bias.

### Mathematical Formula

The core CUPED formula for adjusting the experiment metric is: $$Y^CUPED=Yˉ−θ(Xˉ−E[X])Y^CUPED​=Yˉ−θ(Xˉ−E[X])$$ Where:

* $\hat{Y}\_{CUPED}$ is the CUPED-adjusted metric
* $\bar{Y}$ is the mean of the original metric
* $\bar{X}$ is the mean of the covariate
* $E\[X]$ is the expected value of the covariate
* $\theta$ is a coefficient that determines how much adjustment is applied

The optimal value for $\theta$ that minimizes variance is: $$θ∗=Cov(X,Y)Var(X)θ∗=Var(X)Cov(X,Y)​$$ This is equivalent to the coefficient you would get from regressing Y on X.

### Variance Reduction

The variance of the CUPED-adjusted metric is: $$Var(Y^CUPED)=Var(Y)(1−ρ2)$$ Where $\rho$ is the correlation between X and Y. This shows that the variance reduction is proportional to the square of the correlation between the covariate and the metric. A higher correlation leads to greater variance reduction.

### Implementation

In practice, CUPED is often implemented by:

1. Collecting pre-experiment data for each user (e.g. 1-2 weeks prior to the experiment).
2. During the experiment, calculating the CUPED-adjusted metric for each user.
3. Using these adjusted metrics to compute treatment effects and run statistical tests.

By reducing variance, CUPED allows experimenters to detect smaller effects or run experiments for shorter durations while maintaining the same statistical power. This can significantly accelerate the experimentation process and decision-making based on A/B test results
